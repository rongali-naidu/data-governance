# 6. DQ score and certification

[← Data Quality](README.md)

## Can we measure data quality? The DQ score

Yes: assign each dataset a set of rules to validate, and use the percentage of rules that pass as its data quality score.

```
DQ score = (rules passed / rules evaluated) × 100
```

For example, a dataset with 20 rules where 18 pass on today's run scores 90%. The score can back a data quality certification for the dataset and be tracked over time.

Refinements to consider when defining the standard:

- **Weight by severity.** A failed primary-key check should cost more than a failed formatting warning (Deequ already distinguishes `CheckLevel.Error` from `CheckLevel.Warning`).
- **Score per dimension.** Report completeness, validity, uniqueness and so on separately, so a single number does not hide where the problem is.
- **Score per run and as a trend.** A run-level score drives alerts; a rolling score supports certification.
- **Open question:** what thresholds map a score to a certification level, and are they set per domain in the data contract?

## Data quality certification vs data usability certification

The bronze/silver/gold labels of the medallion architecture describe how ready a dataset is to use, not how good its data is. These are two separate certifications.

- **Product (data) quality:** each provider (domain) ensures its data meets the required data quality standard defined in its data contract.
- **Data usability certification:** gives users a clear signal of a dataset's readiness level (raw, curated or summarized) by indicating the degree of transformation, cleaning and standardization it has undergone. It parallels the stage, fact, dimension and aggregated tables of traditional data warehouses, and the bronze, silver and gold layers of the medallion architecture.

| | Data quality certification | Data usability certification |
| --- | --- | --- |
| Question it answers | How good is the data? | How ready is the data for analysis or consumption? |
| Indicates | How accurate, complete, consistent and reliable a dataset is | How transformed, cleaned and standardized a dataset is: raw, curated or summarized |
| Owned by | Each provider (domain), which must meet the agreed DQ standard in its data contract | The publishing team, based on the modeling and transformation applied |
| Traditional parallel | Constraints plus post-load validation scripts | Stage, fact and dimension tables, and aggregated datasets |
| Medallion parallel | Applies at every layer | Bronze, silver and gold layers |

Not every team publishing to the data lake follows dimensional modeling or other usability-focused design practices. Usability certification clearly marks datasets that are modeled, structured and ready for analytical or business consumption.

**Open item:** the data quality standard (data contract) is still to be finalized.
