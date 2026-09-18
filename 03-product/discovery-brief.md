# Discovery Brief — Woman Alert

This document captures findings from user research conducted by the team to validate assumptions about the problem and solution.

---

## Research conducted

### Method 1: Written survey

**When:** June 9 – July 2, 2025  
**Participants:** 24 ADSO students from group 3145556 (21 male, 3 female)  
**Format:** Online questionnaire

#### Key findings

| Question | Result | Insight |
|----------|--------|---------|
| Do you consider this app necessary or very necessary? | 21/24 (87.5%) | Strong perceived need across diverse demographics |
| What is the biggest obstacle to using such an app? | 12/24 (50%) — no mobile phone with internet access | Digital divide is a real barrier; fallback channels (SMS) are critical |
| Which activation method do you prefer? | 12/24 (50%) — shaking the phone | Gesture-based activation resonates with users |
| Would you trust an app with your location in an emergency? | 20/24 (83%) | Privacy concerns are secondary to safety in emergency context |

#### Assumptions validated

✅ **Validated:** Users are willing to share location in emergency situations  
✅ **Validated:** Non-obvious activation methods (shaking, gestures) are preferred over navigating an app interface  
✅ **Validated:** SMS as a fallback channel is necessary (not all users have reliable internet)

#### Assumptions invalidated

❌ **Invalidated:** The assumption that women would be the primary respondents — the survey was predominantly male, indicating a potential sampling bias that needs correction in future research.

---

### Method 2: Oral survey / informal interviews

**When:** Approximately March 27, 2026  
**Participants:** ~4 training environments (second floor, including multimedia), ~5 instructors  
**Format:** In-person discussions, no gender exclusion  

#### Key findings

- **Activation methods:** Repeated suggestions for alternative activation methods beyond the main panic button (shaking, physical buttons, discreet patterns)
- **Response time concern:** Participants highlighted the risk that users may not react in time to activate the alert — this informs the need for automatic triggers (sudden movement detection)
- **UI elements:** Colors were discussed as important for visibility under stress
- **Feature request:** Inclusion of an assistance phone number directly within the app was suggested

#### Open questions identified

- What is the minimum interaction required to trigger an alert without accidental activation?
- How should the app handle false positives (alerts triggered by mistake)?
- Should there be a "discreet mode" that makes the app appear as something else?

---

## User personas synthesized

### Persona 1: María — Primary user (mobile app)

**Age:** 28  
**Location:** Neiva, Huila  
**Context:** Lives alone, works evening shifts, walks home at night  
**Tech proficiency:** Basic — uses WhatsApp and social media, but not tech-savvy  
**Primary need:** Activate an alert in under 5 seconds without drawing attention  
**Constraints:** May have unstable mobile data connection in certain areas  

### Persona 2: Carlos — Administrator (web platform)

**Age:** 35  
**Role:** System administrator for a local support organization  
**Context:** Manages user accounts, reviews reports, monitors active alerts  
**Tech proficiency:** Advanced — comfortable with web administration panels  
**Primary need:** Quick access to real-time alert status and user information  
**Constraints:** Must handle multiple concurrent alerts during peak times  

---

## Competitor analysis

| Solution | Strengths | Weaknesses | Differentiation opportunity for Woman Alert |
|-----------|-----------|------------|-------------------------------------------|
| Emergency hotlines (123) | Direct human contact, government-backed | Requires phone call, no location sharing, no evidence recording | Multi-channel, automated, evidence preservation |
| Personal safety apps (commercial) | Rich features, polished UI | Subscription costs, privacy concerns, not localized to Colombia | Free, privacy-first, designed for Colombian context |
| Family location sharing (Google Maps, Find My) | Real-time tracking, familiar interface | Not emergency-focused, no panic button, no evidence | Emergency-optimized, dedicated to violence prevention |

---

## Technical constraints discovered

| Constraint | Impact | Mitigation |
|------------|--------|------------|
| GPS accuracy in urban canyons | Location may be inaccurate indoors | Accept 10-20m accuracy (per RNF7), use Wi-Fi positioning as fallback |
| Battery drain from continuous GPS | Users may disable location to save battery | Implement RNF6 (≤10% battery/hour), allow user-controlled tracking intervals |
| Network failures in rural areas | Alerts may not reach contacts | Implement Chain of Responsibility pattern for channel retry (RNF9) |
| Device storage limits for evidence | Audio/video files consume space | Use cloud storage with local deletion after upload |

---

## Insights for backlog prioritization

Based on discovery findings, the following features should be prioritized:

1. **Sudden movement detection** — Users highlighted that they may not react in time to manually activate alerts
2. **SMS fallback channel** — 50% of survey respondents lack reliable internet access
3. **Discreet activation methods** — Both surveys indicated preference for non-obvious triggers
4. **Evidence recording** — Unique differentiator vs. existing hotlines; critical for legal support

---

## Correlations

* Problem Framing → `03-product/problem-framing.md`
* User Stories implementing discoveries → `04-requirements/user-stories.md`
* Technical constraints → `05-architecture/decisions/`
* Data model supporting evidence requirements → `06-data/models.md` (evidence table)
* Security requirements for privacy → `00-governance/security-policy.md`
