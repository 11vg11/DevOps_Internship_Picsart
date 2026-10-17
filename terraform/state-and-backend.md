# Terraform State and Backends

## 1. Introduction

Terraform state records the relationship between configuration addresses and real infrastructure objects, along with metadata and values needed for future plans. A backend determines where and how state is stored.

## 2. Core concepts

- **Local state:** stored in the working directory by default; useful for isolated learning, unsafe to share casually.
- **Remote backend:** stores state centrally for collaboration and can support locking.
- **Locking:** prevents concurrent writers from corrupting shared state.
- State can include sensitive attributes even when outputs are marked sensitive.
- State addresses, imports, and moves preserve Terraform's mapping to real objects.

## 3. How it works

Terraform reads state before planning, refreshes known objects when supported, computes changes, then writes updated state after operations. A remote backend coordinates storage; backend initialization is separate from provider configuration. Backend credentials should come from an approved credential chain, not HCL literals.

## 4. Commands and examples

```sh
terraform state list
terraform state show RESOURCE_ADDRESS
terraform init -migrate-state
```

State inspection may reveal secrets. `init -migrate-state` can move state; run it only as part of a reviewed migration with backup and recovery planning. Never force-unlock unless the lock is confirmed stale and you own the operation.

## 5. Configuration examples

Illustrative S3 backend configuration (create and secure the bucket/table through an approved process before use):

```hcl
terraform {
  backend "s3" {
    bucket       = "REPLACE_WITH_OWNED_STATE_BUCKET"
    key          = "learning/lab.tfstate"
    region       = "REPLACE_WITH_REGION"
    encrypt      = true
    use_lockfile = true
  }
}
```

Backend syntax and locking support depend on Terraform version; check current S3 backend documentation. Configure versioning, encryption, access controls, recovery, and audit separately. Do not put backend credentials in this block.

## 6. Hands-on exercises

**Prerequisites:** local Terraform only; do not configure a real remote backend without an owned bucket and approval. **Expected outcome:** identify state files and explain their sensitivity.

1. In a disposable local directory, initialize a no-resource configuration.
2. Inspect generated files without publishing or sharing state contents.
3. Use `terraform state list` and explain why an empty result is expected when no resources are managed.
4. Review a remote backend design on paper, covering lock, backup, encryption, access, and recovery. Do not migrate or upload real state for this exercise.

## 7. Real-world use cases

Sharing infrastructure ownership across a team, coordinating CI runs, retaining state backups, and enforcing controlled access to infrastructure metadata.

## 8. Best practices

- Protect state and plan files as sensitive data; encrypt in transit/at rest and restrict access.
- Use locking, versioning/backups, audit logging, and a documented recovery process.
- Separate state by environment/ownership boundary; avoid concurrent manual/CI applies.
- Keep state out of Git and public artifacts; do not edit state JSON by hand.
- Use `terraform state mv`/imports only with a reviewed mapping and backup.

## 9. Common errors and troubleshooting

For lock errors, find the active operation before considering recovery. For missing resources, verify workspace/backend/key and credentials. For state drift or accidental loss, stop changes, preserve backups, inspect remote objects, and follow import/recovery procedures rather than recreating resources blindly.

## 10. Interview questions

1. **Why does Terraform need state?** To map configuration to real objects and track managed attributes.
2. **Why is state sensitive?** It can store passwords, tokens, identifiers, and other resource attributes.
3. **Why use a remote backend?** Shared access, centralized controls, and often locking/backup support.
4. **What is state locking?** A mechanism that prevents concurrent state-writing operations.
5. **Can state be safely deleted to fix a plan?** No; that can orphan resources and lead to unintended recreation.

## 11. Further learning

- [Terraform state](https://developer.hashicorp.com/terraform/language/state)
- [Backend configuration](https://developer.hashicorp.com/terraform/language/backend)
- [S3 backend](https://developer.hashicorp.com/terraform/language/backend/s3)

## 12. Progress checklist

- [ ] Explain state mapping, refresh, and backend roles
- [ ] Identify local state and plan files as sensitive
- [ ] Describe remote locking, encryption, backup, and recovery
- [ ] Explain safe state migration and lock troubleshooting
