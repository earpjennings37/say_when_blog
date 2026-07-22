#############
##README - Infra Repo##
#############
# Thanos IRSA Setup (Terraform)

This folder contains the IAM Role for Service Accounts (IRSA) configuration
required for Thanos to write Prometheus metrics to AWS S3.

## Components

### IAM Policy
Allows:
- s3:PutObject
- s3:GetObject
- s3:ListBucket

Against the bucket:
- thanos-infinity-37

### IAM Role (IRSA)
Trusts the EKS OIDC provider and allows the Prometheus service account:

system:serviceaccount:monitoring:prometheus-kube-prometheus-prometheus

to assume the role.

### Role Attachment
Attaches the S3 policy to the IRSA role.

## Output
`thanos_irsa_role_arn` is used in the GitOps repo to annotate the Prometheus
service account.

## Apply
terraform init
terraform apply

#############
##README - GitOps Repo##
#############

# Thanos Deployment (GitOps)

This folder contains the GitOps-managed deployment of Thanos components:
- Thanos Sidecar (inside kube-prometheus-stack)
- Thanos Store Gateway
- Thanos Compactor
- Thanos Query

## Requirements

1. Prometheus installed via kube-prometheus-stack
2. IRSA role created in the infra repo
3. S3 bucket: thanos-infinity-37

## Components

### Thanos Sidecar
Uploads Prometheus blocks to S3 using IRSA.

### Thanos Store Gateway
Reads blocks from S3.

### Thanos Compactor
Downsamples and enforces retention.

### Thanos Query
Aggregates data from:
- Prometheus Sidecar
- Store Gateway
- Compactor

Grafana should point to Thanos Query.

## Apply
Argo CD will automatically sync these Applications and deploy Thanos.