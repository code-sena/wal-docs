# Product Vision — Woman Alert

This is the vision for **Woman Alert**, built using real data from the project's SRS. The only points that remain unresolved are the three KPIs for the strategic pillars, which depend on targets that the team still needs to define.

## Vision Statement

For women at risk or victims of gender-based violence in Colombia who need a fast, reliable, and effective way to ask for help, **Woman Alert** is a system composed of a mobile application (primary channel) and a supporting web platform that allows users to activate emergency alerts with real-time location, notify their configured trusted contacts through multiple channels (SMS, email, and push notifications), and access available support information.

Unlike existing institutional mechanisms — which face limited awareness of response pathways and poorly coordinated channels — our product complements these mechanisms with a panic button, real-time tracking, evidence recording, and an administrative dashboard for management and analysis, reducing response times in situations of risk.

## Team Mission

Contribute to reducing response times in situations of gender-based violence in Colombia by empowering women with an accessible, secure, and easy-to-use technological tool for moments of high stress. This mission is derived from the introduction and general objective of the SRS.

## Strategic Pillars

The SRS itself defines four pillars for the operation of the system; these are adopted here as the product's strategic pillars:

| Pillar                  | Description (according to the SRS)                                                       | Success Metrics                                                                           |
| ----------------------- | ---------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| Prevention              | Provide useful information, action guidelines, and safety recommendations                | To be defined with the team (e.g., percentage of users who consult the action guide)      |
| Immediate Response      | Panic button that sends alerts and real-time location to configured contacts             | RNF5 compliance: ≥95% of alerts dispatched within ≤2 seconds                              |
| Support                 | Alert history and continuous monitoring by configured contacts while the alert is active | To be defined with the team (e.g., percentage of alerts with completed active monitoring) |
| Management and Analysis | Administrative system that allows visualization of generated data and statistics         | To be defined with the team (e.g., reports generated per period)                          |

## High-Level Roadmap

The scope defined in the SRS already serves as a concrete Horizon 1. Horizons 2 and 3 are no longer blank — they were populated with the actual content from `03-product/roadmap.md` once that document was defined:

| Horizon                             | Objective                                        | Modules / Scope                                                                                                                                                                                                                                                                                         | Uncertainty |
| ----------------------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| H1 (defined scope)                  | Deliver the core system described in the SRS     | Registration/login, emergency contacts, panic button with multiple activation methods, multi-channel notifications, real-time tracking, evidence (audio/video), history, safe/risk zone map and help directory, language/theme customization, web platform (public website + user portal + admin panel) | Low         |
| H2 (next = Phase 2 of the roadmap)  | Iterate on the MVP once it is working end-to-end | Evidence (audio/video), zone map and help directory, additional activation methods, reports and audit capabilities in the admin panel, multi-channel retry in case of failures                                                                                                                          | Medium      |
| H3 (later = Phase 3 of the roadmap) | Continuous improvement based on real-world usage | Evaluate inactivity confirmation (discovery-brief finding), UX adjustments with real users, formal verification of critical NFRs (RNF5, RNF6, RNF7)                                                                                                                                                     | High        |

## Product Principles

Derived from the non-functional requirements already defined in the SRS. These are not new proposals; they are principles that the system is already required to fulfill:

1. **Accessible even under stress** (RNF11): The interface must be simple and clear, with large visual elements and contrasting colors, allowing the user to interact without complications during an emergency.

2. **Activation in the fewest possible steps** (RNF12): The app must provide at least two rapid activation methods outside the main interface, such as a widget, physical button, or touch pattern.

3. **Privacy and data security by default** (RNF1, RNF8, RNF16): Profile photos must not be publicly accessible, information must be encrypted in transit (HTTPS/TLS) and at rest (AES-256), and access must be restricted according to the user's role.

4. **Fault tolerance in alert delivery** (RNF9): If one communication channel fails, the system automatically retries through another available channel.

## Definition of Done

**Objective:** Fulfill the core system described in the SRS scope, with the critical non-functional requirements verified.

| Key Result                                                        | Baseline | Objective (according to the SRS)                              | Date                             |
| ----------------------------------------------------------------- | -------- | ------------------------------------------------------------- | -------------------------------- |
| KR1: Percentage of alerts dispatched within the time limit (RNF5) | —        | ≥95% within ≤2 seconds until delivery to the channel provider | October 20, 2026 (delivery date) |
| KR2: Location accuracy during an active alert (RNF7)              | —        | 10–20 meters, subject to GPS signal                           | October 20, 2026 (delivery date) |
| KR3: Device compatibility (RNF10)                                 | —        | Android 10 and later                                          | October 20, 2026 (delivery date) |

## Correlations

* Problem Framing (the why) → `03-product/problem-framing.md`
* Backlog implementing the vision → `04-requirements/user-stories.md`
* KPIs in operations → `13-operations/README.md`
* Architecture supporting the vision → `05-architecture/overview.md`
* Data model enabling the vision → `06-data/models.md`
