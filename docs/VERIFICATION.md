# Verification and Troubleshooting

This document records the general Linux diagnostics used to understand the running infrastructure.

It does not contain complete deployment instructions or production endpoint values.

## System information

```bash
hostname
grep -E '^(PRETTY_NAME|VERSION_ID)=' /etc/os-release
uname -r
uptime -p
free -h
df -h /
```

These commands were used to verify OS versions, kernel versions, uptime and host resources.

## Service state

```bash
systemctl status x-ui --no-pager -l
ps -ef | grep -E 'xray|sing-box' | grep -v grep
```

This helped distinguish:

- the Xray process started by 3x-ui;
- a separate Xray process;
- sing-box.

## Listening sockets

```bash
sudo ss -tulpn
sudo ss -lntup
```

Used to identify:

- listening TCP/UDP sockets;
- owning processes;
- local loopback services;
- publicly exposed services that require review.

## Active TCP sessions

```bash
sudo ss -ntp
```

Filtered output was used to trace specific processes and verify that both VPS nodes had matching active sessions.

Sensitive addresses and real endpoint values are intentionally omitted from the repository.

## Process-to-port mapping

```bash
sudo lsof -nP -iTCP:<port>
```

This was used to confirm which process owned each side of a local loopback connection.

## Routing

```bash
ip route
ip rule
```

These commands were used to inspect the routing table and policy-routing state of both VPS nodes.

## NAT inspection

```bash
sudo iptables -t nat -S
```

The observed inter-node path was handled by user-space networking processes rather than a custom Linux NAT forwarding chain.

## Runtime verification

The runtime inspection confirmed the following high-level relationship:

```text
Xray
  |
  v
local loopback
  |
  v
sing-box
  |
  v
remote VPS
```

The remote VPS simultaneously showed corresponding `ESTABLISHED` sessions.

This provided runtime evidence of communication between the two nodes without exposing working configuration data.

## Verification approach

The infrastructure was verified using the running system state rather than relying only on configuration files.

The investigation included:

- checking running processes;
- identifying listening ports;
- matching ports to processes;
- inspecting active TCP sessions;
- comparing socket state on both VPS nodes;
- inspecting Linux routing information;
- checking NAT rules;
- reviewing service state with systemd.

This approach helped reconstruct the actual runtime architecture of the infrastructure.

## Publication safety

Real production values are intentionally excluded.

The public repository does not contain:

- real VPS IP addresses;
- client UUIDs;
- passwords;
- tokens;
- SSH private keys;
- ready-to-use connection strings;
- client configuration files;
- administrative panel credentials.

The purpose of this document is to demonstrate Linux administration and network troubleshooting skills without exposing active infrastructure.
