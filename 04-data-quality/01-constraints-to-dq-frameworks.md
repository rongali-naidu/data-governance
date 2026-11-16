# 1. Context: from database constraints to DQ frameworks

[← Data Quality](README.md)

Data quality frameworks are the modern data architecture's replacement for the schema and data enforcement that traditional relational databases did for free.

## Context

After moving from a traditional Oracle-based data warehouse to cloud data lakes and warehouses, I noticed a growing demand for data quality (DQ) frameworks. That raised a simple question: how did we manage data quality before?

To answer it, I took a DQ implementation example and translated each check into how we used to handle it. Most DQ conditions turned out to be table- and column-level constraints in a relational database:

- `NOT NULL`, `PRIMARY KEY`, `UNIQUE` and `CHECK` constraints were embedded in the schema. They silently and reliably enforced integrity at the storage layer.
- The database also enforced the schema itself while data was written into the table (schema-on-write).
- At the end of ETL batch loads, we ran validation scripts to check data correctness, and referential integrity where it was not defined at the table level.

In today's cloud data warehouses and lakehouses, these schema and data validations are either not supported or not enforced. DQ frameworks fill that gap. Tools such as [Deequ](https://aws.amazon.com/blogs/big-data/test-data-quality-at-scale-with-deequ/) let you codify and validate expectations about data independently of the storage layer.

## Example: DQ checks with Deequ

Adapted from the AWS blog [Test data quality at scale with Deequ](https://aws.amazon.com/blogs/big-data/test-data-quality-at-scale-with-deequ/):

```scala
VerificationSuite()
  .onData(dataset)
  // define a data quality check
  .addCheck(
    Check(CheckLevel.Error, "Review Check")
      .hasSize(_ >= 3000000)             // at least 3 million rows
      .hasMin("star_rating", _ == 1.0)   // min is 1.0
      .hasMax("star_rating", _ == 5.0)   // max is 5.0
      .isComplete("review_id")           // should never be NULL
      .isUnique("review_id")             // should not contain duplicates
      .isComplete("marketplace")         // should never be NULL
      // contains only the listed values
      .isContainedIn("marketplace", Array("US", "UK", "DE", "JP", "FR"))
      .isNonNegative("year"))            // should not contain negative values
  // compute metrics and verify check conditions
  .run()
```

Each check expresses a DQ rule that was traditionally enforced by database schema constraints.

## Mapping each check to a database constraint

| Deequ condition | Constraint type | PostgreSQL | Oracle | Redshift | Glue Catalog |
| --- | --- | --- | --- | --- | --- |
| `.hasMin(...)` and `.hasMax(...)` on `star_rating` | CHECK | `CHECK (star_rating BETWEEN 1 AND 5)` ✅ enforced | `CHECK (star_rating BETWEEN 1 AND 5)` ✅ enforced | ❌ Not supported | ❌ Not supported (Glue does not enforce CHECK) |
| `.isComplete("review_id")` | NOT NULL | `review_id NOT NULL` ✅ enforced | `review_id NOT NULL` ✅ enforced | ✅ Enforced | ⚠️ Defined, not enforced (metadata only) |
| `.isUnique("review_id")` | UNIQUE or PRIMARY KEY | `UNIQUE(review_id)` or `PRIMARY KEY(review_id)` ✅ enforced | Same as PostgreSQL ✅ enforced | ❌ Defined, not enforced | ❌ Not supported (no UNIQUE or PK enforcement) |
| `.isComplete("marketplace")` | NOT NULL | `marketplace NOT NULL` ✅ enforced | `marketplace NOT NULL` ✅ enforced | ✅ Enforced | ⚠️ Defined, not enforced (metadata only) |
| `.isContainedIn("marketplace", [...])` | CHECK | `CHECK (marketplace IN ('US','UK','DE','JP','FR'))` ✅ enforced | Same as PostgreSQL ✅ enforced | ❌ Not supported | ❌ Not supported |
| `.isNonNegative("year")` | CHECK | `CHECK (year >= 0)` ✅ enforced | `CHECK (year >= 0)` ✅ enforced | ❌ Not supported | ❌ Not supported |
| `.hasSize(_ >= 3000000)` | No constraint equivalent | Post-load validation script | Post-load validation script | Post-load validation script | Not applicable |

Two notes on the mapping:

- `hasSize` was never a schema constraint. It corresponds to the row-count checks we ran in post-load validation scripts.
- `hasMin(_ == 1.0)` and `hasMax(_ == 5.0)` assert that the minimum is exactly 1 and the maximum exactly 5. A `CHECK ... BETWEEN 1 AND 5` only enforces the range, so the Deequ version is slightly stricter.

## Why constraint enforcement disappeared

- **Schema-on-read.** Many platforms, especially data lakes, validate nothing when data is written.
- **Performance at scale.** Enforcing constraints in massively parallel or distributed engines slows writes. Flexibility and throughput are prioritized over rigid enforcement of data contracts.

As a result, developers now own data validation logic in their pipelines instead of relying on the storage engine to guarantee integrity.

## The role of DQ frameworks

Deequ, or any custom-built DQ framework, lets you:

- Express data expectations as code.
- Run validations as part of ETL/ELT workflows.
- Simulate schema constraints on platforms that do not enforce them.
- Detect anomalies and issues before bad data reaches production.
