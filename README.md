# Terraform with Private Module and GitHub Actions

This repository demonstrates how to use a Terraform module from a private GitHub repository in a different private repository with a GitHub Actions workflow.

## Steps

### 1. Generate SSH Keys
Create an SSH key pair that will be used by GitHub Actions to authenticate and access the private repository.
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```
### 2. Add Deploy Key to the Private Module Repository
  - Go to the private repository containing your Terraform module.
  - Navigate to Settings > Deploy keys.
  - Click Add deploy key.
  - Give it a title (e.g., "GitHub Actions Key") and paste the contents of the id_ed25519.pub file into the Key field.
  - Check Allow write access if needed, then click Add key.

### 3. Add Private Key to GitHub Actions Secrets
  - Go to the repository containing your GitHub Actions workflow.
  - Navigate to Settings > Secrets and variables > Actions > New repository secret.
  - Name the secret SSH_PRIVATE_KEY and paste the contents of the id_ed25519 file into the Value field.
  - Click Add secret.

### 4. Update Your GitHub Actions Workflow
Modify your workflow file to include the ssh-agent and configure it to use the private SSH key.
yaml```
name: terraform

on:
  push:
    branches:
      - main

jobs:
  terraform:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3
        with:
          terraform_version: 1.9.3

      - name: Setup SSH
        uses: webfactory/ssh-agent@v0.9.1
        with:
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}

      - name: Terraform Init
        run: terraform init

      - name: Terraform Validate
        run: terraform validate

      - name: Terraform Plan
        run: terraform plan

      - name: Terraform Apply
        if: github.ref == 'refs/heads/main' && github.event_name == 'push'
        run: terraform apply -auto-approve
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```
### 5. Reference the Private Module in Your Terraform Code
In your Terraform configuration, reference the private module using the SSH URL format.

hcl```
module "example" {
  source = "git@github.com:username/private-module-repo.git//path/to/module"
  # Other module variables
}
```

### 6. Run the Workflow
Push your changes to the GitHub repository to trigger the workflow.

bash```
git add .
git commit -m "Setup Terraform with private module"
git push origin main
```
