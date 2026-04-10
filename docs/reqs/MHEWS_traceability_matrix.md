# Full Traceability Matrix (IEEE 29148)
**Deliverable:** CON-D-05
**Date:** 2026-03-10
**Version:** 1.0
**Status:** Baseline
**Standard:** IEEE 29148:2018, Section 6.6 (Traceability)

---

## 1. Introduction

### 1.1 Purpose

This document provides bidirectional traceability for all 579 MHEWS requirements across four dimensions:

1. **Forward Traceability** (REQ → DES → TST): requirement → design element → test ID
2. **Backward Traceability** (TST → REQ): test ID → requirements verified
3. **Risk Traceability** (REQ → RISK): requirements linked to 30 risks
4. **Coverage Summary**: gap analysis

### 1.2 Sources

| Artefact | Location |
|---|---|
| Normalized Requirements (579) | `consolidation/11_normalized_requirements.md` |
| Req Traceability Map | `consolidation/16_req_traceability_map.md` |
| Module-Feature Trace (61 TST-*) | `consolidation/13_module_feature_trace.md` |
| Software Design Description | `consolidation/18_software_design_description.md` |
| V&V Plan | `consolidation/20_vv_plan.md` |
| Risk Register (30 RISK-*) | `consolidation/19_risk_register.md` |

### 1.3 Design Element Key

| Prefix | Django App | SDD § |
|---|---|---|
| `hazards.*` | `apps.hazards` | 3.3 (M1) |
| `risk.*` | `apps.risk` | 3.5 (M2) |
| `ingestion.*` | `apps.ingestion` | 3.4 (M3) |
| `forecasting.*` | `apps.forecasting` | 3.6 (M4) |
| `impact.*` | `apps.impact` | 3.7 (M5) |
| `alerts.*` | `apps.alerts` | 3.8 (M6) |
| `dissemination.*` | `apps.dissemination` | 3.9 (M7) |
| `incidents.*` | `apps.incidents` | 3.10 (M8) |
| `audit.*` | `apps.audit` | 3.11 (M9) |
| `accounts.*` | `apps.accounts` | 3.2 (M10) |
| `gateway.*` | `apps.gateway` | 3.12 (M11) |
| `preparedness.*` | `apps.preparedness` | 3.13 (M12) |
| `core.*` | `apps.core` | 3.1 |

### 1.4 Test Level Key

| Code | Level | From V&V Plan §2 |
|---|---|---|
| U | Unit Test | pytest, per-commit |
| I | Integration Test | DRF APITestCase, per-PR |
| S | System Test | Docker Compose E2E, per-sprint |
| A | Acceptance Test | Manual + automated, sprint review |

---

## 2. Forward Traceability (REQ → DES → TST)

### 2.1 M1 — Hazard & Taxonomy Management

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-FR-0059 | `hazards.ThresholdConfig` | TST-M1-002 | U I S A |
| MHEWS-FR-0074 | `hazards.HazardType`, `HazardTypeViewSet` | TST-M1-001 | U I S A |
| MHEWS-FR-0110 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0154 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0156 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0157 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0158 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0189 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0194 | `hazards.ThresholdConfig` | TST-M1-002 | U I S A |
| MHEWS-FR-0195 | `hazards.ThresholdConfig` | TST-M1-002 | U I S A |
| MHEWS-FR-0211 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0215 | `hazards.HazardType` | TST-M1-001, TST-AI-002 | U I S A |
| MHEWS-FR-0231 | `hazards.ThresholdConfig` | TST-M1-002 | U I S A |
| MHEWS-FR-0247 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0248 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0249 | `hazards.HazardType` | TST-M1-001, TST-AI-002 | U I S A |
| MHEWS-FR-0251 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0252 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FR-0324 | `hazards.AdminBoundary` | TST-M1-003 | U I — A |
| MHEWS-FR-0342 | `hazards.HazardType` | TST-M1-001 | U I S A |
| MHEWS-FC-INV-05 | `hazards.ThresholdConfig` (unique constraint) | TST-M1-002 | U I S A |

### 2.2 M2 — Risk & Scenario Modelling

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-FR-0021 | `risk.RiskScenario` | TST-M2-001 | U I — A |
| MHEWS-FR-0057 | `risk.RiskScenario`, `alerts.CAPDraft` | TST-M2-004 | U I — A |
| MHEWS-FR-0068 | `risk.RiskScenario` | TST-M2-001 | U I — A |
| MHEWS-FR-0108 | `risk.ScenarioResult` | TST-M2-002 | U I — A |
| MHEWS-FR-0164 | `risk.ScenarioResult`, Results UI | TST-M2-003 | — I — A |
| MHEWS-FR-0179 | `risk.RiskScenario`, `alerts.CAPDraft` | TST-M2-004 | U I — A |
| MHEWS-FR-0302 | `risk.RiskScenario` | TST-M2-001 | U I — A |
| MHEWS-FR-0326 | `risk.RiskScenario` | TST-M2-001 | U I — A |
| MHEWS-FR-0327 | `risk.RiskScenario` | TST-M2-001 | U I — A |
| MHEWS-FR-0332 | `risk.RiskScenario` | TST-M2-001 | U I — A |
| MHEWS-FR-0333 | `risk.ScenarioResult` | TST-M2-002 | U I — A |

### 2.3 M3 — Data Ingestion & Monitoring

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-FR-0023 | `ingestion.DataSource`, `DataSourceViewSet` | TST-M3-001 | U I S A |
| MHEWS-FR-0034 | `ingestion.ThresholdBreachEvent` | TST-M3-003 | U I S A |
| MHEWS-FR-0035 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0036 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0037 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0038 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0039 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0040 | `ingestion.ThresholdBreachEvent` | TST-M3-003 | U I S A |
| MHEWS-FR-0041 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0051 | `ingestion.ThresholdBreachEvent` | TST-M3-003 | U I S A |
| MHEWS-FR-0052 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0060 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0061 | `ingestion.DataSource`, Health Dashboard | TST-M3-004 | — I S A |
| MHEWS-FR-0062 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0083 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0084 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0085 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0087 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0091 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0094 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0097 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0106 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0133 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0139 | `ingestion.DataSource`, Health Dashboard | TST-M3-004 | — I S A |
| MHEWS-FR-0180 | `ingestion.DataSource`, Health Dashboard | TST-M3-004 | — I S A |
| MHEWS-FR-0181 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0182 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0183 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0185 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0186 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0187 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0188 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0190 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0218 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0228 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0229 | `ingestion.DataSource`, Health Dashboard | TST-M3-004 | — I S A |
| MHEWS-FR-0230 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0232 | `ingestion.ThresholdBreachEvent` | TST-M3-003 | U I S A |
| MHEWS-FR-0234 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0235 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0266 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0273 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0276 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0284 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0285 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0288 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0289 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0300 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0309 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0322 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0323 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0331 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0334 | `ingestion.ObservationRecord`, Dashboard | TST-M3-002 | — I S A |
| MHEWS-FR-0335 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0340 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0341 | `ingestion.DataSource` | TST-M3-001 | U I S A |
| MHEWS-FR-0355 | `ingestion.ThresholdBreachEvent` | TST-M3-003 | U I S A |
| MHEWS-FC-ERR-06 | `ingestion.DataSource` STM-05 | TST-M3-001, TST-M3-004 | U I S A |
| MHEWS-FC-STM-03 | `ingestion.ThresholdBreachEvent` STM | TST-M3-003 | U I S A |
| MHEWS-FC-STM-05 | `ingestion.DataSource` STM | TST-M3-004 | — I S A |
| MHEWS-FC-INV-09 | `ingestion.DataSourceViewSet` validation | TST-M3-001 | U I S A |
| MHEWS-SD-STREAM-02 | `ingestion.ObservationRecord`, SSE | TST-M3-002 | — I S A |
| MHEWS-NFR-0011 | `ingestion.poll_data_source` Celery | TST-M3-003 | Load Test |
| MHEWS-NFR-0045 | `ingestion.poll_data_source` timing | TST-M3-002 | Load Test |
| MHEWS-SD-STREAM-03 | SSE streaming latency | TST-M3-002 | Load Test |

### 2.4 M4 — Forecasting & Nowcasting Engine

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-FR-0006 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0007 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0012 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0053 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0058 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-FR-0069 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-FR-0072 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-FR-0107 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0124 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0127 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-FR-0128 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0140 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-FR-0141 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0142 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-FR-0143 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0144 | `forecasting.ModelRunViewSet` trigger | TST-M4-002 | U I — A |
| MHEWS-FR-0191 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0192 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-FR-0199 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0200 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0233 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0246 | `forecasting.ModelRunViewSet` trigger | TST-M4-002 | U I — A |
| MHEWS-FR-0290 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0292 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0293 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0294 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0295 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0296 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0297 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0298 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0299 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-FR-0315 | `forecasting.ForecastModel` | TST-M4-001, TST-M4-005 | U I — A |
| MHEWS-FR-0319 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0347 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-FR-0357 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0358 | `forecasting.ForecastModel` | TST-M4-001 | U I — A |
| MHEWS-FR-0365 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-FC-STM-06 | `forecasting.ModelRun` STM | TST-M4-001, TST-M4-005 | U I — A |
| MHEWS-FC-ERR-10 | `forecasting.execute_model_run` | TST-M4-005 | U I — A |
| MHEWS-SD-COMPUTE-01 | `forecasting.ModelRun`, Celery Beat | TST-M4-005 | U I — A |
| MHEWS-SD-COMPUTE-02 | `forecasting.ForecastModel`, Docker | TST-M4-001 | U I — A |
| MHEWS-SD-COMPUTE-03 | `forecasting.ForecastModel`, Docker | TST-M4-001 | U I — A |
| MHEWS-SD-COMPUTE-04 | `forecasting.ModelRun`, Celery Beat | TST-M4-005 | U I — A |
| MHEWS-SD-COMPUTE-05 | `forecasting.ModelRun`, Celery Beat | TST-M4-004, TST-M4-005 | U I — A |
| MHEWS-SD-UQ-01 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-SD-UQ-02 | `forecasting.ModelRun`, Viz UI | TST-M4-003 | — I — A |
| MHEWS-SD-MODEL-01 | `forecasting.ModelRunViewSet` | TST-M4-002 | U I — A |
| MHEWS-SD-MODEL-02 | `forecasting.ModelRun`, Celery Beat | TST-M4-005 | U I — A |
| MHEWS-SD-GDB-03 | `forecasting.ModelRun`, PostGIS | TST-M4-003 | — I — A |
| MHEWS-NFR-0025 | `forecasting.ModelRun` validation | TST-M4-001 | Load Test |
| NFR-PERF-FCST-01 | `forecasting.execute_model_run` | TST-M4-005 | Load Test |

### 2.5 M5 — Impact Analysis & Exposure Modelling

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-FR-0002 | `impact.ImpactLayer`, Heatmap UI | TST-M5-003 | — I — A |
| MHEWS-FR-0003 | `impact.ImpactLayer`, Heatmap UI | TST-M5-003 | — I — A |
| MHEWS-FR-0020 | `impact.ImpactLayer`, `alerts.CAPDraft` | TST-M5-005 | U I — A |
| MHEWS-FR-0073 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0078 | `impact.ExposureDataset` | TST-M5-001 | U I — A |
| MHEWS-FR-0131 | `impact.ExposureDataset` | TST-M5-001 | U I — A |
| MHEWS-FR-0132 | `impact.ExposureDataset` | TST-M5-001 | U I — A |
| MHEWS-FR-0150 | `impact.ImpactLayer`, Heatmap UI | TST-M5-003 | — I — A |
| MHEWS-FR-0153 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0159 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0160 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0161 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0162 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0163 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0165 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0166 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0167 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0168 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0169 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0170 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0171 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0172 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0173 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0174 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0175 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0176 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0177 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0178 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0193 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0219 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0220 | `impact.ExposureDataset` | TST-M5-001 | U I — A |
| MHEWS-FR-0221 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0222 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0223 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0224 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0225 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0236 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0254 | `impact.ImpactLayer`, Heatmap UI | TST-M5-003 | — I — A |
| MHEWS-FR-0260 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0261 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0262 | `impact.ImpactLayer`, Heatmap UI | TST-M5-003 | — I — A |
| MHEWS-FR-0264 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0286 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0336 | `impact.ExposureDataset` | TST-M5-001 | U I — A |
| MHEWS-FR-0337 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0345 | `impact.ImpactLayer`, `alerts.CAPDraft` | TST-M5-005 | U I — A |
| MHEWS-FR-0346 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0354 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FR-0366 | `impact.ExposureDataset` | TST-M5-001 | U I — A |
| MHEWS-FR-0367 | `impact.ExposureDataset` | TST-M5-001 | U I — A |
| MHEWS-FR-0370 | `impact.ImpactLayer`, computation | TST-M5-002 | U I — A |
| MHEWS-FC-STM-10 | `alerts.LLMOutput` STM (narratives) | TST-M5-004 | U I — A |
| MHEWS-SD-STORE-03 | `impact.ExposureDataset`, MinIO/S3 | TST-M5-001 | U I — A |
| MHEWS-NFR-0033 | `impact.ImpactLayer` query timing | TST-M5-002 | Load Test |
| MHEWS-NFR-0034 | `impact.ImpactLayer` spatial perf | TST-M5-002 | Load Test |
| NFR-PERF-IMP-01 | `impact` spatial query P95 | TST-M5-002 | Load Test |
| NFR-PERF-IMP-02 | `impact` spatial index | TST-M5-002 | Load Test |

### 2.6 M6 — Alert Authoring (CAP)

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-FR-0001 | `alerts.LLMOutput`, AI narrative | TST-M6-003 | U I S A |
| MHEWS-FR-0008 | `alerts.LLMOutput`, AI narrative | TST-M6-003 | U I S A |
| MHEWS-FR-0015 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0016 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-FR-0018 | `alerts.CAPDraft`, approval | TST-M6-005 | U I S A |
| MHEWS-FR-0019 | `alerts.CAPDraft`, approval | TST-M6-005 | U I S A |
| MHEWS-FR-0022 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-FR-0026 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0027 | `alerts.CAPDraft`, approval | TST-M6-005 | U I S A |
| MHEWS-FR-0028 | `alerts.CAPDraft`, Update/Cancel | TST-M6-006 | U I S A |
| MHEWS-FR-0029 | `alerts.CAPTemplate` | TST-M6-001 | U I S A |
| MHEWS-FR-0030 | `alerts.CAPDraft`, Update/Cancel | TST-M6-006 | U I S A |
| MHEWS-FR-0031 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0032 | `alerts.CAPDraft`, Update/Cancel | TST-M6-006 | U I S A |
| MHEWS-FR-0043 | `alerts.CAPDraft`, approval | TST-M6-005 | U I S A |
| MHEWS-FR-0056 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-FR-0063 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-FR-0064 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-FR-0089 | `alerts.LLMOutput`, AI narrative | TST-M6-003 | U I S A |
| MHEWS-FR-0098 | `alerts.CAPDraft`, approval | TST-M6-005 | U I S A |
| MHEWS-FR-0099 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0100 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-FR-0101 | `alerts.LLMOutput`, AI narrative | TST-M6-003 | U I S A |
| MHEWS-FR-0103 | `alerts.CAPDraft`, i18n | TST-M6-007 | U I — A |
| MHEWS-FR-0121 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0122 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0123 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0146 | `alerts.CAPTemplate` | TST-M6-001 | U I S A |
| MHEWS-FR-0196 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0197 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0198 | `alerts.CAPDraft`, approval | TST-M6-005 | U I S A |
| MHEWS-FR-0202 | `alerts.CAPTemplate` | TST-M6-001 | U I S A |
| MHEWS-FR-0203 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0204 | `alerts.CAPTemplate` | TST-M6-001 | U I S A |
| MHEWS-FR-0237 | `alerts.CAPDraft`, approval | TST-M6-005 | U I S A |
| MHEWS-FR-0238 | `alerts.CAPDraft`, approval | TST-M6-005 | U I S A |
| MHEWS-FR-0239 | `alerts.CAPDraft`, i18n | TST-M6-007 | U I — A |
| MHEWS-FR-0250 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0255 | `alerts.LLMOutput`, AI narrative | TST-M6-003 | U I S A |
| MHEWS-FR-0256 | `alerts.CAPDraft`, approval | TST-M6-005 | U I S A |
| MHEWS-FR-0303 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0304 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-FR-0306 | `alerts.CAPDraft`, i18n | TST-M6-007 | U I — A |
| MHEWS-FR-0310 | `alerts.CAPTemplate` | TST-M6-001 | U I S A |
| MHEWS-FR-0311 | `alerts.CAPDraft`, i18n | TST-M6-007 | U I — A |
| MHEWS-FR-0312 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0317 | `alerts.CAPTemplate` | TST-M6-001 | U I S A |
| MHEWS-FR-0350 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-FR-0359 | `alerts.CAPDraft`, approval | TST-M6-005 | U I S A |
| MHEWS-FC-STM-01 | `alerts.CAPDraft` STM | TST-M6-005 | U I S A |
| MHEWS-FC-ERR-07 | `alerts.ValidationResult` error | TST-M6-004 | U I S A |
| MHEWS-FC-INV-06 | `alerts.CAPDraftViewSet` constraints | TST-M6-002 | — I S A |
| MHEWS-FC-INV-07 | `alerts.ValidationResult` XSD | TST-M6-004 | U I S A |
| MHEWS-FC-OUV-01 | `alerts.ValidationResult` output | TST-M6-004 | U I S A |
| MHEWS-FC-OUV-03 | `alerts.LLMOutput` response | TST-M6-003 | U I S A |
| MHEWS-SD-LLM-01 | `alerts.LLMOutput`, LLM adapter | TST-M6-003 | U I S A |
| MHEWS-SD-LLM-02 | `alerts.LLMOutput`, LLM adapter | TST-M6-003 | U I S A |
| MHEWS-SD-LLM-03 | `alerts.LLMOutput`, LLM adapter | TST-M6-003 | U I S A |
| MHEWS-SD-LLM-05 | `alerts.LLMOutput`, LLM adapter | TST-M6-003 | U I S A |
| MHEWS-SD-LLM-06 | `alerts.LLMOutput`, `accounts.SystemSetting` | TST-M6-003, TST-M10-005 | U I S A |
| MHEWS-SD-VALID-01 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-SD-VALID-02 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-SD-VALID-03 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-SD-VALID-04 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-SD-VALID-05 | `alerts.ValidationResult` | TST-M6-004 | U I S A |
| MHEWS-SD-DRAW-01 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-SD-DRAW-02 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-SD-DRAW-03 | `alerts.CAPDraft`, Editor UI | TST-M6-002 | — I S A |
| MHEWS-NFR-0004 | `alerts.CAPDraft`, UI workflow | TST-M6-005 | Acc. Audit |
| NFR-PERF-LLM-01 | `alerts.LLMOutput` timeout 30s | TST-M6-003 | Load Test |

### 2.7 M7 — Dissemination

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-FR-0004 | `dissemination.CommunityReport` | TST-M7-009 | U I — A |
| MHEWS-FR-0044 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0066 | `dissemination.DispatchJobViewSet` retry | TST-M7-007 | U I S A |
| MHEWS-FR-0079 | `dissemination.CommunityReport` | TST-M7-009 | U I — A |
| MHEWS-FR-0104 | `dissemination.ContactGroup` | TST-M7-006 | U I — A |
| MHEWS-FR-0109 | `dissemination.CommunityReport` | TST-M7-009 | U I — A |
| MHEWS-FR-0112 | `dissemination.CommunityReport`, AI cat. | TST-M7-009 | U I — A |
| MHEWS-FR-0114 | `dissemination.CommunityReport` | TST-M7-009 | U I — A |
| MHEWS-FR-0115 | `dissemination.CommunityReport` | TST-M7-009 | U I — A |
| MHEWS-FR-0116 | `dissemination.CommunityReport` | TST-M7-009 | U I — A |
| MHEWS-FR-0118 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0119 | `dissemination.DispatchJobViewSet` retry | TST-M7-007 | U I S A |
| MHEWS-FR-0129 | `dissemination.DispatchJob`, email | TST-M7-002 | U I S A |
| MHEWS-FR-0147 | `dissemination.ContactGroup` | TST-M7-006 | U I — A |
| MHEWS-FR-0201 | `dissemination.ContactGroup` | TST-M7-006 | U I — A |
| MHEWS-FR-0205 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0206 | `dissemination.CommunityReport` | TST-M7-009 | U I — A |
| MHEWS-FR-0208 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0212 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0216 | `dissemination.CommunityReport` | TST-M7-009 | U I — A |
| MHEWS-FR-0226 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0240 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0241 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0243 | `dissemination.ContactGroup` | TST-M7-006 | U I — A |
| MHEWS-FR-0267 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0280 | `dissemination.DispatchJobViewSet` retry | TST-M7-007 | U I S A |
| MHEWS-FR-0287 | `dissemination.ContactGroup` | TST-M7-006 | U I — A |
| MHEWS-FR-0301 | `dissemination.DispatchJob`, all channels | TST-M7-002, TST-M7-003, TST-M7-004 | U I S A |
| MHEWS-FR-0321 | `dissemination.DispatchJob`, WA adapter | TST-M7-003 | U I S A |
| MHEWS-FR-0343 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0348 | `dissemination.DispatchJob`, Dashboard | TST-M7-001 | — I S A |
| MHEWS-FR-0349 | `dissemination.Contact` | TST-M7-005 | U I — A |
| MHEWS-FR-0368 | `dissemination.DispatchJob`, email | TST-M7-002 | U I S A |
| MHEWS-FR-0369 | `dissemination.DispatchJob`, WA adapter | TST-M7-003 | U I S A |
| MHEWS-FC-STM-02 | `dissemination.DispatchJob` STM | TST-M7-001 | — I S A |
| MHEWS-FC-STM-07 | `dissemination.CommunityReport` STM | TST-M7-009 | U I — A |
| MHEWS-FC-STM-08 | `dissemination.Contact` consent STM | TST-M7-005 | U I — A |
| MHEWS-FC-STM-09 | `dissemination.DispatchReceipt` STM | TST-M7-001, TST-M7-002, TST-M7-003 | U I S A |
| MHEWS-FC-ERR-08 | `dissemination.DispatchJob` retry | TST-M7-007 | U I S A |
| MHEWS-FC-INV-10 | `dissemination.ContactViewSet` phone val | TST-M7-005 | U I — A |
| MHEWS-FC-OUV-04 | `dissemination.DispatchJob` WA response | TST-M7-003 | U I S A |
| MHEWS-SD-PORTAL-01 | `dissemination.portal_urls` | TST-M7-004 | — I S A |
| MHEWS-SD-PORTAL-02 | `dissemination.portal_urls` | TST-M7-004 | — I S A |
| MHEWS-SD-EMAIL-01 | SendGrid adapter | TST-M7-002 | U I S A |
| MHEWS-SD-EMAIL-02 | SendGrid adapter | TST-M7-002 | U I S A |
| MHEWS-SD-EMAIL-03 | SendGrid adapter | TST-M7-002 | U I S A |
| MHEWS-SD-EMAIL-04 | SendGrid adapter | TST-M7-002 | U I S A |
| MHEWS-SD-WA-01 | WA adapter | TST-M7-003 | U I S A |
| MHEWS-SD-WA-02 | WA adapter | TST-M7-003 | U I S A |
| MHEWS-SD-WA-03 | WA adapter, channel config | TST-M7-003, TST-M7-008 | U I S A |
| MHEWS-SD-WA-04 | WA adapter | TST-M7-003 | U I S A |
| MHEWS-SD-WA-05 | `mock_whatsapp` | TST-M7-003 | U I S A |
| MHEWS-SD-CONTACT-01 | `dissemination.Contact` | TST-M7-005 | U I — A |
| MHEWS-SD-CONTACT-02 | `dissemination.ContactGroup` | TST-M7-006 | U I — A |
| MHEWS-SD-CONTACT-03 | `dissemination.Contact`, `accounts` | TST-M7-005 | U I — A |
| MHEWS-SD-CONTACT-04 | `dissemination.Contact` | TST-M7-005 | U I — A |
| MHEWS-SD-CONTACT-05 | `dissemination.DispatchJob` | TST-M7-001 | — I S A |
| MHEWS-SD-CONTACT-06 | `dissemination.Contact`, `accounts` | TST-M7-010 | U I — A |
| MHEWS-SD-FEEDBACK-01 | `dissemination.CommunityReport` | TST-M7-009 | U I — A |
| MHEWS-SD-FEEDBACK-02 | `dissemination.CommunityReport`, AI | TST-M7-009 | U I — A |
| MHEWS-SD-FEEDBACK-03 | `dissemination.CommunityReport` mod | TST-M7-009 | U I — A |
| MHEWS-SD-FEEDBACK-04 | `dissemination.CommunityReport`, `ingestion` | TST-M7-009 | U I — A |
| MHEWS-SD-FEEDBACK-05 | `dissemination.CommunityReport`, `incidents` | TST-M8-005 | U I — A |
| MHEWS-SD-SELFREG-01 | `dissemination.Contact`, self-reg | TST-M7-010 | U I — A |
| MHEWS-SD-GDB-02 | `dissemination.ContactGroup`, PostGIS | TST-M7-006 | U I — A |
| MHEWS-SD-SCOPE-01 | Documentation (scope exclusion) | TST-M7-003 | — — — — |
| MHEWS-NFR-0018 | `dissemination.dispatch_*_batch` | TST-M7-002, TST-M7-003 | Load Test |
| MHEWS-NFR-0042 | `dissemination.dispatch_*_batch` timing | TST-M7-002, TST-M7-003 | Load Test |
| MHEWS-SD-PORTAL-03 | Portal accessibility | TST-M7-004 | Acc. Audit |
| MHEWS-SD-PORTAL-04 | Portal load time | TST-M7-004 | Load Test |
| NFR-SCAL-03 | WA dispatch capacity | TST-M7-003 | Load Test |

### 2.8 M8 — Incident Record & Lifecycle Tracking

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-FR-0033 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0113 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0125 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0126 | `incidents.Incident` | TST-M8-001 | U I — A |
| MHEWS-FR-0138 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0149 | `incidents.Incident` | TST-M8-001 | U I — A |
| MHEWS-FR-0184 | `incidents.Incident` | TST-M8-001 | U I — A |
| MHEWS-FR-0207 | `incidents.AfterActionReport`, UI | TST-M8-002 | U I — A |
| MHEWS-FR-0209 | `incidents.Incident` | TST-M8-001 | U I — A |
| MHEWS-FR-0210 | `incidents.Incident` | TST-M8-001 | U I — A |
| MHEWS-FR-0213 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0214 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0217 | `incidents.Incident`, search | TST-M8-003 | U I — A |
| MHEWS-FR-0242 | `incidents.AfterActionReport`, UI | TST-M8-002 | U I — A |
| MHEWS-FR-0245 | `incidents.Incident` | TST-M8-001 | U I — A |
| MHEWS-FR-0257 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0263 | `incidents.Incident`, search | TST-M8-003 | U I — A |
| MHEWS-FR-0265 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0270 | `incidents.Incident`, search | TST-M8-003 | U I — A |
| MHEWS-FR-0271 | `incidents.Incident` | TST-M8-001 | U I — A |
| MHEWS-FR-0274 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0275 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0277 | `incidents.Incident` | TST-M8-001 | U I — A |
| MHEWS-FR-0278 | `incidents.AfterActionReport` | TST-M8-002 | U I — A |
| MHEWS-FR-0316 | `incidents.Incident` | TST-M8-001 | U I — A |
| MHEWS-FR-0344 | `incidents.Incident` | TST-M8-001 | U I — A |
| MHEWS-FR-0356 | `incidents.Incident`, search | TST-M8-003 | U I — A |
| MHEWS-FC-STM-04 | `incidents.Incident` STM | TST-M8-001 | U I — A |

### 2.9 M9 — Audit & Compliance Framework

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-FR-0009 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0010 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0011 | `audit.AuditEntry`, immutability | TST-M9-003 | U I S — |
| MHEWS-FR-0013 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0014 | `audit.EvidencePackage` | TST-M9-002 | U I S A |
| MHEWS-FR-0017 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0024 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0025 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0045 | `audit.EvidencePackage` | TST-M9-002 | U I S A |
| MHEWS-FR-0046 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0047 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0048 | `audit.AuditEntry`, immutability | TST-M9-003 | U I S — |
| MHEWS-FR-0049 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0050 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0054 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0055 | `audit.EvidencePackage` | TST-M9-002 | U I S A |
| MHEWS-FR-0065 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0067 | `audit.AuditEntry`, compliance | TST-M9-005 | — I S A |
| MHEWS-FR-0070 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0071 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0075 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0081 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0086 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0088 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0093 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0095 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0096 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0105 | `audit.AuditEntry`, compliance | TST-M9-005 | — I S A |
| MHEWS-FR-0111 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0117 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0130 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0148 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0151 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0155 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0253 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0258 | `audit.EvidencePackage` | TST-M9-002 | U I S A |
| MHEWS-FR-0259 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0268 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0269 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0272 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0281 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0282 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0283 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0291 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0313 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0314 | `audit.AuditEntry`, compliance | TST-M9-005 | — I S A |
| MHEWS-FR-0318 | `audit.AuditEntry`, immutability | TST-M9-003 | U I S — |
| MHEWS-FR-0320 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0325 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0328 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0338 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0339 | `audit.AuditEntry`, compliance | TST-M9-005 | — I S A |
| MHEWS-FR-0351 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FR-0361 | `audit.AuditEntry`, viewer | TST-M9-001 | — I S A |
| MHEWS-FR-0364 | `audit.AuditEntry`, reporting | TST-M9-004 | U I — A |
| MHEWS-FC-ERR-09 | `audit.AuditEntry`, immutability err | TST-M9-003 | U I S — |
| MHEWS-FC-OUV-06 | `audit.AuditEntry`, output validation | TST-M9-003 | U I S — |
| MHEWS-SD-LLM-04 | `audit.AuditEntry`, LLM audit | TST-M9-001 | — I S A |
| MHEWS-SD-STORE-04 | `audit.EvidencePackage`, MinIO/S3 | TST-M9-002 | U I S A |
| MHEWS-SD-DRILL-02 | `audit.AuditEntry`, drill isolation | TST-M9-001, TST-M12-001 | — I S A |
| MHEWS-NFR-0007 | `audit.AuditEntry`, replay SLA | TST-M9-004 | Load Test |
| MHEWS-NFR-0013 | `audit.AuditEntry`, retention | TST-M9-001 | Integration Test |
| MHEWS-NFR-0023 | `audit.AuditEntry`, replay SLA | TST-M9-004 | Load Test |
| NFR-PERF-AUD-01 | `audit.AuditEntry`, query perf | TST-M9-004 | Load Test |

### 2.10 M10 — Administration & Access Control

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-NFR-0001 | `accounts.CustomUser`, RBAC | TST-M10-002 | U I — A |
| MHEWS-NFR-0002 | `accounts.CustomUser`, lockout | TST-M10-001 | U I S A |
| MHEWS-FR-0005 | `accounts.SystemSetting` | TST-M10-005 | U I — A |
| MHEWS-FR-0076 | `accounts.SystemSetting` | TST-M10-005 | U I — A |
| MHEWS-FR-0077 | `accounts.SystemSetting` | TST-M10-005 | U I — A |
| MHEWS-FR-0080 | `accounts.CustomUser`, activity | TST-M10-004 | — I — A |
| MHEWS-FR-0102 | `accounts.SystemSetting` | TST-M10-005 | U I — A |
| MHEWS-FR-0120 | `accounts.SystemSetting` | TST-M10-005 | U I — A |
| MHEWS-FR-0135 | `accounts.CustomUser`, `UserViewSet` | TST-M10-002 | U I — A |
| MHEWS-FR-0136 | `accounts.CustomUser`, `UserViewSet` | TST-M10-002 | U I — A |
| MHEWS-FR-0137 | `accounts.CustomUser`, Django Groups | TST-M10-003 | U I S — |
| MHEWS-FR-0145 | `accounts.CustomUser`, `UserViewSet` | TST-M10-002 | U I — A |
| MHEWS-FR-0152 | `accounts.CustomUser`, Django Groups | TST-M10-003 | U I S — |
| MHEWS-FR-0244 | `accounts.CustomUser`, Django Groups | TST-M10-003 | U I S — |
| MHEWS-FR-0279 | `accounts.CustomUser`, `UserViewSet` | TST-M10-002 | U I — A |
| MHEWS-FR-0329 | `accounts.CustomUser`, Django Groups | TST-M10-003 | U I S — |
| MHEWS-FR-0330 | `accounts.CustomUser`, `UserViewSet` | TST-M10-002 | U I — A |
| MHEWS-FR-0352 | `accounts.CustomUser`, `UserViewSet` | TST-M10-002 | U I — A |
| MHEWS-FR-0353 | `accounts.CustomUser`, `UserViewSet` | TST-M10-002 | U I — A |
| MHEWS-FR-0360 | `accounts.CustomUser`, `UserViewSet` | TST-M10-002 | U I — A |
| MHEWS-FR-0362 | `accounts.CustomUser`, `UserViewSet` | TST-M10-002 | U I — A |
| MHEWS-FR-0363 | `accounts.CustomUser`, `UserViewSet` | TST-M10-002 | U I — A |
| MHEWS-FC-INV-08 | `accounts.CustomUser`, password val | TST-M10-002 | U I — A |

### 2.11 M11 — Integration & API Gateway

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-NFR-0005 | `gateway.APIKey`, rate limiting | TST-M11-003 | U I — — |
| MHEWS-NFR-0006 | `gateway.APIKey`, rate limiting | TST-M11-003 | U I — — |
| MHEWS-FR-0042 | `gateway.ConnectorConfig` | TST-M11-001 | U I — A |
| MHEWS-FR-0134 | `gateway.ConnectorConfig` | TST-M11-001 | U I — A |
| MHEWS-FR-0227 | `gateway.ConnectorConfig` | TST-M11-001 | U I — A |

### 2.12 M12 — Preparedness, Drill & Response

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-SD-DRILL-01 | `preparedness.Drill` | TST-M12-001 | U I S A |
| MHEWS-FC-STM-11 | `preparedness.Drill` STM | TST-M12-001 | U I S A |

### 2.13 AI Chatbot (Cross-Module)

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-NFR-0003 | `alerts.LLMOutput`, chatbot | TST-AI-001 | U I — A |

### 2.14 Cross-Cutting Requirements

FRs and FCs that span modules or live in `core` middleware.

| Req ID | Design Element | TST ID | Levels |
|---|---|---|---|
| MHEWS-FR-0082 | `core` data export | (Integration) | — I — — |
| MHEWS-FR-0090 | `core` data sync | (Integration) | — I — — |
| MHEWS-FR-0305 | `core` i18n, `django-modeltranslation` | TST-M6-007 | U I — A |
| MHEWS-FR-0307 | `hazards.HazardType` xref | TST-M1-001 | U I S A |
| MHEWS-FR-0308 | `alerts.CAPDraft` xref | TST-M6-002 | — I S A |
| MHEWS-FC-ERR-01 | `core.middleware.ErrorResponse` | (Integration) | — I — — |
| MHEWS-FC-ERR-02 | `core.middleware.RequestID` | (Integration) | — I — — |
| MHEWS-FC-ERR-03 | `core.middleware.ErrorResponse` | (Integration) | — I — — |
| MHEWS-FC-ERR-04 | `core.middleware.ErrorResponse` | (Integration) | — I — — |
| MHEWS-FC-ERR-05 | `core.middleware.ErrorResponse` | (Integration) | — I — — |
| MHEWS-FC-INV-01 | `core` validation utilities | (Integration) | — I — — |
| MHEWS-FC-INV-02 | `core` serializer sanitization | (Security Scan) | — — — — |
| MHEWS-FC-INV-03 | `core` file upload validation | (Integration) | — I — — |
| MHEWS-FC-INV-04 | `hazards.AdminBoundary` validation | TST-M1-003 | U I — A |
| MHEWS-FC-OUV-02 | `core` API response schema | (CI Gate) | — — — — |
| MHEWS-FC-OUV-05 | `core` GeoJSON serializer | (Integration) | — I — — |
| MHEWS-SD-MAP-01 | MapLibre GL JS config | TST-M5-003, TST-M4-003 | — I — A |
| MHEWS-SD-MAP-02 | Base map tile source | (Deployment) | — — — — |
| MHEWS-SD-MAP-03 | MapLibre GL JS layers | TST-M5-003, TST-M3-002 | — I — A |
| MHEWS-SD-MAP-04 | MapLibre forecast layers | TST-M4-003 | — I — A |
| MHEWS-SD-MAP-05 | Map accessibility | (Acc. Audit) | — — — — |
| MHEWS-SD-STREAM-01 | Redis pub/sub event bus | (Integration) | — I — — |
| MHEWS-SD-STREAM-04 | Redis Sentinel | (DR Test) | — — — — |
| MHEWS-SD-STORE-01 | MinIO/S3 adapter | (Integration) | — I — — |
| MHEWS-SD-STORE-02 | `core.FileAttachment` | (Integration) | — I — — |
| MHEWS-SD-GDB-01 | PostGIS configuration | (Deployment) | — — — — |
| MHEWS-SD-TILE-01 | Self-hosted tile server | (Deployment) | — — — — |
| MHEWS-SD-I18N-01 | `django-modeltranslation` | TST-M6-007 | U I — A |
| MHEWS-SD-I18N-02 | `django-modeltranslation` | TST-M6-007 | U I — A |
| MHEWS-SD-BOUND-01 | `hazards.AdminBoundary`, Shapefile | TST-M1-003 | U I — A |
| MHEWS-SD-BOUND-02 | `hazards.AdminBoundary`, Shapefile | TST-M1-003 | U I — A |

### 2.15 Cross-Cutting NFRs (105 total)

NFRs verified through environment-level methods, not feature-specific tests.

| Req ID | Cat | Design Element | Method |
|---|---|---|---|
| MHEWS-NFR-0008 | SCAL | Docker Compose scaling | Load Test |
| MHEWS-NFR-0009 | REL | PostgreSQL backup | DR Test |
| MHEWS-NFR-0010 | MAINT | API versioning | CI Gate |
| MHEWS-NFR-0012 | PORT | Docker, no cloud lock-in | Config Audit |
| MHEWS-NFR-0014 | PORT | Dockerfile | Config Audit |
| MHEWS-NFR-0015 | MAINT | GitHub Actions CI/CD | CI Gate |
| MHEWS-NFR-0016 | REL | PostgreSQL replication | DR Test |
| MHEWS-NFR-0017 | AVAIL | DR runbook | DR Test |
| MHEWS-NFR-0019 | SEC | Encryption at rest | Security Scan |
| MHEWS-NFR-0020 | SEC | TLS config | Security Scan |
| MHEWS-NFR-0021 | SEC | Key rotation | Security Scan |
| MHEWS-NFR-0022 | REL | Error handling middleware | Integration Test |
| MHEWS-NFR-0024 | REL | Celery queue isolation | Integration Test |
| MHEWS-NFR-0026 | INTOP | PostGIS, GeoJSON output | Integration Test |
| MHEWS-NFR-0027 | OBS | Prometheus metrics | Integration Test |
| MHEWS-NFR-0028 | AVAIL | Health check endpoints | Integration Test |
| MHEWS-NFR-0029 | SCAL | Docker resource limits | Load Test |
| MHEWS-NFR-0030 | PERF | E2E pipeline timing | Load Test |
| MHEWS-NFR-0031 | AUD | Hash chain validation | Integration Test |
| MHEWS-NFR-0032 | MAINT | Code documentation | CI Gate |
| MHEWS-NFR-0035 | AVAIL | Container auto-restart | DR Test |
| MHEWS-NFR-0036 | SEC | OWASP ZAP scan | Security Scan |
| MHEWS-NFR-0037 | SEC | Dependency scanning | Security Scan |
| MHEWS-NFR-0038 | SOV | Data residency config | Config Audit |
| MHEWS-NFR-0039 | UX | RTL language support | Acc. Audit |
| MHEWS-NFR-0040 | SEC | CSRF, XSS protections | Security Scan |
| MHEWS-NFR-0041 | PERF | API response P95 | Load Test |
| MHEWS-NFR-0043 | AVAIL | Zero-downtime deploy | DR Test |
| MHEWS-NFR-0044 | SEC | PII / GDPR | Security Scan |
| MHEWS-NFR-0046 | SEC | Input sanitization | Security Scan |
| MHEWS-NFR-0047 | AVAIL | Service health monitoring | DR Test |
| MHEWS-NFR-0048 | MAINT | Django admin customization | CI Gate |
| MHEWS-NFR-0049 | SCAL | Horizontal scaling | Load Test |
| MHEWS-NFR-0050 | PERF | DB query optimization | Load Test |
| MHEWS-NFR-0051 | AVAIL | Log aggregation | DR Test |
| MHEWS-NFR-0052 | MAINT | Environment parity | Config Audit |
| MHEWS-NFR-0053 | MAINT | Docker image optimization | CI Gate |
| MHEWS-NFR-0054 | SEC | Session management | Security Scan |
| MHEWS-NFR-0055 | UX | WCAG 2.1 AA | Acc. Audit |
| MHEWS-NFR-0056 | AVAIL | Graceful degradation | Integration Test |
| MHEWS-NFR-0057 | SEC | MFA enforcement | Security Scan |
| MHEWS-NFR-0058 | MAINT | API error standards | CI Gate |
| MHEWS-NFR-0059 | SEC | Password policy | Security Scan |
| MHEWS-NFR-0060 | PERF | Concurrent users | Load Test |
| MHEWS-NFR-0061 | SCAL | DB connection pooling | Load Test |
| MHEWS-NFR-0062 | MAINT | Migration strategy | CI Gate |
| MHEWS-NFR-0063 | AVAIL | Backup verification | DR Test |
| MHEWS-NFR-0064 | MAINT | Config management | Config Audit |
| MHEWS-NFR-0065 | SEC | Patch management SLA | Security Scan |
| MHEWS-NFR-0066 | SEC | Session timeout | Security Scan |
| MHEWS-NFR-0067 | AVAIL | Service restart | DR Test |
| MHEWS-NFR-0068 | MAINT | Logging standards | CI Gate |
| MHEWS-NFR-0069 | MAINT | Monitoring dashboard | Integration Test |
| MHEWS-NFR-0070 | SCAL | Cache eviction | Load Test |
| NFR-LOG-01 | LOG | Structured JSON logging | Integration Test |
| NFR-LOG-02 | LOG | Log rotation / retention | Config Audit |
| NFR-LOG-03 | LOG | PII scrubbing | Integration Test |
| NFR-LOG-04 | LOG | Request correlation IDs | Integration Test |
| NFR-LOG-05 | LOG | Log aggregation pipeline | Config Audit |
| NFR-LOG-06 | LOG | Log level configuration | Integration Test |
| NFR-SCAL-01 | SCAL | API horizontal scaling | Load Test |
| NFR-SCAL-02 | SCAL | Ingestion throughput | Load Test |
| NFR-SCAL-04 | SCAL | PgBouncer pooling | Load Test |
| NFR-SCAL-05 | SCAL | Redis cluster scaling | Load Test |
| NFR-SCAL-06 | SCAL | Celery auto-scaling | Load Test |
| NFR-MAINT-01 | MAINT | Dependency pinning | CI Gate |
| NFR-MAINT-02 | MAINT | DB migration tooling | CI Gate |
| NFR-MAINT-03 | MAINT | Code review standards | CI Gate |
| NFR-MAINT-04 | MAINT | Test coverage 80% | Automated Test |
| NFR-MAINT-05 | MAINT | OpenAPI documentation | Automated Test |
| NFR-MAINT-06 | MAINT | Docker image versioning | CI Gate |
| NFR-MAINT-07 | MAINT | Feature flag framework | CI Gate |
| NFR-OBS-01 | OBS | Prometheus export | Integration Test |
| NFR-OBS-02 | OBS | Grafana dashboards | Config Audit |
| NFR-OBS-03 | OBS | Stale data alerting | Integration Test |
| NFR-OBS-04 | OBS | Uptime monitoring | DR Test |
| NFR-OBS-05 | OBS | Compliance dashboard | Integration Test |
| NFR-OBS-06 | OBS | Alert pipeline tracing | Integration Test |
| NFR-PERF-BATCH-01 | PERF | Celery queue isolation | Load Test |

---

## 3. Backward Traceability (TST → REQ)

Reverse mapping: each test ID → all requirements it verifies. Derived from Section 2 forward tables.

### 3.1 M1 — Hazard & Taxonomy Management

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M1-001 | Hazard Type Registry | MHEWS-FR-0074, FR-0110, FR-0154, FR-0156, FR-0157, FR-0158, FR-0189, FR-0211, FR-0215, FR-0247, FR-0248, FR-0249, FR-0251, FR-0252, FR-0342, FR-0307 |
| TST-M1-002 | Threshold Management | MHEWS-FR-0059, FR-0194, FR-0195, FR-0231, MHEWS-FC-INV-05 |
| TST-M1-003 | Area Registry | MHEWS-FR-0324, MHEWS-FC-INV-04, MHEWS-SD-BOUND-01, SD-BOUND-02 |

### 3.2 M2 — Risk & Scenario Modelling

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M2-001 | Scenario Builder | MHEWS-FR-0021, FR-0068, FR-0302, FR-0326, FR-0327, FR-0332 |
| TST-M2-002 | Scenario Execution Engine | MHEWS-FR-0108, FR-0333 |
| TST-M2-003 | Scenario Results Viewer | MHEWS-FR-0164 |
| TST-M2-004 | Scenario-to-CAP Linkage | MHEWS-FR-0057, FR-0179 |

### 3.3 M3 — Data Ingestion & Monitoring

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M3-001 | Connector Registry | MHEWS-FR-0023, FR-0035, FR-0036, FR-0037, FR-0039, FR-0041, FR-0052, FR-0083, FR-0084, FR-0085, FR-0094, FR-0133, FR-0185, FR-0186, FR-0187, FR-0190, FR-0234, FR-0235, FR-0273, FR-0284, FR-0288, FR-0300, FR-0309, FR-0322, FR-0323, FR-0331, FR-0335, FR-0340, FR-0341, MHEWS-FC-ERR-06, FC-INV-09 |
| TST-M3-002 | Data Monitoring Dashboard | MHEWS-FR-0038, FR-0060, FR-0062, FR-0087, FR-0091, FR-0097, FR-0106, FR-0181, FR-0182, FR-0183, FR-0188, FR-0218, FR-0228, FR-0230, FR-0266, FR-0276, FR-0285, FR-0289, FR-0334, MHEWS-SD-STREAM-02, SD-STREAM-03, MHEWS-SD-MAP-03, MHEWS-NFR-0045 |
| TST-M3-003 | Threshold Evaluation | MHEWS-FR-0034, FR-0040, FR-0051, FR-0232, FR-0355, MHEWS-FC-STM-03, MHEWS-NFR-0011 |
| TST-M3-004 | Health Dashboard | MHEWS-FR-0061, FR-0139, FR-0180, FR-0229, MHEWS-FC-ERR-06, FC-STM-05 |

### 3.4 M4 — Forecasting & Nowcasting Engine

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M4-001 | Model Registry | MHEWS-FR-0006, FR-0007, FR-0012, FR-0053, FR-0107, FR-0124, FR-0128, FR-0141, FR-0143, FR-0191, FR-0199, FR-0200, FR-0233, FR-0290, FR-0292, FR-0293, FR-0294, FR-0295, FR-0296, FR-0297, FR-0298, FR-0315, FR-0319, FR-0357, FR-0358, MHEWS-FC-STM-06, MHEWS-SD-COMPUTE-02, SD-COMPUTE-03, MHEWS-NFR-0025 |
| TST-M4-002 | Manual Forecast Trigger | MHEWS-FR-0144, FR-0246, MHEWS-SD-MODEL-01 |
| TST-M4-003 | Forecast Visualization | MHEWS-FR-0058, FR-0069, FR-0072, FR-0127, FR-0140, FR-0142, FR-0192, FR-0299, FR-0347, FR-0365, MHEWS-SD-UQ-01, SD-UQ-02, SD-GDB-03, MHEWS-SD-MAP-01, SD-MAP-04 |
| TST-M4-004 | Forecast Run Notifications | MHEWS-SD-COMPUTE-05 |
| TST-M4-005 | Scheduled Model Execution | MHEWS-FR-0315, MHEWS-FC-STM-06, FC-ERR-10, MHEWS-SD-COMPUTE-01, SD-COMPUTE-04, SD-COMPUTE-05, SD-MODEL-02, NFR-PERF-FCST-01 |

### 3.5 M5 — Impact Analysis & Exposure Modelling

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M5-001 | Exposure Data Management | MHEWS-FR-0078, FR-0131, FR-0132, FR-0220, FR-0336, FR-0366, FR-0367, MHEWS-SD-STORE-03 |
| TST-M5-002 | Impact Computation Engine | MHEWS-FR-0073, FR-0153, FR-0159, FR-0160, FR-0161, FR-0162, FR-0163, FR-0165, FR-0166, FR-0167, FR-0168, FR-0169, FR-0170, FR-0171, FR-0172, FR-0173, FR-0174, FR-0175, FR-0176, FR-0177, FR-0178, FR-0193, FR-0219, FR-0221, FR-0222, FR-0223, FR-0224, FR-0225, FR-0236, FR-0260, FR-0261, FR-0264, FR-0286, FR-0337, FR-0346, FR-0354, FR-0370, MHEWS-NFR-0033, NFR-0034, NFR-PERF-IMP-01, NFR-PERF-IMP-02 |
| TST-M5-003 | Impact Heatmap Viewer | MHEWS-FR-0002, FR-0003, FR-0150, FR-0254, FR-0262, MHEWS-SD-MAP-01, SD-MAP-03 |
| TST-M5-004 | Qualitative Impact Narrator | MHEWS-FC-STM-10 |
| TST-M5-005 | Impact-to-CAP Linkage | MHEWS-FR-0020, FR-0345 |

### 3.6 M6 — Alert Authoring (CAP)

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M6-001 | CAP Template Library | MHEWS-FR-0029, FR-0146, FR-0202, FR-0204, FR-0310, FR-0317 |
| TST-M6-002 | CAP Editor UI | MHEWS-FR-0015, FR-0026, FR-0031, FR-0099, FR-0121, FR-0122, FR-0123, FR-0196, FR-0197, FR-0203, FR-0250, FR-0303, FR-0312, FR-0350, FR-0308, MHEWS-FC-INV-06, MHEWS-SD-DRAW-01, SD-DRAW-02, SD-DRAW-03 |
| TST-M6-003 | AI Narrative Integration | MHEWS-FR-0001, FR-0008, FR-0089, FR-0101, FR-0255, MHEWS-FC-OUV-03, MHEWS-SD-LLM-01, SD-LLM-02, SD-LLM-03, SD-LLM-05, SD-LLM-06, NFR-PERF-LLM-01 |
| TST-M6-004 | CAP Validation Service | MHEWS-FR-0016, FR-0022, FR-0056, FR-0063, FR-0064, FR-0100, FR-0304, MHEWS-FC-ERR-07, FC-INV-07, FC-OUV-01, MHEWS-SD-VALID-01, SD-VALID-02, SD-VALID-03, SD-VALID-04, SD-VALID-05 |
| TST-M6-005 | Approval Workflow (Dual-Auth) | MHEWS-FR-0018, FR-0019, FR-0027, FR-0043, FR-0098, FR-0198, FR-0237, FR-0238, FR-0256, FR-0359, MHEWS-FC-STM-01, MHEWS-NFR-0004 |
| TST-M6-006 | CAP Update/Cancel | MHEWS-FR-0028, FR-0030, FR-0032 |
| TST-M6-007 | Multilingual Alert Authoring | MHEWS-FR-0103, FR-0239, FR-0306, FR-0311, FR-0305, MHEWS-SD-I18N-01, SD-I18N-02 |

### 3.7 M7 — Dissemination

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M7-001 | Dispatch Status Dashboard | MHEWS-FR-0044, FR-0118, FR-0205, FR-0208, FR-0212, FR-0226, FR-0240, FR-0241, FR-0267, FR-0343, FR-0348, MHEWS-FC-STM-02, FC-STM-09, MHEWS-SD-CONTACT-05 |
| TST-M7-002 | Email Dispatch | MHEWS-FR-0129, FR-0301, FR-0368, MHEWS-FC-STM-09, MHEWS-SD-EMAIL-01, SD-EMAIL-02, SD-EMAIL-03, SD-EMAIL-04, MHEWS-NFR-0018, NFR-0042 |
| TST-M7-003 | WhatsApp Dispatch | MHEWS-FR-0301, FR-0321, FR-0369, MHEWS-FC-STM-09, FC-OUV-04, MHEWS-SD-WA-01, SD-WA-02, SD-WA-03, SD-WA-04, SD-WA-05, SD-SCOPE-01, MHEWS-NFR-0018, NFR-0042, NFR-SCAL-03 |
| TST-M7-004 | Web Portal (Public Alerts) | MHEWS-FR-0301, MHEWS-SD-PORTAL-01, SD-PORTAL-02, SD-PORTAL-03, SD-PORTAL-04 |
| TST-M7-005 | Contact Management | MHEWS-FR-0349, MHEWS-FC-STM-08, FC-INV-10, MHEWS-SD-CONTACT-01, SD-CONTACT-03, SD-CONTACT-04 |
| TST-M7-006 | Distribution List Management | MHEWS-FR-0104, FR-0147, FR-0201, FR-0243, FR-0287, MHEWS-SD-CONTACT-02, SD-GDB-02 |
| TST-M7-007 | Dispatch Retry | MHEWS-FR-0066, FR-0119, FR-0280, MHEWS-FC-ERR-08 |
| TST-M7-008 | WhatsApp Channel Config | MHEWS-SD-WA-03 |
| TST-M7-009 | Community Hazard Reporting | MHEWS-FR-0004, FR-0079, FR-0109, FR-0112, FR-0114, FR-0115, FR-0116, FR-0206, FR-0216, MHEWS-FC-STM-07, MHEWS-SD-FEEDBACK-01, SD-FEEDBACK-02, SD-FEEDBACK-03, SD-FEEDBACK-04 |
| TST-M7-010 | Public Self-Registration | MHEWS-SD-CONTACT-06, SD-SELFREG-01 |

### 3.8 M8 — Incident Record & Lifecycle Tracking

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M8-001 | Incident CRUD | MHEWS-FR-0126, FR-0149, FR-0184, FR-0209, FR-0210, FR-0245, FR-0271, FR-0277, FR-0316, FR-0344, MHEWS-FC-STM-04 |
| TST-M8-002 | After-Action Reporting | MHEWS-FR-0033, FR-0113, FR-0125, FR-0138, FR-0207, FR-0213, FR-0214, FR-0242, FR-0257, FR-0265, FR-0274, FR-0275, FR-0278 |
| TST-M8-003 | Incident Search & History | MHEWS-FR-0217, FR-0263, FR-0270, FR-0356 |
| TST-M8-004 | CAP-Incident Linkage | (no req currently mapped — see Coverage Summary) |
| TST-M8-005 | Community Report Linkage | MHEWS-SD-FEEDBACK-05 |

### 3.9 M9 — Audit & Compliance Framework

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M9-001 | Audit Log Viewer | MHEWS-FR-0009, FR-0013, FR-0017, FR-0046, FR-0047, FR-0049, FR-0054, FR-0075, FR-0093, FR-0095, FR-0111, FR-0117, FR-0130, FR-0148, FR-0151, FR-0155, FR-0253, FR-0259, FR-0328, FR-0361, MHEWS-SD-LLM-04, SD-DRILL-02, MHEWS-NFR-0013 |
| TST-M9-002 | Evidence Package Generation | MHEWS-FR-0014, FR-0045, FR-0055, FR-0258, MHEWS-SD-STORE-04 |
| TST-M9-003 | Audit Immutability Enforcement | MHEWS-FR-0011, FR-0048, FR-0318, MHEWS-FC-ERR-09, FC-OUV-06 |
| TST-M9-004 | Audit Reporting | MHEWS-FR-0010, FR-0024, FR-0025, FR-0050, FR-0065, FR-0070, FR-0071, FR-0081, FR-0086, FR-0088, FR-0096, FR-0268, FR-0269, FR-0272, FR-0281, FR-0282, FR-0283, FR-0291, FR-0313, FR-0320, FR-0325, FR-0338, FR-0351, FR-0364, MHEWS-NFR-0007, NFR-0023, NFR-PERF-AUD-01 |
| TST-M9-005 | Compliance Health Monitoring | MHEWS-FR-0067, FR-0105, FR-0314, FR-0339 |

### 3.10 M10 — Administration & Access Control

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M10-001 | Authentication (JWT) | MHEWS-NFR-0002 |
| TST-M10-002 | User Management | MHEWS-NFR-0001, MHEWS-FR-0135, FR-0136, FR-0145, FR-0279, FR-0330, FR-0352, FR-0353, FR-0360, FR-0362, FR-0363, MHEWS-FC-INV-08 |
| TST-M10-003 | RBAC Enforcement | MHEWS-FR-0137, FR-0152, FR-0244, FR-0329 |
| TST-M10-004 | User Activity View | MHEWS-FR-0080 |
| TST-M10-005 | System Settings | MHEWS-FR-0005, FR-0076, FR-0077, FR-0102, FR-0120, MHEWS-SD-LLM-06 |

### 3.11 M11 — Integration & API Gateway

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M11-001 | External Connector Framework | MHEWS-FR-0042, FR-0134, FR-0227 |
| TST-M11-002 | Provider Webhook Receiver | (verified through RISK-023 mitigations — no direct FR mapped) |
| TST-M11-003 | API Rate Limiting | MHEWS-NFR-0005, NFR-0006 |
| TST-M11-004 | API Documentation (OpenAPI) | (verified through NFR-MAINT-05 — no direct FR mapped) |

### 3.12 M12 — Preparedness, Drill & Response

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-M12-001 | Drill Mode Management | MHEWS-SD-DRILL-01, SD-DRILL-02, MHEWS-FC-STM-11 |
| TST-M12-002 | SOP Management | (post-PoC; no reqs mapped yet) |

### 3.13 AI Chatbot

| TST ID | Feature | Req IDs Verified |
|---|---|---|
| TST-AI-001 | Active Alert Q&A | MHEWS-NFR-0003 |
| TST-AI-002 | Hazard Definition Q&A | MHEWS-FR-0215, FR-0249 |

---

## 4. Risk Traceability (REQ → RISK)

Mapping from risk register (30 RISK-* entries) to related requirement IDs. Source: `consolidation/19_risk_register.md`.

### 4.1 Technical Risks

| RISK ID | Title | Score | Related Req IDs |
|---|---|---|---|
| RISK-001 | API response time under peak load | High | NFR-SCAL-01, NFR-PERF-BATCH-01 |
| RISK-002 | Claude API unavailability/cost spike | High | MHEWS-SD-LLM-01, NFR-PERF-LLM-01 |
| RISK-003 | Single→multi-tenant migration | High | CAP-01 (capacity constraint) |
| RISK-004 | PostGIS spatial query degradation | Medium | NFR-PERF-IMP-01, NFR-PERF-IMP-02 |
| RISK-005 | Celery worker resource exhaustion | Very High | NFR-PERF-BATCH-01, CAP-07 (capacity constraint) |
| RISK-006 | WhatsApp Business API approval delays | Very High | MHEWS-SD-WA-01, SD-WA-02, SD-WA-03, SD-WA-04, SD-WA-05, NFR-SCAL-03 |
| RISK-007 | WhatsApp template rejection | Medium | MHEWS-SD-WA-03 |
| RISK-008 | CAP validation profile incompatibility | Low | TST-M6-004, MHEWS-SD-VALID-01, SD-VALID-02, SD-VALID-03, SD-VALID-04, SD-VALID-05 |
| RISK-009 | Map tile serving unreliability | Medium | MHEWS-SD-TILE-01, SD-MAP-01, SD-MAP-02, SD-MAP-03, SD-MAP-04, SD-MAP-05 |
| RISK-010 | Data sovereignty non-compliance | Very High | NFR-SOV-01, NFR-SOV-02, NFR-SOV-03, NFR-SOV-04 |
| RISK-011 | DB connection pool exhaustion | High | NFR-SCAL-04 |
| RISK-012 | Redis single-instance failure | Very High | NFR-AVAIL-03, NFR-REL-04 |

### 4.2 Operational Risks

| RISK ID | Title | Score | Related Req IDs |
|---|---|---|---|
| RISK-013 | NMHS staff training/adoption failure | Very High | NFR-UX-01, TST-M12-001 |
| RISK-014 | Internet connectivity loss (Profile A) | Very High | NFR-AVAIL-03, NFR-REL-01 |
| RISK-015 | Backup/recovery procedure failure | Very High | NFR-REL-04, NFR-AVAIL-04 |
| RISK-016 | Alert fatigue from false positives | Very High | HITL-VER-03, TST-M8-002 |
| RISK-017 | i18n quality degradation | High | MHEWS-SD-I18N-01, SD-I18N-02 |
| RISK-018 | Dual-authorization bypass | Very High | HITL-VER-01, NFR-SEC-06, NFR-SEC-08 |

### 4.3 Integration Risks

| RISK ID | Title | Score | Related Req IDs |
|---|---|---|---|
| RISK-019 | External data feed unavailability | Very High | TST-M3-004, NFR-OBS-03, MHEWS-FC-ERR-06 |
| RISK-020 | WhatsApp rate limiting/delivery failures | Very High | TST-M7-007, TST-M7-001, MHEWS-SD-WA-04 |
| RISK-021 | Email deliverability degradation | Medium | MHEWS-SD-EMAIL-01, SD-EMAIL-02, SD-EMAIL-03, SD-EMAIL-04 |
| RISK-022 | MapLibre browser compatibility | Medium | MHEWS-SD-MAP-05, NFR-UX-01 |
| RISK-023 | Webhook receiver abuse | High | TST-M11-002, TST-M11-003, NFR-SEC-04 |

### 4.4 Security Risks

| RISK ID | Title | Score | Related Req IDs |
|---|---|---|---|
| RISK-024 | JWT token compromise | Very High | NFR-SEC-06, MHEWS-NFR-0002, MHEWS-NFR-0057 |
| RISK-025 | API key leakage | High | NFR-LOG-03, NFR-SEC-03 |
| RISK-026 | ABAC privilege escalation | Medium | NFR-SEC-06, HITL-VER-01, MHEWS-NFR-0001 |
| RISK-027 | Audit log tampering | Medium | NFR-AUDIT-01, NFR-AUDIT-07, NFR-AUDIT-06 |
| RISK-028 | PII exposure in LLM prompts | High | NFR-LOG-03, NFR-SOV-01, MHEWS-SD-LLM-06 |
| RISK-029 | Session fixation / CSRF attacks | Medium | MHEWS-NFR-0066, NFR-SEC-02 |
| RISK-030 | Dependency vulnerability in images | Very High | NFR-MAINT-01, MHEWS-NFR-0065, NFR-SEC-07 |

### 4.5 Risk-to-Requirement Density

| Req ID | Risks Linked | Score Range |
|---|---|---|
| NFR-SEC-06 | RISK-018, RISK-024, RISK-026 | Very High–Medium |
| NFR-PERF-BATCH-01 | RISK-001, RISK-005 | High–Very High |
| NFR-LOG-03 | RISK-025, RISK-028 | High |
| NFR-SCAL-01 | RISK-001 | High |
| NFR-SOV-01 | RISK-010, RISK-028 | Very High–High |
| MHEWS-SD-WA-03 | RISK-006, RISK-007 | Very High–Medium |
| HITL-VER-01 | RISK-018, RISK-026 | Very High–Medium |
| NFR-UX-01 | RISK-013, RISK-022 | Very High–Medium |
| NFR-REL-04 | RISK-012, RISK-015 | Very High |
| NFR-AVAIL-03 | RISK-012, RISK-014 | Very High |

---

## 5. Coverage Summary

### 5.1 Forward Traceability Statistics

| Module | Reqs Traced | TST IDs Used | Coverage |
|---|---|---|---|
| M1 — Hazard Taxonomy | 21 | TST-M1-001, -002, -003 | 100% (3/3 features) |
| M2 — Risk & Scenario | 11 | TST-M2-001, -002, -003, -004 | 100% (4/4 features) |
| M3 — Data Ingestion | 65 | TST-M3-001, -002, -003, -004 | 100% (4/4 features) |
| M4 — Forecasting | 51 | TST-M4-001, -002, -003, -004, -005 | 100% (5/5 features) |
| M5 — Impact Analysis | 57 | TST-M5-001, -002, -003, -004, -005 | 100% (5/5 features) |
| M6 — CAP Authoring | 69 | TST-M6-001, -002, -003, -004, -005, -006, -007 | 100% (7/7 features) |
| M7 — Dissemination | 71 | TST-M7-001 through -010 | 100% (10/10 features) |
| M8 — Incident Tracking | 28 | TST-M8-001, -002, -003, -004, -005 | 100% (5/5 features) |
| M9 — Audit | 63 | TST-M9-001, -002, -003, -004, -005 | 100% (5/5 features) |
| M10 — Admin/RBAC | 23 | TST-M10-001, -002, -003, -004, -005 | 100% (5/5 features) |
| M11 — API Gateway | 5 | TST-M11-001, -003 | 50% (2/4 features; -002, -004 have no direct FR) |
| M12 — Drill & Response | 2 | TST-M12-001 | 50% (1/2 features; -002 post-PoC) |
| AI Chatbot | 1 | TST-AI-001, -002 | 100% (2/2 features) |
| Cross-cutting FRs | 30 | Various | n/a (middleware/config reqs) |
| Cross-cutting NFRs | 77 | n/a (environment-level) | n/a |
| **TOTAL** | **574 FR + 5 NFR in-module** | **61 TST IDs** | **95% feature coverage** |

### 5.2 Test Level Distribution

From V&V Plan §3.2:

| Level | Features Covered | Percentage |
|---|---|---|
| Unit (U) | 46 / 61 | 75% |
| Integration (I) | 61 / 61 | 100% |
| System (S) | 24 / 61 | 39% |
| Acceptance (A) | 58 / 61 | 95% |

### 5.3 NFR Verification Method Distribution

| Method | NFR Count |
|---|---|
| Load Test | 33 |
| Security Scan | 14 |
| DR Test | 12 |
| Integration Test | 17 |
| CI Gate | 12 |
| Config Audit | 7 |
| Deployment Test | 4 |
| Accessibility Audit | 4 |
| Automated Test | 5 |

### 5.4 Gap Analysis

#### 5.4.1 Features Without Direct Requirement Mapping

| TST ID | Feature | Gap Reason | Resolution |
|---|---|---|---|
| TST-M11-002 | Provider Webhook Receiver | No FR mapped; covered by RISK-023 mitigations | Accept — design-derived security feature |
| TST-M11-004 | API Documentation (OpenAPI) | No FR mapped; covered by NFR-MAINT-05 | Accept — documentation is an NFR |
| TST-M12-002 | SOP Management | Post-PoC feature; no reqs mapped | Track — add reqs when SOP feature is scoped |
| TST-M8-004 | CAP-Incident Linkage | Feature defined but no FR currently mapped | Action — derive req from US-M8-05 in next backlog grooming |

#### 5.4.2 Requirements Without Test Coverage

| Category | Count | Examples | Rationale |
|---|---|---|---|
| Cross-cutting middleware reqs | ~12 | MHEWS-FC-ERR-01 to ERR-05, FC-INV-01 to INV-03, FC-OUV-02, FC-OUV-05 | Verified via integration tests (no dedicated TST-* ID); covered by middleware test suite |
| Deployment/config reqs | ~4 | MHEWS-SD-MAP-02, SD-GDB-01, SD-TILE-01, SD-MAP-05 | Verified via deployment checklist, not automated tests |
| Scope-exclusion markers | 1 | MHEWS-SD-SCOPE-01 | Documentation only; no test needed |

#### 5.4.3 Risk Coverage Gaps

| Risk | Score | Gap | Recommended Action |
|---|---|---|---|
| RISK-010 | Very High | NFR-SOV-01 through SOV-04 not in NFR table above (they are in 15_fr_nfr_classification.md but not yet in normalized reqs) | Add SOV NFRs to normalized requirements in next iteration |
| RISK-012 | Very High | NFR-AVAIL-03, NFR-REL-04 reference HA features not testable in single-node baseline | Accept — HA testing deferred to Upgrade Path B1 |
| RISK-018 | Very High | HITL-VER-01, HITL-VER-03 are process controls, not requirements with TST-* IDs | Accept — verified via acceptance testing of TST-M6-005 |

### 5.5 Traceability Completeness Summary

| Dimension | Covered | Total | Percentage |
|---|---|---|---|
| REQ → Design Element | 574 | 579 | 99.1% |
| REQ → TST ID (FRs) | 462 | 474 | 97.5% |
| REQ → Verification Method (NFRs) | 105 | 105 | 100% |
| TST → REQ (backward) | 57 | 61 | 93.4% (4 features with gaps noted) |
| RISK → REQ | 30 | 30 | 100% |
| REQ with RISK linkage | 48 unique reqs | 579 | 8.3% (expected — only risk-adjacent reqs) |

---

## Cross-References

| Document | Relevance |
|---|---|
| `consolidation/11_normalized_requirements.md` | Full requirement text for all 579 IDs |
| `consolidation/13_module_feature_trace.md` | Feature → TST-* ID mapping (61 features) |
| `consolidation/16_req_traceability_map.md` | REQ → Module/Feature mapping (579 rows) |
| `consolidation/18_software_design_description.md` | Design elements referenced in DES column |
| `consolidation/19_risk_register.md` | 30 risks with "Related Reqs" column |
| `consolidation/20_vv_plan.md` | TST-* test level assignments (U/I/S/A) |
| `consolidation/21_structured_srs.md` | Structured SRS (IEEE 29148) |
