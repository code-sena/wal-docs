# Product Vision — Woman Alert

This is the vision for **Woman Alert**, built from the general project document (v1.9), SRS v2.6 and the state of the code published in `main` on 28/10/2026.

## Vision Statement

For women in Colombia, and in Huila in particular, who may face a risk situation and need to ask for help quickly, **Woman Alert** is a solution made up of an Android app, from which alerts are activated with as few steps as possible, and a complementary web platform for consultation, administration and alert tracking. The app notifies the user's trusted contacts by email and push, shares her location while the alert is active, records evidence and offers discreet options for when someone is watching the phone.

Unlike the official channels, which are phone or in-person services (123 and 155), our product gathers the alert, the trusted contacts, the location and the evidence in one tool. It is a complementary support tool: it does not guarantee that an alert reaches its recipient and it does not replace the National Police, the Attorney General's Office, the Family Commissioners or the official lines. The product prioritizes women in its name, context and design criteria, but does not deny access to anyone.

## Team Mission

Apply the competences of the ADSO training program (requirements, analysis, design, development, integration, testing and documentation) to build a tool that makes it easier for a woman at risk to ask for help and share her location with the people she trusts.

## Strategic Pillars

| Pillar                  | Description                                                                              | Success Metrics                                                                           |
| ----------------------- | ---------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| Preparation             | Tutorial, trusted contacts, activation methods, cancellation and duress PINs, assistance lines | Users with at least one contact with email; tutorial completed (not measured yet)   |
| Immediate Response      | Fast activation (SOS, contact, shake, widget), 5-second window, notices by email and push | RNF-REN-001: ≥95 % of alerts dispatched to the providers within 2 s (not measured yet)    |
| Accompaniment           | Periodic location, evidence, chat, public tracking page, resend, lost-signal notice, trips and automatic protection | Notices opened per alert (`seen_at`); alerts with a lost-signal notice      |
| Management and Analysis | Admin panel: dashboard, users, alerts, zones, reports, evidence, moderation, audit and configuration | Reports exported per period; moderation cases resolved                             |

## High-Level Roadmap

| Horizon                             | Objective                                        | Modules / Scope |
| ----------------------------------- | ------------------------------------------------ | --------------- |
| H1 — delivered (APK v1.0.0, 28/10/2026) | Deliver the core system | Accounts and Google login, contacts, activation methods with cancellation and duress PINs, notices by email and push with tracking link, location, evidence, chat, history, map and zones, assistance, trips, automatic protection, user portal, admin panel, four languages and six themes |
| H2 — next | Verify and close gaps | Manual tests of the native features with the release APK (PM-04 to PM-09), open security findings, privacy policy and terms, accessibility, web parity (trips, personal data), local queue to create the alert without connection |
| H3 — later | Improve with real use | Validation with users from Huila, measurement of RNF-REN-001 to 003, hosting without sleep, production AAB, gateway decision, encryption of evidence |

## Product Principles

1. **Accessible even under stress** (RNF-USB-001): visible controls and clear messages, with as few steps as possible.
2. **Activation in the fewest possible steps** (RNF-USB-002): SOS button, alert to a contact, shake and widget, plus server activation for trips and automatic protection.
3. **Discretion by design:** discreet mode, duress PIN, cancelling with the volume button, recording without preview.
4. **Privacy by default** (RNF-PRI-001, RNF-PRI-002): each user only sees her data; the administration never sees the contacts' personal data; evidence and locations have retention periods.
5. **Honesty about delivery** (RN-05, RN-15): a pending notice is never shown as sent, and the app never promises that help will arrive.

## Definition of Done

**Objective:** deliver the core system with the critical requirements verified by recorded tests.

| Key Result                                                        | Baseline | Objective                                                     | Date / status                    |
| ----------------------------------------------------------------- | -------- | ------------------------------------------------------------- | -------------------------------- |
| KR1: Automated tests passing in CI                                | 0        | 100 % green on every pull request                             | Met — 553 tests, 28/10/2026      |
| KR2: Alerts dispatched within the time limit (RNF-REN-001)        | —        | ≥95 % within 2 s until delivery to the provider               | Not measured                     |
| KR3: Device compatibility (RNF-COM-001)                           | —        | Android 7.0 (API 24) or later                                 | Met in the APK; tested on 2 phones |
| KR4: Manual tests of the native features with the release APK     | 0 of 6   | PM-04 to PM-09 recorded                                       | Pending                          |

## Correlations

* Problem Framing (the why) → `03-product/problem-framing.md`
* Backlog implementing the vision → `04-requirements/user-stories.md`
* KPIs in operations → `13-operations/README.md`
* Architecture supporting the vision → `05-architecture/overview.md`
* Data model enabling the vision → `06-data/models.md`
