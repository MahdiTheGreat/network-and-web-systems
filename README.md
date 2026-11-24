# Network, Security & Web Systems Monorepo

A unified engineering and infrastructure monorepo spanning computer networking protocols, network reconnaissance and penetration testing tools, encrypted socket communications, cloud-native backend microservices, and asynchronous web client applications. The repository integrates projects developed in **Go (Golang)**, **Python**, and **JavaScript**, complete with **Docker** multi-stage containerization, **Kubernetes** orchestration, and **Redis** in-memory persistence.

---

## Engineering Pillars

```
                        Network & Web Systems Monorepo
                                      │
    ┌───────────────────┬─────────────┴─────────────┬───────────────────┐
    ▼                   ▼                           ▼                   ▼
Cloud-Native        Encrypted Socket            Network Recon &       Web Clients &
Microservices       Communications              Port Scanning         REST Consumers
• Go HTTP Server    • Python Socket Shell       • 10+ Port Scan Modes • JavaScript Fetch
• Redis In-Memory   • Asymmetric RSA Host Keys  • ICMP Latency Probes • HTML5 LocalStorage
• Kubernetes HPA    • Multi-Thread Mailboxes    • Subnet IP Sweeps    • Regex Form Validation
• Docker Multi-Stage• Dual-Stream Audit Logs    • OS Fingerprinting   • Viewport CSS Flexbox
```

---

### 1. Cloud-Native Microservices & Kubernetes Orchestration ([`projects/urlShortner/`](projects/urlShortner/))
* **High-Throughput Go Backend:** Implements an $O(1)$ URL shortening and HTTP redirect engine in Go, utilizing in-memory key-value caching in Redis with configurable TTL expiration.
* **Multi-Stage Docker Packaging:** Compiles statically linked CGO-disabled binaries in a Golang builder stage and deploys them into a hardened Alpine Linux runtime image, reducing container footprint by $>90\%$.
* **Full-Stack Kubernetes Architecture:**
  * Application Tier: Multi-replica Deployments, ClusterIP/NodePort Services, and Horizontal Pod Autoscalers (HPA).
  * Storage Tier: Redis StatefulSets with stable ordinal hostnames, Headless Services, and persistent volumes (`PV` & `PVC`).
  * Security: Kubernetes `Secret` resources safeguarding database credentials.

---

### 2. Transport Security & Encrypted Socket Systems ([`projects/SSH_Chat_Room/`](projects/SSH_Chat_Room/))
* **SSHv2 Encrypted Transport:** Secures multi-client socket communications against eavesdropping and packet injection using the Paramiko SSH protocol library.
* **Cryptographic Handshakes:** Enforces asymmetric RSA host key verification (`test_rsa.key`) alongside credential-based access control (`UserRecords.csv`).
* **Concurrent Dual-Thread Architecture:** Assigns two independent execution threads per connected client (a connection worker handling network I/O and a dedicated mailbox thread managing asynchronous message queues).
* **Dual-Channel Audit Logging:** Maintains separate telemetry streams for low-level protocol packets (`server.log`) and human-readable security audit trails (`server_events.log`).

---

### 3. Network Reconnaissance & Penetration Probing ([`projects/scanner/`](projects/scanner/))
* **ICMP Ping Latency Suite:** Measures round-trip time (RTT), latency variance, and packet loss across domain names and raw IPv4 endpoints.
* **Subnet IP Range Discovery:** Sweeps CIDR network ranges using ARP/ICMP probes with fallback TCP service detection (`-Pn`) for stealth hosts.
* **10-Vector Port Scanner:** Automates multi-technique Layer 4 reconnaissance:
  * TCP SYN Stealth (`-sS`), TCP Connect (`-sT`), ACK Firewall Mapping (`-sA`), Window (`-sW`), Maimon (`-sM`), Null (`-sN`), FIN (`-sF`), Xmas (`-sX`), IP Protocol (`-sO`), and UDP/SCTP (`-sU -sY -sZ`).
* **Active/Passive Fingerprinting:** Identifies target operating system kernels and network stacks via Nmap and Xprobe2, including TCP Idle Zombie scanning (`-sI`).

---

### 4. Asynchronous Web Clients & REST Consumers ([`projects/Mr-or-Mrs/`](projects/Mr-or-Mrs/))
* **Asynchronous REST Integration:** Dispatches non-blocking HTTP GET requests using the JavaScript Fetch API and Promise chains to consume demographic classification services.
* **Client-Side Persistence:** Caches confirmed classifications and manual overrides in HTML5 `localStorage`, eliminating redundant API calls.
* **Responsive Styling & Validation:** Viewport-adaptive design using CSS Flexbox, custom opacity overlays, and regex input sanitation.

---

## Directory Structure

```text
network-and-web-systems/
├── README.md
├── .gitignore
└── projects/
    ├── urlShortner/
    │   ├── README.md
    │   ├── Dockerfile
    │   ├── httpServer.go
    │   ├── go.mod
    │   ├── go.sum
    │   ├── alpine-deployment.yml
    │   ├── redis-app-deployment.yml
    │   ├── redis-app-service.yml
    │   ├── redis-app-autoscaler.yml
    │   ├── redis-db-statefulSet.yaml
    │   ├── redis-db-headless-service.yml
    │   ├── redis-db-service.yaml
    │   ├── redis-db-autoscaler.yml
    │   ├── persistent-volume.yml
    │   ├── persistent-volume-claim.yml
    │   └── pass-secret.yaml
    ├── SSH_Chat_Room/
    │   ├── README.md
    │   ├── server.py
    │   ├── client1.py
    │   ├── client2.py
    │   ├── interactive.py
    │   ├── test_rsa.key
    │   ├── UserRecords.csv
    │   └── report.pdf
    ├── scanner/
    │   ├── README.md
    │   ├── ipScanner.py
    │   ├── ping.py
    │   ├── portScanner.py
    │   ├── test.py
    │   ├── xprobe2.png
    │   └── report.pdf
    └── Mr-or-Mrs/
        ├── README.md
        ├── index.html
        └── Gender_binary.jpg
```

---

## Technical Stack & Tooling

* **Languages:** Go (Golang), Python 3, JavaScript (ES6+), HTML5, CSS3.
* **Networking & Security:** Sockets, SSHv2, RSA Cryptography, ICMP, TCP/UDP, Nmap, Paramiko, Xprobe2, Wireshark/Netdiscover.
* **Cloud & DevOps:** Docker, Kubernetes (Deployments, StatefulSets, Headless Services, HPA, PV/PVC, Secrets), Minikube.
* **Datastores & Caching:** Redis In-Memory K-V Datastore, HTML5 LocalStorage, CSV Flat-File Databases.
