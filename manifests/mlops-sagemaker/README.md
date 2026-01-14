# MLOps with Amazon SageMaker

This manifest deploys a complete MLOps environment on AWS using SageMaker.

## What Gets Deployed

| Component | Description |
|-----------|-------------|
| **VPC** | New VPC with public/private subnets, NAT gateway |
| **S3 Buckets** | Artifact storage with SSE encryption |
| **ECR Repository** | Container registry for custom SageMaker kernels |
| **SageMaker Studio Domain** | Full Studio environment with MLflow, JupyterLab, Docker access |
| **User Profiles** | `ds-user-1` (data scientist), `lead-ds-user-1` (lead) |
| **Custom Kernel** | Example "echo" kernel image |
| **SageMaker Project Templates** | XGBoost training pipeline (Service Catalog) |

## Prerequisites

- Python 3.11+
- Node.js 18+
- AWS CLI configured
- uv (Python package manager)

## Quick Start

### 1. Configure Environment

Copy the example `.env` file and update with your values:

```bash
cp .env.example .env
# Edit .env with your AWS account details
```

Required variables:
```
AWS_PROFILE=dev
AWS_DEFAULT_REGION=us-east-1
PRIMARY_ACCOUNT=<your-account-id>
PRIMARY_REGION=us-east-1
ADMIN_ROLE_ARN=arn:aws:iam::<your-account-id>:role/<your-admin-role>
```

### 2. Run Deployment

```bash
# See all available commands
make help

# Full deployment (setup + bootstrap + deploy)
make all

# Or step by step:
make setup              # Create venv and install deps
make bootstrap          # Bootstrap CDK and SeedFarmer
make deploy             # Deploy the manifest
```

### 3. Verify Deployment

After deployment (~15-25 minutes):
1. Open AWS Console → SageMaker → Studio
2. Verify domain exists in your region
3. Launch Studio and confirm user profiles are available

## Makefile Targets

| Target | Description |
|--------|-------------|
| `make help` | Show all available commands |
| `make setup` | Create virtual environment and install dependencies |
| `make bootstrap-cdk` | Bootstrap CDK in target account/region |
| `make bootstrap-seedfarmer` | Bootstrap SeedFarmer toolchain |
| `make bootstrap` | Bootstrap both CDK and SeedFarmer |
| `make deploy` | Deploy the MLOps SageMaker manifest |
| `make destroy` | Destroy the deployment |
| `make status` | Check deployment status |
| `make clean` | Clean up virtual environment |
| `make all` | Run full setup and deployment |

## Cleanup

```bash
make destroy
```

## Files

- `deployment.yaml` - Main deployment manifest
- `networking-modules.yaml` - VPC configuration
- `storage-modules.yaml` - S3 and ECR configuration
- `sagemaker-studio-modules.yaml` - SageMaker Studio configuration
- `kernels-modules.yaml` - Custom kernel configuration
- `sagemaker-templates-modules.yaml` - SageMaker project templates
- `.env` - Environment variables (git-ignored)
- `Makefile` - Deployment automation
