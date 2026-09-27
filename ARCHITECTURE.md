# Architecture / Архитектура

---

# Русская версия

## Назначение

Этот документ описывает высокоуровневую архитектуру двухузловой Linux VPS-инфраструктуры и те связи между компонентами, которые были подтверждены стандартными средствами диагностики Linux.

Документ намеренно не содержит:

- реальных IP-адресов;
- рабочих портов;
- UUID и других клиентских идентификаторов;
- паролей и токенов;
- клиентских конфигураций;
- готовых строк подключения;
- приватных ключей и сертификатов.

## Общая схема

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

## Узел 1 — входной VPS

На входном узле было подтверждено следующее окружение:

- Ubuntu Server 22.04 LTS;
- запущен Xray;
- запущен sing-box;
- установлен и активен 3x-ui;
- Xray и sing-box взаимодействуют через локальное loopback-соединение;
- sing-box поддерживает активные соединения со вторым VPS.

Публичный репозиторий намеренно не содержит реальные адреса и рабочие порты.

## Узел 2 — внешний VPS

На втором узле было подтверждено:

- Ubuntu Server 24.04 LTS;
- запущен sing-box;
- также присутствуют Xray и 3x-ui;
- узел принимает активные соединения от первого VPS;
- межузловые TCP-сессии были проверены через `ss`.

## Подтверждённая runtime-цепочка

По состоянию работающей системы была подтверждена следующая цепочка:

```text
Xray на входном VPS
      |
      v
127.0.0.1:<локальный порт>
      |
      v
sing-box на входном VPS
      |
      v
внешний VPS:<межузловой порт>
      |
      v
sing-box на внешнем VPS
```

Точные значения рабочих endpoint'ов и производственные параметры намеренно не публикуются.

## Важное замечание о 3x-ui

3x-ui/Xray активен на серверах, однако наблюдаемая межузловая передача была связана с отдельной цепочкой Xray/sing-box.

Поэтому в этом репозитории не утверждается, что именно 3x-ui напрямую передаёт трафик между двумя VPS.

Такое разделение сделано намеренно, чтобы описание соответствовало фактическим данным диагностики.

## Как архитектура проверялась

Для восстановления фактической runtime-архитектуры использовались:

```bash
ps -ef
systemctl status x-ui
ss -tulpn
ss -ntp
lsof -nP -iTCP:<port>
ip route
ip rule
iptables -t nat -S
```

Эти команды позволили:

- определить активные процессы;
- сопоставить процессы и сокеты;
- проверить локальные loopback-соединения;
- увидеть межузловые `ESTABLISHED`-сессии;
- проверить маршрутизацию и NAT-состояние.

## Граница публичной документации

Этот документ предназначен для демонстрации навыков:

- администрирования Linux;
- диагностики сетевых соединений;
- анализа процессов и сокетов;
- документирования многокомпонентной инфраструктуры.

Он не является инструкцией по развёртыванию готового сервиса и не содержит данных, достаточных для подключения к рабочей инфраструктуре.

---

# English version

## Purpose

This document describes the high-level architecture of a two-node Linux VPS infrastructure and the component relationships that were verified using standard Linux diagnostic tools.

The document intentionally excludes:

- real IP addresses;
- production ports;
- client UUIDs and identifiers;
- passwords and tokens;
- client configuration files;
- ready-to-use connection strings;
- private keys and certificates.

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

The following environment was observed on the entry node:

- Ubuntu Server 22.04 LTS;
- Xray is running;
- sing-box is running;
- 3x-ui is installed and active;
- Xray and sing-box communicate through a local loopback connection;
- sing-box maintains active sessions to the second VPS.

The public repository intentionally omits real addresses and production ports.

## Node 2 — External VPS

The second node was observed to have:

- Ubuntu Server 24.04 LTS;
- sing-box running;
- Xray and 3x-ui also present;
- active sessions received from the first VPS;
- inter-node TCP sessions verified with `ss`.

## Verified runtime chain

The following runtime relationship was confirmed from live socket state:

```text
Xray on Entry VPS
      |
      v
127.0.0.1:<local port>
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

Exact production endpoint values are intentionally excluded.

## Important note about 3x-ui

3x-ui/Xray is active on the systems, but the observed inter-node transport was associated with a separate Xray/sing-box chain.

For that reason, this repository does not claim that 3x-ui itself directly forwards traffic between the two VPS nodes.

This distinction is intentional so that the documentation reflects the runtime evidence accurately.

## How the architecture was verified

The following tools were used to reconstruct the runtime architecture:

```bash
ps -ef
systemctl status x-ui
ss -tulpn
ss -ntp
lsof -nP -iTCP:<port>
ip route
ip rule
iptables -t nat -S
```

These commands helped to:

- identify active processes;
- map processes to sockets;
- verify local loopback communication;
- observe inter-node `ESTABLISHED` sessions;
- inspect routing and NAT state.

## Public documentation boundary

This document is intended to demonstrate skills in:

- Linux administration;
- network troubleshooting;
- process and socket analysis;
- infrastructure documentation.

It is not a deployment guide and does not provide enough information to connect to the active infrastructure.