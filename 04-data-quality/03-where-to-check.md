# 3. Where to perform DQ checks

[← Data Quality](README.md)

Check quality as close as possible to where data is generated, and keep redundant checks at every handoff downstream.

## Shift left

Shift-left architecture borrows from DevOps and testing. It moves data quality, validation and governance activities toward the point where data is generated, instead of waiting until data lands in the lake or warehouse.

1. **At the data source:** validate and enforce DQ rules where data is first generated or captured.
2. **During ingestion:** apply DQ checks inside ingestion pipelines to detect and reject bad data early.
3. **Post-ingestion:** run monitoring and reconciliation checks after loading to confirm consistency and compliance.

## Source systems vs data lake / warehouse

- **Source systems are the first line of defense.** They sit closest to the business process and user input, so errors caught here are cheapest to fix.
- **The data lake / warehouse is the final line of defense.** It catches issues missed upstream and monitors quality at scale across systems.
- **Best practice:** do not rely only on downstream checks. Build layered, redundant validation at every handoff, especially between systems.

## DQ monitoring vs unit testing vs data cleaning

Unit tests and cleaning rules encode known assumptions; DQ monitoring is the dynamic safety net for issues nobody planned or tested for.

Unit tests and cleaning rules are usually built on the initial data analysis done before designing the ETL pipeline or schema. When data patterns change, and they will, those assumptions break. Monitoring catches what the assumptions miss.

| Aspect | Unit testing of datasets/pipelines | DQ monitoring | Data cleaning & transformation |
| --- | --- | --- | --- |
| What | Schema checks, column types, business rules, edge cases, join behavior, partitioning logic | Monitors real-time or batch data for accuracy, completeness, uniqueness, freshness and more | Applies rules to correct, standardize or transform data (deduplication, type casting, formatting) |
| When | During development or before deployment; also on code changes | Continuously in production | During ingestion, transformation or enrichment |
| Why | Catch logic and schema issues early; validate transformation logic | Detect unexpected issues in live data (pipeline failures, data drift, upstream changes) | Make raw data usable, consistent and ready for analytics or ML |
| Where | Manually or in CI/CD workflows | Production, usually integrated with alerting and monitoring tools | ETL/ELT jobs, data wrangling scripts, enrichment pipelines, staging zones |
| Examples | Validate derived column logic; row count validation between source and target | Detect nulls in historically populated columns; warn on duplicate IDs; alert on schema drift | Standardize date formats; remove special characters; impute missing values; normalize values |
| Adaptability to data changes | Valid only while assumptions hold; needs code updates when data changes | Detects previously unseen or evolving issues (new null patterns, format changes, drift) | Fixed, predefined rules (`NVL`, `fillna`, type casting); can become outdated as data evolves |
