# Discovery Brief — Woman Alert

> What the team learned before and during the construction of Woman Alert, and how much weight each
> source can carry. Official figures are cited with their source in `03-product/problem-framing.md`.
> Team consultations are recorded honestly: they guided design decisions, but there is no preserved
> evidence to present them as validation.

## Research conducted

### Method 1: Written survey

The team applied a questionnaire to potential users to choose colors, themes and activation methods.

#### Key findings

- Shaking the phone appeared as a desired activation method, because a person at risk may not have time to open the app.
- Users asked for discreet ways to activate and cancel, because the aggressor may be close.
- Several people mentioned unstable mobile data, especially outside the city.

#### Assumptions validated

None can be declared validated: the team does not keep the instrument, the results or the list of participants.

#### Assumptions invalidated

None recorded.

### Method 2: Oral survey / informal interviews

A debate session with users and instructors to discuss the activation methods and the visual design.

#### Key findings

- **Activation methods:** repeated suggestions for alternatives to the main button (shaking, discreet patterns), which led to the shake detection, the widget and the alert to a single contact.
- **Discretion:** concern about the app being discovered, which led to discreet mode, the duress PIN and cancelling with the volume button.
- **Themes:** preference for being able to change the colors, which led to six themes.

#### Open questions identified

- Do the activation methods work under real stress and with the phone in a pocket or bag?
- Do contacts understand the email and the tracking page without explanation?
- How many users have the permissions and battery settings that the background features need?
- Is the lack of coverage in rural Huila a blocking barrier for creating the alert?

---

## User personas synthesized

These personas are design hypotheses, not validated profiles.

### Persona 1: María — Primary user (mobile app)

**Age:** 28  
**Location:** Neiva, Huila  
**Context:** Lives with her partner, works evening shifts and takes a motorbike taxi home at night  
**Tech proficiency:** Basic — uses messaging apps and social media  
**Primary need:** Notify her sister and a friend in seconds, without unlocking the phone and without the partner noticing  
**Constraints:** Unstable mobile data in some areas; a budget phone with aggressive battery saving  

### Persona 2: Lucía — Trusted contact

**Age:** 45  
**Context:** María's sister; does not have the app installed  
**Primary need:** Understand immediately that the notice is real, see where María is and know whether someone is already responding  
**Constraints:** Reads email on her phone; may not see a notice at night  

### Persona 3: Carlos — Administrator (web platform)

**Age:** 35  
**Role:** Administrator for a local support organization  
**Context:** Reviews alerts, zones, reports and moderation cases  
**Tech proficiency:** Advanced — comfortable with web panels  
**Primary need:** See the state of the alerts and export reports without accessing personal data he does not need  
**Constraints:** Must not see the contacts' personal data  

---

## Competitor analysis

| Solution | Strengths | Weaknesses | Differentiation opportunity for Woman Alert |
|-----------|-----------|------------|-------------------------------------------|
| Line 123 (free) | Official, direct human contact | Requires a call and speaking safely; no shared location with family | Notice to trusted contacts with location, without speaking |
| Line 155 (free, 24 h) | Specialized guidance for women | Phone guidance, not an emergency dispatch; not a reporting channel | Assistance module opens 123, purple line and mental health line in the dialer |
| Family location sharing apps | Real-time location, familiar | Not emergency-focused; no SOS, evidence or discreet cancellation | Emergency flow with countdown, evidence, chat, resend and lost-signal notice |
| Official digital alert apps | — | None with SOS, live location, evidence and "notify me if I don't arrive" was found in the official sources consulted | Woman Alert is presented as complementary, without claiming to be the first of its kind |

---

## Technical constraints discovered

| Constraint | Impact | Mitigation |
|------------|--------|------------|
| Google Maps authorization failed in the APK of 14/10/2026 | Map not visible | Migrated to MapLibre + OpenFreeMap (mobile) and Leaflet + OpenStreetMap (web) |
| Render blocks SMTP | Emails could not be sent | Email by HTTP API (Resend, Brevo as alternative) |
| Free Render plan sleeps after 15 minutes | First request up to about 90 s; scheduled tasks stop | UptimeRobot every 5 minutes; RPC timeout of 90 s |
| Android kills background work | Location, shake and trips stop with the screen off | Kotlin foreground services; permissions and protection section |
| GPS accuracy indoors | Location may be inaccurate | Show the last recorded location with its date and time; never invent one |
| Evidence size | Large files on mobile data | Chunked recording, upload queue, 50 MB limit per file |

---

## Insights for backlog prioritization

1. **Shake detection and widget** — the person may not have time to open the app.
2. **Server-side activation** — trips and automatic protection create the alert even if the phone loses its signal.
3. **Discreet options** — discreet mode, duress PIN and volume cancellation.
4. **Evidence recording** — differentiator compared with the phone lines.
5. **Email with a tracking link** — contacts without the app can still follow the alert (SMS was discarded).

---

## Correlations

* Problem Framing → `03-product/problem-framing.md`
* User Stories implementing discoveries → `04-requirements/user-stories.md`
* Technical constraints → `05-architecture/decisions/`
