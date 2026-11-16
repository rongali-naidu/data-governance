# Data Security

[← Data Governance overview](../README.md)

## What is data security?

Data security protects data from unauthorized access, modification, deletion or disclosure. It keeps data confidential, accurate and available to authorized users. Access control is critical, but additional mechanisms such as encryption, integrity checks and monitoring address risks like insider threats, system breaches and network interception.

Data security makes data available to authorized users while blocking unauthorized parties.

## Core objectives: the CIA triad

| Objective | Meaning |
| --- | --- |
| **Confidentiality** | Only authorized users or systems can access data. |
| **Integrity** | Data remains accurate, complete and unaltered. |
| **Availability** | Authorized users can access data when needed. |

> Integrity in the security sense (data is not tampered with) is different from integrity as a [data quality dimension](../04-data-quality/02-dimensions.md) (relationships between entities are maintained).

## Key data security mechanisms

<!-- TODO: replace or extend with the original "Key Data Security Mechanisms" diagram from the blog post. -->

| Mechanism | Protects against | Examples |
| --- | --- | --- |
| Identity and access control | Unauthorized access | Least privilege, RBAC/ABAC, MFA, IAM policies, row/column-level access |
| Encryption at rest | Stolen storage, backups or snapshots | KMS-managed keys, TDE, S3/EBS encryption |
| Encryption in transit | Interception, man-in-the-middle | TLS for all connections |
| Key management | Key misuse or loss | Central KMS/HSM, key rotation, separation of duties |
| Masking and tokenization | Exposure of sensitive fields to users who do not need them | Dynamic masking, tokenized card numbers |
| Integrity checks | Tampering and corruption | Checksums, hashing, signed files |
| Logging, monitoring and auditing | Undetected misuse | Access logs, anomaly alerts, audit trails |
| Backup and recovery | Loss, ransomware, deletion | Versioned backups, tested restores, defined RPO/RTO |

## Why encryption is essential beyond access control

Access control decides who may access data, but it cannot fully prevent misuse when credentials are stolen, insiders act maliciously or a system is breached. Encryption is the last line of defense:

- **Mitigates insider threats:** even DBAs cannot read sensitive fields without the keys.
- **Protects data in transit:** prevents interception and man-in-the-middle attacks.
- **Reduces breach impact:** stolen backups are useless without decryption keys.
- **Keeps data usable for authorized users:** data is decrypted on access for legitimate use.
- **Supports regulatory compliance:** many laws and standards (PCI DSS, GDPR, HIPAA) require or expect encryption of sensitive data.

## Data security policy

Every enterprise has a data security policy that defines how data must be protected from unauthorized access, modification or loss.

**Applies to:** all data (personal, financial, operational, intellectual property and so on).

It defines data classification levels, for example public, internal, confidential and restricted (see the AWS whitepaper on [Data Classification](https://docs.aws.amazon.com/whitepapers/latest/data-classification/data-classification.html)). For each classification level it specifies:

- Access control rules (least privilege, MFA)
- Encryption requirements (at rest and in transit)
- Backup and recovery expectations
- Logging, monitoring and incident response
