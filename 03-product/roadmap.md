# Product Roadmap — Woman Alert

This roadmap defines the delivery plan over three horizons, from MVP to long-term evolution based on the SRS scope and discovery findings.

---

## Horizon 1 — MVP (Sprint 1-4)

**Objective:** Deliver the core system described in the SRS with essential emergency response capabilities.

**Timeline:** Sprint 1 (Sep 19–25), Sprint 2 (Sep 26 – Oct 2), Sprint 3 (Oct 3–9), Sprint 4 (Oct 10–16)

### Modules and features

#### Identity and Access Management
- User registration (phone/email with verification)
- Login with JWT authentication
- Profile configuration (name, photo, tutorial completion)
- Emergency contacts management (add, edit, delete contacts)
- Password recovery via email or SMS

#### Alert Activation
- Main panic button in app interface
- Widget activation (home screen widget)
- Sudden movement detection (accelerometer-based trigger)
- Physical button activation (volume key long-press)
- Discreet confirmation pattern (gesture-based)

#### Alert Delivery
- Multi-channel notifications (SMS, email, push notifications via FCM)
- Real-time location sharing with contacts
- Alert contact tracking (delivery status, retry logic)
- Chain of Responsibility pattern for channel failover

#### Evidence Recording
- Audio recording during active alert
- Photo capture during active alert
- Video recording during active alert
- Cloud storage upload with encryption
- Evidence history for user review

#### Location Tracking
- Continuous GPS tracking during active alert
- Location accuracy 10-20m (RNF7)
- Frequent location logging (home, work, frequent places)
- Safe/risk zone map visualization

#### Notifications
- In-app notifications (reminders, warnings, updates)
- Alert reminders when user doesn't respond
- Persistent notifications for active alerts

#### Web Platform
- Public landing page (information about the service)
- User portal (history, locations, evidence review)
- Administrative panel (user management, alerts, reports)

#### Cross-cutting
- Language support (Spanish primary, English toggle)
- Theme customization (light/dark mode)
- Device registration and management
- Permission management (GPS, camera, microphone, contacts)

---

## Horizon 2 — Iteration (Sprint 5-8)

**Objective:** Enhance the MVP based on user feedback and operational learnings.

**Timeline:** Sprint 5 (Oct 17–23), Sprint 6 (Oct 24–30), Sprint 7 (Oct 31 – Nov 6), Sprint 8 (Nov 7–13)

### Enhancements

#### Evidence Expansion
- Evidence metadata tagging (timestamp, location, context)
- Evidence sharing with authorities (with user consent)
- Evidence compression for bandwidth optimization
- Evidence retention policy implementation

#### Zone Management
- User-reported zone classifications (safe/dangerous)
- Zone geofencing notifications (entering/leaving risk zones)
- Zone-based alert routing (contacts specific to zone)
- Crowd-sourced zone safety ratings

#### Advanced Activation
- Voice activation (keyword trigger)
- Inactivity confirmation (user must confirm safety periodically)
- Wearable device integration (smartwatch panic button)
- Automatic distress pattern detection

#### Reporting and Analytics
- Alert statistics dashboard for administrators
- Response time analytics (verify RNF5 compliance)
- Geographic hotspots visualization
- User engagement metrics

#### Administrative Features
- Audit log viewer
- Bulk user operations
- System configuration management
- Moderation queue for reported content

---

## Horizon 3 — Long-term Evolution (Sprint 9+)

**Objective:** Continuous improvement based on real-world usage and stakeholder feedback.

**Timeline:** Ongoing, based on user feedback and organizational priorities

### Future considerations

#### UX and Usability
- Accessibility improvements (screen reader support, high contrast)
- Offline mode for areas with no connectivity
- Reduced cognitive load interface design
- User onboarding flow optimization

#### Integration
- Integration with Colombian emergency services (123 line)
- Integration with women's support organizations
- API for third-party developers (with approval)
- Wearable-specific app versions

#### Privacy and Security
- End-to-end encryption for all communications
- Zero-knowledge architecture for sensitive data
- GDPR-like data portability features
- Blockchain-based evidence verification

#### AI and Automation
- Predictive risk assessment based on location patterns
- Automated alert escalation (contacts → authorities)
- Pattern recognition for false positive reduction
- Chatbot for resource directory queries

#### Community Features
- Verified user communities (trusted networks)
- Peer support connection features
- Anonymous reporting channels
- Community-driven safety resources

---

## Dependencies between horizons

| Horizon | Dependency on previous horizon | Reason |
|---------|------------------------------|--------|
| H2 | H1 core features stable | Cannot iterate on features that don't exist yet |
| H3 | H2 operational data | Real-world usage data needed to inform H3 decisions |
| H3 evidence sharing | H1 evidence recording | Need base evidence capture before sharing logic |
| H3 integration with 123 | H1 multi-channel notifications | Established notification patterns needed for external integration |

---

## Risk-based scope adjustments

| Risk | Probability | Impact | Mitigation / Scope adjustment |
|------|-------------|--------|-------------------------------|
| GPS accuracy insufficient indoors | Medium | High | Accept lower accuracy in H1, improve in H2 with Wi-Fi positioning |
| High false positive rate from sudden movement | Medium | High | Add machine learning in H2, start with sensitivity threshold in H1 |
| Cloud storage costs exceed budget | Low | High | Implement retention policy in H1, optimize compression in H2 |
| User adoption lower than expected | Medium | High | Focus on H1 usability, gather feedback in H2 before major features |

---

## Success criteria per horizon

### Horizon 1 (MVP)
- ✅ RNF5: ≥95% of alerts dispatched within ≤2 seconds
- ✅ RNF6: Battery consumption ≤10% per hour of continuous GPS use
- ✅ RNF7: Location accuracy 10-20m
- ✅ End-to-end alert flow (activation → delivery → location sharing) working
- ✅ Minimum 20 pilot users for testing

### Horizon 2 (Iteration)
- ✅ False positive rate < 5% (measured by cancelled alerts)
- ✅ Evidence upload success rate > 98%
- ✅ Average alert response time reduced by 20% vs. H1 baseline
- ✅ User retention rate > 60% after 30 days

### Horizon 3 (Long-term)
- ✅ Integration with at least one external support organization
- ✅ Community-reported zones with >80% approval rate
- ✅ User-reported satisfaction score > 4.0/5.0

---

## Correlations

* Product Vision → `03-product/vision.md`
* User Stories implementing roadmap → `04-requirements/user-stories.md`
* Technical backlog for deferred features → `15-project-control/technical-backlog.md`
