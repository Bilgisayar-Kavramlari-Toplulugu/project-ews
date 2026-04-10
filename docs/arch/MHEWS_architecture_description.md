# MHEWS Architecture Description
**Standard:** IEEE 42010:2022 (ISO/IEC/IEEE 42010)
**Version:** 1.0
**Date:** 2026-03-06
**Status:** Baseline Draft

---

## 1. Introduction

### 1.1 Purpose

This document is the Architecture Description (AD) for the Multi-Hazard Early Warning System (MHEWS). It conforms to the structure and requirements of IEEE 42010:2022 and serves as the single authoritative reference for the system's architectural decisions, viewpoints, and rationale.

The AD bridges the gap between the Product Requirements Document (PRD) and implementation. It answers *how* the system is structured, *why* those structures were chosen, and *what constraints* shape the design space.

### 1.2 Scope

The MHEWS is a digital, software-only platform that supports the end-to-end early warning pipeline: hazard monitoring, threshold evaluation, CAP-format alert authoring, multi-channel dissemination, incident tracking, and structured audit. It is hazard-agnostic, model-driven, and deployable on both commercial cloud and government on-premise infrastructure using identical Docker images.

**In scope:** 12 software modules (M1--M12), their data models, integration contracts, deployment topology, security architecture, and quality attribute mechanisms.

**Out of scope:** Physical infrastructure (sirens, sensors), institutional governance, SMS/cell-broadcast/mobile push channels, external identity federation, and GIS tile serving (consume-only by design).

### 1.3 Intended Audience

| Audience | Primary Interest |
|---|---|
| Development team | Module boundaries, data models, interface contracts, technology choices |
| DevOps / Infrastructure | Deployment profiles, container topology, scaling triggers |
| Client product owner | Architecture decisions aligned to stakeholder answers (BQ-01--BQ-21) |
| System integrators | External interface contracts, API gateway, connector registry |
| Auditors | Traceability from requirements to architecture mechanisms |

### 1.4 Conformance

This document conforms to IEEE 42010:2022 by:

- Identifying stakeholders and their concerns (Section 2)
- Defining architecture viewpoints with explicit concerns addressed (Section 3)
- Recording architecture decisions with context, rationale, and consequences (Section 4)
- Mapping quality attributes to architecture mechanisms (Section 5)
- Specifying interfaces between the system and its environment (Section 6)
- Documenting constraints and assumptions (Section 7)

### 1.5 Related Documents

| Document | Location |
|---|---|
| PRD (Section III Annex) | `docs/mhewsprd.md` |
| Design Document (Section II) | `docs/SECTION II -- Client Guidance and Software Design Document.docx` |
| Blocker Question Responses | `consolidation/07_bq_responses.md` |
| Interface Contracts | `consolidation/04_interface_contracts.md` |
| Deployment Profiles | `consolidation/10_deployment_profiles.md` |
| SLA Tiering Analysis | `consolidation/08_sla_tiering_analysis.md` |
| Capacity Baseline | `consolidation/09_capacity_baseline.md` |
| PlantUML Diagrams | `consolidation/06_plantuml_appendix.md` |
| Configuration Management Plan | `consolidation/12_cm_plan.md` |
| Sprint Plan | `poc_sprint_plan.md` |

---

## 2. Stakeholders and Concerns

### 2.1 Stakeholder Identification

| Stakeholder | Description | Representative Concerns |
|---|---|---|
| **NMHS Operators** | National Meteorological and Hydrological Service staff who monitor data, draft alerts, and manage incidents | Usability, alert pipeline speed, dashboard clarity, real-time data freshness |
| **Approvers** | Senior NMHS staff who review and authorize CAP alert dispatch | Dual-authorization enforcement, clear audit trail, mobile-friendly approval queue |
| **Tenant Administrators** | System administrators who configure hazard types, thresholds, contacts, and user roles | RBAC/ABAC management, contact directory tools, system health visibility |
| **Public Users** | Alert recipients (email, WhatsApp, web portal) and community reporters who submit feedback | Alert delivery reliability, multilingual content, accessible public portal, feedback submission |
| **Auditors** | Internal and external reviewers who verify compliance with WMO, regulatory, and institutional requirements | Immutable audit trail, evidence package generation, complete dispatch receipts |
| **System Integrators** | Teams connecting MHEWS to external data feeds, NMHS sensor networks, or downstream CAP consumers | Documented API contracts, OpenAPI schema, connector registry, CAP feed endpoint |
| **DevOps / Infrastructure** | Teams responsible for deployment, monitoring, and scaling | Container topology, health checks, scaling triggers, environment-variable-driven configuration |
| **Development Team** | Engineers building and maintaining the system | Module boundaries, technology choices, test strategy, CI/CD pipeline |

### 2.2 Key Architectural Concerns

| Concern | Stakeholders | Architecture Response |
|---|---|---|
| **Availability** | All | 99.9% SLA, active-passive HA, health checks, auto-restart (Section 3.3, ADR-08) |
| **Performance** | Operators, Approvers | Celery worker isolation, Redis caching, PostGIS indexing, defined SLA per operation (Section 5) |
| **Security** | All | JWT + session auth, RBAC/ABAC, dual-authorization, TLS, field-level PII encryption, immutable audit (Section 3.5) |
| **Deployability** | DevOps, Client | Dual-profile Docker Compose (cloud + on-premise), env-var switching, no code differences (Section 3.3, ADR-03) |
| **Internationalization** | Operators, Public Users | Tri-lingual (French + English + Language X), django-modeltranslation, per-contact language dispatch (ADR-10) |
| **Scalability** | DevOps, Operators | Horizontal worker scaling, connection pooling, documented upgrade paths per capacity dimension (Section 5) |
| **Data Sovereignty** | Client, Auditors | Profile B: all data on-premise, local LLM (Ollama), no external API calls (Section 3.3) |
| **Maintainability** | Development Team | Modular Django apps, one-app-per-PRD-module, 12-factor config, CI/CD gates (Section 5) |
| **Interoperability** | System Integrators | CAP v1.2, OGC consume-only, Atom feed for CAP export, OpenAPI 3 schema (Section 6) |

---

## 3. Architecture Viewpoints

### 3.1 Functional Viewpoint

**Concerns addressed:** Module responsibilities, boundaries, inter-module dependencies.

#### 3.1.1 Module Inventory

The system comprises 12 modules, each mapped to a Django application. Module boundaries are enforced at the Python package level -- cross-module communication uses Django signals or REST API calls, never direct model imports.

| Module | Django App | Responsibility | Phase |
|---|---|---|---|
| **M1 -- Hazard & Taxonomy** | `hazards` | Hazard type registry, threshold configuration, administrative boundary management | Sprint 1 |
| **M2 -- Risk & Scenario Modelling** | `risk` | Scenario definition, model registry, threshold calibration log | Post-PoC |
| **M3 -- Data Ingestion & Monitoring** | `ingestion` | Data source registry, polling workers, observation storage, threshold evaluation | Sprint 1 |
| **M4 -- Forecasting & Nowcasting** | `forecasting` | Forecast model execution (scheduled + on-demand), model run lifecycle | Post-PoC (stub Sprint 1) |
| **M5 -- Impact Analysis** | `impact` | Exposure datasets, pre-computed impact layers, on-demand spatial queries | Post-PoC (stub Sprint 1) |
| **M6 -- CAP Alert Authoring** | `alerts` | CAP template management, draft lifecycle, XSD validation, LLM narrative integration, approval workflow | Sprint 2 |
| **M7 -- Dissemination** | `dissemination` | Contact directory, dispatch orchestration, email adapter, WhatsApp adapter, public web portal, community feedback | Sprint 2 |
| **M8 -- Incident Tracking** | `incidents` | Incident lifecycle, after-action reports, community report linkage | Post-PoC (stub Sprint 1) |
| **M9 -- Audit & Compliance** | `audit` | Immutable audit event log, evidence package generation, compliance reporting | Sprint 1 |
| **M10 -- Administration & Access** | `accounts` | User management, RBAC (Django Groups), ABAC (django-guardian), JWT authentication | Sprint 1 |
| **M11 -- API Gateway** | `gateway` | API versioning, rate limiting, OpenAPI schema, connector registry, webhook receivers | Post-PoC (stub Sprint 1) |
| **M12 -- Preparedness & Drill** | `preparedness` | Drill mode activation, SOP repository, exercise simulation | Post-PoC |

Supporting applications:

| App | Purpose | Lifecycle |
|---|---|---|
| `core` | Shared base models (`TimestampedModel`), mixins, permission helpers | Permanent |
| `mock_whatsapp` | Internal mock of Meta Cloud API for development and testing | Removed when live WhatsApp connected |
| `config` | Django settings, URL routing, Celery configuration | Permanent |

#### 3.1.2 Module Dependency Map

Dependencies flow downstream through the alert pipeline. No circular dependencies exist.

```
M3 (Ingestion) ──signal──► M6 (Alerts) ──signal──► M7 (Dissemination)
       │                        │                         │
       ▼                        ▼                         ▼
M1 (Hazards)              M4 (Forecasting)          M9 (Audit)
                          M5 (Impact)               M10 (Auth)
                                                    M11 (Gateway)

M8 (Incidents) ◄── M6, M7 (linked post-event)
M2 (Risk) ◄── M8 (calibration signal)
M12 (Preparedness) ── standalone, reads M1 + M6 data
```

**Coupling rules:**
- M3 emits `threshold.breached` signal; M6 subscribes. M3 has no knowledge of M6.
- M6 emits `cap.approved` signal; M7 subscribes. M6 has no knowledge of M7.
- M9 (Audit) receives synchronous calls from all modules; it has no outbound dependencies.
- M10 (Auth) is a dependency of every authenticated endpoint; it has no module-level outbound dependencies.

#### 3.1.3 Diagrams

- **Use Case Diagram:** `consolidation/06_plantuml_appendix.md` -- Diagram 1
- **Component Diagram:** `consolidation/06_plantuml_appendix.md` -- Diagram 2

---

### 3.2 Information Viewpoint

**Concerns addressed:** Core data entities, relationships, data flow through the alert pipeline.

#### 3.2.1 Core Data Entities

| Entity | Module | Description |
|---|---|---|
| `HazardType` | M1 | Hazard classification (Flood, Cyclone, Drought, etc.). Reference data, infrequently changed. |
| `ThresholdConfig` | M1 | Parameter-threshold pair per hazard type (e.g., river_level_m > 5.0). Drives breach detection. |
| `AdminBoundary` | M1 | PostGIS geometry for administrative regions (country, region, district). Imported from shapefile/GADM. |
| `DataSource` | M3 | Registered external feed (GDACS, ECMWF, NMHS sensor). Contains URL, poll interval, auth config. |
| `ObservationRecord` | M3 | Single observation from a data source: value, unit, location (PostGIS Point), timestamp. |
| `ThresholdBreachEvent` | M3 | Records when an observation exceeds a configured threshold. State machine: OPEN > ACKNOWLEDGED > RESOLVED / CAP_DRAFTED. |
| `ForecastModel` | M4 | Registered forecast/impact model. Metadata: name, type, hazard scope, execution parameters. |
| `ModelRun` | M4 | Single execution of a forecast model. State: QUEUED > RUNNING > COMPLETED / FAILED. Output stored in MinIO. |
| `RiskScenario` | M2 | Scenario definition combining hazard, exposure area, and model configuration. |
| `CAPTemplate` | M6 | Pre-configured alert template per hazard type. Contains default headline, description, instruction patterns. |
| `CAPDraft` | M6 | Operator-authored alert draft. State machine: DRAFT > PENDING_APPROVAL > APPROVED / REJECTED / EXPIRED / CANCELLED. |
| `ValidationResult` | M6 | XSD and business rule validation outcome for a CAPDraft. |
| `CAPPackage` | M6 | Immutable approved alert: generated XML, SHA-256 hash, XSD version. Created on approval. |
| `LLMOutput` | M6 | AI-generated narrative: model ID, version, prompt hash, output text, token count, latency. Full audit trail. |
| `Contact` | M7 | Alert recipient: name, email, WhatsApp number (E.164), preferred language, area of interest, opt-in status per channel. |
| `DispatchJob` | M7 | Per-channel dispatch for an approved alert: channel (EMAIL/WHATSAPP), status, contact count. |
| `DispatchReceipt` | M7 | Per-contact delivery record: provider message ID, status (QUEUED/SENT/DELIVERED/FAILED/BOUNCED), timestamps. |
| `CommunityReport` | M7 | Public-submitted geo-tagged field report. AI-categorized, moderation-gated. |
| `Incident` | M8 | Operational event record linking alerts, breach events, community reports, and after-action analysis. |
| `AuditEntry` | M9 | Immutable event record: event type, actor, resource reference, JSON payload, timestamp. No UPDATE/DELETE permitted. |
| `Drill` | M12 | Exercise simulation session. Flags all generated artifacts with `status=Exercise` to distinguish from live operations. |

#### 3.2.2 Key Relationships

```
HazardType ──1:N──► ThresholdConfig
HazardType ──1:N──► CAPTemplate
HazardType ──1:N──► DataSource (via hazard_type FK)

DataSource ──1:N──► ObservationRecord
ObservationRecord ──N:1──► ThresholdBreachEvent

ThresholdBreachEvent ──0:1──► CAPDraft (linked on operator action)
CAPDraft ──N:1──► CAPTemplate
CAPDraft ──0:1──► CAPPackage (created on approval)
CAPDraft ──0:1──► LLMOutput (AI narrative)

CAPPackage ──1:N──► DispatchJob (one per channel)
DispatchJob ──1:N──► DispatchReceipt (one per contact)

Contact ──N:M──► ContactGroup
Contact ──N:1──► AdminBoundary (area of interest)

Incident ──N:M──► CAPPackage (alerts linked to incident)
Incident ──N:M──► CommunityReport (field reports linked post-moderation)
Incident ──0:1──► AfterActionReport

ForecastModel ──1:N──► ModelRun
```

#### 3.2.3 Alert Pipeline Data Flow

The primary data flow follows the WMO warning chain: observation to decision to action.

```
[External Feed] ──poll──► ObservationRecord ──evaluate──► ThresholdBreachEvent
                                                               │
                                                          (operator action)
                                                               │
                                                               ▼
                          LLMOutput ◄──generate── CAPDraft ──validate──► ValidationResult
                                                     │
                                                (approver action)
                                                     │
                                                     ▼
                                               CAPPackage ──signal──► DispatchJob
                                                                         │
                                                                    ┌────┴────┐
                                                                    ▼         ▼
                                                              Email WA    WhatsApp
                                                                    │         │
                                                                    ▼         ▼
                                                             DispatchReceipt (per contact)
                                                                    │
                                                                    ▼
                                                              AuditEntry (per event)
```

**Sequence Diagrams:**
- Alert Pipeline: `consolidation/06_plantuml_appendix.md` -- Diagram 3
- Data Ingestion Pipeline: `consolidation/06_plantuml_appendix.md` -- Diagram 4
- AI Narrative Generation: `consolidation/06_plantuml_appendix.md` -- Diagram 5
- CAP State Machine: `consolidation/06_plantuml_appendix.md` -- Diagram 6

---

### 3.3 Deployment Viewpoint

**Concerns addressed:** Container topology, dual-profile deployment, environment-variable-driven switching.

#### 3.3.1 Core Principle

One codebase. One set of Docker images. Two deployment profiles differentiated entirely by environment variables and external service endpoints. No `if profile == 'cloud':` logic exists in application code.

#### 3.3.2 Container Services

| Service | Image | Purpose | Both Profiles |
|---|---|---|---|
| `web` | Custom Django (Gunicorn, 4 workers) | API server, serves DRF endpoints | Yes |
| `celery-default` | Same Django image | Workers for dispatch, audit, report generation | Yes |
| `celery-model` | Same Django image | Isolated workers for forecast/impact model execution | Yes |
| `celery-beat` | Same Django image | Periodic task scheduler | Yes |
| `redis` | `redis:7-alpine` | Celery broker + application cache | Yes |
| `postgres` | `postgis/postgis:16-3.4` | Primary database with PostGIS | Yes |
| `minio` | `minio/minio` | S3-compatible object storage | Profile B (Profile A uses S3/GCS) |
| `tileserver` | `maptiler/tileserver-gl` | Self-hosted map tiles | Profile B only |
| `ollama` | `ollama/ollama` | Self-hosted LLM inference | Profile B only |

#### 3.3.3 Profile A -- Cloud Deployment

**Target:** National authorities with no restrictions on commercial cloud providers.

| Component | Implementation |
|---|---|
| PostgreSQL + PostGIS | Container or managed (RDS + PostGIS extension) |
| Redis | Container or managed (ElastiCache) |
| Object Storage | AWS S3 / Azure Blob / GCP GCS |
| LLM | Anthropic Claude API (external) |
| Email | SendGrid or AWS SES (external) |
| WhatsApp | Meta Cloud API (external) |
| Map Tiles | OpenFreeMap hosted vector tiles |

```
[Operators/Public] ──HTTPS──► [Django API — Cloud VM]
                                    │
                          ┌─────────┼──────────┐
                          ▼         ▼          ▼
                     [PostgreSQL] [Redis]  [S3/MinIO]
                                    │
                          ┌─────────┼──────────┐
                          ▼         ▼          ▼
                    [Claude API] [SendGrid] [Meta WA]
```

#### 3.3.4 Profile B -- On-Premise / Air-Gapped Deployment

**Target:** Government ministries and national meteorological services whose data sovereignty policy prohibits commercial cloud hosting or external API calls.

| Component | Implementation |
|---|---|
| PostgreSQL + PostGIS | Container (on-premise) |
| Redis | Container (on-premise) |
| Object Storage | MinIO container (on-premise) |
| LLM | Ollama container (local, no external calls) |
| Email | Postfix container or government SMTP relay |
| WhatsApp | Disabled (`WHATSAPP_ENABLED=false`) or Meta if partially connected |
| Map Tiles | tileserver-gl container + country `.mbtiles` extract |

```
[Operators/Public] ──HTTPS──► [Django API — Gov Server]
                                    │
                          ┌─────────┼──────────┐
                          ▼         ▼          ▼
                     [PostgreSQL] [Redis]  [MinIO]
                                    │
                          ┌─────────┼──────────┐
                          ▼         ▼          ▼
                    [Ollama]  [Postfix SMTP]  [WA: disabled]
```

#### 3.3.5 Docker Compose File Structure

| File | Purpose |
|---|---|
| `docker-compose.yml` | Base services: PostgreSQL, Redis, MinIO, Django, Celery workers, Beat |
| `docker-compose.cloud.yml` | Profile A override: removes MinIO (uses S3), sets cloud env vars |
| `docker-compose.onprem.yml` | Profile B override: adds Ollama, tileserver, Postfix; disables WA |
| `docker-compose.dev.yml` | Development: adds pgAdmin, Flower, mock_whatsapp, Mailhog |

**Usage:**
```bash
# Profile A (cloud)
docker compose -f docker-compose.yml -f docker-compose.cloud.yml up

# Profile B (on-premise)
docker compose -f docker-compose.yml -f docker-compose.onprem.yml up
```

#### 3.3.6 Profile Migration

Migration between profiles requires only environment variable changes and data transfer (pg_dump + MinIO mirror). No code changes. The adapter pattern for LLM, email, object storage, and map tiles ensures all services are swappable. Estimated migration downtime: less than 5 minutes (service restart after `.env` update).

**Deployment Diagrams:** `consolidation/10_deployment_profiles.md` -- Profile A and Profile B PlantUML diagrams.

---

### 3.4 Concurrency Viewpoint

**Concerns addressed:** Worker isolation, queue topology, connection management, async patterns.

#### 3.4.1 Celery Worker Pools

The system uses dedicated Celery queues to isolate workloads and prevent mutual interference. This is a core architectural requirement (NFR-PERF-BATCH-01): batch processing must not degrade real-time alert pipeline performance.

| Queue | Worker Pool | Concurrency | Responsibility |
|---|---|---|---|
| `celery-default` | `celery-default` worker | 4 concurrent tasks | Dispatch jobs, audit report generation, community report categorization, scheduled summaries |
| `celery-model` | `celery-model` worker | 2 concurrent tasks | Forecast model execution, impact computation, NWP ingestion post-processing |
| `celery-ingestion` | `celery-default` worker (shared) | 8 concurrent tasks | Data source polling (100 sources at 60s interval = 1.67 tasks/sec peak) |
| `celery-beat` | Scheduler (single process) | N/A | Periodic task triggers: polling schedule, breach resolution check (5 min), report generation, cluster summarization (2 hr) |

#### 3.4.2 Redis Usage

Redis serves dual roles:

| Role | Key Pattern | TTL | Notes |
|---|---|---|---|
| Celery broker | `celery` prefix (managed by Celery) | Task-dependent | Message queue for all worker pools |
| Application cache | `mhews:cache:*` | 60s--3600s | Threshold configs, hazard type lookups, session data |
| SSE channel | `mhews:sse:*` | Ephemeral | Server-Sent Events for dashboard real-time updates |

Single Redis instance (1GB RAM) is sufficient at baseline. Redis Sentinel mode is recommended for the HA configuration (3 nodes: master + 2 Sentinel).

#### 3.4.3 Database Connection Pooling

| Parameter | Baseline Value | Rationale |
|---|---|---|
| PgBouncer pool size | 50 connections | 1 per concurrent API request at peak |
| Pool mode | Transaction pooling | Connections returned to pool after each transaction; maximizes utilization |
| Django `CONN_MAX_AGE` | 0 (managed by PgBouncer) | Django delegates connection lifecycle to PgBouncer |

**Upgrade trigger:** When concurrent sessions exceed 40 (80% of pool), increase to 100 connections via PgBouncer configuration change (no code change).

#### 3.4.4 Async Patterns

| Pattern | Mechanism | Use Case |
|---|---|---|
| Fire-and-forget task | Celery `delay()` | Dispatch job creation, LLM narrative generation, community report categorization |
| Task chain | Celery `chain()` | NWP ingestion > model trigger: `ingest_nwp_product.s() | trigger_model_run.s()` |
| Scheduled periodic | Celery Beat (cron) | Data source polling, breach resolution check, report generation |
| Webhook receiver | Django view (synchronous) | SendGrid delivery receipts, WhatsApp delivery receipts, WhatsApp STOP handling |
| Real-time push | Server-Sent Events (SSE) | Breach notifications, approval queue updates, dispatch status updates |
| Long-running computation | Celery `celery-model` queue | Model runs (up to 120s for country-scale impact computation) |

---

### 3.5 Security Viewpoint

**Concerns addressed:** Authentication, authorization, data protection, audit integrity.

#### 3.5.1 Authentication

| Mechanism | Scope | Details |
|---|---|---|
| JWT (simplejwt) | API access (React frontend, integrators) | Access token: 15 min expiry. Refresh token: 24 hr. Stored in memory (not localStorage). |
| Django session | Django Admin access | Standard session-based auth for admin panel. |
| API key | CAP feed consumers, external integrations | Static bearer token for read-only public endpoints (M11). |
| Anonymous | Public portal, community feedback form | No authentication required. Rate-limited. |

#### 3.5.2 Authorization

**Layer 1 -- RBAC (Django Groups):**

| Role | Group | Permissions |
|---|---|---|
| Operator | `operator` | Draft alerts, view observations, acknowledge breaches, view dispatch status, use chatbot |
| Approver | `approver` | All Operator permissions + approve/reject alerts |
| Admin | `admin` | All Approver permissions + manage users, manage hazard types, manage contacts, manage thresholds, activate drill mode, view full audit log |
| Auditor | `auditor` | Read-only access to audit log + evidence packages |

**Layer 2 -- ABAC (django-guardian, object-level):**

Object-level permissions are installed from Sprint 1 but policies are configured post-PoC. This enables per-object access control without architectural changes:
- Per-hazard-type alert drafting permissions
- Per-region data source management
- Per-team incident ownership

**Dual Authorization (BQ-03):**

The user who submits a CAP draft for approval SHALL NOT be the same user who approves it. Enforced at the API layer:
- `PATCH /cap/drafts/{id}/approve/` checks `request.user.id != cap_draft.submitted_by_id`
- Self-approval returns HTTP 403 with `SELF_APPROVAL_FORBIDDEN`
- Emergency bypass: Admin role only, requires written justification (50+ characters), logged as `AuditEvent(CAP_OVERRIDE_APPROVED)`, appended as `<note>` in CAP XML

#### 3.5.3 Data Protection

| Layer | Mechanism |
|---|---|
| Transport | TLS 1.2+ everywhere (HTTPS for API, encrypted connections to PostgreSQL and Redis) |
| At rest | AES-256 encryption for PostgreSQL data volume and MinIO storage |
| Field-level PII | `django-encrypted-model-fields` for Contact.phone_whatsapp, Contact.email (post-PoC hardening) |
| LLM data masking | PII redacted before LLM API calls (MHEWS-SD-LLM-06). Contact names, phone numbers, and email addresses never sent to external LLM. |
| Object integrity | CAPPackage: SHA-256 hash computed at generation; immutable after approval |

#### 3.5.4 Audit Trail Integrity

The audit subsystem (M9) enforces immutability:

- `AuditEntry` model has no `update()` or `delete()` methods exposed.
- Database-level CHECK constraint prevents UPDATE/DELETE on the `audit_auditentry` table.
- Not even superuser can modify audit records through the application.
- Evidence packages: ZIP archives containing CAP XML, dispatch receipts, and audit entries for a given alert, stored in MinIO with SHA-256 checksum.
- Retention: online for 2 years (NFR-RET-01); archive policy configurable.

---

## 4. Architecture Decisions (ADR Format)

### ADR-01: Django over FastAPI

**Status:** Accepted
**Date:** 2026-03-05
**Context:** The system has 12 PRD modules with complex user management (RBAC/ABAC, multi-tenant upgrade path, audit), and a small team (2--4 developers). Framework choice determines development velocity and long-term maintainability.
**Decision:** Django 5.x + Django REST Framework.
**Rationale:** Django provides built-in ORM with migrations, extensible auth system, admin interface, signals for event-driven audit, first-class Celery integration, and django-guardian for object-level permissions. FastAPI excels at narrow-scope microservices but requires assembling these capabilities from scratch. At MHEWS data volumes (50 concurrent users, 100 data sources), Django's synchronous request handling introduces no performance bottleneck.
**Consequences:** Synchronous views (async views available in Django 5.x but not required at baseline). DRF serializers handle all API validation. The team uses Django conventions (app per module, signals for decoupling, management commands for operations).

---

### ADR-02: Single-Tenant Baseline with Multi-Tenant Upgrade Path

**Status:** Accepted
**Date:** 2026-03-05 (BQ-02, BQ-07)
**Context:** Phase 1 serves a single national authority. Future phases may serve multiple countries.
**Decision:** Single-tenant deployment with no tenant isolation code. Upgrade path preserved via coding conventions.
**Rationale:** Multi-tenant architecture (schema separation, tenant-aware middleware) adds significant complexity with no Phase 1 benefit. Single-tenant deployment is simpler to deploy, test, and debug.
**Upgrade path:** `django-tenants` package for schema-per-tenant (1--2 sprint weeks) when a second tenant is onboarded. Convention: every user-created resource has `created_by` FK; no hardcoded organization names; `MHEWS_BASE_URL` from env var.
**Consequences:** No `tenant_id` FK on models. No tenant-aware middleware. Django admin is not the primary management UI (does not support tenant scoping).

---

### ADR-03: Dual Deployment Profile (Cloud + On-Premise)

**Status:** Accepted
**Date:** 2026-03-05 (BQ-06)
**Context:** Some clients operate under strict data sovereignty policies; others permit commercial cloud. The system must serve both without codebase divergence.
**Decision:** Two deployment profiles (A=cloud, B=on-premise) sharing identical Docker images, differentiated by environment variables only.
**Rationale:** Containerized architecture with adapter patterns for LLM, email, object storage, and map tiles enables zero-code profile switching. No `if profile` conditionals in application code.
**Consequences:** Every external service dependency must have an env-var-configurable endpoint. Profile B adds Ollama, tileserver-gl, and Postfix containers. Profile B can operate fully air-gapped (WhatsApp disabled gracefully).

---

### ADR-04: CAP-Only Alert Format (No Proprietary)

**Status:** Accepted
**Date:** 2026-03-05 (BQ-09)
**Context:** International interoperability requires standardized alert formats. Some EWS implementations use proprietary formats.
**Decision:** CAP v1.2 (OASIS) is the sole alert format. No proprietary alert schema.
**Rationale:** CAP v1.2 is the WMO-endorsed standard for all-hazards alert exchange. Using a single standard simplifies validation, export, and third-party integration. The CAP feed endpoint (IC-07) enables downstream systems to consume alerts without custom adapters.
**XSD profile:** Pluggable via `CAP_PROFILE_XSD` env var. Defaults to bundled CAP v1.2 base schema. WMO profile XSD shipped as option. IPAWS removed entirely (BQ-09).
**Consequences:** All alert content must conform to CAP v1.2 structure. Multi-language support via multiple `<info>` blocks per `<alert>`.

---

### ADR-05: Pluggable LLM Provider via Environment Variable

**Status:** Accepted
**Date:** 2026-03-05 (BQ-06, BQ-15)
**Context:** Profile A clients use cloud LLM (Anthropic Claude); Profile B clients require local inference (data sovereignty).
**Decision:** LLM adapter pattern with `LLM_BACKEND` env var. Supported backends: `anthropic` (Claude API), `ollama` (self-hosted).
**Rationale:** Both backends expose a compatible HTTP API (messages endpoint). The adapter normalizes request/response formats. LLM failure is non-blocking -- operators can author alert narratives manually.
**Recommended on-premise models:** `mistral:7b` (strong multilingual/French), `llama3.1:8b` (general quality), `llama3.2:3b` (constrained hardware).
**Consequences:** NFR-PERF-LLM-01 (30s SLA) applies to both backends. Profile B on CPU-only hardware targets `llama3.1:8b` for this SLA. Every LLM call is logged in `LLMOutput` with model ID, version, prompt hash, and token count for reproducibility and audit.

---

### ADR-06: MapLibre GL JS + Environment-Variable Tile Source

**Status:** Accepted
**Date:** 2026-03-05 (BQ-16)
**Context:** Map visualization is required in M3 (monitoring dashboard), M5 (impact maps), M6 (CAP area picker), and M7 (public alert portal).
**Decision:** MapLibre GL JS as the rendering library. Tile source configured via `MAP_TILE_URL` env var.
**Profile A:** OpenFreeMap / Protomaps hosted vector tiles (no API key, CDN-backed).
**Profile B:** Self-hosted `tileserver-gl` container with country `.mbtiles` extract from Geofabrik.
**Consequences:** No code change between profiles for map rendering. CAP area definition in Phase A uses administrative boundary selection only (no custom polygon drawing -- deferred post-Phase A).

---

### ADR-07: Celery for Model Execution (CPU-Only Phase A)

**Status:** Accepted
**Date:** 2026-03-05 (BQ-17)
**Context:** Forecast and impact models must execute within the system. Options: HPC cluster, Docker-in-Docker, Celery workers.
**Decision:** Models run as Python functions inside `celery-model` queue workers. CPU-only for Phase A. No HPC or GPU required.
**Rationale:** Celery provides task queuing, retry logic, result tracking, and monitoring (Flower) with no additional infrastructure. Models are registered in a `ModelRegistry` (dict of `model_id` to callable). Adding a new model = adding a Python module + registry entry.
**Execution patterns:** Scheduled batch (triggered after NWP ingestion via `chain()`) and on-demand (operator triggers via API).
**Upgrade path:** GPU workers added by deploying `celery-model` container on a GPU-equipped host. Kubernetes HPA for autoscaling model workers.
**Consequences:** `celery-model` queue is isolated from `celery-default` and `celery-ingestion`. Model run SLA: 120s for country-scale impact, 30s for district-scale.

---

### ADR-08: 99.9% SLA with Active-Passive HA

**Status:** Accepted
**Date:** 2026-03-05 (BQ-01)
**Context:** An MHEWS is a life-safety system. SLA must balance availability guarantees with infrastructure cost.
**Decision:** 99.9% availability (8.76 hours unplanned downtime/year) for all modules. Active-passive architecture (single region).
**Architecture:**
- Primary API instance + warm standby behind load balancer (failover: 20--30s)
- PostgreSQL: streaming replication to replica, auto-failover via Patroni (RTO: 1--3 min, RPO: ~0 with sync replication)
- Redis Sentinel mode (3 nodes, failover: 30s)
**Cost:** ~2x single-instance baseline.
**Documented upgrade path:** Tiered SLA (M6/M7/M10 at 99.99% via active-active multi-region) is documented in `consolidation/08_sla_tiering_analysis.md` but not contracted. Upgrade trigger: client request or post-PoC review identifying life-safety incidents attributable to downtime.
**Consequences:** Single-region deployment for Phase 1. RTO target: 3 minutes. RPO target: 1 minute.

---

### ADR-09: Community Feedback in Scope (Public Web Form)

**Status:** Accepted
**Date:** 2026-03-05 (BQ-10)
**Context:** WMO Pillar 4 and multiple legacy requirements call for community-sourced hazard observations. This was initially ambiguous in the PRD.
**Decision:** Public-facing web form for anonymous geo-tagged hazard reports. Moderated by Admin/Moderator before publication. AI-categorized by LLM service.
**Rationale:** Community reports provide ground truth that complements instrument-based observations. Moderation gate prevents misinformation. AI categorization reduces operator workload.
**Data model:** `CommunityReport` with location (PostGIS Point), description, photo upload (MinIO), AI-assigned hazard category and confidence score, moderation status.
**Consequences:** New public endpoint (no auth, rate-limited). LLM categorization task in `celery-default` queue. Cluster summarization every 2 hours via Celery Beat. Approved reports linkable to Incident records (M8).

---

### ADR-10: Tri-Lingual i18n via django-modeltranslation

**Status:** Accepted
**Date:** 2026-03-05 (BQ-07, BQ-12)
**Context:** Deployments require minimum three simultaneous active locales: French, English, and one deployment-specific language.
**Decision:** Django `gettext` (PO/MO files) for static strings. `django-modeltranslation` for model field content (hazard type names, area descriptions, CAP templates). Third locale set via `MHEWS_LOCALE_EXTRA` env var.
**Rationale:** Django's built-in i18n framework is mature and well-supported. `django-modeltranslation` transparently adds translatable fields to models without changing queries. Adding a new locale requires only translation files, not code changes.
**Consequences:** All user-facing strings wrapped in `_()` from Sprint 1. Per-contact language dispatch: email and WhatsApp templates selected by `Contact.preferred_language`. CAP `<info>` blocks generated per active locale.

---

## 5. Quality Attributes

### 5.1 Performance

| NFR ID | Metric | Target | Architecture Mechanism |
|---|---|---|---|
| NFR-PERF-01 | API response time (95th percentile) | 500ms | Gunicorn 4 workers x 2 threads, Redis caching of threshold configs, PostGIS spatial indexes |
| NFR-PERF-02 | Ingest-to-threshold latency | 60s | Celery Beat 60s polling, threshold evaluation inline with ingestion task |
| NFR-PERF-03 | Dispatch completion (10K recipients) | 10 min | Celery dispatch workers, batch chunking (500/batch), parallel email + WhatsApp jobs |
| NFR-PERF-LLM-01 | AI narrative generation | 30s end-to-end | Dedicated Celery task, prompt+response within single LLM call, timeout with fallback |
| NFR-PERF-AUD-01 | Audit report generation (30-day window) | 60s | Indexed `created_at` on AuditEntry, pre-filtered queries |
| NFR-PERF-BATCH-01 | Batch isolation | No degradation of real-time pipeline | Dedicated `celery-model` queue, separate worker pool |
| NFR-PERF-FCST-01 | Forecast ingestion pipeline | 30 min after NWP run | Celery chain: ingest > parse > store, parallelized per product |
| NFR-PERF-IMP-01 | Impact query (pre-computed) | 10s | PostGIS materialized views, spatial indexes |
| NFR-PERF-IMP-02 | Impact computation (on-demand, large area) | 120s | `celery-model` queue, spatial join against population raster |

### 5.2 Scalability

| Dimension | Baseline | Upgrade Trigger | Upgrade Path |
|---|---|---|---|
| Concurrent operators | 50 | >40 sustained | Add second API instance (active-active, 1 day) |
| Data sources | 100 @ 60s polling | >80 sources or <60s needed | Scale ingestion workers from 2 to 8 |
| Recipients per alert | 10,000 (email + WA) | >7,000 contacts | WhatsApp tier upgrade + SendGrid plan upgrade |
| DB connections | 50 (PgBouncer) | Scaled with operator count | Increase pool to 100 (config change) |
| Object storage | 500 GB (MinIO) | Volume growth | Expand MinIO volume (zero downtime) |

All upgrade paths are documented in `consolidation/09_capacity_baseline.md` with specific steps and cost estimates.

### 5.3 Availability

| Mechanism | Implementation |
|---|---|
| Active-passive API | Primary + warm standby behind load balancer; health check every 10s |
| Database replication | PostgreSQL streaming replication; Patroni for automatic failover |
| Redis Sentinel | 3-node Sentinel for broker/cache HA |
| Container auto-restart | Docker `restart: unless-stopped` policy on all services |
| Health endpoints | `GET /health/` (liveness), `GET /ready/` (readiness, checks DB + Redis + MinIO) |
| Graceful degradation | LLM unavailable: operator authors manually. WhatsApp disabled: email + portal remain active. Single data source failure: DEGRADED status after 3 failures, other sources unaffected. |

**RTO:** 3 minutes. **RPO:** 1 minute (synchronous PostgreSQL replication).

### 5.4 Security

Defense-in-depth layers:

| Layer | Mechanism |
|---|---|
| Perimeter | TLS termination, rate limiting (`django-ratelimit`), CORS policy |
| Authentication | JWT with short-lived access tokens (15 min), refresh rotation |
| Authorization | RBAC (Django Groups) + ABAC (django-guardian object-level) |
| Business rules | Dual-authorization for alert approval, emergency bypass with audit |
| Data protection | TLS in transit, AES-256 at rest, field-level PII encryption, LLM PII masking |
| Audit | Immutable append-only log, evidence packages, DB-level write protection |
| Dependency | `pip-audit` in CI, Docker image scanning, pinned dependency versions |

### 5.5 Maintainability

| Mechanism | Implementation |
|---|---|
| Modular architecture | One Django app per PRD module; cross-app coupling via signals or REST calls only |
| 12-factor configuration | All config from environment variables; no hardcoded secrets, URLs, or org names |
| CI/CD pipeline | GitHub Actions: Docker build, `ruff` lint, `pytest-django` test suite, `pip-audit` security check |
| Schema management | Django migrations under version control; `makemigrations --check` in CI prevents drift |
| API documentation | `drf-spectacular` auto-generates OpenAPI 3 schema from DRF serializers |
| Configuration management | IEEE 828 compliant CM plan (`consolidation/12_cm_plan.md`): ID immutability, change control process, baseline tagging |

---

## 6. Interface Summary

### 6.1 Internal Module Contracts

Seven internal interface contracts are defined in `consolidation/04_interface_contracts.md`:

| Contract | Direction | Mechanism | Summary |
|---|---|---|---|
| **IC-01** | M3 > M6 | Django signal `threshold.breached` | Breach notification to operator dashboard. No auto-draft creation (BQ-04). Operator manually initiates CAP draft. |
| **IC-02** | M6 > M7 | Django signal `cap.approved` | Triggers dispatch orchestration. Creates DispatchJob per channel, DispatchReceipt per contact. |
| **IC-03** | M7 > M9 | Synchronous audit logger | Dispatch lifecycle events: QUEUED, STARTED, SENT, DELIVERED, FAILED per receipt. |
| **IC-04** | M3 > M9 | Synchronous audit logger | Ingestion events: BATCH_COMPLETE, SOURCE_FAILED, BREACH_DETECTED, STATUS_CHANGED. |
| **IC-05** | M6 > M4 | REST GET (post-PoC) | Forecast context request for CAP enrichment: hazard, location, lead time, confidence. |
| **IC-06** | M8 > M2 | Django signal `after_action.completed` (post-PoC) | After-action calibration signal. M2 logs for analyst review; no automatic threshold modification. |
| **IC-07** | M6 > External | REST GET `/api/v1/cap/feed/` | Atom feed of published CAP alerts. Public API key auth. Immutable entries. Updated within 30s of approval. |

### 6.2 External Interfaces

| Interface | Protocol | Direction | Auth | Notes |
|---|---|---|---|---|
| **GDACS GeoRSS** | HTTPS GET | Inbound (poll) | None (public) | 60s polling. Failure: DEGRADED after 3 consecutive. |
| **ECMWF CDS API** | HTTPS + API key | Inbound (poll) | CDS API key | Post-PoC. Mocked locally during PoC. |
| **SendGrid Email** | HTTPS POST | Outbound (dispatch) | Bearer token | Delivery receipt via signed webhook. |
| **Meta WhatsApp** | HTTPS POST | Outbound (dispatch) | Bearer token | Delivery receipt via HMAC-SHA256 webhook. Mocked in PoC. |
| **Anthropic Claude** | HTTPS POST | Outbound (LLM) | API key | Profile A only. Profile B uses Ollama (local). |
| **CAP Feed** | HTTPS GET (Atom) | Outbound (publish) | API key (read-only) | Third-party consumers subscribe to published alerts. |
| **WhatsApp STOP** | HTTPS POST | Inbound (webhook) | HMAC verification | Contact opt-out processing. |
| **SendGrid Webhook** | HTTPS POST | Inbound (webhook) | HMAC-SHA256 | Delivery status callbacks (delivered, bounced, etc.). |

### 6.3 Connector Registry (M11)

External data feeds are implemented as named, configurable connectors. Each connector is activated by environment variables:

```
CONNECTOR_<NAME>_ENABLED=true/false
CONNECTOR_<NAME>_URL=...
CONNECTOR_<NAME>_API_KEY=...
```

| Connector | Feed | Default State |
|---|---|---|
| `gdacs` | GDACS GeoRSS | Enabled (public) |
| `ecmwf_cds` | ECMWF / CDS API | Disabled |
| `glofas` | GLOFAS Copernicus CEMS | Disabled |
| `rsmc_cyclone` | RSMC cyclone track | Disabled |
| `nmhs_sensor` | National NMHS sensor network | Disabled |
| `copernicus_cdse` | Copernicus Sentinel imagery | Disabled |
| `national_ogc` | National OGC WMS/WFS | Disabled |

Disabled connectors are skipped silently by the ingestion pipeline.

---

## 7. Constraints and Assumptions

### 7.1 Hard Constraints (from Section II Design Document)

| ID | Constraint | Impact |
|---|---|---|
| C-01 | Digital/software-only scope | No siren hardware, no institutional governance processes |
| C-02 | Hazard-agnostic, model-driven | No hardcoded hazard-specific logic; all hazard behaviour driven by configuration |
| C-03 | Containerized (Docker) | All services run as Docker containers. Orchestration-agnostic (Compose baseline, K8s upgrade path) |
| C-04 | OGC standards: consume only | System consumes OGC WMS/WFS feeds. No OGC serving (no GeoServer, no tile serving beyond read-only map display) |
| C-05 | CAP: authoring + validation + export only | No CAP hub ingest. System produces CAP, does not consume third-party CAP. |
| C-06 | Dissemination: Email + Web Portal + WhatsApp only | SMS, cell-broadcast, mobile push are out of scope (BQ-19: formal risk acceptance required) |
| C-07 | No external identity federation | No SAML, no OIDC with external providers. All users managed in Django's auth system. |
| C-08 | Hybrid RBAC/ABAC | Role-based via Django Groups, object-level via django-guardian. No external policy engine. |
| C-09 | Immutable structured audit | Full audit framework with append-only evidence. No audit record modification. |

### 7.2 Assumptions

| ID | Assumption | Risk if Invalid |
|---|---|---|
| A-01 | GDACS GeoRSS remains publicly accessible | Primary data source for PoC; mock fallback exists |
| A-02 | SendGrid or equivalent email provider available for Profile A | Email dispatch blocked; Postfix fallback |
| A-03 | Anthropic Claude API available for Profile A LLM | Ollama fallback; manual narrative authoring |
| A-04 | Meta WhatsApp Business verification completed by client before live dispatch | Mock covers PoC; production WhatsApp blocked until verification |
| A-05 | Single-country deployment for Phase 1 | Multi-tenant architecture not required |
| A-06 | 50 concurrent operator sessions is peak capacity | Performance targets and pool sizing based on this |
| A-07 | CPU-only servers available for Phase A model execution | GPU not required; model complexity constrained accordingly |
| A-08 | Client provides administrative boundary data (government profile) or GADM public data is acceptable (non-government profile) | PostGIS boundary data required for area-based alerts |
| A-09 | Python 3.12 runtime environment available on all deployment targets | Docker containers ensure this |
| A-10 | Client accepts English and French as base languages with one additional locale per deployment | i18n framework designed for exactly this |

### 7.3 Key Trade-offs

| Trade-off | Decision | Rationale |
|---|---|---|
| Monolith vs. microservices | Monolith (Django) | Small team, shared database, tightly coupled alert pipeline. Microservices add operational complexity with no Phase 1 benefit. Upgrade path: extract M6/M7/M10 if tiered SLA requires independent scaling. |
| Polling vs. streaming | Polling (Celery Beat) | 100 sources at 60s is well within Celery capacity. Kafka/NATS adds operational burden. Upgrade path: D2 (streaming backbone) when sub-10s latency required. |
| Cloud LLM vs. local LLM | Both (adapter pattern) | Cloud (Claude) offers higher quality; local (Ollama) offers data sovereignty. Adapter pattern makes both first-class options. |
| Single-tenant vs. multi-tenant | Single-tenant | Phase 1 has one tenant. Multi-tenant code increases complexity. Coding conventions preserve upgrade path. |
| Active-passive vs. active-active | Active-passive | 99.9% SLA achievable at 2x cost. Active-active (99.95%--99.99%) costs 2.5--6x. Documented as upgrade path. |

---

## Appendix A: Technology Stack Summary

| Layer | Technology | Version |
|---|---|---|
| Language | Python | 3.12 |
| Backend framework | Django + DRF | 5.x |
| Frontend framework | React + TypeScript | 18 |
| Build tool | Vite | Latest |
| CSS | Tailwind CSS | 3.x |
| Map rendering | MapLibre GL JS | Latest |
| Database | PostgreSQL + PostGIS | 16 + 3.4 |
| Task queue | Celery | 5 |
| Message broker / cache | Redis | 7 |
| Object storage | MinIO (on-prem) / S3 (cloud) | Latest |
| Auth tokens | djangorestframework-simplejwt | Latest |
| Object permissions | django-guardian | Latest |
| Model translation | django-modeltranslation | Latest |
| Model history | django-simple-history | Latest |
| CAP validation | lxml + bundled XSD | Latest |
| API documentation | drf-spectacular (OpenAPI 3) | Latest |
| LLM (cloud) | Anthropic Claude API | claude-haiku-4-5 / claude-sonnet-4-6 |
| LLM (on-premise) | Ollama | Latest |
| Containerization | Docker + Docker Compose | Latest |
| CI/CD | GitHub Actions | N/A |
| Linting | ruff | Latest |
| Testing | pytest-django | Latest |

## Appendix B: Diagram Cross-Reference

| Diagram | Location | Viewpoint |
|---|---|---|
| Use Case Diagram | `consolidation/06_plantuml_appendix.md` Diagram 1 | Functional |
| Component Diagram | `consolidation/06_plantuml_appendix.md` Diagram 2 | Functional |
| Alert Pipeline Sequence | `consolidation/06_plantuml_appendix.md` Diagram 3 | Information / Concurrency |
| Data Ingestion Sequence | `consolidation/06_plantuml_appendix.md` Diagram 4 | Information / Concurrency |
| AI Narrative Sequence | `consolidation/06_plantuml_appendix.md` Diagram 5 | Information |
| CAP State Machine Activity | `consolidation/06_plantuml_appendix.md` Diagram 6 | Information |
| Deployment Diagram: Profile A | `consolidation/10_deployment_profiles.md` | Deployment |
| Deployment Diagram: Profile B | `consolidation/10_deployment_profiles.md` | Deployment |

## Appendix C: Glossary

| Term | Definition |
|---|---|
| ABAC | Attribute-Based Access Control |
| CAP | Common Alerting Protocol (OASIS CAP v1.2) |
| CCB | Change Control Board |
| GDACS | Global Disaster Alerting Coordination System |
| HITL | Human-in-the-Loop |
| LLM | Large Language Model |
| MHEWS | Multi-Hazard Early Warning System |
| NMHS | National Meteorological and Hydrological Service |
| NWP | Numerical Weather Prediction |
| OGC | Open Geospatial Consortium |
| PII | Personally Identifiable Information |
| RBAC | Role-Based Access Control |
| RPO | Recovery Point Objective |
| RTO | Recovery Time Objective |
| SLA | Service Level Agreement |
| SSE | Server-Sent Events |
| WMO | World Meteorological Organization |
| XSD | XML Schema Definition |
