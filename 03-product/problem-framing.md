# Problem Framing — Woman Alert

This document describes the problem that Woman Alert addresses, using official figures cited in the general project document (v1.9). Each figure keeps its source, date and scope, because the sources measure different things (cases notified to the health system, forensic assessments and surveys) and must not be compared directly.

## 1. The problem in one sentence

How can a woman at risk ask for help quickly and share her location with the people she trusts, when lack of signal, fear of being discovered, not knowing the support routes and the time it takes to reach someone can delay or prevent the request?

## 2. Affected users

| Segment                         | Description | Estimated size |
| ------------------------------- | ----------- | -------------- |
| User (mobile app)               | Person, mainly women, who uses the app to prepare her contacts and activate alerts | Context: Colombia and Huila; no own estimate for smaller areas |
| Trusted contact                 | Family member or friend who receives the notice and follows the alert without an account | Up to 5 per user by default (1 to 20 configurable) |
| User (authenticated web portal) | Same user, when she reviews her information from a computer | Subset of mobile users |
| Administrator (web platform)    | Staff who manage users, alerts, zones, reports, evidence, moderation and configuration | Defined by the operating organization |

## 3. Jobs to Be Done (JTBD)

When a user is in a risk situation, she wants to notify her trusted contacts in seconds, with her location and without having to unlock the phone, search for a number or explain what is happening, so that someone she trusts knows she needs help and can follow what is happening. When someone is watching her phone, she wants to do it without being discovered.

## 4. Evidence of the problem

| Type of evidence               | Source | Date | Key finding |
| ------------------------------ | ------ | ---- | ----------- |
| Health surveillance            | National Institute of Health (INS) | 2024, period XII (preliminary) | 154,312 suspected cases of gender-based and domestic violence; rate 292.2 per 100,000; 76.2 % women |
| Health surveillance, Huila     | INS | 2024 | Rate of 509.5 per 100,000 in Huila, above the national rate |
| Department report              | Gobernación del Huila | 2025 data, published 2026 | Rate of 534.0 suspected cases notified to Sivigila; greater impact on women and in urban areas; gaps and underreporting in rural areas |
| Forensic assessments           | INMLCF | 2024 (preliminary) | 62,860 domestic violence assessments, 47,855 women; intimate partner violence: 35,063 of 40,778 were women |
| Victimization survey           | DANE, ECSC | 2023 data | 69.7 % of women aged 15+ who were victims of a crime did not report it (80.6 % in rural areas) — it measures crimes in general, not gender violence |
| Connectivity                   | DANE | 2024 | 65.6 % of households with Internet; 41.9 % in rural areas |
| Policy framework               | CONPES 4080 of 2022; Laws 1257 of 2008, 1761 of 2015 and 1581 of 2012 | — | Barriers to access for rural women; protection of women; treatment of personal data |

**Team consultation.** According to the team, some design decisions (colors, themes and activation methods such as shaking the phone) were taken with users through a questionnaire and a debate session. The team does not keep the instrument, the results or the list of participants, so this process is not presented as validation of the design. The five barriers (no signal, difficulty sharing the location, fear of being discovered, not knowing the routes, difficulty reaching contacts) are treated as design hypotheses, not as local findings.

## 5. Current user solution (and its problems)

| Current solution | Limitations |
| ---------------- | ----------- |
| Line 123 (free) and line 155 (free, 24 hours, guidance for women) | Phone channels: they do not solve lack of coverage or the impossibility of speaking safely |
| Calls or messages to family and friends | Time to search, unlock and explain; the contact may not see or understand the urgency; no shared location or record |
| Official digital alert tools | No official Colombian app with SOS button, live location, evidence recording and "notify me if I don't arrive" was found in the sources consulted; none with verifiable name and validity in Huila |

## 6. Solution hypothesis

We believe that an Android app that lets the user activate an alert with few steps, notifies her trusted contacts by email and push with a public tracking link, shares her location, records evidence and offers discreet options, supported by a web platform for consultation and administration, will make it easier to ask for help in time. We will know it works when tests on real phones show that the notices arrive and when users from Huila confirm that the activation methods are usable under stress.

## 7. Success metrics (North Star)

| Metric | Baseline | Objective | How to measure it |
| ------ | -------- | --------- | ----------------- |
| Alert dispatch time (RNF-REN-001) | — | ≤ 2 s from activation to delivery to the provider (Resend or FCM) for ≥95 % of alerts | Backend timestamps per notice (not measured yet) |
| Location accuracy (RNF-REN-003) | — | 10 to 20 m, subject to GPS signal | Comparison of reported coordinates (not measured yet) |
| Battery consumption (RNF-REN-002) | — | ≤ 10 % per hour of foreground service | Measurement on physical phones (not measured yet) |
| Notices opened | — | To be defined | `alert_contact.seen_at` and `tracking_view` |

**North Star Metric:** percentage of alerts in which at least one contact opens the notice (`seen_at`) within 5 minutes.

## 8. Hypothesis risks

| Risk | Probability | Impact | Validation experiment |
| ---- | ----------- | ------ | --------------------- |
| Delivery depends on connectivity, Resend, FCM and the contact (RNF-DIS-002) | Medium | High | Manual tests between two phones with the release APK (PM-04 to PM-09) and review of `alert_contact` failures |
| Users may not activate the app at the real moment of risk | Medium | High | Validate the activation methods (SOS, contact, shake, widget) with users from Huila |
| Someone watching the phone discovers the app | Medium | High | Test discreet mode, duress PIN and volume cancellation with users |
| Android restrictions stop the background features | High | Medium | Tests on several manufacturers with the screen off |

## 9. Out of scope (not addressed in this cycle)

* Anonymous alerts: every alert belongs to an account; what is protected is the privacy of the contacts and the discretion of the user.
* SMS, WhatsApp and automatic calls.
* The web platform does not activate alerts: the app is the only channel for that.

## Correlations

* Product vision → `03-product/vision.md`
* Discovery brief → `03-product/discovery-brief.md`
* Requirements → `04-requirements/functional.md`
