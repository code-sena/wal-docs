# Problem Framing — Woman Alert

This document describes the problem that Woman Alert addresses, using real data taken from the project's SRS and from the team's own research (written survey and oral round). A few points remain undefined because they depend on a team decision rather than an existing source — these are marked as "pending" instead of being filled in with an invented number.

## 1. The problem in one sentence

Women in Colombia who face situations of risk or gender-based violence struggle with the lack of timely and effective response mechanisms due to the absence of accessible and secure tools that allow them to quickly communicate with their support networks, resulting in victims remaining at risk without adequate support.

## 2. Affected users

| Segment                         | Description                                                                                                                                                            | Estimated size (according to sources cited in the SRS)                                                                                                                                                                                                                                                                  | Priority |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| User (mobile app)               | Woman experiencing a situation of risk or gender-based violence who uses the app to activate alerts, manage trusted contacts, consult safe areas and support resources | Initial scope: Neiva, Huila (the city where the team is based). The INS already identifies Huila among the departments with the highest concentration of gender-based violence cases nationwide. The system is designed to scale to other departments later, but the starting point and initial validation are in Neiva | High     |
| User (authenticated web portal) | Same user, when she needs to review her history, location, evidence, or device status from a computer (the web complements the app; it does not replace it)            | Subset of mobile users                                                                                                                                                                                                                                                                                                  | Medium   |
| Administrator (web platform)    | Personnel responsible for managing users, alerts, areas, evidence, reports, system configuration, and moderation from the administrative panel                         | Number to be defined by the team                                                                                                                                                                                                                                                                                        | Medium   |

## 3. Jobs to Be Done (JTBD)

When a user is in a situation of risk or gender-based violence, she wants to activate an emergency alert that shares her real-time location and immediately notifies her trusted contacts through multiple channels (SMS, email, push notifications), so that she can receive help quickly, safely, and discreetly, even in high-stress situations.

## 4. Evidence of the problem

| Type of evidence               | Source                                                                                                                                                          | Date                       | Key finding                                                                                                                                                                                                                                                                                  |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Institutional report           | Ombudsman's Office (Defensoría del Pueblo)                                                                                                                      | Jan–Apr 2025               | 123 official femicides, 79 attempted homicides potentially related to gender, 5,307 cases of domestic violence against women, and 3,804 sexual offenses (2,011 involving girls and adolescents)                                                                                              |
| Observatory report             | Observatorio Feminicidios Colombia (via Noticias RCN)                                                                                                           | Through Jul 2025           | 501 femicides and 296 attempted femicides nationwide; Antioquia, Bogotá, Atlántico, Valle del Cauca, Magdalena, Santander, and Norte de Santander were the most affected; 72 femicides occurred in July alone, across 20 departments                                                         |
| Press report                   | Infobae                                                                                                                                                         | Mar 2025                   | 23.6% increase in domestic violence in Bogotá compared with previous periods; 73% of victims were women                                                                                                                                                                                      |
| Epidemiological bulletin       | National Institute of Health (INS)                                                                                                                              | As of Jun 2024             | 66,621 cases of gender-based violence in Colombia, 75.6% against women (50,374 cases), concentrated in Bogotá, Antioquia, Cundinamarca, Cali, Valle del Cauca, Santander, and Huila                                                                                                          |
| Written survey (internal team) | Survey of 24 ADSO students from group 3145556 (21 male, 3 female)                                                                                               | Jun 9 – Jul 2, 2025        | 21/24 considered the app necessary or very necessary; the greatest perceived obstacle was not fear but not having a mobile phone with internet access (12/24); activation by "shaking the phone" received the most votes (12/24)                                                             |
| Oral survey (internal team)    | Approximately 4 training environments (all on the second floor, including multimedia) and 5 instructors (including one from another group); no gender exclusion | Approximately Mar 27, 2026 | Repeated suggestions were made regarding activation methods (shaking, buttons); the risk that the user may not react in time and therefore prevent the alert from being activated was highlighted; colors were discussed; the inclusion of an assistance number within the app was suggested |

## 5. Current user solution (and its problems)

| Current solution                                                                                      | Limitations                                                                                                                                                                    | Cost/Friction                                                      |
| ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------ |
| Existing institutions and support programs (hotlines, police/family courts, health centers, shelters) | Barriers that make immediate access to help difficult persist; limited awareness of response routes and limited coordination between available channels (according to the SRS) | Victims remain without adequate support and continue to be at risk |
| Manual communication with the support network (calls, messages)                                       | Does not share real-time location or record the interaction as evidence                                                                                                        | Depends on someone answering; no subsequent backup or record       |

## 6. Solution hypothesis

We believe that designing and implementing a system composed of a mobile application (primary channel) and a supporting web platform — featuring a panic button, multi-channel notifications to trusted contacts, real-time tracking, evidence recording, and a directory of support resources — will reduce response times in situations of gender-based violence. We will know we have achieved this when the system meets the alert-processing times defined in the requirements (see RNF5 below) and users report having received help through the app.

## 7. Success metrics (North Star)

These metrics are already defined in the SRS as non-functional requirements (NFRs); they are not hypotheses to be validated but rather system objectives:

| Metric                     | Baseline | Objective defined in the SRS                                                                                                                                                | How to measure it                                                  |
| -------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| Alert dispatch time (RNF5) | —        | Maximum 2 seconds from activation until delivery to the channel provider (FCM, SMS, or email); at least 95% of alerts must meet this target under normal network conditions | Backend measurement from activation until delivery to the provider |
| Location accuracy (RNF7)   | —        | Updates with an accuracy of 10 to 20 meters, subject to GPS signal quality                                                                                                  | Comparison of reported coordinates against a GPS reference         |
| Battery consumption (RNF6) | —        | GPS/accelerometer usage in the foreground must not exceed 10% battery consumption per hour of continuous use                                                                | Measurement of battery consumption during field tests              |

**North Star Metric:** To be defined by the team. The natural candidate, based on what is already available, is the percentage of alerts handled within the RNF5 target time.

## 8. Hypothesis risks

| Risk                                                                                                                     | Probability     | Impact | Validation experiment                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------ | --------------- | ------ | ------------------------------------------------------------------------------------------------------------------------ |
| Dependence on internet connectivity and third-party services (FCM, SMS provider, email server) for alert delivery (RNF4) | To be evaluated | High   | Failure-tolerance tests using the Chain of Responsibility pattern already defined (retry through an alternative channel) |
| Users may not activate the app at the actual moment of risk                                                              | To be evaluated | High   | Validate quick activation methods with users (RNF12: widget, physical button, touch pattern)                             |

## 9. Out of scope (not addressed in this cycle)

* There is currently no dedicated RF for "anonymity" in the SRS; the possibility of sending an alert without identifying oneself should be incorporated as an acceptance criterion for RF4/RF12, rather than as a separate module (explicit note from the SRS itself).

* The web platform does not replace the mobile app: it is a supporting tool (public site, user portal, and administrative panel), while the mobile app remains the primary channel for activating alerts.

## Correlations

* Product vision → `03-product/vision.md`

* User Stories that implement this solution → `04-requirements/user-stories.md`

* Detailed KPIs → `13-operations/README.md`

* Domain entities discovered through problem framing → `02-domain/entities-and-rules.md`

* Non-functional requirements derived from evidence findings → `04-requirements/non-functional.md`
