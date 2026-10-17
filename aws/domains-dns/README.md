# Domains and DNS

## 1. Introduction

A domain name is a human-readable name registered through a registrar. The Domain Name System (DNS) publishes records that let clients discover service endpoints. DNS resolution does not itself create a service or authorize access.

## 2. Core concepts

| Record | Purpose |
|---|---|
| A | Maps a name to an IPv4 address |
| AAAA | Maps a name to an IPv6 address |
| CNAME | Aliases a name to another DNS name; generally not used at a zone apex, subject to provider-specific alias features |
| MX | Lists mail exchangers and preference |
| TXT | Publishes text, commonly for domain verification and email policy |
| NS | Identifies authoritative nameservers for a zone |

**TTL** controls how long resolvers may cache an answer. Registrar, authoritative nameservers, DNS hosting, and record ownership are related but distinct responsibilities.

## 3. How it works

A resolver checks its cache and, if needed, follows DNS delegation from root to TLD to authoritative nameservers. It retrieves the requested record and caches it for the TTL. Updates can appear inconsistent until caches expire; propagation is not a single global switch. The returned IP must still route to a running service with appropriate network rules and TLS configuration.

## 4. Commands and examples

```sh
dig example.com A
dig example.com AAAA
dig example.com MX
dig example.com NS
dig +short www.example.com CNAME
dig @1.1.1.1 example.com A
nslookup example.com
```

Querying a specific resolver can help compare results; use resolvers permitted by your network policy. Avoid placing private/internal DNS results in public issue reports.

## 5. Configuration examples

Illustrative zone records (documentation example, not a live domain):

```dns
@      3600 IN A     203.0.113.20
www    3600 IN CNAME example.com.
@      3600 IN MX 10 mail.example.com.
@      3600 IN TXT   "verification-token-provided-by-service"
```

`203.0.113.0/24` is reserved for documentation; replace all example values in a controlled DNS console. Actual syntax and apex alias support depend on DNS provider.

## 6. Hands-on exercises

**Prerequisites:** `dig` or `nslookup`; a public domain is optional. **Expected outcome:** identify authoritative nameservers and compare cached answers without modifying a zone.

1. Query A, AAAA, MX, TXT, and NS records for a domain you are allowed to inspect.
2. Compare answers from the system resolver and one permitted public resolver.
3. Record TTL and explain why an update might not be visible immediately.
4. If practicing a change, use a test subdomain you control and record the prior value and rollback plan.

## 7. Real-world use cases

Routing web traffic to a load balancer/CDN, mail delivery, certificate/domain verification, and service discovery.

## 8. Best practices

- Enable registrar account MFA and domain/transfer locks; renew domains and maintain accurate ownership contacts.
- Keep DNS records minimal and review old entries; lower TTL before planned migrations where appropriate, then restore a sensible TTL.
- Use SPF, DKIM, and DMARC for email according to the mail provider's instructions.
- Protect DNS management credentials; use DNSSEC where supported and operationally appropriate.
- Never assume DNS changes make an application secure or reachable; verify routes, TLS, and service health independently.

## 9. Common errors and troubleshooting

Check spelling, record type/name, authoritative nameserver delegation, TTL/cache, CNAME conflicts, and whether the target address is current. Then verify that the IP is routable, firewall rules permit traffic, the process listens, and TLS certificate covers the hostname. `NXDOMAIN` means no name exists at that queried location; resolver differences may indicate delegation or caching issues.

## 10. Interview questions

1. **A vs. CNAME?** A maps to IPv4; CNAME aliases a DNS name to another name.
2. **What does TTL mean?** The period a DNS answer may be cached before re-query.
3. **What are NS records for?** They identify authoritative nameservers for a zone/delegation.
4. **Does DNS propagation happen instantly?** No; resolver caches and delegation state can cause delay.
5. **Does a correct A record guarantee the website works?** No; routing, firewall, server, and TLS must also work.

## 11. Further learning

- [ICANN domain name basics](https://www.icann.org/resources/pages/domain-name-basics-2014-03-03-en)
- [Amazon Route 53 Developer Guide](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html)
- [`dig` manual](https://bind9.readthedocs.io/en/latest/manpages.html#dig-dns-lookup-utility)

## 12. Progress checklist

- [ ] Explain registration, delegation, nameservers, records, and TTL
- [ ] Query common DNS record types with `dig` or `nslookup`
- [ ] Troubleshoot resolver/cache differences
- [ ] Trace a domain name through to a cloud service endpoint
