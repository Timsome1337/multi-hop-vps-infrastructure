# Проверка и диагностика / Verification and Troubleshooting

---

# Русская версия

Этот документ описывает общую диагностику Linux, которая использовалась для анализа работающей инфраструктуры.

Документ не содержит полных инструкций по развёртыванию, рабочих адресов узлов или других производственных параметров.

## Информация о системе

```bash
hostname
grep -E '^(PRETTY_NAME|VERSION_ID)=' /etc/os-release
uname -r
uptime -p
free -h
df -h /
```

Эти команды использовались для проверки:

- версии операционной системы;
- версии ядра Linux;
- времени непрерывной работы сервера;
- использования оперативной памяти;
- использования дискового пространства.

## Состояние сервисов

```bash
systemctl status x-ui --no-pager -l
ps -ef | grep -E 'xray|sing-box' | grep -v grep
```

Это позволило различить:

- процесс Xray, запущенный через 3x-ui;
- отдельный процесс Xray;
- процесс sing-box.

## Прослушиваемые сокеты

```bash
sudo ss -tulpn
sudo ss -lntup
```

Команды использовались для определения:

- TCP- и UDP-портов в состоянии LISTEN;
- процессов, которым принадлежат сокеты;
- локальных loopback-сервисов;
- сервисов, доступных на сетевых интерфейсах.

## Активные TCP-сессии

```bash
sudo ss -ntp
```

Фильтрация вывода использовалась для отслеживания отдельных процессов и проверки наличия соответствующих активных соединений на обоих VPS.

Реальные IP-адреса и рабочие endpoint-значения в публичном репозитории намеренно не публикуются.

## Сопоставление процесса и порта

```bash
sudo lsof -nP -iTCP:<port>
```

Команда использовалась для определения процесса, которому принадлежит конкретное TCP-соединение или локальный порт.

## Маршрутизация

```bash
ip route
ip rule
```

Команды использовались для проверки:

- основной таблицы маршрутизации;
- policy routing;
- сетевых интерфейсов и маршрутов между ними.

## Проверка NAT

```bash
sudo iptables -t nat -S
```

Проверка показала, что наблюдаемая межузловая связь не была реализована через пользовательскую цепочку NAT в iptables.

Основная передача между процессами выполнялась пользовательскими сетевыми сервисами.

## Проверка работающей цепочки

По состоянию активных соединений была подтверждена следующая высокоуровневая схема:

```text
Xray
  |
  v
локальное loopback-соединение
  |
  v
sing-box
  |
  v
удалённый VPS
```

На втором VPS одновременно наблюдались соответствующие соединения в состоянии `ESTABLISHED`.

Это позволило подтвердить фактическое взаимодействие узлов без публикации рабочих конфигураций.

## Подход к диагностике

Инфраструктура анализировалась по фактическому состоянию работающей системы, а не только по конфигурационным файлам.

В ходе диагностики выполнялись:

- проверка работающих процессов;
- определение прослушиваемых портов;
- сопоставление портов и процессов;
- анализ активных TCP-сессий;
- сравнение сетевого состояния на обоих VPS;
- анализ таблиц маршрутизации;
- проверка NAT;
- проверка systemd-сервисов.

Такой подход позволил восстановить реальную runtime-архитектуру инфраструктуры.

## Безопасность публикации

В публичной версии проекта намеренно отсутствуют:

- реальные IP-адреса VPS;
- UUID и другие идентификаторы клиентов;
- пароли;
- токены;
- приватные SSH-ключи;
- готовые строки подключения;
- клиентские конфигурации;
- данные доступа к административным панелям.

Цель этого документа — показать навыки администрирования Linux и сетевой диагностики без раскрытия рабочей инфраструктуры.

---

# English version

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

These commands were used to verify:

- operating system versions;
- Linux kernel versions;
- server uptime;
- memory usage;
- disk usage.

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

These commands were used to identify:

- listening TCP/UDP sockets;
- owning processes;
- local loopback services;
- services exposed on network interfaces.

## Active TCP sessions

```bash
sudo ss -ntp
```

Filtered output was used to trace specific processes and verify that both VPS nodes had matching active sessions.

Sensitive addresses and real endpoint values are intentionally omitted from the public repository.

## Process-to-port mapping

```bash
sudo lsof -nP -iTCP:<port>
```

This command was used to determine which process owned a particular TCP connection or local port.

## Routing

```bash
ip route
ip rule
```

These commands were used to inspect:

- the main routing table;
- policy routing;
- interfaces and routes between them.

## NAT inspection

```bash
sudo iptables -t nat -S
```

The inspection showed that the observed inter-node path was not implemented through a custom iptables NAT forwarding chain.

The relevant traffic path was handled by user-space networking services.

## Runtime verification

Runtime inspection confirmed the following high-level relationship:

```text
Xray
  |
  v
local loopback connection
  |
  v
sing-box
  |
  v
remote VPS
```

The second VPS simultaneously showed corresponding `ESTABLISHED` sessions.

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
- client UUIDs or identifiers;
- passwords;
- tokens;
- SSH private keys;
- ready-to-use connection strings;
- client configuration files;
- administrative panel credentials.

The purpose of this document is to demonstrate Linux administration and network troubleshooting skills without exposing active infrastructure.