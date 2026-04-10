# MHEWS Software Design Description (SDD)
**Standard:** IEEE 1016-2009
**Version:** 1.0
**Date:** 2026-03-06
**Status:** Baseline Draft

---

## 1. Introduction

### 1.1 Purpose

This Software Design Description (SDD) specifies the software design of the Multi-Hazard Early Warning System (MHEWS). It conforms to the structure and requirements of IEEE 1016-2009 and serves as the bridge between the Architecture Description (IEEE 42010) and implementation. This document provides sufficient detail for developers to understand module boundaries, data entities, interface contracts, component interactions, error handling patterns, and security mechanisms.

### 1.2 Scope

The MHEWS is a digital, software-only platform supporting the end-to-end early warning pipeline: hazard monitoring, threshold evaluation, CAP-format alert authoring, multi-channel dissemination, incident tracking, and structured audit. The system comprises 12 modules mapped to Django applications, a shared core library, and a WhatsApp mock application for development.

**In scope:** Design of all 12 software modules, their data models, internal and external interfaces, component interactions, error handling, and security mechanisms.

**Out of scope:** Physical infrastructure (sirens, sensors), institutional governance, SMS/cell-broadcast/mobile push channels, external identity federation, and GIS tile serving (consume-only by design).

### 1.3 Definitions, Acronyms, and Abbreviations

| Term | Definition |
|---|---|
| ABAC | Attribute-Based Access Control |
| ADR | Architecture Decision Record |
| CAP | Common Alerting Protocol (OASIS CAP v1.2) |
| DRF | Django REST Framework |
| HITL | Human-in-the-Loop |
| IC | Interface Contract |
| JWT | JSON Web Token |
| LLM | Large Language Model |
| MHEWS | Multi-Hazard Early Warning System |
| NMHS | National Meteorological and Hydrological Service |
| OGC | Open Geospatial Consortium |
| PII | Personally Identifiable Information |
| RBAC | Role-Based Access Control |
| RPO | Recovery Point Objective |
| RTO | Recovery Time Objective |
| SLA | Service Level Agreement |
| SSE | Server-Sent Events |
| STM | State Machine |
| WMO | World Meteorological Organization |
| XSD | XML Schema Definition |

### 1.4 References

| Document | Location | Standard |
|---|---|---|
| Architecture Description | `consolidation/17_architecture_description.md` | IEEE 42010:2022 |
| Interface Contracts | `consolidation/04_interface_contracts.md` | -- |
| Functional Completeness | `consolidation/02_completeness_functional.md` | -- |
| Module-Feature Trace | `consolidation/13_module_feature_trace.md` | -- |
| Sprint Plan | `poc_sprint_plan.md` | -- |
| Deployment Profiles | `consolidation/10_deployment_profiles.md` | -- |
| SLA Tiering Analysis | `consolidation/08_sla_tiering_analysis.md` | -- |
| Capacity Baseline | `consolidation/09_capacity_baseline.md` | -- |
| Configuration Management Plan | `consolidation/12_cm_plan.md` | IEEE 828 |
| PlantUML Diagrams | `consolidation/06_plantuml_appendix.md` | -- |
| PRD (Section III Annex) | `docs/mhewsprd.md` | -- |
| Design Document (Section II) | `docs/SECTION II -- Client Guidance and Software Design Document.docx` | -- |

---

## 2. Design Overview

### 2.1 System Context

MHEWS operates within an ecosystem of external data providers (GDACS, ECMWF), communication services (SendGrid, Meta WhatsApp), LLM providers (Anthropic Claude, Ollama), and downstream CAP consumers. Operators interact through a React dashboard; the public receives alerts via email, WhatsApp, and a web portal.

The system follows the WMO four-pillar early warning framework:
1. **Disaster Risk Knowledge** -- Hazard taxonomy, risk scenarios, exposure data (M1, M2, M5)
2. **Detection, Monitoring, Analysis & Forecasting** -- Data ingestion, threshold evaluation, forecast models (M3, M4)
3. **Warning Dissemination & Communication** -- CAP authoring, multi-channel dispatch, public portal (M6, M7)
4. **Preparedness & Response Capabilities** -- Incident tracking, drill mode, after-action learning (M8, M12)

### 2.2 Design Methodology

The design follows these principles:

- **Domain-Driven Design:** Each PRD module maps to a single Django application with its own models, serializers, views, URLs, tasks, and admin configuration.
- **Event-Driven Decoupling:** Modules communicate via Django signals and Celery tasks, not direct model imports. No circular dependencies exist.
- **12-Factor Configuration:** All environment-specific values come from environment variables. No hardcoded secrets, URLs, or organization names.
- **Adapter Pattern:** External services (LLM, email, WhatsApp, object storage, map tiles) are swappable via environment variables without code changes.
- **Human-in-the-Loop:** All critical decisions (alert creation, approval, threshold changes) require explicit operator action. No fully automated alert dispatch.

### 2.3 Technology Stack Summary

| Layer | Technology | Version | Rationale |
|---|---|---|---|
| Language | Python | 3.12 | Runtime standardized via Docker |
| Backend Framework | Django + DRF | 5.x | Batteries for auth, ORM, admin, signals, Celery |
| Frontend Framework | React + TypeScript | 18 | Dashboard-heavy UI with Vite build tooling |
| CSS Framework | Tailwind CSS | 3.x | Utility-first styling |
| Map Rendering | MapLibre GL JS | Latest | Open-source, tile-source agnostic |
| Database | PostgreSQL + PostGIS | 16 + 3.4 | Geospatial queries, `postgis/postgis:16-3.4` image |
| Task Queue | Celery | 5 | Ingestion workers, dispatch, scheduled reports |
| Message Broker / Cache | Redis | 7 | Celery broker + application cache + SSE channel |
| Object Storage | MinIO (on-prem) / S3 (cloud) | Latest | S3-compatible; no cloud dependency for on-prem |
| Auth Tokens | djangorestframework-simplejwt | Latest | JWT access + refresh tokens |
| Object Permissions | django-guardian | Latest | ABAC groundwork for object-level permissions |
| Model Translation | django-modeltranslation | Latest | Tri-lingual model content |
| Model History | django-simple-history | Latest | Immutable change trail for audit |
| CAP Validation | lxml + bundled XSD | Latest | Offline schema validation |
| API Documentation | drf-spectacular (OpenAPI 3) | Latest | Auto-generated from DRF serializers |
| LLM (Cloud) | Anthropic Claude API | claude-haiku-4-5 / claude-sonnet-4-6 | AI narrative + chatbot |
| LLM (On-Premise) | Ollama | Latest | Data sovereignty; local inference |
| Containerization | Docker + Docker Compose | Latest | Per Section II mandate |
| CI/CD | GitHub Actions | N/A | Docker build + lint + test gates |
| Linting | ruff | Latest | Fast Python linter |
| Testing | pytest-django | Latest | Database rollback per test |

### 2.4 Project Structure

```
backend/
+-- config/
|   +-- settings/
|   |   +-- base.py
|   |   +-- development.py
|   |   +-- production.py
|   +-- urls.py
|   +-- celery.py
+-- apps/
    +-- core/             # Shared base models, mixins, permission helpers
    +-- accounts/         # M10 -- User management, RBAC, JWT
    +-- hazards/          # M1  -- Hazard types, thresholds, admin boundaries
    +-- ingestion/        # M3  -- Data sources, observations, breach events
    +-- forecasting/      # M4  -- Forecast models, model runs (stub in PoC)
    +-- impact/           # M5  -- Exposure data, impact layers (stub in PoC)
    +-- alerts/           # M6  -- CAP templates, drafts, validation, packages
    +-- dissemination/    # M7  -- Contacts, dispatch, channel adapters, portal
    +-- mock_whatsapp/    # Internal mock of Meta Cloud API
    +-- incidents/        # M8  -- Incident lifecycle, after-action (stub in PoC)
    +-- audit/            # M9  -- Immutable audit events
    +-- gateway/          # M11 -- API versioning, rate limiting (stub in PoC)
    +-- preparedness/     # M12 -- Drill mode, SOP repository (post-PoC)
    +-- risk/             # M2  -- Scenarios, calibration (post-PoC)
```

---

## 3. Design Entities (by Module)

### 3.1 Core (`core`)

**Purpose:** Provides shared base classes, mixins, and utility functions used across all modules. Not a PRD module -- it is infrastructure code.

**Key Models:**
- `TimestampedModel` (abstract) -- Base model with `created_at` (auto_now_add) and `updated_at` (auto_now). All module models inherit from this.
- `AuditableMixin` (abstract) -- Adds `created_by` FK to User. Used by models that require actor tracking.

**Key Utilities:**
- Permission helper functions for RBAC/ABAC checks
- Structured error response builder
- Request ID middleware (generates/propagates `X-Request-ID` per MHEWS-FC-ERR-02)

**Dependencies:** None (leaf node; depended upon by all other apps).

---

### 3.2 Module M10 -- Administration & Access Control (`accounts`)

**Purpose:** Manages user identity, authentication (JWT), role-based access control via Django Groups, and object-level permission groundwork via django-guardian.

**Key Models:**
- `CustomUser` -- Extends `AbstractBaseUser`. Fields: username, email, first_name, last_name, is_active, preferred_language, date_joined. No external federation.
- Django `Group` -- Pre-configured groups: `operator`, `approver`, `admin`, `auditor`.
- `SystemSetting` -- Key-value store for tenant-level configuration (e.g., `MHEWS_CAP_SENDER_URI`).

**Key Views/Endpoints:**
- `POST /api/auth/token/` -- JWT issuance (simplejwt `TokenObtainPairView`)
- `POST /api/auth/token/refresh/` -- JWT refresh (simplejwt `TokenRefreshView`)
- `UserViewSet` -- CRUD for user management (Admin only)
- `GET /api/auth/me/` -- Current user profile and permissions
- `POST /api/auth/change-password/` -- Password change with policy enforcement (MHEWS-FC-INV-08)

**State Machines:** None.

**Features (from trace table):**
- TST-M10-001: Authentication (JWT)
- TST-M10-002: User Management
- TST-M10-003: RBAC Enforcement
- TST-M10-004: User Activity View
- TST-M10-005: System Settings

**Dependencies:** `core`.

---

### 3.3 Module M1 -- Hazard & Taxonomy Management (`hazards`)

**Purpose:** Maintains the registry of hazard types, associated threshold configurations, and administrative boundary geometries. This is reference data that drives the behaviour of all downstream modules.

**Key Models:**
- `HazardType` -- Fields: code (unique, e.g. FLOOD), name, description, icon, is_active, default_list_id (FK to ContactGroup, nullable). Translatable fields: name, description.
- `ThresholdConfig` -- Fields: hazard_type (FK), parameter_name, operator (GT/GTE/LT/LTE/EQ), value (float), unit, is_active. Constraint: one active threshold per hazard_type + parameter_name pair (MHEWS-FC-INV-05).
- `AdminBoundary` -- Fields: name, level (country/region/district), code (ISO 3166-2), geom (PostGIS MultiPolygon), parent (self-FK). Imported from Shapefile/GADM.

**Key Views/Endpoints:**
- `HazardTypeViewSet` -- CRUD. Filterable by is_active. Admin role for write operations.
- `ThresholdConfigViewSet` -- CRUD nested under hazard type. Validation per MHEWS-FC-INV-05.
- `AdminBoundaryViewSet` -- List/retrieve with GeoJSON serialization. Bulk import via management command.

**State Machines:** None.

**Features:**
- TST-M1-001: Hazard Type Registry
- TST-M1-002: Threshold Management
- TST-M1-003: Area Registry (Admin Boundaries)

**Dependencies:** `core`.

---

### 3.4 Module M3 -- Data Ingestion & Monitoring (`ingestion`)

**Purpose:** Manages external data source registration, scheduled polling, observation storage, threshold evaluation, and breach event lifecycle. This is the entry point of the alert pipeline.

**Key Models:**
- `DataSource` -- Fields: name, source_type (GDACS_GEORSS/ECMWF_CDS/MOCK/CUSTOM), url, poll_interval_seconds, auth_config (JSONField, encrypted), hazard_type (FK), status (STM-05 states), last_fetch_status, last_fetch_at, consecutive_failures.
- `ObservationRecord` -- Fields: data_source (FK), hazard_type (FK), parameter_name, value (float), unit, location (PostGIS Point), observed_at, raw_payload (JSONField), batch_id (UUID).
- `ThresholdBreachEvent` -- Fields: observation (FK), threshold_config (FK), observed_value, threshold_value, breach_magnitude, status (STM-03 states), acknowledged_at, acknowledged_by (FK User), cap_draft_id (FK, unique, nullable), dismissal_note, resolved_at.

**Key Views/Endpoints:**
- `DataSourceViewSet` -- CRUD with URL validation (MHEWS-FC-INV-09). Admin role for write.
- `ObservationRecordViewSet` -- List/retrieve. Filterable by hazard_type, data_source, date range.
- `ThresholdBreachEventViewSet` -- List/retrieve. Actions: `acknowledge`, `dismiss` (requires note).
- `GET /api/ingestion/health/` -- Source health dashboard data (status, last_fetch, failure count).

**State Machines:**
- **STM-03** (`ThresholdBreachEvent`): OPEN > ACKNOWLEDGED > CAP_DRAFTED > RESOLVED | DISMISSED. See `consolidation/02_completeness_functional.md`.
- **STM-05** (`DataSource`): ACTIVE > PAUSED | DEGRADED | INACTIVE. DEGRADED set after 3 consecutive failures (MHEWS-FC-ERR-06).

**Celery Tasks:**
- `poll_data_source(source_id)` -- Fetches data, parses, persists ObservationRecords, evaluates thresholds, emits `threshold_breached` signal on breach. Scheduled via Celery Beat per source's `poll_interval_seconds`.
- `check_breach_resolution()` -- Celery Beat (every 5 min). Re-evaluates OPEN/ACKNOWLEDGED breaches; transitions to RESOLVED if sensor value returned below threshold.

**Dependencies:** `core`, `hazards` (M1, for HazardType and ThresholdConfig lookups).

---

### 3.5 Module M2 -- Risk & Scenario Modelling (`risk`)

**Purpose:** Enables analysts to define what-if scenarios combining hazard types, geographic areas, and model configurations. Receives after-action calibration signals from M8 for threshold performance review. Post-PoC module.

**Key Models:**
- `RiskScenario` -- Fields: name, description, hazard_type (FK), area (FK AdminBoundary), model_config (JSONField), created_by (FK User), status (DRAFT/RUNNING/COMPLETED/FAILED).
- `ScenarioResult` -- Fields: scenario (FK), output_summary (JSONField), output_file_key (MinIO/S3 key), computed_at.
- `ThresholdCalibrationLog` -- Fields: incident_id (UUID, unique), hazard_type (FK), breach_threshold (FK), actual_impact_level, false_positive (boolean), operator_notes, completed_at.

**Key Views/Endpoints:**
- `RiskScenarioViewSet` -- CRUD with scenario execution trigger.
- `ScenarioResultViewSet` -- List/retrieve (read-only).
- `ThresholdCalibrationLogViewSet` -- List for analyst dashboard (read-only).

**State Machines:** None (RiskScenario has a simple status field, not a formal STM).

**Features:**
- TST-M2-001: Scenario Builder
- TST-M2-002: Scenario Execution Engine
- TST-M2-003: Scenario Results Viewer
- TST-M2-004: Scenario-to-CAP Linkage

**Dependencies:** `core`, `hazards` (M1), `incidents` (M8, via IC-06 signal consumer).

---

### 3.6 Module M4 -- Forecasting & Nowcasting Engine (`forecasting`)

**Purpose:** Manages forecast model registration, scheduled and on-demand execution, and model run lifecycle. Post-PoC module (stub in Sprint 1).

**Key Models:**
- `ForecastModel` -- Fields: name, model_type (STATISTICAL/ML/NWP_POST), hazard_scope (M2M HazardType), execution_config (JSONField), is_active, created_by (FK User).
- `ModelRun` -- Fields: model (FK), status (STM-06 states), started_at, completed_at, output_key (MinIO/S3 key), error_message, triggered_by (FK User, nullable for scheduled runs).

**Key Views/Endpoints:**
- `ForecastModelViewSet` -- CRUD. Admin role for write.
- `ModelRunViewSet` -- List/retrieve. Action: `trigger` (on-demand run). Action: `cancel` (RUNNING > CANCELLED).
- `GET /api/forecasts/context/` -- Forecast context for CAP enrichment (IC-05). Parameters: hazard_type, lat, lon, at.

**State Machines:**
- **STM-06** (`ModelRun`): QUEUED > RUNNING > COMPLETED | FAILED | CANCELLED. One RUNNING instance per model_id enforced at queue level. See `consolidation/02_completeness_functional.md`.

**Celery Tasks:**
- `execute_model_run(run_id)` -- Runs on `celery-model` queue. Executes registered model function, stores output to MinIO/S3. Triggers downstream forecast ingestion on COMPLETED.
- Scheduled model execution via Celery Beat (after NWP ingestion via `chain()`).

**Features:**
- TST-M4-001: Model Registry
- TST-M4-002: Manual Forecast Trigger
- TST-M4-003: Forecast Visualization
- TST-M4-004: Forecast Run Notifications
- TST-M4-005: Scheduled Model Execution

**Dependencies:** `core`, `hazards` (M1).

---

### 3.7 Module M5 -- Impact Analysis & Exposure Modelling (`impact`)

**Purpose:** Manages exposure datasets, pre-computed impact layers, and on-demand spatial impact queries. Provides qualitative impact narratives via LLM integration. Post-PoC module (stub in Sprint 1).

**Key Models:**
- `ExposureDataset` -- Fields: name, category (POPULATION/INFRASTRUCTURE/AGRICULTURE), area (FK AdminBoundary), data_source, file_key (MinIO/S3 key), imported_at.
- `ImpactLayer` -- Fields: hazard_type (FK), exposure_dataset (FK), area (FK AdminBoundary), impact_level (LOW/MEDIUM/HIGH/EXTREME), geometry (PostGIS), computed_at.
- `ImpactNarrative` -- Fields: hazard_type (FK), area (FK AdminBoundary), narrative_text, model_id, model_version, generated_at, status (STM-10 states as part of LLMOutput pattern).

**Key Views/Endpoints:**
- `ExposureDatasetViewSet` -- CRUD with file upload validation (MHEWS-FC-INV-03).
- `ImpactLayerViewSet` -- List/retrieve with GeoJSON output. Filterable by hazard_type, area.
- `GET /api/impact/query/` -- On-demand spatial impact computation (PostGIS join).

**State Machines:** LLMOutput state machine (STM-10) applies to impact narratives.

**Features:**
- TST-M5-001: Exposure Data Management
- TST-M5-002: Impact Computation Engine
- TST-M5-003: Impact Heatmap Viewer
- TST-M5-004: Qualitative Impact Narrator
- TST-M5-005: Impact-to-CAP Linkage

**Dependencies:** `core`, `hazards` (M1).

---

### 3.8 Module M6 -- Alert Authoring (CAP) (`alerts`)

**Purpose:** Manages the complete CAP alert lifecycle: template management, draft creation, field editing, AI narrative integration, XSD validation, dual-authorization approval, and immutable CAP package generation. This is the central module of the warning pipeline.

**Key Models:**
- `CAPTemplate` -- Fields: hazard_type (FK), name, headline_template, description_template, instruction_template, default_urgency, default_severity, default_certainty, is_active. Translatable fields: headline_template, description_template, instruction_template.
- `CAPDraft` -- Fields: template (FK), linked_breach_event (FK ThresholdBreachEvent, unique, nullable), status (STM-01 states), headline, description, instruction, urgency, severity, certainty, language, area (FK AdminBoundary), onset, expires_at, submitted_by (FK User), approved_by (FK User, nullable), approved_at, rejection_note, bypass_reason, distribution_list_ids (ArrayField UUID), channels (ArrayField).
- `ValidationResult` -- Fields: cap_draft (FK), is_valid (boolean), errors (JSONField), validated_at.
- `CAPPackage` -- Fields: cap_draft (OneToOne), xml_content (TextField), xml_object_key (MinIO/S3 key), xsd_version, sha256_hash, generated_at. Immutable after creation.
- `LLMOutput` -- Fields: cap_draft (FK, nullable), model_id, model_version, prompt_hash, output_text, token_count, latency_ms, status (STM-10 states), trace_id, created_at.

**Key Views/Endpoints:**
- `CAPTemplateViewSet` -- CRUD. Admin role for write.
- `CAPDraftViewSet` -- CRUD with state machine enforcement. Key actions:
  - `POST /api/alerts/drafts/` -- Create from template + optional breach event ID.
  - `PATCH /api/alerts/drafts/{id}/` -- Edit fields (only in DRAFT state).
  - `POST /api/alerts/drafts/{id}/validate/` -- Generate CAP XML via `lxml`, validate against bundled XSD, persist ValidationResult (MHEWS-FC-ERR-07).
  - `POST /api/alerts/drafts/{id}/submit/` -- Operator submits for approval (DRAFT > PENDING_APPROVAL). Guard: validation_result.is_valid = True.
  - `POST /api/alerts/drafts/{id}/approve/` -- Approver approves (PENDING_APPROVAL > APPROVED). Guard: approver != submitter (dual-auth). Emits `cap_approved` signal.
  - `POST /api/alerts/drafts/{id}/reject/` -- Approver rejects. Requires rejection_note.
  - `POST /api/alerts/drafts/{id}/cancel/` -- Cancel draft.
  - `POST /api/alerts/drafts/{id}/generate-narrative/` -- Trigger LLM narrative generation.
- `CAPPackageViewSet` -- Read-only. Retrieve by ID. Download as XML.
- `GET /api/v1/cap/feed/` -- Atom feed of published CAP alerts (IC-07). Public API key auth.

**State Machines:**
- **STM-01** (`CAPDraft`): DRAFT > PENDING_APPROVAL > APPROVED | REJECTED > DRAFT (re-edit) | CANCELLED | EXPIRED. APPROVED is terminal. Dual-auth enforced. Emergency bypass by Admin with 50+ char justification. See `consolidation/02_completeness_functional.md`.
- **STM-10** (`LLMOutput`): REQUESTED > GENERATED > ACCEPTED | REJECTED | EXPIRED. ACCEPTED outputs are immutable audit evidence.

**Celery Tasks:**
- `generate_llm_narrative(draft_id)` -- Calls LLM provider, stores output as LLMOutput. Async, non-blocking.
- `expire_drafts()` -- Celery Beat (every 5 min). Transitions DRAFT/PENDING_APPROVAL drafts past `expires_at` to EXPIRED.

**Features:**
- TST-M6-001: CAP Template Library
- TST-M6-002: CAP Editor UI
- TST-M6-003: AI Narrative Integration
- TST-M6-004: CAP Validation Service
- TST-M6-005: Approval Workflow (Dual-Auth)
- TST-M6-006: CAP Update/Cancel
- TST-M6-007: Multilingual Alert Authoring

**Dependencies:** `core`, `hazards` (M1), `ingestion` (M3, for ThresholdBreachEvent FK), `forecasting` (M4, via IC-05 REST call, post-PoC).

---

### 3.9 Module M7 -- Dissemination (`dissemination`)

**Purpose:** Manages the contact directory, dispatch orchestration, channel-specific adapters (email, WhatsApp), public web portal, community hazard reporting, and public self-registration.

**Key Models:**
- `Contact` -- Fields: name, email (encrypted post-PoC), phone_whatsapp (E.164, encrypted post-PoC), preferred_language, email_opted_in (boolean), whatsapp_opted_in (boolean), area_of_interest (FK AdminBoundary, nullable), is_active, drill_contact (boolean).
- `ContactGroup` -- Fields: name, description. M2M relationship with Contact.
- `DistributionList` -- Alias/view of ContactGroup scoped for alert dispatch.
- `DispatchJob` -- Fields: cap_package (FK), channel (EMAIL/WHATSAPP), status (STM-02 states), contact_count, started_at, completed_at, failure_reason. Unique constraint: (cap_package_id, channel).
- `DispatchReceipt` -- Fields: dispatch_job (FK), contact (FK), provider_message_id, status (STM-09 states), sent_at, delivered_at, failure_reason.
- `CommunityReport` -- Fields: location (PostGIS Point), description, photo_key (MinIO/S3 key), ai_hazard_category, ai_confidence_score, status (STM-07 states), moderated_by (FK User, nullable), rejection_reason, submitted_at.

**Key Views/Endpoints:**
- `ContactViewSet` -- CRUD. WhatsApp number validation (MHEWS-FC-INV-10). Admin role for write.
- `ContactGroupViewSet` -- CRUD with member management.
- `DispatchJobViewSet` -- List/retrieve (read-only). Action: `retry-failed` (POST, re-queues failed receipts per MHEWS-FC-ERR-08).
- `DispatchReceiptViewSet` -- List/retrieve (read-only). Filterable by job, status.
- `GET /api/portal/alerts/` -- Public (no auth). Returns active CAPPackage records as JSON for the web portal.
- `POST /api/portal/reports/` -- Public (no auth, rate-limited). Submit community hazard report.
- `CommunityReportViewSet` -- Moderation queue. Actions: `approve`, `reject` (requires rejection_reason).
- `POST /api/dissemination/webhooks/sendgrid/` -- SendGrid delivery receipt webhook (HMAC-SHA256 verified).
- `POST /api/dissemination/webhooks/whatsapp/` -- WhatsApp delivery receipt webhook (HMAC-SHA256 verified).
- `POST /api/portal/register/` -- Public self-registration for alert notifications.

**State Machines:**
- **STM-02** (`DispatchJob`): QUEUED > RUNNING > COMPLETED | FAILED. 15-minute dead-letter timeout.
- **STM-07** (`CommunityReport`): PENDING > APPROVED | REJECTED > ARCHIVED. Approved triggers cluster summarization.
- **STM-08** (Contact consent): OPTED_IN > OPTED_OUT > RE_OPTED_IN. Tracked independently per channel.
- **STM-09** (`DispatchReceipt`): QUEUED > SENT > DELIVERED | FAILED | BOUNCED. 24-hour no-callback timeout.

**Celery Tasks:**
- `dispatch_email_batch(job_id)` -- Sends email via SendGrid for all contacts in job. Updates DispatchReceipt per contact.
- `dispatch_whatsapp_batch(job_id)` -- Sends WhatsApp via Meta Cloud API (or mock) for all contacts in job.
- `categorize_community_report(report_id)` -- LLM-based hazard categorization of community report.
- `cluster_summarize_reports()` -- Celery Beat (every 2 hours). Summarizes approved reports by geographic cluster.
- `check_receipt_timeout()` -- Celery Beat (daily). Marks SENT receipts as FAILED if no callback received within 24h.

**Features:**
- TST-M7-001 through TST-M7-010 (10 features). See `consolidation/13_module_feature_trace.md`.

**Dependencies:** `core`, `hazards` (M1), `alerts` (M6, for CAPPackage FK, `cap_approved` signal consumer), `audit` (M9, via IC-03).

---

### 3.10 Module M8 -- Incident Record & Lifecycle Tracking (`incidents`)

**Purpose:** Manages operational incident records that link alerts, breach events, community reports, and after-action analysis. Provides the learning feedback loop required by WMO Pillar 4. Post-PoC module (stub in Sprint 1).

**Key Models:**
- `Incident` -- Fields: title, description, hazard_type (FK), area (FK AdminBoundary), status (STM-04 states), started_at, closed_at, created_by (FK User).
- `IncidentUpdate` -- Fields: incident (FK), update_type (STATUS_CHANGE/NOTE/AFTER_ACTION), content (TextField), created_by (FK User), created_at.
- `AfterActionReport` -- Fields: incident (OneToOne), summary, lessons_learned, false_positive (boolean), actual_impact_level, completed_by (FK User), completed_at.
- M2M relationships: Incident <> CAPPackage, Incident <> CommunityReport.

**Key Views/Endpoints:**
- `IncidentViewSet` -- CRUD with state machine enforcement. Actions: `start`, `monitor`, `close`, `force-close` (Admin, requires justification).
- `IncidentUpdateViewSet` -- Create/list nested under incident.
- `AfterActionReportViewSet` -- Create/retrieve nested under incident. On completion, emits `after_action.completed` signal (IC-06).

**State Machines:**
- **STM-04** (`Incident`): OPEN > IN_PROGRESS > MONITORING > CLOSED > ARCHIVED. After-action report required for MONITORING > CLOSED. Admin override for force-close.

**Features:**
- TST-M8-001: Incident CRUD
- TST-M8-002: After-Action Reporting
- TST-M8-003: Incident Search & History
- TST-M8-004: CAP-Incident Linkage
- TST-M8-005: Community Report Linkage

**Dependencies:** `core`, `hazards` (M1), `alerts` (M6), `dissemination` (M7, for CommunityReport linkage).

---

### 3.11 Module M9 -- Audit & Compliance Framework (`audit`)

**Purpose:** Provides an immutable, append-only audit event log that records all significant system actions. Supports evidence package generation and compliance reporting. All modules produce audit events; M9 only consumes and stores them.

**Key Models:**
- `AuditEntry` -- Fields: event_type (CharField, choices), actor (FK User, nullable for system events), resource_type (CharField), resource_id (UUID), payload (JSONField), request_id (UUID), ip_address, created_at (auto_now_add). **No update() or delete() methods. Database-level CHECK constraint prevents UPDATE/DELETE on the table.** Validated per MHEWS-FC-OUV-06.
- `EvidencePackage` -- Fields: related_cap_package (FK, nullable), related_incident (FK, nullable), file_key (MinIO/S3 key), sha256_hash, generated_at, generated_by (FK User).

**Key Views/Endpoints:**
- `AuditEntryViewSet` -- List/retrieve (read-only). Filterable by event_type, actor, resource_type, resource_id, date range. Accessible to Admin and Auditor roles.
- `POST /api/audit/evidence-packages/` -- Generate evidence package for a given CAP or incident. Returns ZIP archive containing CAP XML, dispatch receipts, and audit entries.
- `GET /api/audit/compliance/health/` -- Compliance dashboard data: audit coverage metrics, alert pipeline completeness.

**State Machines:** None.

**Audit Event Types (partial list):**

| Module | Event Types |
|---|---|
| M3 | `INGESTION_BATCH_COMPLETE`, `INGESTION_SOURCE_FAILED`, `THRESHOLD_BREACH_DETECTED`, `SOURCE_STATUS_CHANGED` |
| M6 | `CAP_DRAFT_CREATED`, `CAP_VALIDATED`, `CAP_SUBMITTED`, `CAP_APPROVED`, `CAP_REJECTED`, `CAP_CANCELLED`, `CAP_EXPIRED`, `CAP_OVERRIDE_APPROVED` |
| M7 | `DISPATCH_JOB_QUEUED`, `DISPATCH_JOB_STARTED`, `DISPATCH_EMAIL_SENT`, `DISPATCH_WA_SENT`, `DISPATCH_RECEIPT_RECEIVED`, `DISPATCH_JOB_COMPLETED`, `DISPATCH_JOB_FAILED` |
| M9 | `SYSTEM_ERROR`, `EXTERNAL_TIMEOUT` |
| M10 | `USER_LOGIN`, `USER_LOGOUT`, `PASSWORD_CHANGED`, `ROLE_CHANGED` |

**Features:**
- TST-M9-001: Audit Log Viewer
- TST-M9-002: Evidence Package Generation
- TST-M9-003: Audit Immutability Enforcement
- TST-M9-004: Audit Reporting
- TST-M9-005: Compliance Health Monitoring

**Dependencies:** `core`. No outbound dependencies. All modules depend on `audit` for event persistence.

---

### 3.12 Module M11 -- Integration & API Gateway (`gateway`)

**Purpose:** Provides API versioning, rate limiting, OpenAPI schema generation, connector registry management, and webhook receiver infrastructure. Post-PoC module (stub in Sprint 1).

**Key Models:**
- `APIKey` -- Fields: name, key (hashed), permissions (JSONField), is_active, created_by (FK User), expires_at.
- `ConnectorConfig` -- Fields: name (unique), feed_type, url, api_key (encrypted), is_enabled, poll_interval_seconds, last_status.
- `WebhookLog` -- Fields: source (sendgrid/whatsapp), payload (JSONField), signature_valid (boolean), processed (boolean), received_at.

**Key Views/Endpoints:**
- `GET /api/schema/` -- OpenAPI 3 schema (auto-generated by drf-spectacular).
- `GET /api/docs/` -- Swagger UI / ReDoc documentation.
- `APIKeyViewSet` -- CRUD. Admin role for management.
- Rate limiting applied via `django-ratelimit` on all public endpoints.

**Connector Registry:** External data feeds configured via environment variables following the pattern `CONNECTOR_<NAME>_ENABLED`, `CONNECTOR_<NAME>_URL`, `CONNECTOR_<NAME>_API_KEY`. See Architecture Description Section 6.3 for the full connector list.

**Features:**
- TST-M11-001: External Connector Framework
- TST-M11-002: Provider Webhook Receiver
- TST-M11-003: API Rate Limiting
- TST-M11-004: API Documentation (OpenAPI)

**Dependencies:** `core`, `accounts` (M10).

---

### 3.13 Module M12 -- Preparedness, Drill & Response (`preparedness`)

**Purpose:** Manages drill/exercise simulations and Standard Operating Procedure (SOP) documentation. Ensures drill alerts are isolated from live operations. Post-PoC module.

**Key Models:**
- `Drill` -- Fields: name, description, status (STM-11 states), activated_by (FK User), activated_at, deactivated_at, participant_notes.
- `DrillParticipant` -- Fields: drill (FK), user (FK), role_during_drill.
- `StandardOperatingProcedure` -- Fields: title, hazard_type (FK, nullable), content (TextField), version, is_active, approved_by (FK User), approved_at.

**Key Views/Endpoints:**
- `DrillViewSet` -- CRUD. Actions: `activate` (INACTIVE > ACTIVE), `deactivate` (ACTIVE > COMPLETED). Admin role required.
- `SOPViewSet` -- CRUD with version tracking. Filterable by hazard_type.

**State Machines:**
- **STM-11** (`Drill`): INACTIVE > ACTIVE > COMPLETED. When ACTIVE: all CAP drafts forced to `status=Exercise`; dispatch restricted to `drill_contact=true` contacts only; dual-auth still enforced.

**Features:**
- TST-M12-001: Drill Mode Management
- TST-M12-002: SOP Management

**Dependencies:** `core`, `hazards` (M1), `alerts` (M6, reads drill mode flag to force Exercise status).

---

### 3.14 Mock WhatsApp (`mock_whatsapp`)

**Purpose:** Internal Django app that mirrors the Meta Cloud API request/response shape for development and testing. Enables end-to-end WhatsApp dispatch testing without Meta Business verification.

**Key Models:**
- `WhatsAppMockMessage` -- Fields: to_number, template_name, parameters (JSONField), fake_message_id (UUID), sent_at.

**Key Views/Endpoints:**
- `POST /mock/whatsapp/send/` -- Accepts Meta Cloud API-shaped payload, persists record, returns fake message ID.
- `POST /mock/whatsapp/webhook/` -- Self-calls after 2s (Celery delay) with fake delivery receipt.

**Lifecycle:** Removed when live Meta Cloud API integration is activated (`WHATSAPP_API_URL` env var switched).

**Dependencies:** `core`.

---

## 4. Interface Design

### 4.1 Internal API Conventions

**URL Namespace Pattern:**
```
/api/{app_name}/{resource}/              -- List / Create
/api/{app_name}/{resource}/{id}/         -- Retrieve / Update / Delete
/api/{app_name}/{resource}/{id}/{action}/ -- Custom action
```

**DRF Router Registration:** Each app registers its ViewSets in its own `urls.py` using `DefaultRouter`. The root `config/urls.py` includes each app's URL module under the `/api/` prefix:

```python
urlpatterns = [
    path('api/auth/', include('apps.accounts.urls')),
    path('api/hazards/', include('apps.hazards.urls')),
    path('api/ingestion/', include('apps.ingestion.urls')),
    path('api/forecasts/', include('apps.forecasting.urls')),
    path('api/impact/', include('apps.impact.urls')),
    path('api/alerts/', include('apps.alerts.urls')),
    path('api/dissemination/', include('apps.dissemination.urls')),
    path('api/incidents/', include('apps.incidents.urls')),
    path('api/audit/', include('apps.audit.urls')),
    path('api/gateway/', include('apps.gateway.urls')),
    path('api/preparedness/', include('apps.preparedness.urls')),
    path('api/portal/', include('apps.dissemination.portal_urls')),
    path('api/chat/', include('apps.alerts.chat_urls')),
    path('api/v1/cap/', include('apps.alerts.cap_feed_urls')),
    path('mock/', include('apps.mock_whatsapp.urls')),
    path('api/schema/', SpectacularAPIView.as_view()),
    path('api/docs/', SpectacularSwaggerView.as_view()),
]
```

**Pagination:** All list endpoints use cursor-based pagination (default page size: 50). Configurable via `DEFAULT_PAGINATION_CLASS` in DRF settings.

**Filtering:** All list endpoints support filtering via `django-filter` backends. Common filters: `created_at__gte`, `created_at__lte`, `hazard_type`, `status`.

**Versioning:** API version prefix `/api/v1/` used for external-facing endpoints (CAP feed). Internal endpoints omit version prefix for simplicity during PoC; versioning via `Accept` header planned post-PoC.

### 4.2 Interface Contracts (IC-01 through IC-07)

Seven interface contracts define the integration seams between modules. Full specifications are in `consolidation/04_interface_contracts.md`. Summary:

| Contract | Direction | Mechanism | Trigger | Key Guarantee |
|---|---|---|---|---|
| **IC-01** | M3 > M6 | Django signal `threshold_breached` + Celery | ThresholdBreachEvent created (status=OPEN) | Notification only; no auto-draft creation |
| **IC-02** | M6 > M7 | Django signal `cap_approved` + Celery | CAPDraft transitions to APPROVED | One DispatchJob per channel; idempotent on cap_package_id |
| **IC-03** | M7 > M9 | Synchronous audit logger | Each dispatch lifecycle state change | 7 audit event types covering full dispatch lifecycle |
| **IC-04** | M3 > M9 | Synchronous audit logger | Ingestion batch complete, source failure, breach | 4 audit event types covering ingestion lifecycle |
| **IC-05** | M6 > M4 | REST GET (post-PoC) | Operator requests forecast context during CAP authoring | HTTP 404 if no forecast available |
| **IC-06** | M8 > M2 | Django signal `after_action.completed` (post-PoC) | After-action report marked COMPLETE | HITL required for any threshold modification |
| **IC-07** | M6 > External | REST GET `/api/v1/cap/feed/` | External consumer polls | Atom feed; immutable entries; API key auth |

### 4.3 Authentication Flow

```
Client                    Django API                   simplejwt
  |                          |                            |
  |-- POST /api/auth/token/ -|-> validate credentials ---|
  |   {username, password}   |                            |
  |                          |<-- access_token (15min) ---|
  |                          |    refresh_token (24hr)    |
  |<- 200 {access, refresh} -|                            |
  |                          |                            |
  |-- GET /api/alerts/       |                            |
  |   Authorization: Bearer  |                            |
  |   {access_token}         |-> decode + validate -------|
  |                          |<-- user object + claims ---|
  |                          |-> check RBAC group --------|
  |<- 200 {data}            -|                            |
  |                          |                            |
  |-- POST /api/auth/token/  |                            |
  |   refresh/               |                            |
  |   {refresh}              |-> validate refresh --------|
  |                          |<-- new access_token -------|
  |<- 200 {access}          -|                            |
```

**Token storage:** Access and refresh tokens stored in JavaScript memory (not localStorage, not cookies). Refresh handled by Axios interceptor on 401 response.

**Session auth:** Django Admin uses standard session-based authentication (separate from JWT).

### 4.4 Server-Sent Events (SSE)

Real-time push to the operator dashboard uses SSE over a persistent HTTP connection:

- **Endpoint:** `GET /api/events/stream/` (authenticated)
- **Channel:** Redis pub/sub key pattern `mhews:sse:{user_id}`
- **Event types:**
  - `threshold.breached` -- New breach event detected
  - `cap.status_changed` -- CAP draft status transition
  - `dispatch.status_changed` -- Dispatch job/receipt status update
  - `system.notification` -- General system notifications

SSE is preferred over WebSockets for this use case because the data flow is unidirectional (server-to-client) and SSE has simpler reconnection semantics.

---

## 5. Data Design

### 5.1 Database Schema Overview

The PostgreSQL database contains tables organized by Django app. Each app's tables are prefixed with the app name (Django convention). Key relationships are enforced via foreign keys with `on_delete` policies:

| App Prefix | Key Tables | Relationships |
|---|---|---|
| `hazards_` | `hazardtype`, `thresholdconfig`, `adminboundary` | ThresholdConfig > HazardType (CASCADE). AdminBoundary self-FK (SET_NULL). |
| `ingestion_` | `datasource`, `observationrecord`, `thresholdbreachevent` | DataSource > HazardType (PROTECT). ObservationRecord > DataSource (CASCADE). ThresholdBreachEvent > ObservationRecord (PROTECT), > ThresholdConfig (PROTECT). |
| `alerts_` | `captemplate`, `capdraft`, `validationresult`, `cappackage`, `llmoutput` | CAPTemplate > HazardType (PROTECT). CAPDraft > CAPTemplate (PROTECT), > ThresholdBreachEvent (SET_NULL, unique). CAPPackage > CAPDraft (OneToOne, CASCADE). LLMOutput > CAPDraft (SET_NULL). |
| `dissemination_` | `contact`, `contactgroup`, `dispatchjob`, `dispatchreceipt`, `communityreport` | DispatchJob > CAPPackage (CASCADE). DispatchReceipt > DispatchJob (CASCADE), > Contact (PROTECT). Contact <> ContactGroup (M2M). |
| `incidents_` | `incident`, `incidentupdate`, `afteractionreport` | IncidentUpdate > Incident (CASCADE). AfterActionReport > Incident (OneToOne, CASCADE). Incident <> CAPPackage (M2M). |
| `audit_` | `auditentry`, `evidencepackage` | AuditEntry: no FK to other tables (resource_type + resource_id pattern). EvidencePackage > CAPPackage (SET_NULL). |
| `accounts_` | `customuser` | Django Group M2M via auth_user_groups. |

### 5.2 PostGIS Spatial Data Patterns

| Use Case | Geometry Type | SRID | Index |
|---|---|---|---|
| Administrative boundaries | MultiPolygon | 4326 (WGS84) | GiST spatial index |
| Observation locations | Point | 4326 | GiST spatial index |
| Community report locations | Point | 4326 | GiST spatial index |
| CAP alert area | Polygon (serialized from AdminBoundary) | 4326 | Via AdminBoundary index |
| Impact layers | Polygon / MultiPolygon | 4326 | GiST spatial index |

**Spatial queries used:**
- `ST_Contains(boundary.geom, observation.location)` -- Associate observation with admin boundary
- `ST_Intersects(boundary.geom, contact.area_of_interest)` -- Geofenced contact resolution for dispatch (IC-02, Priority 2)
- `ST_Area(ST_Transform(boundary.geom, local_srid))` -- Area calculation for impact assessment

### 5.3 Redis Cache Key Patterns

| Pattern | TTL | Purpose |
|---|---|---|
| `mhews:cache:hazard_types` | 3600s | Cached list of active hazard types |
| `mhews:cache:thresholds:{hazard_type_id}` | 300s | Cached threshold configs for a hazard type |
| `mhews:cache:user:{user_id}:perms` | 600s | User permission set (RBAC groups + ABAC) |
| `mhews:sse:{user_id}` | Ephemeral | SSE channel for real-time push |
| `mhews:ratelimit:{ip}:{endpoint}` | 60s | Rate limit counter per IP per endpoint |
| `celery` prefix | Task-dependent | Celery broker messages (managed by Celery) |

### 5.4 Celery Task Signatures

| Task | Queue | Schedule | Retry Policy |
|---|---|---|---|
| `ingestion.tasks.poll_data_source` | `celery-ingestion` | Celery Beat (per source interval, default 60s) | 3 retries, exponential backoff (30s, 60s, 120s) |
| `ingestion.tasks.check_breach_resolution` | `celery-default` | Celery Beat (every 5 min) | No retry (idempotent) |
| `alerts.tasks.generate_llm_narrative` | `celery-default` | On demand | 2 retries, 30s backoff. Timeout: 60s |
| `alerts.tasks.expire_drafts` | `celery-default` | Celery Beat (every 5 min) | No retry (idempotent) |
| `dissemination.tasks.dispatch_email_batch` | `celery-default` | On signal (`cap_approved`) | 3 retries per contact, 15s backoff |
| `dissemination.tasks.dispatch_whatsapp_batch` | `celery-default` | On signal (`cap_approved`) | 3 retries per contact, 15s backoff |
| `dissemination.tasks.categorize_community_report` | `celery-default` | On creation | 2 retries, 30s backoff |
| `dissemination.tasks.cluster_summarize_reports` | `celery-default` | Celery Beat (every 2 hours) | No retry |
| `dissemination.tasks.check_receipt_timeout` | `celery-default` | Celery Beat (daily) | No retry |
| `forecasting.tasks.execute_model_run` | `celery-model` | On demand / Celery Beat (post-NWP ingest) | 1 retry, 60s backoff. Timeout: 120s |
| `mock_whatsapp.tasks.send_fake_receipt` | `celery-default` | 2s delay after mock send | No retry |

---

## 6. Component Design

### 6.1 Celery Worker Architecture

The system uses three logical worker pools sharing the same Django Docker image but bound to different queues:

```
                         +-------------------+
                         |    Celery Beat     |
                         | (single process)   |
                         +--------+----------+
                                  |
              schedule triggers   |
            +---------------------+---------------------+
            |                     |                     |
            v                     v                     v
   +----------------+   +----------------+   +------------------+
   | celery-default  |   | celery-ingestion|   | celery-model     |
   | (4 concurrency) |   | (8 concurrency) |   | (2 concurrency)  |
   +--------+-------+   +--------+-------+   +--------+---------+
            |                     |                     |
   dispatch, audit,      data source        forecast model
   LLM narrative,        polling,           execution,
   community reports,    threshold eval     impact computation
   report generation
```

**Isolation rationale (NFR-PERF-BATCH-01):** The `celery-model` queue is isolated so that long-running model computations (up to 120s) do not block time-sensitive dispatch and ingestion tasks.

**Scaling:** Each worker pool scales independently by increasing the `--concurrency` flag or deploying additional worker containers bound to the same queue.

### 6.2 LLM Integration Pattern

The LLM service uses a provider abstraction controlled by the `LLM_BACKEND` environment variable:

```
          +-------------------+
          | LLM Service Layer |
          | (alerts/services/ |
          |  llm_service.py)  |
          +--------+----------+
                   |
          LLM_BACKEND env var
                   |
         +---------+---------+
         |                   |
         v                   v
  +-------------+    +--------------+
  | Anthropic   |    | Ollama       |
  | Adapter     |    | Adapter      |
  | (Cloud)     |    | (On-Premise) |
  +------+------+    +------+-------+
         |                   |
         v                   v
  Claude API           Local HTTP
  (claude-haiku-4-5     (localhost:11434)
   claude-sonnet-4-6)
```

**Adapter interface:**
- `generate_narrative(hazard_type, severity, location, context) -> LLMResponse`
- `categorize_report(description, photo_url) -> CategoryResult`
- `chat(messages, context) -> ChatResponse`

**Configuration:**
- `LLM_BACKEND` -- `anthropic` (default) or `ollama`
- `LLM_API_KEY` -- API key for Anthropic (ignored for Ollama)
- `LLM_MODEL_FAST` -- Model ID for fast operations (default: `claude-haiku-4-5` / `llama3.2:3b`)
- `LLM_MODEL_QUALITY` -- Model ID for quality operations (default: `claude-sonnet-4-6` / `mistral:7b`)
- `LLM_TIMEOUT` -- Request timeout in seconds (default: 60)

**Failure handling:** LLM unavailability is non-blocking. Operators can author narratives manually. Timeout per MHEWS-FC-ERR-04 (60s default). Retry via Celery task (2 retries, 30s backoff).

**Audit:** Every LLM call is logged as `LLMOutput` with model_id, model_version, prompt_hash (SHA-256 of input), output_text, token_count, and latency_ms.

**PII masking:** Contact names, phone numbers, and email addresses are redacted from LLM input before API calls (MHEWS-SD-LLM-06).

### 6.3 Notification Dispatch Pipeline (M6 > M7 > Channels)

The end-to-end dispatch pipeline follows this sequence:

```
1. CAPDraft transitions to APPROVED (STM-01)
2. CAPPackage generated (XML + SHA-256 hash)
3. Django signal `cap_approved` emitted with IC-02 payload
4. M7 signal handler receives signal:
   a. Resolve contacts:
      - Priority 1: Explicit distribution_list_ids
      - Priority 2: Geofenced contacts from alert area polygon
      - Priority 3: Default distribution list for hazard type
      - Fallback: No contacts -> DispatchJob(status=FAILED, reason=NO_CONTACTS)
   b. Filter by channel opt-in status (STM-08)
   c. Filter by drill_contact flag if drill mode active (STM-11)
   d. Create DispatchJob per channel (EMAIL, WHATSAPP)
   e. Create DispatchReceipt per contact per channel
   f. Emit AuditEvent(DISPATCH_JOB_QUEUED) per job (IC-03)
   g. Enqueue Celery tasks for batch processing
5. Celery worker picks up dispatch task:
   a. Update DispatchJob status to RUNNING
   b. For each contact in batch:
      - Select template by contact.preferred_language
      - Send via channel adapter (SendGrid / Meta WA / mock)
      - Update DispatchReceipt to SENT with provider_message_id
      - Emit per-contact AuditEvent
   c. On completion: DispatchJob status to COMPLETED
6. Provider webhook received:
   a. Verify HMAC signature (MHEWS-FC-OUV-04)
   b. Match provider_message_id to DispatchReceipt
   c. Update receipt status (DELIVERED / FAILED / BOUNCED)
   d. Emit AuditEvent(DISPATCH_RECEIPT_RECEIVED)
```

### 6.4 Data Ingestion Pipeline (M3 > Threshold Check > M6)

```
1. Celery Beat triggers poll_data_source(source_id) per schedule
2. Ingestion task executes:
   a. Fetch data from external URL (HTTPS GET)
   b. Parse response (GeoRSS XML / JSON / CSV depending on source_type)
   c. Validate parsed records (coordinate validation per MHEWS-FC-INV-04)
   d. Persist ObservationRecords (batch insert)
   e. Emit AuditEvent(INGESTION_BATCH_COMPLETE) (IC-04)
3. Threshold evaluation (inline, same task):
   a. Load active ThresholdConfigs for source's hazard_type (cached in Redis)
   b. For each observation, evaluate against each threshold
   c. If threshold breached:
      - Create ThresholdBreachEvent(status=OPEN)
      - Emit Django signal `threshold_breached` (IC-01)
      - Emit AuditEvent(THRESHOLD_BREACH_DETECTED) (IC-04)
4. IC-01 consumer (M6):
   a. Push SSE notification to operator dashboard
   b. Make breach event available for "Draft Alert" action
   c. No auto-creation of CAPDraft (BQ-04 decision)
5. Error handling:
   a. Network/parse/auth failure: mark DataSource.last_fetch_status=FAILED
   b. Increment consecutive_failures counter
   c. After 3 consecutive: DataSource status > DEGRADED (STM-05)
   d. Emit AuditEvent(INGESTION_SOURCE_FAILED) (IC-04)
   e. Retry: up to 3 times within 10-minute window, exponential backoff
```

### 6.5 Map Tile Serving Pattern

Map rendering uses MapLibre GL JS on the frontend with a tile source determined by the deployment profile:

| Profile | Tile Source | Configuration |
|---|---|---|
| A (Cloud) | OpenFreeMap / Protomaps CDN | `MAP_TILE_URL=https://tiles.openfreemap.org/...` |
| B (On-Premise) | Self-hosted tileserver-gl | `MAP_TILE_URL=http://tileserver:8080/...` |

The `MAP_TILE_URL` environment variable is injected into the React frontend at build time. No code change between profiles. Profile B requires a pre-downloaded `.mbtiles` extract (country-level, from Geofabrik) mounted into the tileserver-gl container.

Map layers displayed:
- Administrative boundaries (from PostGIS via GeoJSON API)
- Observation points (from ingestion data)
- Community report locations (from approved reports)
- Alert area polygons (from CAPPackage area data)
- Impact heatmaps (from pre-computed impact layers, post-PoC)

---

## 7. Error Handling Design

### 7.1 Error Handling Requirements

The system implements 10 error handling requirements defined in `consolidation/02_completeness_functional.md`:

| Requirement ID | Scope | Summary |
|---|---|---|
| MHEWS-FC-ERR-01 | Cross-module | Structured JSON error response format for all API errors |
| MHEWS-FC-ERR-02 | Cross-module | Request ID propagation (X-Request-ID) for end-to-end tracing |
| MHEWS-FC-ERR-03 | Cross-module | Global exception handler: sanitized 500 responses, full stack trace in logs |
| MHEWS-FC-ERR-04 | Cross-module | External service timeout handling with configurable timeouts |
| MHEWS-FC-ERR-05 | Cross-module | Database error handling (connection pool, unique constraint, FK violations) |
| MHEWS-FC-ERR-06 | M3 | Ingestion source failure: DEGRADED after 3 failures, exponential backoff |
| MHEWS-FC-ERR-07 | M6 | CAP validation failure: structured error list, no state advancement |
| MHEWS-FC-ERR-08 | M7 | Dispatch channel failure: per-contact isolation, manual retry endpoint |
| MHEWS-FC-ERR-09 | M9 | Audit write failure: durable fallback queue, non-blocking primary operation |
| MHEWS-FC-ERR-10 | M4 | Model run failure: no cascade, dashboard notification |

### 7.2 Django Exception Hierarchy

```
Exception
+-- DjangoBaseException
    +-- ValidationError          --> HTTP 422 (field-level errors)
    +-- PermissionDenied         --> HTTP 403
    +-- ObjectDoesNotExist       --> HTTP 404
    +-- IntegrityError           --> HTTP 409 (DUPLICATE_RESOURCE) or 422 (REFERENTIAL_INTEGRITY)
+-- DRF APIException
    +-- AuthenticationFailed     --> HTTP 401
    +-- NotAuthenticated         --> HTTP 401
    +-- Throttled                --> HTTP 429
    +-- ParseError               --> HTTP 400
+-- MHEWSException (custom base)
    +-- InvalidStateTransition   --> HTTP 409 (state machine violation)
    +-- ExternalServiceTimeout   --> HTTP 502 (upstream timeout)
    +-- CapacityExceeded         --> HTTP 503 (connection pool exhaustion)
    +-- SelfApprovalForbidden    --> HTTP 403 (dual-auth violation)
```

### 7.3 DRF Error Response Format

All API error responses conform to the MHEWS-FC-ERR-01 structure:

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error description",
    "field": "field_name | null",
    "request_id": "uuid (from X-Request-ID header)"
  }
}
```

For validation errors with multiple fields:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Input validation failed",
    "details": [
      {"field": "severity", "code": "INVALID_CHOICE", "message": "Value 'EXTREME_MAX' is not a valid choice. Valid choices: Extreme, Severe, Moderate, Minor, Unknown"},
      {"field": "expires_at", "code": "INVALID_DATE", "message": "expires_at must be greater than sent"}
    ],
    "request_id": "uuid"
  }
}
```

HTTP status codes follow RFC 9110 semantics:

| Code | Usage |
|---|---|
| 400 | Malformed request (parse error, missing required field) |
| 401 | Authentication required or token expired |
| 403 | Insufficient permissions (RBAC/ABAC denial, self-approval) |
| 404 | Resource not found |
| 409 | Conflict (duplicate resource, invalid state transition) |
| 422 | Validation error (field-level, referential integrity) |
| 429 | Rate limit exceeded |
| 500 | Unhandled server error (sanitized; stack trace in logs only) |
| 502 | External service timeout or error |
| 503 | Capacity exceeded (connection pool, queue full) |

### 7.4 Celery Retry Policies

| Task Category | Max Retries | Backoff Strategy | Dead-Letter Action |
|---|---|---|---|
| Data ingestion polling | 3 | Exponential: 30s, 60s, 120s | Mark source DEGRADED; AuditEvent |
| Dispatch (email/WA) | 3 per contact | Exponential: 15s, 30s, 60s | DispatchReceipt(FAILED); AuditEvent; manual retry available |
| LLM narrative | 2 | Fixed: 30s | LLMOutput(status=FAILED); operator authors manually |
| Model execution | 1 | Fixed: 60s | ModelRun(FAILED); AuditEvent; dashboard notification |
| Signal handler (IC-01, IC-02) | 3 | Exponential: 10s, 30s, 60s | Dead-letter queue; AuditEvent; operator notified |

---

## 8. Security Design

### 8.1 Authentication Layers

| Layer | Mechanism | Scope | Token Lifetime |
|---|---|---|---|
| JWT (simplejwt) | Bearer token in `Authorization` header | API access from React frontend and integrators | Access: 15 min, Refresh: 24 hr |
| Django session | Session cookie | Django Admin panel access | Session-based (configurable) |
| API key | Bearer token (static, hashed in DB) | CAP feed consumers (IC-07), external integrations | Until revoked |
| Anonymous | No authentication | Public portal, community report form, CAP feed (with API key) | N/A |

**JWT implementation details:**
- Access token contains: user_id, username, groups, exp, iat, jti
- Refresh token: single-use (rotated on each refresh)
- Tokens stored in JavaScript memory (not localStorage, not cookies) to mitigate XSS
- Axios interceptor handles automatic refresh on 401 response
- Token blacklisting on logout via simplejwt `OutstandingToken`

### 8.2 Authorization Layers

**Layer 1 -- RBAC (Django Groups):**

| Role | Group | Key Permissions |
|---|---|---|
| Operator | `operator` | Draft alerts, view observations, acknowledge breaches, view dispatch, use chatbot |
| Approver | `approver` | All Operator + approve/reject alerts |
| Admin | `admin` | All Approver + manage users, hazard types, contacts, thresholds, drill mode, full audit |
| Auditor | `auditor` | Read-only audit log + evidence packages |

Enforcement: DRF `permission_classes` on each ViewSet. Custom permission class `IsInGroup(group_name)` checks `request.user.groups.filter(name=group_name).exists()`.

**Layer 2 -- ABAC (django-guardian, object-level):**

Installed from Sprint 1 but policies configured post-PoC. Enables:
- Per-hazard-type alert drafting permissions
- Per-region data source management
- Per-team incident ownership

Enforcement: `guardian.shortcuts.get_objects_for_user()` in ViewSet `get_queryset()`.

**Dual Authorization (STM-01):**
- `CAPDraft.approve()` checks `request.user.id != cap_draft.submitted_by_id`
- Self-approval returns HTTP 403 with code `SELF_APPROVAL_FORBIDDEN`
- Emergency bypass: Admin role, requires `bypass_reason` (50+ chars), logged as `AuditEvent(CAP_OVERRIDE_APPROVED)`, 24-hour post-review reminder queued

### 8.3 Field-Level Encryption Pattern

PII fields are encrypted at rest using `django-encrypted-model-fields` (post-PoC hardening):

| Model | Encrypted Fields | Key Management |
|---|---|---|
| `Contact` | email, phone_whatsapp | `FIELD_ENCRYPTION_KEY` env var |

Encrypted fields are searchable via HMAC-based blind index for exact match lookups.

### 8.4 Audit Trail Integration

Every security-relevant action produces an `AuditEntry`:

| Action | Event Type | Key Payload Fields |
|---|---|---|
| Login success | `USER_LOGIN` | user_id, ip_address |
| Login failure | `USER_LOGIN_FAILED` | username, ip_address, failure_reason |
| Password change | `PASSWORD_CHANGED` | user_id |
| Role change | `ROLE_CHANGED` | user_id, old_groups, new_groups, changed_by |
| Alert approval | `CAP_APPROVED` | cap_draft_id, approved_by, approver_role |
| Override approval | `CAP_OVERRIDE_APPROVED` | cap_draft_id, approved_by, bypass_reason |
| Self-approval blocked | `SELF_APPROVAL_BLOCKED` | cap_draft_id, user_id |

All audit entries include `request_id` (X-Request-ID) for end-to-end tracing per MHEWS-FC-ERR-02.

---

## 9. Design Constraints

### 9.1 Hard Constraints (from Section II Design Document)

| ID | Constraint | Design Impact |
|---|---|---|
| C-01 | Digital/software-only scope | No hardware integrations (sirens, sensors). All input via API/data feeds. |
| C-02 | Hazard-agnostic, model-driven | No hardcoded hazard logic. All behavior driven by HazardType + ThresholdConfig configuration. |
| C-03 | Containerized (Docker) | All services as Docker containers. Docker Compose baseline. Kubernetes upgrade path. |
| C-04 | OGC standards: consume only | System consumes OGC WMS/WFS. No OGC serving (no GeoServer). |
| C-05 | CAP: authoring + validation + export only | No CAP hub ingest. System produces CAP, does not consume third-party CAP. |
| C-06 | Dissemination: Email + Web Portal + WhatsApp only | SMS, cell-broadcast, mobile push are out of scope. |
| C-07 | No external identity federation | No SAML, no OIDC. All users in Django auth. |
| C-08 | Hybrid RBAC/ABAC | Django Groups + django-guardian. No external policy engine (OPA, Casbin). |
| C-09 | Immutable structured audit | Append-only AuditEntry. DB-level constraint. No UPDATE/DELETE permitted. |

### 9.2 Technology Constraints (from PoC Decisions)

| Constraint | Rationale |
|---|---|
| Python 3.12 runtime | Standardized via Docker. Latest stable with performance improvements. |
| Django 5.x (not FastAPI) | Batteries for auth, ORM, admin, signals outweigh FastAPI's async advantage at MHEWS data volumes (ADR-01). |
| PostgreSQL 16 + PostGIS 3.4 | Required for geospatial queries. No alternative RDBMS considered. |
| Redis 7 (not RabbitMQ) | Dual-role as Celery broker and application cache. Simpler operations than RabbitMQ. |
| Celery 5 (not Kafka/NATS) | Polling-based ingestion at 60s intervals does not require streaming infrastructure (ADR-07). Upgrade path documented. |
| MinIO (not direct S3) | S3-compatible for on-premise deployment. Swappable to S3/GCS via env var. |
| Single-tenant baseline | Phase 1 serves one national authority. Multi-tenant upgrade via django-tenants when needed (ADR-02). |
| Active-passive HA | 99.9% SLA achievable at 2x cost. Active-active documented as upgrade path (ADR-08). |

### 9.3 Design Decisions Cross-Reference

All architecture decisions are documented as ADRs in `consolidation/17_architecture_description.md`, Section 4:

| ADR | Decision | Impact on Design |
|---|---|---|
| ADR-01 | Django over FastAPI | Monolithic Django project, DRF for API, signals for decoupling |
| ADR-02 | Single-tenant baseline | No tenant_id FK, no tenant middleware |
| ADR-03 | Dual deployment profile | Adapter pattern for all external services, env-var switching |
| ADR-04 | CAP-only alert format | Single validation path (lxml + XSD), no proprietary schemas |
| ADR-05 | Pluggable LLM provider | `LLM_BACKEND` env var, adapter interface |
| ADR-06 | MapLibre GL JS | `MAP_TILE_URL` env var, no server-side rendering |
| ADR-07 | Celery for model execution | `celery-model` isolated queue, CPU-only Phase A |
| ADR-08 | 99.9% SLA, active-passive HA | Patroni for PG failover, Redis Sentinel |
| ADR-09 | Community feedback in scope | Public endpoint, moderation gate, LLM categorization |
| ADR-10 | Tri-lingual i18n | django-modeltranslation, per-contact language dispatch |

---

## Appendix A: Module Dependency Matrix

| Module | Depends On | Depended On By |
|---|---|---|
| `core` | -- | All modules |
| `accounts` (M10) | `core` | All authenticated modules |
| `hazards` (M1) | `core` | M2, M3, M4, M5, M6, M7, M8, M12 |
| `ingestion` (M3) | `core`, M1 | M6 (via IC-01 signal), M9 (via IC-04 audit) |
| `risk` (M2) | `core`, M1 | -- |
| `forecasting` (M4) | `core`, M1 | M6 (via IC-05 REST, post-PoC) |
| `impact` (M5) | `core`, M1 | M6 (linkage) |
| `alerts` (M6) | `core`, M1, M3 | M7 (via IC-02 signal), M8 (linkage), External (via IC-07 feed) |
| `dissemination` (M7) | `core`, M1, M6 | M8 (CommunityReport linkage), M9 (via IC-03 audit) |
| `incidents` (M8) | `core`, M1, M6, M7 | M2 (via IC-06 signal, post-PoC) |
| `audit` (M9) | `core` | -- (all modules emit events to M9) |
| `gateway` (M11) | `core`, M10 | -- |
| `preparedness` (M12) | `core`, M1, M6 | -- |
| `mock_whatsapp` | `core` | M7 (via env-var URL) |

## Appendix B: State Machine Cross-Reference

| STM ID | Entity | Module | States | Terminal States | Reference |
|---|---|---|---|---|---|
| STM-01 | CAPDraft | M6 | DRAFT, PENDING_APPROVAL, APPROVED, REJECTED, CANCELLED, EXPIRED | APPROVED, CANCELLED, EXPIRED | `consolidation/02_completeness_functional.md` |
| STM-02 | DispatchJob | M7 | QUEUED, RUNNING, COMPLETED, FAILED | COMPLETED, FAILED | `consolidation/02_completeness_functional.md` |
| STM-03 | ThresholdBreachEvent | M3 | OPEN, ACKNOWLEDGED, CAP_DRAFTED, RESOLVED, DISMISSED | RESOLVED, DISMISSED | `consolidation/02_completeness_functional.md` |
| STM-04 | Incident | M8 | OPEN, IN_PROGRESS, MONITORING, CLOSED, ARCHIVED | ARCHIVED | `consolidation/02_completeness_functional.md` |
| STM-05 | DataSource | M3 | ACTIVE, PAUSED, DEGRADED, INACTIVE | INACTIVE | `consolidation/02_completeness_functional.md` |
| STM-06 | ModelRun | M4 | QUEUED, RUNNING, COMPLETED, FAILED, CANCELLED | COMPLETED, FAILED, CANCELLED | `consolidation/02_completeness_functional.md` |
| STM-07 | CommunityReport | M7 | PENDING, APPROVED, REJECTED, ARCHIVED | ARCHIVED | `consolidation/02_completeness_functional.md` |
| STM-08 | Contact (consent) | M7 | OPTED_IN, OPTED_OUT, RE_OPTED_IN | -- (cyclic) | `consolidation/02_completeness_functional.md` |
| STM-09 | DispatchReceipt | M7 | QUEUED, SENT, DELIVERED, FAILED, BOUNCED | DELIVERED, FAILED, BOUNCED | `consolidation/02_completeness_functional.md` |
| STM-10 | LLMOutput | M6 | REQUESTED, GENERATED, ACCEPTED, REJECTED, EXPIRED | ACCEPTED, REJECTED, EXPIRED | `consolidation/02_completeness_functional.md` |
| STM-11 | Drill | M12 | INACTIVE, ACTIVE, COMPLETED | COMPLETED | `consolidation/02_completeness_functional.md` |

## Appendix C: Feature-to-Module Trace Summary

61 features across 13 modules (including AI Chatbot). Full trace table with user story mappings and test IDs in `consolidation/13_module_feature_trace.md`.

| Module | Feature Count | PoC Sprint | Post-PoC |
|---|---|---|---|
| M1 -- Hazard Taxonomy | 3 | S1 | -- |
| M2 -- Risk & Scenario | 4 | -- | All |
| M3 -- Data Ingestion | 4 | S1-S2 | -- |
| M4 -- Forecasting | 5 | -- | All |
| M5 -- Impact Analysis | 5 | S3 (partial) | 4 features |
| M6 -- CAP Authoring | 7 | S2-S3 | 1 feature |
| M7 -- Dissemination | 10 | S2 (partial) | 7 features |
| M8 -- Incident Tracking | 5 | -- | All |
| M9 -- Audit | 5 | S1-S3 | 2 features |
| M10 -- Admin/RBAC | 5 | S1 | 2 features |
| M11 -- API Gateway | 4 | S1-S3 (partial) | 1 feature |
| M12 -- Drill & Response | 2 | -- | All |
| AI Chatbot | 2 | S3 | -- |
| **Total** | **61** | **21** | **40** |
