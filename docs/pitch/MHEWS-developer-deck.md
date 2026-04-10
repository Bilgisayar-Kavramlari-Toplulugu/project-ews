---
marp: true
theme: default
paginate: true
title: MHEWS Developer Deck
description: System architecture, module responsibilities, and implementation view
---

# MHEWS Developer Deck
## System Overview, Modules, and How They Work

Audience: Engineering, QA, DevOps, Technical Leads

---

# 1) System Purpose and Scope

MHEWS is a software-only, multi-hazard warning platform that supports:

- Hazard monitoring and threshold evaluation
- CAP-compliant alert authoring and validation
- Controlled dissemination (Email, Web Portal, WhatsApp)
- Incident tracking, audit evidence, and drill workflows

Out of scope for this baseline:

- SMS/cell broadcast/mobile push
- External identity federation (SAML/OIDC/LDAP)
- Hardware siren control and physical infrastructure control

---

# 2) Architecture in One View

High-level flow:

1. Data sources are ingested and normalized (M3)
2. Threshold/risk logic drives breach visibility and workflows (M1/M2/M3)
3. Alerts are authored and validated as CAP artifacts (M6)
4. Approved alerts are disseminated and tracked (M7)
5. Incident, compliance, and evidence remain traceable end-to-end (M8/M9/M10/M11/M12)

Core properties:

- Modular backend apps
- Event-driven handoffs + explicit interface contracts
- Immutable auditability
- Role and attribute-aware authorization

---

# 3) Module Map (M1–M12)

- M1 `hazards`: hazard types, taxonomy, thresholds, severity scales
- M2 `risk`: scenarios, risk modelling, calibration workflows
- M3 `ingestion`: source registry, polling, normalization, breach events
- M4 `forecasting`: model runs, connectors, confidence metadata
- M5 `impact`: exposure/vulnerability + spatial impact outputs
- M6 `alerts`: CAP draft lifecycle, validation, approval, XML package
- M7 `dissemination`: contacts, targeting, dispatch, receipts, portal
- M8 `incidents`: incident lifecycle and after-action records
- M9 `audit`: immutable logs, report/export, evidence packs
- M10 `accounts`: identity, RBAC/ABAC, user/role admin
- M11 `gateway`: API keys, connector surface, webhook handling, feed export
- M12 `preparedness`: drill mode, SOP flows, exercise summaries

---

# 4) Pipeline Walkthrough (Happy Path)

1. Admin configures hazard thresholds (M1)
2. Monitoring source emits new observation (M3)
3. Breach event enters `OPEN` and appears in operator dashboard (M3)
4. Operator creates CAP draft from breach context (M6)
5. CAP validation runs (schema + configured profile) (M6)
6. Approver finalizes publication (dual-control policies in M10/M6)
7. Dispatch jobs run per channel (M7)
8. Receipts/webhooks update delivery status (M7/M11)
9. Audit events and evidence package remain queryable/exportable (M9)

---

# 5) Key Interface Contracts (Engineering Critical)

- IC-01: M3 -> M6 (`threshold_breached`) notification handoff
- IC-02: M6 -> M7 (`cap_approved`) dispatch trigger
- IC-03: M7 -> M9 dispatch-state audit logging
- IC-04: M3 -> M9 ingestion/breach audit events
- IC-05: M6 -> M4 forecast context retrieval (post-PoC)
- IC-06: M8 -> M2 after-action calibration signal
- IC-07: M6 -> External CAP feed export endpoint

Why this matters:

- Prevents module boundary drift
- Makes integration testable with explicit guarantees
- Enables parallel team execution with stable seams

---

# 6) Module Feature Highlights

M1–M3 (signal generation):
- Threshold configuration and validation
- Source health, failure/backoff behavior
- Breach state transitions and operator action points

M4–M5 (decision support):
- Forecast model orchestration and confidence output
- Exposure ingestion and impact computations
- Spatial outputs for operator context

M6–M7 (public warning path):
- CAP template/editor/validator/update-cancel chain
- Contact targeting and multi-channel dispatch
- Receipt capture and retry workflows

M8–M12 (governance/readiness):
- Incident closure and lessons learned
- Immutable evidence and compliance reports
- Drill isolation and SOP-linked response readiness

---

# 7) Data and State Machines

Core stateful entities:

- `CAPDraft`: draft -> pending approval -> approved/rejected/published
- `ThresholdBreachEvent`: open -> acknowledged -> drafted/resolved/dismissed
- `DispatchJob` and `DispatchReceipt`: queued/run/completed + per-recipient outcomes
- `Incident`: created -> active -> closed (with after-action)
- `DrillMode`: inactive -> active -> completed

Engineering principle:

- Use strict transition guards + audit events for every critical transition.
- Reject invalid transitions with explicit error codes.

---

# 8) Cross-Cutting NFRs (What Developers Must Enforce)

- Performance/SLA: key latency and throughput targets
- Security: authn/authz, key handling, webhook validation, encryption
- Reliability: retries, isolation, backup and recovery readiness
- Observability: health checks, metrics, alerting, traceability
- Logging: structured logs with sensitive-data redaction
- Maintainability: migration discipline, lint/test gates, OpenAPI consistency
- Accessibility/UX constraints for operator/public-facing surfaces

Implementation rule:

- Treat NFRs as acceptance criteria, not optional hardening tasks.

---

# 9) Deployment and Runtime Model

Profiles:

- Profile A (cloud-enabled integrations)
- Profile B (on-premise/air-gapped capable pattern)

Runtime components:

- API services, Celery workers/queues, PostgreSQL/PostGIS, Redis, object storage
- Channel adapters (email/WhatsApp mock/live), webhook receiver, portal endpoints

Operational focus:

- Queue isolation for critical flows
- Config-driven provider switching
- Environment parity across dev/stage/prod

---

# 10) Delivery Strategy for Engineering Teams

Suggested team split:

- Data and monitoring: M1/M3
- Modelling and impact: M2/M4/M5
- Alert lifecycle and dissemination: M6/M7/M11
- Governance and platform: M8/M9/M10/M12 + DevOps

Execution checklist:

1. Lock interface contracts and event schemas
2. Build vertical slices through happy path
3. Add failure-path tests (timeouts, retries, invalid transitions)
4. Enforce traceability from requirement -> story -> test
5. Gate releases with performance, security, and audit checks

---

# 11) What to Build First

Sprint-priority technical baseline:

- M1 threshold registry + M3 ingestion/breach flow
- M6 CAP draft/validation + approval controls
- M7 email/portal dispatch + receipt persistence
- M9 immutable audit pipeline
- M10 role/access enforcement

Result:

- End-to-end warning path works early
- Subsequent modules extend capability without re-architecting core flow

---

# 12) Summary for Developers

MHEWS is a modular warning platform with explicit boundaries and strong auditability.

Success depends on:

- Contract-first module integration
- State-machine correctness
- NFR-driven implementation discipline
- Early end-to-end integration testing

If those are enforced, module-level parallel development is safe and scalable.

