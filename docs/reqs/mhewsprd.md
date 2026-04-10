# SECTION III — Annex

## Module Catalog  
1. **Hazard & Taxonomy Management**  
2. **Risk & Scenario Modelling**  
3. **Data Ingestion & Monitoring**  
4. **Forecasting & Nowcasting Engine**  
5. **Impact Analysis & Exposure Modelling**  
6. **Alert Authoring (CAP)**  
7. **Dissemination (Email, Web Portal, WhatsApp)**  
8. **Incident Record & Lifecycle Tracking**  
9. **Audit & Compliance Framework**  
10. **Administration & Access Control (RBAC/ABAC)**  
11. **Integration & API Gateway**  
12. **Preparedness, Drill & Response Support**

## Scope Constraints (Aligned with SRS)
- **In-scope dissemination channels:** Email, Web Portal, WhatsApp only.
- **Out-of-scope dissemination channels:** SMS, Cell Broadcast, mobile push, siren/radio/social media automation.
- **Identity boundary:** local identity authority only; no external federation (SAML/OIDC/LDAP).
- **CAP boundary:** CAP authoring, validation, export, and outbound feed are in scope; inbound CAP hub ingest is out of scope.

## Feature Catalog  

### Hazard & Taxonomy Management  
- **Hazard Catalog CRUD**: create/read/update/delete hazard types and metadata.  
- **Taxonomy & Hierarchy**: manage hazard categories and parent-child relationships.  
- **Threshold Class Definition**: define and version severity/alert thresholds for each hazard.  
- **Cross-Hazard Rules**: specify cascading-trigger relationships between hazards.  
- **Hazard Descriptor Export**: produce standardized hazard profile for other modules.  

### Risk & Scenario Modelling  
- **Scenario Templates**: define scenario with hazard(s), geography, assumptions.  
- **Assumption Profiles**: specify exposure and vulnerability baseline settings.  
- **Trigger Rule Editor**: build threshold-based rules (risk matrix, AND/OR conditions).  
- **Scenario Execution Engine**: run simulations using forecasts and assumptions.  
- **Scenario Comparison & Reporting**: compare runs and export scenario report.  

### Data Ingestion & Monitoring  
- **Source Registration**: configure sensor/forecast feeds (OGC API/SensorThings/API).  
- **Data Normalization**: transform incoming data to standard formats (GeoJSON, OGC features).  
- **Quality Control**: validate schema, detect outliers/missing data, flag issues.  
- **Health Dashboard**: monitor data latency, freshness, and availability with alerts.  
- **Backup & Archival**: store raw and processed data for historical analysis.  

### Forecasting & Nowcasting Engine  
- **Model Registry**: register versioned forecast models (NWP, ensemble, ML).  
- **Run Scheduler**: automate forecasts at required intervals, with lead-time control.  
- **Forecast Archive**: store forecast outputs (grids, tracks) with metadata.  
- **Nowcast Integration**: support rapid-update models (e.g., high-frequency weather).  
- **Confidence Scoring**: evaluate and attach confidence/uncertainty to forecasts.  

### Impact Analysis & Exposure Modelling  
- **Exposure Dataset Manager**: import and version exposure data (population, assets).  
- **Vulnerability Model Registry**: register models/functions linking hazard to impact.  
- **Impact Computation Engine**: overlay hazard footprints with exposure/vulnerability.  
- **Impact Metrics Generator**: compute affected population, economic loss, etc.  
- **Qualitative Impact Narrator**: generate text descriptions if quantitative data missing.  

### Alert Authoring (CAP)  
- **CAP Template Library**: store reusable templates for common hazards/events.  
- **CAP Editor UI**: guided form for alert content, multilingual fields, and areas.  
- **CAP Validation Service**: run CAP v1.2 schema + deployment-configured profile checks (base mandatory; national/GDACS optional).  
- **Versioning & References**: manage <references> for updates/cancels correctly.  
- **Export Package Composer**: assemble CAP XML with attachments (graphics, docs).  

### Dissemination (Email, Web Portal, WhatsApp)  
- **Channel Configuration**: define email/WhatsApp accounts and templates (no SMS/CBS/mobile push).  
- **Audience Segmentation**: manage distribution lists and geofenced groups.  
- **Publish Orchestration**: coordinate sending via each channel simultaneously.  
- **Status Monitoring**: track message sends, deliveries, failures (e.g. webhook receipts).  
- **Web Portal Integration**: update web-based dashboards/alerts in sync.  

### Incident Record & Lifecycle Tracking  
- **Incident Creation**: log new incidents/hazard events and severity classification.  
- **Timeline Events**: record important milestones (trigger, forecast, alert, end).  
- **Artifact Linking**: attach relevant items (forecasts, scenarios, CAPs) to incident.  
- **Status Workflows**: support state transitions (monitoring → warning → closed).  
- **After-Action Reporting**: collect outcome data and lessons learned for post-mortem.  

### Audit & Compliance Framework  
- **Audit Event Logging**: capture all key actions (logins, changes, approvals).  
- **Immutable Ledger**: write events to tamper-evident store with hashes.  
- **Evidence Packaging**: compile CAPs, validation reports, receipts into audit packs.  
- **Compliance Monitoring**: dashboards for policy adherence and integrity checks.  
- **Retention Policies**: enforce how long logs and evidence are kept.  

### Administration & Access Control (RBAC/ABAC)  
- **User & Role Management**: CRUD for user accounts, roles, and tenancy.  
- **Attribute Policy Editor**: define ABAC rules (e.g. geographical scope).  
- **Authentication Services**: local sign-in with JWT/session controls (no external federation or LDAP).  
- **Session & MFA**: configure session timeouts, multi-factor options.  
- **Access Review Tools**: generate reports on who has what permissions.  

### Integration & API Gateway  
- **API Key Management**: issue/rotate API keys with tenant scoping.  
- **Schema Versioning**: maintain contract versions for OGC/SensorThings APIs.  
- **Rate Limiting**: throttle clients to protect from abuse.  
- **Adapters/Connectors**: built-in connectors for common external systems (e.g., weather APIs).  
- **Webhook Receiver**: endpoints for channel webhooks (e.g., WhatsApp status updates).  

### Preparedness, Drill & Response Support
- **Drill Mode Control**: tenant-level drill flag forcing CAP status `Exercise`.
- **Drill Isolation**: drill alerts are restricted to drill audiences and never published as live alerts.
- **SOP Repository**: manage response plans and standard operating procedures linked to hazards.
- **Exercise Reporting**: generate post-drill summaries and participation metrics.
- **After-Action Feedback Loop**: capture lessons learned and feed calibration recommendations to risk/threshold workflows.

## User Story Catalog (by Module)  

### Hazard & Taxonomy Management  
1. **Hazard CRUD:** *As a Tenant Admin, I want to create, update, and delete hazard types (with metadata) so that the system can handle new or changing hazard definitions.*  
   - **Acceptance:** Ability to add a hazard with name, category, and threshold; hazard appears in selection lists.  
2. **Threshold Configuration:** *As a Tenant Admin, I want to define alert thresholds for each hazard (e.g., severity levels) so that alerts are triggered correctly.*  
   - **Acceptance:** Can configure threshold ranges; threshold values validated and stored.  
3. **Cross-Hazard Rule:** *As a User, I want to specify that one hazard automatically triggers related hazard alerts (e.g., earthquake ⇒ tsunami) so the system can cascade warnings.*  
   - **Acceptance:** Define rule; when hazard A alert is published, hazard B is flagged.  

### Risk & Scenario Modelling  
4. **Scenario Template Creation:** *As an Analyst, I want to create scenario templates (hazards, exposures, vulnerability) so that operational staff can run them during an event.*  
   - **Acceptance:** Template saved with input fields; can be reused.  
5. **Trigger Rule Builder:** *As an Analyst, I want to define trigger rules (e.g., if flood > X, then action Y) so that scenario results can recommend actions.*  
   - **Acceptance:** Can save logic; rule is applied during scenario runs.  
6. **Run Scenarios:** *As an Operator, I want to execute a predefined scenario with current data and view the results so I can assess potential impacts.*  
   - **Acceptance:** Scenario run completes with metrics; shows differences from baseline.  

### Data Ingestion & Monitoring  
7. **Source Configuration:** *As an Admin, I want to register a new data source (e.g., weather API) so the system begins ingesting relevant data.*  
   - **Acceptance:** System can connect (test), and data appears in monitoring.  
8. **Quality Alerts:** *As an Operator, I want to be alerted if data feed stops or is delayed so I know not to trust stale data.*  
   - **Acceptance:** "Data stale" warning shown if feed > SLA.  
9. **Schema Validation:** *As a Developer, I want incoming data validated against schema so malformed data is rejected with logs.*  
   - **Acceptance:** Invalid payloads are logged and not stored; audit event recorded.  

### Forecasting & Nowcasting Engine  
10. **Model Registration:** *As an Analyst, I want to register a new forecast model (with version) so I can use it for future simulations.*  
    - **Acceptance:** Model metadata stored; visible in run options.  
11. **Scheduled Runs:** *As an Operator, I want system to automatically run forecasts at set intervals so that forecasts are always up-to-date.*  
    - **Acceptance:** Runs occur on schedule; failures send alerts.  
12. **Forecast Display:** *As an Operator, I want to view forecast outputs and confidence scores so I can interpret their reliability.*  
    - **Acceptance:** Forecast map and confidence shown; user can query forecast at a point.  

### Impact Analysis & Exposure Modelling  
13. **Exposure Upload:** *As an Analyst, I want to upload population and infrastructure data so impact computations can use current exposures.*  
    - **Acceptance:** Uploaded data validated and can be selected in impact runs.  
14. **Vulnerability Functions:** *As an Analyst, I want to define how hazard intensity maps to impact severity (e.g., flood depth → % damage) so impacts are quantified.*  
    - **Acceptance:** Function saved; used in impact calculation pipeline.  
15. **Impact Summary:** *As an Operator, I want to see total affected population and assets so I can include key figures in warnings.*  
    - **Acceptance:** System displays impact metrics (pop, assets) after analysis.  

### Alert Authoring (CAP)  
16. **CAP Drafting:** *As an Operator, I want to create an alert using CAP templates so that required fields are captured correctly.*  
    - **Acceptance:** Form enforces mandatory CAP fields (urgency, severity, area).  
17. **Validate CAP:** *As an Operator, I want to validate the CAP content before sending so that format and profile rules are checked.*  
    - **Acceptance:** Validation passes or error list shown; cannot publish if errors.  
18. **Update/Cancel:** *As an Approver, I want to send an Update or Cancel message referencing the original alert so recipients know it supersedes previous info.*  
    - **Acceptance:** New CAP includes correct <references>; system only allows one pending update/cancel chain.  

### Dissemination (Email, Portal, WhatsApp)  
19. **Select Channels:** *As an Operator, I want to choose dissemination channels and audience so that alerts go to the right people.*  
    - **Acceptance:** Alert queued to chosen channels; contact lists respected.  
20. **Delivery Monitoring:** *As an Operator, I want to see delivery status (sent, delivered, failed) for each channel so I know if dissemination succeeded.*  
    - **Acceptance:** Channel status updated in real-time; failures flagged.  
21. **Portal Publish:** *As an Operator, I want alerts to appear on the public portal with geotagged info so the public can access them.*  
    - **Acceptance:** Alert appears on portal map/list with same text.  

### Incident Record & Lifecycle  
22. **Incident Creation:** *As an Operator, I want to open a new incident when a hazard is detected so I can track it end-to-end.*  
    - **Acceptance:** New incident record created; linked to hazard event metadata.  
23. **Status Transition:** *As an Operator, I want to move an incident through stages (Monitoring, Warning, Closed) so I manage workflow.*  
    - **Acceptance:** Cannot skip required stages; each transition logged.  
24. **Attach CAP to Incident:** *As an Approver, I want each CAP to be linked to the appropriate incident so we maintain context.*  
    - **Acceptance:** CAP export screen associates the alert with the active incident.  

### Audit & Compliance  
25. **Comprehensive Logging:** *As an Auditor, I want every change (user login, edit, publish) logged so we can reconstruct events.*  
    - **Acceptance:** Audit entries exist for all listed operations with user/time.  
26. **Immutable Audit Store:** *As a Security Officer, I want the logs stored immutably so they can’t be tampered with.*  
    - **Acceptance:** Attempted modifications to audit store are detected and denied.  
27. **Evidence Packets:** *As an Auditor, I want to export all relevant artifacts for an alert (CAP, logs, receipts) so I can hand it to regulators.*  
    - **Acceptance:** System compiles a package containing CAP XML, validation report, receipts, and prints a timestamped archive.  

### Administration & Access Control  
28. **Role Assignment:** *As a Tenant Admin, I want to assign roles (Operator, Approver, Analyst) to users so I control permissions.*  
    - **Acceptance:** User gains or loses access to features based on role.  
29. **Attribute Policy:** *As a Tenant Admin, I want to restrict who can publish alerts for certain regions so we enforce jurisdiction.*  
    - **Acceptance:** User cannot target or approve alerts outside their authorized region.  
30. **Access Review Reports:** *As an Auditor, I want reports of who has what roles so I can check for excessive privileges.*  
    - **Acceptance:** System generates a CSV of users, roles, and last login dates on demand.  

### Integration & API Gateway  
31. **API Key Issuance:** *As an Admin, I want to issue API keys for external clients so they can fetch data.*  
    - **Acceptance:** New API key created with expiration and scope restrictions.  
32. **Schema Enforcement:** *As a Developer, I want incoming API requests validated against JSON schemas so erroneous calls fail with clear errors.*  
    - **Acceptance:** Invalid requests return 4xx with validation messages; valid ones pass through.  
33. **Webhook Handling:** *As an Operator, I want WhatsApp delivery receipts to be automatically processed so alert statuses update.*  
    - **Acceptance:** Incoming webhook triggers lookup of corresponding message; status updated accordingly.  

### Preparedness, Drill & Response Support
34. **Activate Drill Mode:** *As an Admin, I want to activate drill mode so all alerts are marked Exercise and isolated from live dissemination.*  
    - **Acceptance:** CAP status is set to `Exercise`; live/public channels are excluded.
35. **Run Full Alert Drill:** *As an Operator, I want to execute end-to-end drill workflows so teams can train without public impact.*  
    - **Acceptance:** Workflow mirrors production states; no live recipient receives drill messages.
36. **Manage SOPs:** *As an Admin, I want to upload and version SOPs linked to hazard types so responders can access current procedures during incidents.*  
    - **Acceptance:** SOP versions are stored, searchable, and linked to hazards/incidents.

## Verification Mapping (Story/Requirement → Method)  

For each story and original requirement, specify how to verify:
- **Test Cases:** automated or manual tests to execute the requirement.  
- **Demonstrations:** show UI or report output.  
- **Inspection:** review configuration or code.  

Example:
- Story 18 “Update/Cancel”: Verify by creating an alert, sending update, and checking `<references>` tag in exported CAP (requirements 68,76).  
- Req 1 “Access Review Report”: Verify by generating report and checking columns (manual inspection).  

(Detailed mapping omitted for brevity.)  

## Standardized Requirements (“The system shall…”)

Below are rewritten key requirements in the prescribed form (legacy reqs preserved afterward). Only a subset is shown due to volume:

- **Req 1 (Access Review):** The system shall generate periodic access review reports listing all user accounts and permissions.  
- **Req 2 (Account Lockout):** The system shall lock user accounts after configurable failed login attempts for a configurable lockout duration.  
- **Req 3 (AI Confidence):** The system shall include an AI-model confidence score with every forecast output.  
- **Req 4 (Layer Toggling):** The system shall allow toggling hazard, exposure, and vulnerability layers on/off in the map display.  
- **Req 27 (Tasking):** The system shall assign and track field data collection tasks for sensors.  
- **Req 68 (CAP Compliance):** The system shall generate CAP alerts conforming to the CAP v1.2 schema and configured profile.  
- **Req 76 (CAP XML):** The system shall generate CAP messages in valid XML format with correct encoding.  
- **Req 304 (Local Authentication):** The system shall provide local authentication and authorization without external federation dependencies.  
- **Req 340 (Data Recency):** The system shall display data freshness indicators for each time series.  

*Original requirements from spreadsheet (excerpt)*:
1. *Access Review Report*: “The system shall generate periodic access review reports listing all user accounts and permissions.”  
2. *Account Lockout*: “The system shall lock accounts after configurable failed login attempts, with an admin reset.”  
3. *AI Confidence Score*: “The system shall include a confidence score in all AI-generated forecasts.”  
4. *Layer Toggling*: “The system shall allow toggling hazard, exposure, and vulnerability layers in the UI.”  
... *(remaining requirements preserved as-is in documentation)*  

## Traceability Matrix (Hybrid)  

| Legacy Req No. | Standardized Req              | User Story             | Module                        | Compliance References                                      |
|---------------|------------------------------|------------------------|-------------------------------|----------------------------------------------------------|
| 1             | Generate access review report| 30                     | Admin/Access Control          | -                                                        |
| 2             | Account lockout after X fails| 30                     | Admin/Access Control          | NIST SP 800-92 (log management best practices)           |
| 3             | AI forecast confidence score | 12                     | Forecasting Engine            | WMO Impact-Based Guidelines (uncertainty)               |
| 4             | Toggle map layers            | 9                      | Data Ingestion & Monitoring   | UN Women Checklist (mapping layers in alerts)           |
| 68            | CAP v1.2 compliance          | 16                     | Alert Authoring (CAP)         | OASIS CAP v1.2 Spec【3†L1-L8】, deployment-configured profile |
| 76            | CAP XML generation           | 16                     | Alert Authoring (CAP)         | OASIS CAP v1.2 Spec【3†L1-L8】                            |
| 311           | Hazard taxonomy updates      | 1                      | Hazard & Taxonomy Management  | UN Women Checklist (data updates)                       |
| ...           | ...                          | ...                    | ...                           | ...                                                      |

*(Note: This table would continue to map each requirement to its module and relevant compliance reference. Citations source compliance standards as needed.)*  

## Compliance Matrices  

### CAP Standards (OASIS CAP v1.2)  
| Module Feature               | CAP Requirement                      | Reference                              |
|------------------------------|--------------------------------------|----------------------------------------|
| CAP Template Library         | Must populate all <info> fields      | OASIS CAP v1.2 Spec【3†L1-L8】         |
| Update/Cancel Workflow       | Must include <references> on updates | OASIS CAP v1.2 Spec【3†L1-L8】         |
| Area targeting              | Support polygons/geocodes            | OASIS CAP v1.2 Spec【3†L1-L8】         |

### OGC Standards  
| Module Feature         | OGC Standard                         | Reference                              |
|------------------------|--------------------------------------|----------------------------------------|
| GIS Data Ingestion     | OGC API – Features (RESTful data)    | OGC API Features Spec【3†L1-L8】       |
| Sensor Data Ingestion  | OGC SensorThings (IoT Observations)  | OGC SensorThings Spec【3†L1-L4】       |
| Feature Serving        | GeoJSON outputs, spatial queries     | OGC standards for EDR/Features         |
| API Schemas            | OpenAPI 3.0 JSON Schema for services | (best practice)                        |

### ISO/NIST Security Standards (design-aligned)  
| Requirement             | Standard / Control                     | Reference                                      |
|-------------------------|----------------------------------------|------------------------------------------------|
| Audit logging           | NIST SP 800-92 (Log management)        |【3†L12-L17】                                   |
| ABAC authorization      | NIST SP 800-162 (Attribute-based AC)    |【3†L9-L11】                                    |
| Container security      | NIST SP 800-190 (Container security)    |【3†L18-L20】                                   |
| Microservices security  | NIST SP 800-204 (Microservices)         |【3†L21-L24】                                   |
| Data encryption         | NIST FIPS 140-2 (if used)               | industry standard, not explicitly cited here   |

### MHEWS Checklist (multi-hazard EWS elements)  
| Module/Feature                  | Checklist Item                                   | Reference                                            |
|---------------------------------|--------------------------------------------------|------------------------------------------------------|
| Hazard & Risk Knowledge         | Maintain hazard list + risk maps                 | ReliefWeb MHEWS Checklist【5†L1-L4】               |
| Monitoring & Data Quality       | Real-time data feeds + quality control           | UN Women EWS Checklist【2†L5-L11】                 |
| Dissemination                   | Multi-channel reach & feedback loop               | UN Women EWS Checklist【2†L12-L15】               |
| Preparedness Response           | Scenario planning + training                     | UN Women EWS Checklist【2†L16-L20】               |
| Governance Process              | Defined roles/responsibilities (Admin & Users)   | UN Women EWS Checklist【2†L21-L24】               |

## Conceptual Glossary  

- **CAP (Common Alerting Protocol)**: An XML-based standard for emergency alerts. ([3†L1-L8])  
- **OGC API – Features**: RESTful standard for accessing geospatial feature data. ([3†L1-L8])  
- **ABAC (Attribute-Based Access Control)**: Access control based on user/object attributes. ([3†L9-L11])  
- **RBAC (Role-Based Access Control)**: Access control based on user roles. (NIST)  
- **Incident**: A tracked event or series of hazards requiring coordinated response.  
- **Scenario**: A modeled situation combining hazard forecasts with assumptions on exposure/vulnerability.  
- **Exposure Data**: Spatial data on population/assets at risk.  
- **Vulnerability Model**: A function mapping hazard intensity to expected impact.  
- **Audit Ledger**: An immutable log of security and operational events.  
- **Trigger Rule**: A condition (often threshold-based) that prompts an action or alert.  

## Data Model  

### Conceptual Entities & Relationships  
(See diagram below.)

```mermaid
erDiagram
    Tenant ||--o{ User            : manages
    User }o--|| Role              : has
    Tenant ||--o{ Incident        : contains
    Incident ||--|{ CAPAlert       : generates
    CAPAlert ||--|{ Dissemination  : initiates
    Incident ||--|{ ModelRun       : includes
    ModelRun ||--|{ Forecast       : produces
    Forecast }|..|{ Exposure       : overlays with
    Exposure }|..|{ Impact         : yields
    HazardType ||--o{ ModelRun      : uses
    HazardType ||--o{ Incident      : categorized_by
    AuditEvent ||--|| CAPAlert      : references
    AuditEvent ||--|| ModelRun      : references
    ```
*Conceptual: Tenants have users with roles. Incidents lead to CAP alerts and disseminations. Forecasts (from model runs) combine with exposure to compute impacts. Hazard types categorize incidents and model runs.*  

### Logical Schema (example tables)  
- **Tenants**(tenant_id PK, name, region)  
- **Users**(user_id PK, tenant_id FK, name, email, status)  
- **Roles**(role_id PK, name, permissions)  
- **UserRoles**(user_id FK, role_id FK)  
- **HazardTypes**(hazard_id PK, name, category, threshold_config)  
- **Incidents**(incident_id PK, tenant_id FK, hazard_id FK, status, start_time, description)  
- **CAPAlerts**(cap_id PK, incident_id FK, msg_type, issued_time, content_xml)  
- **Forecasts**(forecast_id PK, model_id FK, timestamp, geojson_data, confidence_score)  
- **Exposures**(exposure_id PK, name, data_blob, last_updated)  
- **Impacts**(impact_id PK, forecast_id FK, exposure_id FK, affected_count, damage_estimate)  
- **AuditEvents**(audit_id PK, user_id FK, event_time, event_type, details)  

### Example Physical Schema Snippet (Mermaid ER)  

```mermaid
erDiagram
    TENANT ||--o{ USER      : contains
    USER ||--o{ ROLE        : has
    TENANT ||--o{ INCIDENT  : manages
    INCIDENT ||--o{ CAPALERT : issues
    FORECAST ||--o{ IMPACT   : produces
    EXPOSURE ||--o{ IMPACT   : used_by
```

## Sample CAP Alert Structure  

```xml
<alert xmlns="urn:oasis:names:tc:emergency:cap:1.2">
  <identifier>20260303T150000-ABC-123</identifier>
  <sender>sender@example.com</sender>
  <sent>2026-03-03T15:00:00-00:00</sent>
  <status>Actual</status>
  <msgType>Alert</msgType>
  <scope>Public</scope>
  <info>
    <category>Met</category>
    <event>Severe Thunderstorm Warning</event>
    <urgency>Immediate</urgency>
    <severity>Severe</severity>
    <certainty>Likely</certainty>
    <effective>2026-03-03T15:00:00-00:00</effective>
    <expires>2026-03-03T16:00:00-00:00</expires>
    <headline>Severe thunderstorm in metropolitan area</headline>
    <description>Severe thunderstorm expected to impact downtown region ...</description>
    <instruction>Seek shelter immediately in sturdy building.</instruction>
    <area>
      <areaDesc>Downtown Metro Region</areaDesc>
      <polygon>45.123,-93.456 45.456,-93.789 45.789,-93.123</polygon>
    </area>
  </info>
</alert>
```

## Sample API Endpoints & JSON Schemas  

- **POST /api/v1/incidents**  
  **Request**: JSON body with `{ "tenant_id": "...", "hazard_id": "...", "start_time": "...", "description": "..." }`.  
  **Response**: 201 Created with `{"incident_id": "...", "status": "Monitoring"}`.  

- **GET /api/v1/incidents/{id}**  
  **Response**: JSON with incident details, linked CAPs and status timeline.  
  _Schema excerpt:_ `{ "incident_id": string, "tenant_id": string, "hazard_id": string, "status": string, "created_at": string, "caps": [ {...} ] }`.  

- **POST /api/v1/cap/validate**  
  **Request**: raw CAP XML.  
  **Response**: `{ "valid": true, "errors": [] }`.  

- **POST /api/v1/forecast/runs**  
  **Request**: `{ "model_id": "...", "parameters": {...} }`.  
  **Response**: `{ "run_id": "...", "status": "Scheduled" }`.  

*(Endpoints would be documented with full JSON Schema in specification; above are illustrative.)*  

DOCUMENT COMPLETE.
