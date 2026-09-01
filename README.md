# Ova Security

Ova Security is a compact cybersecurity appliance architecture for local networks. It is designed to centralize visibility, detection, controlled filtering, vulnerability prioritization, traceability, resilience, and assisted decision-making inside a single defensive platform.

This public repository contains only the architectural presentation of the project. It does not include source code, credentials, production configuration, private network addresses, exploit procedures, customer data, or internal operational secrets.

## Table of contents

- [1. Vision](#1-vision)
- [2. Architecture overview](#2-architecture-overview)
- [3. Wired network architecture](#3-wired-network-architecture)
- [4. Internal logical architecture](#4-internal-logical-architecture)
- [5. Functional modules](#5-functional-modules)
- [6. Technology role map](#6-technology-role-map)
- [7. Data domains](#7-data-domains)
- [8. Event and decision flow](#8-event-and-decision-flow)
- [9. Log management and traceability](#9-log-management-and-traceability)
- [10. Integrity register](#10-integrity-register)
- [11. Assistant architecture](#11-assistant-architecture)
- [12. Resilience model](#12-resilience-model)
- [13. Administration and governance](#13-administration-and-governance)
- [14. Validation approach](#14-validation-approach)
- [15. Public boundaries](#15-public-boundaries)
- [16. License](#16-license)

## 1. Vision

Small and medium networks often sit between two extremes: minimal protection on one side, and complex enterprise security platforms on the other. Ova Security targets this middle ground with a local appliance that is easier to understand, deploy, operate, and audit.

The product idea is simple:

- place a dedicated appliance near the network edge;
- observe assets, flows, alerts, and service health;
- convert raw technical signals into understandable security context;
- let the administrator validate sensitive defensive decisions;
- preserve evidence locally for later verification.

The appliance follows a defensive and controlled posture. It is not presented as an offensive platform, an autonomous decision-maker, or a cloud-dependent monitoring system.

## 2. Architecture overview

```mermaid
flowchart LR
  Internet[Internet / upstream network]
  Router[Router]
  Appliance[Ova Security appliance]
  LAN[Protected LAN]
  Endpoints[Workstations, servers, IoT]
  Admin[Administration workstation]

  Internet --> Router --> Appliance --> LAN --> Endpoints
  Admin --> Appliance

  classDef wan fill:#fff4df,stroke:#d99000,stroke-width:2px,color:#392400;
  classDef appliance fill:#071c33,stroke:#42c6d1,stroke-width:2px,color:#ffffff;
  classDef lan fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;
  classDef admin fill:#edf5ff,stroke:#2f6fb2,stroke-width:2px,color:#06284d;

  class Internet,Router wan;
  class Appliance appliance;
  class LAN,Endpoints lan;
  class Admin admin;
```

Ova Security is positioned as a local edge appliance. Its main role is to become the security coordination point between the upstream network, the protected network, and the administrator.

At a high level, the appliance provides:

- a controlled network position;
- security event collection;
- asset and traffic visibility;
- alert analysis;
- vulnerability insight;
- evidence preservation;
- administration and reporting.

## 3. Wired network architecture

The appliance is designed around a wired segmentation model. The exact interface names and addresses are intentionally not published, but the logical separation remains clear.

```mermaid
flowchart TB
  subgraph WAN["WAN / upstream side"]
    ISP[Internet access]
    R[Upstream router]
  end

  subgraph BOX["Ova Security appliance"]
    WANI[WAN interface]
    LANI[LAN interface]
    MGMTI[Management interface]
    Firewall[Defensive filtering]
    Sensors[Detection sensors]
    Control[Policy controller]
  end

  subgraph MGMT["Management zone"]
    Admin[Authorized administrator]
  end

  subgraph LAN["Protected network"]
    Switch[LAN switch]
    Clients[Client devices]
    Servers[Local servers]
    IoT[IoT / embedded devices]
  end

  ISP --> R --> WANI
  WANI --> Firewall --> LANI --> Switch
  Switch --> Clients
  Switch --> Servers
  Switch --> IoT
  MGMTI --> Admin
  Sensors --> Control
  Control --> Firewall

  classDef wan fill:#fff4df,stroke:#d99000,stroke-width:2px,color:#392400;
  classDef appliance fill:#071c33,stroke:#42c6d1,stroke-width:2px,color:#ffffff;
  classDef security fill:#e6fbff,stroke:#0097a7,stroke-width:2px,color:#053940;
  classDef lan fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;
  classDef mgmt fill:#edf5ff,stroke:#2f6fb2,stroke-width:2px,color:#06284d;

  class ISP,R wan;
  class WANI,LANI,MGMTI appliance;
  class Firewall,Sensors,Control security;
  class Switch,Clients,Servers,IoT lan;
  class Admin mgmt;
```

### Security zones

| Zone | Purpose |
| --- | --- |
| WAN side | Receives upstream connectivity and untrusted traffic. |
| Protected LAN | Contains the internal devices supervised by the appliance. |
| Management zone | Reserved for authorized administration and maintenance. |
| Appliance core | Centralizes filtering, detection, analysis, evidence, and reporting. |

### Default posture

- Outgoing traffic can be controlled according to policy.
- Incoming access to protected assets is restricted by default.
- Management access is isolated from ordinary user traffic.
- Sensitive rules are tied to validation and traceability.

## 4. Internal logical architecture

```mermaid
flowchart TB
  subgraph Sources["Data sources"]
    Network[Network flows]
    Alerts[Detection alerts]
    Vulns[Vulnerability results]
    Inventory[Asset discovery]
    System[System health]
    AdminActions[Administrator actions]
  end

  subgraph Core["Appliance core"]
    Collector[Collection service]
    Normalizer[Normalization layer]
    Correlator[Correlation engine]
    Risk[Risk scoring]
    Policy[Policy validation]
    Ledger[Signed evidence register]
    Knowledge[Local knowledge base]
  end

  subgraph Interfaces["Operator interfaces"]
    Console[Administration console]
    Reports[Reports]
    Assistant[Assistant]
  end

  Network --> Collector
  Alerts --> Collector
  Vulns --> Collector
  Inventory --> Collector
  System --> Collector
  AdminActions --> Collector
  Collector --> Normalizer --> Correlator --> Risk
  Risk --> Console
  Policy --> Console
  Correlator --> Ledger
  Risk --> Ledger
  Policy --> Ledger
  Knowledge --> Assistant
  Ledger --> Reports
  Console --> Reports
  Assistant --> Console

  classDef source fill:#f6f8fa,stroke:#8aa0b2,stroke-width:1.5px,color:#1d2b36;
  classDef core fill:#e6fbff,stroke:#0097a7,stroke-width:2px,color:#053940;
  classDef risk fill:#f1ecff,stroke:#7c4dcc,stroke-width:2px,color:#2b174f;
  classDef evidence fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;
  classDef ui fill:#edf5ff,stroke:#2f6fb2,stroke-width:2px,color:#06284d;

  class Network,Alerts,Vulns,Inventory,System,AdminActions source;
  class Collector,Normalizer,Correlator,Knowledge core;
  class Risk,Policy risk;
  class Ledger evidence;
  class Console,Reports,Assistant ui;
```

The internal architecture is separated into data sources, appliance core services, and operator-facing interfaces. This separation keeps the solution maintainable and makes it easier to evolve one function without rewriting the whole system.

## 5. Functional modules

| Module | Responsibility | Public implementation idea |
| --- | --- | --- |
| Asset inventory | Discover and classify devices visible from the supervised network. | Active and passive observations are consolidated into an asset list. |
| Traffic observation | Understand who communicates with whom and when. | Flow metadata is collected and summarized for supervision. |
| Intrusion detection | Identify suspicious activity patterns. | Security alerts are normalized and attached to assets. |
| Vulnerability analysis | Detect exposed services and prioritize weaknesses. | Results are mapped to assets and severity levels. |
| Defensive filtering | Reduce exposure and block unauthorized access patterns. | Rules are controlled and linked to administrator validation. |
| Administration console | Give the operator a readable control surface. | Assets, alerts, risks, services, and reports are grouped by operational need. |
| Evidence register | Preserve important events and decisions. | Records are chained, signed, and kept locally. |
| Assistant | Explain alerts, reports, and documentation. | Retrieval-augmented context supports human decision-making. |
| Resilience monitor | Track service health and degraded modes. | Essential functions remain visible when one component is unhealthy. |
| Reporting | Produce useful summaries for operation and audit. | Executive, asset, incident, and integrity-oriented reports can be generated. |

## 6. Technology role map

The architecture can integrate several open-source components. This repository describes their role at a public level and does not publish internal configuration.

```mermaid
flowchart TB
  subgraph Platform["Appliance platform"]
    Hardware[Compact edge hardware]
    OS[Linux server OS]
  end

  subgraph Security["Security services"]
    Firewall[Firewalld / nftables]
    IDS[Suricata]
    NetworkAnalysis[Zeek]
    Vuln[Greenbone / OpenVAS]
  end

  subgraph Application["Application services"]
    API[Backend API]
    DB[(Relational database)]
    Cache[(Cache / queue)]
    UI[Administration UI]
  end

  subgraph Intelligence["Assistant layer"]
    RAG[RAG pipeline]
    Vector[(Vector index)]
    LLM[Local or controlled LLM]
  end

  Hardware --> OS --> Firewall
  OS --> IDS
  OS --> NetworkAnalysis
  OS --> Vuln
  IDS --> API
  NetworkAnalysis --> API
  Vuln --> API
  API --> DB
  API --> Cache
  API --> UI
  API --> RAG
  RAG --> Vector --> LLM --> UI

  classDef platform fill:#edf5ff,stroke:#2f6fb2,stroke-width:2px,color:#06284d;
  classDef security fill:#e6fbff,stroke:#0097a7,stroke-width:2px,color:#053940;
  classDef app fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;
  classDef ai fill:#f1ecff,stroke:#7c4dcc,stroke-width:2px,color:#2b174f;

  class Hardware,OS platform;
  class Firewall,IDS,NetworkAnalysis,Vuln security;
  class API,DB,Cache,UI app;
  class RAG,Vector,LLM ai;
```

| Technology family | Architectural role |
| --- | --- |
| Compact edge hardware | Hosts the appliance close to the protected network. |
| Linux server OS | Provides system services, networking, scheduling, and service supervision. |
| Firewalld / nftables | Provides defensive filtering and policy enforcement. |
| Suricata | Produces intrusion detection alerts from observed traffic. |
| Zeek | Produces network telemetry and protocol-level visibility. |
| Greenbone / OpenVAS | Supports vulnerability analysis and remediation prioritization. |
| Web administration layer | Exposes controlled supervision, reports, and operator actions. |
| Database layer | Stores assets, alerts, reports, state, and evidence metadata. |
| Cache / queue layer | Helps decouple collection, processing, and interface updates. |
| RAG and LLM layer | Supports contextual explanation and decision assistance. |

## 7. Data domains

Ova Security separates operational data into domains. This makes the system easier to audit and avoids mixing raw security signals with decisions or reports.

```mermaid
flowchart LR
  Assets[(Assets)]
  Flows[(Network flows)]
  Alerts[(Alerts)]
  Vulns[(Vulnerabilities)]
  Actions[(Validated actions)]
  Evidence[(Evidence register)]
  Reports[(Reports)]
  Knowledge[(Knowledge base)]

  Assets --> Alerts
  Flows --> Alerts
  Vulns --> Reports
  Alerts --> Actions
  Actions --> Evidence
  Alerts --> Evidence
  Evidence --> Reports
  Knowledge --> Reports

  classDef data fill:#f6f8fa,stroke:#8aa0b2,stroke-width:1.5px,color:#1d2b36;
  classDef sensitive fill:#fff4df,stroke:#d99000,stroke-width:2px,color:#392400;
  classDef evidence fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;
  classDef knowledge fill:#f1ecff,stroke:#7c4dcc,stroke-width:2px,color:#2b174f;

  class Assets,Flows,Alerts,Vulns,Reports data;
  class Actions sensitive;
  class Evidence evidence;
  class Knowledge knowledge;
```

| Domain | Description | Retention intent |
| --- | --- | --- |
| Assets | Devices, status, classification, and risk context. | Kept while the device is part of the supervised environment. |
| Flows | Metadata describing communications and service activity. | Rotated and summarized to avoid uncontrolled growth. |
| Alerts | Detection events and contextual security findings. | Preserved according to operational relevance. |
| Vulnerabilities | Weaknesses mapped to assets and services. | Refreshed after scans and remediation cycles. |
| Actions | Human-approved decisions and administrative changes. | Preserved as governance evidence. |
| Evidence | Integrity-oriented records for sensitive events. | Protected from uncontrolled deletion. |
| Reports | Human-readable summaries for operations and audits. | Generated on demand or scheduled. |
| Knowledge base | Documentation used by the assistant. | Updated independently from the language model. |

## 8. Event and decision flow

```mermaid
sequenceDiagram
  participant Asset as Network asset
  participant Sensor as Observation sensors
  participant Core as Appliance core
  participant Risk as Risk engine
  participant Admin as Administrator
  participant Filter as Filtering layer
  participant Ledger as Evidence register

  Asset->>Sensor: Network activity
  Sensor->>Core: Event and context
  Core->>Risk: Normalize and correlate
  Risk->>Admin: Alert with priority and explanation
  Admin->>Core: Validate defensive decision
  Core->>Filter: Apply approved rule
  Core->>Ledger: Store event, decision, and result
```

The flow is built to avoid blind automation. Detection and analysis can be automated, but sensitive response remains controlled by a human decision.

## 9. Log management and traceability

Ova Security treats logs as operational evidence, not as a storage problem. The log pipeline is designed to reduce volume while preserving traceability.

```mermaid
flowchart LR
  Sources[Log sources]
  Collect[Automatic collection]
  Rotate[Rotation]
  Compress[Compression]
  Retain[Retention policy]
  Clean[Controlled cleanup]
  Register[Signed register]
  Review[Audit review]

  Sources --> Collect --> Rotate --> Compress --> Retain --> Clean
  Collect --> Register
  Rotate --> Register
  Compress --> Register
  Clean --> Register
  Register --> Review

  classDef source fill:#f6f8fa,stroke:#8aa0b2,stroke-width:1.5px,color:#1d2b36;
  classDef process fill:#e6fbff,stroke:#0097a7,stroke-width:2px,color:#053940;
  classDef storage fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;
  classDef audit fill:#fff4df,stroke:#d99000,stroke-width:2px,color:#392400;

  class Sources source;
  class Collect,Rotate,Compress,Retain,Clean process;
  class Register storage;
  class Review audit;
```

### Log pipeline objectives

- Collect useful events automatically.
- Reduce file growth through rotation and compression.
- Keep retention predictable.
- Avoid deleting evidence without control.
- Preserve a verifiable trail of important actions.

## 10. Integrity register

The register is designed to make sensitive events verifiable after they are stored. It links each important event to integrity metadata and preserves a chain between records.

```mermaid
flowchart TB
  Event[Event detected]
  Hash[Event fingerprint]
  Previous[Previous record hash]
  Chain[Chained record]
  Signature[Digital signature]
  Store[Local register]
  Verify[Verification]
  Valid[Valid evidence]
  Invalid[Integrity alert]

  Event --> Hash --> Chain
  Previous --> Chain
  Chain --> Signature --> Store --> Verify
  Verify --> Valid
  Verify --> Invalid

  classDef event fill:#edf5ff,stroke:#2f6fb2,stroke-width:2px,color:#06284d;
  classDef crypto fill:#f1ecff,stroke:#7c4dcc,stroke-width:2px,color:#2b174f;
  classDef store fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;
  classDef danger fill:#ffecec,stroke:#d94a4a,stroke-width:2px,color:#5a1010;

  class Event event;
  class Hash,Previous,Chain,Signature,Verify crypto;
  class Store,Valid store;
  class Invalid danger;
```

The objective is not to publish a blockchain system. The objective is to keep a local, verifiable, tamper-evident register for security-relevant actions.

### Examples of records

- Detection alert accepted by the operator.
- Filtering decision validated by an administrator.
- Configuration change.
- Service degradation or recovery event.
- Vulnerability report generation.
- Integrity verification result.

## 11. Assistant architecture

The assistant is used to make security information understandable. It can interpret alerts, reports, screenshots, and documentation, but it remains a support layer.

```mermaid
flowchart LR
  User[Administrator question]
  Docs[Local documentation]
  Logs[Security events]
  Reports[Generated reports]
  Embed[Embedding]
  Index[Vector index]
  Retrieve[Relevant context]
  LLM[Language model]
  Answer[Answer with sources]
  Human[Human validation]

  Docs --> Embed --> Index
  Logs --> Embed
  Reports --> Embed
  User --> Retrieve
  Index --> Retrieve --> LLM --> Answer --> Human

  classDef input fill:#f6f8fa,stroke:#8aa0b2,stroke-width:1.5px,color:#1d2b36;
  classDef ai fill:#f1ecff,stroke:#7c4dcc,stroke-width:2px,color:#2b174f;
  classDef output fill:#e6fbff,stroke:#0097a7,stroke-width:2px,color:#053940;
  classDef human fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;

  class User,Docs,Logs,Reports input;
  class Embed,Index,Retrieve,LLM ai;
  class Answer output;
  class Human human;
```

### Assistant guardrails

- It explains and recommends; it does not replace the administrator.
- It should cite the context used for its answer.
- It should avoid actions that are not validated.
- It should not require publishing private operational data.
- It should remain aligned with the defensive scope of the appliance.

## 12. Resilience model

The appliance must remain understandable even when a component is degraded. The resilience model tracks service health and separates normal operation from degraded and safe states.

```mermaid
stateDiagram-v2
  [*] --> INIT
  INIT --> NORMAL: core checks passed
  NORMAL --> DEGRADED: non-critical component issue
  NORMAL --> SAFE: critical control issue
  DEGRADED --> RECOVERY: recovery procedure started
  RECOVERY --> NORMAL: service restored
  RECOVERY --> DEGRADED: partial recovery
  SAFE --> RECOVERY: operator validation

  classDef normal fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;
  classDef warning fill:#fff4df,stroke:#d99000,stroke-width:2px,color:#392400;
  classDef danger fill:#ffecec,stroke:#d94a4a,stroke-width:2px,color:#5a1010;
  classDef init fill:#edf5ff,stroke:#2f6fb2,stroke-width:2px,color:#06284d;

  class INIT init;
  class NORMAL normal;
  class DEGRADED,RECOVERY warning;
  class SAFE danger;
```

| Mode | Meaning | Expected behavior |
| --- | --- | --- |
| INIT | Appliance startup and service checks. | Verify core services before declaring the appliance ready. |
| NORMAL | Main services are available. | Full supervision, alerting, reporting, and evidence recording. |
| DEGRADED | One non-critical function is impaired. | Keep essential monitoring and show the affected function clearly. |
| RECOVERY | The appliance attempts restoration. | Record recovery steps and notify the operator. |
| SAFE | A critical condition requires caution. | Limit behavior to essential defensive functions and require attention. |

## 13. Administration and governance

Administration is intentionally separated from ordinary network use. The architecture is designed around clear roles, controlled actions, and traceable decisions.

```mermaid
flowchart TB
  Operator[Administrator]
  Console[Management console]
  RBAC[Role and permission checks]
  Decision[Decision validation]
  Action[Defensive action]
  Evidence[Evidence register]
  Report[Audit report]

  Operator --> Console --> RBAC --> Decision --> Action
  Decision --> Evidence
  Action --> Evidence
  Evidence --> Report

  classDef human fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;
  classDef ui fill:#edf5ff,stroke:#2f6fb2,stroke-width:2px,color:#06284d;
  classDef control fill:#e6fbff,stroke:#0097a7,stroke-width:2px,color:#053940;
  classDef evidence fill:#fff4df,stroke:#d99000,stroke-width:2px,color:#392400;

  class Operator human;
  class Console ui;
  class RBAC,Decision,Action control;
  class Evidence,Report evidence;
```

### Governance rules

- Administrators must be identifiable.
- Sensitive actions must be validated.
- Changes should be traceable.
- Evidence should remain local and verifiable.
- The assistant must not execute uncontrolled decisions.
- Reports should be understandable by technical and non-technical reviewers.

## 14. Validation approach

The architecture can be validated by comparing a baseline environment with an environment protected and supervised by the appliance.

```mermaid
flowchart TB
  Baseline[Baseline environment]
  Protected[Protected environment]
  Observe[Observe network behavior]
  Detect[Detect suspicious activity]
  Link[Link event to asset]
  Score[Assess risk]
  Decide[Validate defensive response]
  Enforce[Apply controlled filtering]
  Prove[Record evidence]
  Review[Review result]

  Baseline --> Observe
  Protected --> Observe
  Observe --> Detect --> Link --> Score --> Decide --> Enforce --> Prove --> Review

  classDef baseline fill:#f6f8fa,stroke:#8aa0b2,stroke-width:1.5px,color:#1d2b36;
  classDef analysis fill:#e6fbff,stroke:#0097a7,stroke-width:2px,color:#053940;
  classDef decision fill:#f1ecff,stroke:#7c4dcc,stroke-width:2px,color:#2b174f;
  classDef security fill:#e9f8ef,stroke:#2e9e5b,stroke-width:2px,color:#073b24;

  class Baseline,Protected baseline;
  class Observe,Detect,Link,Score analysis;
  class Decide decision;
  class Enforce,Prove,Review security;
```

### Validation criteria

- Devices are discovered and classified.
- Suspicious activity is detected.
- Alerts are linked to assets.
- Risk indicators are understandable.
- Defensive responses remain controlled.
- Evidence is preserved for later review.
- Degraded states are visible.
- Reports remain useful for operational review.

## 15. Public boundaries

This repository is designed for public presentation. It intentionally excludes:

- source code;
- exact production configuration;
- firewall rule exports;
- private prompts and datasets;
- keys, credentials, tokens, certificates, and secrets;
- private IP addresses and operational endpoints;
- raw logs, packet captures, and customer data;
- screenshots containing private information;
- offensive commands or reproducible attack instructions.

## 16. License

This repository is proprietary documentation. The public availability of the repository does not grant the right to copy, reuse, resell, mirror, or derive a competing template or product from this material.

See [LICENSE](LICENSE).
