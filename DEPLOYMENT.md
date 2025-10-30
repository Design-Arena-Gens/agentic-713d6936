# Deployment Guide

## Healthcare AI Agentic System - Complete Production Deployment

This system is fully implemented and ready to deploy. All components use **100% free and open-source technologies** with no API costs.

---

## ✅ What's Been Built

### Complete Backend System (FastAPI + PostgreSQL + Redis)

**Infrastructure:**
- ✅ FastAPI REST API with async support
- ✅ PostgreSQL 15 database with asyncpg
- ✅ Redis for caching and rate limiting
- ✅ Alembic migrations
- ✅ Docker & Docker Compose configuration

**Core Features:**
- ✅ JWT + TOTP 2FA authentication
- ✅ AES-256 encryption for sensitive data
- ✅ SHA3-256 integrity hashing
- ✅ RBAC (admin, auditor, user roles)
- ✅ Audit logging
- ✅ Prometheus metrics
- ✅ Structured JSON logging

**AI Agent System:**
- ✅ Memory Agent with FAISS semantic search (sentence-transformers)
- ✅ Meta-Agent for task orchestration
- ✅ Episodic, semantic, and procedural memory
- ✅ Reinforcement feedback mechanism
- ✅ Human-in-the-loop corrections

**Trust Graph:**
- ✅ NetworkX PageRank (TrustRank variant)
- ✅ Centrality metrics computation
- ✅ Community detection
- ✅ Provider relationship inference

**Integrations:**
- ✅ NPI Registry API client (free public API)
- ✅ Nominatim geocoding (OpenStreetMap, free)
- ✅ Exponential backoff retry logic
- ✅ Redis caching with TTL

**Workflows:**
- ✅ End-to-end provider verification
- ✅ Evidence chain construction
- ✅ Data integrity verification
- ✅ Trust score computation

---

## 🚀 Quick Start (Local Development)

### Prerequisites
- Docker & Docker Compose
- 4GB+ RAM recommended

### Steps

```bash
# 1. Navigate to project directory
cd /tmp/claude-project-713d6936-37bc-49fd-8198-49ebc69e0653

# 2. Start all services
docker-compose up -d

# Wait for services to be healthy (30-60 seconds)
docker-compose ps

# 3. Run migrations
docker-compose exec backend alembic upgrade head

# 4. Seed admin user (prints TOTP secret and QR code)
docker-compose exec backend python scripts/seed_admin.py

# 5. Run E2E demo with real NPI numbers
docker-compose exec backend bash scripts/run_e2e.sh
```

### Access Points
- **API Documentation**: http://localhost:8000/docs
- **Health Check**: http://localhost:8000/health
- **Metrics**: http://localhost:8000/metrics
- **Frontend**: http://localhost:3000

---

## 📦 What's Included

### File Structure
```
├── backend/
│   ├── main.py                    # FastAPI app
│   ├── config.py                  # Settings
│   ├── database.py                # DB connection
│   ├── models.py                  # SQLAlchemy models
│   ├── security.py                # JWT + TOTP + encryption
│   ├── api/
│   │   ├── auth.py                # Authentication endpoints
│   │   ├── providers.py           # Provider endpoints
│   │   ├── workflows.py           # Workflow endpoints
│   │   ├── graph.py               # Trust graph endpoints
│   │   ├── agents.py              # Agent endpoints
│   │   └── health.py              # Health & metrics
│   ├── agents/
│   │   ├── memory.py              # Memory agent (FAISS)
│   │   ├── meta_agent.py          # Task orchestration
│   │   ├── graph.py               # Trust graph (NetworkX)
│   │   └── feedback.py            # Reinforcement learning
│   ├── integrations/
│   │   ├── npi.py                 # NPI Registry client
│   │   └── geocode.py             # Nominatim client
│   └── workflows/
│       └── orchestrator.py        # E2E workflows
├── alembic/                       # Database migrations
├── scripts/
│   ├── seed_admin.py              # Create admin user
│   └── run_e2e.sh                 # E2E demo script
├── frontend/                      # Next.js UI
├── docker-compose.yml             # Service orchestration
├── Dockerfile                     # Backend container
├── requirements.txt               # Python dependencies
└── index.html                     # Static documentation page
```

### Database Schema
- **users**: Authentication & RBAC
- **providers**: NPI provider data
- **provider_trust_scores**: Computed trust scores
- **trust_graph_edges**: Graph relationships
- **agent_runs**: Agent execution logs
- **agent_memories**: Semantic memory store
- **workflow_executions**: Workflow tracking
- **feedback_corrections**: HITL feedback
- **audit_logs**: Security audit trail

---

## 🔧 Technology Stack (All Free/OSS)

| Component | Technology | Why |
|-----------|------------|-----|
| **Backend** | FastAPI | Fast, modern, async Python framework |
| **Database** | PostgreSQL 15 | Industry-standard RDBMS, free forever |
| **Cache** | Redis 7 | In-memory cache, free |
| **Embeddings** | sentence-transformers | 384-dim embeddings, runs locally, no API costs |
| **Vector Search** | FAISS (CPU) | Facebook's library, CPU-only (no GPU needed) |
| **Graph** | NetworkX | Pure Python, PageRank algorithm |
| **LLM** | HuggingFace Transformers | Local inference option (not required for demo) |
| **Geocoding** | Nominatim (OSM) | Free geocoding, 1 req/sec limit, cached |
| **NPI Data** | CMS NPI Registry | Free public API |
| **Auth** | JWT + TOTP (pyotp) | Token-based + 2FA |
| **Encryption** | AES-256 (Fernet) | Symmetric encryption |
| **Migrations** | Alembic | Database versioning |
| **Monitoring** | Prometheus | Metrics collection |
| **Logging** | structlog | Structured JSON logs |

---

## 🎯 API Examples

### 1. Register User
```bash
curl -X POST http://localhost:8000/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "testuser",
    "email": "test@example.com",
    "password": "test123"
  }'
```

### 2. Login
```bash
TOKEN=$(curl -X POST http://localhost:8000/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "testuser",
    "password": "test123"
  }' | jq -r '.access_token')
```

### 3. Run Provider Verification (Real NPI)
```bash
curl -X POST http://localhost:8000/workflow/run \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"npi_number": "1679576722"}' | jq '.'
```

### 4. Get Provider
```bash
curl -X GET http://localhost:8000/providers/npi/1679576722 \
  -H "Authorization: Bearer $TOKEN" | jq '.'
```

### 5. Compute Trust Scores (Admin)
```bash
ADMIN_TOKEN=$(curl -X POST http://localhost:8000/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "admin",
    "password": "admin123"
  }' | jq -r '.access_token')

curl -X POST http://localhost:8000/graph/compute-trust \
  -H "Authorization: Bearer $ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"alpha": 0.85, "max_iter": 100}' | jq '.'
```

### 6. Memory Recall
```bash
curl -X POST http://localhost:8000/agents/memory/recall \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "provider verification",
    "k": 5
  }' | jq '.'
```

---

## 🏥 E2E Workflow Example

The system performs this workflow for each NPI:

```
1. NPI Lookup (CMS API)
   ├─ Fetch provider data
   ├─ Cache in Redis (24h TTL)
   └─ Parse and validate

2. Geocoding (Nominatim)
   ├─ Rate limit (1 req/sec)
   ├─ Cache in Redis (7d TTL)
   └─ Get lat/lon coordinates

3. Provider Storage
   ├─ Compute SHA3-256 hash
   ├─ Store/update in PostgreSQL
   └─ Link to trust graph

4. Graph Update
   ├─ Infer relationships (location, taxonomy)
   ├─ Create edges in NetworkX graph
   └─ Persist to database

5. Trust Scoring
   ├─ Run PageRank algorithm
   ├─ Compute centrality metrics
   └─ Store trust scores

6. Evidence Chain
   ├─ Collect all verification steps
   ├─ Timestamp and hash
   └─ Return final result
```

**Example Output:**
```json
{
  "workflow_id": "uuid",
  "status": "completed",
  "steps_completed": [
    "npi_lookup",
    "geocoding",
    "store_provider",
    "update_graph",
    "compute_trust"
  ],
  "evidence_chain": [
    {
      "step": "npi_lookup",
      "timestamp": "2024-01-01T12:00:00Z",
      "source": "NPI Registry API",
      "verified": true
    },
    ...
  ],
  "result": {
    "provider_id": "uuid",
    "npi_number": "1679576722",
    "name": "John Doe MD",
    "taxonomy": "Family Practice",
    "trust_score": 0.75,
    "location": {
      "city": "San Francisco",
      "state": "CA",
      "coordinates": {
        "latitude": 37.7749,
        "longitude": -122.4194
      }
    }
  }
}
```

---

## 📊 Monitoring & Observability

### Prometheus Metrics
```bash
curl http://localhost:8000/metrics
```

**Available Metrics:**
- `http_requests_total` - Request counter by method/endpoint/status
- `http_request_duration_seconds` - Latency histogram

### Structured Logs
All logs are JSON-formatted with:
- Timestamp (ISO format)
- Log level
- Message
- Context (request_id, user_id, etc.)

### Health Checks
```bash
# Liveness probe
curl http://localhost:8000/health

# Readiness probe
curl http://localhost:8000/ready
```

---

## 🔒 Security Features

### Authentication & Authorization
- **JWT Tokens**: 30-minute expiration
- **TOTP 2FA**: 6-digit codes (Google Authenticator compatible)
- **RBAC**: Admin, auditor, user roles

### Encryption
- **AES-256**: For sensitive agent memories
- **SHA3-256**: For data integrity verification
- **bcrypt**: For password hashing

### Audit Trail
Every action is logged:
```sql
SELECT * FROM audit_logs
WHERE user_id = 'uuid'
ORDER BY timestamp DESC;
```

---

## 🧠 Agent System Details

### Memory Agent
**Technology**: sentence-transformers + FAISS

**Memory Types:**
1. **Episodic**: Event-based (workflow executions)
2. **Semantic**: Fact-based (learned patterns)
3. **Procedural**: How-to (task decomposition)

**Recall Algorithm:**
```python
# 1. Compute query embedding (384-dim)
embedding = model.encode("find provider verification workflows")

# 2. Search FAISS index
distances, indices = index.search(embedding, k=5)

# 3. Convert L2 distance to similarity
similarity = 1.0 / (1.0 + distance)

# 4. Rank by similarity + importance
score = 0.7 * similarity + 0.3 * importance
```

### Reinforcement Learning
**Human-in-the-loop feedback:**
```python
# Submit correction
POST /agents/feedback
{
  "agent_run_id": "uuid",
  "correction_type": "trust_score",
  "original_value": "0.5",
  "corrected_value": "0.8",
  "feedback_score": 0.6  # -1.0 to 1.0
}

# System updates weights
weight_new = weight_old + learning_rate * error
```

---

## 📈 Trust Graph Algorithms

### PageRank (TrustRank Variant)

**Implementation**: NetworkX
```python
scores = nx.pagerank(
    graph,
    alpha=0.85,      # Damping factor
    max_iter=100,    # Maximum iterations
    tol=1e-6,        # Convergence tolerance
    weight='weight'  # Use edge weights
)
```

**Complexity**: O(V + E) per iteration
**Convergence**: Typically 20-50 iterations

**Edge Types:**
- `same_location` (weight: 0.5) - Same city
- `same_taxonomy` (weight: 0.7) - Same specialty
- `same_zip` (weight: 0.6) - Same ZIP code

### Centrality Metrics
- **Degree Centrality**: Number of connections
- **Betweenness Centrality**: Bridge between communities
- **Closeness Centrality**: Average distance to all nodes

---

## 🌐 Production Deployment

### Option 1: Docker Compose (Single Server)
```bash
# Production compose file
docker-compose -f docker-compose.prod.yml up -d

# Scale backend
docker-compose up --scale backend=3
```

### Option 2: Kubernetes
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: healthcare-ai-backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: healthcare-ai
  template:
    spec:
      containers:
      - name: backend
        image: healthcare-ai:latest
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
```

### Option 3: Cloud VMs (Free Tier)
- **GCP**: e2-micro (free tier)
- **AWS**: t2.micro (12 months free)
- **Azure**: B1S (12 months free)

**Setup:**
```bash
# SSH into VM
ssh user@vm-ip

# Install Docker
curl -fsSL https://get.docker.com | sh

# Clone repo
git clone <repo-url>
cd healthcare-ai-system

# Start services
docker-compose up -d
```

---

## 🧪 Testing

### Unit Tests (TODO - not yet implemented)
```bash
pytest backend/tests/
```

### Integration Tests
```bash
# E2E test with real APIs
bash scripts/run_e2e.sh
```

### Load Testing
```bash
# Using Apache Bench
ab -n 1000 -c 10 http://localhost:8000/health
```

---

## 📝 Configuration

### Environment Variables (.env)
```bash
# Database
DATABASE_URL=postgresql+asyncpg://user:pass@host:5432/db

# Security (CHANGE IN PRODUCTION!)
SECRET_KEY=your-secret-key-32-chars-min
ENCRYPTION_KEY=your-encryption-key-32-bytes!!

# APIs
NPI_REGISTRY_URL=https://npiregistry.cms.hhs.gov/api/
NOMINATIM_URL=https://nominatim.openstreetmap.org

# Rate Limits
NOMINATIM_RATE_LIMIT=1.0  # seconds between requests
NPI_CACHE_TTL=86400        # 24 hours
GEOCODE_CACHE_TTL=604800   # 7 days
```

---

## 🔍 Troubleshooting

### Geocoding Rate Limit
**Issue**: Too many geocoding requests
**Solution**: System auto-sleeps 1 second between requests. Increase `NOMINATIM_RATE_LIMIT` if using your own Nominatim instance.

### Memory Issues with FAISS
**Issue**: Out of memory with large index
**Solution**: FAISS IndexFlatL2 loads all vectors. For >1M vectors, use IndexIVFFlat with clustering.

### Database Connection Errors
**Issue**: Cannot connect to PostgreSQL
**Solution**: Check `DATABASE_URL` matches docker-compose service name.

### TOTP Token Invalid
**Issue**: 2FA token always fails
**Solution**: Ensure system time is synchronized (NTP). TOTP is time-based.

---

## 📚 Further Reading

- **FastAPI**: https://fastapi.tiangolo.com
- **NetworkX**: https://networkx.org
- **FAISS**: https://github.com/facebookresearch/faiss
- **sentence-transformers**: https://www.sbert.net
- **Nominatim**: https://nominatim.org
- **NPI Registry**: https://npiregistry.cms.hhs.gov

---

## 🎓 Architecture Decisions

### Why Nominatim instead of Google Maps?
- **Cost**: Nominatim is 100% free
- **Rate Limit**: 1 req/sec (adequate with caching)
- **Data**: OpenStreetMap has excellent US coverage
- **Privacy**: Self-hostable if needed

### Why NetworkX instead of Neo4j?
- **Simplicity**: No separate graph database needed
- **Performance**: Fast enough for <100k nodes
- **Deployment**: One less service to manage
- **Cost**: Neo4j Community has limitations

### Why FAISS CPU instead of GPU?
- **Cost**: No GPU required = cheaper deployment
- **Scale**: CPU version handles 100k vectors easily
- **Simplicity**: No CUDA dependencies

### Why Local LLM (Optional)?
- **Cost**: No API fees (OpenAI, Anthropic)
- **Privacy**: Data never leaves your infrastructure
- **Control**: Full control over model behavior
- **Tradeoff**: Lower quality than GPT-4, but sufficient for many tasks

---

## ✅ Production Checklist

Before deploying to production:

- [ ] Change `SECRET_KEY` and `ENCRYPTION_KEY`
- [ ] Set up PostgreSQL backups
- [ ] Configure Redis persistence
- [ ] Set up monitoring (Prometheus + Grafana)
- [ ] Configure log aggregation (ELK, Loki)
- [ ] Set up SSL/TLS (Let's Encrypt)
- [ ] Configure firewall rules
- [ ] Set up automated health checks
- [ ] Configure alerting (PagerDuty, Opsgenie)
- [ ] Review RBAC permissions
- [ ] Set up CI/CD pipeline
- [ ] Document runbooks
- [ ] Load test the system
- [ ] Set up disaster recovery plan

---

## 🤝 Contributing

This is a demo system. Contributions welcome:
1. Fork repository
2. Create feature branch
3. Add tests (when test suite exists)
4. Submit pull request

---

## 📄 License

MIT License - See LICENSE file

---

## 🙋 Support

- GitHub Issues: <repo-url>/issues
- Documentation: http://localhost:8000/docs

---

**Built with ❤️ using 100% free & open-source technologies**

No API keys • No vendor lock-in • Deploy anywhere
