# Claude Code Instructions — misc-spine / rkamradt platform

This repo is a context map, not runnable code. Read it to understand the platform
before touching any service repo. All conventions below apply across every repo in
this collection.

---

## Platform overview

Single-node K3s cluster ("servy", Rockport ME). All services follow this pipeline:

```
GitHub repo → GitHub Actions → ghcr.io/rkamradt/<name>:sha-<7hex>
                                      ↓
                         rkamradt-helm-charts  (version-values.yaml patched by CI)
                                      ↓
                         ArgoCD  (auto-sync, self-heal, prune)
                                      ↓
                         K3s cluster
                                      ↓
                         Cloudflare Tunnel → *.kamradtfamily.net
```

**Key repos:**
- `rkamradt-helm-charts` — source of truth for all deployments; ArgoCD watches this
- `rkamradt-platform` — platform services (Kafka, MongoDB, auth, etc.)
- `misc-spine` — this repo; context map and deployment guide

---

## Adding a new service — mandatory reading

See **DEPLOYING.md** for the full checklist. Critical steps:

1. Dockerfile → GitHub Actions (`build.yml`) → push to ghcr.io
2. Helm chart in `rkamradt-helm-charts/<name>/` (copy `k8s-dashboard/` for K8s API access, `home/` for Ollama/SSE)
3. Add entry to `rkamradt-helm-charts/apps/values.yaml`
4. Set `HELM_CHARTS_PAT` secret on the new repo (PyNaCl script in DEPLOYING.md)
5. Copy `ghcr-secret` into the new namespace after ArgoCD creates it
6. Add Cloudflare route in Zero Trust dashboard (no Ingress resources in cluster)

---

## Conventions — follow these exactly

**Image tags:** `sha-${GITHUB_SHA::7}` — never `latest` as a deployed tag  
**Helm values:** base in `values.yaml`, CI overwrites `version-values.yaml` with the tag  
**Namespace:** matches service name exactly (`home` → namespace `home`)  
**Service DNS:** `http://<name>.<namespace>:<port>` for Cloudflare route  
**Ports:** Node.js → 3000, Python/FastAPI → 8000, nginx static → 80  
**Resources (baseline):** `requests: 64Mi/10m`, `limits: 128Mi/200m` — raise for Python  
**Python services:** use `startupProbe` with `failureThreshold: 30, periodSeconds: 5`
(Python + LangGraph startup is slow on this CPU-only node)  
**Secrets:** never commit; `ghcr-secret` is copied manually per namespace  
**ArgoCD changes:** all config must go through `rkamradt-helm-charts` — manual `kubectl` changes will be reverted  

---

## Services currently running

| Service | Subdomain | Namespace | Port | Stack |
|---------|-----------|-----------|------|-------|
| ORACLE Chat | `www` | `home` | 3000 | Node.js, Ollama SSE |
| Portfolio | `profile` | `portfolio` | 80 | Static HTML, nginx |
| Cluster Dashboard | `cluster` | `k8s-dashboard` | 3000 | Node.js, K8s API |
| Game of Life | `life` | `gol` | 80 | Vanilla JS, nginx |
| Cluster Ops Agent | `agent` | `cluster-ops-agent` | 8000 | Python, LangGraph, FastAPI |
| Ollama (LLM) | — | `llm` | 11434 | Internal only |
| Receipts | `receipts` | `receipts` | 8080 | Java 21, Spring Boot, Google OAuth2/Sheets/Drive, Anthropic API |
| JupyterLab | — | `jupyter` | 8888 | quay.io/jupyter/scipy-notebook, internal port-forward only, no auth |

**Ollama in-cluster:** `http://ollama.llm.svc.cluster.local:11434`, model `llama3.2:3b-instruct-q4_K_M`  
**Local dev tunnel:** `kubectl port-forward -n llm svc/ollama 11434:11434`

---

## K8s API access pattern (for services that need it)

Use `k8s-dashboard` and `cluster-ops-agent` as references.

```yaml
# ClusterRole — list only the resources the service actually needs
rules:
  - apiGroups: [""]
    resources: ["pods", "pods/log", "events", "namespaces"]
    verbs: ["get", "list", "watch"]
```

In-cluster: reads SA token from `/var/run/secrets/kubernetes.io/serviceaccount/token`  
Local dev: `kubectl proxy --port=8001`, set `K8S_PROXY_URL=http://localhost:8001`

---

## Cloudflare tunnel

Token-based — `cloudflared` in the cluster fetches routing config from Cloudflare's API.
**There are no Ingress resources.** All routing changes happen in the Cloudflare Zero Trust
dashboard: Networks → Tunnels → [tunnel] → Published application routes → Add a route.

---

## Public-image services (no build pipeline)

Some services use a public upstream image directly — no GitHub repo, no Actions workflow,
no `ghcr-secret`, no `versionValuesFile`. The helm chart just pins the image.
Examples: `jupyter` (quay.io/jupyter/scipy-notebook), `ollama`.
To upgrade, edit the image tag in `values.yaml` and push to helm-charts.

---

## Gotchas

- `HELM_CHARTS_PAT` must be re-encrypted if GitHub rotates the repo's public key (run the PyNaCl script again)
- Pod stuck in `ImagePullBackOff` → `ghcr-secret` missing from namespace (copy it)
- ArgoCD shows `OutOfSync` immediately after registering → normal, annotate to force refresh or wait 3 min
- `kubectl` changes to ArgoCD-managed resources are reverted automatically — always edit through helm charts
- Cloudflare tunnel routing is **not** in the cluster — don't look for Ingress resources
- If a pod errors with `invalid literal for int() with base 10: 'tcp://...'` on startup, Kubernetes is injecting a `<SERVICENAME>_PORT` env var that collides with the app's own config. Fix: add `enableServiceLinks: false` to the pod spec
