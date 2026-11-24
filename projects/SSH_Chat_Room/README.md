# Secure SSH Multi-Client Chat & Socket Messaging Engine

An asynchronous, encrypted multi-client chat room architecture implemented in **Python** using low-level socket programming and the **SSHv2 / Paramiko** protocol suite, featuring asymmetric RSA cryptography, concurrent multi-threading, and separated dual-stream audit logging.

---

## Architectural Overview

```
                      [ Client 1 ]           [ Client 2 ]
                           │                      │
                   (Encrypted SSHv2)      (Encrypted SSHv2)
                           ▼                      ▼
                   ┌──────────────────────────────────────┐
                   │        Central SSH Server            │
                   ├──────────────────────────────────────┤
                   │  • Asymmetric RSA Host Key Security  │
                   │  • Credential Authentication Store   │
                   └──────────────────┬───────────────────┘
                                      │
               ┌──────────────────────┴──────────────────────┐
               ▼                                             ▼
     Client Worker Thread                          Client Mailbox Thread
  (Socket I/O & Command Parsing)                (Asynchronous Message Queue)
```

---

## Core System Features

### 1. Asymmetric Cryptography & SSHv2 Transport
* **Encrypted Communication Channel:** Protects message contents against eavesdropping and packet injection using SSHv2 encrypted socket tunnels.
* **Host Key Verification:** Uses an asymmetric RSA key pair (`test_rsa.key`) to establish server identity and negotiate session ciphers during the initial cryptographic handshake.
* **Credential Authentication:** Validates user login credentials against a protected user database (`UserRecords.csv`) prior to granting shell channel access.

### 2. Concurrent Multi-Threaded Client Architecture
To prevent slow network clients from stalling the messaging engine, each connected user session operates via two dedicated execution threads:
* **Connection & Protocol Worker Thread:** Manages socket input/output streams, handles command tokenization, and enforces protocol state transitions.
* **Dedicated Mailbox Thread:** Implements an asynchronous queue buffer that collects incoming peer messages and system broadcasts, preventing blocking during active network writes.

### 3. Dual-Stream Audit & Security Telemetry
Maintains strict operational separation between protocol transport telemetry and application audit events:
* **Protocol & Transport Stream (`server.log`):** Records low-level SSH protocol exchanges, cipher negotiations, frame sequences, and raw socket transactions.
* **Security & Audit Stream (`server_events.log`):** Human-readable chronological audit ledger logging user authentications, channel establishments, point-to-point message routing, and disconnect events.

---

## Interactive Protocol Commands

The server terminal accepts structured command verbs:

| Command | Syntax | Description |
| :--- | :--- | :--- |
| **Login** | `Login <username> <password>` | Authenticates identity against the credential store and opens an encrypted session channel. |
| **Message** | `msg <receiver_username> <message>` | Routes a point-to-point encrypted payload to the designated target client's mailbox. |
| **Logout** | `logout` | Safely tears down the SSH session channel and closes client socket descriptors. |
