# DevOps Internship Learning Lab

A learning portfolio for DevOps, cloud infrastructure, and IT operations. The repository organizes study notes and safe, repeatable lab ideas for Docker, SSH, CI/CD, Kubernetes, AWS, DNS, and Terraform.

> **Experience statement:** This repository documents topics studied and exercises to perform. It does not by itself establish that every listed lab has been completed or that I have production operating experience. Add evidence, commands, and outcomes to a lab only after carrying it out.

## Learning objectives

- Package and operate applications using containers.
- Use SSH securely to administer remote Linux systems.
- Understand automated integration, delivery, and deployment workflows.
- Deploy and troubleshoot foundational Kubernetes resources.
- Explain AWS compute, network, security, and identity building blocks.
- Diagnose domain and DNS configuration.
- Describe Infrastructure as Code and manage infrastructure changes with Terraform.

## Technologies and topic guides

| Topic | Documentation |
|---|---|
| Docker and containers | [Docker guide](./docker/README.md) |
| SSH and remote access | [SSH guide](./ssh/README.md) |
| CI/CD | [CI/CD guide](./cicd/README.md) |
| Kubernetes | [Kubernetes guide](./kubernetes/README.md) |
| AWS overview | [AWS guide](./aws/README.md) |
| AWS EC2 | [EC2 guide](./aws/ec2/README.md) |
| AWS VPC and networking | [VPC guide](./aws/vpc/README.md) |
| AWS Security Groups | [Security Groups guide](./aws/security-groups/README.md) |
| AWS IAM | [IAM guide](./aws/iam/README.md) |
| Domains and DNS | [Domains and DNS guide](./aws/domains-dns/README.md) |
| Terraform and IaC | [Terraform guide](./terraform/README.md) |
| IaC fundamentals | [IaC fundamentals](./terraform/iac-fundamentals.md) |
| Imperative vs. declarative | [Approaches compared](./terraform/imperative-vs-declarative.md) |
| Providers and variables | [Providers and variables](./terraform/providers-variables.md) |
| State and backends | [Terraform state and backends](./terraform/state-and-backend.md) |
| Modules | [Terraform modules](./terraform/modules.md) |
| Existing coursework | [Lesson 1 — Core DevOps Concepts](./lesson-1-core-devops-concepts) |

## Repository structure

```text
DevOps_Internship_Picsart/
├── README.md
├── .gitignore
├── lesson-1-core-devops-concepts
├── docker/README.md
├── ssh/README.md
├── cicd/README.md
├── kubernetes/README.md
├── aws/
│   ├── README.md
│   ├── ec2/README.md
│   ├── vpc/README.md
│   ├── security-groups/README.md
│   ├── iam/README.md
│   └── domains-dns/README.md
└── terraform/
    ├── README.md
    ├── iac-fundamentals.md
    ├── imperative-vs-declarative.md
    ├── providers-variables.md
    ├── state-and-backend.md
    └── modules.md
```

## Recommended learning order

1. Review the existing [core DevOps lesson](./lesson-1-core-devops-concepts) and Linux command-line basics.
2. Learn [SSH](./ssh/README.md), then [Docker](./docker/README.md).
3. Study [CI/CD](./cicd/README.md) and [Kubernetes](./kubernetes/README.md).
4. Learn [domains and DNS](./aws/domains-dns/README.md), then AWS [EC2](./aws/ec2/README.md), [VPC](./aws/vpc/README.md), [Security Groups](./aws/security-groups/README.md), and [IAM](./aws/iam/README.md).
5. Study [IaC fundamentals](./terraform/iac-fundamentals.md), [Terraform](./terraform/README.md), and its guides on [providers and variables](./terraform/providers-variables.md), [state](./terraform/state-and-backend.md), and [modules](./terraform/modules.md).

## Setup and prerequisites

- A Linux, macOS, or WSL terminal and basic shell/Git familiarity.
- Install only the tools for a lab: Docker Engine or Docker Desktop; OpenSSH client; `kubectl` and a local cluster such as kind or minikube; AWS CLI; Terraform; and `dig`/`nslookup` where available.
- For AWS exercises, an authorized AWS account and configured billing alerts are required. Use a dedicated lab account where possible and understand charges before creating resources.
- Check each guide for lab-specific prerequisites. Version-dependent commands and provider syntax can change; consult the linked official documentation for the installed version.

## Using this lab

Read the concepts first, then complete an exercise only in a disposable local environment or an AWS account you are authorized to use. Record prerequisites, commands, expected results, observed results, cleanup, and any errors you resolved. Examples are teaching templates, not evidence that they were executed here. Do not run commands that create or destroy cloud resources without reviewing their effects and authorization.

## Practical project inventory

The repository currently contains the [Lesson 1 assignment](./lesson-1-core-devops-concepts). No application project, Dockerfile, CI/CD workflow, Kubernetes manifest, or Terraform configuration was present during this documentation update. Add links here as practical projects are added; do not treat examples in the guides as completed projects.

## Security and cost

- Never commit private keys, passwords, access keys, tokens, `.env` files, Terraform state, or secret variable files.
- Prefer short-lived credentials and IAM roles over long-lived AWS access keys.
- Restrict inbound access; do not expose SSH or administrative ports to the entire internet.
- Review Terraform plans, resource ownership, region, and estimated cost before applying changes.
- Terminate lab resources and remove unused volumes, addresses, snapshots, and gateways when finished; some resources incur charges while idle.

## Progress checklist

- [ ] Docker images, containers, volumes, networks, and Compose
- [ ] SSH keys, client configuration, and secure file transfer
- [ ] CI pipelines, artifacts, deployments, and rollback strategies
- [ ] Kubernetes workloads, Services, configuration, and troubleshooting
- [ ] EC2, VPC networking, Security Groups, and IAM
- [ ] Domain records and DNS diagnostics
- [ ] Terraform plans, variables, state, backends, and modules
- [ ] Complete and document at least one safe end-to-end lab

## What I plan to study next

- Build and document an end-to-end sample application delivery flow.
- Practice observability fundamentals: metrics, logs, alerts, and service objectives.
- Explore configuration management, Linux service operations, and incident response.
- Add verified lab evidence and small practical projects as they are completed.

## Official documentation

- [Docker documentation](https://docs.docker.com/)
- [OpenSSH manual pages](https://www.openssh.com/manual.html)
- [GitHub Actions documentation](https://docs.github.com/actions)
- [Kubernetes documentation](https://kubernetes.io/docs/)
- [Amazon EC2 documentation](https://docs.aws.amazon.com/ec2/)
- [Amazon VPC documentation](https://docs.aws.amazon.com/vpc/)
- [AWS IAM documentation](https://docs.aws.amazon.com/iam/)
- [Amazon Route 53 documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html)
- [Terraform documentation](https://developer.hashicorp.com/terraform/docs)
