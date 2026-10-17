# Terraform Modules

## 1. Introduction

A Terraform module is a collection of configuration files managed together. The root module is the working directory; child modules package reusable resources behind inputs and outputs.

## 2. Core concepts

- **Root module:** configuration executed from the current directory.
- **Child module:** reusable configuration referenced with a `module` block.
- **Inputs/outputs:** module interface; **source/version:** where a module comes from and which version is used.
- **Composition:** modules can call other modules; resources and outputs form dependency relationships.
- A module is not automatically secure or reusable merely because it has a separate directory.

## 3. How it works

Terraform loads module source during initialization, evaluates module inputs, and incorporates child resources into the root dependency graph. A child module can expose selected values as outputs. Dependencies should normally be expressed through references rather than unnecessary explicit ordering.

## 4. Commands and examples

```sh
terraform init
terraform validate
terraform plan
```

`init` installs module sources. Review source and versions; a plan still needs review before apply.

## 5. Configuration examples

Root configuration:

```hcl
module "example" {
  source = "./modules/example"

  name = "lab-example"
}
```

Child module `modules/example/main.tf`:

```hcl
variable "name" {
  description = "Name label for this example."
  type        = string
}

resource "terraform_data" "example" {
  input = var.name
}

output "name" {
  value = terraform_data.example.output
}
```

This uses Terraform's built-in `terraform_data` resource (Terraform 1.4+) and does not create AWS infrastructure. For published modules, use reviewed immutable versions or commit references as appropriate.

## 6. Hands-on exercises

**Prerequisites:** Terraform 1.4+ in a disposable local directory. **Expected outcome:** call a local module and inspect its output without provisioning cloud resources.

1. Create the root and child files shown above.
2. Run `terraform init`, `terraform fmt`, `terraform validate`, and `terraform plan`.
3. Explain how the root supplies `name` and how the child exposes output.
4. Do not run `apply` unless intentionally practicing local state in an isolated directory; no AWS resources are involved in this example.

## 7. Real-world use cases

Standardized VPC or service patterns, repeated environment deployments, and organizational modules that encode reviewed defaults.

## 8. Best practices

- Design small, clear interfaces with typed inputs, validation, and documented outputs.
- Keep modules composable; avoid hidden provider credentials and excessive hardcoding.
- Pin remote source versions and review upgrades; test module changes in a disposable environment.
- Use secure defaults and make risky options explicit.
- Keep state ownership at the root/environment level and document resource ownership.

## 9. Common errors and troubleshooting

Missing required inputs, invalid source paths, incompatible provider requirements, and cyclic references are common failures. Run `terraform init` after changing module sources, then validate; inspect the dependency graph and module interface when output/input wiring is wrong.

## 10. Interview questions

1. **What is a Terraform module?** A collection of Terraform configuration files treated as a reusable unit.
2. **What is the root module?** The module in the directory where Terraform is run.
3. **How does a module receive configuration?** Through typed input variables.
4. **Why pin a module version?** To make upgrades deliberate and reduce unreviewed behavior changes.
5. **Where should state normally be owned?** By the root configuration/environment, not independently by reusable child modules.

## 11. Further learning

- [Terraform modules](https://developer.hashicorp.com/terraform/language/modules)
- [Module sources](https://developer.hashicorp.com/terraform/language/modules/sources)
- [Terraform Registry](https://registry.terraform.io/)

## 12. Progress checklist

- [ ] Explain root and child modules and their interfaces
- [ ] Build and call a local module
- [ ] Pin and review module source versions
- [ ] Explain composition, dependency graphs, and state ownership
