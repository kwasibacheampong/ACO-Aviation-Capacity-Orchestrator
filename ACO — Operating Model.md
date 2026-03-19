# ACO — Operating Model

## Governance, cadence, and decision rights

---

## Five-layer operating structure

### Layer 1 — Capacity Owner (AOCC Orchestration Lead)

The human accountability layer. This role owns:

| Accountability | What it means |
|---------------|--------------|
| **Predictability** | System performs reliably under peak load and disruption |
| **Growth without trust erosion** | Throughput gains do not compromise stakeholder or regulatory relationships |
| **Clear ownership of failure modes** | When something goes wrong, the trace is clear and the response is documented |
| **Non-displaceability** | ACO becomes the operating layer — not a bolt-on tool |

---

### Layer 2 — Operational Intelligence Spine

Daily outputs produced by the platform:

| Output | Detail |
|--------|--------|
| **Constraint outlook** | Next 2 / 6 / 24 hour constraint risk profile |
| **Top 5 bottleneck risks** | Ranked by impact with driver breakdown |
| **Ecosystem signals** | Airline schedule changes, staffing shifts, weather, policy updates |

Controls maintained by data domain owners:

- Data quality scorecards per source system
- Ingestion lag monitoring
- Confidence heatmaps (model certainty by node/zone)

---

### Layer 3 — Orchestration Execution Surface

Three tiers of decision authority, activated in sequence as governance matures:

| Tier | Description | Gate to next tier |
|------|-------------|------------------|
| **Advisory** | Recommendation surfaced — operator decides | n/a — default at launch |
| **Guided** | Operator executes via toolchain with one-click confirmation | Demonstrated advisory reliability + operator sign-off |
| **Automated** | System executes within guardrail envelope without operator confirmation | Measured reliability thresholds met + executive sponsor sign-off |

Every action at every tier produces:
- `decision_trace_id`
- Guardrail check results
- Predicted impact
- Post-action measurement (for model learning)

---

### Layer 4 — Strategic Guardrails

Non-negotiable. Enforced at the rules engine level — not guidelines, hard stops.

| Guardrail | Rule |
|-----------|------|
| **No trust fragmentation** | No actions that damage relationships between airport, airline, security, or ground handler stakeholders |
| **No unaudited claims** | No capacity claims without audit evidence — every output must be traceable |
| **No black boxes** | Explainability required on every recommendation output |
| **No premature automation** | No automated action without measured reliability thresholds and explicit sign-off |
| **No compliance sacrifice** | Throughput targets never override safety, noise, or regulatory constraints |

---

### Layer 5 — Governance Cadence

#### Weekly — Risk & Readiness Note
- What changed in the operating environment this week
- Where risk is accumulating (queues, stands, staffing, weather)
- Which playbooks are "hot" this week
- **Output:** Risk & Readiness Note + recommended interventions list

#### Monthly — Model Performance Review
- Model drift analysis
- False positive / false negative rates by model
- Bottleneck portfolio (what is structurally limiting capacity)
- **Output:** Updated Orchestration Roadmap + investment asks

#### Quarterly — Narrative Reset
- Reframe the platform story with regulators and executives:
  - From "tech platform" → "capacity credibility + compliance-grade growth"
- **Output:** Strategic narrative refresh + KPI proof pack

---

## Decision rights

| Role | Owns |
|------|------|
| **AOCC Orchestration Lead** | Playbook activation · override policy · escalation triggers |
| **Data Domain Owners** | Source system fidelity · schema governance |
| **Executive Sponsor** | Automation enablement gates · regulatory posture |

---

*ACO · Fruitful Bough · 2025–2026*

---
---

# ACO — Investment Memo

## Category

Capacity orchestration infrastructure for regulated mobility ecosystems.

---

## The pain

Airports cannot expand easily. Regulatory constraints, ESG pressure, planning timelines, and community opposition make physical expansion a 10–15 year commitment at best. Demand growth does not wait.

The bottleneck is operational capacity — the ability to orchestrate existing airside and landside resources in real time, under constraints.

---

## The solution

A digital twin + predictive + orchestration engine that converts airport operations into:

- **Forecastable capacity** — what will constrain throughput in the next 15/30/60 minutes
- **Controllable constraints** — guardrail-governed recommendations with full audit trail
- **Measurable growth** — 5–15% throughput uplift without physical expansion

**"Digital twin is not visualisation. It is governance + execution."**

---

## Why now

| Factor | Detail |
|--------|--------|
| **Data maturity** | AODB, sensor, queue, and operational feeds now available and standardised at most Tier 1/2 airports |
| **Stream processing** | Kafka + Flink make real-time event-driven state management production-grade |
| **ML explainability** | SHAP and equivalent techniques make model outputs auditable — meeting regulatory requirements |
| **ESG/regulatory pressure** | Orchestration is becoming non-optional — capacity must be demonstrably managed, not just reported |

---

## Buyers and ACV

| Segment | Model | ACV |
|---------|-------|-----|
| Tier 1 hubs | Multi-year platform licence + professional services | High |
| Mid-tier airports | Modular deployment (MVP first, expand) | Mid |
| Airport groups | Portfolio orchestration — shared playbook library | Premium |

---

## Moat

| Source | Detail |
|--------|--------|
| **Multi-stakeholder embedding** | Data integrations across AODB, FIDS, security, ground handling — switching cost is high once live |
| **Twin topology + playbook library** | Airport-specific graph topology and operational playbooks compound over time |
| **Compliance-grade decision trace** | `decision_trace_id` on every output — no competitor has this at production grade |
| **Model learning per archetype** | Models improve per airport type (hub, regional, O&D) — data moat grows with fleet size |

---

## ROI model

| KPI | Driver |
|-----|--------|
| **Throughput uplift** | 5–15% more movements without physical expansion |
| **Queue reduction** | Security and border wait time reduction → NPS + passenger satisfaction |
| **OTP lift** | Gate/stand conflict prevention → on-time performance improvement |
| **Retail dwell uplift** | Flow optimisation → more time in commercial zones |

**The "invisible expansion" story is CFO-friendly.** No capex. No planning permission. No 10-year timeline. Measurable ROI from month one.

---

## Competition

| Category | Why it is not ACO |
|----------|------------------|
| Airport dashboards | Visualisation ≠ orchestration. Dashboards show what happened. ACO acts on what is about to happen. |
| Digital twin vendors | 3D twins ≠ execution. Most twins are visualisation layers without a recommendations engine or audit trail. |
| Operations optimisation tools | Point solutions (queue management, gate allocation) ≠ integrated capacity orchestration. |

---

## 10-slide pitch structure

1. The Aviation Constraint Crisis — growth capped, expansion blocked
2. Capacity Is the New Expansion — why orchestration wins
3. What We Built — ACO Platform (digital twin + orchestration)
4. How It Works — data → twin → prediction → actions
5. MVP Demo Walkthrough — bottleneck forecast → intervention
6. ROI Model — throughput + revenue + resilience
7. Market & Category — airports + adjacent regulated hubs
8. Business Model — platform pricing, modules, services attach
9. Competition — dashboards ≠ orchestration · twins ≠ execution
10. Vision — adaptive mobility infrastructure · portfolio orchestration

---

*ACO · Fruitful Bough · 2025–2026*  
*Integrity · Trust · Value*
