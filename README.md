# Ova Security

Public architecture documentation for **Ova Security**, a compact wired cybersecurity appliance designed to observe, filter, audit, and preserve local evidence for small and medium networks.

This repository presents the architecture at a public level. It does not contain secrets, internal credentials, production configuration, private deployment procedures, or customer data.

## Contents

- [Product overview](#product-overview)
- [Edge network position](#edge-network-position)
- [Internal architecture](#internal-architecture)
- [Functional modules](#functional-modules)
- [Technology role map](#technology-role-map)
- [How the system works](#how-the-system-works)
- [Technology details](#technology-details)
- [Data and evidence model](#data-and-evidence-model)
- [Log integrity and auditability](#log-integrity-and-auditability)
- [Assistant and knowledge layer](#assistant-and-knowledge-layer)
- [Resilience mode](#resilience-mode)
- [Operational lifecycle](#operational-lifecycle)
- [Security boundaries](#security-boundaries)
- [License](#license)

## Product overview

Ova Security is positioned as an **edge cyber appliance**: a local box installed between the internet access point and the protected internal network. Its objective is to bring together monitoring, filtering, vulnerability visibility, audit evidence, and operator assistance in one controlled platform.

![Ova Security system architecture](assets/report-diagrams/framed/system-architecture.png)

The product is built around four principles:

| Principle | Meaning |
| --- | --- |
| Local control | Security decisions, logs, and evidence remain under the operator's control. |
| Wired-first deployment | The appliance is placed on a physical network path to separate internet, protected LAN, and administration access. |
| Explainable operations | Alerts, blocked flows, scans, and administrative actions are presented with traceable context. |
| Evidence preservation | Important events are recorded in an integrity-oriented register so later changes can be detected. |

## Edge network position

Ova Security is designed to sit at the edge of the protected network. It receives traffic from the upstream router, applies filtering and inspection, then exposes only validated flows toward internal equipment.

![Ova Security edge network zones](assets/report-diagrams/framed/general-architecture.png)

### Network zones

| Zone | Role |
| --- | --- |
| WAN / upstream | External connectivity coming from the router or internet access equipment. |
| Appliance edge | Filtering, monitoring, logging, vulnerability visibility, and evidence generation. |
| Protected LAN | Workstations, servers, IoT devices, and internal services. |
| Administration | Restricted management access for operators and maintainers. |

## Internal architecture

The platform is structured in layers. Each layer has a clear responsibility so the solution can remain understandable, auditable, and maintainable.

![Ova Security internal architecture](assets/report-diagrams/framed/system-architecture.png)

### Layer responsibilities

| Layer | Responsibility |
| --- | --- |
| Hardware and OS | Hosts the appliance runtime, network interfaces, local storage, and system services. |
| Network security services | Filters traffic, observes sessions, detects suspicious behavior, and maps exposed services. |
| Application services | Converts raw technical events into dashboard data, reports, audit records, and operator workflows. |
| Evidence and assistant layer | Preserves important actions, validates integrity, and helps the operator understand incidents. |

## Functional modules

Ova Security is presented as a product, not as a collection of disconnected tools. The modules below form one operational chain.

![Ova Security use case architecture](assets/report-diagrams/framed/use-case-architecture.png)

| Module | Purpose | Typical output |
| --- | --- | --- |
| Firewall control | Blocks unauthorized inbound or lateral traffic according to validated rules. | Allowed/refused flows, rule status, blocked sources. |
| Intrusion detection | Observes network traffic and detects suspicious patterns. | Alerts, severity, affected host, detection context. |
| Network mapping | Identifies visible devices, services, and communication patterns. | Asset inventory, exposed ports, topology hints. |
| Vulnerability visibility | Associates services and hosts with known weaknesses. | Prioritized findings and remediation guidance. |
| Log management | Collects, rotates, compresses, and retains operational logs. | Searchable event history and controlled storage usage. |
| Integrity register | Chains and signs important events to detect later alteration. | Verifiable audit trail. |
| SOC assistant | Explains alerts, reports, logs, and procedures in operator language. | Summaries, diagnostic steps, recommended actions. |

## Technology role map

The implementation can rely on well-known open-source components while keeping the product value in integration, orchestration, presentation, and evidence handling.

![Ova Security technology role map](assets/report-diagrams/framed/technology-map.png)

| Component family | Public role |
| --- | --- |
| Linux services | Base operating environment, service supervision, scheduled tasks, and local process control. |
| Firewall engine | Packet filtering, rule application, and controlled network segmentation. |
| IDS / network analysis | Traffic observation, protocol analysis, alert generation, and session metadata. |
| Vulnerability scanner | Controlled discovery and vulnerability assessment of selected assets. |
| Web application | Dashboard, reports, search, administration, and operator workflows. |
| Local evidence store | Stores signed records, log metadata, configuration changes, and audit history. |
| Assistant layer | Uses project documentation and local events to answer operational questions. |

## How the system works

Ova Security works as a controlled chain. Each component produces data for the next one, and sensitive actions are kept visible to the operator.

1. **Traffic enters the appliance** from the upstream network interface.
2. **The firewall layer applies the active policy** and separates unauthorized traffic from allowed traffic.
3. **Detection and traffic analysis services observe events** such as suspicious flows, protocol activity, service exposure, and abnormal behavior.
4. **The backend normalizes events** into a common structure: source, destination, service, severity, timestamp, affected asset, and action status.
5. **The risk layer correlates events** with assets, vulnerabilities, previous alerts, and operational context.
6. **The dashboard presents the situation** through equipment status, alerts, service health, reports, and audit views.
7. **The operator validates sensitive changes**, such as blocking decisions, exceptions, remediation status, or administrative actions.
8. **The evidence layer records important actions** with hashing, chaining, signing, and verification.
9. **The assistant helps explain the context** using indexed documentation, reports, logs, and known procedures, while final decisions remain human-controlled.

This creates a defensive loop: **observe -> correlate -> decide -> enforce -> prove**.

## Technology details

The technologies below are described by role, not by private configuration. The repository explains how they cooperate inside the architecture without publishing deployable secrets or internal rules.

| Technology / block | Role in Ova Security | Input | Output | Integration logic |
| --- | --- | --- | --- | --- |
| Linux appliance runtime | Hosts the complete local platform and supervises services. | Boot process, network interfaces, service definitions. | Running security and application services. | Provides the stable base for scheduled jobs, local storage, permissions, and service recovery. |
| Firewall engine | Enforces the network security policy. | Validated rules, blocked IP decisions, interface zones. | Allowed traffic, refused traffic, rule state. | Receives controlled decisions from the backend and applies them at the network edge. |
| Intrusion detection service | Detects suspicious network behavior. | Mirrored or routed traffic metadata. | Alerts with severity, signature, source, destination, and protocol context. | Feeds the event pipeline and gives the dashboard actionable incident data. |
| Traffic analysis service | Builds visibility on conversations and services. | Network sessions, DNS activity, protocol metadata. | Flow history, service map, communication context. | Helps distinguish normal activity from suspicious activity and supports asset mapping. |
| Vulnerability scanner | Identifies exposed services and known weaknesses on selected assets. | Asset inventory, selected targets, scan policy. | Findings, severity, affected service, remediation status. | Links vulnerability information to the dashboard and risk prioritization layer. |
| Backend API | Coordinates data collection, business logic, and dashboard access. | Alerts, logs, assets, scanner results, operator actions. | Normalized records, risk scores, reports, commands. | Acts as the orchestration layer between sensors, storage, evidence, UI, and assistant. |
| Risk and correlation engine | Converts raw findings into prioritized operational context. | Alerts, asset data, vulnerability data, service status. | Incident priority, affected scope, recommended next step. | Reduces noise by connecting events to the real network context. |
| Web dashboard | Gives operators one place to understand and control the appliance. | API data, reports, health metrics, audit status. | Visual supervision, validated actions, exports. | Presents the system as one product instead of separate technical tools. |
| Log lifecycle manager | Keeps logs useful without saturating local storage. | Raw logs, generated events, retention policy. | Rotated archives, compressed history, searchable active data. | Maintains operational visibility while controlling disk usage. |
| Signed ledger | Preserves proof of sensitive events. | Important events, hashes, previous record reference, signature material. | Tamper-evident audit chain. | Makes later alteration detectable by checking hash continuity and signature validity. |
| Assistant / RAG layer | Helps operators understand incidents and procedures. | User questions, indexed documentation, selected events, reports. | Explanation, summary, diagnostic checklist, suggested response. | Retrieves relevant local context before generating an answer, then leaves critical validation to the operator. |

### Technology diagrams

Each technology block has a focused diagram showing how it receives input, what it does inside the appliance, and what it returns to the rest of the system.

#### Linux appliance runtime

![Linux appliance runtime](assets/diagrams/tech-01-platform-runtime.svg)

The runtime layer starts the appliance services, supervises process state, exposes network interfaces, manages local storage permissions, and runs scheduled collection tasks. It is the base that keeps the product stable even when one service must restart or recover.

#### Firewall engine

![Firewall engine](assets/report-diagrams/framed/firewall-architecture.png)

The firewall engine applies the validated security policy at the network edge. It receives interface zones, operator-approved rules, and block decisions from the backend, then returns rule state, allowed/refused flow information, and auditable change events.

#### Intrusion detection and traffic analysis

![Suricata intrusion detection architecture](assets/report-diagrams/framed/suricata-architecture.png)

![Zeek traffic analysis architecture](assets/report-diagrams/framed/zeek-architecture.png)

Detection and traffic analysis transform network activity into alerts, protocol metadata, flow history, and investigation context. This layer feeds the correlation engine so the dashboard can show what happened, where it happened, and which asset is affected.

#### Vulnerability visibility

![Greenbone vulnerability visibility architecture](assets/report-diagrams/framed/greenbone-architecture.png)

The vulnerability layer connects known assets and detected services with security findings. Its role is not only to list weaknesses, but to prioritize them according to exposure, affected service, and operational importance.

#### Backend API and dashboard

![Backend API and dashboard](assets/diagrams/tech-05-backend-dashboard.svg)

The backend API is the coordination point of the product. It normalizes data, stores structured records, prepares dashboard views, manages operator workflows, and sends validated actions toward security services.

#### Signed ledger

![Signed ledger chain architecture](assets/report-diagrams/framed/ledger-chain-architecture.png)

![PKI signature architecture](assets/report-diagrams/framed/pki-signature-architecture.png)

The signed ledger receives sensitive events, creates a hash, links each record to the previous one, and stores a verifiable proof. If someone modifies an old record, the chain no longer validates.

#### Assistant and RAG layer

![Assistant and RAG layer](assets/report-diagrams/framed/llm-rag-architecture.png)

The assistant retrieves approved project context before producing an answer. It can explain alerts, summarize reports, and propose diagnostic steps, but it does not silently apply critical changes.

## Data and evidence model

The architecture separates raw logs, normalized events, operator actions, and signed evidence. This separation makes the platform easier to audit and safer to operate.

![Ova Security data and evidence model](assets/report-diagrams/framed/integrity-control.png)

### Main data families

| Data family | Examples | Treatment |
| --- | --- | --- |
| Network events | Connections, DNS activity, protocol metadata, IDS alerts. | Normalized, indexed, linked to assets. |
| Security decisions | Blocked traffic, accepted rules, validated exceptions. | Recorded with timestamp, source, and operator context. |
| Asset information | Hostnames, services, observed ports, device categories. | Updated over time and used to contextualize risks. |
| Vulnerability findings | CVE references, severity, affected service, remediation status. | Prioritized and connected to assets. |
| Audit records | Configuration changes, sensitive actions, register updates. | Hashed, chained, signed, and verifiable. |

## Log integrity and auditability

The log strategy has two goals: keep the system lightweight and preserve proof. The appliance should reduce disk usage without destroying the chain of evidence.

![Ova Security log integrity workflow](assets/report-diagrams/framed/ledger-chain-architecture.png)

### Integrity workflow

1. Security events and sensitive actions are normalized.
2. Each important record receives a cryptographic hash.
3. Records are chained with the previous hash.
4. The chain is signed by the appliance.
5. Verification checks the signature, hashes, and chain continuity.
6. Any later modification breaks the chain and raises an integrity alert.

### Log lifecycle

| Step | Goal |
| --- | --- |
| Collection | Gather events from network, security, system, and application services. |
| Rotation | Split active logs into controlled periods. |
| Compression | Reduce storage pressure without losing traceability. |
| Retention | Keep useful history according to operational policy. |
| Signed ledger | Preserve a tamper-evident trace of important events. |

## Assistant and knowledge layer

The assistant is not a replacement for the operator. It is a support layer that helps interpret alerts, explain logs, summarize reports, and propose diagnostic steps from approved project knowledge.

![Ova Security assistant and knowledge architecture](assets/report-diagrams/framed/llm-rag-architecture.png)

### Assistant capabilities

| Capability | Description |
| --- | --- |
| Natural questions | The operator asks questions in plain language. |
| Context retrieval | The assistant uses indexed documentation, reports, logs, and known procedures. |
| Alert explanation | Events are translated into understandable risk context. |
| Action guidance | The assistant proposes next steps, checks, and remediation paths. |
| Human validation | Sensitive actions remain under operator control. |

## Resilience mode

Ova Security includes a degraded-mode logic for situations where a service becomes unavailable, the system detects an anomaly, or the appliance must keep a minimal defensive posture while recovering.

![Ova Security resilience mode cycle](assets/report-diagrams/framed/resilience-cycle.png)

The resilience cycle follows six stages:

| Stage | Role |
| --- | --- |
| Monitoring | Monitor service health, network state, resource pressure, and appliance behavior. |
| Anomaly detection | Identify outage, overload, failed service, suspicious condition, or attack symptom. |
| Mode decision | Decide whether the appliance remains normal, switches to degraded mode, or enters a safe mode. |
| Minimal protection | Keep essential filtering and local protection active even when advanced services are degraded. |
| Recovery | Restart, resynchronize, or restore affected services in a controlled way. |
| Return to normal | Validate restored services and resume standard operation with updated evidence. |

## Operational lifecycle

The architecture supports a continuous cycle: observe, understand, decide, act, and prove.

![Ova Security operational lifecycle](assets/report-diagrams/framed/operational-sequence.png)

| Phase | Description |
| --- | --- |
| Observe | Collect network, service, system, and security signals. |
| Correlate | Link events to assets, services, rules, and previous activity. |
| Prioritize | Highlight the most important risks first. |
| Decide | Let the operator validate rules, exceptions, and remediation actions. |
| Enforce | Apply firewall or operational decisions in a controlled way. |
| Prove | Store a signed trace of sensitive changes and security decisions. |

## Security boundaries

This repository intentionally avoids publishing operational details that would weaken a real deployment.

Published:

- Product-level architecture
- Public module descriptions
- High-level data flows
- Public validation logic
- Documentation license

Not published:

- Secrets, keys, tokens, or credentials
- Real customer data
- Private network addresses
- Production firewall rules
- Internal deployment scripts
- Exploit procedures or offensive playbooks

## License

This documentation, diagrams, structure, and written content are protected by the license included in [LICENSE](LICENSE). Public visibility does not grant permission to copy, reuse, resell, rebrand, or redistribute the project content.
