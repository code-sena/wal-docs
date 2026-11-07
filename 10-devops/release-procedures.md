# Release Procedures

> Step-by-step guides for releasing Alerta Mujer to QA and production environments.

---

## Pre-Release Checklist

Before any release, verify:

- [ ] All tests pass on the source branch (`develop` or `qa`)
- [ ] Pull request approved by at least one team member
- [ ] Changelog updated with new features and fixes
- [ ] Database migrations reviewed (if any changes to schema)
- [ ] Environment variables documented (if any new secrets needed)
- [ ] No hardcoded credentials or API keys in code
- [ ] Documentation updated to match new features
- [ ] Team lead gives approval for release

---

## Release to QA (Branch `qa`)

**Duration:** ~10 minutes  
**Downtime:** None (QA is a separate environment)  
**Who:** Any team member with `maintain` access to Backend-ALERTA-MUJER

### Step 1: Prepare the Release Branch

```bash
# Switch to develop and pull latest
git checkout develop
git pull origin develop

# Create release branch
git checkout -b release/v1.0.X develop

# Update version numbers (optional, if using semantic versioning)
# - backend: update pom.xml in all three services
# - web: update package.json
# - mobile: update package.json and app.json

git add -A
git commit -m "chore(release): bump version to v1.0.X"
```

### Step 2: Create Pull Request to `qa`

```bash
# Push release branch
git push origin release/v1.0.X

# Create PR on GitHub:
# - From: release/v1.0.X
# - To: qa
# - Title: "Release v1.0.X to QA"
# - Description: List of features and fixes included
```

**Wait for:**
- GitHub Actions to complete all tests
- At least one team member approval

### Step 3: Merge to `qa`

```bash
# Merge with --no-ff to preserve branch history
git checkout qa
git pull origin qa
git merge --no-ff release/v1.0.X -m "Merge release v1.0.X to qa"
git push origin qa
```

**GitHub Actions will automatically:**
1. Run all tests
2. Build Docker images with `qa` tag
3. Push to container registry

### Step 4: Deploy to Render QA (Manual)

```bash
# Manually trigger Render deployment
# https://dashboard.render.com/
# - Select each service (auth, alert, evidence)
# - Click "Manually Deploy" or wait for webhook

# Or use Render CLI:
# render deploy --service alerta-mujer-auth-qa
```

### Step 5: Smoke Test QA

```bash
# Health check
curl https://qa-alerta-mujer-auth.onrender.com/actuator/health

# Sample user login
curl -X POST https://qa-alerta-mujer-alert.onrender.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password"}'

# Visit web app
# https://qa-alerta-mujer.vercel.app
```

### Step 6: Notify Team

```bash
# Slack message (example):
# "🚀 Release v1.0.X deployed to QA. Ready for testing.
#  - Features: [list]
#  - Fixes: [list]
#  - Test env: https://qa-alerta-mujer.vercel.app"
```

---

## Release to Production (Branch `main`)

**Duration:** ~15 minutes  
**Downtime:** ~2 minutes (services restart)  
**Who:** Tech lead or senior developer with admin access

### CRITICAL: Use Production Checklist

Before proceeding, verify:

- [ ] All QA tests passed (minimum 2 hours testing)
- [ ] No critical bugs found in QA
- [ ] Production database backup created (in Render dashboard)
- [ ] Rollback plan understood (see below)
- [ ] All team members notified
- [ ] Low-traffic window selected (not during peak hours in Neiva)

### Step 1: Create Release from QA

```bash
# Switch to main and pull latest
git checkout main
git pull origin main

# Merge qa into main
# Use fast-forward only (no merge commits)
git merge qa

# Tag the release
git tag -a v1.0.X -m "Release version 1.0.X - Features: [short list]"

# Push to origin
git push origin main
git push origin v1.0.X
```

### Step 2: Monitor Deployment

GitHub Actions automatically:
1. Runs all tests on `main`
2. Builds Docker images with `latest` and `v1.0.X` tags
3. Pushes to container registry
4. **Render webhook triggers automatic deployment**

**Expected timeline:**
- 0:00 - Commit pushed to main
- 0:30 - GitHub Actions completes (all tests)
- 1:00 - Docker images built and pushed
- 1:30 - Render services restart (< 2 minutes each)
- 2:00 - All services UP (verify with health checks)

```bash
# Monitor logs in Render dashboard:
# https://dashboard.render.com/ > each service > Logs tab

# Or use curl to check status:
watch -n 5 'curl -s https://alerta-mujer-auth.onrender.com/actuator/health | jq .status'
```

### Step 3: Smoke Test Production

```bash
# Health checks
curl https://alerta-mujer-auth.onrender.com/actuator/health
curl https://alerta-mujer-alert.onrender.com/actuator/health
curl https://alerta-mujer-evidence.onrender.com/actuator/health

# Expected: {"status":"UP"}

# Verify web loads
open https://alerta-mujer.vercel.app

# Test core functionality:
# 1. Login as admin
# 2. View dashboard
# 3. Trigger test SOS alert
# 4. Check notification received
# 5. Verify location tracking
# 6. Check evidence upload
```

### Step 4: Monitor for Errors (1 hour)

```bash
# Watch logs for errors:
# - "ERROR" in Render logs
# - "OutOfMemory"
# - "SQLException" or "DatabaseError"
# - "401 Unauthorized" (auth issues)
# - "CORS" errors

# Check metrics:
# - CPU usage < 80%
# - Memory usage < 80%
# - Database connections < 20
# - Error rate < 0.1%
```

### Step 5: Announce Release

```bash
# Update status page (if one exists)
# Slack announcement (example):
# "✅ Alerta Mujer v1.0.X is now live in production
#  - Features: [list]
#  - Deployment: 15 minutes ago, all systems UP
#  - Status: https://alerta-mujer.vercel.app"

# Email users (if major feature release)
# In-app notification (optional)
```

---

## Rollback Procedure

**If critical bugs appear in production:**

### Quick Rollback (< 10 minutes)

```bash
# Identify the bad commit
git log --oneline main | head -5

# Revert the commit
git revert <bad-commit-hash>

# Push to main (Render will auto-deploy)
git push origin main

# Verify services come back UP:
curl https://alerta-mujer-auth.onrender.com/actuator/health

# Notify team and users
```

### Full Rollback (if revert fails)

```bash
# Go back to previous working tag
git checkout main
git reset --hard v1.0.X-1  # Previous version
git push --force origin main

# Render will rebuild and deploy the previous version
# Expected downtime: ~5 minutes
```

### Database Rollback

If the deploy included a database migration that broke production:

1. **In Render dashboard:**
   - Select "alerta-mujer-db"
   - Go to "Backups"
   - Restore from backup created before this release

2. **Restart services** after restore completes

3. **Verify services are UP** and using the old schema

---

## Mobile App Release

Mobile releases are manual (Expo, EAS):

### Build APK

```bash
cd AlertaMujer/Frontend-movil

# Login to EAS
eas login

# Build locally
eas build --local --platform android --output app-release.apk

# Takes ~30 minutes
# Output: app-release.apk in current directory
```

### Create GitHub Release

```bash
# On GitHub website:
# 1. Go to https://github.com/JustinDanielaBahamon/AlertaMujer/releases
# 2. Click "Draft a new release"
# 3. Tag: v1.0.X
# 4. Title: "Release v1.0.X"
# 5. Upload APK as asset: app-release.apk
# 6. Publish release

# Or use GitHub CLI:
gh release create v1.0.X ./app-release.apk --title "Release v1.0.X" --notes "Features: ..."
```

### Update Download Link

Update the landing page to point to latest release:

```html
<!-- Landing page HTML (in web frontend) -->
<a href="https://github.com/JustinDanielaBahamon/AlertaMujer/releases/latest/download/AlertaMujer.apk" 
   class="btn btn-primary">
  Download APK (v1.0.X)
</a>
```

---

## Hotfix Release (Emergency)

**Use only for critical production bugs.**

### Step 1: Create Hotfix Branch

```bash
# From main (production code)
git checkout main
git pull origin main
git checkout -b hotfix/critical-bug-name

# Fix the bug
# Commit the fix
git commit -m "fix: [critical bug description]"
```

### Step 2: Merge to Main and QA

```bash
# Merge to main (production)
git checkout main
git merge --no-ff hotfix/critical-bug-name
git tag -a v1.0.X-hotfix1 -m "Hotfix: [description]"
git push origin main v1.0.X-hotfix1

# Also merge to qa and develop
git checkout qa
git merge --no-ff hotfix/critical-bug-name
git push origin qa

git checkout develop
git merge --no-ff hotfix/critical-bug-name
git push origin develop

# Clean up hotfix branch
git branch -d hotfix/critical-bug-name
```

### Step 3: Deploy and Monitor

Follow the "Release to Production" steps above.

---

## Changelog Format

Maintain a `CHANGELOG.md` in each repository:

```markdown
# Changelog

## [1.0.X] - 2026-11-07

### Added
- Feature A: Description
- Feature B: Description

### Fixed
- Bug A: Description
- Bug B: Description

### Changed
- API endpoint X: Changed response format

### Deprecated
- Old endpoint Y (use Z instead)

### Removed
- Unused utility class X

## [1.0.X-1] - 2026-10-28
...
```

---

## Release Schedule

| Environment | Release Window | Frequency | Approver |
|---|---|---|---|
| QA (`qa` branch) | Anytime | 1-2x per week | Any team member |
| Production (`main` branch) | Weekday 09:00-12:00 UTC (morning in Neiva) | 1x per week | Tech lead |
| Hotfix | Anytime (emergency) | As needed | Tech lead + one approval |

---

## Post-Release Tasks

After every production release:

1. **Update release notes** on GitHub
2. **Announce to users** (Slack, email, in-app message)
3. **Document any issues** encountered during deployment
4. **Review metrics** for the next 24 hours (error rate, uptime)
5. **Plan next release** based on feedback
6. **Close GitHub issues** that were fixed in this release

---

## Correlations

- CI/CD pipelines → `10-devops/ci-cd.md`
- Production monitoring → `10-devops/production-monitoring.md`
- Deployment infrastructure → `05-architecture/deployment.md`
- Incident management → `13-operations/incident-management.md`
