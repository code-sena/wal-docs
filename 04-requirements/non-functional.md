# Non-Functional Requirements — Woman Alert

> The original SRS IDs (RNF1–RNF18) are preserved and organized by category according to the format of this folder. Where the SRS already defines a numerical metric, it is copied exactly as specified. Where the RNF is qualitative (without a numerical value), this is explicitly stated in the "Metric" column instead of inventing a number — this avoids falling into the "The system must be secure" error that this document itself warns against.

## Performance

| ID   | Requirement          | Metric                                                                                                                                                                                                          | How to Verify                                                                  |
| ---- | -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| RNF5 | Alert performance    | Alert dispatch must take a maximum of 2 seconds; at least 95% of alerts must meet this requirement under normal network conditions. | Load testing / backend measurement from activation to delivery to the provider |
| RNF6 | Battery optimization | GPS/accelerometer usage in the foreground must not exceed 10% battery consumption per hour of continuous use.                                                                                                   | Battery consumption measurement during field testing with the device           |

## Availability and Fault Tolerance

| ID    | Requirement                   | Metric                                                                                                                                                                                                                                                                               | How to Verify                                                                           |
| ----- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------- |
| RNF4  | Internet connectivity         | The application requires internet access to send alerts, location data, and notifications (SMS/email/push); successful delivery also depends on third-party services (FCM, SMS provider, email server) and the status of the receiving device — no SLO metric is defined in the SRS. | Connectivity and integration testing with external providers                            |
| RNF9  | Alert channel fault tolerance | If a channel (SMS, email, or push) fails, the system must automatically retry using another available channel.                                                                                                                                                                       | Simulated channel failure testing (Chain of Responsibility pattern)                     |
| RNF13 | Background operation          | The application must send alerts and monitor events even when closed or running in the background, using a Foreground Service, within the manufacturer's power-management limitations.                                                                                               | Testing with the application in the background/closed on different device manufacturers |

## Security

| ID    | Requirement                      | Description                                                                                                                                                                 |
| ----- | -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RNF1  | Profile photo privacy            | Profile photos must not be publicly visible; only authorized personnel may access them when necessary to respond to an alert.                                               |
| RNF8  | Data security and protection     | All personal information, contacts, messages, and location data must be transmitted using HTTPS/TLS encryption and stored using AES-256 encryption.                         |
| RNF14 | Role-based access control        | The system must restrict functionality according to the authenticated user's role (user / administrator).                                                                   |
| RNF15 | Web authentication security      | The web platform must protect sessions and credentials through appropriate authentication and authorization mechanisms.                                                     |
| RNF16 | Sensitive information protection | Personal data, contacts, locations, alerts, and evidence must be restricted according to roles/permissions; one user must not be able to access another user's information. |

## Location and Sensors

| ID   | Requirement                       | Metric                                                                                                                                                                                                        | How to Verify                                              |
| ---- | --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| RNF2 | GPS availability                  | The system must maintain access to location data for alerts requiring accurate positioning; if the signal is lost, the last known location must be stored.                                                    | Testing with intermittent GPS signal                       |
| RNF7 | Location reliability and accuracy | Location accuracy must be between 10 and 20 meters depending on GPS signal quality, with regular updates while the alert is active; if optimal accuracy is unavailable, the last known location must be used. | Comparison of reported coordinates against a GPS reference |

## Usability and Accessibility

| ID    | Requirement                 | Description                                                                                                                                                            |
| ----- | --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RNF3  | Notification frequency      | The application must allow users to configure notification frequency and type, avoiding notification overload or anxiety.                                              |
| RNF11 | Accessibility and usability | The interface must be simple, clear, and accessible, with large visual elements and contrasting colors, allowing it to be used effectively under stressful situations. |
| RNF12 | Rapid emergency activation  | At least two rapid activation methods must be available outside the main interface: floating widget, physical button (volume), or touch pattern (three fingers).       |

## Compatibility

| ID    | Requirement                    | Description                                                                                                                                                                             |
| ----- | ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RNF10 | Device compatibility           | Compatible with Android version 10 and later, providing support for advanced device sensors.                                                                                            |
| RNF17 | Web compatibility              | Compatible with modern browsers: Google Chrome, Microsoft Edge, and Mozilla Firefox in recent versions.                                                                                 |
| RNF18 | Web platform usage environment | Primarily oriented toward computers/desktop resolutions, especially for administrative functions; a mobile view may be available, but mobile optimization is not the primary objective. |

## Identified Gaps (Not Invented, Only Flagged)

* The SRS does not define a **scalability** NFR with its own metric (e.g., the number of concurrent users supported). It only states in the constraints (Section 2.3) that "the architecture must support multiple concurrent connections... and simultaneous alerts from multiple users," without specifying a numerical value. If the team defines a target number, it should be added here as a new NFR.
* The SRS does not define an **observability** NFR (logs, metrics, traces) or a **maintainability** NFR (test coverage, cyclomatic complexity) with specific metrics. These appear in the reference document (NFR-005, NFR-006), but not in the Woman Alert SRS. They are not invented here; they remain pending items to be defined by the team if they decide to adopt them.

## Correlations

* Functional requirements → `04-requirements/functional.md`
* Architectural decisions that depend on these NFRs → `05-architecture/`
* Traceability matrix → `04-requirements/traceability-matrix.md`
