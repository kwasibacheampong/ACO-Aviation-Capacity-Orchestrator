# ACO — Aviation Capacity Orchestrator

### Digital Twin + Predictive + Orchestration Engine for Airport Operations

[![Status](https://img.shields.io/badge/Status-PRD%20Complete-blue?style=flat-square)]()
[![MVP](https://img.shields.io/badge/MVP-90–120%20Days-orange?style=flat-square)]()
[![Throughput](https://img.shields.io/badge/Throughput%20Uplift-5–15%25-brightgreen?style=flat-square)]()
[![Built With](https://img.shields.io/badge/Intelligence-Claude%20API-FF6B35?style=flat-square)](https://anthropic.com)
[![By](https://img.shields.io/badge/Built%20by-Fruitful%20Bough-C9A84C?style=flat-square)](https://github.com/kwasibacheampong)

> **[Fruitful Bough](https://github.com/kwasibacheampong)** · Strategic architect of measurable value. Not advisory — structural. Not consultancy — engineering.

---

## The problem

Airports cannot expand easily. Regulatory constraints, ESG pressure, and planning timelines make physical expansion a 10–15 year commitment. Demand growth does not wait.

The bottleneck is not runway length — it is **operational capacity**: the ability to orchestrate airside and landside resources in real time, under constraints, at scale.

ACO (Aviation Capacity Orchestrator) converts airport operations into:

- **Forecastable capacity** — predictive models at T+15/30/60 minutes
- **Controllable constraints** — guardrail-governed recommendations with full audit trail
- **Measurable growth** — 5–15% throughput uplift without a single square metre of new infrastructure

**"Capacity orchestration is the new runway."**

---

## What this is

ACO is a **digital twin + predictive + orchestration engine** that enables airports to orchestrate airside and landside capacity in real time, under regulatory and ESG constraints.

The platform connects:

```
Airport Data Sources (AODB, FIDS, sensors, queues, weather)
                    ↓
         Event Backbone (Kafka, 5 topic domains)
                    ↓
      Stream Processing (Flink: normalise, enrich, derive state)
                    ↓
     Digital Twin Engine (Graph: nodes, edges, constraints)
                    ↓
        Predictive Layer (queue forecasts, conflict detection)
                    ↓
      Orchestration Engine (ranked actions + guardrails)
                    ↓
       Decision Support UI (Ops / Exec / Commercial / Regulatory)
```

Every recommendation carries a `decision_trace_id`, an explainability payload, and a guardrail check result. No black-box outputs.

---

## Users & personas

### Primary operators
| Persona | Role |
|---------|------|
| **AOCC** | Airport Operations Control Center — owns orchestration decisions |
| **Terminal Ops / Passenger Flow** | Zone crowding, terminal flow management |
| **Stand / Gate Management** | Conflict detection, turnaround coordination |
| **Security Ops + Border Force** | Queue forecasting, lane management |
| **Airline Station Managers** | Flight state, stand/gate allocation |
| **Ground Handling Ops** | Resource availability, GSE coordination |

### Secondary
| Persona | Role |
|---------|------|
| **Commercial / Non-aero** | Revenue-aware orchestration, dwell-time optimisation |
| **Regulatory & ESG Reporting** | Compliance-grade audit trail, constraint evidence |
| **Executive (COO / CIO)** | Strategic capacity outlook, KPI proof pack |

---

## Scope

### MVP — 90–120 days buildable

| Capability | Detail |
|-----------|--------|
| **Data Fabric ingestion** | AODB, FIDS, stand allocation, security queue sensors, weather/NOTAM |
| **Operational State Model** | 2D/graph twin (not full 3D) — nodes, edges, constraints |
| **Queue forecasting** | Security + Border wait time prediction at T+15/30/60 |
| **Gate/stand conflict detection** | Early warning ≥ 30 minutes ahead |
| **Decision Support UI** | Recommended actions with explainability, role-based views |
| **Audit trail + governance** | Immutable log — every event, model output, recommendation, override |

### V1
- Full airport operational twin (airside + landside)
- What-if simulation (rolling horizon)
- Orchestration playbooks (semi-automated actions)
- Revenue-aware orchestration (dwell-time + retail uplift)

### V2
- Multi-airport group orchestration
- "Capacity market" primitives — credits and optimisation marketplace
- Advanced 3D/WebGL twin + immersive OCC

---

## Architecture

ACO is a microservices platform built on an event-driven backbone. Full specification in [`/architecture`](./architecture/).

### Layers

| Layer | Technology | Role |
|-------|-----------|------|
| **Ingestion** | Connector microservices per source | AODB, FIDS, sensors → canonical ACO Event schema |
| **Event Backbone** | Kafka (5 topic domains) | `flight.events`, `passengerflow.events`, `security.events`, `resource.events`, `constraints.events` |
| **Stream Processing** | Apache Flink | Normalisation, enrichment, state derivation, feature generation, anomaly detection |
| **Storage** | Postgres/Redis (hot), Timescale (time series), Neo4j (graph), S3 (event store) | Operational state, topology, time series, event replay |
| **Digital Twin Engine** | Neo4j graph + Twin API | Airport as nodes + edges + constraints — event-driven updates |
| **Predictive Layer** | ML inference service (gRPC) + model registry | Queue forecasts, conflict detection, feature store |
| **Orchestration Engine** | Rules engine + recommendation ranking + playbook catalog | Guardrail-governed ranked actions, advisory → guided → automated |
| **Application Layer** | API Gateway + RBAC + Web UI | Ops console, executive view, audit service |

### Runtime
```
Kubernetes (EKS / AKS / GKE) + Helm charts
Kafka (managed or Strimzi)
Postgres / TimescaleDB
Neo4j or JanusGraph
S3-compatible object store
Vault / cloud-native secrets
CI/CD: GitHub Actions + ArgoCD
```

### Environments
`dev` / `staging` / `prod` — synthetic data generator for test environment.

---

## Functional requirements — acceptance criteria

| Requirement | Acceptance criteria |
|-------------|-------------------|
| **FR-01 Ingestion** | ≥ 5 event types · end-to-end event visibility ≤ 5 seconds for streaming sources |
| **FR-02 State Store** | Updates idempotent · full state rebuild possible from event log replay |
| **FR-03 Digital Twin** | Twin state query via API < 250ms p95 (hot paths) |
| **FR-04 Predictive** | Queue overflow prediction precision ≥ 0.80 · Gate conflict detection recall ≥ 0.90 |
| **FR-05 Recommendations** | Every decision returns `decision_trace_id` + `explainability_payload` |
| **FR-06 UI** | Role-based views: Ops / Exec / Commercial / Regulatory |
| **FR-07 Audit** | "Why did we do X at 09:03?" answerable in < 1 minute with trace |

---

## Non-functional requirements

| Category | Requirement |
|----------|------------|
| **Availability** | 99.9% (MVP) → 99.95% (V1) |
| **RPO / RTO** | RPO: 15 min · RTO: 60 min (MVP), tighter in V1 |
| **Security** | Zero Trust · RBAC · SOC2-ready logging |
| **Compliance** | GDPR — passenger data aggregated/anonymised by design |
| **Observability** | Full OpenTelemetry tracing: ingestion → processing → state → inference → recommendation → UI |

### SLO monitoring
- Ingest lag
- Event processing throughput
- State query p95/p99
- Model inference p95
- UI error rate

---

## Data model

Full schema in [`/data-model`](./data-model/). Key entities:

### Canonical event schema — `aco_event`
```
event_id          uuid, pk
event_time        timestamp
ingest_time       timestamp
event_type        enum
source_system     string
tenant_id         string
payload           jsonb
schema_version    string
correlation_id    string
```

### Core domain tables
| Table | Purpose |
|-------|---------|
| `flights` | Flight state, gate/stand assignment, turnaround times |
| `airport_nodes` | Gates, stands, zones, security, border, retail — with capacity and geometry |
| `airport_edges` | Walkways, airside paths, bus routes — with travel time and constraints |
| `queues` | Time series — current load, wait time estimate, T+15/30/60 forecasts |
| `resources` | Security lanes, staff, GSE — availability and capacity effect |
| `constraints` | Noise, slot, staffing, safety — scoped to airport/terminal/node/flight |
| `recommendations` | Ranked actions with expected impact, confidence, and `decision_trace_id` |
| `decision_traces` | Full explainability — input events, feature versions, model versions, guardrails checked, operator override |

### Mandatory lineage fields on all persisted artifacts
`tenant_id` · `source_system` · `schema_version` · `model_version` · `decision_trace_id`

---

## Operating model

ACO maps to a five-layer operating structure:

| Layer | ACO Equivalent | Output cadence |
|-------|---------------|----------------|
| **Capacity Owner** (AOCC Orchestration Lead) | Owns predictability, growth, failure modes, non-displaceability | Continuous |
| **Operational Intelligence Spine** | Constraint outlook, top 5 bottleneck risks, ecosystem signals | Daily |
| **Orchestration Execution Surface** | Advisory → Guided → Automated actions with decision trace | Real time |
| **Strategic Guardrails** | No black-box outputs · no automation without measured reliability · no trust fragmentation | Enforced per action |
| **Governance Cadence** | Weekly Risk & Readiness Note · Monthly model performance review · Quarterly narrative reset | Scheduled |

### Guardrails — non-negotiable
- No actions that fragment trust across stakeholders (airport vs airline vs security)
- No speculative capacity claims without audit evidence
- No black-box recommendations — explainability required on every output
- No automation without measured reliability thresholds and explicit sign-off
- No sacrifice of compliance to hit throughput

---

## Investment thesis

| Dimension | Detail |
|-----------|--------|
| **Category** | Capacity orchestration infrastructure for regulated mobility ecosystems |
| **Pain** | Airports cannot expand. Demand grows. The bottleneck is operational capacity. |
| **Why now** | Sensor + data maturity · better stream processing + simulation · ML explainability now feasible · ESG/regulatory pressure makes orchestration non-optional |
| **Moat** | Multi-stakeholder embedding · twin topology + playbook library · compliance-grade decision trace · model learning per airport archetype |
| **Buyers** | Tier 1 hubs (high ACV multi-year) · mid-tier airports (modular) · airport groups (portfolio orchestration) |
| **ROI story** | Throughput uplift + queue reduction + OTP lift + retail dwell uplift = "invisible expansion" — CFO-friendly |

---

## Repository structure

```
aviation-capacity-orchestrator/
├── README.md
├── architecture/
│   ├── reference-architecture.md     # End-to-end system architecture
│   ├── security-architecture.md      # RBAC, tenant isolation, WORM audit
│   └── observability.md              # OpenTelemetry traces, runbooks
├── data-model/
│   ├── canonical-event-schema.md     # aco_event schema
│   ├── core-domain-tables.md         # All 8 domain tables with field types
│   └── lineage-governance.md         # Mandatory lineage fields, replay discipline
├── operating-model/
│   ├── control-rhythm.md             # Weekly / monthly / quarterly cadence
│   ├── decision-rights.md            # AOCC, data owners, exec sponsor
│   └── guardrails.md                 # Five non-negotiable guardrail rules
├── pitch/
│   └── investment-memo.md            # Category, pain, solution, moat, ROI
└── CHANGELOG.md
```

---

## Part of the Fruitful Bough Platform Portfolio

| Platform | Domain | Status |
|----------|--------|--------|
| [Aurora OS](../aurora-os) | AI discovery & growth intelligence | Phase 1 build |
| [ACE-OS](../ace-os) | Airport commerce & experience operating system | Architecture complete |
| **ACO** (this repo) | Aviation digital twin capacity orchestrator | PRD complete · MVP 90–120 days |
| [FMCG GTM Accelerator](../uk-fmcg-gtm-accelerator) | UK grocery channel decision intelligence | PRD complete |
| [SIGFA](../sigfa) | Social impact growth & funding accelerator | Pilot proven · MVP live |

---

*Built by [Kwasi Boakye Acheampong](https://github.com/kwasibacheampong) · Principal, Fruitful Bough · 2025–2026*  
*Integrity · Trust · Value*
