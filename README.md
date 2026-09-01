# Ova Security

Ova Security is a compact cybersecurity appliance architecture designed to improve local network visibility, defensive control, event traceability, and assisted decision-making.

This repository is intentionally public and limited to a technical architecture overview. It does not contain source code, credentials, production configuration, private IP addresses, offensive procedures, or internal operational secrets.

## 1. Project vision

Many small and medium networks have the same security problem: tools are separated, logs are difficult to exploit, alerts lack context, and decisions are hard to justify during an incident or an audit.

Ova Security proposes a local appliance that centralizes the main defensive functions:

- observe the network and connected assets;
- detect suspicious activity;
- prioritize vulnerabilities and risky devices;
- apply controlled defensive filtering;
- keep signed local evidence;
- help administrators interpret events and reports.

The goal is not to replace a full security operations center. The goal is to provide a compact, understandable, locally controlled layer for environments that need better visibility and stronger governance without deploying a complex security platform.

## 2. High-level architecture

```mermaid
flowchart LR
  WAN[Internet / upstream network]
  Router[Router]
  Appliance[Ova Security appliance]
  LAN[Protected network]
  Assets[Workstations, servers, IoT]
  Admin[Administration workstation]

  WAN --> Router --> Appliance --> LAN --> Assets
  Admin --> Appliance
```

The appliance is placed at the network edge, between the upstream router and the protected network. It observes traffic, receives security events, consolidates relevant information, and exposes a controlled administration surface.

## 3. Logical architecture

```mermaid
flowchart TB
  subgraph Input["Input layer"]
    Traffic[Network traffic]
    Logs[System and security logs]
    Assets[Asset discovery data]
    Reports[Reports and documentation]
  end

  subgraph Core["Ova Security appliance core"]
    Collection[Collection and normalization]
    Detection[Detection and correlation]
    Vulnerability[Vulnerability prioritization]
    Filtering[Defensive filtering]
    Ledger[Signed evidence register]
    Assistant[Decision-support assistant]
  end

  subgraph Output["Operator layer"]
    Console[Administration console]
    Alerts[Prioritized alerts]
    Evidence[Audit-ready evidence]
    Actions[Validated defensive actions]
  end

  Input --> Collection
  Collection --> Detection
  Collection --> Vulnerability
  Detection --> Alerts
  Vulnerability --> Alerts
  Alerts --> Console
  Console --> Actions
  Actions --> Filtering
  Detection --> Ledger
  Vulnerability --> Ledger
  Actions --> Ledger
  Reports --> Assistant
  Logs --> Assistant
  Assistant --> Console
  Ledger --> Evidence
```

## 4. Main functional modules

| Module | Role | Output |
| --- | --- | --- |
| Asset inventory | Identifies active devices and tracks their state. | Known assets, unknown assets, risk context. |
| Network observation | Collects network activity and service signals. | Flow visibility and suspicious behavior indicators. |
| Intrusion detection | Detects abnormal or hostile activity patterns. | Alerts with severity and affected assets. |
| Vulnerability analysis | Helps identify exposed services and weaknesses. | Prioritized remediation list. |
| Defensive filtering | Applies approved filtering decisions. | Controlled access restrictions. |
| Evidence register | Records important events and decisions. | Signed and verifiable traceability. |
| Assistant | Helps explain alerts, reports, and context. | Human-readable analysis with sources. |
| Reporting | Produces operational and audit-oriented summaries. | Reports for administrators and reviewers. |

## 5. Layered design

```mermaid
flowchart TB
  L5[Presentation and reporting]
  L4[Decision support and governance]
  L3[Detection, analysis, and prioritization]
  L2[Collection, parsing, and normalization]
  L1[Network edge and appliance services]

  L1 --> L2 --> L3 --> L4 --> L5
```

### Network edge and appliance services

This layer represents the physical and logical position of the appliance. It provides the network point where traffic can be observed and where approved defensive policies can be enforced.

### Collection, parsing, and normalization

This layer transforms heterogeneous events into a consistent internal format. It reduces fragmentation between network traffic, system logs, vulnerability information, and administrative actions.

### Detection, analysis, and prioritization

This layer turns raw observations into security signals. It connects suspicious activity to assets and gives the administrator a clearer view of what matters first.

### Decision support and governance

This layer ensures that sensitive actions remain controlled. The assistant can explain and recommend, but final validation stays with the administrator.

### Presentation and reporting

This layer provides the operator-facing view: assets, alerts, risks, health state, reports, and evidence summaries.

## 6. Data flow

```mermaid
sequenceDiagram
  participant Network as Network activity
  participant Appliance as Ova Security appliance
  participant Analysis as Analysis modules
  participant Console as Administration console
  participant Ledger as Evidence register
  participant Admin as Administrator

  Network->>Appliance: Traffic and events
  Appliance->>Analysis: Normalize and analyze data
  Analysis->>Console: Present alert with context
  Analysis->>Ledger: Record event evidence
  Admin->>Console: Review and validate action
  Console->>Appliance: Send validated decision
  Appliance->>Ledger: Record decision and result
```

## 7. Traceability model

Traceability is designed as a cross-cutting function. Important events and decisions are recorded in a local evidence register.

```mermaid
flowchart LR
  Event[Security event]
  Normalize[Normalize]
  Fingerprint[Generate fingerprint]
  Chain[Link to previous record]
  Sign[Sign record]
  Store[Store locally]
  Verify[Verify later]

  Event --> Normalize --> Fingerprint --> Chain --> Sign --> Store --> Verify
```

The register helps answer operational and audit questions:

- what happened;
- which asset was involved;
- which decision was taken;
- who validated the response;
- when the action was applied;
- whether the evidence remained coherent after storage.

This public description does not disclose private keys, exact record formats, production logs, or internal signing material.

## 8. Assistant architecture

The assistant is a decision-support component. It helps administrators understand information faster, but it does not replace human validation.

```mermaid
flowchart LR
  Question[Administrator question]
  Documentation[Local documentation]
  Events[Security events]
  Index[Knowledge index]
  Context[Retrieved context]
  Model[Language model]
  Answer[Answer with sources]
  Human[Human validation]

  Documentation --> Index
  Events --> Index
  Question --> Index --> Context --> Model --> Answer --> Human
```

The assistant can be used to:

- explain an alert in plain language;
- summarize an incident report;
- connect an event to available documentation;
- propose checks to perform;
- help prepare a response that still requires validation.

Guardrails:

- no sensitive action should be executed without validation;
- responses should be grounded in available context;
- private operational data should remain inside the controlled environment;
- the assistant should support the operator, not become the decision maker.

## 9. Resilience model

Ova Security includes a resilience model so that essential functions remain understandable during degraded situations.

```mermaid
stateDiagram-v2
  [*] --> Normal
  Normal --> Degraded: component anomaly
  Degraded --> Recovery: recovery procedure
  Recovery --> Normal: services restored
  Degraded --> Safe: critical condition
  Safe --> Recovery: operator validation
```

| Mode | Meaning |
| --- | --- |
| Normal | Core services are available and the appliance operates as expected. |
| Degraded | A non-critical function is affected, but essential monitoring continues. |
| Recovery | The system attempts to restore service health and records the recovery process. |
| Safe | The appliance limits behavior to essential defensive functions and requests operator attention. |

## 10. Validation approach

The architecture can be validated through controlled defensive scenarios. The public repository only describes the validation method at a high level.

```mermaid
flowchart TB
  Baseline[Baseline environment]
  Protected[Environment with appliance]
  Observe[Observe activity]
  Detect[Detect suspicious behavior]
  Prioritize[Prioritize risk]
  Decide[Validate response]
  Record[Record evidence]
  Review[Review result]

  Baseline --> Observe
  Protected --> Observe
  Observe --> Detect --> Prioritize --> Decide --> Record --> Review
```

Validation criteria:

- assets are discovered and classified;
- suspicious activity is detected;
- alerts are associated with the relevant device;
- risks are understandable for the administrator;
- defensive responses are controlled;
- evidence is preserved for later review;
- degraded states remain visible.

This repository does not provide attack commands, exploit steps, operational firewall rules, real addresses, or private screenshots.

## 11. Security and publication boundaries

This repository is safe for public presentation because it intentionally excludes:

- appliance source code;
- production configuration;
- secrets, tokens, keys, credentials, and certificates;
- private network addresses and access endpoints;
- raw logs, packet captures, or customer data;
- internal prompts, private datasets, or model credentials;
- offensive procedures or reproducible attack instructions.

## 12. Summary

Ova Security is designed as a local cybersecurity appliance that combines visibility, detection, controlled filtering, traceability, resilience, and decision support.

Its main architectural value is the centralization of defensive information in a format that remains understandable for administrators and usable during audits, while keeping sensitive operational details private.

## License

This repository is proprietary documentation. See [LICENSE](LICENSE).
