# Software Requirements Specification (SRS)
**Standard:** IEEE 29148:2018
**Project:** Multi-Hazard Early Warning System (MHEWS)
**Version:** 1.0
**Date:** 2026-03-09
**Status:** Baseline Draft
**Task:** CON-D-06 (also completes CON-P2-21 — acceptance criteria for legacy FRs)

---

## 1. Introduction

### 1.1 Purpose

This Software Requirements Specification (SRS) defines all functional and non-functional requirements for the Multi-Hazard Early Warning System (MHEWS). It conforms to IEEE 29148:2018 and serves as the single authoritative requirements baseline for design, implementation, testing, and acceptance.

This document consolidates 579 requirements from four sources:
- **Part A** — 439 active legacy requirements (from 479 raw, after deduplication and OOS retirement)
- **Part B** — 72 system-derived requirements (from ghost requirement analysis)
- **Part C** — 37 functional completeness requirements (error handling, state machines, validation)
- **Part D** — 31 NFR completeness requirements (logging, scalability, maintainability, observability, performance)

Each functional requirement includes acceptance criteria in GIVEN/WHEN/THEN format (completing CON-P2-21).

### 1.2 Scope

**System Name:** Multi-Hazard Early Warning System (MHEWS)

**System Purpose:** A digital, software-only platform supporting the end-to-end early warning pipeline: hazard monitoring, threshold evaluation, CAP-format alert authoring, multi-channel dissemination, incident tracking, and structured audit. The system is hazard-agnostic, model-driven, and deployable on both commercial cloud and government on-premise infrastructure.

**Modules:** 12 software modules (M1–M12) plus shared infrastructure (`core`, `mock_whatsapp`, `config`).

**In scope:** Digital alert pipeline, CAP authoring/validation/export, email/WhatsApp/web portal dissemination, RBAC/ABAC access control, immutable audit, drill/exercise simulation, impact analysis, forecast model integration, community hazard reporting.

**Out of scope:** Physical infrastructure (sirens, sensors, PA systems), institutional governance, SMS/cell-broadcast/mobile push channels, external identity federation (SAML/OIDC), GIS tile serving (consume-only by design), native mobile applications, citizen-facing chatbot, anticipatory funding triggers, evacuation routing.

### 1.3 Product Overview

#### 1.3.1 System Context

MHEWS operates within an ecosystem of:
- **External data providers:** GDACS, ECMWF, national NWP services, satellite APIs
- **Communication services:** SendGrid (email), Meta WhatsApp Cloud API
- **LLM providers:** Anthropic Claude (cloud), Ollama (on-premise)
- **Downstream consumers:** CAP feed subscribers, institutional dashboards
- **Users:** NMHS operators interact through a React dashboard; public receives alerts via email, WhatsApp, and web portal

#### 1.3.2 User Characteristics

| Stakeholder | Description |
|---|---|
| **NMHS Operators** | Monitor data, draft alerts, manage incidents. Moderate technical skill. |
| **Approvers** | Senior NMHS staff who review and authorize CAP alert dispatch. |
| **Tenant Administrators** | Configure hazard types, thresholds, contacts, user roles. |
| **Public Users** | Alert recipients (email, WhatsApp, web portal) and community reporters. |
| **Auditors** | Internal/external reviewers verifying compliance. |
| **System Integrators** | Teams connecting MHEWS to external feeds or downstream consumers. |
| **DevOps / Infrastructure** | Teams responsible for deployment, monitoring, scaling. |
| **Development Team** | Engineers building and maintaining the system. |

#### 1.3.3 Constraints

| # | Constraint |
|---|---|
| C1 | Digital/software-only scope — no siren hardware, no institutional governance |
| C2 | Hazard-agnostic and model-driven |
| C3 | Containerized (Docker), orchestration-agnostic |
| C4 | OGC standards: consume only, minimal expose |
| C5 | CAP: authoring + validation + export ONLY (no CAP hub ingest) |
| C6 | Dissemination: Email, Web Portal, WhatsApp ONLY |
| C7 | Hybrid RBAC/ABAC, multi-tenant, NO external federation |
| C8 | Full structured audit framework with immutable evidence |
| C9 | Dual deployment profiles: cloud (Profile A) and on-premise (Profile B) |

#### 1.3.4 Assumptions and Dependencies

| # | Assumption |
|---|---|
| A1 | PostgreSQL 16 + PostGIS 3.4 is available in the deployment environment |
| A2 | Redis 7 is available for Celery broker and cache |
| A3 | Docker runtime is available on all target hosts |
| A4 | External data feeds (GDACS, ECMWF) are accessible in Profile A deployments |
| A5 | Meta Business Account verification for WhatsApp will be completed during Sprint 1 |
| A6 | Client will provide official administrative boundary shapefiles or approve GADM usage |
| A7 | Third language (Language X) will be identified before Sprint 2 |
| A8 | SendGrid account with dedicated IP is available for Profile A |
| A9 | MinIO or S3-compatible storage is available for object storage |

### 1.4 Definitions, Acronyms, Abbreviations

| Term | Definition |
|---|---|
| ABAC | Attribute-Based Access Control |
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

### 1.5 References

| # | Document | Location |
|---|---|---|
| R1 | Normalized Requirements (Master List) | `consolidation/11_normalized_requirements.md` |
| R2 | System-Derived Requirements | `consolidation/01_system_derived_requirements.md` |
| R3 | Functional Completeness | `consolidation/02_completeness_functional.md` |
| R4 | NFR Completeness | `consolidation/03_nfr_completeness.md` |
| R5 | Interface Contracts | `consolidation/04_interface_contracts.md` |
| R6 | User Story Catalog | `consolidation/05_user_story_catalog.md` |
| R7 | FR/NFR Classification | `consolidation/15_fr_nfr_classification.md` |
| R8 | Requirement Traceability Map | `consolidation/16_req_traceability_map.md` |
| R9 | Architecture Description | `consolidation/17_architecture_description.md` |
| R10 | Software Design Description | `consolidation/18_software_design_description.md` |
| R11 | Risk Register | `consolidation/19_risk_register.md` |
| R12 | V&V Plan | `consolidation/20_vv_plan.md` |
| R13 | Module-Feature Trace | `consolidation/13_module_feature_trace.md` |
| R14 | PRD (Section III Annex) | `docs/mhewsprd.md` |
| R15 | Design Document (Section II) | `docs/SECTION II — Client Guidance and Software Design Document.docx` |

---

## 2. Overall Description

### 2.1 Product Perspective

MHEWS implements the WMO four-pillar early warning framework:

| WMO Pillar | MHEWS Modules | Coverage |
|---|---|---|
| 1. Disaster Risk Knowledge | M1 (Hazard & Taxonomy), M2 (Risk & Scenario), M5 (Impact & Exposure) | Hazard registry, risk scenarios, exposure datasets, vulnerability indices |
| 2. Detection, Monitoring, Analysis & Forecasting | M3 (Data Ingestion), M4 (Forecasting) | Multi-source ingestion, threshold evaluation, forecast model execution |
| 3. Warning Dissemination & Communication | M6 (CAP Alert Authoring), M7 (Dissemination) | CAP authoring, dual-auth approval, email/WhatsApp/portal dispatch |
| 4. Preparedness & Response Capabilities | M8 (Incident Tracking), M12 (Drill & Exercise) | Incident lifecycle, after-action learning, drill simulation, SOP repository |

Cross-cutting modules: M9 (Audit & Compliance), M10 (Administration & Access), M11 (API Gateway).

### 2.2 Product Functions

| Module | Django App | Responsibility | Sprint |
|---|---|---|---|
| M1 — Hazard & Taxonomy | `hazards` | Hazard type registry, threshold configuration, admin boundaries | S1 |
| M2 — Risk & Scenario | `risk` | Scenario definition, model registry, threshold calibration | Post-PoC |
| M3 — Data Ingestion | `ingestion` | Data source registry, polling, observation storage, threshold evaluation | S1 |
| M4 — Forecasting | `forecasting` | Forecast model execution, model run lifecycle | Post-PoC (stub S1) |
| M5 — Impact Analysis | `impact` | Exposure datasets, impact layers, spatial queries | Post-PoC (stub S1) |
| M6 — CAP Alert Authoring | `alerts` | CAP templates, draft lifecycle, XSD validation, LLM narrative, approval | S2 |
| M7 — Dissemination | `dissemination` | Contact directory, dispatch, email/WhatsApp/portal, community feedback | S2 |
| M8 — Incident Tracking | `incidents` | Incident lifecycle, after-action reports | Post-PoC (stub S1) |
| M9 — Audit & Compliance | `audit` | Immutable audit log, evidence packages, compliance reporting | S1 |
| M10 — Administration | `accounts` | User management, RBAC/ABAC, JWT authentication | S1 |
| M11 — API Gateway | `gateway` | API versioning, rate limiting, OpenAPI, connectors | Post-PoC (stub S1) |
| M12 — Drill & Exercise | `preparedness` | Drill mode, SOP repository, exercise simulation | Post-PoC |

### 2.3 User Characteristics

See Section 1.3.2.

### 2.4 Constraints

See Section 1.3.3.

### 2.5 Assumptions and Dependencies

See Section 1.3.4.

---

## 3. Specific Requirements — Functional Requirements by Module

Requirements are organized by module. Each table includes:
- **Req ID** — Unique identifier
- **Statement** — Requirement text (abbreviated; full text in `consolidation/11_normalized_requirements.md`)
- **Priority** — CRITICAL / HIGH / MEDIUM / LOW
- **Sprint** — Target sprint
- **Acceptance Criteria** — GIVEN/WHEN/THEN format

**Source key:** Legacy = Part A | SD = Part B (System-Derived) | FC = Part C (Functional Completeness)


### 3.1 Module M1 — Hazard & Taxonomy Management (`hazards`)

**Overview:** Maintains the registry of hazard types, associated threshold configurations, severity scales, and administrative boundary geometries. Reference data driving all downstream modules.

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0059 | Configure trigger thresholds independently per hazard type | CRITICAL | S1 | GIVEN a hazard type exists WHEN admin sets a threshold THEN threshold is saved and active for that hazard type only |
| MHEWS-FR-0074 | Configure severity scales per hazard type | HIGH | S1 | GIVEN a hazard type WHEN admin defines severity scale THEN scale values are stored and retrievable per hazard |
| MHEWS-FR-0110 | Multi-hazard risk identification, assessment, storage, analysis | CRITICAL | S1 | GIVEN the system is running WHEN admin navigates to hazard registry THEN all registered hazard types with metadata are displayed |
| MHEWS-FR-0154 | Categorize hazards into predefined groups | HIGH | S1 | GIVEN a new hazard WHEN admin assigns a category THEN hazard is stored under the correct group |
| MHEWS-FR-0156 | Deactivate hazard types without deleting history | MEDIUM | S1-S2 | GIVEN an active hazard type WHEN admin deactivates it THEN is_active=false AND historical records remain intact |
| MHEWS-FR-0157 | Support hierarchical parent-child hazard relationships | MEDIUM | S1-S2 | GIVEN hazard types exist WHEN admin sets parent-child link THEN hierarchy is stored and queryable |
| MHEWS-FR-0158 | Manage hazard types and metadata in central registry | CRITICAL | S1 | GIVEN the system WHEN admin creates a hazard type with code, name, description THEN it is persisted and unique by code |
| MHEWS-FR-0189 | Threshold visualizer for layer styling per hazard | MEDIUM | S1-S2 | GIVEN threshold configs exist WHEN admin opens visualizer THEN layer colour scales reflect configured thresholds |
| MHEWS-FR-0194 | Configure and modify hazard trigger rules | CRITICAL | S1 | GIVEN a hazard type WHEN admin edits trigger threshold THEN new threshold is active and old value is audit-logged |
| MHEWS-FR-0195 | Apply hazard-specific threshold overrides per region | HIGH | S1 | GIVEN a threshold and an admin boundary WHEN admin creates regional override THEN override applies only to that area |
| MHEWS-FR-0211 | Store hazard attributes: severity scales, frequency, scope, seasonality | HIGH | S1 | GIVEN a hazard type WHEN admin edits metadata fields THEN all attributes are persisted |
| MHEWS-FR-0215 | Hazard encyclopedia with reference cards | LOW | S1-S2 | GIVEN registered hazard types WHEN user views encyclopedia THEN reference card with description is displayed |
| MHEWS-FR-0231 | Threshold prompt logic — admin rule creator interface | HIGH | S1 | GIVEN the admin UI WHEN admin creates a threshold rule THEN rule is saved with operator, value, unit, hazard type |
| MHEWS-FR-0247 | Register hazard categories: meteo, hydro, geo, bio, tech | CRITICAL | S1 | GIVEN the hazard registry WHEN admin adds a hazard THEN it is categorized under a valid domain |
| MHEWS-FR-0248 | Store Streamlined 2025 hazard list as version-controlled registry | HIGH | S1 | GIVEN the system WHEN hazard list is loaded THEN it is versioned and auditable |
| MHEWS-FR-0249 | Search hazard registry by name, category, identifier | MEDIUM | S1-S2 | GIVEN hazard types exist WHEN user searches by name or code THEN matching results are returned |
| MHEWS-FR-0251 | Select hazards when creating alerts or analyses | HIGH | S1 | GIVEN hazard types exist WHEN user opens hazard selector THEN active hazard types are listed for selection |
| MHEWS-FR-0252 | Modify hazard classifications and thresholds | HIGH | S1 | GIVEN an existing hazard WHEN admin modifies classification THEN change is saved and audit-logged |
| MHEWS-FR-0324 | Regional customization of hazard types and severity scales | HIGH | S1 | GIVEN a country deployment WHEN admin customizes severity scale THEN country-specific scale is applied |
| MHEWS-FR-0342 | Map hazard types to configurable severity scales | HIGH | S1 | GIVEN a hazard type WHEN admin maps severity scale THEN mapping is stored and used in alert authoring |
| MHEWS-FC-INV-05 | ThresholdConfig input validation | HIGH | S1 | GIVEN threshold input WHEN operator not in {GT,GTE,LT,LTE,EQ} or value not finite THEN HTTP 422 with field-level errors |


### 3.2 Module M2 — Risk & Scenario Modelling (`risk`)

**Overview:** Enables analysts to define what-if scenarios combining hazard types, areas, and model configurations. Receives after-action calibration signals from M8. Post-PoC module.

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0021 | Calculate baseline risk thresholds using historical data | HIGH | S2 | GIVEN historical hazard data WHEN analyst runs baseline calculation THEN risk thresholds are computed and stored |
| MHEWS-FR-0057 | Cross-hazard logic triggering secondary hazard analysis | HIGH | S2 | GIVEN a primary hazard event WHEN cross-hazard rule matches THEN secondary hazard analysis is triggered |
| MHEWS-FR-0068 | Configure hazard thresholds and define exposed asset layers | HIGH | S2 | GIVEN admin access WHEN thresholds and asset layers are configured THEN they are used in impact calculation |
| MHEWS-FR-0108 | Digital twin simulation layer without affecting live operations | MEDIUM | S2-S3 | GIVEN simulation mode WHEN analyst runs scenario THEN no live alerts or dispatch are triggered |
| MHEWS-FR-0164 | Risk query tool with side-panel for zone risk indicators | HIGH | S2 | GIVEN the map WHEN user clicks a zone THEN risk profile panel opens with computed indicators |
| MHEWS-FR-0179 | Save impact analysis configuration as reusable scenario | HIGH | S2 | GIVEN a completed analysis WHEN user clicks save THEN scenario config is persisted and reloadable |
| MHEWS-FR-0302 | Compute combined risk score for compounding hazards | HIGH | S2 | GIVEN overlapping hazard events WHEN computation runs THEN combined risk score is calculated |
| MHEWS-FR-0326 | Centralized risk data repository | HIGH | S2 | GIVEN risk data WHEN stored THEN hazard, exposure, vulnerability data is in a single controlled repository |
| MHEWS-FR-0327 | Compute risk levels combining hazard, exposure, vulnerability | HIGH | S2 | GIVEN input datasets WHEN risk profiling runs THEN risk level is computed and stored |
| MHEWS-FR-0332 | Save and reload impact analysis scenarios | HIGH | S2 | GIVEN a scenario config WHEN user saves THEN it is persisted; WHEN user loads THEN config is restored |
| MHEWS-FR-0333 | Simulate user-defined hazard intensity scenarios | HIGH | S2 | GIVEN scenario parameters WHEN simulation executes THEN results are computed without affecting live data |


### 3.3 Module M3 — Data Ingestion & Monitoring (`ingestion`)

**Overview:** Manages external data source registration, scheduled polling, observation storage, threshold evaluation, and breach event lifecycle. Entry point of the alert pipeline.

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0023 | Ingest automated data feeds from satellite APIs, IoT sensors, forecast outputs | CRITICAL | S1 | GIVEN a configured data source WHEN poll executes THEN observations are stored with source metadata |
| MHEWS-FR-0034 | Detect monitoring data anomalies exceeding thresholds | HIGH | S1 | GIVEN incoming data WHEN value exceeds threshold THEN anomaly is detected and logged |
| MHEWS-FR-0035 | Authenticated API-based ingestion of external datasets | HIGH | S1 | GIVEN API credentials WHEN ingestion runs THEN data is fetched with authentication |
| MHEWS-FR-0036 | Ingest hazard, exposure, vulnerability datasets from multiple sources | HIGH | S1 | GIVEN multiple configured sources WHEN poll cycle runs THEN data from all sources is ingested |
| MHEWS-FR-0037 | OGC-compliant API ingestion (WMS/WFS) | HIGH | S1 | GIVEN an OGC endpoint WHEN ingestion runs THEN WMS/WFS data is fetched and stored |
| MHEWS-FR-0038 | Provisional data status pending manual verification | MEDIUM | S1-S2 | GIVEN ingested data WHEN marked provisional THEN it is excluded from threshold evaluation until verified |
| MHEWS-FR-0039 | Ingest real-time hazard data from external sources | CRITICAL | S1 | GIVEN a real-time feed WHEN data arrives THEN it is stored within 5 seconds |
| MHEWS-FR-0040 | Disable faulty sensors to prevent false alarms | HIGH | S1 | GIVEN a faulty sensor WHEN operator disables it THEN its data is excluded from threshold evaluation |
| MHEWS-FR-0041 | Validate uploaded shapefiles and metadata | MEDIUM | S1-S2 | GIVEN a shapefile upload WHEN validation runs THEN CRS, required fields, and geometry integrity are checked |
| MHEWS-FR-0051 | Initiate alert draft workflow on threshold breach | CRITICAL | S1 | GIVEN a threshold breach WHEN signal fires THEN a draft alert is queued for operator review |
| MHEWS-FR-0052 | Scheduled batch ingestion of datasets | MEDIUM | S1-S2 | GIVEN a batch schedule WHEN time arrives THEN ingestion job runs and stores results |
| MHEWS-FR-0060 | Simultaneous monitoring of multiple hazard types | CRITICAL | S1 | GIVEN multiple hazard types configured WHEN data arrives THEN all types are monitored concurrently |
| MHEWS-FR-0061 | Monitor sensor heartbeat and flag inactive sensors | HIGH | S1 | GIVEN sensor endpoints WHEN heartbeat missed for 3 cycles THEN sensor is flagged inactive |
| MHEWS-FR-0062 | Real-time hazard monitoring dashboard as default landing page | HIGH | S1 | GIVEN analyst login WHEN dashboard loads THEN real-time monitoring data is displayed |
| MHEWS-FR-0083 | Normalize multi-source datasets to common reference framework | HIGH | S1 | GIVEN multi-source data WHEN harmonization runs THEN temporal and spatial alignment is applied |
| MHEWS-FR-0084 | Data ingestion via API, manual upload, scheduled feeds | HIGH | S1 | GIVEN each ingestion method WHEN used THEN data is stored with consistent format |
| MHEWS-FR-0085 | Checksum validation on all ingested datasets | HIGH | S1 | GIVEN an ingested file WHEN checksum is computed THEN it matches declared hash or rejection occurs |
| MHEWS-FR-0087 | Compute data quality metrics for key datasets | MEDIUM | S1-S2 | GIVEN an ingested dataset WHEN quality check runs THEN completeness, timeliness, accuracy metrics are stored |
| MHEWS-FR-0091 | Dataset version history with audit trails | HIGH | S1 | GIVEN a dataset WHEN new version is ingested THEN previous version is preserved with timestamp |
| MHEWS-FR-0094 | Require approval before activating new dataset | HIGH | S1 | GIVEN an ingested dataset WHEN operator approves THEN status changes to active |
| MHEWS-FR-0097 | Roll back to previous dataset version | MEDIUM | S1-S2 | GIVEN multiple dataset versions WHEN operator selects rollback THEN prior version is restored |
| MHEWS-FR-0106 | Continuous real-time monitoring of all configured streams | CRITICAL | S1 | GIVEN configured data streams WHEN system is running THEN all streams are polled per schedule |
| MHEWS-FR-0133 | Integrate external meteorological and satellite data APIs | HIGH | S1 | GIVEN API configs WHEN ingestion runs THEN external data is fetched and stored |
| MHEWS-FR-0139 | Real-time health status indicators for all ingestion pipelines | HIGH | S1 | GIVEN data sources WHEN health check runs THEN status (online/offline/degraded) is displayed |
| MHEWS-FR-0180 | Data health status per connected source (live/outdated) | HIGH | S1 | GIVEN sources WHEN last update exceeds 30 days THEN status shows outdated |
| MHEWS-FR-0181 | Display source attribution for each dataset | MEDIUM | S1-S2 | GIVEN a dataset WHEN user views it THEN source attribution is displayed |
| MHEWS-FR-0182 | Edit dataset metadata fields | MEDIUM | S1-S2 | GIVEN a dataset WHEN authorized user edits metadata THEN changes are saved and audit-logged |
| MHEWS-FR-0183 | Upload additional datasets | MEDIUM | S1-S2 | GIVEN admin access WHEN file is uploaded THEN dataset is validated and stored |
| MHEWS-FR-0185 | Display current global SPI status on dashboard | HIGH | S1 | GIVEN SPI data feed WHEN dashboard loads THEN current drought status is displayed |
| MHEWS-FR-0186 | Display water level data for key river basins | HIGH | S1 | GIVEN GloFAS feed WHEN dashboard loads THEN water levels are displayed per basin |
| MHEWS-FR-0187 | Display active cyclone/storm count | HIGH | S1 | GIVEN GDACS feed WHEN dashboard loads THEN active storm count is displayed |
| MHEWS-FR-0188 | Auto-refresh toggle re-fetching map layers at configurable interval | MEDIUM | S1-S2 | GIVEN auto-refresh enabled WHEN interval elapses THEN map layers are refreshed |
| MHEWS-FR-0190 | Click-to-verify: change flagged alert from automated to verified | HIGH | S1 | GIVEN a flagged event WHEN operator clicks verify THEN status changes to verified |
| MHEWS-FR-0218 | Hazard data inventory with metadata and location | MEDIUM | S1-S2 | GIVEN datasets WHEN user opens inventory THEN catalogue with metadata is displayed |
| MHEWS-FR-0228 | Live feed table: timestamp, hazard, location, intensity, source | HIGH | S1 | GIVEN incoming data WHEN dashboard loads THEN live feed table is populated in real-time |
| MHEWS-FR-0229 | Sensor health dashboard pinging endpoints at 60s intervals | HIGH | S1 | GIVEN sensor endpoints WHEN health check runs THEN per-source status grid is displayed |
| MHEWS-FR-0230 | Data viewer for satellite imagery and open datasets | MEDIUM | S1-S2 | GIVEN external APIs WHEN user opens viewer THEN satellite imagery is rendered |
| MHEWS-FR-0232 | Evaluate incoming data against thresholds on each packet | CRITICAL | S1 | GIVEN threshold config WHEN data packet arrives THEN threshold evaluation runs within 5 seconds |
| MHEWS-FR-0234 | Generate real-time heatmaps from intensity values | HIGH | S1 | GIVEN monitoring data WHEN heatmap renders THEN intensity values drive colour scale |
| MHEWS-FR-0235 | Pop-up time-series charts for last 24h monitoring trends | HIGH | S1 | GIVEN a data point WHEN user clicks THEN 24h trend chart is displayed |
| MHEWS-FR-0266 | Dashboard panel for real-time drought and flood indicators | HIGH | S1 | GIVEN indicator data WHEN panel loads THEN drought/flood indicators are displayed |
| MHEWS-FR-0273 | Retry data ingestion for transient failures | HIGH | S1 | GIVEN a poll failure WHEN retry policy triggers THEN up to 3 retries with exponential backoff |
| MHEWS-FR-0276 | Store ingestion timestamps in UTC | MEDIUM | S1-S2 | GIVEN any ingestion WHEN timestamp is stored THEN it is in UTC format |
| MHEWS-FR-0284 | Reject datasets failing schema or metadata validation | HIGH | S1 | GIVEN an invalid dataset WHEN validation fails THEN dataset is rejected with error details |
| MHEWS-FR-0285 | Manual dataset upload through admin interface | MEDIUM | S1-S2 | GIVEN admin UI WHEN user uploads file THEN it is validated and stored |
| MHEWS-FR-0288 | Validate required metadata fields before activation | HIGH | S1 | GIVEN a dataset WHEN activation is requested THEN all required metadata must be populated |
| MHEWS-FR-0289 | Validate metadata consistency across modules | MEDIUM | S1-S2 | GIVEN metadata WHEN cross-module check runs THEN inconsistencies are flagged |
| MHEWS-FR-0300 | Escalate monitoring alerts to roles based on severity | HIGH | S1 | GIVEN a severity threshold WHEN exceeded THEN appropriate role is notified |
| MHEWS-FR-0309 | Ingest and harmonize climate data from multiple sources | HIGH | S1 | GIVEN multiple climate APIs WHEN data is fetched THEN harmonization is applied |
| MHEWS-FR-0322 | Integrate multi-source real-time monitoring inputs | CRITICAL | S1 | GIVEN configured sources WHEN data arrives THEN all sources are integrated |
| MHEWS-FR-0323 | Render dynamic GIS heatmaps for real-time parameters | HIGH | S1 | GIVEN monitoring data WHEN heatmap renders THEN dynamic update reflects latest values |
| MHEWS-FR-0331 | Integrate satellite-based monitoring data | HIGH | S1 | GIVEN satellite feed WHEN ingestion runs THEN satellite observations are stored |
| MHEWS-FR-0334 | Configure scheduled ingestion jobs per source | MEDIUM | S1-S2 | GIVEN a data source WHEN schedule is configured THEN jobs run at defined intervals |
| MHEWS-FR-0335 | Validate incoming datasets against predefined schemas | HIGH | S1 | GIVEN a schema WHEN data arrives THEN validation runs and rejects non-conforming data |
| MHEWS-FR-0340 | Ingest data from meteo, hydro, seismic, environmental sensors | CRITICAL | S1 | GIVEN sensor feeds WHEN polling runs THEN multi-type data is ingested |
| MHEWS-FR-0341 | Monitor external APIs/sensors at 60-second intervals | HIGH | S1 | GIVEN sensor endpoints WHEN interval elapses THEN availability check runs |
| MHEWS-FR-0355 | Continuously monitor all configured hazard thresholds | CRITICAL | S1 | GIVEN active thresholds WHEN system is running THEN continuous evaluation occurs |
| MHEWS-FC-ERR-06 | Mark source FAILED on poll failure, retry with backoff, notify admin | HIGH | S1 | GIVEN a poll failure WHEN error occurs THEN source status=FAILED, backoff retry starts, admin notified |
| MHEWS-FC-STM-03 | ThresholdBreachEvent state machine (OPEN→ACK→CAP_DRAFTED/RESOLVED/DISMISSED) | HIGH | S1 | GIVEN a breach WHEN state transition is attempted THEN only valid transitions succeed; invalid returns 409 |
| MHEWS-FC-STM-05 | DataSource state machine (ACTIVE/PAUSED/DEGRADED/INACTIVE) | MEDIUM | S1-S2 | GIVEN 3 consecutive failures WHEN evaluation runs THEN source transitions to DEGRADED |
| MHEWS-FC-INV-09 | Data source URL validation (HTTPS, reachability, SSRF protection) | CRITICAL | S1 | GIVEN a URL WHEN it uses private IP or non-HTTPS THEN HTTP 422 is returned |
| MHEWS-SD-MAP-01 | Use MapLibre GL JS as primary map rendering engine | HIGH | S1 | GIVEN the application WHEN map component loads THEN MapLibre GL JS is used |
| MHEWS-SD-MAP-03 | OGC WMS/WFS client adapter with layer toggle and opacity | HIGH | S1 | GIVEN an OGC endpoint WHEN layer is added THEN toggle and opacity controls work |
| MHEWS-SD-MAP-04 | Time-slider UI for temporal geospatial data | MEDIUM | S1-S2 | GIVEN temporal data WHEN slider moves THEN displayed layer updates within 1 second |
| MHEWS-SD-STREAM-02 | SSE endpoint for real-time dashboard updates | HIGH | S1 | GIVEN an authenticated session WHEN event occurs THEN SSE pushes update within 60 seconds |
| MHEWS-SD-FEEDBACK-04 | Geographic cluster summaries of community reports every 2 hours | HIGH | S1 | GIVEN approved reports WHEN Celery Beat runs THEN cluster summary is generated per district |


### 3.4 Module M4 — Forecasting & Nowcasting Engine (`forecasting`)

**Overview:** Manages forecast model registration, scheduled and on-demand execution, model run lifecycle, and forecast visualization. Post-PoC module (stub in Sprint 1).

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0006 | Monitor AI model availability and alert on failure | HIGH | S2 | GIVEN a model WHEN availability check fails THEN admin alert is generated |
| MHEWS-FR-0007 | Isolate AI model runtime from core services | HIGH | S2 | GIVEN model execution WHEN running THEN it uses separate resource pool from API/ingestion |
| MHEWS-FR-0012 | Maintain registry metadata for AI training datasets | MEDIUM | S2-S3 | GIVEN a training dataset WHEN registered THEN metadata (name, version, source) is stored |
| MHEWS-FR-0053 | Configuration placeholder for AI bias monitoring | LOW | S2-S3 | GIVEN admin settings WHEN bias monitoring config is opened THEN placeholder fields exist |
| MHEWS-FR-0058 | Support short/medium/long-term forecast horizons | HIGH | S2 | GIVEN a forecast model WHEN configured THEN it specifies supported horizon range |
| MHEWS-FR-0069 | Side-by-side comparison of multiple model outputs | MEDIUM | S2-S3 | GIVEN two model outputs WHEN comparison view opens THEN outputs are displayed side-by-side |
| MHEWS-FR-0072 | Attach confidence intervals to forecast outputs | HIGH | S2 | GIVEN a forecast output WHEN generated THEN confidence interval is included |
| MHEWS-FR-0107 | Integrate deterministic forecasting model connectors | HIGH | S2 | GIVEN a deterministic model WHEN registered THEN connector is configured and executable |
| MHEWS-FR-0124 | Flag potential model drift conditions | MEDIUM | S2-S3 | GIVEN model metrics WHEN drift threshold exceeded THEN analyst is notified |
| MHEWS-FR-0127 | Compute drought indices (Dry Spell, SMCI, IDSI) | HIGH | S2 | GIVEN input data WHEN computation runs THEN drought indices are calculated for selected area |
| MHEWS-FR-0128 | Retrieve forecast data from ECMWF API | HIGH | S2 | GIVEN ECMWF credentials WHEN fetch runs THEN forecast data is retrieved and stored |
| MHEWS-FR-0140 | Compute flood indicators from satellite and precipitation data | HIGH | S2 | GIVEN input data WHEN computation runs THEN flood indicators are calculated |
| MHEWS-FR-0141 | Archive all forecast datasets with metadata | HIGH | S2 | GIVEN a forecast run WHEN completed THEN output is archived with metadata |
| MHEWS-FR-0142 | Select forecast horizons (short/medium/long-term) | MEDIUM | S2-S3 | GIVEN forecast UI WHEN user selects horizon THEN corresponding data is displayed |
| MHEWS-FR-0143 | Normalize forecast inputs to canonical internal schema | HIGH | S2 | GIVEN raw forecast data WHEN normalization runs THEN canonical schema is applied |
| MHEWS-FR-0144 | Generate predictive hazard outputs through forecast connectors | CRITICAL | S2 | GIVEN a registered model WHEN execution triggers THEN predictive output is generated |
| MHEWS-FR-0191 | Select and compare forecast outputs from different sources | MEDIUM | S2-S3 | GIVEN multiple model outputs WHEN user selects comparison THEN outputs are displayed together |
| MHEWS-FR-0192 | Lead-time slider for future forecast periods | MEDIUM | S2-S3 | GIVEN forecast data WHEN slider moves THEN forecast for selected time is displayed |
| MHEWS-FR-0199 | Synthesize monitoring data into monthly bulletin via LLM | MEDIUM | S2-S3 | GIVEN monitoring data WHEN generation triggers THEN formatted bulletin is produced |
| MHEWS-FR-0200 | Append map snapshots and charts to generated reports | MEDIUM | S2-S3 | GIVEN a generated report WHEN rendering THEN map snapshots and charts are embedded |
| MHEWS-FR-0233 | Model hazard spread likelihood to communities | MEDIUM | S2-S3 | GIVEN monitoring data and spatial datasets WHEN propagation runs THEN spread probabilities are computed |
| MHEWS-FR-0246 | Model downstream hazard spread probabilities | HIGH | S2 | GIVEN configured propagation logic WHEN model runs THEN downstream probabilities are output |
| MHEWS-FR-0290 | Include model identifier and version in AI-generated outputs | HIGH | S2 | GIVEN an AI output WHEN generated THEN model_id and version are attached |
| MHEWS-FR-0292 | Generate model comparison reports | MEDIUM | S2-S3 | GIVEN multiple model runs WHEN comparison report generates THEN side-by-side metrics are displayed |
| MHEWS-FR-0293 | Manage model deprecation preventing use of deprecated models | MEDIUM | S2-S3 | GIVEN a deprecated model WHEN execution is attempted THEN it is blocked with a deprecation message |
| MHEWS-FR-0294 | Activate new model version without service downtime | HIGH | S2 | GIVEN a new model version WHEN hot-swap activates THEN service continues without interruption |
| MHEWS-FR-0295 | Store model metadata (name, version, type, source) | MEDIUM | S2-S3 | GIVEN a model WHEN registered THEN metadata is stored and queryable |
| MHEWS-FR-0296 | Compute model performance indicators | HIGH | S2 | GIVEN model runs WHEN metrics are computed THEN performance indicators are stored |
| MHEWS-FR-0297 | Version-controlled model registry | HIGH | S2 | GIVEN a model WHEN new version is registered THEN previous version is preserved |
| MHEWS-FR-0298 | Export model registry data in standard format | MEDIUM | S2-S3 | GIVEN model registry WHEN export is requested THEN data is output in JSON/CSV |
| MHEWS-FR-0299 | Rollback to prior model version | HIGH | S2 | GIVEN model versions WHEN rollback is requested THEN prior version is activated |
| MHEWS-FR-0315 | Pluggable forecast connector framework | HIGH | S2 | GIVEN the framework WHEN new connector is added THEN no core system modification is needed |
| MHEWS-FR-0319 | Integrate probabilistic forecasting connectors | HIGH | S2 | GIVEN a probabilistic model WHEN registered THEN connector is configured and executable |
| MHEWS-FR-0347 | Select geographic polygons and extract forecast parameters | MEDIUM | S2-S3 | GIVEN a polygon selection WHEN query runs THEN forecast values are extracted |
| MHEWS-FR-0357 | Perform time-series analysis of hazard indicator trends | HIGH | S2 | GIVEN indicator data WHEN analysis runs THEN trends are computed and stored |
| MHEWS-FR-0358 | Graphical time-series visualization of parameters | HIGH | S2 | GIVEN selected area WHEN chart loads THEN time-series graph is rendered |
| MHEWS-FR-0365 | Toggle visualization of individual forecast parameters | MEDIUM | S2-S3 | GIVEN forecast display WHEN user toggles a parameter THEN visibility changes without page reload |
| MHEWS-FC-ERR-10 | Model run failure handling: set FAILED, emit audit event, notify | HIGH | S2 | GIVEN a failed run WHEN error occurs THEN status=FAILED, AuditEvent emitted, operator notified |
| MHEWS-FC-STM-06 | ModelRun state machine (QUEUED→RUNNING→COMPLETED/FAILED/CANCELLED) | HIGH | S2 | GIVEN a model run WHEN transition is attempted THEN only valid transitions succeed |
| MHEWS-SD-COMPUTE-01 | Model Execution Service — async job runner | HIGH | S2 | GIVEN a model run request WHEN queued THEN ModelJob tracks lifecycle (QUEUED→RUNNING→COMPLETED/FAILED) |
| MHEWS-SD-COMPUTE-02 | Model packaging as Docker image with standard entrypoint | HIGH | S2 | GIVEN a model WHEN packaged THEN `python run.py --input/--output` entrypoint works |
| MHEWS-SD-COMPUTE-03 | Model resource isolation from API/ingestion | HIGH | S2 | GIVEN model execution WHEN running THEN separate CPU/memory limits are enforced |
| MHEWS-SD-COMPUTE-04 | ModelJob data entity | HIGH | S2 | GIVEN a model run WHEN created THEN ModelJob with all required fields is persisted |
| MHEWS-SD-COMPUTE-05 | Auto-terminate runs exceeding 2x expected duration | HIGH | S2 | GIVEN a running model WHEN duration exceeds 2x limit THEN status is set to FAILED |
| MHEWS-SD-GDB-03 | Forecast grid spatial point query within 200ms | MEDIUM | S2-S3 | GIVEN a coordinate WHEN point query runs THEN forecast value is returned within 200ms |
| MHEWS-SD-MODEL-01 | On-demand model run API (POST /api/forecasts/runs/) | HIGH | S2 | GIVEN an authenticated request WHEN POST is sent THEN ModelRun is created with trigger=ON_DEMAND |
| MHEWS-SD-MODEL-02 | Scheduled model run on NWP ingest completion | HIGH | S2 | GIVEN NWP ingest completes WHEN chain triggers THEN model run starts automatically |
| MHEWS-SD-UQ-01 | Confidence score per-model metadata contract | HIGH | S2 | GIVEN a model output WHEN generated THEN confidence field (score, method, tier) is included |
| MHEWS-SD-UQ-02 | Confidence display with colour-coded tier | MEDIUM | S2-S3 | GIVEN a forecast display WHEN confidence is shown THEN colour + text label are used |


### 3.5 Module M5 — Impact Analysis & Exposure Modelling (`impact`)

**Overview:** Manages exposure datasets, pre-computed impact layers, on-demand spatial impact queries, and qualitative impact narratives via LLM. Post-PoC module (stub in Sprint 1).

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0002 | Toggle hazard, exposure, vulnerability layers in map interface | MEDIUM | S2-S3 | GIVEN map layers WHEN user toggles a layer THEN visibility changes without page reload |
| MHEWS-FR-0003 | Support static and dynamic datasets in GIS visualization | MEDIUM | S2-S3 | GIVEN both dataset types WHEN displayed THEN static and dynamic layers render correctly |
| MHEWS-FR-0020 | Store impact metrics snapshot at alert authorization time | HIGH | S2 | GIVEN alert approval WHEN authorization occurs THEN impact snapshot is archived with the CAP |
| MHEWS-FR-0073 | Configure impact threshold values for alert triggering | HIGH | S2 | GIVEN impact thresholds WHEN admin configures values THEN they are used in impact evaluation |
| MHEWS-FR-0078 | Display critical infrastructure within hazard zones | HIGH | S2 | GIVEN hazard polygon WHEN overlay runs THEN critical assets within zone are highlighted |
| MHEWS-FR-0131 | Manage exposure and vulnerability datasets for impact modelling | HIGH | S2 | GIVEN datasets WHEN managed via UI THEN CRUD operations work with validation |
| MHEWS-FR-0132 | Store geospatial datasets for population, infrastructure, facilities | HIGH | S2 | GIVEN geospatial data WHEN stored THEN PostGIS geometry and attributes are persisted |
| MHEWS-FR-0150 | GIS-based map visualization of hazard and risk layers | HIGH | S2 | GIVEN risk layers WHEN map loads THEN layers render with correct styling |
| MHEWS-FR-0153 | Graph-based asset interdependency modelling | MEDIUM | S2-S3 | GIVEN asset dependencies WHEN cascade analysis runs THEN dependency graph is computed |
| MHEWS-FR-0159 | Adjust opacity of active map layers via slider | LOW | S2-S3 | GIVEN an active layer WHEN opacity slider moves THEN transparency changes in real-time |
| MHEWS-FR-0160 | Display vulnerability layers grouped by category | MEDIUM | S2-S3 | GIVEN vulnerability data WHEN layer panel loads THEN layers are grouped by category |
| MHEWS-FR-0161 | Split-view map mode for comparative analysis | LOW | S2-S3 | GIVEN two layer sets WHEN split-view toggles THEN side-by-side maps render |
| MHEWS-FR-0162 | Pop-up info panel on polygon selection showing metadata | MEDIUM | S2-S3 | GIVEN a polygon WHEN user clicks THEN metadata panel appears |
| MHEWS-FR-0163 | Support community-sourced GIS layers including sacred sites | LOW | S2-S3 | GIVEN community layers WHEN loaded THEN they are visually differentiated from standard layers |
| MHEWS-FR-0165 | Time-slider control for historical time periods | MEDIUM | S2-S3 | GIVEN historical data WHEN slider moves THEN corresponding layer is displayed |
| MHEWS-FR-0166 | Switch between satellite, streets, topographic basemaps | LOW | S2-S3 | GIVEN basemap options WHEN user selects THEN basemap switches without page reload |
| MHEWS-FR-0167 | Dynamic map legend reflecting active layer colour scale | MEDIUM | S2-S3 | GIVEN an active layer WHEN legend renders THEN it matches layer's colour scale |
| MHEWS-FR-0168 | Standard map controls: zoom, search, tilt | MEDIUM | S2-S3 | GIVEN map component WHEN user interacts THEN zoom/search/tilt controls work |
| MHEWS-FR-0169 | Download current map view as image | LOW | S2-S3 | GIVEN a map view WHEN download is clicked THEN PNG image is generated |
| MHEWS-FR-0170 | Display demographic data disaggregated by gender, age | MEDIUM | S2-S3 | GIVEN population data WHEN area is selected THEN demographics are displayed |
| MHEWS-FR-0171 | List critical assets within selected zone from spatial queries | HIGH | S2 | GIVEN a zone WHEN query runs THEN assets within the zone are listed |
| MHEWS-FR-0172 | Generate downloadable district risk factsheet as PDF | MEDIUM | S2-S3 | GIVEN a district WHEN factsheet is requested THEN PDF is generated and downloadable |
| MHEWS-FR-0173 | Display qualitative vulnerability assessment text | LOW | S2-S3 | GIVEN a selected area WHEN panel opens THEN qualitative assessment is shown |
| MHEWS-FR-0174 | Display cascading impact pathways from selected asset | MEDIUM | S2-S3 | GIVEN an asset WHEN selected THEN cascade pathways are displayed |
| MHEWS-FR-0175 | Step 1: Select hazard layer for impact analysis | HIGH | S2 | GIVEN impact workflow WHEN step 1 opens THEN hazard layers are selectable |
| MHEWS-FR-0176 | Step 2: Select asset layer for impact analysis | HIGH | S2 | GIVEN step 2 WHEN opened THEN asset layers are selectable |
| MHEWS-FR-0177 | Step 3: Select impact metric type | HIGH | S2 | GIVEN step 3 WHEN opened THEN metric types (economic loss, population) are selectable |
| MHEWS-FR-0178 | Execute spatial intersection of hazard and asset layers | HIGH | S2 | GIVEN hazard+asset layers WHEN analysis runs THEN result map layer is produced |
| MHEWS-FR-0193 | Spatial query combining forecast with exposure for impact estimate | HIGH | S2 | GIVEN forecast + exposure WHEN query runs THEN projected impact is computed |
| MHEWS-FR-0219 | Toggle open dataset layers (OSM, WorldPop) on map | MEDIUM | S2-S3 | GIVEN open dataset layers WHEN toggled THEN visibility changes |
| MHEWS-FR-0220 | Spatial harmonization: reproject, warp, align CRS | HIGH | S2 | GIVEN disparate CRS datasets WHEN harmonization runs THEN datasets are aligned to WGS84 |
| MHEWS-FR-0221 | Zonal statistics within hazard polygon | HIGH | S2 | GIVEN a hazard polygon WHEN zonal stats run THEN population/metric sums are computed |
| MHEWS-FR-0222 | Store population data disaggregated by age, sex, disability | HIGH | S2 | GIVEN demographic data WHEN stored THEN disaggregation fields are persisted |
| MHEWS-FR-0223 | Visualize vulnerability index scores as heatmaps | MEDIUM | S2-S3 | GIVEN DSVI/INFORM data WHEN map renders THEN heatmap overlay is displayed |
| MHEWS-FR-0224 | Map toggle for female-headed household concentrations | MEDIUM | S2-S3 | GIVEN gender data WHEN toggle activates THEN zones are highlighted |
| MHEWS-FR-0225 | Temporal slider for historical and monitoring data | MEDIUM | S2-S3 | GIVEN time-series data WHEN slider moves THEN corresponding data is displayed |
| MHEWS-FR-0236 | Global search bar with geocoding | MEDIUM | S2-S3 | GIVEN a search query WHEN entered THEN location is found and map centers on it |
| MHEWS-FR-0254 | Overlay active hazard layers with exposure datasets | HIGH | S2 | GIVEN hazard + exposure WHEN overlay renders THEN both layers display simultaneously |
| MHEWS-FR-0260 | Compute confidence metrics for impact model outputs | HIGH | S2 | GIVEN impact output WHEN generated THEN confidence metric is computed and stored |
| MHEWS-FR-0261 | Export impact analysis results in CSV, JSON, GIS formats | MEDIUM | S2-S3 | GIVEN results WHEN export is requested THEN file is generated in selected format |
| MHEWS-FR-0262 | Generate heatmaps visualizing impact severity | HIGH | S2 | GIVEN impact results WHEN map renders THEN severity heatmap is displayed |
| MHEWS-FR-0264 | Combine hazard forecast with exposure for impact forecasts | CRITICAL | S2 | GIVEN forecast + exposure WHEN impact computation runs THEN projected impact is calculated |
| MHEWS-FR-0286 | Select and switch between map base layers | MEDIUM | S2-S3 | GIVEN base layer options WHEN user selects THEN base layer switches |
| MHEWS-FR-0336 | Model cascading impacts using asset dependency graphs | HIGH | S2 | GIVEN asset graph WHEN cascade analysis runs THEN downstream impacts are computed |
| MHEWS-FR-0337 | Store impact metrics disaggregated by sector | MEDIUM | S2-S3 | GIVEN impact results WHEN stored THEN sector disaggregation is preserved |
| MHEWS-FR-0345 | Aggregate impact results at configurable boundary levels | HIGH | S2 | GIVEN boundaries WHEN aggregation runs THEN results are grouped by selected level |
| MHEWS-FR-0346 | CRS reprojection and spatial harmonization engine | HIGH | S2 | GIVEN disparate CRS data WHEN engine runs THEN all data is harmonized to WGS84 |
| MHEWS-FR-0354 | Temporal visualization slider for historical events | MEDIUM | S2-S3 | GIVEN historical data WHEN slider moves THEN event data for that time is displayed |
| MHEWS-FR-0366 | Calculate vulnerability indicators from socio-economic parameters | HIGH | S2 | GIVEN socio-economic data WHEN calculation runs THEN vulnerability indices are computed |
| MHEWS-FR-0367 | Integrate vulnerability datasets into impact modelling | HIGH | S2 | GIVEN vulnerability data WHEN integrated THEN impact models use vulnerability inputs |
| MHEWS-FR-0370 | Compute zonal statistics within hazard polygons | HIGH | S2 | GIVEN a hazard polygon WHEN zonal stats execute THEN spatial impact is quantified |
| MHEWS-FC-STM-10 | LLMOutput state machine (REQUESTED→GENERATED→ACCEPTED/REJECTED/EXPIRED) | HIGH | S2 | GIVEN an LLM output WHEN state transition occurs THEN only valid transitions succeed |
| MHEWS-SD-STORE-03 | Exposure dataset upload pipeline (GeoJSON/Shapefile → PostGIS) | HIGH | S2 | GIVEN a GeoJSON upload WHEN pipeline runs THEN data is validated, converted to PostGIS, raw file stored |


### 3.6 Module M6 — Alert Authoring / CAP (`alerts`)

**Overview:** Manages the complete CAP alert lifecycle: template management, draft creation, field editing, AI narrative integration, XSD validation, dual-authorization approval, and immutable CAP package generation. Central module of the warning pipeline.

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0001 | Include confidence score in all AI-generated outputs | HIGH | S1 | GIVEN an AI output WHEN generated THEN confidence score is attached |
| MHEWS-FR-0008 | Generate draft risk narratives using integrated LLM | HIGH | S1 | GIVEN a CAP draft WHEN narrative generation triggers THEN LLM produces draft text |
| MHEWS-FR-0015 | Alerts include hazard type, severity, location, validity, protective actions | CRITICAL | S1 | GIVEN an alert WHEN created THEN all required CAP fields are present |
| MHEWS-FR-0016 | Detect and prevent duplicate alert issuance | HIGH | S1 | GIVEN an existing alert WHEN duplicate submission occurs THEN it is rejected |
| MHEWS-FR-0018 | Configurable alert escalation rules triggering role notifications | HIGH | S1 | GIVEN severity rules WHEN alert severity matches THEN configured roles are notified |
| MHEWS-FR-0019 | Manage end-to-end alert drafting and approval workflow | CRITICAL | S1 | GIVEN a threshold breach WHEN workflow starts THEN draft→approval→dispatch lifecycle executes |
| MHEWS-FR-0022 | Enforce integrity validation of alert metadata before dissemination | CRITICAL | S1 | GIVEN a CAP draft WHEN validation runs THEN all metadata fields are checked |
| MHEWS-FR-0026 | Filter and search alerts by hazard type, date, severity | MEDIUM | S1-S2 | GIVEN alerts exist WHEN user applies filters THEN matching alerts are returned |
| MHEWS-FR-0027 | Enforce defined alert state transitions | CRITICAL | S1 | GIVEN a CAP draft WHEN invalid transition is attempted THEN HTTP 409 is returned |
| MHEWS-FR-0028 | Link superseded alerts in traceable chain | HIGH | S1 | GIVEN a superseding alert WHEN created THEN link to superseded alert is stored |
| MHEWS-FR-0029 | Version-controlled alert templates | MEDIUM | S1-S2 | GIVEN a template WHEN updated THEN previous version is preserved |
| MHEWS-FR-0030 | Allow authorized users to update issued alerts | HIGH | S1 | GIVEN an issued alert WHEN update is submitted THEN amendment record is created |
| MHEWS-FR-0031 | Maintain full version history of each alert | HIGH | S1 | GIVEN alert changes WHEN history is queried THEN all versions are returned |
| MHEWS-FR-0032 | Issue all-clear alerts for active hazard events | CRITICAL | S1 | GIVEN an active alert WHEN all-clear is issued THEN CAP msgType=Cancel is generated |
| MHEWS-FR-0043 | Enforce role-based review and authorization before dissemination | CRITICAL | S1 | GIVEN a submitted draft WHEN approval is requested THEN only Approver role can approve |
| MHEWS-FR-0056 | CAP compliance for alert generation | CRITICAL | S1 | GIVEN alert generation WHEN CAP XML is produced THEN it passes XSD validation |
| MHEWS-FR-0063 | Configurable CAP version compatibility | MEDIUM | S1-S2 | GIVEN CAP profile config WHEN validation runs THEN selected version XSD is used |
| MHEWS-FR-0064 | Generate CAP-compliant XML alert documents | CRITICAL | S1 | GIVEN alert data WHEN XML generation runs THEN valid CAP XML is produced |
| MHEWS-FR-0089 | Include data source references in AI narratives | HIGH | S1 | GIVEN an AI narrative WHEN generated THEN source references are included |
| MHEWS-FR-0098 | Alert lifecycle states: Draft, Approval, Broadcast, False-Alarm, All-Clear | CRITICAL | S1 | GIVEN a CAP draft WHEN lifecycle progresses THEN valid states are enforced |
| MHEWS-FR-0099 | Hazard-specific alert validity periods | HIGH | S1 | GIVEN a hazard type WHEN alert is created THEN default validity period is applied |
| MHEWS-FR-0100 | Generate alerts compliant with CAP v1.2 | CRITICAL | S1 | GIVEN alert data WHEN XML generates THEN CAP v1.2 XSD validation passes |
| MHEWS-FR-0101 | LLM-assisted alert drafting for analyst review | HIGH | S1 | GIVEN a draft WHEN LLM assist triggers THEN narrative is generated for human review |
| MHEWS-FR-0121 | Allow authorized users to create draft alerts | CRITICAL | S1 | GIVEN Operator role WHEN draft creation is requested THEN new CAPDraft is created |
| MHEWS-FR-0122 | Auto-generate draft alerts upon threshold trigger events | CRITICAL | S1 | GIVEN a threshold breach WHEN signal fires THEN CAPDraft is auto-created from template |
| MHEWS-FR-0123 | Expire unsubmitted drafts after configurable duration | MEDIUM | S1-S2 | GIVEN a stale draft WHEN expiry check runs THEN draft status changes to EXPIRED |
| MHEWS-FR-0146 | Gender-sensitive narrative standards in alert templates | MEDIUM | S1-S2 | GIVEN a template WHEN configured THEN gender-sensitive language fields are available |
| MHEWS-FR-0196 | Auto-create draft alert when hazard trigger rule is met | CRITICAL | S1 | GIVEN a trigger match WHEN signal fires THEN draft is created with template fields |
| MHEWS-FR-0197 | Alert editor pre-populated with templates from Response Matrix | HIGH | S1 | GIVEN a trigger WHEN editor opens THEN fields are pre-populated from matching template |
| MHEWS-FR-0198 | Role-based access on approval action advancing to dissemination | CRITICAL | S1 | GIVEN a submitted draft WHEN Approver approves THEN alert advances to dispatch |
| MHEWS-FR-0202 | Pre-approved alert templates in multiple languages | HIGH | S1 | GIVEN templates WHEN language is selected THEN corresponding template loads |
| MHEWS-FR-0203 | Auto-populate alert fields from triggering event context | HIGH | S1 | GIVEN a threshold breach WHEN draft is created THEN hazard type, severity, area are auto-filled |
| MHEWS-FR-0204 | Attach response action cards to outgoing alerts | MEDIUM | S1-S2 | GIVEN an alert WHEN action card is attached THEN it is included in dispatch |
| MHEWS-FR-0237 | CAP logic engine converting approved warning to CAP v1.2 XML | CRITICAL | S1 | GIVEN an approved alert WHEN generation runs THEN CAP v1.2 XML is produced |
| MHEWS-FR-0238 | Two-step alert dispatch authorization (ARM→TARGET→FIRE) | CRITICAL | S1 | GIVEN dispatch request WHEN two-step auth completes THEN dispatch executes |
| MHEWS-FR-0239 | Multi-lingual pre-written alert templates auto-selected by locale | HIGH | S1 | GIVEN recipient locale WHEN template loads THEN matching language version is used |
| MHEWS-FR-0250 | Link alert levels to predefined response actions | HIGH | S1 | GIVEN an alert level WHEN mapped THEN response actions are linked |
| MHEWS-FR-0255 | Require analyst approval of all AI-generated narrative content | CRITICAL | S1 | GIVEN AI narrative WHEN generated THEN analyst must explicitly accept before use |
| MHEWS-FR-0256 | Require human approval before alert dissemination | CRITICAL | S1 | GIVEN a submitted alert WHEN approval is requested THEN human Approver must authorize |
| MHEWS-FR-0303 | Allow alerts tagged with multiple concurrent hazard types | HIGH | S1 | GIVEN an alert WHEN tagged THEN multiple hazard types can be assigned |
| MHEWS-FR-0304 | Classify alerts into severity levels (Advisory/Watch/Warning/Emergency) | CRITICAL | S1 | GIVEN an alert WHEN severity is set THEN it matches controlled vocabulary |
| MHEWS-FR-0306 | Multilingual content fields in CAP documents | HIGH | S1 | GIVEN a CAP document WHEN multilingual THEN content in all configured languages is included |
| MHEWS-FR-0310 | Export AI narratives in structured format | MEDIUM | S1-S2 | GIVEN an AI narrative WHEN export is requested THEN structured file is generated |
| MHEWS-FR-0311 | Multilingual AI narrative generation | HIGH | S1 | GIVEN a language code WHEN AI narrative generates THEN output is in requested language |
| MHEWS-FR-0312 | Version history for AI-generated narratives | HIGH | S1 | GIVEN AI narrative changes WHEN queried THEN all versions are returned |
| MHEWS-FR-0317 | Generate printable alert summary documents | LOW | S1-S2 | GIVEN an alert WHEN print is requested THEN summary PDF is generated |
| MHEWS-FR-0350 | Generate standardized alert messages following defined structures | HIGH | S1 | GIVEN alert data WHEN message generates THEN it follows defined content structure |
| MHEWS-FR-0359 | Require dual authorization for critical alert broadcast | CRITICAL | S1 | GIVEN a broadcast request WHEN submitted THEN two different authorized officers must approve |
| MHEWS-FC-ERR-07 | CAP validation failure: return field-level errors, block advancement | CRITICAL | S1 | GIVEN invalid CAP data WHEN validation fails THEN error list returned and draft stays in DRAFT |
| MHEWS-FC-STM-01 | CAPDraft state machine (DRAFT→PENDING→APPROVED/REJECTED/CANCELLED/EXPIRED) | CRITICAL | S1 | GIVEN a CAP draft WHEN transition occurs THEN only valid transitions succeed |
| MHEWS-FC-INV-06 | CAP date/time field validation (ISO 8601, expires > sent) | HIGH | S1 | GIVEN CAP dates WHEN validated THEN expires > sent and within 30 days |
| MHEWS-FC-INV-07 | CAP controlled vocabulary validation | HIGH | S1 | GIVEN CAP fields WHEN set THEN values match CAP v1.2 vocabulary |
| MHEWS-FC-OUV-01 | Validate every CAP XML against bundled XSD before storage | CRITICAL | S1 | GIVEN generated XML WHEN XSD validation runs THEN failures block storage and dispatch |
| MHEWS-FC-OUV-03 | LLM output length/safety validation (≤2000 chars, no PII, language match) | HIGH | S1 | GIVEN LLM output WHEN validation runs THEN length/PII/language checks pass or flag |
| MHEWS-SD-LLM-01 | AI Narrative Service provider abstraction (swappable via config) | CRITICAL | S1 | GIVEN config change WHEN provider switches THEN no code changes required |
| MHEWS-SD-LLM-02 | Versioned prompt template registry editable without code deploy | HIGH | S1 | GIVEN admin UI WHEN template is updated THEN next LLM call uses updated template |
| MHEWS-SD-LLM-03 | LLM context packaging service | HIGH | S1 | GIVEN alert context WHEN packaged THEN structured LLM input includes hazard, breach, forecast, impact |
| MHEWS-SD-LLM-05 | LLM failure does not block CAP draft (ai_status=UNAVAILABLE) | CRITICAL | S1 | GIVEN LLM failure WHEN draft is being created THEN draft proceeds with retry option |
| MHEWS-SD-LLM-06 | PII masking before cloud LLM API calls | HIGH | S1 | GIVEN prompt assembly WHEN PII is detected THEN it is masked before API call |
| MHEWS-SD-VALID-01 | Bundled CAP v1.2 XSD (no runtime URL fetch) | HIGH | S1 | GIVEN validation WHEN XSD is needed THEN bundled file is used |
| MHEWS-SD-VALID-02 | CAP profile selection (base mandatory, GDACS/national optional) | MEDIUM | S1-S2 | GIVEN CAP profiles WHEN validation runs THEN base + selected profile is applied |
| MHEWS-SD-VALID-03 | CAP mandatory field validation at application level | HIGH | S1 | GIVEN a CAP draft WHEN submitted THEN all mandatory fields are validated with named errors |
| MHEWS-SD-VALID-04 | Pluggable CAP profile XSD via env var | HIGH | S1 | GIVEN CAP_PROFILE_XSD env var WHEN set THEN that XSD is used for validation |
| MHEWS-SD-VALID-05 | Remove all IPAWS-specific logic | HIGH | S1 | GIVEN codebase WHEN searched THEN no IPAWS references exist |
| MHEWS-SD-DRAW-01 | CAP area via admin boundary selection | HIGH | S1 | GIVEN boundaries WHEN operator selects THEN geometry is stored as CAP area polygon |
| MHEWS-SD-DRAW-02 | Polygon drawing tool (MapLibre GL Draw) in CAP Editor | HIGH | S1 | GIVEN CAP editor WHEN polygon is drawn THEN WGS84 CAP polygon is stored |
| MHEWS-SD-DRAW-03 | CAP area circle definition (center + radius) | MEDIUM | S1-S2 | GIVEN center and radius WHEN entered THEN CAP-compliant point-radius notation is stored |


### 3.7 Module M7 — Dissemination (`dissemination`)

**Overview:** Manages contact directory, dispatch orchestration, channel-specific adapters (email, WhatsApp), public web portal, community hazard reporting, and public self-registration.

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0004 | Categorize and summarize community feedback using NLP | MEDIUM | S2-S3 | GIVEN community feedback WHEN NLP runs THEN categories and summary are assigned |
| MHEWS-FR-0044 | Export alert content as audio message files | MEDIUM | S2-S3 | GIVEN an alert WHEN audio export triggers THEN audio file is generated |
| MHEWS-FR-0066 | Notify administrators of channel failures | HIGH | S2 | GIVEN a dispatch failure WHEN channel fails THEN admin notification is sent |
| MHEWS-FR-0079 | Allow citizens to submit impact validation reports | MEDIUM | S2-S3 | GIVEN the public portal WHEN citizen submits report THEN it enters moderation queue |
| MHEWS-FR-0104 | Polygon-based geographic areas for targeted recipients | HIGH | S2 | GIVEN a polygon WHEN drawn THEN contacts within polygon are selected for dispatch |
| MHEWS-FR-0109 | Submit field reports with photos and text | MEDIUM | S2-S3 | GIVEN the reporting form WHEN submitted THEN photo and text are stored with geo-tag |
| MHEWS-FR-0112 | NLP-based categorization of citizen feedback | MEDIUM | S2-S3 | GIVEN feedback text WHEN NLP runs THEN hazard category is assigned |
| MHEWS-FR-0114 | Route crowd-sourced reports through verification workflow | MEDIUM | S2-S3 | GIVEN a report WHEN submitted THEN it enters PENDING state awaiting moderator action |
| MHEWS-FR-0115 | Geo-tagged incident reporting via web portal | MEDIUM | S2-S3 | GIVEN the portal WHEN user submits report THEN geo-tag is captured |
| MHEWS-FR-0116 | Attach images and text to incident reports | MEDIUM | S2-S3 | GIVEN report form WHEN images attached THEN they are stored in object storage |
| MHEWS-FR-0118 | Dashboard displaying dissemination performance metrics | MEDIUM | S2-S3 | GIVEN dispatches WHEN dashboard loads THEN delivery rates and timing are displayed |
| MHEWS-FR-0119 | Automatically retry failed dissemination attempts | HIGH | S2 | GIVEN a failed dispatch WHEN retry triggers THEN re-dispatch is attempted with backoff |
| MHEWS-FR-0129 | Email-based alert dissemination | CRITICAL | S2 | GIVEN an approved alert WHEN dispatch runs THEN email is sent to all email contacts |
| MHEWS-FR-0147 | Polygon-based geo-fencing for targeted dissemination | HIGH | S2 | GIVEN a geofence polygon WHEN dispatch runs THEN only contacts within polygon receive alert |
| MHEWS-FR-0201 | Draw geographic polygon to select recipient population | HIGH | S2 | GIVEN the dispatch UI WHEN polygon is drawn THEN matching contacts are selected |
| MHEWS-FR-0205 | Submit situation report via web portal | MEDIUM | S2-S3 | GIVEN the portal WHEN report is submitted THEN it is stored with optional photo |
| MHEWS-FR-0206 | Map view clustering crowd-sourced reports | MEDIUM | S2-S3 | GIVEN approved reports WHEN map loads THEN reports are clustered by location |
| MHEWS-FR-0208 | Map-integrated shelter management interface | MEDIUM | S2-S3 | GIVEN shelter data WHEN map loads THEN shelters display with capacity and status |
| MHEWS-FR-0212 | Process community feedback using LLM for categorization | MEDIUM | S2-S3 | GIVEN feedback text WHEN LLM processes THEN category and summary are generated |
| MHEWS-FR-0216 | Aggregate community feedback themes for after-action review | MEDIUM | S2-S3 | GIVEN feedback WHEN aggregation runs THEN common themes are identified |
| MHEWS-FR-0226 | Georeferenced entry form for field officer submissions | LOW | S2-S3 | GIVEN the form WHEN submitted THEN text, photo, audio are stored with geo-reference |
| MHEWS-FR-0240 | Public web form for hazard impact reporting | MEDIUM | S2-S3 | GIVEN the public form WHEN submitted THEN report enters moderation queue |
| MHEWS-FR-0241 | Real-time stream of incoming crowdsourced reports | MEDIUM | S2-S3 | GIVEN incoming reports WHEN dashboard loads THEN live ticker is displayed |
| MHEWS-FR-0243 | Shelter management dashboard with capacity and status | MEDIUM | S2-S3 | GIVEN shelters WHEN dashboard loads THEN locations, capacity, open/closed are shown |
| MHEWS-FR-0267 | Georeferenced submission forms for community observations | LOW | S2-S3 | GIVEN the form WHEN submitted THEN observations are stored with coordinates |
| MHEWS-FR-0280 | Notify institutional contacts upon alert activation | HIGH | S2 | GIVEN an approved alert WHEN dispatch runs THEN institutional contacts are notified |
| MHEWS-FR-0287 | Localized message templates per configured language | HIGH | S2 | GIVEN a language WHEN template loads THEN localized version is used |
| MHEWS-FR-0301 | Distribute alerts across all in-scope channels | CRITICAL | S2 | GIVEN an approved alert WHEN dispatch runs THEN email, WhatsApp, portal all receive |
| MHEWS-FR-0321 | Capture read receipt metadata from channels | MEDIUM | S2-S3 | GIVEN dispatch WHEN delivery receipt arrives THEN metadata is stored |
| MHEWS-FR-0343 | Real-time shelter occupancy dashboard | MEDIUM | S2-S3 | GIVEN shelter data WHEN dashboard loads THEN current vs total capacity is shown |
| MHEWS-FR-0348 | Shared dashboards for inter-agency coordination | HIGH | S2 | GIVEN authorized users WHEN dashboard loads THEN shared coordination view is displayed |
| MHEWS-FR-0349 | Institutional contact directory for dissemination | HIGH | S2 | GIVEN contacts WHEN directory is queried THEN structured institutional list is returned |
| MHEWS-FR-0368 | Warning message distribution to all configured channels | CRITICAL | S2 | GIVEN a warning WHEN dispatch runs THEN all in-scope channels are used |
| MHEWS-FR-0369 | WhatsApp-based alert dissemination (mock in Phase A) | HIGH | S2 | GIVEN an approved alert WHEN WhatsApp dispatch runs THEN messages are sent via mock/live API |
| MHEWS-FC-ERR-08 | Dispatch channel failure: set FAILED, don't abort batch, expose retry | CRITICAL | S2 | GIVEN a contact failure WHEN dispatch fails THEN receipt=FAILED, batch continues, retry endpoint available |
| MHEWS-FC-STM-02 | DispatchJob state machine (QUEUED→RUNNING→COMPLETED/FAILED) | HIGH | S2 | GIVEN a dispatch job WHEN transition occurs THEN only valid transitions succeed |
| MHEWS-FC-STM-07 | CommunityReport state machine (PENDING→APPROVED/REJECTED→ARCHIVED) | HIGH | S2 | GIVEN a report WHEN moderation action occurs THEN valid transitions are enforced |
| MHEWS-FC-STM-08 | Contact consent state machine per channel | HIGH | S2 | GIVEN a contact WHEN opt-out occurs THEN per-channel consent is updated |
| MHEWS-FC-STM-09 | DispatchReceipt state machine (QUEUED→SENT→DELIVERED/FAILED/BOUNCED) | HIGH | S2 | GIVEN a receipt WHEN callback arrives THEN status transitions to correct state |
| MHEWS-FC-INV-10 | WhatsApp number validation (E.164 format) | HIGH | S2 | GIVEN a phone number WHEN validated THEN E.164 format is enforced (7-15 digits) |
| MHEWS-FC-OUV-04 | Dispatch receipt webhook validation (HMAC, message ID match) | HIGH | S2 | GIVEN a webhook WHEN received THEN HMAC signature and message ID are verified |
| MHEWS-SD-PORTAL-01 | Public alert portal (unauthenticated) with active alerts | HIGH | S2 | GIVEN the portal WHEN loaded THEN all active non-expired alerts are displayed |
| MHEWS-SD-PORTAL-02 | Portal update within 60s of CAP approval via SSE | HIGH | S2 | GIVEN approval WHEN 60 seconds pass THEN portal reflects new alert |
| MHEWS-SD-EMAIL-01 | Email dispatch adapter abstracting provider | HIGH | S2 | GIVEN email dispatch WHEN provider is configured via env THEN adapter sends via that provider |
| MHEWS-SD-EMAIL-02 | Email template engine with per-language variants | HIGH | S2 | GIVEN a language WHEN email renders THEN correct language template is used |
| MHEWS-SD-EMAIL-03 | Dispatch to 10,000 email recipients within 5 minutes | HIGH | S2 | GIVEN 10,000 contacts WHEN dispatch runs THEN all emails sent within 5 minutes |
| MHEWS-SD-EMAIL-04 | Email opt-out compliance with unsubscribe link | HIGH | S2 | GIVEN an email WHEN unsubscribe is clicked THEN contact opt_in=false immediately |
| MHEWS-SD-WA-01 | WhatsApp dispatch adapter targeting Meta Cloud API | CRITICAL | S2 | GIVEN WhatsApp dispatch WHEN triggered THEN messages sent via Meta API with template |
| MHEWS-SD-WA-02 | WhatsApp message template with CAP parameters | CRITICAL | S2 | GIVEN a template WHEN used THEN headline, severity, area, portal URL are included |
| MHEWS-SD-WA-03 | WhatsApp webhook HMAC-SHA256 signature verification | HIGH | S2 | GIVEN a webhook WHEN signature is invalid THEN HTTP 403 and AuditEvent |
| MHEWS-SD-WA-04 | WhatsApp opt-in compliance (exclude OPTED_OUT/PENDING) | CRITICAL | S2 | GIVEN contacts WHEN dispatch runs THEN only opted-in contacts receive messages |
| MHEWS-SD-WA-05 | WhatsApp mock adapter for dev/test | HIGH | S2 | GIVEN mock mode WHEN dispatch runs THEN mock API accepts and simulates receipts |
| MHEWS-SD-CONTACT-01 | Contact data model with per-channel opt-in fields | CRITICAL | S2 | GIVEN a contact WHEN created THEN email, whatsapp, language, location, opt-in fields exist |
| MHEWS-SD-CONTACT-02 | DistributionList with hazard filter and geofence | CRITICAL | S2 | GIVEN a list WHEN configured THEN hazard_type_filter and geofence_polygon are stored |
| MHEWS-SD-CONTACT-03 | Contact CRUD API (Admin-only, soft delete) | HIGH | S2 | GIVEN admin role WHEN CRUD operation runs THEN contact is managed; hard delete blocked |
| MHEWS-SD-CONTACT-04 | Bulk CSV import of contacts | MEDIUM | S2-S3 | GIVEN a CSV WHEN uploaded THEN valid rows imported, invalid rows rejected with errors |
| MHEWS-SD-CONTACT-05 | Immutable DispatchReceipt entity | HIGH | S2 | GIVEN a dispatch WHEN receipt is created THEN all fields are immutable after creation |
| MHEWS-SD-CONTACT-06 | Contact anonymization for GDPR compliance | HIGH | S2 | GIVEN an anonymization request WHEN admin runs THEN PII is replaced with hashes |
| MHEWS-SD-GDB-02 | Geofenced audience segmentation query within 500ms for 100k records | HIGH | S2 | GIVEN a CAP polygon WHEN spatial query runs THEN contacts returned within 500ms |
| MHEWS-SD-FEEDBACK-01 | Public community hazard report submission form | HIGH | S2 | GIVEN the public form WHEN submitted THEN geo-tagged report is stored |
| MHEWS-SD-FEEDBACK-02 | AI categorization of community reports within 30 seconds | HIGH | S2 | GIVEN a report WHEN LLM runs THEN category and confidence assigned within 30 seconds |
| MHEWS-SD-FEEDBACK-03 | Moderation gate: no public display before approval | HIGH | S2 | GIVEN a report WHEN submitted THEN it is not visible publicly until moderator approves |
| MHEWS-SD-FEEDBACK-05 | Report-to-Incident linkage with audit event | MEDIUM | S2-S3 | GIVEN an approved report WHEN linked to incident THEN AuditEvent is recorded |
| MHEWS-SD-SELFREG-01 | Public alert subscription self-registration with double opt-in | HIGH | S2 | GIVEN the registration form WHEN submitted THEN double opt-in confirmation is required |
| MHEWS-SD-SCOPE-01 | Explicit scope exclusion notice for OOS channels | CRITICAL | S2 | GIVEN documentation WHEN reviewed THEN SMS, CBS, push, siren, radio, social media are listed as OOS |


### 3.8 Module M8 — Incident Record & Lifecycle Tracking (`incidents`)

**Overview:** Manages operational incident records linking alerts, breach events, community reports, and after-action analysis. Provides the learning feedback loop (WMO Pillar 4). Post-PoC (stub in Sprint 1).

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0033 | Generate annual drill performance reports | MEDIUM | S2-S3 | GIVEN drill data WHEN report generates THEN annual metrics are produced |
| MHEWS-FR-0113 | Generate post-event reports summarizing alerts and impacts | HIGH | S2 | GIVEN a closed incident WHEN report generates THEN summary includes alerts and impacts |
| MHEWS-FR-0125 | Calculate and record impact metrics during drills | MEDIUM | S2-S3 | GIVEN a drill WHEN running THEN impact metrics are calculated and stored |
| MHEWS-FR-0126 | Drill simulation mode mirroring live operations | HIGH | S2 | GIVEN drill mode WHEN activated THEN live operations are mirrored without real dispatch |
| MHEWS-FR-0138 | Track false alarm rates per hazard type | HIGH | S2 | GIVEN closed incidents WHEN metrics are computed THEN false alarm rate is stored |
| MHEWS-FR-0149 | Inject simulated hazard layers for drill/training | HIGH | S2 | GIVEN drill mode WHEN admin injects event THEN simulated hazard appears in system |
| MHEWS-FR-0184 | Filter documents by category (Legislation, SOPs, etc.) | MEDIUM | S2-S3 | GIVEN documents WHEN filter is applied THEN matching documents are returned |
| MHEWS-FR-0207 | Link SOPs and response scenarios to hazard triggers | HIGH | S2 | GIVEN a hazard trigger WHEN matched THEN linked SOP is displayed |
| MHEWS-FR-0209 | LLM-powered SOP guidance assistant | MEDIUM | S2-S3 | GIVEN a user query WHEN LLM processes THEN tactical actions from SOPs are presented |
| MHEWS-FR-0210 | Drill mode activation with DRILL ONLY header and channel isolation | HIGH | S2 | GIVEN drill activation WHEN toggled THEN alerts show EXERCISE watermark and dispatch restricted |
| MHEWS-FR-0213 | Measure elapsed time between event injection and approval | MEDIUM | S2-S3 | GIVEN drill WHEN approval occurs THEN response timer is recorded |
| MHEWS-FR-0214 | Display drill participation metrics | MEDIUM | S2-S3 | GIVEN a completed drill WHEN metrics display THEN participant count and ack rate are shown |
| MHEWS-FR-0217 | Navigation shortcut from drill review to response plan editor | LOW | S2-S3 | GIVEN drill review WHEN shortcut is clicked THEN user navigates to response plan |
| MHEWS-FR-0242 | Admin panel for SOP management linked to hazard triggers | HIGH | S2 | GIVEN admin UI WHEN SOP is uploaded and linked THEN it displays on matching trigger |
| MHEWS-FR-0245 | Global drill mode switch with EXERCISE watermark | HIGH | S2 | GIVEN drill switch WHEN activated THEN all alerts marked EXERCISE ONLY |
| MHEWS-FR-0257 | Record performance metrics during drill exercises | HIGH | S2 | GIVEN a drill WHEN running THEN response times and participation are recorded |
| MHEWS-FR-0263 | Collect post-event damage and impact data | HIGH | S2 | GIVEN a closed event WHEN data collection runs THEN damage data is stored |
| MHEWS-FR-0265 | Store post-event evaluation findings and lessons learned | HIGH | S2 | GIVEN a closed incident WHEN AAR is completed THEN lessons learned are stored |
| MHEWS-FR-0270 | Post-event analytics: evaluate system performance | HIGH | S2 | GIVEN a closed event WHEN analytics run THEN performance report is generated |
| MHEWS-FR-0271 | Support response planning, drills, post-event evaluation | HIGH | S2 | GIVEN the incident module WHEN used THEN all three workflows are supported |
| MHEWS-FR-0274 | Manage emergency response documentation | HIGH | S2 | GIVEN response plans WHEN managed THEN version-controlled repository is maintained |
| MHEWS-FR-0275 | Store and version-control emergency response plans | HIGH | S2 | GIVEN a plan WHEN updated THEN previous version is preserved |
| MHEWS-FR-0277 | Drill and hazard simulation for training | HIGH | S2 | GIVEN training mode WHEN simulation runs THEN no live impact occurs |
| MHEWS-FR-0278 | Non-live simulation mode for training | HIGH | S2 | GIVEN simulation WHEN activated THEN real dissemination is disabled |
| MHEWS-FR-0316 | Store post-event review documentation in incident record | HIGH | S2 | GIVEN review docs WHEN stored THEN they are linked to the incident |
| MHEWS-FR-0344 | Auto-map hazard trigger events to predefined SOP workflows | HIGH | S2 | GIVEN a trigger WHEN matched THEN corresponding SOP is assigned |
| MHEWS-FR-0356 | Timeline playback of hazard event lifecycle | MEDIUM | S2-S3 | GIVEN a closed event WHEN playback runs THEN timeline from trigger to warning is visualized |
| MHEWS-FC-STM-04 | Incident state machine (OPEN→IN_PROGRESS→MONITORING→CLOSED→ARCHIVED) | HIGH | S2 | GIVEN an incident WHEN state transition occurs THEN valid transitions are enforced; AAR required for MONITORING→CLOSED |

### 3.9 Module M9 — Audit & Compliance Framework (`audit`)

**Overview:** Provides an immutable, append-only audit event log recording all significant system actions. Supports evidence package generation and compliance reporting. All modules produce audit events; M9 consumes and stores them.

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0009 | Log all AI-generated content including model ID, version, context, output | HIGH | S1 | GIVEN AI generation WHEN output is produced THEN log entry includes model_id, version, input_hash, output |
| MHEWS-FR-0010 | Enforce configurable retention policy for AI outputs | MEDIUM | S1-S2 | GIVEN retention config WHEN policy check runs THEN expired outputs are handled per policy |
| MHEWS-FR-0011 | Assign unique trace ID to each AI output | HIGH | S1 | GIVEN an AI output WHEN created THEN unique trace_id is assigned |
| MHEWS-FR-0013 | Log all AI model usage events | HIGH | S1 | GIVEN an AI invocation WHEN executed THEN usage event is logged with timestamp and user |
| MHEWS-FR-0014 | Archive all issued warnings for audit | CRITICAL | S1 | GIVEN an issued alert WHEN archived THEN complete CAP + metadata is stored immutably |
| MHEWS-FR-0017 | Log timestamp of dissemination initiation per alert | HIGH | S1 | GIVEN a dispatch WHEN initiated THEN timestamp is logged |
| MHEWS-FR-0024 | Compute alert performance metrics (lead time, false alarm rate) | HIGH | S1 | GIVEN completed alerts WHEN metrics are computed THEN lead time and false alarm rate are stored |
| MHEWS-FR-0025 | Enforce configurable alert data retention policies | HIGH | S1 | GIVEN retention config WHEN policy runs THEN expired alert data is handled per policy |
| MHEWS-FR-0045 | Package evidence artifacts for regulatory review | HIGH | S1 | GIVEN a CAP or incident WHEN package is requested THEN ZIP with XML, receipts, audit entries is generated |
| MHEWS-FR-0046 | Log threshold changes and config updates immutably | CRITICAL | S1 | GIVEN a threshold change WHEN saved THEN audit entry is created with before/after values |
| MHEWS-FR-0047 | Export audit trail logs in structured formats | HIGH | S1 | GIVEN audit data WHEN export is requested THEN structured file (CSV/JSON) is generated |
| MHEWS-FR-0048 | Verify audit log integrity using cryptographic checksums | HIGH | S1 | GIVEN audit logs WHEN integrity check runs THEN SHA-256 hash chain is validated |
| MHEWS-FR-0049 | Log identity of authorizing officer per alert | CRITICAL | S1 | GIVEN an approval WHEN authorized THEN officer identity is recorded |
| MHEWS-FR-0050 | Log authorization response times per alert workflow | MEDIUM | S1-S2 | GIVEN an approval workflow WHEN completed THEN response time is recorded |
| MHEWS-FR-0054 | Log all detected security breach events | CRITICAL | S1 | GIVEN a security event WHEN detected THEN immutable audit entry is created |
| MHEWS-FR-0055 | Archive all CAP XML payloads per alert | HIGH | S1 | GIVEN a CAP package WHEN generated THEN XML is archived in object storage |
| MHEWS-FR-0065 | Generate capacity planning reports | LOW | S1-S2 | GIVEN system metrics WHEN report generates THEN capacity data is compiled |
| MHEWS-FR-0067 | Export structured checklist compliance evidence | MEDIUM | S1-S2 | GIVEN compliance data WHEN export runs THEN structured checklist is generated |
| MHEWS-FR-0070 | Automate periodic compliance reports | MEDIUM | S1-S2 | GIVEN a schedule WHEN report time arrives THEN compliance report is auto-generated |
| MHEWS-FR-0071 | Version-control compliance export templates | MEDIUM | S1-S2 | GIVEN a template WHEN updated THEN previous version is preserved |
| MHEWS-FR-0075 | Log all system configuration changes | HIGH | S1 | GIVEN a config change WHEN saved THEN audit entry is created |
| MHEWS-FR-0081 | Controlled, auditable data deletion workflow | HIGH | S1 | GIVEN deletion request WHEN executed THEN action is audit-logged with justification |
| MHEWS-FR-0086 | Generate data lineage reports | MEDIUM | S1-S2 | GIVEN a dataset WHEN lineage report generates THEN source chain is displayed |
| MHEWS-FR-0088 | Configure data retention policies per category | HIGH | S1 | GIVEN retention config WHEN admin sets policy THEN it applies to the specified category |
| MHEWS-FR-0093 | Log all dataset access events | HIGH | S1 | GIVEN a dataset access WHEN it occurs THEN user, timestamp, action are logged |
| MHEWS-FR-0095 | Record source and version for each ingested dataset | HIGH | S1 | GIVEN an ingested dataset WHEN recorded THEN source and version are stored |
| MHEWS-FR-0096 | Enforce dataset retention policy with expiry action | HIGH | S1 | GIVEN retention policy WHEN data expires THEN configured action (archive/delete) executes |
| MHEWS-FR-0105 | Log deployed software version per component | MEDIUM | S1-S2 | GIVEN a deployment WHEN version is set THEN it is logged |
| MHEWS-FR-0111 | Export dissemination audit logs | HIGH | S1 | GIVEN dissemination data WHEN export runs THEN audit logs are output |
| MHEWS-FR-0117 | Log channel-level dissemination statistics | HIGH | S1 | GIVEN a dispatch WHEN completed THEN per-channel stats are logged |
| MHEWS-FR-0130 | Store explainability metadata for AI outputs | HIGH | S1 | GIVEN an AI output WHEN stored THEN explainability metadata is included |
| MHEWS-FR-0148 | Georeferenced data attribution to submitting user | MEDIUM | S1-S2 | GIVEN a georeferenced entry WHEN submitted THEN user ID and timestamp are attributed |
| MHEWS-FR-0151 | Periodic review of governance audit logs | HIGH | S1 | GIVEN audit logs WHEN review period arrives THEN logs are accessible for reviewer |
| MHEWS-FR-0155 | Immutable audit log of hazard metadata changes | HIGH | S1 | GIVEN a hazard metadata change WHEN saved THEN immutable audit entry is created |
| MHEWS-FR-0253 | Log all hazard metadata updates | HIGH | S1 | GIVEN a hazard update WHEN saved THEN audit entry is created |
| MHEWS-FR-0258 | Archive impact computation results with parameters | HIGH | S1 | GIVEN impact results WHEN computed THEN results are archived with input parameters |
| MHEWS-FR-0259 | Log impact calculation parameters and triggers | HIGH | S1 | GIVEN an impact computation WHEN executed THEN parameters and trigger conditions are logged |
| MHEWS-FR-0268 | Log infrastructure-level system events | MEDIUM | S1-S2 | GIVEN a system event WHEN detected THEN it is logged to operational log |
| MHEWS-FR-0269 | Calculate alert lead time, coverage rate, forecast accuracy | HIGH | S1 | GIVEN completed alerts WHEN metrics compute THEN lead time, coverage, accuracy are stored |
| MHEWS-FR-0272 | Log all data ingestion events | HIGH | S1 | GIVEN an ingestion event WHEN completed THEN timestamp and source metadata are logged |
| MHEWS-FR-0281 | Governance KPI dashboard | HIGH | S1 | GIVEN KPIs WHEN dashboard loads THEN governance metrics are displayed |
| MHEWS-FR-0282 | Measure alert lead time (breach to dissemination) | HIGH | S1 | GIVEN an alert lifecycle WHEN completed THEN lead time is computed and stored |
| MHEWS-FR-0283 | Configurable long-term archival of historical records | HIGH | S1 | GIVEN archival policy WHEN configured THEN historical data is archived per policy |
| MHEWS-FR-0291 | Log all model version changes | HIGH | S1 | GIVEN a model version change WHEN saved THEN audit entry is created |
| MHEWS-FR-0313 | Baseline system performance reports | MEDIUM | S1-S2 | GIVEN system metrics WHEN report generates THEN baseline performance data is compiled |
| MHEWS-FR-0314 | Log system performance metrics for critical operations | MEDIUM | S1-S2 | GIVEN critical operations WHEN executed THEN performance metrics are logged |
| MHEWS-FR-0318 | Log all privileged access activities | CRITICAL | S1 | GIVEN admin action WHEN executed THEN it is logged to immutable audit |
| MHEWS-FR-0320 | Archive raw ingested datasets separately from processed | MEDIUM | S1-S2 | GIVEN ingestion WHEN completed THEN raw data is archived separately |
| MHEWS-FR-0325 | Enforce immutability of requirement identifiers | LOW | S1-S2 | GIVEN a requirement ID WHEN assigned THEN it cannot be changed |
| MHEWS-FR-0328 | Log all user role assignment changes | HIGH | S1 | GIVEN a role change WHEN saved THEN before/after values are audit-logged |
| MHEWS-FR-0338 | Generate security configuration audit reports | HIGH | S1 | GIVEN security config WHEN report generates THEN current settings are compiled |
| MHEWS-FR-0339 | Monitor and alert on security events | HIGH | S1 | GIVEN security monitoring WHEN anomaly detected THEN alert is raised |
| MHEWS-FR-0351 | Verify backup archive integrity | HIGH | S1 | GIVEN a backup WHEN verification runs THEN SHA-256 checksum is validated |
| MHEWS-FR-0361 | Log all user activity events across modules | HIGH | S1 | GIVEN any user action WHEN executed THEN activity event is logged |
| MHEWS-FR-0364 | Log all dataset schema validation results | MEDIUM | S1-S2 | GIVEN validation WHEN completed THEN results including errors are logged |
| MHEWS-FC-ERR-09 | Audit write failure: queue to fallback, don't block, flush on reconnect | CRITICAL | S1 | GIVEN audit write failure WHEN error occurs THEN event is queued; primary operation continues |
| MHEWS-FC-OUV-06 | Audit event completeness validation before persist | CRITICAL | S1 | GIVEN an AuditEvent WHEN persisting THEN event_type, created_at, resource_type, resource_id must be non-null |
| MHEWS-SD-LLM-04 | Immutable LLMOutput audit record | CRITICAL | S1 | GIVEN an LLM invocation WHEN completed THEN immutable record with model_id, prompt_hash, output is stored |
| MHEWS-SD-STORE-04 | Evidence package storage as PDF in object storage | HIGH | S1 | GIVEN a CAP lifecycle WHEN evidence package generates THEN PDF report is stored in MinIO/S3 |


### 3.10 Module M10 — Administration & Access Control (`accounts`)

**Overview:** Manages user identity, authentication (JWT), role-based access control via Django Groups, and object-level permission groundwork via django-guardian.

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0005 | Configure AI governance parameters (model selection, output constraints) | HIGH | S1 | GIVEN admin UI WHEN governance params are set THEN they are enforced on AI calls |
| MHEWS-FR-0076 | Rollback configuration changes to previous state | MEDIUM | S1-S2 | GIVEN a config change WHEN rollback is requested THEN previous state is restored |
| MHEWS-FR-0077 | Validate all configuration changes before activation | HIGH | S1 | GIVEN a config change WHEN submitted THEN validation runs before activation |
| MHEWS-FR-0080 | Customizable dashboard views per role and preference | LOW | S1-S2 | GIVEN a user WHEN dashboard loads THEN role-specific customized view is shown |
| MHEWS-FR-0102 | Deployment configuration templates for supported environments | LOW | S1-S2 | GIVEN a target env WHEN template is requested THEN environment-specific config is generated |
| MHEWS-FR-0120 | Versioned references to system documentation | LOW | S1-S2 | GIVEN documentation WHEN stored THEN versioned references are maintained |
| MHEWS-FR-0135 | Bulk user enrollment via CSV upload | MEDIUM | S1-S2 | GIVEN a CSV WHEN uploaded THEN valid users are enrolled and invalid rows rejected |
| MHEWS-FR-0136 | Custom user groups with configurable permissions | MEDIUM | S1-S2 | GIVEN admin UI WHEN group is created THEN permissions are configurable |
| MHEWS-FR-0137 | Predefined user roles (Admin, Analyst, Operator, Decision Maker) | CRITICAL | S1 | GIVEN the system WHEN deployed THEN predefined roles exist with correct permissions |
| MHEWS-FR-0145 | GDPR data export per data subject request | HIGH | S1 | GIVEN a data subject request WHEN export runs THEN personal data is exported |
| MHEWS-FR-0152 | Configure governance roles and permissions | HIGH | S1 | GIVEN admin UI WHEN roles are configured THEN permissions are applied |
| MHEWS-FR-0244 | Separate views and permissions per role | CRITICAL | S1 | GIVEN role assignment WHEN user navigates THEN role-appropriate views are shown |
| MHEWS-FR-0279 | Map system roles to institutional organizational structures | MEDIUM | S1-S2 | GIVEN org structure WHEN role mapping is configured THEN it aligns to institution |
| MHEWS-FR-0329 | Enforce configurable RBAC for all functions | CRITICAL | S1 | GIVEN RBAC config WHEN user accesses function THEN permission check is enforced |
| MHEWS-FR-0330 | Customized dashboards per agency role | HIGH | S1 | GIVEN a role WHEN user logs in THEN role-tailored dashboard is displayed |
| MHEWS-FR-0352 | Export system configuration in portable format | MEDIUM | S1-S2 | GIVEN config WHEN export is requested THEN portable format file is generated |
| MHEWS-FR-0353 | System metadata registry of components and versions | MEDIUM | S1-S2 | GIVEN components WHEN registry is queried THEN names and versions are returned |
| MHEWS-FR-0360 | Immediately revoke user system access | CRITICAL | S1 | GIVEN an active user WHEN admin revokes access THEN access is blocked immediately |
| MHEWS-FR-0362 | Store user-specific configuration preferences | LOW | S1-S2 | GIVEN a user WHEN preference is set THEN it is persisted per account |
| MHEWS-FR-0363 | Create, update, and deactivate user profiles | HIGH | S1 | GIVEN admin role WHEN CRUD on user THEN profile is managed accordingly |
| MHEWS-FC-INV-08 | Password policy (≥12 chars, uppercase, digit, special, breached-password check) | HIGH | S1 | GIVEN password input WHEN policy check runs THEN all rules are enforced |

### 3.11 Module M11 — Integration & API Gateway (`gateway`)

**Overview:** Provides API versioning, rate limiting, OpenAPI schema generation, connector registry, and webhook receiver infrastructure. Post-PoC (stub in Sprint 1).

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0042 | Expose authenticated APIs for third-party alert distribution | HIGH | S1 | GIVEN API key WHEN authenticated request is made THEN alert data is returned |
| MHEWS-FR-0134 | Integrate third-party hazard feeds via connector registry | HIGH | S1 | GIVEN a feed config WHEN connector runs THEN data is ingested |
| MHEWS-FR-0227 | Admin UI for managing API endpoint configurations | HIGH | S1 | GIVEN admin UI WHEN connector config is edited THEN changes are saved |

### 3.12 Module M12 — Preparedness, Drill & Response (`preparedness`)

**Overview:** Manages drill/exercise simulations and SOP documentation. Ensures drill alerts are isolated from live operations. Post-PoC module.

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-SD-DRILL-01 | Tenant-level DrillMode flag forcing CAP status=Exercise | HIGH | S1-S2 | GIVEN drill activation WHEN DrillMode=ACTIVE THEN all CAP drafts have status=Exercise |
| MHEWS-SD-DRILL-02 | Auto-generate drill summary report on deactivation | MEDIUM | S2-S3 | GIVEN drill deactivation WHEN completed THEN drill report is generated (CSV/PDF) |
| MHEWS-FC-STM-11 | DrillMode state machine (INACTIVE→ACTIVE→COMPLETED) | HIGH | S1-S2 | GIVEN drill flag WHEN state transition occurs THEN only valid transitions succeed |

### 3.13 Cross-Cutting Functional Requirements

**Overview:** Requirements applying to all modules (error handling, validation, i18n, data export, infrastructure).

| Req ID | Statement | Priority | Sprint | Acceptance Criteria |
|--------|-----------|----------|--------|---------------------|
| MHEWS-FR-0082 | Export data in CSV, JSON, GIS formats | MEDIUM | S2-S3 | GIVEN exportable data WHEN user requests export THEN file in selected format is generated |
| MHEWS-FR-0090 | Manage data synchronization across components | MEDIUM | S2-S3 | GIVEN distributed components WHEN sync runs THEN data consistency is maintained |
| MHEWS-FR-0103 | Multilingual alert message generation | HIGH | S1-S2 | GIVEN configured languages WHEN alert generates THEN messages in all languages are produced |
| MHEWS-FR-0305 | Store alert content in multiple configured languages | HIGH | S1-S2 | GIVEN multilingual alert WHEN stored THEN all language versions are persisted |
| MHEWS-FR-0307 | Multilingual user interface for all system languages | HIGH | S1-S2 | GIVEN a locale WHEN UI loads THEN all strings are in that language |
| MHEWS-FR-0308 | Generate alert messages in multiple languages | HIGH | S1-S2 | GIVEN configured languages WHEN message generates THEN all language versions are produced |
| MHEWS-FC-ERR-01 | Uniform JSON error response format | HIGH | S1-S2 | GIVEN any API error WHEN returned THEN response matches `{"error":{"code","message","field","request_id"}}` |
| MHEWS-FC-ERR-02 | Request ID propagation (X-Request-ID) | HIGH | S1-S2 | GIVEN any request WHEN processed THEN X-Request-ID is generated, propagated, and returned |
| MHEWS-FC-ERR-03 | Unhandled exception catch-all (500 with sanitized message) | CRITICAL | S1 | GIVEN an unhandled exception WHEN caught THEN HTTP 500 with sanitized message and AuditEvent |
| MHEWS-FC-ERR-04 | External service timeout handling with configurable timeouts | HIGH | S1-S2 | GIVEN an external call WHEN timeout occurs THEN AuditEvent is logged and error returned |
| MHEWS-FC-ERR-05 | Database error handling (503/409/422 per error type) | HIGH | S1-S2 | GIVEN a DB error WHEN caught THEN appropriate HTTP status and error log are returned |
| MHEWS-FC-INV-01 | DRF serializer validation on all API inputs | HIGH | S1-S2 | GIVEN POST/PUT/PATCH body WHEN received THEN DRF serializer validates before business logic |
| MHEWS-FC-INV-02 | String injection prevention (SQL, HTML, CAP XML) | CRITICAL | S1 | GIVEN string input WHEN processed THEN ORM parameterization, HTML-escape, and lxml safe APIs are used |
| MHEWS-FC-INV-03 | File upload validation (MIME type, size, filename) | HIGH | S1-S2 | GIVEN a file upload WHEN received THEN MIME allowlist, 50MB limit, filename sanitization are enforced |
| MHEWS-FC-INV-04 | Coordinate validation (lat ∈ [-90,90], lon ∈ [-180,180]) | HIGH | S1-S2 | GIVEN lat/lon input WHEN validated THEN out-of-range values are rejected |
| MHEWS-FC-OUV-02 | API response schema validation (OpenAPI 3 conformance) | HIGH | S1-S2 | GIVEN API responses WHEN generated THEN they conform to drf-spectacular schema |
| MHEWS-FC-OUV-05 | GeoJSON output validation (RFC 7946) | HIGH | S1-S2 | GIVEN GeoJSON output WHEN generated THEN coordinates are in [lon, lat] order |
| MHEWS-SD-MAP-02 | Single approved base map tile source configurable via env var | HIGH | S1-S2 | GIVEN tile config WHEN map loads THEN base map renders from configured source |
| MHEWS-SD-STREAM-01 | Internal event bus for async inter-module communication | HIGH | S1-S2 | GIVEN defined events WHEN emitted THEN subscribers receive them asynchronously |
| MHEWS-SD-STREAM-04 | Message broker fault tolerance (24h retention, backoff, DLQ) | HIGH | S1-S2 | GIVEN unacknowledged events WHEN 24h passes THEN they are retained; failed events go to DLQ |
| MHEWS-SD-STORE-01 | Object storage adapter (S3-compatible) configurable via env | HIGH | S1-S2 | GIVEN env config WHEN file operation runs THEN S3-compatible store is used |
| MHEWS-SD-STORE-02 | Immutable FileAttachment entity with sha256 checksum | HIGH | S1-S2 | GIVEN a file WHEN attached THEN FileAttachment with checksum is created immutably |
| MHEWS-SD-GDB-01 | PostGIS as primary spatial DB (SRID 4326, GiST indexes) | CRITICAL | S1 | GIVEN spatial data WHEN stored THEN PostGIS with SRID 4326 and GiST indexes is used |
| MHEWS-SD-TILE-01 | Self-hosted tile server for Profile B deployments | HIGH | S1-S2 | GIVEN Profile B WHEN deployed THEN containerized tile server serves tiles via MAP_TILE_URL |
| MHEWS-SD-I18N-01 | Tri-lingual locale support (French, English, Language X) | HIGH | S1-S2 | GIVEN locale config WHEN system runs THEN three locales are simultaneously active |
| MHEWS-SD-I18N-02 | gettext wrappers and django-modeltranslation for all strings | HIGH | S1-S2 | GIVEN user-facing strings WHEN rendered THEN gettext translations are used |
| MHEWS-SD-BOUND-01 | import_boundaries management command (shapefile/GeoJSON + GADM) | HIGH | S1-S2 | GIVEN boundary data WHEN command runs THEN boundaries are imported to PostGIS |
| MHEWS-SD-BOUND-02 | GADM boundary bundling for non-government installations | HIGH | S1-S2 | GIVEN first-run init WHEN command runs THEN GADM Level 0-2 data is fetched and verified |

---

## 4. Non-Functional Requirements

Organized by category. 105 NFRs total (70 from Part A legacy, 4 from Part B system-derived, 31 from Part D completeness).

### 4.1 Performance & Latency (PERF) — 20 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0003 | AI narrative generation SLA | 30 seconds | Load Test | HIGH |
| MHEWS-NFR-0004 | Concurrent alert workflow throughput | 50 concurrent | Load Test | HIGH |
| MHEWS-NFR-0007 | Audit report generation SLA | 60 seconds | Load Test | HIGH |
| MHEWS-NFR-0011 | Batch processing SLA | Per config | Load Test | MEDIUM |
| MHEWS-NFR-0023 | Event replay SLA | 10 seconds for 24h | Load Test | MEDIUM |
| MHEWS-NFR-0025 | Forecast ingestion SLA | Per config | Load Test | HIGH |
| MHEWS-NFR-0030 | Concurrent dispatch throughput | No queuing delay | Load Test | HIGH |
| MHEWS-NFR-0033 | Impact query performance | ≤10 seconds | Load Test | HIGH |
| MHEWS-NFR-0034 | Impact computation SLA | ≤120 seconds | Load Test | HIGH |
| MHEWS-NFR-0042 | Alert dissemination time | ≤60 seconds post-approval | Load Test | CRITICAL |
| MHEWS-NFR-0045 | Data processing latency | ≤5 seconds | Load Test | CRITICAL |
| MHEWS-NFR-0050 | Real-time processing constraints | Per defined SLAs | Load Test | HIGH |
| MHEWS-NFR-0060 | Real-time feed processing | ≤60 seconds | Load Test | CRITICAL |
| MHEWS-SD-STREAM-03 | Streaming pipeline latency | ≤5s ingestion, ≤60s notification | Load Test | CRITICAL |
| NFR-PERF-LLM-01 | End-to-end AI narrative SLA | 30 seconds P95 | Load Test | HIGH |
| NFR-PERF-AUD-01 | Audit report generation for 30-day window | 60 seconds (100k events) | Load Test | HIGH |
| NFR-PERF-BATCH-01 | Batch job isolation from real-time pipeline | Separate Celery queues | Load Test | CRITICAL |
| NFR-PERF-FCST-01 | Forecast data availability post-NWP run | ≤30 minutes | Load Test | HIGH |
| NFR-PERF-IMP-01 | Pre-computed impact query SLA | ≤10 seconds | Load Test | HIGH |
| NFR-PERF-IMP-02 | On-demand impact computation SLA | ≤120s country, ≤30s district | Load Test | CRITICAL |

### 4.2 Security (SEC) — 14 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0001 | Periodic access review reports | Quarterly | Integration Test | HIGH |
| MHEWS-NFR-0002 | Account lockout after failed attempts | Configurable (default 5) | Security Scan | CRITICAL |
| MHEWS-NFR-0005 | API authentication on all endpoints | JWT + API key | Security Scan | CRITICAL |
| MHEWS-NFR-0006 | API rate limiting | Configurable per endpoint | Load Test | HIGH |
| MHEWS-NFR-0019 | Encryption at rest | AES-256 | Security Scan | CRITICAL |
| MHEWS-NFR-0020 | Encryption in transit | TLS 1.2+ | Security Scan | CRITICAL |
| MHEWS-NFR-0021 | Encryption key rotation | Zero-downtime rotation | Security Scan | HIGH |
| MHEWS-NFR-0040 | Intrusion detection integration | Where available | Security Scan | MEDIUM |
| MHEWS-NFR-0046 | Combined encryption (transit + rest) | TLS 1.2+ / AES-256 | Security Scan | CRITICAL |
| MHEWS-NFR-0054 | Secure access controls across all modules | RBAC + ABAC | Security Scan | CRITICAL |
| MHEWS-NFR-0057 | Multi-factor authentication | All accounts | Security Scan | CRITICAL |
| MHEWS-NFR-0059 | Password complexity rules | Configurable | Security Scan | HIGH |
| MHEWS-NFR-0065 | Security patch management | No full downtime | Security Scan | HIGH |
| MHEWS-NFR-0066 | Session timeout | Configurable | Security Scan | HIGH |

### 4.3 Availability & Recovery (AVAIL) — 9 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0017 | Disaster recovery deployment | Optional geo-separated | DR Test | HIGH |
| MHEWS-NFR-0029 | High availability — no SPOF | Active-passive HA | DR Test | CRITICAL |
| MHEWS-NFR-0043 | Annual availability | ≥99.9% | DR Test | CRITICAL |
| MHEWS-NFR-0047 | Failover clustering and redundancy | Critical services | DR Test | CRITICAL |
| MHEWS-NFR-0051 | RTO and RPO | RTO ≤5min, RPO ≤1min | DR Test | CRITICAL |
| MHEWS-NFR-0062 | RPO specification | ≤1 minute | DR Test | CRITICAL |
| MHEWS-NFR-0063 | RTO specification | ≤5 minutes | DR Test | CRITICAL |
| MHEWS-NFR-0067 | Enhanced availability | ≥99.95% | DR Test | CRITICAL |
| MHEWS-NFR-0070 | Zero-downtime model version switching | Forecast engine | Deployment Test | HIGH |

### 4.4 Reliability & Resilience (REL) — 5 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0009 | Automated backups | Regular schedule | DR Test | HIGH |
| MHEWS-NFR-0016 | Database redundancy | No SPOF | DR Test | HIGH |
| MHEWS-NFR-0022 | Standardized error handling | Uniform across modules | Integration Test | MEDIUM |
| MHEWS-NFR-0024 | Fault isolation between modules | No cascading failures | Integration Test | HIGH |
| MHEWS-NFR-0052 | Resilience under disruption | Redundancy + backup | DR Test | HIGH |

### 4.5 Scalability (SCAL) — 10 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0008 | Auto-scaling support | Configurable | Load Test | HIGH |
| MHEWS-NFR-0018 | Dissemination capacity | Max configured volume | Load Test | HIGH |
| MHEWS-NFR-0053 | Horizontal scaling | No interruption | Load Test | HIGH |
| MHEWS-NFR-0064 | Scale threshold | Handle increased load | Load Test | HIGH |
| NFR-SCAL-01 | Concurrent user capacity | ≥50 users, P95 ≤500ms | Load Test | HIGH |
| NFR-SCAL-02 | Data source feed throughput | 100 feeds at 60s polling | Load Test | HIGH |
| NFR-SCAL-03 | Alert dispatch at scale | 10,000 contacts in 15min | Load Test | HIGH |
| NFR-SCAL-04 | Database connection pooling | 50 connections via PgBouncer | Load Test | HIGH |
| NFR-SCAL-05 | Storage scalability | 500 GB without architecture change | Config Audit | MEDIUM |
| NFR-SCAL-06 | Stateless API design | JWT-only, no server sessions | Integration Test | HIGH |

### 4.6 Maintainability (MAINT) — 13 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0010 | Backward compatibility | Across minor versions | CI Gate | MEDIUM |
| MHEWS-NFR-0015 | CI/CD support | Automated pipeline | CI Gate | MEDIUM |
| MHEWS-NFR-0049 | Modular CI/CD | Independent deployment | CI Gate | HIGH |
| MHEWS-NFR-0056 | Modular architecture | Loosely-coupled modules | CI Gate | HIGH |
| MHEWS-NFR-0068 | Documentation governance | Version-controlled | CI Gate | MEDIUM |
| NFR-MAINT-01 | Dependency version pinning | Exact versions | CI Gate | HIGH |
| NFR-MAINT-02 | Database migration integrity | Reversible, non-destructive | CI Gate | CRITICAL |
| NFR-MAINT-03 | Code linting and formatting | ruff + eslint+prettier | CI Gate | HIGH |
| NFR-MAINT-04 | Test coverage minimum | ≥80% backend | CI Gate | HIGH |
| NFR-MAINT-05 | OpenAPI documentation | Auto-generated at /api/schema/ | CI Gate | HIGH |
| NFR-MAINT-06 | Environment parity | Same Docker images | Deployment Test | HIGH |
| NFR-MAINT-07 | Deprecation policy | ≥1 release cycle notice | CI Gate | MEDIUM |
| MHEWS-NFR-0041 | Load testing capability | Pre-release execution | Load Test | MEDIUM |

### 4.7 Observability & Monitoring (OBS) — 10 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0027 | System health checks every 60 minutes | Per data fetcher | Integration Test | HIGH |
| MHEWS-NFR-0028 | Health check endpoints for external monitoring | All services | Integration Test | HIGH |
| MHEWS-NFR-0035 | Infrastructure monitoring | All components | Integration Test | HIGH |
| MHEWS-NFR-0061 | Resource utilization monitoring | CPU, memory, storage | Integration Test | MEDIUM |
| NFR-OBS-01 | Health check endpoint (GET /health/) | 200/503 with sub-checks | Integration Test | HIGH |
| NFR-OBS-02 | Prometheus metrics (GET /metrics/) | Request count, latency, tasks | Integration Test | HIGH |
| NFR-OBS-03 | Alerting on system anomalies | Defined thresholds | Integration Test | HIGH |
| NFR-OBS-04 | Distributed tracing (OpenTelemetry) | All requests and tasks | Integration Test | MEDIUM |
| NFR-OBS-05 | Operations dashboard (Admin-only) | 24h error rate, queue depth | Integration Test | HIGH |
| NFR-OBS-06 | Celery worker monitoring (Flower) | Real-time visibility | Manual Inspection | MEDIUM |

### 4.8 Structured Logging (LOG) — 7 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0069 | Standardized log format (structured JSON) | All modules | Automated Test | HIGH |
| NFR-LOG-01 | Structured JSON log format | timestamp, level, service, request_id | Automated Test | HIGH |
| NFR-LOG-02 | Log levels per environment | Configurable via LOG_LEVEL | Automated Test | HIGH |
| NFR-LOG-03 | No sensitive data in logs | PII/secrets → [REDACTED] | Automated Test | CRITICAL |
| NFR-LOG-04 | Celery task logging | task name, ID, queue, timing | Automated Test | HIGH |
| NFR-LOG-05 | Log retention | ≥90 days primary, ≥2 years archive | Config Audit | HIGH |
| NFR-LOG-06 | Module coverage | All 12 modules emit structured logs | Integration Test | HIGH |

### 4.9 Audit & Immutability (AUD) — 3 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0013 | Compliance archive retention | Per retention policy | Config Audit | HIGH |
| MHEWS-NFR-0031 | Immutable alert timestamps | All lifecycle events | Integration Test | CRITICAL |
| MHEWS-NFR-0032 | Immutable audit logs | No UPDATE/DELETE on audit table | Integration Test | CRITICAL |

### 4.10 Data Sovereignty & Privacy (SOV) — 4 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0036 | Configuration isolation per deployment | No cross-tenant exposure | Config Audit | HIGH |
| MHEWS-NFR-0037 | Data segregation between instances | Multi-tenant isolation | Config Audit | CRITICAL |
| MHEWS-NFR-0038 | Replication support for country instances | Repeatable deployment | Config Audit | MEDIUM |
| MHEWS-NFR-0044 | Data governance (validation, lineage, retention) | Enforced across modules | Config Audit | HIGH |

### 4.11 Usability & Accessibility (UX) — 5 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0039 | i18n framework | Extensible for all text | Accessibility Audit | HIGH |
| MHEWS-NFR-0055 | WCAG 2.1 AA compliance | Intuitive, accessible | Accessibility Audit | HIGH |
| MHEWS-SD-MAP-05 | Map accessibility (keyboard + screen reader) | WCAG 2.2 AA | Accessibility Audit | MEDIUM |
| MHEWS-SD-PORTAL-03 | Portal WCAG accessibility | WCAG 2.2 AA, colour + text | Accessibility Audit | MEDIUM |
| MHEWS-SD-PORTAL-04 | Portal mobile responsiveness | 320px-1440px, Lighthouse ≥80 | Accessibility Audit | MEDIUM |

### 4.12 Portability & Containerization (PORT) — 2 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0012 | Cloud-agnostic deployment | No vendor lock-in | Deployment Test | HIGH |
| MHEWS-NFR-0014 | Containerized deployment (Docker) | All services | Deployment Test | CRITICAL |

### 4.13 Interoperability (INTOP) — 3 NFRs

| Req ID | Statement | Target Value | Verification | Priority |
|--------|-----------|-------------|--------------|----------|
| MHEWS-NFR-0026 | Geospatial CRS standardization | WGS84 / EPSG:4326 | Integration Test | HIGH |
| MHEWS-NFR-0048 | CAP + REST + OGC interoperability | CAP v1.2, REST, WMS/WFS | Integration Test | HIGH |
| MHEWS-NFR-0058 | OGC compliance | WMS, WFS, WCS consume-only | Integration Test | HIGH |

---

## 5. Interface Requirements

### 5.1 Internal Interfaces (IC-01 through IC-07)

Seven interface contracts define integration seams between modules. Full specifications in `consolidation/04_interface_contracts.md`.

| Contract | Direction | Mechanism | Trigger | Guarantee |
|----------|-----------|-----------|---------|-----------|
| IC-01 | M3 → M6 | Django signal `threshold_breached` + Celery | ThresholdBreachEvent created (OPEN) | Notification only; no auto-draft |
| IC-02 | M6 → M7 | Django signal `cap_approved` + Celery | CAPDraft → APPROVED | One DispatchJob per channel; idempotent |
| IC-03 | M7 → M9 | Synchronous audit logger | Each dispatch state change | 7 audit event types |
| IC-04 | M3 → M9 | Synchronous audit logger | Ingestion batch, source failure, breach | 4 audit event types |
| IC-05 | M6 → M4 | REST GET (post-PoC) | Operator requests forecast context | HTTP 404 if no forecast |
| IC-06 | M8 → M2 | Django signal `after_action.completed` | AAR marked COMPLETE | HITL required for threshold mods |
| IC-07 | M6 → External | REST GET `/api/v1/cap/feed/` | External consumer polls | Atom feed; immutable; API key auth |

### 5.2 External Interfaces

| Interface | Protocol | Direction | Auth |
|-----------|----------|-----------|------|
| GDACS GeoRSS Feed | HTTPS GET (RSS) | Inbound data | None (public) |
| ECMWF CDS API | HTTPS REST | Inbound data | API key |
| SendGrid Email API | HTTPS REST | Outbound dispatch | API key |
| Meta WhatsApp Cloud API | HTTPS REST + Webhook | Outbound + inbound receipts | Bearer token + HMAC |
| Anthropic Claude API | HTTPS REST | Outbound LLM call | API key |
| Ollama (Profile B) | HTTP REST (localhost) | Outbound LLM call | None (internal) |
| OpenStreetMap Tiles | HTTPS GET | Inbound tiles | None (public) |
| CAP Feed (Atom) | HTTPS GET | Outbound to consumers | API key |

---

## 6. Appendices

### Appendix A — Requirement ID Cross-Reference

| ID Range | Source | Part | Count |
|----------|--------|------|-------|
| MHEWS-FR-0001 — MHEWS-FR-0370 | Legacy requirements (req-479.csv) | A | 369 FR |
| MHEWS-NFR-0001 — MHEWS-NFR-0070 | Legacy requirements (req-479.csv) | A | 70 NFR |
| MHEWS-SD-MAP-01 — MHEWS-SD-BOUND-02 | System-derived (ghost requirements) | B | 68 FR + 4 NFR |
| MHEWS-FC-ERR-01 — MHEWS-FC-OUV-06 | Functional completeness analysis | C | 37 FR |
| NFR-LOG-01 — NFR-PERF-IMP-02 | NFR completeness analysis | D | 31 NFR |
| **Total** | | | **474 FR + 105 NFR = 579** |

### Appendix B — Glossary

| Term | Definition |
|---|---|
| CAP Draft | An in-progress alert document going through the authoring lifecycle |
| CAP Package | The immutable, finalized CAP XML document with SHA-256 hash |
| Dispatch Job | A unit of work sending a single CAP alert to all contacts via one channel |
| Dispatch Receipt | Per-contact delivery status record for a single dispatch attempt |
| DrillMode | Tenant-level flag forcing all alerts to Exercise status |
| Evidence Package | ZIP archive containing CAP XML, approval chain, dispatch receipts, audit log |
| Golden Path | End-to-end system test from ingestion through dispatch |
| ModelRun | A single execution of a registered forecast model |
| Profile A | Cloud deployment profile (external APIs enabled) |
| Profile B | On-premise deployment profile (fully air-gapped capable) |
| Threshold Breach Event | A recorded instance where observed data exceeded a configured threshold |

---

*End of SRS — CON-D-06*

