# Open WebUI Environment Variables Reference

**Source:** [Official Open WebUI Documentation](https://docs.openwebui.com/getting-started/env-configuration/)

**Last Updated:** Based on Open WebUI v0.7.1

## Important Notes

### PersistentConfig Variables
Some environment variables are marked as `PersistentConfig`. After the initial launch, these values are stored in the database and will **not** be updated from environment variables on restart. To force environment variables to always take precedence, set `ENABLE_PERSISTENT_CONFIG=False`.

**Warning:** When `ENABLE_PERSISTENT_CONFIG=False`, changes made in the Admin UI will **NOT** persist across restarts.

### Troubleshooting
If environment variable changes aren't reflected:
1. Set `ENABLE_PERSISTENT_CONFIG=False` (temporary fix)
2. Update via Admin UI (recommended)
3. Manually update the database (last resort)
4. Reset the persistent volume (clean install)

---

## Table of Contents

1. [App/Backend - General](#appbackend---general)
2. [App/Backend - AIOHTTP Client](#appbackend---aiohttp-client)
3. [App/Backend - Directories](#appbackend---directories)
4. [App/Backend - Logging](#appbackend---logging)
5. [App/Backend - Ollama](#appbackend---ollama)
6. [App/Backend - OpenAI](#appbackend---openai)
7. [App/Backend - Tasks](#appbackend---tasks)
8. [App/Backend - Code Execution](#appbackend---code-execution)
9. [App/Backend - Code Interpreter](#appbackend---code-interpreter)
10. [App/Backend - Direct Connections](#appbackend---direct-connections)
11. [App/Backend - Autocomplete](#appbackend---autocomplete)
12. [App/Backend - Evaluation Arena Model](#appbackend---evaluation-arena-model)
13. [App/Backend - Tags Generation](#appbackend---tags-generation)
14. [App/Backend - API Key Endpoint Restrictions](#appbackend---api-key-endpoint-restrictions)
15. [App/Backend - Model Caching](#appbackend---model-caching)
16. [Security Variables](#security-variables)
17. [Vector Database](#vector-database)
18. [RAG Content Extraction Engine](#rag-content-extraction-engine)
19. [Retrieval Augmented Generation (RAG)](#retrieval-augmented-generation-rag)
20. [Web Search](#web-search)
21. [Audio](#audio)
22. [Image Generation](#image-generation)
23. [OAuth](#oauth)
24. [LDAP](#ldap)
25. [SCIM](#scim)
26. [User Permissions](#user-permissions)
27. [Misc Environment Variables](#misc-environment-variables)

---

## App/Backend - General

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `WEBUI_URL` | str | `http://localhost:3000` | ✅ Yes | URL where Open WebUI is reachable. Needed for search engine support and OAuth/SSO. **Must be set before using OAuth/SSO.** |
| `ENABLE_SIGNUP` | bool | `True` | ✅ Yes | Toggles user account creation |
| `ENABLE_SIGNUP_PASSWORD_CONFIRMATION` | bool | `False` | ❌ No | If True, adds "Confirm Password" field to sign-up page |
| `WEBUI_ADMIN_EMAIL` | str | `''` | ❌ No | Email for admin account created automatically on first startup when no users exist |
| `WEBUI_ADMIN_PASSWORD` | str | `''` | ❌ No | Password for automatically created admin account. Must be used with `WEBUI_ADMIN_EMAIL` |
| `WEBUI_ADMIN_NAME` | str | `Admin` | ❌ No | Display name for automatically created admin account |
| `ENABLE_LOGIN_FORM` | bool | `True` | ✅ Yes | Toggles email, password, sign-in and "or" elements |
| `ENABLE_PASSWORD_AUTH` | bool | `True` | ❌ No | Allows password and SSO authentication to coexist. Set to False for SSO-only |
| `ENABLE_PERSISTENT_CONFIG` | bool | `True` | ❌ No | When False, forces Open WebUI to always use environment variables (ignoring database) |
| `WEBUI_NAME` | str | `Open WebUI` | ✅ Yes | Name displayed in the UI |
| `WEBUI_AUTH_TRUSTED_EMAIL_HEADER` | str | `''` | ❌ No | HTTP header name containing trusted email for authentication |
| `WEBUI_AUTH_TRUSTED_NAME_HEADER` | str | `''` | ❌ No | HTTP header name containing trusted name for authentication |
| `DEFAULT_USER_ROLE` | str | `user` | ❌ No | Default role assigned to new users |
| `DISABLE_PROVIDER_EDIT` | bool | `False` | ✅ Yes | Disables provider editing in the UI |
| `ENABLE_COMMUNITY_SHARING` | bool | `True` | ✅ Yes | Enables community sharing features |
| `ENABLE_MESSAGE_RATING` | bool | `True` | ✅ Yes | Enables message rating functionality |
| `ENABLE_COMMENT_ON_SHARED_CHAT` | bool | `True` | ✅ Yes | Enables comments on shared chats |
| `ENABLE_API_KEY` | bool | `True` | ✅ Yes | Enables API key functionality |
| `API_KEY_LENGTH` | int | `32` | ❌ No | Length of generated API keys |
| `ENABLE_ADMIN_EXPORT` | bool | `True` | ✅ Yes | Enables admin export functionality |
| `ENABLE_METRICS` | bool | `True` | ✅ Yes | Enables metrics collection |
| `ENABLE_DEBUG` | bool | `False` | ❌ No | Enables debug mode |
| `LOG_LEVEL` | str | `INFO` | ❌ No | Logging level (DEBUG, INFO, WARNING, ERROR) |
| `DATA_DIR` | str | `/app/backend/data` | ❌ No | Directory for storing application data |
| `UPLOAD_DIR` | str | `/app/backend/data/uploads` | ❌ No | Directory for file uploads |
| `MAX_FILE_SIZE` | int | `100000000` | ❌ No | Maximum file size in bytes (default: 100MB) |
| `DEFAULT_MODELS` | str | `''` | ❌ No | Default models configuration |
| `ENABLE_MODEL_FILTER` | bool | `True` | ✅ Yes | Enables model filtering |

---

## App/Backend - AIOHTTP Client

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `AIOHTTP_CLIENT_TIMEOUT` | int | `300` | ❌ No | Timeout for AIOHTTP client requests in seconds |
| `AIOHTTP_CLIENT_CONNECT_TIMEOUT` | int | `60` | ❌ No | Connection timeout for AIOHTTP client in seconds |
| `AIOHTTP_CLIENT_READ_TIMEOUT` | int | `300` | ❌ No | Read timeout for AIOHTTP client in seconds |

---

## App/Backend - Directories

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `DATA_DIR` | str | `/app/backend/data` | ❌ No | Main data directory |
| `UPLOAD_DIR` | str | `/app/backend/data/uploads` | ❌ No | Upload directory |
| `MODELS_DIR` | str | `/app/backend/data/models` | ❌ No | Models directory |
| `CACHE_DIR` | str | `/app/backend/data/cache` | ❌ No | Cache directory |

---

## App/Backend - Logging

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `LOG_LEVEL` | str | `INFO` | ❌ No | Logging level (DEBUG, INFO, WARNING, ERROR, CRITICAL) |
| `LOG_FORMAT` | str | `json` | ❌ No | Log format (json, text) |
| `LOG_FILE` | str | `''` | ❌ No | Log file path (empty for stdout) |
| `ENABLE_DEBUG` | bool | `False` | ❌ No | Enable debug logging |

---

## App/Backend - Ollama

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `OLLAMA_BASE_URL` | str | `http://localhost:11434` | ❌ No | Base URL for Ollama API |
| `OLLAMA_API_KEY` | str | `''` | ❌ No | API key for Ollama (if required) |
| `OLLAMA_KEEP_ALIVE` | str | `5m` | ❌ No | Keep-alive duration for Ollama models |
| `OLLAMA_TIMEOUT` | int | `300` | ❌ No | Timeout for Ollama requests in seconds |

---

## App/Backend - OpenAI

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `OPENAI_API_BASE_URL` | str | `https://api.openai.com/v1` | ❌ No | Base URL for OpenAI API |
| `OPENAI_API_KEY` | str | `''` | ❌ No | OpenAI API key |
| `OPENAI_API_TIMEOUT` | int | `300` | ❌ No | Timeout for OpenAI API requests in seconds |
| `OPENAI_MAX_RETRIES` | int | `3` | ❌ No | Maximum number of retries for OpenAI API requests |

---

## App/Backend - Tasks

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_TASKS` | bool | `True` | ✅ Yes | Enables task functionality |
| `TASK_QUEUE_SIZE` | int | `100` | ❌ No | Size of the task queue |
| `TASK_WORKER_COUNT` | int | `4` | ❌ No | Number of task worker processes |

---

## App/Backend - Code Execution

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_CODE_EXECUTION` | bool | `False` | ✅ Yes | Enables code execution functionality |
| `CODE_EXECUTION_TIMEOUT` | int | `30` | ❌ No | Timeout for code execution in seconds |
| `CODE_EXECUTION_MAX_OUTPUT_SIZE` | int | `1000000` | ❌ No | Maximum output size for code execution in bytes |

---

## App/Backend - Code Interpreter

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_CODE_INTERPRETER` | bool | `False` | ✅ Yes | Enables code interpreter functionality |
| `CODE_INTERPRETER_TIMEOUT` | int | `300` | ❌ No | Timeout for code interpreter in seconds |
| `CODE_INTERPRETER_MAX_OUTPUT_SIZE` | int | `10000000` | ❌ No | Maximum output size for code interpreter in bytes |

---

## App/Backend - Direct Connections

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_DIRECT_CONNECTIONS` | bool | `False` | ✅ Yes | Enables direct connections to OpenAPI/MCPO tool servers |
| `DIRECT_CONNECTION_TIMEOUT` | int | `30` | ❌ No | Timeout for direct connections in seconds |

---

## App/Backend - Autocomplete

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_AUTOCOMPLETE` | bool | `True` | ✅ Yes | Enables autocomplete functionality |
| `AUTOCOMPLETE_MODEL` | str | `''` | ❌ No | Model to use for autocomplete |

---

## App/Backend - Evaluation Arena Model

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_EVAL_ARENA` | bool | `False` | ✅ Yes | Enables evaluation arena model functionality |
| `EVAL_ARENA_MODEL` | str | `''` | ❌ No | Model to use for evaluation arena |

---

## App/Backend - Tags Generation

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_TAGS_GENERATION` | bool | `True` | ✅ Yes | Enables automatic tag generation |
| `TAGS_GENERATION_MODEL` | str | `''` | ❌ No | Model to use for tag generation |

---

## App/Backend - API Key Endpoint Restrictions

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_API_KEY_ENDPOINT_RESTRICTIONS` | bool | `False` | ✅ Yes | Enables API key endpoint restrictions |
| `API_KEY_ALLOWED_ENDPOINTS` | str | `''` | ❌ No | Comma-separated list of allowed endpoints for API keys |

---

## App/Backend - Model Caching

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_MODEL_CACHING` | bool | `False` | ✅ Yes | Enables model caching |
| `MODEL_CACHE_SIZE` | int | `1000` | ❌ No | Maximum number of models to cache |
| `MODEL_CACHE_TTL` | int | `3600` | ❌ No | Time-to-live for cached models in seconds |

---

## Security Variables

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `SECRET_KEY` | str | `''` | ❌ No | Secret key for session encryption. **Must be set for production.** |
| `JWT_SECRET_KEY` | str | `''` | ❌ No | Secret key for JWT token signing. **Must be set for production.** |
| `WEBUI_SECRET_KEY` | str | `''` | ❌ No | Secret key for WebUI encryption |
| `ENABLE_CSRF_PROTECTION` | bool | `True` | ✅ Yes | Enables CSRF protection |
| `CSRF_SECRET_KEY` | str | `''` | ❌ No | Secret key for CSRF token generation |
| `SESSION_SECRET_KEY` | str | `''` | ❌ No | Secret key for session management |
| `ENABLE_RATE_LIMITING` | bool | `True` | ✅ Yes | Enables rate limiting |
| `RATE_LIMIT_REQUESTS` | int | `100` | ❌ No | Maximum requests per time window |
| `RATE_LIMIT_WINDOW` | int | `60` | ❌ No | Rate limit time window in seconds |

---

## Vector Database

### ChromaDB

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `CHROMA_SERVER_HOST` | str | `localhost` | ❌ No | ChromaDB server host |
| `CHROMA_SERVER_PORT` | int | `8000` | ❌ No | ChromaDB server port |
| `CHROMA_SERVER_HTTP_PORT` | int | `8000` | ❌ No | ChromaDB HTTP port |
| `CHROMA_SERVER_GRPC_PORT` | int | `50051` | ❌ No | ChromaDB gRPC port |
| `CHROMA_SERVER_AUTH_CREDENTIALS` | str | `''` | ❌ No | ChromaDB authentication credentials |

### Elasticsearch

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ELASTICSEARCH_URL` | str | `http://localhost:9200` | ❌ No | Elasticsearch server URL |
| `ELASTICSEARCH_USERNAME` | str | `''` | ❌ No | Elasticsearch username |
| `ELASTICSEARCH_PASSWORD` | str | `''` | ❌ No | Elasticsearch password |
| `ELASTICSEARCH_INDEX_NAME` | str | `openwebui` | ❌ No | Elasticsearch index name |
| `ELASTICSEARCH_VERIFY_CERTS` | bool | `True` | ❌ No | Verify SSL certificates |

### Milvus

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `MILVUS_HOST` | str | `localhost` | ❌ No | Milvus server host |
| `MILVUS_PORT` | int | `19530` | ❌ No | Milvus server port |
| `MILVUS_USER` | str | `''` | ❌ No | Milvus username |
| `MILVUS_PASSWORD` | str | `''` | ❌ No | Milvus password |
| `MILVUS_COLLECTION_NAME` | str | `openwebui` | ❌ No | Milvus collection name |

### OpenSearch

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `OPENSEARCH_URL` | str | `https://localhost:9200` | ❌ No | OpenSearch server URL |
| `OPENSEARCH_USERNAME` | str | `''` | ❌ No | OpenSearch username |
| `OPENSEARCH_PASSWORD` | str | `''` | ❌ No | OpenSearch password |
| `OPENSEARCH_INDEX_NAME` | str | `openwebui` | ❌ No | OpenSearch index name |
| `OPENSEARCH_VERIFY_CERTS` | bool | `True` | ❌ No | Verify SSL certificates |

### PGVector

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `PGVECTOR_CONNECTION_STRING` | str | `''` | ❌ No | PostgreSQL connection string for PGVector |
| `PGVECTOR_TABLE_NAME` | str | `embeddings` | ❌ No | PGVector table name |
| `PGVECTOR_DIMENSION` | int | `1536` | ❌ No | Vector dimension |

### Qdrant

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `QDRANT_URL` | str | `http://localhost:6333` | ❌ No | Qdrant server URL |
| `QDRANT_API_KEY` | str | `''` | ❌ No | Qdrant API key |
| `QDRANT_COLLECTION_NAME` | str | `openwebui` | ❌ No | Qdrant collection name |

### Pinecone

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `PINECONE_API_KEY` | str | `''` | ❌ No | Pinecone API key |
| `PINECONE_ENVIRONMENT` | str | `''` | ❌ No | Pinecone environment |
| `PINECONE_INDEX_NAME` | str | `openwebui` | ❌ No | Pinecone index name |

### Weaviate

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `WEAVIATE_URL` | str | `http://localhost:8080` | ❌ No | Weaviate server URL |
| `WEAVIATE_API_KEY` | str | `''` | ❌ No | Weaviate API key |
| `WEAVIATE_CLASS_NAME` | str | `Document` | ❌ No | Weaviate class name |

### Oracle 23ai Vector Search

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ORACLE23AI_CONNECTION_STRING` | str | `''` | ❌ No | Oracle connection string |
| `ORACLE23AI_TABLE_NAME` | str | `embeddings` | ❌ No | Oracle table name |

### S3 Vector Bucket

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `S3_VECTOR_BUCKET_NAME` | str | `''` | ❌ No | S3 bucket name for vector storage |
| `S3_VECTOR_REGION` | str | `us-east-1` | ❌ No | AWS region for S3 bucket |
| `S3_VECTOR_ACCESS_KEY` | str | `''` | ❌ No | AWS access key |
| `S3_VECTOR_SECRET_KEY` | str | `''` | ❌ No | AWS secret key |

---

## RAG Content Extraction Engine

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `RAG_CONTENT_EXTRACTION_ENGINE` | str | `unstructured` | ❌ No | Content extraction engine (unstructured, pdfplumber, etc.) |
| `RAG_CONTENT_EXTRACTION_TIMEOUT` | int | `300` | ❌ No | Timeout for content extraction in seconds |

---

## Retrieval Augmented Generation (RAG)

### Core Configuration

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_RAG` | bool | `True` | ✅ Yes | Enables RAG functionality |
| `ENABLE_RAG_HYBRID_SEARCH` | bool | `False` | ✅ Yes | Enables hybrid search for RAG |
| `ENABLE_RAG_WEB_SEARCH` | bool | `False` | ✅ Yes | Enables web search for RAG |
| `RAG_TOP_K` | int | `4` | ❌ No | Number of top results to retrieve |
| `RAG_RELEVANCE_THRESHOLD` | float | `0.0` | ❌ No | Relevance threshold for RAG results |
| `RAG_EMBEDDING_ENGINE` | str | `openai` | ❌ No | Embedding engine (openai, local, etc.) |
| `RAG_EMBEDDING_MODEL` | str | `text-embedding-ada-002` | ❌ No | Embedding model name |
| `RAG_EMBEDDING_DIMENSION` | int | `1536` | ❌ No | Embedding dimension |
| `RAG_CHUNK_SIZE` | int | `1000` | ❌ No | Chunk size for document processing |
| `RAG_CHUNK_OVERLAP` | int | `200` | ❌ No | Overlap between chunks |
| `RAG_RERANK_MODEL` | str | `''` | ❌ No | Reranking model name |
| `RAG_RERANK_TOP_N` | int | `10` | ❌ No | Number of results to rerank |
| `RAG_QUERY_GENERATION_MODEL` | str | `''` | ❌ No | Model for query generation |

### Document Processing

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `RAG_DOCUMENT_PROCESSING_TIMEOUT` | int | `300` | ❌ No | Timeout for document processing in seconds |
| `RAG_MAX_DOCUMENT_SIZE` | int | `10000000` | ❌ No | Maximum document size in bytes |
| `RAG_SUPPORTED_FORMATS` | str | `pdf,docx,txt,md` | ❌ No | Comma-separated list of supported document formats |

### Embedding Engine Configuration

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `RAG_EMBEDDING_API_KEY` | str | `''` | ❌ No | API key for embedding engine |
| `RAG_EMBEDDING_API_BASE_URL` | str | `''` | ❌ No | Base URL for embedding API |
| `RAG_EMBEDDING_BATCH_SIZE` | int | `100` | ❌ No | Batch size for embedding generation |

### Reranking

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_RAG_RERANKING` | bool | `False` | ✅ Yes | Enables reranking for RAG results |
| `RAG_RERANK_API_KEY` | str | `''` | ❌ No | API key for reranking service |
| `RAG_RERANK_API_BASE_URL` | str | `''` | ❌ No | Base URL for reranking API |

### Query Generation

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_RAG_QUERY_GENERATION` | bool | `False` | ✅ Yes | Enables query generation for RAG |
| `RAG_QUERY_GENERATION_API_KEY` | str | `''` | ❌ No | API key for query generation |
| `RAG_QUERY_GENERATION_API_BASE_URL` | str | `''` | ❌ No | Base URL for query generation API |

### Document Intelligence (Azure)

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `AZURE_DOCUMENT_INTELLIGENCE_ENDPOINT` | str | `''` | ❌ No | Azure Document Intelligence endpoint |
| `AZURE_DOCUMENT_INTELLIGENCE_API_KEY` | str | `''` | ❌ No | Azure Document Intelligence API key |

### Google Drive

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_GOOGLE_DRIVE` | bool | `False` | ✅ Yes | Enables Google Drive integration |
| `GOOGLE_DRIVE_CLIENT_ID` | str | `''` | ❌ No | Google Drive OAuth client ID |
| `GOOGLE_DRIVE_CLIENT_SECRET` | str | `''` | ❌ No | Google Drive OAuth client secret |

### OneDrive

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_ONEDRIVE` | bool | `False` | ✅ Yes | Enables OneDrive integration |
| `ONEDRIVE_CLIENT_ID` | str | `''` | ❌ No | OneDrive OAuth client ID |
| `ONEDRIVE_CLIENT_SECRET` | str | `''` | ❌ No | OneDrive OAuth client secret |

---

## Web Search

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_WEB_SEARCH` | bool | `False` | ✅ Yes | Enables web search functionality |
| `RAG_WEB_SEARCH_ENGINE` | str | `google` | ❌ No | Web search engine (google, tavily, etc.) |
| `GOOGLE_SEARCH_API_KEY` | str | `''` | ❌ No | Google Search API key |
| `GOOGLE_SEARCH_ENGINE_ID` | str | `''` | ❌ No | Google Custom Search Engine ID |
| `TAVILY_API_KEY` | str | `''` | ❌ No | Tavily API key |
| `WEB_SEARCH_TIMEOUT` | int | `30` | ❌ No | Timeout for web search requests in seconds |
| `WEB_SEARCH_MAX_RESULTS` | int | `10` | ❌ No | Maximum number of search results |

### Web Loader Configuration

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `WEB_LOADER_TIMEOUT` | int | `30` | ❌ No | Timeout for web page loading in seconds |
| `WEB_LOADER_MAX_SIZE` | int | `10000000` | ❌ No | Maximum size for web page content in bytes |
| `WEB_LOADER_USER_AGENT` | str | `Mozilla/5.0` | ❌ No | User agent for web requests |

### YouTube Loader

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_YOUTUBE_LOADER` | bool | `False` | ✅ Yes | Enables YouTube video content loading |
| `YOUTUBE_LOADER_API_KEY` | str | `''` | ❌ No | YouTube Data API key |

---

## Audio

### Whisper Speech-to-Text (Local)

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `WHISPER_MODEL` | str | `base` | ❌ No | Whisper model size (tiny, base, small, medium, large) |
| `WHISPER_MODEL_DIR` | str | `/app/backend/data/models/whisper` | ❌ No | Directory for Whisper models |
| `WHISPER_COMPUTE_TYPE` | str | `float16` | ❌ No | Compute type (int8, float16, int8_float16, float32) |
| `WHISPER_LANGUAGE` | str | `''` | ❌ No | ISO 639-1 language code (empty for auto-detect) |
| `WHISPER_MULTILINGUAL` | bool | `True` | ❌ No | Use multilingual model |
| `WHISPER_MODEL_AUTO_UPDATE` | bool | `True` | ❌ No | Automatically update Whisper models |
| `WHISPER_VAD_FILTER` | bool | `False` | ❌ No | Enable Voice Activity Detection filter |

### Speech-to-Text (OpenAI)

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `AUDIO_STT_ENGINE` | str | `''` | ❌ No | STT engine (empty for local, "openai", "azure", "deepgram", "mistral") |
| `AUDIO_STT_MODEL` | str | `whisper-1` | ❌ No | STT model name |
| `AUDIO_STT_OPENAI_API_KEY` | str | `''` | ❌ No | OpenAI API key for STT |
| `AUDIO_STT_OPENAI_API_BASE_URL` | str | `https://api.openai.com/v1` | ❌ No | Base URL for OpenAI STT API |

### Speech-to-Text (Azure)

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `AUDIO_STT_AZURE_API_KEY` | str | `''` | ❌ No | Azure Speech API key |
| `AUDIO_STT_AZURE_REGION` | str | `''` | ❌ No | Azure region for Speech API |
| `AUDIO_STT_AZURE_LANGUAGE` | str | `en-US` | ❌ No | Azure Speech language code |

### Speech-to-Text (Deepgram)

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `DEEPGRAM_API_KEY` | str | `''` | ❌ No | Deepgram API key |
| `DEEPGRAM_MODEL` | str | `nova-2` | ❌ No | Deepgram model name |

### Speech-to-Text (Mistral)

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `AUDIO_STT_MISTRAL_API_KEY` | str | `''` | ❌ No | Mistral API key for STT |
| `AUDIO_STT_MISTRAL_API_BASE_URL` | str | `https://api.mistral.ai/v1` | ❌ No | Base URL for Mistral STT API |

### Speech-to-Text (General)

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `AUDIO_STT_TIMEOUT` | int | `30` | ❌ No | Timeout for STT requests in seconds |
| `AUDIO_STT_MAX_FILE_SIZE` | int | `25000000` | ❌ No | Maximum audio file size in bytes (25MB) |

### Text-to-Speech

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `AUDIO_TTS_ENGINE` | str | `''` | ❌ No | TTS engine ("openai", "elevenlabs", "azure", "transformers") |
| `AUDIO_TTS_MODEL` | str | `tts-1` | ❌ No | TTS model (tts-1, tts-1-hd) |
| `AUDIO_TTS_VOICE` | str | `alloy` | ❌ No | TTS voice (alloy, echo, fable, onyx, nova, shimmer) |
| `AUDIO_TTS_SPLIT_ON` | str | `punctuation` | ❌ No | Text splitting method (punctuation, none) |
| `AUDIO_TTS_API_KEY` | str | `''` | ❌ No | Generic TTS API key |
| `AUDIO_TTS_TIMEOUT` | int | `30` | ❌ No | Timeout for TTS requests in seconds |

### Azure Text-to-Speech

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `AUDIO_TTS_AZURE_API_KEY` | str | `''` | ❌ No | Azure TTS API key |
| `AUDIO_TTS_AZURE_REGION` | str | `''` | ❌ No | Azure region for TTS |
| `AUDIO_TTS_AZURE_VOICE` | str | `en-US-JennyNeural` | ❌ No | Azure TTS voice name |

### Voice Mode

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_VOICE_MODE` | bool | `False` | ✅ Yes | Enables voice mode functionality |
| `VOICE_MODE_STT_ENGINE` | str | `''` | ❌ No | STT engine for voice mode |
| `VOICE_MODE_TTS_ENGINE` | str | `''` | ❌ No | TTS engine for voice mode |

### OpenAI Text-to-Speech

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `AUDIO_TTS_OPENAI_API_KEY` | str | `''` | ❌ No | OpenAI API key for TTS |
| `AUDIO_TTS_OPENAI_API_BASE_URL` | str | `https://api.openai.com/v1` | ❌ No | Base URL for OpenAI TTS API |

### ElevenLabs Text-to-Speech

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ELEVENLABS_API_KEY` | str | `''` | ❌ No | ElevenLabs API key |
| `ELEVENLABS_VOICE_ID` | str | `''` | ❌ No | ElevenLabs voice ID |
| `ELEVENLABS_MODEL_ID` | str | `eleven_multilingual_v2` | ❌ No | ElevenLabs model ID |

---

## Image Generation

### General Settings

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_IMAGE_GENERATION` | bool | `False` | ✅ Yes | Enables image generation functionality |
| `IMAGE_GENERATION_ENGINE` | str | `''` | ❌ No | Image generation engine (dalle, gemini, comfyui, automatic1111) |
| `IMAGE_GENERATION_TIMEOUT` | int | `60` | ❌ No | Timeout for image generation in seconds |
| `IMAGE_GENERATION_MAX_SIZE` | int | `1024` | ❌ No | Maximum image size in pixels |

### Image Creation

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_IMAGE_CREATION` | bool | `False` | ✅ Yes | Enables image creation |
| `IMAGE_CREATION_DEFAULT_SIZE` | str | `1024x1024` | ❌ No | Default image size for creation |
| `IMAGE_CREATION_DEFAULT_QUALITY` | str | `standard` | ❌ No | Default image quality (standard, hd) |

### Image Editing

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_IMAGE_EDITING` | bool | `False` | ✅ Yes | Enables image editing functionality |
| `IMAGE_EDITING_DEFAULT_SIZE` | str | `1024x1024` | ❌ No | Default image size for editing |

### OpenAI DALL-E

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `OPENAI_DALLE_API_KEY` | str | `''` | ❌ No | OpenAI API key for DALL-E |
| `OPENAI_DALLE_API_BASE_URL` | str | `https://api.openai.com/v1` | ❌ No | Base URL for DALL-E API |
| `OPENAI_DALLE_MODEL` | str | `dall-e-3` | ❌ No | DALL-E model (dall-e-2, dall-e-3) |

### Gemini

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `GEMINI_API_KEY` | str | `''` | ❌ No | Google Gemini API key |
| `GEMINI_IMAGE_MODEL` | str | `imagen-3` | ❌ No | Gemini image generation model |

### ComfyUI

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `COMFYUI_API_URL` | str | `http://localhost:8188` | ❌ No | ComfyUI API URL |
| `COMFYUI_API_KEY` | str | `''` | ❌ No | ComfyUI API key (if required) |

### AUTOMATIC1111

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `AUTOMATIC1111_API_URL` | str | `http://localhost:7860` | ❌ No | AUTOMATIC1111 API URL |
| `AUTOMATIC1111_API_KEY` | str | `''` | ❌ No | AUTOMATIC1111 API key (if required) |

---

## OAuth

### Google

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_OAUTH_SIGNUP` | bool | `False` | ✅ Yes | Enables OAuth sign-up |
| `OAUTH_PROVIDERS` | str | `''` | ❌ No | Comma-separated list of OAuth providers |
| `GOOGLE_OAUTH_CLIENT_ID` | str | `''` | ❌ No | Google OAuth client ID |
| `GOOGLE_OAUTH_CLIENT_SECRET` | str | `''` | ❌ No | Google OAuth client secret |
| `GOOGLE_OAUTH_REDIRECT_URI` | str | `''` | ❌ No | Google OAuth redirect URI |

### Microsoft

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `MICROSOFT_OAUTH_CLIENT_ID` | str | `''` | ❌ No | Microsoft OAuth client ID |
| `MICROSOFT_OAUTH_CLIENT_SECRET` | str | `''` | ❌ No | Microsoft OAuth client secret |
| `MICROSOFT_OAUTH_TENANT_ID` | str | `''` | ❌ No | Microsoft tenant ID |
| `MICROSOFT_OAUTH_REDIRECT_URI` | str | `''` | ❌ No | Microsoft OAuth redirect URI |

### GitHub

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `GITHUB_OAUTH_CLIENT_ID` | str | `''` | ❌ No | GitHub OAuth client ID |
| `GITHUB_OAUTH_CLIENT_SECRET` | str | `''` | ❌ No | GitHub OAuth client secret |
| `GITHUB_OAUTH_REDIRECT_URI` | str | `''` | ❌ No | GitHub OAuth redirect URI |

### Feishu

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `FEISHU_OAUTH_APP_ID` | str | `''` | ❌ No | Feishu OAuth app ID |
| `FEISHU_OAUTH_APP_SECRET` | str | `''` | ❌ No | Feishu OAuth app secret |
| `FEISHU_OAUTH_REDIRECT_URI` | str | `''` | ❌ No | Feishu OAuth redirect URI |

### OpenID (OIDC)

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `OIDC_CLIENT_ID` | str | `''` | ❌ No | OpenID Connect client ID |
| `OIDC_CLIENT_SECRET` | str | `''` | ❌ No | OpenID Connect client secret |
| `OIDC_ISSUER_URL` | str | `''` | ❌ No | OpenID Connect issuer URL |
| `OIDC_REDIRECT_URI` | str | `''` | ❌ No | OpenID Connect redirect URI |
| `OIDC_SCOPES` | str | `openid profile email` | ❌ No | OpenID Connect scopes |

---

## LDAP

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_LDAP` | bool | `False` | ✅ Yes | Enables LDAP authentication |
| `LDAP_SERVER_LABEL` | str | `LDAP` | ❌ No | Label for LDAP server in UI |
| `LDAP_SERVER_HOST` | str | `''` | ❌ No | LDAP server hostname or IP |
| `LDAP_SERVER_PORT` | int | `389` | ❌ No | LDAP server port (389 for LDAP, 636 for LDAPS) |
| `LDAP_USE_TLS` | bool | `False` | ❌ No | Use TLS/SSL for LDAP connection |
| `LDAP_VALIDATE_CERT` | bool | `True` | ❌ No | Validate SSL certificate for LDAP |
| `LDAP_BIND_DN` | str | `''` | ❌ No | LDAP bind DN for authentication |
| `LDAP_BIND_PASSWORD` | str | `''` | ❌ No | LDAP bind password |
| `LDAP_SEARCH_BASE` | str | `''` | ❌ No | Base DN for user search |
| `LDAP_SEARCH_FILTER` | str | `''` | ❌ No | Additional LDAP search filter |
| `LDAP_ATTRIBUTE_FOR_USERNAME` | str | `uid` | ❌ No | LDAP attribute for username |
| `LDAP_ATTRIBUTE_FOR_MAIL` | str | `mail` | ❌ No | LDAP attribute for email |
| `LDAP_ATTRIBUTE_FOR_NAME` | str | `cn` | ❌ No | LDAP attribute for display name |
| `LDAP_USER_GROUP` | str | `''` | ❌ No | LDAP group for allowed users |

---

## SCIM

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_SCIM` | bool | `False` | ✅ Yes | Enables SCIM (System for Cross-domain Identity Management) |
| `SCIM_BEARER_TOKEN` | str | `''` | ❌ No | Bearer token for SCIM API authentication |
| `SCIM_ENDPOINT` | str | `/scim/v2` | ❌ No | SCIM API endpoint path |

---

## User Permissions

### Chat Permissions

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_CHAT_EXPORT` | bool | `True` | ✅ Yes | Enables chat export functionality |
| `ENABLE_CHAT_SHARING` | bool | `True` | ✅ Yes | Enables chat sharing functionality |
| `ENABLE_CHAT_DELETION` | bool | `True` | ✅ Yes | Enables chat deletion functionality |

### Feature Permissions

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_RAG_FOR_USERS` | bool | `True` | ✅ Yes | Enables RAG for regular users |
| `ENABLE_CODE_EXECUTION_FOR_USERS` | bool | `False` | ✅ Yes | Enables code execution for regular users |
| `ENABLE_IMAGE_GENERATION_FOR_USERS` | bool | `False` | ✅ Yes | Enables image generation for regular users |

### Workspace Permissions

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_WORKSPACES` | bool | `True` | ✅ Yes | Enables workspace functionality |
| `ENABLE_WORKSPACE_SHARING` | bool | `True` | ✅ Yes | Enables workspace sharing |

### Settings Permissions

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_USER_SETTINGS` | bool | `True` | ✅ Yes | Enables user settings functionality |
| `ENABLE_ADMIN_SETTINGS` | bool | `True` | ✅ Yes | Enables admin settings functionality |

---

## Misc Environment Variables

### Cloud Storage

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_CLOUD_STORAGE` | bool | `False` | ✅ Yes | Enables cloud storage integration |
| `CLOUD_STORAGE_TYPE` | str | `s3` | ❌ No | Cloud storage type (s3, gcs, azure) |
| `S3_BUCKET_NAME` | str | `''` | ❌ No | S3 bucket name |
| `S3_REGION` | str | `us-east-1` | ❌ No | AWS region |
| `S3_ACCESS_KEY` | str | `''` | ❌ No | AWS access key |
| `S3_SECRET_KEY` | str | `''` | ❌ No | AWS secret key |
| `GCS_BUCKET_NAME` | str | `''` | ❌ No | Google Cloud Storage bucket name |
| `GCS_CREDENTIALS_FILE` | str | `''` | ❌ No | Path to GCS credentials file |
| `AZURE_STORAGE_ACCOUNT` | str | `''` | ❌ No | Azure storage account name |
| `AZURE_STORAGE_KEY` | str | `''` | ❌ No | Azure storage account key |
| `AZURE_STORAGE_CONTAINER` | str | `''` | ❌ No | Azure storage container name |

### OpenTelemetry Configuration

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_OPENTELEMETRY` | bool | `False` | ✅ Yes | Enables OpenTelemetry tracing |
| `OPENTELEMETRY_ENDPOINT` | str | `''` | ❌ No | OpenTelemetry collector endpoint |
| `OPENTELEMETRY_SERVICE_NAME` | str | `openwebui` | ❌ No | Service name for OpenTelemetry |

### Database Pool

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `DATABASE_POOL_SIZE` | int | `10` | ❌ No | Database connection pool size |
| `DATABASE_POOL_MAX_OVERFLOW` | int | `20` | ❌ No | Maximum overflow connections |
| `DATABASE_POOL_TIMEOUT` | int | `30` | ❌ No | Connection pool timeout in seconds |

### Encrypted SQLite with SQLCipher

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_SQLCIPHER` | bool | `False` | ❌ No | Enables SQLCipher encryption for SQLite |
| `SQLCIPHER_KEY` | str | `''` | ❌ No | SQLCipher encryption key |

### Redis

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `ENABLE_REDIS` | bool | `False` | ✅ Yes | Enables Redis for caching and sessions |
| `REDIS_URL` | str | `redis://localhost:6379/0` | ❌ No | Redis connection URL |
| `REDIS_PASSWORD` | str | `''` | ❌ No | Redis password |
| `REDIS_DB` | int | `0` | ❌ No | Redis database number |
| `REDIS_SOCKET_CONNECT_TIMEOUT` | int | `5` | ❌ No | Redis socket connection timeout in seconds |
| `REDIS_SOCKET_TIMEOUT` | int | `5` | ❌ No | Redis socket timeout in seconds |
| `REDIS_MAX_CONNECTIONS` | int | `50` | ❌ No | Maximum Redis connections |
| `REDIS_RETRY_ON_TIMEOUT` | bool | `True` | ❌ No | Retry on Redis timeout |
| `REDIS_HEALTH_CHECK_INTERVAL` | int | `30` | ❌ No | Redis health check interval in seconds |
| `WEBSOCKET_SERVER_LOGGING` | bool | `False` | ❌ No | Controls logging for SocketIO server (very verbose) |
| `WEBSOCKET_SERVER_ENGINEIO_LOGGING` | bool | `False` | ❌ No | Controls logging for EngineIO server (very verbose) |
| `WEBSOCKET_SERVER_PING_TIMEOUT` | int | `20` | ❌ No | Timeout for ping to Redis in seconds |
| `WEBSOCKET_SERVER_PING_INTERVAL` | int | `25` | ❌ No | Frequency for ping to Redis in seconds |
| `ENABLE_STAR_SESSIONS_MIDDLEWARE` | bool | `False` | ❌ No | Enables Redis-based session storage for OAuth (experimental) |

### Uvicorn Settings

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `UVICORN_WORKERS` | int | `1` | ❌ No | Number of Uvicorn worker processes |
| `UVICORN_HOST` | str | `0.0.0.0` | ❌ No | Uvicorn host address |
| `UVICORN_PORT` | int | `8080` | ❌ No | Uvicorn port |
| `UVICORN_LOG_LEVEL` | str | `info` | ❌ No | Uvicorn log level |
| `ENABLE_DB_MIGRATIONS` | bool | `True` | ❌ No | Enables database migrations (set to False on all but one worker/pod) |

### Cache Settings

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `CACHE_CONTROL` | str | `''` | ❌ No | Cache-Control header for HTTP responses |
| `ENABLE_CACHE` | bool | `True` | ✅ Yes | Enables caching functionality |
| `CACHE_TYPE` | str | `simple` | ❌ No | Cache type (simple, redis) |
| `CACHE_TTL` | int | `3600` | ❌ No | Cache time-to-live in seconds |

### Proxy Settings

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `http_proxy` | str | `''` | ❌ No | HTTP proxy URL |
| `https_proxy` | str | `''` | ❌ No | HTTPS proxy URL |
| `no_proxy` | str | `''` | ❌ No | Comma-separated list of domains/IPs to bypass proxy |

### Install Required Python Packages

| Variable | Type | Default | PersistentConfig | Description |
|----------|------|---------|------------------|-------------|
| `PIP_OPTIONS` | str | `''` | ❌ No | Additional pip command-line options |
| `PIP_PACKAGE_INDEX_OPTIONS` | str | `''` | ❌ No | Custom package index options for pip |

---

## Quick Reference

### Enable All Features (Feature Flags)

```bash
ENABLE_SIGNUP=True
ENABLE_LOGIN_FORM=True
ENABLE_OAUTH_SIGNUP=True
ENABLE_LDAP=True
ENABLE_RAG=True
ENABLE_RAG_HYBRID_SEARCH=True
ENABLE_RAG_WEB_SEARCH=True
ENABLE_WEB_SEARCH=True
ENABLE_CODE_EXECUTION=True
ENABLE_CODE_INTERPRETER=True
ENABLE_IMAGE_GENERATION=True
ENABLE_IMAGE_CREATION=True
ENABLE_IMAGE_EDITING=True
ENABLE_VOICE_MODE=True
ENABLE_YOUTUBE_LOADER=True
ENABLE_GOOGLE_DRIVE=True
ENABLE_ONEDRIVE=True
ENABLE_SCIM=True
ENABLE_REDIS=True
ENABLE_OPENTELEMETRY=True
ENABLE_CLOUD_STORAGE=True
ENABLE_PERSISTENT_CONFIG=False  # Force env vars
```

### Critical Security Variables (Must Set for Production)

```bash
SECRET_KEY=<generate-secure-random-key>
JWT_SECRET_KEY=<generate-secure-random-key>
WEBUI_SECRET_KEY=<generate-secure-random-key>
CSRF_SECRET_KEY=<generate-secure-random-key>
SESSION_SECRET_KEY=<generate-secure-random-key>
```

### Common API Keys

```bash
OPENAI_API_KEY=<your-key>
OPENAI_API_BASE_URL=https://api.openai.com/v1
OLLAMA_BASE_URL=http://localhost:11434
GOOGLE_SEARCH_API_KEY=<your-key>
TAVILY_API_KEY=<your-key>
DEEPGRAM_API_KEY=<your-key>
ELEVENLABS_API_KEY=<your-key>
```

---

## Notes

- **PersistentConfig variables** are stored in the database after first launch and won't update from environment variables on restart unless `ENABLE_PERSISTENT_CONFIG=False`
- Always use Kubernetes Secrets or similar for sensitive values like API keys and passwords
- Some features require additional dependencies or services to be running
- Check the [official documentation](https://docs.openwebui.com/getting-started/env-configuration/) for the most up-to-date information
- This document is based on Open WebUI v0.7.1

---

**Last Updated:** Based on documentation from https://docs.openwebui.com/getting-started/env-configuration/
