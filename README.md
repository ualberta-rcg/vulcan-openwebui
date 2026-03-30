# University of Alberta - Vulcan Open WebUI Deployment  

![Kubernetes](https://img.shields.io/badge/Kubernetes-1.24+-green?style=flat-square)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)

**Maintained by:** Rahim Khoja ([khoja1@ualberta.ca](mailto:khoja1@ualberta.ca))

---

## Description

Kubernetes deployment configuration for **Open WebUI** on the Vulcan RKE2 cluster. Production-hardened with Qdrant vector database, PostgreSQL, Redis, OAuth/OIDC authentication, and Kubeflow inference endpoints.

**Key Features:**
- **Auto-scaling deployment** (2-15 replicas via HPA) with zero-downtime rolling updates and pod anti-affinity
- **Qdrant** vector database for scalable RAG embeddings (Helm-deployed)
- **PostgreSQL 16** for application data
- **Redis** for session management and websocket coordination
- **OAuth/OIDC** authentication via Digital Research Alliance of Canada
- **RAG** with hybrid search, BGE-M3 embeddings, and reranking via Kubeflow inference
- **Apache Tika** for document extraction with HPA (1-4 replicas)
- **NetworkPolicies** restricting backend access to OpenWebUI pods only
- **Traefik IngressRoute** with ServersTransport for WebSocket/streaming support

---

## Architecture

### Services
| Service | Image | Replicas | Purpose |
|---|---|---|---|
| Open WebUI | `ghcr.io/open-webui/open-webui:latest` | 2-15 (HPA) | Main application |
| Qdrant | `qdrant/qdrant` (Helm) | 5 (clustered) | Vector database for RAG |
| PostgreSQL | `postgres:16` | 1 | Application database |
| Redis | `redis:latest` | 1 | Sessions, websockets, cache |
| Apache Tika | `apache/tika:latest-full` | 1-4 (HPA) | Document extraction |

### Infrastructure
- **Namespace:** `openwebui`
- **Storage:** NFS-backed PVCs via `nfs-client` StorageClass
- **Ingress:** Traefik IngressRoute with ServersTransport (300s timeouts, HTTP/2 disabled for WebSocket)
- **TLS:** cert-manager with Let's Encrypt DNS validation

### Network Security
All backend services are protected by NetworkPolicies:
- `postgres-access` — port 5432, OpenWebUI pods only
- `redis-access` — port 6379, OpenWebUI pods only
- `qdrant-access` — ports 6333/6334 (OpenWebUI pods + Prometheus), port 6335 (Qdrant p2p for Raft consensus)
- `tika-access` — port 9998, OpenWebUI pods only

---

## Deployment

### Prerequisites

- RKE2 Kubernetes cluster with Rancher
- Traefik ingress controller (with `kubernetescrd` provider enabled)
- cert-manager with a `ClusterIssuer` named `letsencrypt-dns`
- `nfs-client` StorageClass
- Cilium CNI (for NetworkPolicy enforcement)
- Helm 3.x
- Qdrant Helm repo: `helm repo add qdrant https://qdrant.to/helm`

### Files

| File | Description |
|---|---|
| `openwebui-secrets.yaml` | Kubernetes Secret (template with `CHANGEME` placeholders) — apply first |
| `openwebui.yaml` | Full deployment manifest (deployments, services, PVCs, network policies, ingress) |
| `qdrant-values.yaml` | Helm values for the Qdrant vector database |
| `.gitignore` | Prevents accidental commit of sensitive files |

### Step 1: Configure and Apply Secrets

Edit `openwebui-secrets.yaml` and replace all `CHANGEME` values:

| Key | Description |
|---|---|
| `webui-secret-key` | Random string for session encryption (must be consistent across replicas) |
| `postgres-password` | PostgreSQL password |
| `database-url` | Connection string (see below) |
| `redis-password` | Redis password |
| `redis-url` | Connection string (see below) |
| `qdrant-api-key` | API key for Qdrant authentication |
| `oauth-client-secret` | OIDC client secret from identity provider |
| `perplexity-api-key` | Perplexity API key for web search |
| `youtube-loader-api-key` | YouTube Data API key |
| `openai-api-key` | OpenAI-compatible API key (or placeholder) |
| `inference-api-key` | Kubeflow inference API key (or placeholder) |

> **About the URL secrets:** `database-url` and `redis-url` are stored as secrets because they embed the password. The URL format itself is known — only the password portion is sensitive. Construct them from the passwords you chose:
>
> ```
> database-url = postgresql://openwebui:<POSTGRES_PASSWORD>@postgres:5432/openwebui
> redis-url    = redis://default:<REDIS_PASSWORD>@redis:6379/0
> ```
>
> For example, if your `postgres-password` is `myS3cretPW`, then `database-url` is `postgresql://openwebui:myS3cretPW@postgres:5432/openwebui`. The same applies to `redis-url` with `redis-password`.

Then apply:

```bash
kubectl apply -f openwebui-secrets.yaml
```

This creates the namespace and the `openwebui-secrets` Secret. Both Qdrant (Helm) and OpenWebUI reference this secret via `secretKeyRef`, so it **must exist before either is deployed**.

### Step 2: Deploy Qdrant via Helm

```bash
helm repo add qdrant https://qdrant.to/helm
helm repo update
helm install qdrant qdrant/qdrant -n openwebui -f qdrant-values.yaml
```

Qdrant reads its API key from the `openwebui-secrets` secret (via `secretKeyRef`), so Step 1 must be done first.

### Step 3: Deploy OpenWebUI and Services

```bash
kubectl apply -f openwebui.yaml
```

This creates PVCs, all deployments (Postgres, Redis, Tika, OpenWebUI), services, NetworkPolicies, and Traefik ingress resources.

> **First-time deploy note:** PVCs are created on first apply. If re-applying, existing PVCs are left untouched (immutable). New sites should use `ReadWriteMany` for `openwebui-data` if your StorageClass supports it — see the comment in the manifest.

### Step 4: Verify

```bash
# All pods running
kubectl get pods -n openwebui

# OpenWebUI health
kubectl exec -n openwebui deploy/openwebui -- curl -s http://localhost:8080/health

# HPA status
kubectl get hpa -n openwebui

# Qdrant health and cluster status
kubectl exec -n openwebui deploy/openwebui -- curl -s http://qdrant:6333/healthz
kubectl exec -n openwebui deploy/openwebui -- curl -s http://qdrant:6333/cluster \
  -H "api-key: $(kubectl get secret openwebui-secrets -n openwebui -o jsonpath='{.data.qdrant-api-key}' | base64 -d)"

# Qdrant collections (after uploading a document)
kubectl exec -n openwebui deploy/openwebui -- curl -s \
  -H "api-key: $(kubectl get secret openwebui-secrets -n openwebui -o jsonpath='{.data.qdrant-api-key}' | base64 -d)" \
  http://qdrant:6333/collections
```

---

## Upgrading

### Qdrant

```bash
helm upgrade qdrant qdrant/qdrant -n openwebui -f qdrant-values.yaml
```

### OpenWebUI and other services

```bash
kubectl apply -f openwebui.yaml
```

> If changing env vars from `value` to `valueFrom` (or vice versa), use `kubectl replace -f openwebui.yaml` instead — Kubernetes strategic merge can't handle that transition.

### Rollout restart (pull latest images)

```bash
kubectl rollout restart deploy/openwebui -n openwebui
```

---

## Configuration

### Key Environment Variables

| Category | Variable | Description |
|---|---|---|
| **Auth** | `OAUTH_CLIENT_ID` | OIDC client identifier |
| **Auth** | `OPENID_PROVIDER_URL` | OIDC discovery endpoint |
| **Vector DB** | `VECTOR_DB=qdrant` | Use Qdrant for embeddings |
| **Vector DB** | `QDRANT_URI` | Qdrant HTTP endpoint |
| **Vector DB** | `ENABLE_QDRANT_MULTITENANCY_MODE` | Shared collections with resource isolation |
| **RAG** | `RAG_EMBEDDING_ENGINE=openai` | Embedding via Kubeflow inference |
| **RAG** | `RAG_EMBEDDING_MODEL=bge-m3` | BGE-M3 multilingual embeddings |
| **RAG** | `ENABLE_RAG_HYBRID_SEARCH` | BM25 + vector hybrid search |
| **Cache** | `REDIS_URL` | Redis connection for caching |
| **Cache** | `WEBSOCKET_MANAGER=redis` | Redis-backed websocket coordination |
| **Search** | `WEB_SEARCH_ENGINE=perplexity_search` | Perplexity for web search |
| **Audio** | `AUDIO_STT_ENGINE=openai` | Whisper via Kubeflow inference |
| **Audio** | `AUDIO_TTS_ENGINE=openai` | Kokoro TTS via Kubeflow inference |
| **Images** | `IMAGE_GENERATION_ENGINE=openai` | Kandinsky-3 via Kubeflow inference |

### Qdrant Notes

Qdrant runs as a 5-node Raft cluster (one per node) on NFS storage (`nfs-client`). It logs a warning about NFS filesystem checks on startup — this is expected and documented in `qdrant-values.yaml`. Snapshot persistence is enabled as a recovery safety net.

> **Important:** The `qdrant-access` NetworkPolicy must allow p2p traffic (port 6335) between Qdrant pods for Raft consensus. This is already configured in `openwebui.yaml`.

---

## Customization for Other Sites

This deployment is the live Vulcan configuration. Other Alliance sites (or any Kubernetes environment) can use it as a starting point by modifying values marked with `# SITE-SPECIFIC` comments in the manifests. Here's a summary of what to change:

### `openwebui.yaml`

| Section | What to Change |
|---|---|
| **`hostAliases`** | Remove or replace with your internal DNS entries (LDAP servers, etc.). Not needed if your cluster DNS handles these. |
| **`WEBUI_NAME`** | Your instance name (shown in the UI title bar) |
| **`WEBUI_URL`** | Your public HTTPS URL |
| **OAuth / OIDC block** | `OAUTH_CLIENT_ID`, `OPENID_PROVIDER_URL`, `OAUTH_PROVIDER_NAME`, `OAUTH_SCOPES`, `OPENID_REDIRECT_URI`, `OAUTH_EMAIL_CLAIM` — all depend on your identity provider. If not using OIDC, set `ENABLE_LOGIN_FORM=true` and remove the OAuth env vars. |
| **Inference endpoints** | `RAG_OPENAI_API_BASE_URL`, `AUDIO_STT_OPENAI_API_BASE_URL`, `AUDIO_TTS_OPENAI_API_BASE_URL`, `OPENAI_API_BASE_URL`, `IMAGES_OPENAI_API_BASE_URL`, `IMAGES_EDIT_OPENAI_API_BASE_URL` — point these at your OpenAI-compatible inference service, or use the real OpenAI API (`https://api.openai.com/v1`). |
| **Model names** | `RAG_EMBEDDING_MODEL`, `AUDIO_STT_MODEL`, `AUDIO_TTS_MODEL`, `IMAGE_GENERATION_MODEL`, `IMAGE_EDIT_MODEL` — match the models available at your inference endpoints. |
| **`storageClassName`** | All PVCs use `nfs-client`. Change to your cluster's StorageClass. |
| **IngressRoute** | Change `Host(...)` match and `tls.secretName` to your domain. |
| **Certificate** | Change `commonName`, `dnsNames`, and `issuerRef` to match your domain and cert-manager setup. |
| **Secrets** | All `CHANGEME` values — these are always site-specific. |

### `qdrant-values.yaml`

| Section | What to Change |
|---|---|
| **`replicaCount`** | Number of Qdrant nodes. Vulcan uses 5 (one per node). Adjust to match your cluster size, or use 1 for small deployments (set `config.cluster.enabled: false` if single-node). |
| **`storageClassName`** | Both `persistence` and `snapshotPersistence` — match your cluster. |
| **`metrics.serviceMonitor.additionalLabels`** | `release: rancher-monitoring` — match your Prometheus operator's label selector. Remove the entire `metrics` block if you don't use Prometheus. |

### Optional: Features to Remove

If your site doesn't have certain capabilities, you can safely remove these env var blocks:

- **Image generation/editing** — remove `ENABLE_IMAGE_GENERATION`, `IMAGE_*`, `IMAGES_*` vars
- **Audio STT/TTS** — remove `AUDIO_*` vars and `ENABLE_VOICE_MODE` / `VOICE_MODE_*` vars
- **Web search** — remove `ENABLE_WEB_SEARCH`, `WEB_SEARCH_ENGINE`, `PERPLEXITY_API_KEY` vars
- **YouTube loader** — remove `ENABLE_YOUTUBE_LOADER`, `YOUTUBE_LOADER_API_KEY` vars

---

## Backup and Restore

### PostgreSQL

**Backup:**
```bash
kubectl exec -n openwebui deploy/postgres -- \
  pg_dump -U openwebui -d openwebui -Fc > openwebui-pg-backup-$(date +%Y%m%d).dump
```

**Restore:**
```bash
kubectl exec -i -n openwebui deploy/postgres -- \
  pg_restore -U openwebui -d openwebui --clean --if-exists < openwebui-pg-backup-YYYYMMDD.dump
```

### Qdrant

Qdrant snapshot persistence is enabled in `qdrant-values.yaml`. To create a manual snapshot of all collections:

```bash
kubectl exec -n openwebui deploy/openwebui -- \
  curl -s -X POST http://qdrant:6333/snapshots \
  -H "api-key: $(kubectl get secret openwebui-secrets -n openwebui -o jsonpath='{.data.qdrant-api-key}' | base64 -d)"
```

Snapshots are stored on the `qdrant-snapshots` PVC. To list available snapshots:

```bash
kubectl exec -n openwebui deploy/openwebui -- \
  curl -s http://qdrant:6333/snapshots \
  -H "api-key: $(kubectl get secret openwebui-secrets -n openwebui -o jsonpath='{.data.qdrant-api-key}' | base64 -d)"
```

> **Note:** If Qdrant data is lost, you can also re-index from OpenWebUI: Admin Settings > Documents > Reindex Knowledge Base. This rebuilds vectors from the source documents stored in PostgreSQL.

### OpenWebUI Data Volume

The `openwebui-data` PVC holds uploaded files and cache. Back up with:

```bash
kubectl exec -n openwebui deploy/openwebui -- \
  tar czf - /app/backend/data > openwebui-data-backup-$(date +%Y%m%d).tar.gz
```

---

## Troubleshooting

### Check pod status and logs
```bash
kubectl get pods -n openwebui
kubectl logs deploy/openwebui -n openwebui --tail=50
kubectl logs deploy/postgres -n openwebui --tail=50
kubectl logs deploy/redis -n openwebui --tail=20
kubectl logs qdrant-0 -n openwebui --tail=20
```

### Verify Postgres
```bash
kubectl exec -n openwebui deploy/postgres -- psql -U openwebui -d openwebui -c "SELECT version();"
kubectl exec -n openwebui deploy/postgres -- pg_isready -U openwebui
```

### Verify Redis
```bash
kubectl exec -n openwebui deploy/redis -- sh -c 'redis-cli -a "$REDIS_PASSWORD" ping'
```

### Verify Qdrant connectivity from OpenWebUI
```bash
kubectl exec -n openwebui deploy/openwebui -- curl -s http://qdrant:6333/healthz
```

### Check auto-scaling
```bash
# HPA status and current replica counts
kubectl get hpa -n openwebui

# Current resource usage per pod
kubectl top pods -n openwebui
```

### Verify Qdrant cluster
```bash
# All 5 nodes should show Ready
kubectl get pods -n openwebui -l app.kubernetes.io/name=qdrant

# Raft cluster status (check all peers connected, leader elected)
kubectl exec -n openwebui deploy/openwebui -- curl -s http://qdrant:6333/cluster \
  -H "api-key: $(kubectl get secret openwebui-secrets -n openwebui -o jsonpath='{.data.qdrant-api-key}' | base64 -d)"
```

### Check Traefik routing
```bash
kubectl get ingressroute -n openwebui
kubectl get serverstransport -n openwebui
```

### Postgres WAL recovery
If Postgres fails with `PANIC: could not locate a valid checkpoint record` after an unclean shutdown on NFS:
```bash
kubectl scale deploy postgres -n openwebui --replicas=0
kubectl run pg-repair --rm -it --restart=Never -n openwebui \
  --image=postgres:16 \
  --overrides='{"spec":{"containers":[{"name":"pg-repair","image":"postgres:16","command":["bash","-c","su - postgres -c \"/usr/lib/postgresql/16/bin/pg_resetwal -f /var/lib/postgresql/data\""],"volumeMounts":[{"name":"data","mountPath":"/var/lib/postgresql/data"}]}],"volumes":[{"name":"data","persistentVolumeClaim":{"claimName":"postgres-data"}}]}}'
kubectl scale deploy postgres -n openwebui --replicas=1
```

---

## References

* [Open WebUI Documentation](https://docs.openwebui.com/)
* [Open WebUI GitHub](https://github.com/open-webui/open-webui)
* [Qdrant Documentation](https://qdrant.tech/documentation/)
* [Qdrant Helm Chart](https://github.com/qdrant/qdrant-helm)
* [Digital Research Alliance of Canada](https://alliancecanada.ca/)
* [PAICE (Pan-Canadian AI Compute Environment)](https://alliancecan.ca/en/services/advanced-research-computing/pan-canadian-ai-compute-environment-paice)
* [Research Computing Group](https://www.ualberta.ca/en/information-services-and-technology/research-computing/index.html)

---

## Support

Many Bothans died to bring us this information. This project is provided as-is, but reasonable questions may be answered based on my coffee intake or mood. ;)

Feel free to open an issue or email **[khoja1@ualberta.ca](mailto:khoja1@ualberta.ca)** or **[kali2@ualberta.ca](mailto:kali2@ualberta.ca)** for U of A related deployments.

---

## License

This project is released under the **MIT License** — see [LICENSE](./LICENSE) for details.

---

## About University of Alberta Research Computing

The [Research Computing Group](https://www.ualberta.ca/en/information-services-and-technology/research-computing/index.html) supports high-performance computing, data-intensive research, and advanced infrastructure for researchers at the University of Alberta and across Canada.
