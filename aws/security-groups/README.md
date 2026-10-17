# AWS Security Groups

## 1. Introduction

Security Groups are stateful virtual firewalls associated with EC2 network interfaces and other supported resources. They control allowed network flows and are a key part of network least privilege.

## 2. Core concepts

- **Inbound rules** allow traffic into the associated resource; **outbound rules** allow traffic leaving it.
- Rules identify protocol, port or port range, and source (inbound) or destination (outbound), commonly a CIDR or another Security Group.
- Security Groups are stateful: response traffic for an allowed connection is tracked automatically.
- Rules are allow-based; multiple attached Security Groups' rules are aggregated.
- Security Groups do not replace IAM permissions, routing, Network ACLs, or host firewalls.

## 3. How it works

When traffic reaches an interface, AWS evaluates its attached Security Groups' rules. An inbound rule can allow TCP 443 from a trusted client range or a load balancer Security Group. Return traffic for an allowed flow is permitted by state tracking. A route must still exist, and the application must listen on the destination port.

## 4. Commands and examples

Read-only inspection:

```sh
aws ec2 describe-security-groups --region REGION
aws ec2 describe-network-interfaces --region REGION
ss -lnt   # On a Linux host: show listening TCP sockets
```

## 5. Configuration examples

Safe rule intent for a small web host:

| Direction | Protocol/port | Source or destination | Purpose |
|---|---|---|---|
| Inbound | TCP 22 | Your trusted public IPv4 `/32` only, or omit SSH and use managed access | Restricted administration |
| Inbound | TCP 443 | Intended client CIDRs or a load balancer Security Group | HTTPS |
| Inbound | TCP 80 | Only if required for redirect/validation; otherwise omit | Optional HTTP |

Do not open SSH, databases, or administrative ports to `0.0.0.0/0` or `::/0`. Prefer referencing the load balancer Security Group for application targets. AWS commonly creates default outbound allow rules; review and narrow egress to workload needs.

## 6. Hands-on exercises

**Prerequisites:** AWS authorization and an existing disposable test Security Group; no rule changes required. **Expected outcome:** identify which rules are necessary for a described connection.

1. Review an authorized Security Group and record inbound protocol, port, and source.
2. For a hypothetical HTTPS request, trace the route and check port 443, listener, host firewall, and response path.
3. Propose a narrower source for SSH than the entire internet. Apply changes only to a disposable resource after review and approval.

## 7. Real-world use cases

Restricting EC2 administration, allowing load balancer-to-application traffic, isolating database access to application tiers, and limiting service egress.

## 8. Best practices

- Permit only required ports and trusted sources; use Security Group references for tier-to-tier access.
- Separate groups by role/purpose, review rules periodically, and log ownership/rationale.
- Avoid unrestricted ingress to SSH, RDP, databases, and control planes.
- Pair Security Groups with private subnets, IAM controls, host firewalls, and monitoring.
- Test both network path and listener; do not use a broad rule as a troubleshooting shortcut.

## 9. Common errors and troubleshooting

For timeouts, check Security Group source CIDR, attached group, correct port/protocol, subnet route, Network ACL, public IP/DNS, and host firewall/listener. A refused connection often indicates that the destination is reachable but no process is listening. Also verify return routes and that the client is using its current public IP.

## 10. Interview questions

1. **Are Security Groups stateful?** Yes; return traffic for an allowed flow is tracked.
2. **Can a Security Group deny a specific source?** Rules are allow-based; narrow allowed sources rather than adding deny rules.
3. **What happens with multiple attached groups?** Their allow rules are combined.
4. **Does an open port guarantee connectivity?** No; routing, other filters, addressing, and a listening service must also be correct.
5. **How should SSH be exposed?** Prefer managed access; otherwise restrict the source to trusted addresses and use strong identity controls.

## 11. Further learning

- [Control traffic with Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)
- [Security Group rules](https://docs.aws.amazon.com/vpc/latest/userguide/security-group-rules.html)

## 12. Progress checklist

- [ ] Read inbound and outbound rule tuples accurately
- [ ] Explain stateful filtering and rule aggregation
- [ ] Design restricted SSH and HTTPS rules
- [ ] Troubleshoot a connection without opening broad access
