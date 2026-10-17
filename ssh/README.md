# SSH and Remote Server Access

## 1. Introduction

SSH (Secure Shell) provides encrypted remote command execution and file transfer. It is a standard administrative channel for Linux systems and should be protected as privileged access.

## 2. Core concepts

- A **key pair** has a private key kept secret by its owner and a public key installed for verification.
- With public-key authentication, the client proves possession of the private key; the private key is not sent to the server.
- The server also has host keys. Verify the server identity fingerprint to reduce man-in-the-middle risk.
- `ssh-agent` can hold unlocked key material for a session; it does not replace protecting the key.

## 3. How it works

The client connects to a server (usually TCP port 22), negotiates encryption, verifies the host key, then authenticates the user. The server checks authorized public keys and account policy before opening a shell, command, or forwarding channel.

## 4. Commands and examples

```sh
ssh-keygen -t ed25519 -C "lab-access"       # Generate a key pair; protect it with a passphrase
ssh -i ~/.ssh/lab_ed25519 user@203.0.113.10 # Connect; replace placeholders with authorized values
ssh user@lab.example.com
scp ./notes.txt user@lab.example.com:~/     # Copy a file over SSH
scp user@lab.example.com:~/result.txt ./
ssh -v user@lab.example.com                 # Show connection diagnostics
```

For directory synchronization, `rsync -av -e ssh ./dir/ user@host:~/dir/` is useful; review the destination carefully before using deletion options.

## 5. Configuration example

Example `~/.ssh/config`:

```sshconfig
Host lab
    HostName lab.example.com
    User labuser
    IdentityFile ~/.ssh/lab_ed25519
    IdentitiesOnly yes
```

Protect the private key with restrictive permissions (typically `chmod 600 ~/.ssh/lab_ed25519`) and the SSH directory with `chmod 700 ~/.ssh`. Install only the public key on a system you administer. Do not put private keys in this repository.

## 6. Hands-on exercises

**Prerequisites:** OpenSSH client and access to a local VM/container or authorized test host. **Expected outcome:** key-authenticated login without exposing a password or key.

1. Generate a passphrase-protected key pair and inspect file names (never print or share the private-key contents).
2. Install the public key using your platform's documented method; connect with `ssh -i`.
3. Add a host alias to the SSH config and connect with the alias.
4. Copy a harmless test file with `scp`, then verify it remotely. Remove only the test file you created.

## 7. Real-world use cases

Remote Linux administration, secure file transfer, automation access to hosts, and controlled tunnels to private services.

## 8. Best practices

- Keep private keys private, encrypted with a passphrase where practical, backed up securely, and rotated/revoked when exposure is suspected.
- Verify host keys through a trusted channel; do not blindly accept unexpected key changes.
- Use individual accounts, least privilege, MFA/bastions where available, and centralized audit controls.
- Restrict network reachability to SSH; disable password/root login only after confirming a working alternative and recovery path.
- Avoid forwarding agents or ports unless needed and understood.

## 9. Common errors and troubleshooting

| Error | Checks |
|---|---|
| `Permission denied (publickey)` | Correct username/key, server authorized key, account status, and key file permissions. |
| Connection timeout | DNS/IP, route, firewall/Security Group, server state, and SSH daemon. |
| Host key changed warning | Verify the change out of band before updating `known_hosts`; do not suppress the warning. |
| Too many authentication failures | Use `IdentitiesOnly yes` and the intended `IdentityFile`. |
| Key ignored | Check ownership and restrictive permissions on `.ssh` and key files. |

## 10. Interview questions

1. **What is the difference between a public and private key?** The public key is distributed for verification; the private key proves identity and must remain secret.
2. **What does the host key protect?** It lets a client recognize the server and detect an unexpected identity change.
3. **What does `scp` use?** SSH transport to securely copy files.
4. **Why use an SSH config alias?** It centralizes host, user, and identity settings and reduces command-line mistakes.
5. **Why is opening SSH to all addresses risky?** It increases exposure to scanning and attacks; use restricted sources and stronger access controls.

## 11. Further learning

- [OpenSSH manual pages](https://www.openssh.com/manual.html)
- [`ssh_config` manual](https://man.openbsd.org/ssh_config)
- [`ssh-keygen` manual](https://man.openbsd.org/ssh-keygen)

## 12. Progress checklist

- [ ] Explain key-pair authentication and host-key verification
- [ ] Generate and protect a key pair without exposing the private key
- [ ] Configure a host alias and connect to an authorized host
- [ ] Transfer a file and troubleshoot permissions/connectivity
