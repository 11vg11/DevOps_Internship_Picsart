# Terraform Providers and Variables

## 1. Introduction

Terraform providers are plugins that interact with APIs such as AWS. Variables make configuration reusable and parameterize environment-specific values without copying whole configurations.

## 2. Core concepts

- **Provider configuration:** selects plugin behavior and often a Region/account context.
- **Resource:** an object Terraform manages; **data source:** reads an existing object.
- **Input variable:** parameter with type, description, validation, and optional default.
- **Output:** publishes a computed value from a module.
- **Locals:** named expressions for reuse.
- **Provider lock file:** records selected provider versions/checksums for reproducible initialization.

## 3. How it works

`terraform init` installs providers under version constraints and records selections in `.terraform.lock.hcl`. Terraform evaluates inputs, configures providers, builds a dependency graph, and uses provider schemas/API calls during plan and apply.

## 4. Commands and examples

```sh
terraform init
terraform providers
terraform validate
terraform plan -var="aws_region=us-east-1"
```

Use a real Region only for an authorized plan; this example does not apply resources. Avoid passing secrets on command lines where they may be exposed in shell history or process listings.

## 5. Configuration examples

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 5.0, < 7.0"
    }
  }
}

variable "aws_region" {
  description = "Region for this environment."
  type        = string
  nullable    = false
}

provider "aws" {
  region = var.aws_region
}

output "selected_region" {
  value = var.aws_region
}
```

Constraints are examples, not a recommendation to use a particular provider release. Check compatibility and commit `.terraform.lock.hcl`. Do not output secrets: marking an output `sensitive` redacts normal display but does not remove the value from state.

## 6. Hands-on exercises

**Prerequisites:** Terraform CLI; no AWS resources needed. **Expected outcome:** initialize and validate a configuration while keeping credentials outside it.

1. In a disposable folder, add a provider requirement and typed Region variable.
2. Run `terraform init`, `terraform fmt -check`, and `terraform validate`.
3. Inspect the lock file and explain why it belongs in source control.
4. Add a placeholder local value and validate; do not add access keys or apply resources.

## 7. Real-world use cases

Configuring multi-region environments, reusing a module with different inputs, and querying existing cloud data to inform new resources.

## 8. Best practices

- Pin compatible provider ranges, review upgrades, and commit the dependency lock file.
- Prefer typed variables with useful descriptions and validation.
- Supply sensitive values through a secret manager/approved environment mechanism, not committed tfvars.
- Avoid hardcoded credentials and overly broad provider assumptions; verify account and Region.
- Mark output sensitivity when appropriate, while remembering state remains sensitive.

## 9. Common errors and troubleshooting

Provider initialization errors often involve invalid source/version constraints, network access, or lock-file conflicts. Type errors come from mismatched inputs; authentication errors require checking the active principal and credential chain without printing secrets.

## 10. Interview questions

1. **Provider vs. resource?** A provider implements platform API operations; a resource describes an object managed through it.
2. **Resource vs. data source?** Resources manage lifecycle; data sources read existing information.
3. **Why commit the lock file?** It records selected provider versions and checksums for consistent initialization.
4. **How do you pass environment settings?** Typed variables through reviewed, non-secret config or controlled runtime inputs.
5. **Does `sensitive = true` remove a value from state?** No, it primarily redacts display; state still requires protection.

## 11. Further learning

- [Terraform providers](https://developer.hashicorp.com/terraform/language/providers)
- [Input variables](https://developer.hashicorp.com/terraform/language/values/variables)
- [Output values](https://developer.hashicorp.com/terraform/language/values/outputs)

## 12. Progress checklist

- [ ] Explain providers, resources, data sources, variables, and outputs
- [ ] Initialize and validate a provider configuration
- [ ] Use types and constraints and commit the provider lock file
- [ ] Explain why sensitive values can still be present in state
