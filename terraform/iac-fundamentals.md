# Infrastructure as Code Fundamentals

## 1. Introduction

IaC represents infrastructure in machine-readable definitions and workflows. It makes changes reviewable, repeatable, and easier to audit than undocumented manual configuration.

## 2. Core concepts

Configuration describes infrastructure; automation tools interpret it; APIs or agents make changes. IaC may be imperative (steps), declarative (desired state), or a combination. Terraform is primarily declarative. Related practices include version control, code review, testing, drift detection, and immutable infrastructure.

## 3. How it works

A change is authored, reviewed, validated, planned, approved, applied, and monitored for drift. Providers translate declarative configuration to API operations. State or another inventory mechanism tracks managed objects. The approach does not remove the need for access control, operational review, backups, or incident response.

## 4. Commands and examples

For Terraform command explanations and safety, see [Terraform guide](./README.md). A minimal workflow is:

```sh
terraform fmt -check
terraform validate
terraform plan
```

These commands require an initialized Terraform directory; `plan` can contact providers and read remote infrastructure. They are not a substitute for review.

## 5. Configuration examples

Example Terraform configuration expressing local desired data (it does not provision cloud infrastructure):

```hcl
resource "terraform_data" "lab_note" {
  input = {
    purpose = "learning"
    owner   = "replace-with-your-name"
  }
}
```

The Terraform workflow is: edit configuration -> review diff -> validate -> plan -> approve -> apply -> verify. Keep environment-specific inputs separate and credentials outside configuration. No deployed IaC project currently exists in this repository.

## 6. Hands-on exercises

**Prerequisites:** Git and a disposable local Terraform directory. **Expected outcome:** describe how a text change becomes an infrastructure plan without making a cloud change.

1. Review a sample Terraform configuration and identify inputs, desired resources, and outputs.
2. Make a harmless description/variable change and inspect `git diff`.
3. Run formatting, validation, and a plan in a local sandbox; explain every planned action.
4. Do not apply unless you have an owned, budgeted, disposable lab and an approved cleanup plan.

## 7. Real-world use cases

Provisioning cloud networks, standardizing environments, managing Kubernetes infrastructure, and making approved changes reproducible.

## 8. Best practices

- Treat infrastructure changes like software: review, test, document, and monitor them.
- Use least-privilege identities, explicit ownership, policy checks, and small change sets.
- Preserve backups and recovery paths; establish state and drift ownership.
- Separate reusable components from environment inputs; avoid secrets and machine-specific assumptions in source.

## 9. Common errors and troubleshooting

Unexpected differences may come from drift, changed provider defaults, wrong account/Region, unpinned dependencies, stale state, or incorrect variables. Confirm context and review plan details before any change.

## 10. Interview questions

1. **Why use IaC?** Repeatability, review, auditability, and reduced manual inconsistency.
2. **Does IaC guarantee no drift?** No; external/manual changes can create drift that must be detected and resolved.
3. **What should be reviewed before apply?** Identity, target account/Region, resource ownership, plan, impact, cost, and rollback/cleanup.
4. **How should secrets be handled?** Use a secret manager or approved ephemeral injection, with narrow access and no source-control exposure.
5. **Is all IaC declarative?** No; tools and workflows can be imperative, declarative, or hybrid.

## 11. Further learning

- [Terraform: What is Infrastructure as Code?](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code)
- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)

## 12. Progress checklist

- [ ] Explain IaC lifecycle and drift
- [ ] Review an IaC change and its plan
- [ ] Identify ownership, credential, state, and cost risks
- [ ] Document a verified lab separately from untested examples
