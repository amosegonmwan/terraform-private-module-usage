# Terraform with Private Module and GitHub Actions

This repository demonstrates how to use a Terraform module from a private GitHub repository in a different private repository with a GitHub Actions workflow.

## Steps

### 1. Generate SSH Keys
Create an SSH key pair that will be used by GitHub Actions to authenticate and access the private repository.
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```
### 2.
