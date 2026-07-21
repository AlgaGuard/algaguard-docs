# AWS six-month cost plan

Checked: 2026-07-22. This is a planning snapshot, not a quote. Re-check every amount, eligible service, region, tax, and account condition immediately before creating resources.

## Current official program check

AWS currently describes a new-customer Free plan that provides an initial credit and opportunities to earn more, and ends after six months or when credits are depleted, whichever comes first. The dated public announcement describes up to USD 200 total credits; the billing guide explains account-plan behavior and automatic closure/content-retention risk when a Free plan ends. Eligibility is limited and AlgaGuard's actual account status is `TBD`.

Official sources checked:

- [AWS Free Tier](https://aws.amazon.com/free/)
- [Choosing an AWS Free Tier plan](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier-plans.html)
- [2025 Free Tier announcement](https://aws.amazon.com/about-aws/whats-new/2025/07/aws-free-tier-credits-month-free-plan/)
- [EC2 On-Demand pricing](https://aws.amazon.com/ec2/pricing/on-demand/)
- [EBS pricing](https://aws.amazon.com/ebs/pricing/)
- [Amazon VPC pricing](https://aws.amazon.com/vpc/pricing/)
- [Route 53 pricing](https://aws.amazon.com/route53/pricing/)
- [S3 pricing](https://aws.amazon.com/s3/pricing/)
- [SES pricing](https://aws.amazon.com/ses/pricing/)
- [AWS Budgets pricing](https://aws.amazon.com/aws-cost-management/aws-budgets/pricing/)
- [AWS Pricing Calculator](https://calculator.aws/)

`TBD`: AWS account age/eligibility, plan type, credit balance/expiry, allowed services, region, EC2 type/architecture, EBS size, backup target, public DNS, SES availability, and approved six-month spend.

## Preferred pilot bill of materials

| Cost area | Plan | Deployment-time calculation |
|---|---|---|
| EC2 Linux | One On-Demand instance, Docker Compose; type from local measurements | Current regional hourly instance rate x running hours |
| EBS | gp3 or reviewed alternative for OS, database, objects/logs, and headroom | Provisioned GB-month + extra IOPS/throughput if any |
| Snapshots/backups | Daily, retained by policy; restore tested | Incremental snapshot/object GB-month and requests |
| Public IPv4 | Prefer one address; AWS charges public IPv4 usage | Current VPC public IPv4 hourly rate x address-hours |
| DNS | Stable product names | Hosted-zone months + queries + registration if purchased |
| Data transfer | MQTT, HTTPS, image pulls, backups, OTA | Regional transfer-out tiers; include reconnect/OTA tests |
| S3 | Optional off-VM backup/firmware through adapter only | Storage class GB-month + requests + retrieval/transfer |
| SES | Optional email through adapter only | Messages/data/attachments after sandbox and identity review |
| GHCR/Actions | Portable build/image path | Check GitHub plan quotas separately; not an AWS line item |
| Monitoring | Lightweight portable tools first | Avoid a mandatory CloudWatch dependency; include unavoidable AWS metrics/log costs if enabled |

Do not select an instance from labels alone. Measure the local stack. `ASSUMPTION`: Keycloak, PostgreSQL/TimescaleDB, Redis, EMQX, NGINX, MinIO, and services may exceed a very small VM; reduce optional observability or combine logical pilot services before weakening backups/security.

## Cost guardrails

1. Require explicit owner approval before creating any resource with possible recurring charges.
2. Record the calculator estimate, region, rates, tax assumptions, date, owner, tags, and deletion plan.
3. Enable AWS Budgets actual and forecast alerts at several percentages of the approved amount, plus Free Tier/credit notifications. The exact budget is `TBD`.
4. Tag every resource with project, environment, owner, and expiry date. Inventory weekly.
5. Avoid NAT Gateway, load balancer, EKS, MSK, Kafka, and duplicated public IPs for the pilot unless separately approved.
6. Cap log retention, prune unused images, monitor EBS/free space, and expire backups only through the approved recovery policy.
7. Test backup export before the Free plan/credits expire; do not rely on AWS account retention as a backup.

## Fallbacks

- If Free plan/credits or required services are unavailable, keep the stack local while requesting the campus server.
- If temporary public hosting is essential, use only an explicitly approved paid EC2 budget sized from measurements and revisit the calculator monthly.
- Reduce optional observability and raw cloud retention before removing TLS, backups, or data-integrity controls.
- Pause/decommission AWS and export data if forecast cost exceeds approval. Do not silently continue paid resources.

Phase 1 requires no paid service and creates no AWS resources.
