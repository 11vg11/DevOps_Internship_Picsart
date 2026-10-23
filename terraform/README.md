# Terraform and Infrastructure as Code

## 1. Introduction

Infrastructure as Code (IaC) manages infrastructure through versioned, reviewable files. Terraform is a declarative IaC tool: configurations describe desired resources and Terraform plans changes to converge real infrastructure toward that description.

## 2. Core concepts

Terraform configurations combine providers, resources, data sources, variables, locals, outputs, modules, and state. A dependency graph determines operation order. State maps configuration objects to real objects and can contain sensitive values.

For a hands-on AWS example, see the [beginner project for monthly budget alerts](./budget-alerts/README.md). It creates an alerting budget; it does not cap spend or stop resources.

## 3. How it works

Terraform initializes providers/modules, reads configuration and state, refreshes remote objects as needed, calculates a plan, and applies approved operations. Configuration and state serve different purposes: configuration describes desired intent; state tracks managed object identity and attributes.

## 4. Commands and examples

Run these only in a reviewed lab directory:

```sh
terraform init       # Initialize providers and modules
terraform fmt -check # Check canonical formatting; omit -check to format files
terraform validate   # Validate configuration structure and types
terraform plan       # Review proposed changes before applying
terraform apply      # Applies the approved plan; may create billable resources
terraform destroy    # Destroys managed resources; use only for owned disposable labs
```

Use `terraform plan -out=reviewed.tfplan` followed by `terraform apply reviewed.tfplan` when an exact reviewed plan is needed; protect the plan file because it can contain sensitive values. Never apply or destroy infrastructure without verifying workspace, account, region, ownership, and plan.

## 5. Configuration example

Read-only AWS configuration example (provider initialization may require AWS credentials; it declares no resources):

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 5.0, < 7.0"
    }
  }
}

variable "aws_region" {
  description = "AWS region for read-only provider queries."
  type        = string
}

provider "aws" {
  region = var.aws_region
}

data "aws_caller_identity" "current" {}

output "account_id" {
  value = data.aws_caller_identity.current.account_id
}
```

Provider version constraints are illustrative; select and test a supported version for the project and commit `.terraform.lock.hcl`. Supply `aws_region` through an ignored local tfvars file or approved environment workflow; do not put credentials there.

## 6. Hands-on exercises

**Prerequisites:** Terraform installed, Git, and optionally authorized AWS read-only credentials. **Expected outcome:** format and validate a read-only configuration and understand a plan without provisioning AWS resources.

1. Save the example in a new disposable directory as `main.tf`.
2. Provide a Region through an untracked local variable file, then run `terraform init`, `terraform fmt -check`, and `terraform validate`.
3. If authorized, run `terraform plan` and confirm it has no managed resource creation. Remove local `.terraform` and state files only after confirming they are from this exercise; no cloud resource should be created.
4. Review the detailed guides for [IaC](./iac-fundamentals.md), [approaches](./imperative-vs-declarative.md), [providers and variables](./providers-variables.md), [state](./state-and-backend.md), and [modules](./modules.md).
5. If you have an authorized AWS account, follow the [budget-alerts project](./budget-alerts/README.md). Review its plan before applying; AWS Budgets alerts do not prevent charges.

## 7. Real-world use cases

Repeatable environment provisioning, reviewed infrastructure changes, reusable platform modules, drift detection, and auditable infrastructure ownership.

## 8. Best practices

- Use remote state with encryption, access controls, locking, backups, and recovery procedures for shared workloads.
- Never commit state, plan outputs, secret variables, or credentials; state and plans may expose sensitive data.
- Pin provider versions, commit the dependency lock file, use modules deliberately, and run formatting/validation/security checks in CI.
- Review plans, use separate accounts/workspaces/environments intentionally, tag resources, and define ownership and cleanup.
- Prefer roles/temporary credentials; estimate cloud costs and avoid hardcoded secrets.
- Avoid manual changes to managed resources; investigate drift and reconcile deliberately.

## 9. Common errors and troubleshooting

| Symptom | Checks |
|---|---|
| Provider init fails | Network/proxy, provider source/version, lock file, and registry access. |
| Credentials or access denied | Active principal, role, account, Region, and IAM authorization. |
| State lock error | Confirm no active run owns the lock before following backend recovery procedures; never force-unlock blindly. |
| Unexpected replacement | Review immutable provider attributes, resource address, state, and plan details before approval. |
| Drift | Compare remote changes, state refresh, and configuration; decide which system is authoritative before applying. |

## 10. Interview questions

1. **What is Terraform state?** A mapping and metadata store connecting configuration addresses to real objects.
2. **What does a plan do?** It proposes operations based on configuration, state, and provider observations without applying them.
3. **Why is Terraform declarative?** Users specify desired resources; Terraform computes an operation graph to reach that state.
4. **Why protect state?** It can contain sensitive values and is required for correct resource tracking.
5. **What is a provider?** A plugin that lets Terraform manage and query a platform's API.

## 11. Further learning

- [Terraform documentation](https://developer.hashicorp.com/terraform/docs)
- [Terraform CLI commands](https://developer.hashicorp.com/terraform/cli/commands)
- [AWS provider documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

## 12. Progress checklist

- [ ] Explain resources, data sources, providers, variables, outputs, and modules
- [ ] Run init, fmt, validate, and plan in a disposable directory
- [ ] Review and explain a plan before any apply
- [ ] Explain state protection, ownership, and safe cleanup
