# Deploying to the Kamradt Cluster

This document describes the full pipeline for getting a new service running at
`*.kamradtfamily.net`. Every showcase project in this collection follows the same
pattern end-to-end.

---

## Architecture Overview

```
GitHub repo  →  GitHub Actions  →  ghcr.io image
                     ↓
              rkamradt-helm-charts  (version-values.yaml updated by CI)
                     ↓
              ArgoCD  (watches helm charts repo, auto-syncs)
                     ↓
              K3s cluster  (single node "servy", Rockport ME)
                     ↓
              Cloudflare Tunnel  →  *.kamradtfamily.net
```

**Key repositories:**
- `rkamradt-helm-charts` — source of truth for all deployments; ArgoCD watches this
- `rkamradt-platform` — platform-level services (Kafka, MongoDB, auth, etc.)
- Individual service repos — each has its own Dockerfile and GitHub Actions workflow

**ArgoCD** is configured with automated sync, self-heal, and prune. Pushing to
`rkamradt-helm-charts/main` is all that's needed to deploy.

**Cloudflare Tunnel** is token-based. All routing is configured in the Cloudflare
Zero Trust dashboard under **Published application routes** — there is nothing to
configure in the cluster. The tunnel fetches its routing config from Cloudflare's API.

---

## Adding a New Service — Full Checklist

### 1. Create the GitHub repo

```bash
curl -s -X POST \
  -H "Authorization: token $GITHUB_PAT" \
  -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/user/repos \
  -d '{"name":"my-service","private":false,"auto_init":false}'
```

### 2. Write the app and Dockerfile

For **Node.js** services:
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package.json ./
RUN npm install --production
COPY server.js ./
COPY public/ ./public/
EXPOSE 3000
CMD ["node", "server.js"]
```

For **static sites** (nginx):
```dockerfile
FROM nginx:alpine
COPY public/ /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 3. Add GitHub Actions workflow

Create `.github/workflows/build.yml`. The pattern is identical for every service —
only the helm chart path and port differ:

```yaml
name: Build and Push

on:
  push:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - uses: actions/checkout@v4

      - name: Set image tag
        id: tag
        run: echo "tag=sha-${GITHUB_SHA::7}" >> $GITHUB_OUTPUT

      - uses: docker/login-action@v4
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: |
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ steps.tag.outputs.tag }}
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest

      - name: Update helm chart version
        run: |
          git clone https://x-access-token:${{ secrets.HELM_CHARTS_PAT }}@github.com/rkamradt/rkamradt-helm-charts.git
          cd rkamradt-helm-charts
          printf 'image:\n  tag: "%s"\n' "${{ steps.tag.outputs.tag }}" > my-service/version-values.yaml
          git config user.email "actions@github.com"
          git config user.name "GitHub Actions"
          git add my-service/version-values.yaml
          git commit -m "Update my-service image tag to ${{ steps.tag.outputs.tag }}"
          git push
```

### 4. Set the HELM_CHARTS_PAT secret on the new repo

The workflow needs write access to `rkamradt-helm-charts`. Use PyNaCl to encrypt
the PAT before sending it to the GitHub Secrets API:

```python
import subprocess, json, os
from base64 import b64encode
from nacl import encoding, public
import urllib.request

r = subprocess.run(['curl','-s','-H',f'Authorization: token {os.environ["GITHUB_PAT"]}',
  f'https://api.github.com/repos/rkamradt/my-service/actions/secrets/public-key'],
  capture_output=True, text=True)
d = json.loads(r.stdout)
enc = b64encode(
  public.SealedBox(public.PublicKey(d['key'].encode(), encoding.Base64Encoder()))
  .encrypt(os.environ['GITHUB_PAT'].encode())
).decode()
req = urllib.request.Request(
  'https://api.github.com/repos/rkamradt/my-service/actions/secrets/HELM_CHARTS_PAT',
  data=json.dumps({"encrypted_value": enc, "key_id": d['key_id']}).encode(),
  method='PUT',
  headers={'Authorization': f'token {os.environ["GITHUB_PAT"]}',
           'Accept': 'application/vnd.github.v3+json',
           'Content-Type': 'application/json'})
with urllib.request.urlopen(req) as resp:
    print(resp.status)  # 201 = created, 204 = updated
```

> **Note:** `pip3 install PyNaCl` if not already installed.

### 5. Create the helm chart in `rkamradt-helm-charts`

Create `my-service/` with this structure:

```
my-service/
  Chart.yaml
  values.yaml
  version-values.yaml    ← CI overwrites this on every build
  templates/
    deployment.yaml
    service.yaml
```

**Chart.yaml:**
```yaml
apiVersion: v2
name: my-service
description: One-line description
type: application
version: 0.1.0
appVersion: "latest"
```

**values.yaml** (Node.js app on port 3000):
```yaml
replicaCount: 1

image:
  repository: ghcr.io/rkamradt/my-service
  tag: "latest"
  pullPolicy: Always

imagePullSecrets:
  - name: ghcr-secret

service:
  type: ClusterIP
  port: 3000
  targetPort: 3000

resources:
  requests:
    memory: 64Mi
    cpu: 10m
  limits:
    memory: 128Mi
    cpu: 200m
```

**version-values.yaml** (placeholder; CI will overwrite):
```yaml
image:
  tag: "latest"
```

**templates/deployment.yaml** and **templates/service.yaml** — copy from any
existing service in the repo (e.g. `portfolio/` for nginx, `home/` for Node.js).

If the service needs **environment variables**, add to `values.yaml`:
```yaml
env:
  MY_VAR: "value"
```
And add to `deployment.yaml` under `containers`:
```yaml
          env:
            {{- range $key, $val := .Values.env }}
            - name: {{ $key }}
              value: {{ $val | quote }}
            {{- end }}
```

If the service needs **Kubernetes API access** (read-only), also add:
- `templates/serviceaccount.yaml`
- `templates/clusterrole.yaml`
- `templates/clusterrolebinding.yaml`

And in `deployment.yaml` add `serviceAccountName: {{ .Chart.Name }}` under `spec.template.spec`.
See `k8s-dashboard/` for a complete example.

### 6. Register in ArgoCD

Add an entry to `apps/values.yaml` in `rkamradt-helm-charts`:

```yaml
  - name: my-service
    path: my-service
    namespace: my-service
    versionValuesFile: version-values.yaml
```

ArgoCD will create the namespace automatically (`CreateNamespace=true` is already
set in the apps helm chart).

### 7. Copy the ghcr-secret into the new namespace

ArgoCD creates the namespace, but the image pull secret must be copied manually
(or via a bootstrap script):

```bash
kubectl get secret ghcr-secret -n portfolio -o json | python3 -c "
import sys, json
s = json.load(sys.stdin)
del s['metadata']['creationTimestamp']
del s['metadata']['resourceVersion']
del s['metadata']['uid']
s['metadata']['namespace'] = 'my-service'
print(json.dumps(s))
" | kubectl apply -f -
```

> **Why is this manual?** The `ghcr-secret` contains credentials that shouldn't
> live in the public helm charts repo. A future improvement would be a cluster-level
> secret sync (e.g. Reflector or ESO).

### 8. Push everything and wait

```bash
# Push the helm chart
cd rkamradt-helm-charts && git add . && git commit -m "Add my-service" && git push

# Push the service repo (triggers GitHub Actions)
cd my-service && git init && git add -A && git commit -m "Initial commit"
git remote add origin https://x-access-token:${GITHUB_PAT}@github.com/rkamradt/my-service.git
git push -u origin main
```

ArgoCD polls every 3 minutes. To sync immediately:
```bash
kubectl annotate application my-service -n argocd argocd.argoproj.io/refresh=normal
```

### 9. Add the Cloudflare tunnel route

In the Cloudflare Zero Trust dashboard:
**Networks → Tunnels → [tunnel name] → Published application routes → Add a route**

| Field | Value |
|-------|-------|
| Subdomain | `my-service` |
| Domain | `kamradtfamily.net` |
| Service | `http://my-service.my-service:PORT` |

The service URL format is `http://<service-name>.<namespace>:<port>` — standard
Kubernetes DNS within the cluster.

---

## Gotchas

**HELM_CHARTS_PAT push fails with "Invalid username or token"**  
The secret was encrypted against a stale public key. Re-run the PyNaCl encryption
script (step 4) against the *current* public key — GitHub rotates them. A 204
response confirms an update; 201 means it was newly created.

**Pod stuck in `ImagePullBackOff`**  
The `ghcr-secret` is missing in the namespace. Run step 7.

**ArgoCD shows the app as `OutOfSync / Missing` immediately after registering**  
Normal — ArgoCD hasn't synced yet. Annotate to force a refresh (step 8) or wait
up to 3 minutes.

**ArgoCD reverts manual `kubectl` changes**  
By design — ArgoCD self-heals. All config changes must go through
`rkamradt-helm-charts`. The one exception is one-time bootstrap operations like
copying secrets.

**Cloudflare tunnel routing is not in the cluster**  
The tunnel is token-based. `cloudflared` inside the cluster fetches all routing
config from Cloudflare's API. There are no Kubernetes Ingress resources. All routing
changes happen in the Cloudflare Zero Trust dashboard.

---

## Services Currently Running

| Service | Subdomain | Namespace | Port | Repo |
|---------|-----------|-----------|------|------|
| ORACLE Chat | `www` | `home` | 3000 | `rkamradt/home` |
| Portfolio / Profile | `profile` | `portfolio` | 80 | `rkamradt/portfolio` |
| Cluster Dashboard | `cluster` | `k8s-dashboard` | 3000 | `rkamradt/k8s-dashboard` |
| Game of Life | `life` | `gol` | 80 | `rkamradt/gol` |
| Ollama (LLM) | — | `llm` | 11434 | `rkamradt-helm-charts/ollama` |
| ArgoCD | `argocd` | `argocd` | — | platform |
