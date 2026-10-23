# AWS Budget Alerts with Terraform

A beginner Terraform project that creates an AWS monthly cost budget and sends
email notifications when actual or forecasted costs cross configured
thresholds.

## What this project creates

- One monthly cost budget (USD 100 by default).
- Email alerts at 80% and 100% of actual spending.
- An email alert when forecast spending exceeds 100% of the budget.

AWS Budgets notifications are not a spending cap. They do not stop services or
resources, and AWS cost data and notifications can be delayed. You must confirm
the email subscription sent by AWS before the address can receive alerts.

## Prerequisites

- Terraform 1.5 or later.
- AWS CLI configured with credentials for an account you are authorized to use.
- An IAM identity allowed to manage and view AWS Budgets.
- A monitored email address that you can verify.

Before applying, confirm which AWS identity is active:

```sh
aws sts get-caller-identity
```

Do not share or commit AWS credentials, account-specific Terraform state, or
your personal variable files.

## Configure and run

From this directory:

```sh
cp terraform.example.tfvars terraform.tfvars
```

Edit `terraform.tfvars` and set your alert email and desired monthly budget.
That local file is ignored by Git.

```sh
terraform init
terraform fmt -check
terraform validate
terraform plan
```

Review the plan, account identity, and region. Only apply if the planned change
is expected and you are authorized:

```sh
terraform apply
```

When AWS sends a subscription confirmation email, follow its verification link
and check that the subscriber is active in **Billing and Cost Management →
Budgets**.

## If the budget already exists

Terraform state is deliberately not included in this project. If you already
created a budget with the same name in this AWS account, do not apply this
configuration as a new resource. First initialize Terraform and configure your
local variables, then import the existing budget into this local state:

```sh
terraform import -var-file=terraform.tfvars \
  aws_budgets_budget.monthly_cost ACCOUNT_ID:monthly-cost-guardrail
terraform plan
```

Replace `ACCOUNT_ID` with the ID from your own AWS environment. Never put it
in this repository. Review the plan and make sure it does not propose replacing
or deleting the existing budget.

Terraform state is local for this learning project. Do not run independent
applies from multiple machines; use a properly secured remote backend before
collaborating on managed infrastructure.

## IAM permissions

The AWS identity used by Terraform needs the permissions required to create,
read, and inspect tags on the budget, including `budgets:ModifyBudget`,
`budgets:ViewBudget`, and `budgets:ListTagsForResource`. Ask an administrator
to grant only the permissions needed under your organization's policy.

## Files

- `main.tf` — AWS provider and budget resource.
- `variables.tf` — Region, monthly amount, and required alert email inputs.
- `terraform.example.tfvars` — Safe example values; copy locally before use.
- `.terraform.lock.hcl` — Locked provider selection and checksums.
