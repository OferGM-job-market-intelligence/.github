# System Architecture Overview

**Project**: Job Market Intelligence Platform  
**Structure**: Multi-repo microservices  
**GitHub Org**: https://github.com/OferGM-job-market-intelligence

## 🎯 System Purpose

Real-time job market analytics platform that:
- Scrapes 10,000+ jobs daily from LinkedIn, Indeed, Glassdoor
- Extracts skills with 85%+ accuracy using ML/NLP (spaCy)
- Provides real-time trend analytics via GraphQL + REST API
- Features interactive dashboard with WebSocket updates

## 🏗️ Architecture Diagram
```
┌─────────────────────────────────────────────────────────────┐
│                    USER (Browser)                           │
│              React + TypeScript + Apollo Client             │
└───────────────────────┬─────────────────────────────────────┘
                        ↓ HTTPS + JWT
┌─────────────────────────────────────────────────────────────┐
│        API GATEWAY (Bun.js) - Repo: api-gateway             │
│          GraphQL + REST + WebSocket + JWT Verify            │
│                      Port: 4000                             │
└──────┬──────────────────────────────────┬───────────────────┘
       ↓                                  ↓
┌──────────────────┐              ┌──────────────────┐
│  AUTH SERVICE    │              │   REDIS CACHE    │
│  (Go) Port 3001  │              │  Trending Data   │
│  Repo: auth-     │              │  Sessions        │
│  service         │              │  Rate Limiting   │
└────────┬─────────┘              └──────────────────┘
         ↓
┌─────────────────────────────────────────────────────────────┐
│              MONGODB ATLAS (Cloud Database)                 │
│     Collections: users, jobs, skills, skill_trends          │
└──────────────────┬──────────────────────────────────────────┘
                   ↑
         ┌─────────┴─────────┐
         ↓                   ↓
┌──────────────────┐  ┌──────────────────┐
│  AGGREGATION     │  │   NLP SERVICE    │
│  SERVICE (Go)    │  │   (Python 3.11)  │
│  Port: 3003      │  │   Port: 3002     │
│  Repo: agg.-svc  │  │   Repo: nlp-svc  │
└──────────────────┘  └────────┬─────────┘
                               ↑
                      ┌────────┴────────┐
                      │  KAFKA BROKER   │
                      │  Event Stream   │
                      │  Topics:        │
                      │  - jobs.raw     │
                      │  - jobs.enriched│
                      └────────┬────────┘
                               ↑
                    ┌──────────┴──────────┐
                    │  SCRAPER SERVICE    │
                    │  (Bun.js/TypeScript)│
                    │  Port: 3000         │
                    │  Repo: scraper-svc  │
                    └─────────────────────┘
```

## 📦 Repository Structure

**GitHub Organization**: `job-market-intelligence`

### Service Repositories (5)
1. **scraper-service** - Job scraping (Bun.js)
2. **nlp-service** - Skill extraction (Python)
3. **aggregation-service** - Trend calculation (Go)
4. **auth-service** - Authentication (Go)
5. **api-gateway** - Unified API (Bun.js)

### Supporting Repositories (4)
6. **frontend** - React dashboard
7. **infrastructure** - Docker, K8s, Terraform, Helm
8. **shared** - Shared types and utilities
9. **.github** - Organization documentation

## 🛠️ Technology Stack

### Languages & Runtimes
- **Bun.js** (scraper, api-gateway) - I/O bound services
- **Python 3.11** (nlp) - ML/NLP workloads
- **Go 1.21** (aggregation, auth) - CPU intensive & security critical
- **TypeScript** - Type safety across services
- **React** - Frontend framework

### Infrastructure
- **Apache Kafka** - Event streaming
- **MongoDB Atlas** - Primary database (512MB free tier)
- **Redis** - Caching, sessions, rate limiting
- **Elasticsearch + Kibana** - Logging and observability
- **Kubernetes** - Container orchestration
- **Docker** - Containerization
- **Terraform** - Infrastructure as Code
- **Helm** - Kubernetes package manager

### APIs & Security
- **GraphQL** - Complex nested queries
- **REST** - Simple endpoints
- **WebSocket** - Real-time updates
- **JWT** - Stateless authentication
- **bcrypt** - Password hashing (cost 12)
- **RBAC** - Role-based access control

## 🔄 Data Flow

### 1. Job Scraping Flow
```
Scraper Service (hourly)
  → Check rate limits (Redis)
  → Scrape LinkedIn/Indeed/Glassdoor
  → Check duplicates (Redis)
  → Publish to Kafka (jobs.raw)
```

### 2. NLP Processing Flow
```
Kafka (jobs.raw)
  → NLP Service consumes
  → Load spaCy model
  → Extract skills (NER + taxonomy)
  → Parse salary (regex)
  → Classify experience level
  → Save to MongoDB
  → Publish to Kafka (jobs.enriched)
```

### 3. Aggregation Flow
```
Trigger every 15 minutes
  → Query MongoDB (last 24h jobs)
  → Calculate trends with goroutines
  → Update Redis cache
  → Store time-series in MongoDB
```

### 4. API Request Flow
```
Client request (JWT in header)
  → API Gateway verifies JWT
  → Check Redis cache
  → Query MongoDB if cache miss
  → Return data
  → WebSocket pushes real-time updates
```

### 5. Authentication Flow
```
User login
  → Auth Service verifies password (bcrypt)
  → Generate JWT access token (15min)
  → Generate JWT refresh token (7d)
  → Store refresh token in Redis
  → Return tokens to client
```

## 📊 Performance Targets

| Service | Metric | Target |
|---------|--------|--------|
| Scraper | Jobs/hour | 500+ |
| Scraper | Startup time | <20ms |
| NLP | Processing rate | 50+ jobs/sec |
| NLP | Accuracy | 85%+ |
| Aggregation | 10K jobs | <500ms |
| Auth | Login time | <50ms |
| API Gateway | Cached | <10ms |
| API Gateway | Uncached | <50ms |
| Frontend | Initial load | <2s |

## 🔒 Security Architecture

### Authentication Layers
1. **JWT Access Tokens** - 15min expiry, stateless
2. **Refresh Tokens** - 7 day expiry, stored in Redis
3. **Password Hashing** - bcrypt cost 12 (~150ms)
4. **Rate Limiting** - 5 login attempts per minute
5. **Token Revocation** - Logout removes from Redis

### Authorization Layers
1. **Public** - Health checks, signup, login
2. **Authenticated** - Dashboard, job search
3. **Premium** - Advanced analytics, CSV exports
4. **Admin** - User management, system config

## 🚀 Deployment Strategy

## 🔄 CI/CD Architecture

### Pipeline Inheritance Model (L4)

All CI/CD logic is centralized in the `.github` repo as reusable workflows.
Service repos contain thin ~60-line caller YAMLs that inherit by reference.

```
.github repo (templates)          Service repos (callers)
┌──────────────────────┐          ┌─────────────────────┐
│ template-format-lint │◄─────────│ scraper-service     │
│ template-unit-tests  │◄─────────│ api-gateway         │
│ template-integ-tests │◄─────────│ nlp-service         │
│ template-coverage    │◄─────────│ aggregation-service │
│ template-scan        │◄─────────│ auth-service        │
│ template-build       │          └─────────────────────┘
│ template-deploy      │          workflow_call (by ref)
└──────────────────────┘
```

### Pipeline Stages

```
Push (any branch)         Pull Request              Push to main
─────────────────         ────────────              ────────────
 formatAndLint ──┐         formatAndLint             build
 unitTests ──────┤         unitTests                   │
                 │         codeCoverage               deploy
                 │         integrationTests         (staging)
                 │         scan                        │
                 │                                   deploy
                 │                                (production)
                 └─► Fast feedback (~2 min)
```

### Security Scanning Layers
1. **gitleaks** — Secret detection (committed API keys, tokens)
2. **Dependency audit** — npm audit / pip-audit / govulncheck
3. **Semgrep SAST** — Static analysis (OWASP Top 10)
4. **Trivy** — Container image vulnerabilities

### Docker Images
- **Registry**: GitHub Container Registry (ghcr.io)
- **Build**: Docker Buildx with GHA cache
- **Tags**: SHA, branch, semver, latest (on main)
- **Platforms**: linux/amd64

### Deployment
- **Staging**: Auto-deploy on push to main
- **Production**: Manual approval via GitHub Environments
- **Strategy**: Rolling (default), canary (production)
- **Safety**: Health check + auto-rollback on failure

---

### Update "Related Documentation" section:

- [CI/CD Templates](../../.github/.github/workflows/) — Reusable workflow templates

### Local Development
- Docker Compose for infrastructure
- Each service runs independently
- Hot reload enabled

### Production (Kubernetes)
- 2-3 replicas per service
- Horizontal Pod Autoscaler
- Rolling updates
- Health checks (liveness, readiness)
- Resource limits enforced

## 📈 Scalability

### Horizontal Scaling
- Scraper: Scale to N pods for more sources
- NLP: Scale based on Kafka consumer lag
- Aggregation: Single instance (scheduled job)
- Auth: Scale for high concurrency
- API Gateway: Scale based on request rate

### Database Scaling
- MongoDB: Sharding (if needed)
- Redis: Cluster mode (if needed)
- Kafka: Add partitions/brokers

## 🔗 Inter-Service Communication

- **Synchronous**: REST/GraphQL (gateway to services)
- **Asynchronous**: Kafka (scraper → nlp)
- **Caching**: Redis (all services)
- **Service Discovery**: Kubernetes DNS

## 📖 Related Documentation

- [90-Day Work Plan](90-day-detailed-workplan.md)
- [Technology Decisions](DECISIONS.md)
- [Coding Conventions](CONVENTIONS.md)
- [Infrastructure Setup](../../infrastructure/README.md)

---

**Last Updated**: Day 1  
**Status**: Foundation Complete ✅