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

## Project Dependency Map

```
blackbook-deploy
  └── blackbook          (React frontend)
  └── blackbook-read-profile  (Node.js API)
  └── MongoDB

javaman-client
  └── javaman            (Node.js backend)

naivecoin-run
  └── naivecoin          (blockchain core)
  └── naivefront         (React UI)
  └── naivetest          (integration tests)
  └── naiveperf          (perf tests)

news-deploy
  └── readnews           (Spring Boot service)
  └── readnewsperf       (Gatling perf tests)
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
