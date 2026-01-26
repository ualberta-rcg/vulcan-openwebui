# University of Alberta - Vulcan Cluster

# Open WebUI Kubernetes Deployment  

![Kubernetes](https://img.shields.io/badge/Kubernetes-1.24+-green?style=flat-square)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)

**Maintained by:** Rahim Khoja ([khoja1@ualberta.ca](mailto:khoja1@ualberta.ca))

---

## Description

This repository contains the Kubernetes deployment configuration for **Open WebUI** - a production-ready, scalable deployment with PostgreSQL, Redis, LDAP authentication, RAG capabilities, and horizontal pod autoscaling.

The deployment provides a comprehensive Open WebUI setup suitable for HPC clusters and Kubernetes environments. This repository serves as both a working deployment template and a comprehensive reference that can be adapted for other Kubernetes clusters.

**Key Features:**
- **Multi-pod deployment** with horizontal pod autoscaling (2-10 replicas)
- **PostgreSQL database** for shared data storage across pods
- **Redis** for session management and websocket coordination
- **LDAP authentication** integrated with LDAP servers
- **RAG (Retrieval Augmented Generation)** with hybrid search enabled
- **Web search** via Perplexity AI
- **Voice mode** with local Whisper STT
- **Persistent storage** for application data and databases

---

## Architecture

The deployment consists of the following components:

### Core Services
- **OpenWebUI**: Main application deployment (2-10 pods, auto-scaling)
- **PostgreSQL 16**: Shared database for all OpenWebUI pods (1 replica) with pgvector extension
- **Redis 7.2**: Session storage and websocket manager (1 replica)
- **Apache Tika**: Content extraction engine for RAG document processing (1 replica)

### Infrastructure
- **Namespace**: `openwebui`
- **Storage**: NFS-based persistent volumes (50Gi for app data, 20Gi for PostgreSQL, 1Gi for Redis)
- **Ingress**: Traefik with sticky sessions enabled
- **Autoscaling**: HorizontalPodAutoscaler based on CPU (70%) and memory (80%) utilization

### Authentication
- **LDAP**: Integrated with LDAP servers (LDAPS)
  - Primary and secondary LDAP servers configured via hostAliases
  - Base DN: `dc=example,dc=com` (configure as needed)
  - Username attribute: `uid`

---

## Deployment

### Prerequisites

- Kubernetes cluster (RKE2 tested)
- Traefik ingress controller
- NFS storage class (`nfs-client`)
- Access to LDAP servers (if using LDAP authentication)

### Quick Start

1. **Update secrets** - Edit the `openwebui-secrets` in `openwebui.yaml`:
   - `webui-secret-key`: Generate a secure random key for session encryption
   - `postgres-password`: Set a strong PostgreSQL password
   - `database-url`: Update with your PostgreSQL password (must match postgres-password)
   - `perplexity-api-key`: Add your Perplexity API key (if using web search)
   - `youtube-loader-api-key`: Add your YouTube API key (if using YouTube loader)

2. **Deploy**:
   ```bash
   kubectl apply -f openwebui.yaml
   ```

3. **Verify deployment**:
   ```bash
   kubectl get pods -n openwebui
   kubectl get svc -n openwebui
   ```

4. **Check database connection**:
   ```bash
   kubectl exec -n openwebui <openwebui-pod> -- env | grep DATABASE_URL
   kubectl logs -n openwebui <openwebui-pod> | grep -i postgres
   ```

### Configuration

Key environment variables configured:
- `WEBUI_SECRET_KEY`: Required for multi-pod session sharing
- `DATABASE_URL`: PostgreSQL connection string
- `REDIS_URL`: Redis connection for caching and sessions
- `WEBSOCKET_REDIS_URL`: Redis connection for websocket coordination
- `VECTOR_DB`: pgvector for RAG embeddings
- `ENABLE_LDAP`: LDAP authentication enabled
- `ENABLE_RAG`: RAG features enabled
- `ENABLE_WEB_SEARCH`: Web search via Perplexity enabled
- `CONTENT_EXTRACTION_ENGINE`: Apache Tika for document processing
- `ENABLE_IMAGE_GENERATION`: Image generation via AUTOMATIC1111 enabled

### Scaling

The deployment automatically scales based on resource usage:
- **Minimum replicas**: 2
- **Maximum replicas**: 10
- **Scale triggers**: CPU > 70% or Memory > 80%
- **Scale down**: 50% reduction per minute (5 min stabilization)
- **Scale up**: 100% increase per 30 seconds or +2 pods per 30 seconds

---

## Features Enabled

### Core Features
- ✅ LDAP authentication (configurable LDAP servers)
- ✅ RAG with hybrid search (BM25 + vector) using pgvector
- ✅ Apache Tika for document content extraction (OCR support)
- ✅ Web search (Perplexity AI)
- ✅ Image generation (AUTOMATIC1111 compatible)
- ✅ Voice mode (local Whisper STT)
- ✅ Model caching (base models and TTL-based)
- ✅ Workspaces and sharing
- ✅ API keys
- ✅ Community sharing
- ✅ YouTube loader for transcript extraction
- ✅ Google Drive and OneDrive integration (user-configured)

### Storage & Persistence
- ✅ PostgreSQL 16 with pgvector extension for database and vector storage
- ✅ Redis for sessions and websockets (shared across pods)
- ✅ Persistent volumes for application data (50Gi), PostgreSQL (20Gi), and Redis (1Gi)

### Security
- ✅ LDAP authentication (signup disabled)
- ✅ Shared secret key for session encryption
- ✅ Sticky sessions via Traefik
- ✅ Pod disruption budget (min 1 available)

---

## Troubleshooting

### Verify PostgreSQL is being used
```bash
kubectl exec -n openwebui <pod> -- env | grep DATABASE_URL
kubectl logs -n openwebui <pod> | grep -i "PostgresqlImpl"
```

### Check Redis connections
```bash
kubectl exec -n openwebui redis-<pod> -- redis-cli -a YOUR_REDIS_PASSWORD_HERE ping
```

### Check PostgreSQL connections
```bash
kubectl exec -n openwebui postgres-<pod> -- psql -U openwebui -d openwebui -c "SELECT count(*) FROM pg_stat_activity WHERE datname = 'openwebui';"
```

### View pod logs
```bash
kubectl logs -n openwebui <openwebui-pod> --tail=50
kubectl logs -n openwebui postgres-<pod>
kubectl logs -n openwebui redis-<pod>
kubectl logs -n openwebui tika-<pod>
```

### Check secrets
```bash
kubectl get secret -n openwebui openwebui-secrets -o yaml
```

---

## References

* [Open WebUI Documentation](https://docs.openwebui.com/)
* [Open WebUI GitHub](https://github.com/open-webui/open-webui)
* [Digital Research Alliance of Canada](https://alliancecanada.ca/)
* [PAICE (Pan-Canadian AI Compute Environment)](https://alliancecan.ca/en/services/advanced-research-computing/pan-canadian-ai-compute-environment-paice)
* [Research Computing Group](https://www.ualberta.ca/en/information-services-and-technology/research-computing/index.html)

---

## 🤝 Support

Many Bothans died to bring us this information. This project is provided as-is, but reasonable questions may be answered based on my coffee intake or mood. ;)

Feel free to open an issue or email **[khoja1@ualberta.ca](mailto:khoja1@ualberta.ca)** or **[kali2@ualberta.ca](mailto:kali2@ualberta.ca)** for U of A related deployments.

---

## 📜 License

This project is released under the **MIT License** - one of the most permissive open-source licenses available.

**What this means:**
- ✅ Use it for anything (personal, commercial, whatever)
- ✅ Modify it however you want
- ✅ Distribute it freely
- ✅ Include it in proprietary software

**The only requirement:** Keep the copyright notice somewhere in your project.

**Full license text:** [MIT License](./LICENSE)

---

## 🧠 About University of Alberta Research Computing

The [Research Computing Group](https://www.ualberta.ca/en/information-services-and-technology/research-computing/index.html) supports high-performance computing, data-intensive research, and advanced infrastructure for researchers at the University of Alberta and across Canada.
