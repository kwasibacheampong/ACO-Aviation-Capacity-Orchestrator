# ACO — Core Domain Tables

Full schema specification for the Aviation Capacity Orchestrator data model.

---

## Canonical event schema — `aco_event`

All ingested events from all source systems are normalised to this schema before entering the event backbone.

```sql
CREATE TABLE aco_event (
  event_id          UUID PRIMARY KEY,
  event_time        TIMESTAMPTZ NOT NULL,
  ingest_time       TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  event_type        VARCHAR(64) NOT NULL,   -- enum: flight_update, queue_change, gate_assign, etc.
  source_system     VARCHAR(64) NOT NULL,   -- e.g. 'AODB', 'FIDS', 'security_sensor_T2'
  tenant_id         VARCHAR(64) NOT NULL,
  payload           JSONB NOT NULL,
  schema_version    VARCHAR(16) NOT NULL,
  correlation_id    UUID                    -- links related events
);
```

---

## Domain tables

### `flights`

```sql
CREATE TABLE flights (
  flight_id           UUID PRIMARY KEY,
  tenant_id           VARCHAR(64) NOT NULL,
  airline_code        CHAR(2) NOT NULL,
  flight_number       VARCHAR(8) NOT NULL,
  origin              CHAR(3),              -- IATA code
  destination         CHAR(3),              -- IATA code
  sched_arrival       TIMESTAMPTZ,
  sched_departure     TIMESTAMPTZ,
  act_arrival         TIMESTAMPTZ,
  act_departure       TIMESTAMPTZ,
  turnaround_start    TIMESTAMPTZ,
  turnaround_end      TIMESTAMPTZ,
  gate_id             UUID REFERENCES airport_nodes(node_id),
  stand_id            UUID REFERENCES airport_nodes(node_id),
  status              VARCHAR(32)           -- scheduled/arrived/boarding/departed/delayed/cancelled
);
```

---

### `airport_nodes`

Nodes represent all physical locations in the airport graph — gates, stands, zones, security, border, retail.

```sql
CREATE TABLE airport_nodes (
  node_id             UUID PRIMARY KEY,
  tenant_id           VARCHAR(64) NOT NULL,
  node_type           VARCHAR(32) NOT NULL, -- gate/stand/zone/security/border/retail
  terminal            VARCHAR(8),
  capacity_nominal    INTEGER,
  geometry            JSONB,                -- polygon or point for mapping
  attributes          JSONB                 -- node-specific metadata
);
```

---

### `airport_edges`

Edges represent all flow paths — walkways, bus routes, airside paths.

```sql
CREATE TABLE airport_edges (
  edge_id             UUID PRIMARY KEY,
  tenant_id           VARCHAR(64) NOT NULL,
  from_node_id        UUID REFERENCES airport_nodes(node_id),
  to_node_id          UUID REFERENCES airport_nodes(node_id),
  edge_type           VARCHAR(32),          -- walkway/bus/airside_path
  travel_time_nominal INTEGER,              -- seconds
  constraints         JSONB                 -- e.g. direction restrictions, capacity limits
);
```

---

### `queues` (time series — use TimescaleDB hypertable)

```sql
CREATE TABLE queues (
  tenant_id           VARCHAR(64) NOT NULL,
  queue_id            UUID NOT NULL,
  node_id             UUID REFERENCES airport_nodes(node_id),
  ts                  TIMESTAMPTZ NOT NULL,
  current_load        INTEGER,              -- people in queue
  wait_time_est       INTEGER,              -- seconds
  service_rate_est    NUMERIC(6,2),         -- people per minute
  forecast_15         JSONB,                -- {load, wait_time, overflow_risk}
  forecast_30         JSONB,
  forecast_60         JSONB,
  PRIMARY KEY (tenant_id, queue_id, ts)
);

-- TimescaleDB
SELECT create_hypertable('queues', 'ts');
```

---

### `resources`

```sql
CREATE TABLE resources (
  resource_id         UUID PRIMARY KEY,
  tenant_id           VARCHAR(64) NOT NULL,
  resource_type       VARCHAR(32) NOT NULL, -- security_lane/staff/gse
  node_id             UUID REFERENCES airport_nodes(node_id),
  status              VARCHAR(16) NOT NULL, -- available/down/assigned
  capacity_effect     NUMERIC(6,2)          -- throughput modifier
);
```

---

### `constraints`

```sql
CREATE TABLE constraints (
  constraint_id       UUID PRIMARY KEY,
  tenant_id           VARCHAR(64) NOT NULL,
  constraint_type     VARCHAR(32) NOT NULL, -- noise/slot/staffing/safety
  scope               VARCHAR(16) NOT NULL, -- airport/terminal/node/flight
  limit_value         JSONB NOT NULL,        -- e.g. {max_movements: 480, window: "23:00-06:00"}
  active_from         TIMESTAMPTZ,
  active_to           TIMESTAMPTZ,
  authority           VARCHAR(128)           -- e.g. 'CAA', 'Local Authority', 'Airport Policy'
);
```

---

### `recommendations`

```sql
CREATE TABLE recommendations (
  recommendation_id   UUID PRIMARY KEY,
  tenant_id           VARCHAR(64) NOT NULL,
  created_at          TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  horizon_minutes     INTEGER NOT NULL,      -- T+15/30/60
  target_scope        JSONB,                 -- {type: node|flight, id: uuid}
  action_type         VARCHAR(64) NOT NULL,  -- open_lane/reroute_flow/gate_swap/schedule_smooth
  expected_impact     JSONB NOT NULL,        -- {throughput_delta, wait_time_delta, revenue_delta, compliance_risk}
  confidence          NUMERIC(4,3),          -- 0.000–1.000
  decision_trace_id   UUID NOT NULL,
  status              VARCHAR(16) NOT NULL   -- proposed/accepted/rejected/expired
);
```

---

### `decision_traces`

The explainability and audit backbone. Every recommendation is linked to exactly one decision trace.

```sql
CREATE TABLE decision_traces (
  decision_trace_id   UUID PRIMARY KEY,
  tenant_id           VARCHAR(64) NOT NULL,
  created_at          TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  input_event_ids     UUID[] NOT NULL,        -- events that triggered this decision
  features_version    VARCHAR(32) NOT NULL,   -- feature store snapshot version
  model_versions      JSONB NOT NULL,         -- {queue_model: v2.1, conflict_model: v1.4}
  explainability      JSONB NOT NULL,         -- SHAP values or equivalent driver breakdown
  guardrails_checked  JSONB NOT NULL,         -- {noise: pass, staffing: pass, safety: pass}
  operator_override   JSONB                   -- {operator_id, reason, timestamp} if overridden
);
```

---

## Mandatory lineage fields

All persisted artifacts must carry:

| Field | Purpose |
|-------|---------|
| `tenant_id` | Multi-airport isolation |
| `source_system` | Data provenance |
| `schema_version` | Forward/backward compatibility |
| `model_version` | Reproducibility for audit |
| `decision_trace_id` | Full recommendation lineage |

**Event sourcing discipline:** Full state must be rebuildable from event log replay. This is a hard requirement for regulatory audit — "Why did we do X at 09:03?" must be answerable in under 1 minute.

---

*ACO · Fruitful Bough · 2025–2026*
