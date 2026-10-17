# Continuous Integration and Delivery

## 1. Introduction

CI/CD automates feedback and release workflows. **Continuous Integration (CI)** frequently integrates changes and runs checks. **Continuous Delivery** keeps changes releasable with deployment available on approval; **Continuous Deployment** automatically releases qualifying changes to production.

## 2. Core concepts

Typical stages include source checkout, dependency setup, lint/unit tests, build, security checks, artifact publication, deployment, and post-deployment verification. Artifacts are versioned outputs (for example, a container image) promoted between environments. Secrets are credentials injected securely at runtime.

## 3. How it works

A repository event triggers a runner. The pipeline checks out a revision, executes reproducible jobs, stores artifacts and test results, and gates deployment based on branch, approval, and environment policy. Rollback may redeploy a known-good artifact or revert a change; database changes need a compatible recovery plan.

## 4. Commands and examples

The following are local examples, not platform-specific pipeline syntax:

```sh
git status
docker build -t example-app:"$(git rev-parse --short HEAD)" .
docker run --rm example-app:"$(git rev-parse --short HEAD)" ./run-tests.sh
```

The run command assumes the image contains an executable `/`-relative `./run-tests.sh`; this repository has no application image or test script. Adapt it to a real project's test command. Avoid placing tokens directly in shell history or committed files.

## 5. Configuration example

Representative **GitHub Actions-specific** workflow syntax (`.github/workflows/ci.yml`):

```yaml
name: CI
on:
  pull_request:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    permissions:
      contents: read
    steps:
      - uses: actions/checkout@v4
      - name: Run project tests
        run: ./run-tests.sh
```

This is a template: a repository must provide `run-tests.sh` and an appropriate runner environment. Pin or review third-party actions according to organizational policy. Deployment syntax and secret names vary by platform and are intentionally omitted.

## 6. Hands-on exercises

**Prerequisites:** a Git repository and a harmless test command. **Expected outcome:** a pull request triggers a visible pass/fail CI check.

1. Add a CI workflow using your platform's official syntax.
2. Run a passing test, then introduce a temporary failing test and confirm the pipeline blocks success.
3. Restore the test, publish a non-production artifact, and document its version/digest.
4. Design (do not deploy) a staging-to-production approval and rollback path.

## 7. Real-world use cases

Fast feedback on pull requests, repeatable packaging, promotion of the same artifact across environments, and controlled releases with approvals and verification.

## 8. Best practices

- Keep jobs deterministic, fast, least-privileged, and separated by trust boundary.
- Use protected branches, required checks, code review, dependency/image scanning, and artifact provenance where supported.
- Store secrets in the CI platform's secret manager; mask output and avoid exposing secrets to untrusted fork builds.
- Build once and promote the same immutable artifact. Keep rollback artifacts and test backward-compatible migrations.
- Use staged/canary releases and health checks for risky changes; define who can approve production.

## 9. Common errors and troubleshooting

| Symptom | Checks |
|---|---|
| Workflow not triggered | YAML location/name, event filters, branch names, and platform permissions. |
| Works locally, fails in CI | Runtime/tool versions, working directory, environment variables, case-sensitive paths, and dependency lockfiles. |
| Secret is empty | Secret scope, environment approval, name spelling, and event restrictions. Never print it to debug. |
| Artifact missing | Upload/download paths, job dependencies, retention, and artifact name consistency. |
| Deployment fails | Credentials/permissions, target health, network reachability, and platform deployment logs. |

## 10. Interview questions

1. **CI vs. CD?** CI validates integrated changes; delivery keeps releases ready for approval, while deployment automatically releases them.
2. **What is an artifact?** A versioned build output that can be tested and promoted.
3. **Why build once?** It ensures the tested artifact is the one deployed, reducing environment drift.
4. **How should a pipeline handle secrets?** Use a secure secret store, least privilege, scoped access, and avoid logging values.
5. **How do you roll back?** Restore a known-good compatible artifact/configuration, verify health, and handle data/schema changes deliberately.

## 11. Further learning

- [GitHub Actions documentation](https://docs.github.com/actions)
- [GitLab CI/CD documentation](https://docs.gitlab.com/ci/)
- [Google Cloud DORA research](https://cloud.google.com/devops/state-of-devops)

## 12. Progress checklist

- [ ] Distinguish CI, Continuous Delivery, and Continuous Deployment
- [ ] Create a pull-request pipeline with a real automated test
- [ ] Explain artifacts, secrets, approvals, and rollback
- [ ] Diagnose a failed job from runner logs without leaking credentials
