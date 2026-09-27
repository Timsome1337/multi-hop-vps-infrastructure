# Security and Publication Safety

## Purpose

This repository documents a private Linux networking lab for portfolio purposes.

It is intentionally limited to architecture, diagnostics and hardening notes.

## Never publish

Do not commit:

```text
real server IP addresses
client UUIDs or identifiers
passwords
SSH private keys
API tokens
subscription URLs
3x-ui credentials
real client configuration files
ready-to-use connection strings
private certificates or keys
unredacted screenshots
```

## Current observations

During inspection of the running servers:

- host firewall protection was not yet consistently configured;
- on one node UFW was inactive;
- on the other node UFW was not installed;
- multiple services were listening on public interfaces;
- SSH was reachable on the standard port.

These are documented as hardening tasks rather than hidden.

## Hardening roadmap

1. Create and test a firewall policy before enabling it.
2. Preserve SSH access during firewall changes.
3. Allow only required service ports.
4. Use SSH key authentication.
5. Disable password authentication after key-based access is verified.
6. Disable direct root login and use a non-root sudo user.
7. Add Fail2ban where appropriate.
8. Enable unattended security updates.
9. Restrict access to administrative interfaces.
10. Review listening services periodically with:

```bash
ss -tulpn
```

11. Review authentication logs and active sessions.
12. Maintain encrypted backups of private configuration files.

## Public documentation boundary

The public repository should remain descriptive rather than operational.

It may show:

- OS versions;
- service status;
- sanitized process lists;
- sanitized socket state;
- high-level architecture;
- general-purpose Linux diagnostic commands.

It should not include:

- complete deployment instructions for a ready-to-use access service;
- production client profiles;
- working endpoint values;
- authentication material;
- step-by-step instructions for reaching restricted resources.

## Screenshot sanitization

Before adding screenshots to GitHub, redact:

- public IPv4/IPv6 addresses;
- provider-specific hostnames;
- client identifiers;
- management URLs;
- connection strings;
- any data that could be used to access a running service.

Localhost values such as `127.0.0.1` may remain visible because they are not externally routable.
