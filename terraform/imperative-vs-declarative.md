# Imperative and Declarative Infrastructure

## 1. Introduction

Imperative and declarative approaches describe different ways to express infrastructure changes. Understanding the difference helps select suitable automation and reason about repeatability.

## 2. Core concepts

- **Imperative:** specify the ordered actions to perform (the how).
- **Declarative:** specify the desired end state (the what); a controller/tool determines changes.
- **Idempotence:** repeating an operation leaves the system in the same intended state, an important property for safe automation.
- Many real systems combine approaches; declarative tools may invoke imperative provisioners or scripts.

## 3. How it works

An imperative script runs listed operations and must handle current state, retries, and partial failure. A declarative engine compares desired configuration with observed state and builds a change plan. Terraform is primarily declarative, but providers and scripts still execute ordered API operations underneath.

## 4. Commands and examples

These illustrate approach, not executable infrastructure commands:

```sh
# Imperative example: explicitly perform steps (pseudocode)
create_network
create_subnet
attach_gateway
```

```hcl
# Declarative Terraform example: describe a desired resource (illustrative only)
resource "terraform_data" "example" {
  input = "desired-value"
}
```

The Terraform resource example requires a compatible Terraform version and creates local state/resource metadata; it is not an AWS resource.

## 5. Configuration examples

In declarative configuration, dependencies can be inferred from references; explicit dependency declarations should be used only where a real dependency is otherwise invisible. In imperative scripts, encode error handling, retries, idempotence, and cleanup intentionally.

## 6. Hands-on exercises

**Prerequisites:** basic shell and Terraform knowledge. **Expected outcome:** explain how two different expressions represent the same target outcome.

1. Write pseudocode for creating a network imperatively.
2. Sketch declarative resource relationships without applying them.
3. Identify partial-failure and rerun behavior for both.
4. Compare how each approach detects drift and documents desired state.

## 7. Real-world use cases

Declarative tools fit infrastructure whose desired state should be continuously reviewed; imperative automation can suit one-off operational procedures or APIs lacking declarative support.

## 8. Best practices

- Prefer idempotent automation and explicit desired state for long-lived managed infrastructure.
- Make imperative scripts safe to rerun; validate inputs, fail clearly, and avoid hidden side effects.
- Avoid mixing unmanaged manual edits with declared ownership.
- Use review, testing, logging, and rollback strategies regardless of approach.

## 9. Common errors and troubleshooting

Repeated imperative runs may duplicate resources if not idempotent. Declarative plans may replace resources when immutable fields change or provider behavior differs. Inspect state, dependencies, actual remote state, and plan before acting.

## 10. Interview questions

1. **Imperative vs. declarative?** Imperative specifies steps; declarative specifies desired outcome.
2. **Which describes Terraform?** Primarily declarative.
3. **What is idempotence?** Repeating an operation has no additional unintended effect.
4. **Can declarative tools run imperative actions?** Yes, but imperative hooks can complicate ordering, reliability, and state.
5. **Which approach detects drift more naturally?** A reconciler/declarative workflow can compare actual and desired state, though detection depends on refresh and provider support.

## 11. Further learning

- [Terraform language overview](https://developer.hashicorp.com/terraform/language)
- [Terraform dependency graph](https://developer.hashicorp.com/terraform/internals/graph)

## 12. Progress checklist

- [ ] Define imperative, declarative, and idempotent
- [ ] Explain why Terraform is primarily declarative
- [ ] Compare retry, drift, and partial-failure handling
- [ ] Identify risks from hidden imperative steps
