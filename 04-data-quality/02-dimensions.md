# 2. Data quality dimensions

[← Data Quality](README.md)

"Data quality" is used loosely, and its meaning varies depending on whom you ask. It is usually described along the dimensions below. Each one is enforced differently at the source system than in the data lake or warehouse.

## Accuracy

Data reflects the real-world value or event it represents: what we loaded is correct in all aspects.

- **At source systems:** application logic (input validation, drop-downs instead of free text) captures correct values. Examples: correct customer address, accurate order quantity, valid timestamps.
- **At data lake / warehouse:** accuracy means matching what was received from the source, with no corruption or transformation error during ingestion or processing. It is usually interpreted as "did we receive what the source emitted?"
  - Usually verified during unit testing. For ongoing DQ monitoring, source and lake/warehouse data must be queried together, for example through DB links or data sharing.

## Completeness

All expected data is present: rows, fields and values.

- **At source systems:**
  - Required fields enforced (e.g. no NULLs in `customer_id`).
  - All records expected in a transaction or API call are submitted.
- **At data lake / warehouse:**
  - Checks for missing files, truncated rows or empty partitions.
  - Column-level null checks in staging and curated zones.
  - Record counts match between source and raw zone (staging layer).
  - Data is available for every column the downstream consumers (analytics, data science) require.

## Consistency

No contradictions within or across datasets and systems.

- **At source systems:**
  - Referential integrity between entities (e.g. orders link to valid customers). This overlaps with the integrity dimension.
  - Consistent rules (e.g. standardized status enums).
- **At data lake / warehouse:**
  - Cross-source comparisons (e.g. the same product name wherever it appears).
  - Metrics consistent across reports (e.g. revenue totals agree between base and aggregated datasets).
  - Time zone, currency and data type standardization during ingestion.

## Uniqueness

No duplicate records or keys where uniqueness is required. This is essential for master data management; master data is usually a dimension in the lake/warehouse.

- **At source systems:** unique constraints and key validation.
- **At data lake / warehouse:** deduplication, record matching, identity resolution.

## Integrity

Correct relationships across data entities (foreign keys, dimensional relationships).

- **At source systems:** referential integrity enforcement.
- **At data lake / warehouse:** validations of integrity between fact and dimension tables.

> Integrity in the DQ sense is different from integrity in the security sense (CIA triad), which means data is not tampered with.

## Validity

Data values follow every rule that defines valid data: correct syntax (format), allowed values, logical consistency and business constraints. Validity overlaps with accuracy and consistency, since invalid data cannot be accurate or consistent.

Format validation focuses on syntax and structure:

- Email addresses follow a valid pattern.
- Dates are in `YYYY-MM-DD` format.
- Numeric fields contain only digits.

Other validity checks:

- Values fall within allowed ranges or categories (e.g. status codes).
- Dates are logical (e.g. no February 30).
- Referential integrity between related fields.

- **At source systems:** front-end validations (e.g. email regex), API schema contracts, database constraints.
- **At data lake / warehouse:** validate format upstream where possible. Validating emails or phone numbers downstream often signals poor data hygiene at the source.

## Timeliness

Data is timely and delivered according to the agreed service level agreement (SLA). This dimension is sometimes called *availability*; *timeliness* avoids confusion with availability in the security CIA triad, which means systems and data are accessible. Some use cases need real-time delivery; others allow daily batch updates. Job failures and pipeline latency are operational issues, but because they affect data freshness they count as DQ issues at a broader level.

- **At source systems:** data is published per SLA contracts.
- **At data lake / warehouse:** data is ingested, processed and made available in the curated datasets used by downstream consumers per SLA.

## Distribution (data drift)

Mainly relevant to data science use cases. Changes in the distribution of data, sometimes called "data drift", can lead to unexpected outputs from machine learning models.

## Summary

| Dimension | What it means | At source systems | At data lake / warehouse |
| --- | --- | --- | --- |
| Accuracy | Reflects the real-world value | Input validation, drop-downs | Matches what the source emitted; source-to-target reconciliation |
| Completeness | All rows, fields and values present | Required fields, complete submissions | Missing files/partitions, null checks, record-count matching |
| Consistency | No contradictions | Referential integrity, standard enums | Cross-source comparisons, metric agreement, standardization |
| Uniqueness | No duplicates | Unique constraints | Deduplication, identity resolution |
| Integrity | Relationships maintained | Referential integrity | Fact-to-dimension validations |
| Validity | Format, allowed values, logic, business rules | Front-end validation, API contracts, constraints | Prefer upstream validation |
| Timeliness | Delivered per SLA | Published per SLA | Curated datasets available per SLA |
| Distribution | No unexpected drift | Usually not checked | Statistical monitoring over time |
