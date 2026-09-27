# Multi-Hop VPS Infrastructure

**Two-node Linux infrastructure lab built on VPS servers with Xray, sing-box and 3x-ui.**

🇷🇺 [Русская версия](#русская-версия) | 🇬🇧 [English version](#english-version)

---

# Русская версия

## О проекте

Это учебный инфраструктурный проект, в котором развёрнута двухузловая схема на двух VPS под управлением Ubuntu Server.

Проект используется как практическая работа по администрированию Linux, VPS, SSH, сетевым сервисам, анализу TCP-соединений и диагностике взаимодействия между пользовательскими сетевыми процессами.

Первый VPS используется как входной узел лабораторной схемы, второй — как внешний узел. Между серверами организовано отдельное межузловое соединение через `sing-box`.

> Репозиторий носит демонстрационный и учебный характер. В нём не публикуются реальные IP-адреса, UUID, ключи, пароли, токены, клиентские конфигурации, ссылки подключения и иные данные доступа.

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

Подробное описание: [`ARCHITECTURE.md`](ARCHITECTURE.md)

## Что было сделано

- арендованы и настроены два VPS;
- выполнено удалённое администрирование серверов по SSH;
- использованы Ubuntu Server 22.04 и Ubuntu Server 24.04;
- развёрнуты `3x-ui`, `Xray` и `sing-box`;
- организовано межсерверное соединение между двумя узлами;
- проверены активные TCP-соединения между VPS;
- исследованы локальные соединения между Xray и sing-box;
- проверены таблицы маршрутизации и policy routing;
- проанализированы слушающие TCP/UDP-порты и процессы;
- выполнена базовая диагностика ресурсов и состояния systemd-сервисов;
- подтверждена работа межузловой связи по состоянию активных сокетов с обеих сторон.

## Что удалось подтвердить диагностикой

На входном узле была подтверждена цепочка:

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

На втором VPS одновременно наблюдались соответствующие `ESTABLISHED`-соединения, обслуживаемые `sing-box`.

Это позволило проверить фактическое взаимодействие узлов без публикации рабочих конфигураций.

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

В проекте использовались стандартные инструменты Linux:

```bash
hostname
uname -r
uptime -p
free -h
df -h /
systemctl status x-ui
ss -tulpn
ss -ntp
ip route
ip rule
iptables -t nat -S
lsof -nP -iTCP
ps -ef
```

Подробнее: [`docs/VERIFICATION.md`](docs/VERIFICATION.md)

## Безопасность публикации

В публичной версии намеренно отсутствуют:

- реальные IP-адреса серверов;
- UUID и другие идентификаторы клиентов;
- пароли и токены;
- SSH-ключи;
- адреса административных панелей;
- реальные клиентские конфигурации;
- готовые строки подключения;
- приватные сертификаты и ключи.

Репозиторий не содержит готовых конфигураций для подключения и не предназначен как инструкция по получению доступа к каким-либо ограниченным ресурсам.

Подробнее: [`SECURITY.md`](SECURITY.md)

## Структура репозитория

```text
multi-hop-vps-infrastructure/
├── README.md
├── ARCHITECTURE.md
├── SECURITY.md
├── .gitignore
├── docs/
│   └── VERIFICATION.md
└── screenshots/
    ├── README.md
    ├── entry-system-info.png
    ├── exit-system-info.png
    ├── entry-loopback-chain.png
    └── inter-node-connection.png
```

## Что проект показывает

Проект демонстрирует практический опыт:

- работы с удалёнными Linux-серверами;
- администрирования VPS;
- работы с SSH;
- диагностики процессов и сетевых соединений;
- анализа маршрутизации;
- работы с несколькими сетевыми сервисами;
- документирования двухузловой инфраструктуры;
- безопасной публикации технической документации без раскрытия рабочих секретов.

## Планы развития

- настроить host-based firewall;
- ограничить административные сервисы;
- перейти на SSH-ключи и отключить парольный вход;
- запретить прямой root-login по SSH;
- добавить Fail2ban;
- настроить автоматические security updates;
- добавить базовый мониторинг и уведомления;
- документировать резервное копирование конфигураций;
- регулярно проверять список слушающих сервисов.

---

# English version

## About

This is a small infrastructure lab implementing a two-node setup on two Ubuntu VPS servers.

The project is intended as hands-on practice with Linux administration, VPS hosting, SSH, network services, TCP session analysis and troubleshooting of interactions between user-space networking processes.

The first VPS acts as an entry node in the lab topology, while the second VPS acts as an external node. A dedicated inter-node connection is handled through `sing-box`.

> The repository is for portfolio and educational documentation only. It does not publish real IP addresses, UUIDs, credentials, tokens, client configuration files, connection strings or other access data.

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

See [`ARCHITECTURE.md`](ARCHITECTURE.md).

## Implemented

- provisioned and configured two VPS servers;
- administered both systems remotely over SSH;
- used Ubuntu Server 22.04 and Ubuntu Server 24.04;
- deployed `3x-ui`, `Xray` and `sing-box`;
- established an inter-node connection between the two VPS nodes;
- verified active TCP sessions on both systems;
- investigated local Xray-to-sing-box communication;
- inspected routing and policy-routing state;
- inspected listening TCP/UDP sockets and owning processes;
- checked host resources and systemd service health;
- verified inter-node connectivity from live socket state on both sides.

## Verified runtime path

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

The second VPS simultaneously showed matching `ESTABLISHED` sessions handled by `sing-box`.

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

See [`docs/VERIFICATION.md`](docs/VERIFICATION.md).

## Publication safety

The public repository intentionally excludes:

- real server IP addresses;
- client UUIDs and identifiers;
- passwords and tokens;
- SSH private keys;
- administration-panel addresses;
- real client configuration files;
- ready-to-use connection strings;
- private certificates and keys.

The repository does not provide ready-to-use connection profiles and is not intended as a guide for accessing restricted resources.

See [`SECURITY.md`](SECURITY.md).

## Roadmap

- configure a host-based firewall;
- restrict administrative services;
- use SSH key authentication and disable password authentication;
- disable direct root SSH login;
- add Fail2ban;
- enable automatic security updates;
- add basic monitoring and alerting;
- document configuration backups;
- periodically audit listening services.
