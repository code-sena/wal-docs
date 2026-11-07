# Production Monitoring and Health Checks

> How to monitor Alerta Mujer in production: health endpoints, logs, performance, and alerts.

---

## Health Check Endpoints

All services expose Spring Boot Actuator endpoints for monitoring:

| Service | Health Endpoint | Details Endpoint |
|---------|-----------------|------------------|
| Auth API | https://alerta-mujer-auth.onrender.com/actuator/health | https://alerta-mujer-auth.onrender.com/actuator/health/liveness |
| Alert API | https://alerta-mujer-alert.onrender.com/actuator/health | https://alerta-mujer-alert.onrender.com/actuator/health/liveness |
| Evidence API | https://alerta-mujer-evidence.onrender.com/actuator/health | https://alerta-mujer-evidence.onrender.com/actuator/health/liveness |
| Web (Vercel) | https://alerta-mujer.vercel.app | N/A (static files) |

### Expected Response

```json
{
  "status": "UP",
  "components": {
    "db": {
      "status": "UP",
      "details": {
        "database": "PostgreSQL",
        "hello": 1
      }
    }
  }
}
```

### Check All Services

```bash
#!/bin/bash
# check-prod-health.sh

SERVICES=(
  "https://alerta-mujer-auth.onrender.com/actuator/health"
  "https://alerta-mujer-alert.onrender.com/actuator/health"
  "https://alerta-mujer-evidence.onrender.com/actuator/health"
)

echo "Checking production services..."
for service in "${SERVICES[@]}"; do
  status=$(curl -s "$service" | jq .status)
  echo "$(basename ${service%/*}): $status"
done
```

---

## Logs

### Render Logs (Backend Services)

Logs are available in Render dashboard:

1. **Navigate to** https://dashboard.render.com
2. **Select each service** (Auth, Alert, Evidence)
3. **View logs** in the "Logs" tab (last 1000 lines)
4. **Search logs** by keyword: `ERROR`, `WARN`, `JWT_SECRET`, `CORS`, etc.

**Retention:** Render keeps logs for 7 days on the free plan.

### Application Logs

Services log to stdout/stderr:

- **Level:** `INFO` in production, `DEBUG` in development
- **Format:** `[timestamp] [level] [thread] [class] message`
- **Key events:**
  - User login/logout
  - SOS alert raised
  - Evidence upload
  - Database migration (Auth service startup)
  - Internal API calls (X-Internal-Key validation)

### Email Logs

The Alert service logs all emails sent (or would-be sent):

```
2026-11-07T11:45:23.123Z INFO com.alertamujer.email.EmailService - Sending email to user@example.com: SOS Alert Notification
```

To test emails in development, leave `EMAIL_PROVIDER` empty in `.env`; emails are printed to logs.

---

## Database Monitoring

### Render Database Dashboard

1. **Navigate to** https://dashboard.render.com
2. **Select "alerta-mujer-db"** (PostgreSQL)
3. **View metrics:**
   - CPU usage
   - Memory usage
   - Disk usage
   - Active connections

### Manual Database Checks

Connect to production database:

```bash
psql -h [db-host] -p 5432 -U [db-user] -d alerta_mujer

# Inside psql:
SELECT version();                           -- PostgreSQL version
SELECT COUNT(*) FROM identity.users;        -- User count
SELECT COUNT(*) FROM alert.alerts;          -- Alert count
SELECT COUNT(*) FROM resource.evidence;     -- Evidence files
```

**Warning:** The free plan database expires on **2026-11-13**. Plan for renewal or migration.

### Backup Strategy

Production database is managed by Render (daily backups). To restore from backup:

1. **Contact Render support** or use Render CLI:
   ```bash
   render db restore alerta-mujer-db --backup-id [id]
   ```
2. **All services will restart** during restore.
3. **Users cannot interact** with the system during restore (< 5 minutes expected).

---

## Performance Monitoring

### Response Times

Monitor API response times using curl:

```bash
# Time a request to the Auth service
curl -w "Time taken: %{time_total}s\n" \
  https://alerta-mujer-auth.onrender.com/actuator/health

# Expected: < 1 second on first request (cold start on free plan)
#           < 100 ms on subsequent requests (warm)
```

### Cold Starts

**Free plan services sleep after 15 minutes of inactivity.** The first request wakes them (1-2 seconds).

- **Solution 1:** Pay for a higher plan (always-on).
- **Solution 2:** Use a monitoring service to ping services every 14 minutes.

Example uptime monitoring with cron:

```bash
# Add to crontab: crontab -e
# Wake up services every 13 minutes
*/13 * * * * curl -s https://alerta-mujer-auth.onrender.com/actuator/health > /dev/null
*/13 * * * * curl -s https://alerta-mujer-alert.onrender.com/actuator/health > /dev/null
*/13 * * * * curl -s https://alerta-mujer-evidence.onrender.com/actuator/health > /dev/null
```

---

## Alerting (Manual for Now)

### Setup Email Alerts

Subscribe to these notifications in Render dashboard:

1. **Service crashes:** Enable email notifications under "Settings"
2. **Disk space:** Set alert at 80% usage
3. **Database CPU:** Set alert at 80% usage

### Manual Monitoring Script

```bash
#!/bin/bash
# monitor-prod.sh - Run every 5 minutes with cron

SERVICES=(
  "auth:https://alerta-mujer-auth.onrender.com/actuator/health"
  "alert:https://alerta-mujer-alert.onrender.com/actuator/health"
  "evidence:https://alerta-mujer-evidence.onrender.com/actuator/health"
)

DATE=$(date "+%Y-%m-%d %H:%M:%S")

for service in "${SERVICES[@]}"; do
  IFS=':' read -r name url <<< "$service"
  status=$(curl -s -o /dev/null -w "%{http_code}" "$url")
  
  if [ "$status" != "200" ]; then
    echo "$DATE ⚠️ $name returned $status" | mail -s "Alerta Mujer: $name DOWN" ops@example.com
  fi
done
```

---

## Common Issues and Fixes

### Service is DOWN (Status: 502)

1. **Check if service is running:**
   ```bash
   curl https://alerta-mujer-auth.onrender.com/actuator/health
   ```

2. **Check logs in Render dashboard** for errors:
   - `Liquibase lock detected` → Wait 5 minutes or clear lock manually
   - `Database connection failed` → Check DB credentials in environment variables
   - `OutOfMemory` → Service may need more memory (upgrade plan)

3. **Restart service:**
   - Go to Render dashboard > [Service name] > "Restart service"
   - Expected downtime: 1-2 minutes

### Database Connection Timeout

1. **Verify DB is running:**
   ```bash
   # In Render dashboard: alerta-mujer-db > Logs
   ```

2. **Check environment variables** on affected service:
   - `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`

3. **Restart database** (if necessary):
   - Render dashboard > alerta-mujer-db > "Restart database"
   - **Warning:** All services will lose connection briefly

### High Database CPU / Slow Queries

1. **Identify slow query:**
   ```bash
   psql -h [host] -U [user] -d alerta_mujer
   SELECT query, mean_time FROM pg_stat_statements 
   ORDER BY mean_time DESC LIMIT 5;
   ```

2. **Check for missing indexes:**
   ```bash
   SELECT * FROM pg_stat_user_indexes WHERE idx_scan = 0;
   ```

3. **Create index if needed:**
   ```bash
   CREATE INDEX idx_alerts_user_id ON alert.alerts(user_id);
   ```

### Disk Space Critical (>90%)

1. **Check what's using disk:**
   ```bash
   SELECT schemaname, tablename, 
          ROUND(pg_total_relation_size(schemaname||'.'||tablename) / 1024 / 1024, 2) AS size_mb
   FROM pg_tables 
   ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;
   ```

2. **Delete old data (if safe):**
   ```bash
   -- Remove evidence older than 30 days (keep if user marked "save")
   DELETE FROM resource.evidence 
   WHERE created_at < NOW() - INTERVAL '30 days' 
   AND alert_id NOT IN (SELECT id FROM alert.alerts WHERE saved = true);
   ```

3. **Upgrade to larger plan** (permanent fix)

---

## Incident Response

### If a Service is Down

1. **Page on-call engineer** (if one is assigned)
2. **Check Render dashboard > Metrics** for resource issues (CPU, memory, disk)
3. **Check logs** for recent errors
4. **Restart service** if hung
5. **Check if database is accessible** from affected service
6. **Verify environment variables** are set correctly
7. **Check recent deployments** (main branch push within last hour?)
8. **Rollback to previous version** if recent deploy is suspected:
   ```bash
   # In Backend-ALERTA-MUJER repo:
   git revert <commit-hash>
   git push origin main  # Render will auto-redeploy
   ```

### Runbook: Full System Restart

**Time required:** 10-15 minutes, all users offline during this time.

1. **Stop all services** (Render dashboard > each service > "Stop service")
   - Wait for services to stop
2. **Stop database** (Render > alerta-mujer-db > "Stop database")
   - Wait 30 seconds
3. **Start database** first
   - Wait until "UP" in health check
4. **Start Auth service** (usually starts database migrations)
   - Wait until "UP"
5. **Start Alert service**
   - Wait until "UP"
6. **Start Evidence service**
   - Wait until "UP"
7. **Verify web loads** at https://alerta-mujer.vercel.app
8. **Notify users** (Slack, email, in-app message)

---

## On-Call Responsibilities

If you are on-call:

- **Monitor health endpoints** (set a phone alarm or cron job)
- **Check logs daily** for warnings or errors
- **Respond to alerts** within 15 minutes
- **Escalate** to team lead if unsure how to fix
- **Document incidents** (what happened, how you fixed it, how to prevent next time)

---

## Metrics to Track

| Metric | Healthy | Warning | Critical |
|--------|---------|---------|----------|
| Service uptime | > 99.5% | 95-99.5% | < 95% |
| API response time | < 100 ms | 100-500 ms | > 500 ms |
| Database CPU | < 50% | 50-80% | > 80% |
| Database connections | < 20 | 20-30 | > 30 |
| Disk usage | < 50% | 50-80% | > 80% |
| Error rate | < 0.1% | 0.1-1% | > 1% |

---

## Links

- **Render Dashboard:** https://dashboard.render.com
- **Vercel Dashboard:** https://vercel.com/dashboard
- **GitHub Releases:** https://github.com/JustinDanielaBahamon/AlertaMujer/releases
- **Production Web:** https://alerta-mujer.vercel.app
- **Production Auth API:** https://alerta-mujer-auth.onrender.com
- **Production Alert API:** https://alerta-mujer-alert.onrender.com
- **Production Evidence API:** https://alerta-mujer-evidence.onrender.com

---

## Correlations

- DevOps and CI/CD → `10-devops/ci-cd.md`
- Deployment infrastructure → `05-architecture/deployment.md`
- Operations procedures → `13-operations/README.md`
- Observability and logging → `13-operations/observability.md`
