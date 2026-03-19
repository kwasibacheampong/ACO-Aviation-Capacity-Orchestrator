# ACO — Reference Architecture

## End-to-end system architecture

Full DevOps-executable specification for the Aviation Capacity Orchestrator.

---

## Layer overview

```
┌─────────────────────────────────────────────────────────────┐
│  SOURCE SYSTEMS                                              │
│  AODB · FIDS · Stand/Gate Manager · Security Sensors ·      │
│  Weather/NOTAM · Ground Handling                             │
└───────────────────────┬─────────────────────────────────────┘
                        │ REST / SFTP CSV / WebSocket / MQ
┌───────────────────────▼─────────────────────────────────────┐
│  INGESTION LAYER                                             │
│  Connector microservices (per source system)                 │
│  Event schema registry · Dead-letter queue handling          │
│  → Normalise to canonical aco_event schema                   │
└───────────────────────┬─────────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────────┐
│  EVENT BACKBONE — Kafka                                      │
│  flight.events · passengerflow.events · security.events      │
│  resource.events · constraints.events                        │
└───────────────────────┬─────────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────────┐
│  STREAM PROCESSING — Apache Flink                            │
│  Normalisation + enrichment                                  │
│  State derivation                                            │
│  Feature generation for ML                                   │
│  Anomaly detection (optional MVP)                            │
└───────────────────────┬─────────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────────┐
│  STORAGE LAYER                                               │
│  Event store: S3-compatible + compacted Kafka topics         │
│  Operational state: Postgres/Redis (hot) + Timescale (TS)    │
│  Graph store: Neo4j — topology + dependencies                │
│  Analytics (optional): DuckDB / BigQuery / Snowflake         │
└───────────────────────┬─────────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────────┐
│  DIGITAL TWIN ENGINE                                         │
│  Twin API (read) — < 250ms p95 on hot paths                  │
│  Twin Mutation Service (write from streams)                  │
│  Scenario Service (fork twin state for simulation)           │
│  Graph: nodes (zones/gates/stands/checkpoints)               │
│         edges (flow paths)                                   │
│         constraints (caps, policy, staffing)                 │
└───────────────────────┬─────────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────────┐
│  ML / SIMULATION                                             │
│  Feature store → Model registry + versioning                 │
│  Inference service (gRPC)                                    │
│  Simulation runner (containerised, horizontally scalable)    │
│  Queue forecast: T+15 / T+30 / T+60                          │
│  Gate conflict detection: ≥ 30 min early warning             │
└───────────────────────┬─────────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────────┐
│  ORCHESTRATION ENGINE                                        │
│  Rules engine (guardrails)                                   │
│  Recommendation ranking                                      │
│  Playbook catalog                                            │
│  Action dispatcher (advisory first; automation optional)     │
│  Every output: decision_trace_id + explainability_payload    │
└───────────────────────┬─────────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────────┐
│  APPLICATION LAYER                                           │
│  API Gateway · RBAC + Audit Service                          │
│  Web UI: Ops console · Executive view                        │
│  Role-based views: Ops / Exec / Commercial / Regulatory      │
└─────────────────────────────────────────────────────────────┘
```

---

## Kafka topic domains

| Topic | Events |
|-------|--------|
| `flight.events` | Arrivals, departures, delays, gate assignments, turnaround status |
| `passengerflow.events` | Zone crowding, terminal flow, boarding gates |
| `security.events` | Queue lengths, wait times, lane open/close, Border Force events |
| `resource.events` | Staff assignments, GSE availability, security lane status |
| `constraints.events` | Noise curfew activations, slot constraints, regulatory alerts |

---

## Input formats supported

| Format | Use case |
|--------|---------|
| REST | Real-time APIs (AODB, security sensors) |
| SFTP CSV | Batch feeds (schedule, stand plans) |
| Message Queue | Async event streams |
| WebSocket | Low-latency streaming (queue sensors) |

---

## Runtime stack

```yaml
orchestration:   Kubernetes (EKS / AKS / GKE) + Helm charts
streaming:       Kafka (managed or Strimzi on-cluster)
database:        PostgreSQL + TimescaleDB
graph:           Neo4j or JanusGraph
object_store:    S3-compatible (AWS S3 / MinIO / Azure Blob)
secrets:         HashiCorp Vault or cloud-native KMS
ci_cd:           GitHub Actions + ArgoCD
environments:    dev / staging / prod
test_data:       Synthetic airport data generator (containerised)
```

---

## Observability — OpenTelemetry traces

Full trace coverage across:

```
ingestion → stream processing → state write → ML inference → recommendation → UI
```

### SLO dashboards
| SLO | Target |
|-----|--------|
| Ingest lag | < 5 seconds (streaming sources) |
| Event processing throughput | Per-airport configured |
| State query p95 | < 250ms |
| State query p99 | < 500ms |
| Model inference p95 | Airport-configurable |
| UI error rate | < 0.1% |

### Runbooks (required at launch)
- Ingestion backpressure
- Consumer lag
- Model inference degradation
- Graph store saturation

---

*ACO · Fruitful Bough · 2025–2026*
