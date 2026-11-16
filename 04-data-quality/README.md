# Data Quality

[← Data Governance overview](../README.md)

Data quality ensures data is correct and **fit for purpose**. While data security ensures data is not tampered with, data quality ensures it is accurate, complete, consistent and delivered on time for the uses it serves. It is the **value layer** of data governance.

In the modern data architecture, data quality frameworks replace the schema and data enforcement that traditional relational databases provided for free.

## Contents

| # | Topic | What it covers |
| --- | --- | --- |
| 1 | [Context: from database constraints to DQ frameworks](01-constraints-to-dq-frameworks.md) | Why DQ matters more after moving to cloud lakes and warehouses; Deequ example; constraint mapping across PostgreSQL, Oracle, Redshift and Glue |
| 2 | [Data quality dimensions](02-dimensions.md) | Accuracy, completeness, consistency, uniqueness, integrity, validity, timeliness, distribution |
| 3 | [Where to perform DQ checks](03-where-to-check.md) | Shift left; source vs lake/warehouse; DQ monitoring vs unit testing vs data cleaning |
| 4 | [DQ check categories and approaches](04-check-categories.md) | Schema validation, business rules, profiling, anomaly detection, distribution |
| 5 | [Data profiling](05-data-profiling.md) | Metrics, platform-agnostic profiling, generic schema for profiling results |
| 6 | [DQ score and certification](06-dq-score-and-certification.md) | Measuring DQ; data quality vs data usability (medallion) certification |
| 7 | [Who owns data quality](07-ownership.md) | Engineer-led DQ; when a DQ platform or data stewards are justified |
| 8 | [DQ tools and evaluation](08-tools-and-evaluation.md) | Deequ, Glue DQ, Airflow, dbt, Great Expectations, OpenMetadata; evaluation matrix |

## Dimensions at a glance

| Dimension | Question |
| --- | --- |
| Accuracy | Does the data reflect real-world values correctly? |
| Completeness | Is all expected data present? |
| Consistency | Are there contradictions within or across datasets? |
| Uniqueness | Are there duplicate records where there shouldn't be? |
| Integrity | Are relationships between entities maintained? (Different from integrity in the security sense.) |
| Validity | Do values conform to formats, domains and rules? |
| Timeliness | Is data delivered on time and according to SLA? |
| Distribution | Has the data's distribution drifted (mainly for ML use cases)? |

## Key principles

1. Cloud lakes and warehouses dropped the constraint enforcement relational databases gave us; DQ frameworks put it back as code.
2. Define quality by dimension: accuracy, completeness, consistency, uniqueness, integrity, validity, timeliness and distribution.
3. Shift left: validate at the source, again during ingestion, and monitor after load.
4. Unit tests and cleaning rules cover known assumptions; continuous DQ monitoring catches what changes.
5. Profile datasets continuously; profiles are the baseline for rule design, anomaly detection and drift monitoring.
6. Measure quality with a DQ score (rules passed / rules evaluated) and keep it separate from usability (bronze/silver/gold).
7. Engineers own DQ by default; add a DQ platform and data stewards only when their benefit clearly exceeds their cost.
