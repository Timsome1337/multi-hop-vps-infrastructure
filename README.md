# Multi-Hop VPS Infrastructure

**Two-node Linux VPS infrastructure lab with Xray, sing-box, SSH and network diagnostics.**

🇷🇺 [Русская версия](#русская-версия) | 🇬🇧 [English version](#english-version)

---

# Русская версия

## О проекте

Это учебный инфраструктурный проект на двух VPS под управлением Ubuntu Server.

Проект создан как практическая работа по:

- администрированию Linux;
- работе с VPS и SSH;
- диагностике TCP-соединений;
- анализу процессов и сокетов;
- проверке маршрутизации;
- документированию многокомпонентной инфраструктуры.

Первый VPS используется как входной узел, второй — как внешний узел. Между ними организовано межузловое соединение через `sing-box`.

> В публичном репозитории не публикуются реальные IP-адреса, UUID, пароли, токены, клиентские конфигурации, строки подключения и другие данные доступа.

## Архитектура

```text
Client
  |
  v
+-----------------------------+
| VPS #1 — Entry node         |
| Ubuntu Server 22.04         |
|                             |
| Xray / Proxy-agent          |
|        |                    |
|        v                    |
| local loopback connection   |
|        |                    |
|     sing-box                |
+-------------+---------------+
              |
              | inter-node connection
              v
+-----------------------------+
| VPS #2 — External node      |
| Ubuntu Server 24.04         |
|                             |
| sing-box                    |
| Xray / 3x-ui                |
+-----------------------------+
```

Подробное описание архитектуры:

[`ARCHITECTURE.md`](ARCHITECTURE.md)

## Что было сделано

- арендованы и настроены два VPS;
- выполнено удалённое администрирование по SSH;
- использованы Ubuntu Server 22.04 и Ubuntu Server 24.04;
- развёрнуты `3x-ui`, `Xray` и `sing-box`;
- организовано межсерверное соединение между двумя узлами;
- проверены активные TCP-сессии с обеих сторон;
- исследованы локальные loopback-соединения между процессами;
- проанализированы слушающие TCP/UDP-порты;
- проверены процессы, маршруты и policy routing;
- проверена NAT-таблица;
- выполнена базовая диагностика ресурсов и состояния systemd-сервисов.

## Что удалось подтвердить

По состоянию работающей системы была подтверждена следующая высокоуровневая цепочка:

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

На втором VPS одновременно наблюдались соответствующие соединения в состоянии `ESTABLISHED`, обслуживаемые `sing-box`.

Это позволило подтвердить фактическое взаимодействие узлов без публикации рабочих конфигураций.

## Технологии

- Linux / Ubuntu Server
- VPS
- SSH
- Termius
- systemd
- Xray
- sing-box
- 3x-ui
- TCP / UDP
- loopback networking
- `ss`
- `lsof`
- `ip route`
- `ip rule`
- `iptables`
- базовый мониторинг Linux

## Диагностика

В ходе анализа использовались стандартные Linux-инструменты:

```bash
hostname
uname -r
uptime -p
free -h
df -h /
systemctl status x-ui
ps -ef
ss -tulpn
ss -ntp
lsof -nP -iTCP:<port>
ip route
ip rule
iptables -t nat -S
```

Подробное описание проверки инфраструктуры:

[`docs/VERIFICATION.md`](docs/VERIFICATION.md)

## Скриншоты

В папке [`screenshots`](screenshots/) находятся обезличенные скриншоты диагностики:

- `entry-system-info.png` — информация о входном VPS;
- `exit-system-info.png` — информация о внешнем VPS;
- `entry-loopback-chain.png` — локальное взаимодействие Xray и sing-box;
- `inter-node-connection.png` — обезличенное активное соединение между узлами.

Реальные публичные IP-адреса и рабочие endpoint-значения на скриншотах скрыты.

## Безопасность публикации

В репозиторий намеренно не включены:

- реальные IP-адреса VPS;
- UUID и клиентские идентификаторы;
- пароли;
- токены;
- SSH private keys;
- адреса административных панелей;
- реальные клиентские конфигурации;
- готовые строки подключения;
- приватные сертификаты и ключи.

Также в дальнейшем планируется усилить конфигурацию серверов:

- настроить host-based firewall;
- ограничить административные сервисы;
- использовать SSH-аутентификацию по ключам;
- отключить password authentication после проверки ключевого доступа;
- запретить прямой root-login по SSH;
- добавить Fail2ban;
- включить автоматические security updates;
- регулярно проверять список слушающих сервисов.

## Структура репозитория

```text
multi-hop-vps-infrastructure/
├── README.md
├── ARCHITECTURE.md
├── .gitignore
├── docs/
│   └── VERIFICATION.md
└── screenshots/
    ├── entry-system-info.png
    ├── exit-system-info.png
    ├── entry-loopback-chain.png
    └── inter-node-connection.png
```

## Что проект демонстрирует

Проект показывает практический опыт:

- работы с удалёнными Linux-серверами;
- администрирования VPS;
- использования SSH;
- диагностики процессов и сетевых соединений;
- анализа маршрутизации;
- работы с несколькими сетевыми сервисами;
- проверки runtime-состояния инфраструктуры;
- технического документирования;
- безопасной публикации проекта без раскрытия секретов.

---

# English version

## About

This is a small infrastructure lab built on two Ubuntu VPS servers.

The project was created as hands-on practice with:

- Linux administration;
- VPS and SSH;
- TCP connection diagnostics;
- process and socket analysis;
- routing inspection;
- infrastructure documentation.

The first VPS acts as an entry node, while the second VPS acts as an external node. An inter-node connection is handled through `sing-box`.

> The public repository intentionally excludes real IP addresses, UUIDs, credentials, tokens, client configurations, connection strings and other access data.

## Architecture

```text
Client
  |
  v
+-----------------------------+
| VPS #1 — Entry node         |
| Ubuntu Server 22.04         |
|                             |
| Xray / Proxy-agent          |
|        |                    |
|        v                    |
| local loopback connection   |
|        |                    |
|     sing-box                |
+-------------+---------------+
              |
              | inter-node connection
              v
+-----------------------------+
| VPS #2 — External node      |
| Ubuntu Server 24.04         |
|                             |
| sing-box                    |
| Xray / 3x-ui                |
+-----------------------------+
```

See:

[`ARCHITECTURE.md`](ARCHITECTURE.md)

## Implemented

- provisioned and configured two VPS servers;
- administered both systems remotely over SSH;
- used Ubuntu Server 22.04 and Ubuntu Server 24.04;
- deployed `3x-ui`, `Xray` and `sing-box`;
- established an inter-node connection between the two nodes;
- verified active TCP sessions on both systems;
- investigated local loopback communication between processes;
- inspected listening TCP/UDP sockets;
- inspected processes, routes and policy-routing state;
- checked the NAT table;
- performed basic resource and systemd service diagnostics.

## Verified runtime relationship

Runtime inspection confirmed the following high-level chain:

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

The second VPS simultaneously showed corresponding `ESTABLISHED` sessions handled by `sing-box`.

This provided runtime evidence of communication between the two nodes without exposing working configuration data.

## Technologies

- Linux / Ubuntu Server
- VPS
- SSH
- Termius
- systemd
- Xray
- sing-box
- 3x-ui
- TCP / UDP
- loopback networking
- `ss`
- `lsof`
- `ip route`
- `ip rule`
- `iptables`
- basic Linux monitoring

## Verification

Standard Linux tools were used during the investigation:

```bash
hostname
uname -r
uptime -p
free -h
df -h /
systemctl status x-ui
ps -ef
ss -tulpn
ss -ntp
lsof -nP -iTCP:<port>
ip route
ip rule
iptables -t nat -S
```

Detailed verification notes:

[`docs/VERIFICATION.md`](docs/VERIFICATION.md)

## Screenshots

The [`screenshots`](screenshots/) directory contains sanitized diagnostic screenshots:

- `entry-system-info.png` — entry VPS system information;
- `exit-system-info.png` — external VPS system information;
- `entry-loopback-chain.png` — local Xray-to-sing-box communication;
- `inter-node-connection.png` — sanitized active connection between the two nodes.

Real public IP addresses and working endpoint values are redacted.

## Publication safety

The repository intentionally excludes:

- real VPS IP addresses;
- client UUIDs and identifiers;
- passwords;
- tokens;
- SSH private keys;
- administration-panel addresses;
- real client configuration files;
- ready-to-use connection strings;
- private certificates and keys.

Planned hardening improvements include:

- configuring a host-based firewall;
- restricting administrative services;
- using SSH key authentication;
- disabling password authentication after key-based access is verified;
- disabling direct root SSH login;
- adding Fail2ban;
- enabling automatic security updates;
- regularly reviewing listening services.

## Repository structure

```text
multi-hop-vps-infrastructure/
├── README.md
├── ARCHITECTURE.md
├── .gitignore
├── docs/
│   └── VERIFICATION.md
└── screenshots/
    ├── entry-system-info.png
    ├── exit-system-info.png
    ├── entry-loopback-chain.png
    └── inter-node-connection.png
```

## What this project demonstrates

- remote Linux server administration;
- VPS management;
- SSH usage;
- process and network troubleshooting;
- routing analysis;
- multi-service Linux networking;
- runtime infrastructure verification;
- technical documentation;
- safe publication without exposing production secrets.