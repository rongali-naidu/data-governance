# 5. Data profiling

[← Data Quality](README.md)

Data profiling examines data from existing sources to collect statistics and summaries about a dataset's structure and content. Those statistics are the baseline that DQ checks, anomaly detection and drift monitoring compare against.

Profiling is a foundational step in understanding data. Summary metrics let a newcomer learn a dataset quickly without querying it: the value range of a numeric column, or the distinct values and their frequency in a string column. Tracked over time, the same metrics reveal deviations, which depending on context are data anomalies or DQ issues.

## Why profiling matters

- **Data quality assurance:** identify missing, inconsistent or erroneous data.
- **Better decision making:** understand data distributions, outliers and patterns.
- **Schema understanding:** discover data types, cardinality and relationships.
- **Compliance and governance:** monitor data completeness and integrity over time.
- **ETL optimization:** design efficient pipelines from known data characteristics.

## How profiling feeds data quality

| DQ use | How the profile is used |
| --- | --- |
| Rule design | Observed ranges, allowed values and null rates become the thresholds in schema and business rules ([categories 1–2](04-check-categories.md)). |
| Anomaly detection | Today's metrics are compared with history, e.g. row count drops 40% or a historically complete column starts showing nulls (category 4). |
| Drift monitoring | Changes in percentiles, mean, standard deviation or value frequencies signal distribution drift (category 5). |
| DQ score inputs | Completeness and uniqueness ratios can feed the per-dimension score ([DQ score](06-dq-score-and-certification.md)). |

## Metrics to collect

Metrics span three levels: dataset, column and column value.

### Dataset-level metrics

| Metric | Definition |
| --- | --- |
| RowCount | Total number of rows |
| Size (MB/GB) | Data size |
| ColumnCount | Number of columns |

### Column-level metrics

| Metric | Definition |
| --- | --- |
| RowCount | Number of rows profiled for the column |
| NullCount / EmptyCount | Count of null values / empty values |
| Completeness ratio | Rows that are neither null nor empty / row count |
| DistinctValueCount (cardinality) | Number of distinct values |
| Uniqueness ratio | Distinct count / row count |
| Size (MB) | Data size of the column |

### Additional metrics for string columns

| Metric | Definition |
| --- | --- |
| MaxLength | Maximum string length |
| MinLength | Minimum string length |
| AverageLength | Average string length |
| Mode | Most frequent value (derived from value counts) |

### Additional metrics for numeric columns

| Metric | Definition |
| --- | --- |
| Q1 (25th percentile) | First quartile |
| Median (50th percentile) | Second quartile |
| Q3 (75th percentile) | Third quartile |
| Mean | Average value |
| Standard deviation | Variation in values |
| Min / Max | Minimum and maximum values |
| Sum | Total sum of values |

### Column-value metrics

For string columns especially, the frequency distribution of distinct values is critical: it exposes new categories, misspellings and skew.

| Metric | Definition |
| --- | --- |
| ColumnValue | Distinct column value (null treated separately) |
| RowCount | Number of rows containing that value |
| SQLRuleName | Optional: associates profiling with a specific validation or business rule |

## Need for a platform-agnostic profiling system

Enterprises often run heterogeneous data platforms: on-prem, cloud and multiple vendors. A robust profiling system must:

- Run on any platform (Hive, Redshift, Snowflake, Athena, Databricks and others).
- Support incremental profiling for partitioned and large datasets.
- Store profiling results in a generic, standardized schema for easy querying and integration.
- Allow extensible metrics and rules for custom profiling needs.
- Integrate with DQ monitoring and lineage frameworks.

## Generic schema for profiling results

Three tables capture dataset-, column- and value-level metrics. All three share the same key: dataset, granularity (entire table or partition), partition value and profile timestamp.

### Table metrics

| Column | Type | Description |
| --- | --- | --- |
| `dataset_name` | string | Dataset identifier |
| `granularity` | string | "Entire Table" or "Partition" |
| `granularity_value` | string | Partition name, or NULL for the entire table |
| `profile_date` | datetime | Timestamp of the profiling run |
| `row_count` | int | Total number of rows |
| `column_count` | int | Number of columns |
| `data_size_gb` | float | Data size in GB |

### Column metrics

| Column | Type | Description |
| --- | --- | --- |
| `dataset_name` | string | Dataset identifier |
| `granularity` | string | "Entire Table" or "Partition" |
| `granularity_value` | string | Partition name, or NULL for the entire table |
| `profile_date` | datetime | Timestamp of the profiling run |
| `column_name` | string | Column name |
| `data_type` | string | Inferred data type (e.g. string, int) |
| `row_count` | int | Total rows |
| `non_null_count` | int | Count of non-null values |
| `null_count` | int | Count of nulls |
| `empty_string_count` | int | For strings, count of empty strings (`''` is not the same as NULL) |
| `distinct_count` | int | Number of distinct values (may be approximate) |
| `uniqueness_ratio` | float | `distinct_count / row_count` |
| `completeness_ratio` | float | `(row_count - null_count - empty_string_count) / row_count` |
| `min_value` | string | Minimum value, stored as string to support all types |
| `max_value` | string | Maximum value |
| `mean` | float | Numeric mean |
| `std_dev` | float | Numeric standard deviation |
| `sum` | float | Numeric sum |
| `q1` | float | 25th percentile (numeric) |
| `median` | float | 50th percentile (numeric) |
| `q3` | float | 75th percentile (numeric) |
| `min_length` | int | Minimum string length |
| `max_length` | int | Maximum string length |
| `average_length` | float | Average string length |
| `data_size_mb` | float | Size of column data in MB |

### Column-value metrics

| Column | Type | Description |
| --- | --- | --- |
| `dataset_name` | string | Dataset identifier |
| `granularity` | string | "Entire Table" or "Partition" |
| `granularity_value` | string | Partition name, or NULL |
| `profile_date` | datetime | Timestamp of the profiling run |
| `column_name` | string | Column name |
| `column_value` | string | Distinct column value (null treated separately) |
| `row_count` | int | Number of rows with this value |
| `sql_rule_name` | string | Optional: associated validation or business rule |

## Design considerations

- **Completeness ratio formula.** Written as "(non-null + non-empty) / total", it can double count. Defining it as rows that are neither null nor empty, as in the schema above, avoids that.
- **Distinct counts at scale.** Exact distinct counts are expensive on large tables; approximate algorithms (HyperLogLog, e.g. `APPROX_COUNT_DISTINCT`) are usually enough for profiling. Record whether a value is approximate.
- **High-cardinality columns.** Value frequencies for IDs or free text can be as large as the table itself. Cap the column-value table to the top N values, or profile only columns below a cardinality threshold.
- **Run identifiers.** Adding a `profile_run_id` and `source_platform` makes it easier to join the three tables and compare the same dataset across platforms.
- **Retention.** Keep enough history for anomaly baselines (for example, the same weekday over several weeks) and roll older runs into summaries.
