# MLOps SageMaker Deployment Makefile
# Load environment variables from .env file

include .env
export

SHELL := /bin/bash
ROOT_DIR := $(shell cd ../.. && pwd)
VENV := $(ROOT_DIR)/.venv
MANIFEST := $(ROOT_DIR)/manifests/mlops-sagemaker/deployment.yaml

.PHONY: help setup bootstrap-cdk bootstrap-seedfarmer bootstrap deploy destroy clean status deploy-% destroy-%

help: ## Show this help message
	@echo "MLOps SageMaker Deployment"
	@echo ""
	@echo "Usage: make [target]"
	@echo ""
	@echo "Targets:"
	@echo "  deploy              Deploy mlops-sagemaker"
	@echo "  deploy-<name>       Deploy another manifest"
	@echo "  destroy             Destroy mlops-sagemaker"
	@echo "  destroy-<name>      Destroy another deployment"
	@echo "  status              List all deployments"
	@echo "  setup               Create venv and install deps"
	@echo "  bootstrap           Bootstrap CDK + SeedFarmer"
	@echo ""
	@echo "Available manifests for deploy-<name>:"
	@ls -1 $(ROOT_DIR)/manifests | grep -v mlops-sagemaker-multiacc | sed 's/^/  /'

setup: ## Create virtual environment and install dependencies
	@echo "Creating virtual environment and installing dependencies..."
	cd $(ROOT_DIR) && uv venv && source $(VENV)/bin/activate && uv pip install -r requirements.txt
	@echo "Setup complete. Run 'source $(VENV)/bin/activate' to activate."

bootstrap-cdk: ## Bootstrap CDK in the target account/region
	@echo "Bootstrapping CDK in $(PRIMARY_ACCOUNT)/$(PRIMARY_REGION)..."
	cdk bootstrap aws://$(PRIMARY_ACCOUNT)/$(PRIMARY_REGION)

bootstrap-seedfarmer: ## Bootstrap SeedFarmer toolchain
	@echo "Bootstrapping SeedFarmer toolchain..."
	source $(VENV)/bin/activate && \
	seedfarmer bootstrap toolchain \
		--project aiops \
		--trusted-principal $(ADMIN_ROLE_ARN) \
		--as-target

bootstrap: bootstrap-cdk bootstrap-seedfarmer ## Bootstrap both CDK and SeedFarmer

deploy: ## Deploy the MLOps SageMaker manifest
	@echo "Deploying MLOps SageMaker manifest..."
	@echo "Account: $(PRIMARY_ACCOUNT)"
	@echo "Region: $(PRIMARY_REGION)"
	source $(VENV)/bin/activate && \
	seedfarmer apply $(MANIFEST)

destroy: ## Destroy the MLOps SageMaker deployment
	@echo "Destroying MLOps SageMaker deployment..."
	source $(VENV)/bin/activate && \
	seedfarmer destroy mlops-sagemaker

deploy-%: ## Deploy another manifest (e.g., make deploy-mlflow-tracking)
	@echo "Deploying $* manifest..."
	source $(VENV)/bin/activate && \
	seedfarmer apply $(ROOT_DIR)/manifests/$*/deployment.yaml

destroy-%: ## Destroy another deployment (e.g., make destroy-mlflow-tracking)
	@echo "Destroying $* deployment..."
	source $(VENV)/bin/activate && \
	seedfarmer destroy $*

status: ## Check deployment status
	@echo "Checking deployment status..."
	source $(VENV)/bin/activate && \
	seedfarmer list deployments

clean: ## Clean up virtual environment
	@echo "Cleaning up..."
	rm -rf $(VENV)

all: setup bootstrap deploy ## Run full setup and deployment
