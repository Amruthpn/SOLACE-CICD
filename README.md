# GitOps Solace Broker Provisioning Pipeline

This repository contains an automated infrastructure-as-code pipeline for managing **Solace PubSub+ Broker** configurations natively using **Ansible** and **Jenkins**.

## Architecture Setup
- **Dev Broker Context:** Runs on `localhost:8080` (Triggered via pushes to `dev` branch)
- **Production Broker Context:** Runs on `localhost:8081` (Triggered via merges to `main` branch)
- **CI/CD Execution Engine:** Jenkins running locally on port `8085` via a dedicated virtual environment path.

## How to Execute Locally
1. Configure credentials of type **Username with password** inside your Jenkins server instance labeled exactly `solace_admin_creds`.
2. Configure your pipeline project pointing directly to this SCM engine tracking `*/dev` and `*/main` vectors.
