# Architecture

## Scope

This document describes only the high-level topology and runtime relationships that were verified through standard Linux diagnostics.

It intentionally omits production addresses, credentials, client identifiers and ready-to-use connection data.

## Overview

```text
Client
  |
  v
Entry VPS
  |
  | Xray -> local loopback -> sing-box
  |
  v
Inter-node connection
  |
  v
External VPS
```

## Node 1 — Entry VPS

Observed environment:

- Ubuntu Server 22.04 LTS;
- Xray is running;
- sing-box is running;
- 3x-ui is installed and active;
- Xray and sing-box communicate through a local loopback connection;
- sing-box maintains active sessions to the second VPS.

The public repository intentionally omits real ports and addresses.

## Node 2 — External VPS

Observed environment:

- Ubuntu Server 24.04 LTS;
- sing-box is running;
- Xray and 3x-ui are also present;
- the node receives active sessions from the entry VPS;
- the inter-node sessions were verified with `ss`.

## Verified runtime chain

```text
Xray on Entry VPS
      |
      v
127.0.0.1:<local relay port>
      |
      v
sing-box on Entry VPS
      |
      v
External VPS:<inter-node port>
      |
      v
sing-box on External VPS
```

The exact client-facing path, working endpoint values and production configuration are intentionally excluded.

## Note about 3x-ui

3x-ui/Xray is active on the systems, but the observed inter-node transport was handled by a separate Xray/sing-box chain.

For that reason, this repository does not claim that 3x-ui itself directly forwards traffic between the two VPS nodes.

## Publication note

This document is intended to demonstrate Linux administration and network troubleshooting skills. It is not a deployment guide and does not include complete commands or configuration files required to reproduce a working access service.
