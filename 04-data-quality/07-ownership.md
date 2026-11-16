# 7. Who owns data quality

[← Data Quality](README.md)

Pipeline engineers own DQ checks by default; a dedicated DQ platform and data stewards are optional and justified only by scale, compliance or cross-domain oversight.

## Engineers own DQ by default

- **Pipeline engineers own DQ checks.** The data engineers who build pipelines design and maintain the DQ checks. This is the default and is mandatory.
- **Unit testing is mandatory.** DQ validations are part of unit and integration testing during initial development.
- **Dedicated DQ role?** Creating a separate data quality team is similar to having a separate QA team for software projects. The question is when such roles are actually needed.

## Engineering-led data quality enforcement

Most fundamental dimensions of data quality can be enforced programmatically:

| Dimension | How engineering enforces it |
| --- | --- |
| Timeliness | Pipelines and orchestration frameworks that provide data availability signals and SLA tracking |
| Completeness | Schema constraints, null checks, row-count reconciliation |
| Duplication | Primary key enforcement, unique constraints, deduplication logic |
| Validity and consistency | Business rules embedded in the pipelines |
| Initial testing | Unit tests and UAT before production |

This gives preventive control at the source and reduces the need for reactive intervention or dedicated DQ teams in most cases.

## Do we need a DQ platform or data stewards?

Not by default. The need depends on business needs, governance requirements and the scale of the data ecosystem. Modern engineering practices let teams enforce data quality programmatically within pipelines, which covers most dimensions effectively.

The deciding question:

> Does the platform deliver benefits that clearly outweigh its total cost (people, infrastructure, licensing, compute), compared with a reactive approach backed by programmatic DQ controls built into pipelines?

If benefits such as enterprise-wide dashboards, regulatory compliance reporting or cross-domain visibility do not clearly exceed the costs, the platform and dedicated roles are unnecessary.

### Operating model: optional, not mandatory

- **Default:** data product teams implement quality controls programmatically in their pipelines.
- **Optional escalation:** a team adopts the DQ platform when business, governance or compliance requirements demand centralized visibility, reporting or non-technical oversight.

This balances efficiency, autonomy and governance, letting teams integrate controls where they are most effective and scaling toward centralized oversight only when justified.

### Role of data stewards

Dedicated data stewards or a centralized DQ team add value only under specific conditions:

- Business-rule validation that cannot be automated.
- Cross-domain governance or regulatory compliance reporting.
- Enterprise-level visibility for non-technical stakeholders.

**Analogy: software quality assurance.** Most modern teams do not keep a separate QA team for every product, because automated testing and CI/CD validation embed quality in the engineering process. Dedicated DQ roles are likewise needed only when scale, compliance or cross-domain oversight requires them; otherwise stewardship responsibilities are distributed across data product teams.
