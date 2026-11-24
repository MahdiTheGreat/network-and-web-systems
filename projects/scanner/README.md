# Network Reconnaissance, Host Discovery & Multi-Protocol Port Scanner

A network diagnostic and penetration testing suite in **Python** implementing ICMP round-trip latency measurement, subnet IP address discovery, active/passive OS fingerprinting, and automated multi-vector TCP/UDP/SCTP port scanning.

---

## Capabilities & Architecture

```
                      Target Host / Subnet
                               │
       ┌───────────────────────┼───────────────────────┐
       ▼                       ▼                       ▼
  ICMP Ping Suite         Subnet Discovery       Port Scanner Engine
  • Raw Echo Requests     • CIDR IP Range Sweeps • 10+ Nmap Probe Modes
  • RTT Latency Telemetry • Active Host Pruning  • Firewall Mapping
  • DNS Resolution        • Unresponsive Fallback• OS Fingerprinting
```

---

## Modular Subsystems

### 1. ICMP Ping & Round-Trip Latency Analyzer (`ping.py`)
* **Dual Resolution:** Resolves both Fully Qualified Domain Names (FQDNs) and raw IPv4 targets via `socket.gethostbyname`.
* **Latency Telemetry:** Sends controlled ICMP echo request packets, calculating round-trip time (RTT) statistics, packet loss percentages, and MTU payload bounds.

### 2. Subnet IP Discovery & Sweep Scanner (`ipScanner.py`)
* **CIDR Traversal:** Parses network boundaries using the `ipaddress` module to sweep arbitrary IPv4 ranges.
* **Firewall Evasion & Fallback Probing:** Incorporates unpingable host detection (`-Pn` mode) to test standard service ports (e.g., HTTP, HTTPS, SSH), identifying active machines configured to drop ICMP echo requests.

### 3. Multi-Vector Port & Protocol Scanner (`portScanner.py`)
Automates low-level Layer 4 and Layer 3 reconnaissance across target port ranges, executing 10 distinct scanning techniques:

| Scan Flag | Methodology | Mechanism & Diagnostic Target |
| :--- | :--- | :--- |
| **`-sS`** | **TCP SYN Stealth** | Half-open scan sending SYN packets without completing the 3-way handshake; minimizes detection. |
| **`-sT`** | **TCP Connect** | Completes standard 3-way handshake (`SYN` $\rightarrow$ `SYN/ACK` $\rightarrow$ `ACK`); reliable for non-root users. |
| **`-sA`** | **TCP ACK** | Sends bare ACK packets; analyzes RST response flags to determine stateful firewall filter rules. |
| **`-sW`** | **TCP Window** | Evaluates open vs. closed ports based on the returned TCP window size in RST packets. |
| **`-sM`** | **TCP Maimon** | Transmits FIN/ACK probes to expose non-RFC-compliant BSD-derived networking stacks. |
| **`-sN`** | **TCP Null** | Clears all header flags; RFC 793 mandates closed ports respond with RST, while open ports drop the packet. |
| **`-sF`** | **TCP FIN** | Sets only the FIN flag to bypass rudimentary packet filters looking for SYN packets. |
| **`-sX`** | **TCP Xmas** | Lights up FIN, PSH, and URG flags; detects open/filtered ports on compliant TCP implementations. |
| **`-sO`** | **IP Protocol** | Identifies which Layer 4 protocols (ICMP, TCP, UDP, GRE, ESP) the target OS actively supports. |
| **`-sU -sY -sZ`**| **UDP & SCTP** | Probes connectionless UDP endpoints and SCTP (Stream Control Transmission Protocol) associations. |

---

## Operating System Fingerprinting & Verification

* **TCP/IP Stack Fingerprinting:** Benchmarks packet responses against signature databases via Nmap and **Xprobe2** (`xprobe2.png`) to identify target kernel versions, TTL decay, and window sizes.
* **TCP Idle Zombie Scanning (`-sI`):** Evaluates blind port scanning using a secondary "zombie" host to infer open ports via IP ID sequence number increments ($\Delta \text{IP ID} = 2$), achieving complete anonymity without transmitting packets directly from the scanner IP.
