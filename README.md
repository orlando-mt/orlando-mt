# Orlando Marcos Torres

Systems Engineer focused on Cloud and DevOps, with 13+ years building and operating AWS infrastructure. CKA and CKAD certified. Based in Lima, Peru.

I work mostly on the platform side: Kubernetes, Terraform, CI/CD pipelines, and the database migrations and cost work that come with running large AWS estates.

- **Cloud:** AWS (EKS, ECS, Aurora, DocumentDB, MSK, Lambda, CloudFront)
- **IaC:** Terraform, GitHub Actions, DevSecOps pipelines
- **Kubernetes:** EKS, Helm, IRSA, Karpenter, External Secrets
- **Languages:** Python, Java, Node.js

---

## Terraform modules for AWS

A set of production-oriented modules covering a full AWS platform. Each one ships with a working example, a CI pipeline running `fmt`, `validate`, TFLint and Checkov, and a README that explains the design decisions behind it — not just the inputs.

### Networking

| Module | What it does |
|--------|--------------|
| [terraform-aws-vpc](https://github.com/orlando-mt/terraform-aws-vpc) | Service-segmented private VPC: one subnet per service per AZ, own route table, flow logs |
| [terraform-aws-vpc-endpoint](https://github.com/orlando-mt/terraform-aws-vpc-endpoint) | Interface and Gateway endpoints with short service names and endpoint policies |
| [terraform-aws-vpc-link](https://github.com/orlando-mt/terraform-aws-vpc-link) | API Gateway VPC Link with egress scoped to the backend |

### Compute and containers

| Module | What it does |
|--------|--------------|
| [terraform-aws-eks](https://github.com/orlando-mt/terraform-aws-eks) | EKS cluster with access entries, multiple node groups, addon ordering, private endpoint by default |
| [terraform-aws-ecs-cluster](https://github.com/orlando-mt/terraform-aws-ecs-cluster) | ECS cluster with capacity providers, Container Insights and ECS Exec logging |
| [terraform-aws-ecs-service](https://github.com/orlando-mt/terraform-aws-ecs-service) | ECS service with typed container definitions, IAM roles, target group and autoscaling |
| [terraform-aws-lambda](https://github.com/orlando-mt/terraform-aws-lambda) | Lambda with all packaging types, least-privilege role and event source mappings |
| [terraform-aws-ecr](https://github.com/orlando-mt/terraform-aws-ecr) | ECR repository with KMS encryption, lifecycle policy and replication |
| [terraform-aws-load-balancer-controller](https://github.com/orlando-mt/terraform-aws-load-balancer-controller) | AWS Load Balancer Controller on EKS with IRSA and a tag-scoped IAM policy |

### Data

| Module | What it does |
|--------|--------------|
| [terraform-aws-rds](https://github.com/orlando-mt/terraform-aws-rds) | Aurora PostgreSQL, provisioned or Serverless v2, credentials in Secrets Manager |
| [terraform-aws-documentdb](https://github.com/orlando-mt/terraform-aws-documentdb) | DocumentDB cluster with managed credentials and granular security group rules |
| [terraform-aws-dynamodb](https://github.com/orlando-mt/terraform-aws-dynamodb) | DynamoDB tables with GSI/LSI, streams, global tables and capacity autoscaling |
| [terraform-aws-s3](https://github.com/orlando-mt/terraform-aws-s3) | S3 with secure defaults, lifecycle rules, replication and VPC endpoint policies |

### Messaging and events

| Module | What it does |
|--------|--------------|
| [terraform-aws-msk](https://github.com/orlando-mt/terraform-aws-msk) | MSK cluster with IAM auth and a security group derived from the enabled listeners |
| [terraform-aws-sqs](https://github.com/orlando-mt/terraform-aws-sqs) | SQS queues with dead letter queues, encryption and FIFO support |
| [terraform-aws-sns](https://github.com/orlando-mt/terraform-aws-sns) | SNS topics with inline subscriptions, message filtering and per-subscription DLQs |
| [terraform-aws-ses](https://github.com/orlando-mt/terraform-aws-ses) | SES domain identities with Easy DKIM and custom MAIL FROM |

### Security and identity

| Module | What it does |
|--------|--------------|
| [terraform-aws-iam](https://github.com/orlando-mt/terraform-aws-iam) | IAM roles and policies with referential validation at plan time |
| [terraform-aws-kms](https://github.com/orlando-mt/terraform-aws-kms) | KMS keys with aliases, rotation and explicit key policies |
| [terraform-aws-acm](https://github.com/orlando-mt/terraform-aws-acm) | ACM certificates with optional automatic Route53 validation |
| [terraform-aws-cognito](https://github.com/orlando-mt/terraform-aws-cognito) | Cognito user pools with MFA, custom attributes and Lambda triggers |
| [terraform-aws-waf](https://github.com/orlando-mt/terraform-aws-waf) | WAFv2 with managed rule groups, IP sets and rate limiting |
| [terraform-aws-eks-external-secrets](https://github.com/orlando-mt/terraform-aws-eks-external-secrets) | External Secrets Operator with one IRSA role and SecretStore per namespace |

### Delivery

| Module | What it does |
|--------|--------------|
| [terraform-aws-cloudfront](https://github.com/orlando-mt/terraform-aws-cloudfront) | CloudFront with S3, VPC and API Gateway origins behind a single domain |
| [terraform-aws-api-gateway](https://github.com/orlando-mt/terraform-aws-api-gateway) | HTTP API base infrastructure, with stages deployed by the application pipeline |

---

## How these modules are built

A few rules run through all of them:

**A module creates what only it uses, and takes shared infrastructure as input.** The flow logs role lives in the VPC module because nothing else will ever use it. KMS keys do not: one key usually encrypts several services, its policy is governed centrally, and its deletion window outlives any `terraform destroy`.

**Infrastructure and deployment are separate concerns.** The API Gateway module creates the API and its domain but no stages, because the OpenAPI definition is deployed by the application pipeline. The ECS cluster and the services on it are separate modules for the same reason: a release should never hold the state of the whole platform.

**Secure defaults, with the unsafe option available.** The EKS API endpoint is private and public access requires listing explicit CIDRs. Aurora and DocumentDB keep their master password in Secrets Manager. S3 blocks public access and denies plaintext requests. Nodes use an IMDS hop limit of 1 so pods cannot borrow node credentials.

**Validations catch mistakes at plan time.** Not just enum checks: capacity required for provisioned billing modes, replicas requiring streams, ACM certificates for CloudFront having to be in us-east-1, the WAF origin request policy that would break API Gateway. Failing in the plan is cheaper than failing ten minutes into an apply.

**Security findings are addressed, not silenced.** Every Checkov finding was either fixed or skipped with a written justification naming the reason — a false positive, a static-analysis limitation, or a deliberate trade-off. The skips are in the code, next to what they refer to.

---

## Elsewhere

- LinkedIn: [in/orlando-marcos-torres](https://www.linkedin.com/in/orlando-marcos-torres)
- Email: omarcost@gmail.com
