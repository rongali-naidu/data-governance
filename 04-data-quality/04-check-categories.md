# 4. DQ check categories and approaches

[← Data Quality](README.md)

DQ checks fall into five categories, from deterministic rules (schema and business rules) to statistical and ML-based detection (profiling, anomalies, drift).

| # | Category | Dimensions targeted | What it checks |
| --- | --- | --- | --- |
| 1 | Schema and data validation | Accuracy, completeness, validity, uniqueness, integrity | See below |
| 2 | Business rule validation | Consistency, accuracy, validity | Domain rules that catch cross-field inconsistencies |
| 3 | Data profiling | Accuracy, validity | Column- and dataset-level statistics; baseline for deviations ([details](05-data-profiling.md)) |
| 4 | Anomaly detection | Accuracy, trend stability | Deviations found with statistical models or ML |
| 5 | Data distribution | Distribution | Data drift |

## 1. Schema and data validation

Checks for schema compliance and data formatting:

- **Data type conformance** (validity): types match (int, string, timestamp).
- **Null checks** (completeness): required fields must not be null.
- **Uniqueness / PK checks** (uniqueness): prevent duplicate key entries.
- **Referential integrity** (integrity): FK relationships hold (e.g. `customer_id` exists).
- **Format validation** (validity): specific patterns, such as
  - dates (`YYYY-MM-DD`, ISO 8601),
  - currency (`$99.99`, `€1.000,00`),
  - time zones (`+05:30`, `UTC`).
- **Row count:** the entire dataset intended for processing is present.
- **Source coverage:** every expected source contributes. When integrating data from many systems, records from one system are easily missed.
- **Schema evolution:** detect new columns.

## 2. Business rule validation

Business and domain rules that catch cross-field inconsistencies:

- `order_amount > 0`
- `status IN ('SHIPPED', 'CANCELLED')`
- `start_date < end_date`
- Revenue drop does not exceed 20%
- Metric values consistent across granular datasets (daily, weekly, monthly)

Implemented in SQL, Python, dbt tests or Great Expectations.

## 3. Data profiling

Examines column- and dataset-level statistics:

- min, max, average, standard deviation
- null counts, distinct values, cardinality
- new or unseen value combinations

Profiling is often the first step in understanding unknown data. Profiles summarize data at table and column level and can be used to detect deviations (data anomalies or DQ issues, depending on context). See [Data profiling](05-data-profiling.md).

## 4. Anomaly detection

Detects deviations using statistical models or ML:

- Volume spikes or drops

Complements rule-based checks with adaptive insight.

## 5. Data distribution

- Detecting data drift. Relevant mainly for ML model monitoring; not strictly anomaly detection.

## Where each approach is supported

- **RDBMS-based data warehouses** support some approaches natively, mainly schema and constraint-based validation (category 1).
- **ETL and orchestration tools** support some approaches, typically rule checks run as pipeline steps (categories 1–2).
- **DQ-specific frameworks** support rule-based checks, profiling and, in some cases, anomaly detection (categories 1–4).
- **Statistical / ML-model-based frameworks** support anomaly detection and drift monitoring (categories 4–5).
