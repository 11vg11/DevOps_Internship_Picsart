# AWS Identity and Access Management (IAM)

## 1. Introduction

IAM controls who can authenticate to AWS and which API actions they are authorized to perform. IAM is distinct from network controls: allowing an API action does not open a port, and a Security Group rule does not grant API permissions.

## 2. Core concepts

- **User:** an identity generally representing a person or legacy workload.
- **Group:** a way to attach common permissions to users; groups do not contain roles.
- **Role:** assumable identity that provides temporary credentials to an authorized principal or service.
- **Policy:** JSON statements describing allowed or denied actions, resources, and optional conditions.
- **Temporary credentials:** time-limited credentials issued through a role/session.
- **Instance profile:** EC2 attachment mechanism that makes an IAM role available to the instance.

## 3. How it works

An authenticated principal makes an AWS API request. AWS evaluates applicable identity/resource policies, explicit denies, permission boundaries, session policies, and organization controls. The request is authorized only if the evaluation allows it. EC2 applications obtain temporary role credentials from the instance metadata service instead of embedding access keys.

## 4. Commands and examples

```sh
aws sts get-caller-identity
aws iam list-attached-user-policies --user-name LAB_USER
aws iam simulate-principal-policy --policy-source-arn PRINCIPAL_ARN \
  --action-names s3:ListBucket --resource-arns BUCKET_ARN
```

These are inspection/simulation examples; callers still need authorization. Avoid recording account IDs, ARNs, or command output in public documentation.

## 5. Configuration examples

Illustrative policy limited to listing one bucket and reading objects in it; replace placeholders and validate policy intent before use:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::EXAMPLE_BUCKET"
    },
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::EXAMPLE_BUCKET/*"
    }
  ]
}
```

Use an EC2 role/instance profile for instance workloads. Do not save long-lived access keys in source code, user data, or images.

## 6. Hands-on exercises

**Prerequisites:** an authorized lab account, IAM policy evaluation familiarity, and no need to create credentials. **Expected outcome:** distinguish identity authorization from network reachability.

1. Use `get-caller-identity` to identify the active principal in your own account.
2. Read a sample policy and enumerate its actions and resource scope.
3. Simulate a permitted and a non-permitted action where authorized.
4. Diagram an EC2 role flow and contrast it with an inbound Security Group rule. Never create or share access keys for this exercise.

## 7. Real-world use cases

Human console/CLI access, service-to-service authorization, EC2 application access to AWS APIs, and cross-account delegation.

## 8. Best practices

- Prefer federation/Identity Center and roles with temporary credentials; avoid long-lived keys when roles or temporary credentials fit.
- Enforce MFA for human access, least privilege, and separate administrator and daily-use identities.
- Scope actions, resources, and conditions; review unused permissions and credential exposure.
- Protect the root user, avoid using it for daily work, and monitor CloudTrail.
- Rotate/revoke exposed credentials promptly; never place secrets in Git, logs, images, or user data.

## 9. Common errors and troubleshooting

For `AccessDenied`, verify active identity/account, action spelling, resource ARN, region, trust policy for role assumption, explicit denies, permission boundaries, session policies, and organization controls. Network timeouts are not IAM denials; test connectivity and authorization as separate layers.

## 10. Interview questions

1. **User vs. role?** A user is a persistent IAM identity; a role is assumed and supplies temporary credentials.
2. **What is least privilege?** Grant only the required actions, resources, and conditions for the required duration.
3. **Why not store access keys on EC2?** Long-lived secrets can leak; an instance role supplies rotating temporary credentials.
4. **Do IAM policies open ports?** No; network controls such as Security Groups determine traffic reachability.
5. **What does an explicit deny do?** It overrides an allow in policy evaluation.

## 11. Further learning

- [AWS IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)
- [IAM policy evaluation logic](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html)
- [IAM roles for EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html)

## 12. Progress checklist

- [ ] Distinguish users, groups, roles, policies, and instance profiles
- [ ] Read policy action/resource/condition scope
- [ ] Explain temporary credentials and role trust
- [ ] Separate IAM authorization from network controls
