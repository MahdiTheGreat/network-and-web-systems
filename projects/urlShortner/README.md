# Cloud-Native URL Shortener: Go Microservice & Kubernetes Deployment

A production-grade, distributed URL redirection microservice developed in **Go (Golang)** and backed by an in-memory **Redis** datastore, fully containerized via **Docker** multi-stage builds and orchestrated on **Kubernetes (Minikube / Production K8s)** with StatefulSets, autoscalers, persistent volumes, and secret management.

---

## Architecture & Infrastructure

```
                      Client Traffic (HTTP Requests)
                                    │
                                    ▼
                      ┌───────────────────────────┐
                      │  Kubernetes Service       │ (NodePort / LoadBalancer)
                      └─────────────┬─────────────┘
                                    │
               ┌────────────────────┴────────────────────┐
               ▼                                         ▼
   ┌───────────────────────┐                 ┌───────────────────────┐
   │ Go App Pod Replica 1  │                 │ Go App Pod Replica 2  │
   │ (HTTP REST Endpoints) │                 │ (HTTP REST Endpoints) │
   └───────────┬───────────┘                 └───────────┬───────────┘
               │                                         │
               └────────────────────┬────────────────────┘
                                    │ (Internal Network Routing)
                                    ▼
                      ┌───────────────────────────┐
                      │  Redis Headless Service   │
                      └─────────────┬─────────────┘
                                    │
               ┌────────────────────┴────────────────────┐
               ▼                                         ▼
   ┌───────────────────────┐                 ┌───────────────────────┐
   │ Redis StatefulSet Pod │                 │ Persistent Volume     │
   │ (`redis-db-0`)        │ ◄────────────── │ (`PV` & `PVC`)        │
   └───────────────────────┘                 └───────────────────────┘
```

---

## Key Engineering Highlights

### 1. High-Performance Go Microservice (`httpServer.go`)
* **REST Endpoints:** Exposes two primary endpoints:
  * `POST /shorten`: Ingests original long URLs, generates unique truncated hashes, and persists the key-value pair in Redis with a configurable Time-To-Live (TTL) expiration window.
  * `GET /{hash}`: Intercepts shortened keys and performs instant $O(1)$ in-memory lookups to issue HTTP 301/302 redirects to destination addresses.
* **Environment Decoupling:** Decouples operational parameters (server port, TTL windows, Redis host/port, database credentials) through structured configuration files and environment variable injections.

### 2. Multi-Stage Docker Containerization (`Dockerfile`)
* **Builder Stage:** Uses `golang:latest` with `CGO_ENABLED=0 GOOS=linux` to compile a statically linked, standalone binary with zero external shared library dependencies.
* **Minimal Runtime Stage:** Copies the compiled binary into a lightweight `alpine:latest` base with SSL root CA certificates (`ca-certificates`), slashing the final deployment image size by $>90\%$ to reduce container attack surfaces and startup latency.

### 3. Kubernetes Orchestration Suite
* **Application Deployment (`redis-app-deployment.yml`):** Deploys multi-replica instances of the Go microservice with rolling update strategies and liveness/readiness probes.
* **Database StatefulSet (`redis-db-statefulSet.yaml`):** Manages the Redis storage tier with stable network identities and predictable ordinal indexing.
* **Storage Persistence (`persistent-volume.yml`, `persistent-volume-claim.yml`):** Connects the Redis container filesystem to durable block storage, ensuring keys and shortened URL mappings survive pod rescheduling or host node reboots.
* **Horizontal Pod Autoscaling (`redis-app-autoscaler.yml`, `redis-db-autoscaler.yml`):** Automatically scales pod replicas up or down in response to real-time CPU and memory saturation metrics.
* **Security & Secret Encryption (`pass-secret.yaml`):** Protects sensitive database access credentials using native Kubernetes `Secret` resources, avoiding plain-text exposure in configuration files.
