# misc-spine

A context repository that documents and links a collection of related personal projects.
This repo contains no runnable code — it exists to provide a map of how the projects relate,
how to run them together, and where to find each one.

**→ [DEPLOYING.md](./DEPLOYING.md)** — full guide for adding a new service to the cluster (GitHub Actions → ghcr.io → helm chart → ArgoCD → Cloudflare tunnel)

---

## Project Groups

### Blackbook — Social Network Demo

A Facebook-inspired social network demo built with React, Node.js, and MongoDB,
deployed on Kubernetes.

| Repo | Description |
|------|-------------|
| [blackbook](../blackbook) | React frontend — "Facebook, but with blackjack and hookers" |
| [blackbook-read-profile](../blackbook-read-profile) | Node.js read-side microservice for user profiles |
| [blackbook-deploy](../blackbook-deploy) | Kubernetes manifests for the full Blackbook stack |

**Stack:** React, Node.js, MongoDB, Kubernetes, nginx  
**Run locally:** see `blackbook-deploy` for `docker-compose` and k8s YAML files.  
**Live at:** `blackbook.kamradtfamily.net` (Cloudflare tunnel → `blackbook.blackbook:80`)

---

### Javaman — Multiplayer Easter Egg Game

A multiplayer game where players share a 100×100 world and navigate through it searching for easter eggs. Auth via Keycloak; game state persisted in PostgreSQL; API routed through the shared api-gateway.

| Repo | Description |
|------|-------------|
| [javaman](../javaman) | Node.js backend — Express, PostgreSQL, `/v1/game/*` REST API |
| [javaman-client](../javaman-client) | React 18 + Vite frontend, Keycloak auth, nginx |

**Stack:** Node.js, React 18 + Vite, Keycloak, PostgreSQL, nginx, Docker  
**Live at:** `javaman.kamradtfamily.net` (Cloudflare tunnel → `javaman-client.javaman:80`)  
**API:** `api.kamradtfamily.net/javaman/v1/game/*` (api-gateway → `javaman.javaman:3001`)

---

### Naivecoin — Cryptocurrency Demo

A minimal cryptocurrency implementation in under 1500 lines of JavaScript,
inspired by [naivechain](https://github.com/lhartikk/naivechain).

| Repo | Description |
|------|-------------|
| [naivecoin](../naivecoin) | Core blockchain, wallet, miner, and HTTP API |
| [naivefront](../naivefront) | React frontend for interacting with naivecoin |
| [naivecoin-run](../naivecoin-run) | Docker Compose + nginx to run the full stack |
| [naivetest](../naivetest) | Integration/acceptance test suite |
| [naiveperf](../naiveperf) | Gatling/JMeter performance tests |

**Stack:** Node.js, React, nginx, Docker  
**Run locally:** use `docker-compose.yml` in `naivecoin-run`.  
**Live at:** `naivecoin.kamradtfamily.net` (Cloudflare tunnel → `naivefront.naivefront:80`)

---

### News Reader — Microservices Demo

A news ingestion and reading system demonstrating microservice patterns with
Spring Boot and reactive programming.

| Repo | Description |
|------|-------------|
| [readnews](../readnews) | Spring Boot (WebFlux + MongoDB) read/query service |
| [readnewsperf](../readnewsperf) | Gatling performance tests for `readnews` |
| [news-deploy](../news-deploy) | Kubernetes manifests and Docker Compose for the news stack |

**Stack:** Java, Spring Boot WebFlux, MongoDB, Kubernetes, Scala (Gatling)  
**Run locally:** see `news-deploy` for k8s YAML and `docker-compose` files.

---

### ArchitectAI — AI Code Generation Platform

An AI-powered architecture and code generation platform. ArchitectAI (React SPA) is the design/management UI; architectai-api (Express backend) handles auth, persistence, GitHub integration, and Anthropic API proxying; github-provider consumes Kafka events to push generated code to GitHub repos and monitors CI/CD pipelines.

| Repo | Description |
|------|-------------|
| [archicode](../archicode) | Full stack — React frontend + Express backend + GitHub provider service |

**Stack:** React 18 + Vite, Express.js, Auth0, MongoDB, Kafka, Anthropic API, GitHub API  
**Run locally:** `node -r dotenv/config server.js` (backend), `npm run dev` (frontend) — requires `.env` with MongoDB/Auth0 secrets.  
**Live at:** `architect.kamradtfamily.net` (Cloudflare tunnel → `architectai.rkamradt-platform:80`); API proxied through `api.kamradtfamily.net/architect`

---

### SimCity Lite — Browser City Builder

A browser-based city builder game with a Spring Boot REST backend managing city state and simulation. Players build zones, manage budgets, and watch the city grow; backend persists game state with optimistic locking via edit tokens and auto-saves every 30 game ticks.

| Repo | Description |
|------|-------------|
| [simcity-lite](../simcity-lite) | Spring Boot backend + React frontend |

**Stack:** Spring Boot 3 (Java 21), React 18 + Vite, H2/PostgreSQL  
**Run locally:** `cd simcity-backend && mvn spring-boot:run`, then `cd simcity-frontend && npm run dev`. Connect in-game at `http://localhost:8080`.  
**Live at:** `simcity.kamradtfamily.net` (Cloudflare tunnel → `simcity-frontend.simcity-lite:80`; API via `api.kamradtfamily.net/simcity`)

---

### GroceryAI — Nutrition Tracker

A full-stack nutrition tracker that logs weekly grocery purchases and automatically queries the USDA FoodData Central database for nutritional data. Displays interactive weekly nutrition reports with charts and sortable tables.

| Repo | Description |
|------|-------------|
| [groceryai](../groceryai) | Spring Boot backend + React frontend |

**Stack:** Spring Boot 3 (Java 21), React 18 + Vite, H2, USDA FoodData Central API  
**Run locally:** `./mvnw spring-boot:run` (backend), `cd nutrition-ui && npm run dev` (frontend). Requires a USDA API key (`DEMO_KEY` works for testing).

---

### DCC-EX Throttle — Model Train Controller

A WebSocket-to-TCP proxy server that bridges browser clients to a remote DCC-EX model train controller. Forwards real-time throttle commands between the web interface and the DCC-EX hardware via TCP tunneling.

| Repo | Description |
|------|-------------|
| [dccex-throttle](../dccex-throttle) | Node.js WebSocket/TCP proxy + web UI |

**Stack:** Node.js, Express, WebSocket, TCP  
**Run locally:** `npm install && npm start` (set `DCCEX_HOST`/`DCCEX_PORT` env vars; defaults to `10.147.0.8:2560`).

---

### Game of Life

Conway's Game of Life rendered to canvas. Text-seed deterministic randomness, named presets (Gosper Gun, Acorn, Pulsar, etc.), variable speed, and click-to-draw. Dark sci-fi aesthetic matching the rest of the cluster showcase.

| Repo | Description |
|------|-------------|
| [gol](../gol) | Single-file HTML5/Canvas, nginx-served |

**Stack:** Vanilla JavaScript, Canvas API, nginx, Docker, GitHub Actions, ArgoCD  
**Run locally:** Open `public/index.html` in a browser — no build step needed.  
**Live at:** `life.kamradtfamily.net` (Cloudflare tunnel → `gol.gol:80`)

---

### clodapp — Platform Landing Page

Keycloak-authenticated React SPA that serves as the landing page for the personal cloud platform at `kamradtfamily.net`. Shows login/logout state and displays identity and token claims.

| Repo | Description |
|------|-------------|
| [clodapp](../clodapp) | React 18 + Vite SPA, nginx-served |

**Stack:** React 18 + Vite, Keycloak (PKCE), nginx, Docker, ArgoCD  
**Run locally:** `npm install && npm run dev` — requires `.env` with `VITE_KEYCLOAK_URL`, `VITE_KEYCLOAK_REALM`, `VITE_KEYCLOAK_CLIENT_ID`.  
**Live at:** `kamradtfamily.net` (Cloudflare tunnel → `clodapp.clodapp:80`)

---

### Portfolio — Professional Profile Site

Personal professional profile page for job search, serving summary, skills, experience timeline, CV download, and links to GitHub/LinkedIn. Dark technical aesthetic, pure static HTML with no build step.

| Repo | Description |
|------|-------------|
| [portfolio](../portfolio) | Static HTML + nginx, Docker + ArgoCD |

**Stack:** Vanilla HTML/CSS, nginx, Docker, GitHub Actions, ArgoCD  
**Run locally:** Open `index.html` in a browser, or `docker build -t portfolio . && docker run -p 80:80 portfolio`.  
**Live at:** `profile.kamradtfamily.net` (Cloudflare tunnel → `portfolio.portfolio:80`)

---

### ORACLE Chat — kamradtfamily.net

Absurdist AI chatbot powered by Ollama (llama3.2) running in the cluster. Streams token-by-token via Server-Sent Events. Rate-limited by real visitor IP (Cloudflare `CF-Connecting-IP` header). ORACLE persona: self-aware Kubernetes spirit with strong opinions about condiments, reverence for R. L. Kamradt, and a fondness for non-sequiturs.

| Repo | Description |
|------|-------------|
| [home](../home) | Express.js backend + canvas particle frontend |

**Stack:** Node.js, Express, SSE, express-rate-limit, nginx, Docker, GitHub Actions, ArgoCD  
**Live at:** `www.kamradtfamily.net` (Cloudflare tunnel → `home.home:3000`)  
**Depends on:** Ollama in-cluster at `http://ollama.llm.svc.cluster.local:11434`

---

### Cluster Command — Live K8s Dashboard

Read-only live dashboard of the K3s cluster — nodes, pods, namespaces, CPU/memory. No login required. Reads from the Kubernetes API via in-cluster service account with a ClusterRole bound to get/list on core resources.

| Repo | Description |
|------|-------------|
| [k8s-dashboard](../k8s-dashboard) | Express.js API + static dashboard frontend |

**Stack:** Node.js, Express, Kubernetes API (in-cluster), nginx, Docker, GitHub Actions, ArgoCD  
**Live at:** `cluster.kamradtfamily.net` (Cloudflare tunnel → `k8s-dashboard.k8s-dashboard:3000`)

---

### Receipts — Receipt Scanning and Bookkeeping

Receipt scanning app for Mary. Upload photos of paper receipts; Anthropic Vision extracts structured data; review/edit fields; confirm to upload the original to Google Drive and append a row to a Google Sheet. Auth restricted to @kamradtfamily.net accounts via Google Workspace OIDC.

| Repo | Description |
|------|-------------|
| [receipts](../receipts) | Spring Boot 3.4 backend + React 18/Vite frontend, single image |

**Stack:** Java 21, Spring Boot, Spring Security OAuth2, Google Sheets/Drive API, Anthropic API, React 18 + Vite, Docker (multi-stage), GitHub Actions, ArgoCD  
**Live at:** `receipts.kamradtfamily.net` (Cloudflare tunnel → `receipts.receipts:8080`)  
**Auth:** Google Workspace OIDC, internal consent screen, hd=kamradtfamily.net enforced server-side

---

### Cluster Ops Agent — AI K8s Operations Assistant

A read-only LangGraph agent that answers questions about the K3s cluster in plain English. Calls live Kubernetes and ArgoCD APIs via five read-only tools, detects unhealthy pods and routes to a focused diagnosis node, streams progress via SSE, and redacts sensitive data from all tool output. Built as a job-search showcase for LangGraph and agent-workflow experience.

| Repo | Description |
|------|-------------|
| [cluster-ops-agent](../cluster-ops-agent) | Python 3.12 — LangGraph graph, FastAPI SSE endpoint, web UI |

**Stack:** Python 3.12, LangGraph 1.2, Ollama (llama3.2), FastAPI, SQLite checkpointer, Kubernetes API (in-cluster ClusterRole), ArgoCD CRD  
**Live at:** `agent.kamradtfamily.net` (Cloudflare tunnel → `cluster-ops-agent.cluster-ops-agent:8000`)  
**Depends on:** Ollama in-cluster at `http://ollama.llm.svc.cluster.local:11434`

---

### Prior Auth Assist — Prior-Authorization Paperwork Assistant

An AI-powered prior-authorization assistant for clinicians. An MCP server exposes five tools
(find_patient, requirements_for, prefill_documentation, missing_items, build_request) backed
by a HAPI FHIR R4 server. Claude reduces paperwork by auto-filling documentation from the
chart and surfacing gaps. All coverage decisions remain with humans.

| Repo | Description |
|------|-------------|
| [priorauth-assist](../priorauth-assist) | Spring Boot 3.4 MCP server + FHIR rules + demo data + Helm charts |

**Stack:** Java 21, Spring Boot 3.4, MCP Java SDK 0.9.0, HAPI FHIR R4, FHIR Claim (PAS-style),
PostgreSQL, React 18, Docker (multi-stage), GitHub Actions, ArgoCD  
**Live at:** `priorauth.kamradtfamily.net` (Cloudflare Access → `priorauth-assist.priorauth-assist:8080`)  
**Internal FHIR:** `priorauth-fhir.priorauth-fhir:8080` (HAPI FHIR v7.4.0, PostgreSQL backend)  
**MCP tools:** stdio (Claude Desktop) and streamable HTTP (in-cluster)  
**Demo:** 5 hand-crafted patients + Synthea population; 5 coverage rules (knee MRI, lumbar MRI, sleep study, adalimumab step-therapy, adalimumab bypass)

---

### API Smoke Tests — In-cluster Service Health Monitor

Periodic smoke test suite that runs as a Kubernetes CronJob every 30 minutes and validates
all in-cluster API services. Bypasses Cloudflare Access and edge auth, so internal-only
services and Cloudflare-gated services are equally testable. Results are emitted as
structured JSON to stdout for Loki/Grafana ingestion.

| Repo | Description |
|------|-------------|
| [api-smoke-tests](../api-smoke-tests) | Python 3.12 — pytest + httpx test suite, runner, Helm chart |

**Stack:** Python 3.12, pytest, httpx, pytest-json-report, minio, Kubernetes CronJob, ArgoCD  
**Schedule:** Every 30 minutes (`*/30 * * * *`)  
**Namespace:** `api-smoke-tests`  
**Setup:** `./setup-secrets.sh` after ArgoCD creates the namespace  
**Services tested:** HAPI FHIR, Ollama, Cluster Ops Agent, Axon Server, Grafana (API key), MinIO (access key)  
**Auth:** No edge auth in-cluster; optional secrets injected via Helm `secretRefs` for Grafana and MinIO

---

### JupyterLab — In-cluster Notebook Environment

Personal JupyterLab instance (scipy-notebook) for data exploration and experimentation.
Internal access only via port-forward — no Cloudflare tunnel, no auth.
Notebooks persisted on a 1Gi PVC at `/home/jovyan/work`.

| Resource | Description |
|----------|-------------|
| [rkamradt-helm-charts/jupyter](../rkamradt-helm-charts/jupyter) | Helm chart — Deployment, Service, PVC |

**Stack:** quay.io/jupyter/scipy-notebook, Kubernetes (K3s), local-path PVC  
**Access:** `kubectl port-forward -n jupyter svc/jupyter 8888:8888` → `http://localhost:8888`  
**Deployed via:** ArgoCD (no GitHub Actions — public image, no build pipeline)

---

### Ollama — In-cluster LLM Inference

A CPU-only Ollama deployment running `llama3.2:3b-instruct-q4_K_M` inside the K3s
cluster. Foundation for the idea-loop side project: a small model generates thoughts
every few minutes and a discriminator scores them.

| Resource | Description |
|----------|-------------|
| [rkamradt-helm-charts/ollama](../rkamradt-helm-charts/ollama) | Helm chart — Deployment, Service, PVC |

**Stack:** Ollama 0.35.1, Llama 3.2 3B Q4_K_M, Kubernetes (K3s), local-path PVC  
**In-cluster endpoint:** `http://ollama.llm.svc.cluster.local:11434`  
**Deployed via:** ArgoCD (entry in `rkamradt-helm-charts/apps/values.yaml`)  
**Internal only:** not exposed through Cloudflare tunnel or Auth0.

---

### API Gateway — Shared JWT Proxy

A Go reverse proxy with Keycloak JWKS JWT validation. Validates Bearer tokens, injects `X-User-ID` (Keycloak `sub`), and routes prefixed paths to in-cluster backends. On key-not-found the JWKS cache auto-refreshes and retries once to handle key rotation.

| Resource | Description |
|----------|-------------|
| [api-gateway](../api-gateway) | Go — JWT validation, reverse proxy, CORS |
| [rkamradt-helm-charts/api-gateway](../rkamradt-helm-charts/api-gateway) | Helm chart — Deployment, Service, ConfigMap |

**Routes:**

| Prefix | Upstream |
|--------|----------|
| `/architect` | `architectai-api.rkamradt-platform:8080` |
| `/simcity` | `simcity-backend.simcity-lite:8080` |
| `/javaman` | `javaman.javaman:3001` |
| `/blackbook` | `blackbook-read-profile.blackbook:8080` |
| `/readnews` | `readnews.nr:8080` |
| `/users`, `/accounts`, `/transactions` | fintech services |

**Stack:** Go, Keycloak JWKS (realm `rkamradt` at `auth.kamradtfamily.net`)  
**Live at:** `api.kamradtfamily.net` (Cloudflare tunnel → `api-gateway.api-gateway:8080`)

---

### Infisical — Secret Manager

Self-hosted secret manager for storing API keys and credentials. Cloudflare Access gated (email policy). Kubernetes operator available for syncing secrets to cluster.

| Resource | Description |
|----------|-------------|
| [rkamradt-helm-charts/infisical](../rkamradt-helm-charts/infisical) | Helm chart |

**Stack:** Node.js, PostgreSQL (`infisicaldb`), Redis  
**Live at:** `secrets.kamradtfamily.net` (Cloudflare Access → `infisical.infisical:8080`)

---

### Outline — Team Wiki

Confluence-style wiki for documentation. Keycloak SSO login. Attachments stored in MinIO.

| Resource | Description |
|----------|-------------|
| [rkamradt-helm-charts/outline](../rkamradt-helm-charts/outline) | Helm chart |

**Stack:** Node.js, PostgreSQL (`outlinedb`), Redis, MinIO (bucket `outline`), Keycloak OIDC  
**Live at:** `docs.kamradtfamily.net` (Cloudflare tunnel → `outline.outline:3000`)

---

### Platform Services — Shared Infrastructure

Stateful services shared across multiple apps in the cluster. All internal-only; no Cloudflare exposure.

| Service | Namespace | Port | Used by |
|---------|-----------|------|---------|
| Keycloak | `keycloak` | 8080 | All apps (SSO); public at `auth.kamradtfamily.net` |
| MongoDB | `mongodb` | 27017 | ArchitectAI API, Blackbook, Readnews |
| Kafka | `kafka` | 9092 | ArchitectAI (github-provider) |
| MinIO | `minio` | 9000/9001 | ArchitectAI API, Outline (file storage) |
| PostgreSQL | `postgresql` | 5432 | Keycloak, Javaman, SimCity, Infisical, Outline |
| Redis | `redis` | 6379 | Infisical, Outline |
| Infisical | `infisical` | 8080 | Secret manager; public at `secrets.kamradtfamily.net` |
| Grafana + Loki + Promtail | `monitoring` | 3000/3100 | Cluster-wide log aggregation |
| Axon Server | `axon-server` | 8124/8024 | Event store (gRPC 8124, HTTP 8024) |

**Deployed via:** ArgoCD, entries in `rkamradt-helm-charts/apps/values.yaml`

---

## Project Dependency Map

```
blackbook-deploy
  └── blackbook                (React frontend)
  └── blackbook-read-profile   (Node.js API)
  └── MongoDB

javaman-client
  └── javaman                  (Node.js backend)

naivecoin-run
  └── naivecoin                (blockchain core)
  └── naivefront               (React UI)
  └── naivetest                (integration tests)
  └── naiveperf                (perf tests)

news-deploy
  └── readnews                 (Spring Boot service)
  └── readnewsperf             (Gatling perf tests)

archicode
  └── ArchitectAI React SPA    (frontend)
  └── architectai-api          (Express backend)
  └── github-provider          (Kafka consumer → GitHub)
  └── MongoDB, Kafka, Auth0, Anthropic API

simcity-lite (simcity.kamradtfamily.net)
  └── simcity-backend          (Spring Boot)
  └── simcity-frontend         (React)
  └── api-gateway              (proxies /simcity → simcity-backend)
  └── PostgreSQL

groceryai
  └── Spring Boot backend
  └── nutrition-ui             (React frontend)
  └── USDA FoodData Central API

dccex-throttle                 (standalone Node.js proxy)
gol (life.kamradtfamily.net)   (standalone single-file HTML)
clodapp (kamradtfamily.net)    (standalone React SPA → Auth0)
portfolio (profile.kamradtfamily.net)  (standalone static HTML)

home (www.kamradtfamily.net)
  └── Ollama in-cluster (llm namespace)

k8s-dashboard (cluster.kamradtfamily.net)
  └── Kubernetes API (in-cluster ClusterRole)

cluster-ops-agent (agent.kamradtfamily.net)
  └── Ollama in-cluster (llm namespace)
  └── Kubernetes API (in-cluster ClusterRole)
  └── ArgoCD Applications CRD

api-gateway (api.kamradtfamily.net)
  └── architectai-api.rkamradt-platform:8080
  └── simcity-backend.simcity-lite:8080
  └── javaman.javaman:3001
  └── blackbook-read-profile.blackbook:8080
  └── readnews.nr:8080
  └── fintech services (user/account/transaction)

api-smoke-tests (CronJob, api-smoke-tests namespace)
  └── priorauth-fhir.priorauth-fhir:8080   (HAPI FHIR)
  └── ollama.llm:11434                       (Ollama)
  └── cluster-ops-agent.cluster-ops-agent:8000
  └── axon-server.axon-server:8024
  └── grafana.monitoring:3000               (optional, needs API key)
  └── minio.minio:9000                      (optional, needs access key)

ollama (llm namespace)
  └── rkamradt-helm-charts/ollama  (Helm chart + PVC)

jupyter (internal only)
  └── rkamradt-helm-charts/jupyter  (Helm chart + PVC, public image)

platform services (shared infra)
  └── MongoDB (mongodb namespace)
  └── Kafka + Zookeeper (kafka namespace)
  └── MinIO (minio namespace)
  └── PostgreSQL (postgresql namespace)
  └── Grafana + Loki + Promtail (monitoring namespace)
  └── Axon Server (axon-server namespace)
```

---

## Common Patterns

- **Frontend:** React apps served behind nginx in Docker
- **Backend APIs:** Node.js or Spring Boot, containerized with Docker
- **Persistence:** MongoDB (general), PostgreSQL (simcity), MinIO (object storage)
- **Auth:** Auth0 (JWT audience `https://api.kamradtfamily.net`); validated at api-gateway for all backend routes
- **Deployment:** All services in `rkamradt-helm-charts`; ArgoCD auto-syncs; CI patches `version-values.yaml` with `sha-<7hex>` tag
- **Public access:** Cloudflare tunnel → no Ingress resources; routes configured in Zero Trust dashboard
- **Local dev:** `docker-compose.yml` files in each stack's run/deploy repo

---

## Repository Index

| Repo | Language | Role |
|------|----------|------|
| [blackbook](../blackbook) | JavaScript/React | Frontend |
| [blackbook-read-profile](../blackbook-read-profile) | Node.js | API service |
| [blackbook-deploy](../blackbook-deploy) | YAML | K8s deployment |
| [javaman](../javaman) | Node.js | Game server |
| [javaman-client](../javaman-client) | JavaScript/React | Game frontend |
| [naivecoin](../naivecoin) | Node.js | Blockchain core |
| [naivefront](../naivefront) | JavaScript/React | Coin UI |
| [naivecoin-run](../naivecoin-run) | YAML | Compose runner |
| [naivetest](../naivetest) | JavaScript | Integration tests |
| [naiveperf](../naiveperf) | JMeter/Gatling | Performance tests |
| [readnews](../readnews) | Java/Spring Boot | News read service |
| [readnewsperf](../readnewsperf) | Scala/Gatling | News perf tests |
| [news-deploy](../news-deploy) | YAML | K8s deployment |
| [archicode](../archicode) | Node.js/React | AI code generation platform |
| [simcity-lite](../simcity-lite) | Java/React | City builder game |
| [groceryai](../groceryai) | Java/React | Nutrition tracker |
| [dccex-throttle](../dccex-throttle) | Node.js | Model train WebSocket proxy |
| [gol](../gol) | JavaScript | Game of Life (life.kamradtfamily.net) |
| [home](../home) | Node.js | ORACLE chat (www.kamradtfamily.net) |
| [k8s-dashboard](../k8s-dashboard) | Node.js | Cluster dashboard (cluster.kamradtfamily.net) |
| [clodapp](../clodapp) | JavaScript/React | Platform landing page |
| [portfolio](../portfolio) | HTML/CSS | Professional profile site (profile.kamradtfamily.net) |
| [rkamradt-helm-charts/ollama](../rkamradt-helm-charts/ollama) | YAML/Helm | LLM inference server |
| [rkamradt-helm-charts/jupyter](../rkamradt-helm-charts/jupyter) | YAML/Helm | JupyterLab (internal) |
| [rkamradt-helm-charts/api-gateway](../rkamradt-helm-charts/api-gateway) | YAML/Helm | Shared JWT proxy (api.kamradtfamily.net) |
| [cluster-ops-agent](../cluster-ops-agent) | Python 3.12 | AI K8s ops agent (agent.kamradtfamily.net) |
| [api-smoke-tests](../api-smoke-tests) | Python 3.12 | In-cluster API smoke test CronJob |
