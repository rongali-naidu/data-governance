# 8. DQ tools and evaluation

[← Data Quality](README.md)

Seven tools are on the shortlist, ranging from code libraries to orchestration operators to catalog-integrated platforms. The evaluation currently focuses on the data lake.

## Tool landscape

| Tool | Type | How rules are defined | Notes |
| --- | --- | --- | --- |
| [Deequ / PyDeequ](https://aws.amazon.com/blogs/big-data/test-data-quality-at-scale-with-deequ/) | Open-source library on Apache Spark (Scala; PyDeequ is the Python wrapper) | Checks as code (`Check`, `VerificationSuite`) | Constraint verification, metrics, profiling, constraint suggestion and anomaly detection on stored metrics |
| Glue ETL Data Quality | Managed AWS feature inside Glue ETL jobs; built on Deequ | DQDL (Data Quality Definition Language) rulesets | Evaluates data in flight in the pipeline; can act on results within the job |
| Glue Catalog Data Quality | Managed AWS feature on Glue Data Catalog tables; built on Deequ | DQDL rulesets, with rule recommendations | Evaluates data at rest; reports a score as the share of rules passed, which matches the [DQ score](06-dq-score-and-certification.md) |
| Airflow DAG operators | Orchestration-level checks (SQL check operators) | SQL conditions in DAG tasks | Fails or branches the DAG on check results; no profiling or scoring of its own |
| dbt | Transformation framework with built-in data tests | YAML generic tests (`unique`, `not_null`, `accepted_values`, `relationships`) and SQL tests | Runs inside the warehouse/engine as part of the transformation workflow |
| Great Expectations | Open-source Python DQ framework | Expectation suites, run through checkpoints | Generates human-readable validation reports (Data Docs) |
| OpenMetadata | Open-source metadata and governance platform | Tests configured in UI or as code | Profiler, DQ tests and results attached to catalog entities; closest to a DQ portal |

This summary is from general knowledge, not a current feature check; confirm each capability during the evaluation.

## Evaluation matrix

Scope: data lake first. Open question: does this capture all the requirements?

Suggested scoring per cell: **Yes / Partial / No**, with a short note.

| Criterion | Deequ / PyDeequ | Glue ETL DQ | Glue Catalog DQ | Airflow operators | dbt | Great Expectations | OpenMetadata |
| --- | --- | --- | --- | --- | --- | --- | --- |
| DQ portal: configure rules — schema enforcement | | | | | | | |
| DQ portal: configure rules — business rules | | | | | | | |
| DQ portal: data-profiling-based checks | | | | | | | |
| DQ portal: anomaly-based checks | | | | | | | |
| DQ portal: track validation results | | | | | | | |
| DQ portal: derive a DQ score per dataset | | | | | | | |
| DQ portal: alarm configuration | | | | | | | |
| DQ portal: data source — AWS data lake (Glue tables, incl. S3) | | | | | | | |
| DQ portal: compute engine — Redshift | | | | | | | |
| DQ portal: compute engine — Athena | | | | | | | |
| DQ portal: compute engine — EMR | | | | | | | |
| DQ portal: compute engine — Cradle (internal EMR-based engine from BDT) | | | | | | | |
| DQ portal: self-service onboarding — new team | | | | | | | |
| DQ portal: self-service onboarding — new data source | | | | | | | |
| DQ portal: self-service onboarding — new compute engine | | | | | | | |
| Programming-based: schema compliance checks | | | | | | | |
| Programming-based: rule-based checks | | | | | | | |
| Check coverage: data drift / distribution monitoring | | | | | | | |
| Check coverage: source-to-target reconciliation (accuracy across source and lake) | | | | | | | |
| Check coverage: referential integrity across datasets (fact to dimension) | | | | | | | |
| Check coverage: freshness / SLA monitoring | | | | | | | |
| Check coverage: schema evolution detection (new or dropped columns) | | | | | | | |
| Check coverage: rule recommendations generated from profiling | | | | | | | |
| Pipeline: fail the job or quarantine bad records in-flight | | | | | | | |
| Pipeline: incremental checks on new partitions only | | | | | | | |
| Pipeline: orchestration integration (Airflow, Step Functions) | | | | | | | |
| Pipeline: rules as code in version control and CI/CD | | | | | | | |
| Data formats: Parquet, Iceberg, Hudi, Delta tables | | | | | | | |
| Results: queryable history of metrics and results | | | | | | | |
| Results: dashboards for non-technical stakeholders | | | | | | | |
| Results: link to catalog, data contracts and certification | | | | | | | |
| Alerts: notification channels (SNS, email, Slack, ticketing) | | | | | | | |
| Operations: issue ownership and incident workflow | | | | | | | |
| Operations: managed service vs self-hosted maintenance effort | | | | | | | |
| Scale: performance on billion-row datasets | | | | | | | |
| Cost: cost per run (compute plus licensing) | | | | | | | |
| Security: IAM / Lake Formation permissions and cross-account access | | | | | | | |
| Maturity: community or vendor support, release activity | | | | | | | |
