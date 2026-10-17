# Amazon EC2

## 1. Introduction

Amazon Elastic Compute Cloud (EC2) provides virtual machine instances with configurable CPU, memory, networking, and storage. Engineers use it when they need control over an operating system or application runtime.

## 2. Core concepts

- **AMI:** machine image with an operating system and optional software.
- **Instance type:** CPU, memory, network, and accelerator profile.
- **Key pair:** SSH authentication material; protect the private key.
- **EBS:** persistent block storage; instance-store disks are ephemeral.
- **User data:** launch-time initialization input; treat it as code and never put secrets in it.
- **Public/private IP:** public addressing enables internet routing when network paths permit; private addresses are used inside connected networks.
- **Instance states:** pending, running, stopping, stopped, shutting-down, terminated.

## 3. How it works

An instance is launched in a Region and Availability Zone, attached to a subnet/network interface and Security Groups, and assigned selected storage and identity. The subnet route table and addresses determine paths; Security Groups and host firewalls further filter traffic. CloudWatch can collect metrics and alarms.

## 4. Commands and examples

Read-only inspection:

```sh
aws ec2 describe-instances --region REGION
aws ec2 describe-instance-status --instance-ids INSTANCE_ID --region REGION
ssh -i ~/.ssh/lab_ed25519 ec2-user@PUBLIC_DNS_NAME
```

Use the login user documented for the selected AMI (`ec2-user` is common for Amazon Linux; others differ). The SSH command requires authorized access and a reachable host. Do not put a real instance ID or hostname into public notes.

## 5. Configuration example

Illustrative Amazon Linux user data (validate package names and behavior for the chosen AMI; not tested here):

```bash
#!/bin/bash
dnf install -y httpd
systemctl enable --now httpd
printf '%s\n' 'Temporary lab page' > /var/www/html/index.html
```

User data runs with elevated privileges during initialization. Keep it idempotent where possible, log appropriately, and never embed credentials.

## 6. Hands-on exercises

**Prerequisites:** authorized AWS account, budget alert, a supported low-cost test AMI/instance type, and an existing or planned VPC/Security Group. **Expected outcome:** a tagged test instance accessible only from your trusted client address.

1. In the AWS console, verify account and Region; select an AMI and small eligible instance type.
2. Choose a private subnet for internal-only work, or a public subnet only if direct inbound access is required. Assign an IAM role rather than access keys.
3. Use a dedicated key pair and a Security Group that permits SSH only from your current trusted public IP (`/32` for IPv4), or use a managed session access method.
4. Review storage, public address, and estimated costs before launch. Verify connectivity and monitoring.
5. Terminate the instance after the lab and check for separately billable storage, snapshots, and addresses. Do not execute cloud-changing commands from this guide.

## 7. Real-world use cases

Legacy application hosting, custom operating system workloads, build agents, controlled compute jobs, and self-managed services.

## 8. Best practices

- Prefer private access via Systems Manager or a bastion/VPN; do not expose SSH to `0.0.0.0/0`.
- Patch the OS, use IMDSv2 where supported, attach least-privilege instance roles, encrypt EBS, and enable backups/monitoring.
- Tag owner, purpose, and expiry; stop/terminate unused instances and account for storage/public address costs.
- Use launch templates and IaC for repeatability; review user data as privileged code.

## 9. Common errors and troubleshooting

Check instance state and status checks, correct AMI username/key, subnet routes, public IP/DNS, Security Group source/port, Network ACL, host firewall, and `sshd` health. A running instance is not necessarily reachable from the internet.

## 10. Interview questions

1. **What is an AMI?** A template used to launch instances.
2. **What is the difference between stop and terminate?** Stop retains a restartable instance and usually EBS data; terminate removes the instance and may delete configured root storage.
3. **Does a public IP guarantee access?** No; routes, Security Groups, Network ACLs, host firewall, and service listener must also allow it.
4. **How should an instance access AWS APIs?** Use an attached IAM role/instance profile with narrowly scoped permissions.
5. **What is user data?** Launch-time initialization input; it can be privileged and should contain no secrets.

## 11. Further learning

- [Amazon EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html)
- [EC2 instance types](https://aws.amazon.com/ec2/instance-types/)
- [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/)

## 12. Progress checklist

- [ ] Explain AMIs, instance types, storage, user data, and instance states
- [ ] Trace EC2 network access through VPC routes and Security Groups
- [ ] Plan a restricted test launch and cleanup
- [ ] Explain IAM instance profiles, monitoring, and cost controls
