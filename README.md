# misc-spine

A context repository that documents and links a collection of related personal projects.
This repo contains no runnable code — it exists to provide a map of how the projects relate,
how to run them together, and where to find each one.

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

---

### Javaman — Multiplayer Easter Egg Game

A Node.js multiplayer game where players search for hidden easter eggs.

| Repo | Description |
|------|-------------|
| [javaman](../javaman) | Node.js backend game server |
| [javaman-client](../javaman-client) | React frontend client |

**Stack:** Node.js, React, nginx, Docker  
**Run locally:** use the `docker-compose.yml` in `javaman-client`.

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

---

### SimCity Lite — Browser City Builder

A browser-based city builder game with a Spring Boot REST backend managing city state and simulation. Players build zones, manage budgets, and watch the city grow; backend persists game state with optimistic locking via edit tokens and auto-saves every 30 game ticks.

| Repo | Description |
|------|-------------|
| [simcity-lite](../simcity-lite) | Spring Boot backend + React frontend |

**Stack:** Spring Boot 3 (Java 21), React 18 + Vite, H2/PostgreSQL  
**Run locally:** `cd simcity-backend && mvn spring-boot:run`, then `cd simcity-frontend && npm run dev`. Connect in-game at `http://localhost:8080`.

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

Conway's Game of Life rendered to canvas with a phosphor-green retro CRT aesthetic. Features automatic pattern reseeding, fade transitions, and optional easter-egg patterns embedded in the simulation.

| Repo | Description |
|------|-------------|
| [gol](../gol) | Single-file HTML5/Canvas implementation |

**Stack:** Vanilla JavaScript, Canvas API  
**Run locally:** Open `kovah.html` in a browser — no build step needed.

---

### clodapp — Platform Landing Page

Auth0-authenticated React SPA that serves as the landing page for the personal cloud platform at `kamradtfamily.net`. Shows login/logout state and displays Auth0 identity fields and access token claims. Deployed via ArgoCD to the K3s cluster.

| Repo | Description |
|------|-------------|
| [clodapp](../clodapp) | React 18 + Vite SPA, nginx-served |

**Stack:** React 18 + Vite, Auth0, nginx, Docker, ArgoCD  
**Run locally:** `npm install && npm run dev` — requires `.env` with `VITE_AUTH0_DOMAIN` and `VITE_AUTH0_CLIENT_ID`.  
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

simcity-lite
  └── simcity-backend          (Spring Boot)
  └── simcity-frontend         (React)

groceryai
  └── Spring Boot backend
  └── nutrition-ui             (React frontend)
  └── USDA FoodData Central API

dccex-throttle                 (standalone Node.js proxy)
gol                            (standalone single-file HTML)
clodapp                        (standalone React SPA)
portfolio                      (standalone static HTML)

ollama (llm namespace)
  └── rkamradt-helm-charts/ollama  (Helm chart + PVC)
```

---

## Common Patterns

- **Frontend:** React apps served behind nginx in Docker
- **Backend APIs:** Node.js or Spring Boot, containerized with Docker
- **Persistence:** MongoDB
- **Deployment:** Kubernetes manifests live in dedicated `-deploy` repos
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
| [gol](../gol) | JavaScript | Game of Life visualization |
| [clodapp](../clodapp) | JavaScript/React | Platform landing page |
| [portfolio](../portfolio) | HTML/CSS | Professional profile site |
| [rkamradt-helm-charts/ollama](../rkamradt-helm-charts/ollama) | YAML/Helm | LLM inference server |
