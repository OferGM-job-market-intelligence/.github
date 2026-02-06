# Technology Decisions Log

Record of all major architectural and technology decisions.

## Multi-Repo Architecture

**Decision**: Use separate repository for each service (multi-repo)  
**Date**: Day 1  
**Rationale**:
- True microservices independence
- Separate CI/CD pipelines per service
- Independent versioning (scraper v1.2, nlp v2.1)
- Smaller clone sizes
- Clear ownership boundaries
- Enterprise standard (inspired by working in Check Point)
- Better for portfolio (demonstrates distributed systems understanding)

**Alternatives Considered**:
- Monorepo: Simpler but less "enterprise"
- Monolith: Not scalable, defeats microservices purpose, honestly didn't even considered it but whatever

**Impact**: 9 repositories to manage, requires documentation strategy

---

## Language Selection

### Bun.js for Scraper & API Gateway

**Decision**: Use Bun.js for I/O-bound services  
**Date**: Day 1  
**Rationale**:
- 3x faster startup than Node.js (15ms vs 50ms)
- Native TypeScript support (no build step)
- Excellent for async I/O operations
- Built-in hot reload for dev experience
- Growing ecosystem, shows cutting-edge awareness
- Faster HTTP server than Node.js

**Alternatives Considered**:
- Node.js: Mature but slower
- Deno: Good but less ecosystem support
- Go: Overkill for I/O bound tasks

**Impact**: Need Bun.js installed locally, smaller learning curve than Go

### Python for NLP Service

**Decision**: Use Python 3.11 for ML/NLP workload  
**Date**: Day 1  
**Rationale**:
- spaCy is industry standard for Named Entity Recognition
- Transformers library for advanced NLP
- scikit-learn for classification
- NO viable alternatives in other languages
- 85%+ accuracy achievable
- Best ML/NLP ecosystem globally

**Alternatives Considered**:
- Go: Immature ML ecosystem
- C++: Verbose, harder to prototype, will probably be faster but will take more time to implement

**Impact**: Need Python 3.11, pip, virtual environments

### Go for Aggregation & Auth Services

**Decision**: Use Go 1.21 for CPU-intensive and security-critical services  
**Date**: Day 1  
**Rationale**:
- **Performance**: 10x faster than Node.js for CPU-bound tasks
- **Concurrency**: Goroutines make parallel processing trivial
- **Security**: golang.org/x/crypto audited by Google
- **Password hashing**: 7x faster than bcrypt.js (120ms vs 850ms)
- **Memory safety**: No null pointer exceptions
- **Compilation**: Catches errors at build time
- **Industry standard**: Auth services at Google, Uber and ofc Check Point use Go

**Alternatives Considered**:
- Rust: Steeper learning curve, overkill
- Java: Verbose, slower startup

**Impact**: 
- Aggregation: Process 10K jobs in 300ms vs 1800ms (Node)
- Auth: Handle 10K concurrent logins easily

---

## Infrastructure Choices

### Apache Kafka

**Decision**: Use Kafka for event streaming  
**Date**: Day 1  
**Rationale**:
- Event streaming, not just messaging
- Message replay capability (can reprocess jobs)
- Higher throughput than RabbitMQ
- Better for microservices
- Industry standard
- Impressive for portfolio

**Alternatives Considered**:
- RabbitMQ: Simpler but limited replay
- AWS SQS: Vendor lock-in, not free locally
- Redis Streams: Too simple for this scale

**Impact**: Need Kafka + Zookeeper (Docker Compose), ~1GB RAM

### MongoDB Atlas

**Decision**: Use MongoDB for primary database  
**Date**: Day 1  
**Rationale**:
- Free 512MB tier (perfect for project)
- Flexible schema (job data varies by source)
- Native JSON storage
- Easy aggregation pipelines
- Good for unstructured data (job descriptions)
- Cloud-hosted (no local management)

**Alternatives Considered**:
- PostgreSQL: Rigid schema, harder for varied job data
- MySQL: Same issues as PostgreSQL
- DynamoDB: AWS-only, more complex

**Impact**: Free tier sufficient, need internet connection

### Redis

**Decision**: Use Redis for all caching and session needs  
**Date**: Day 1  
**Rationale**:
- Sub-millisecond response times
- Simple data structures (strings, sorted sets, hashes)
- Perfect for: trending data, rate limiting, sessions, deduplication
- Wide industry adoption
- Easy to set up

**Alternatives Considered**:
- Memcached: Simpler but less features

**Impact**: Required for rate limiting and performance

### Kubernetes

**Decision**: Deploy to Kubernetes (Minikube locally)  
**Date**: Day 1  
**Rationale**:
- Industry standard for microservices
- Auto-restart crashed pods
- Horizontal scaling
- Service discovery
- Production-ready orchestration
- Impressive for portfolio

**Alternatives Considered**:
- Docker Compose: Too simple for "enterprise"
- Docker Swarm: Dying technology
- Nomad: Less common

**Impact**: Need to learn K8s (part of goals), ~4GB RAM for Minikube

---

## API Architecture

### GraphQL + REST Hybrid

**Decision**: Support both GraphQL and REST  
**Date**: Day 1  
**Rationale**:
- **GraphQL**: Complex nested queries (dashboard needs job + skills + company + salary in 1 request)
- **REST**: Simple endpoints (health checks, webhooks, CSV exports)
- Reduces dashboard from 20+ requests to 1
- Best of both worlds
- Shows versatility

**Alternatives Considered**:
- GraphQL only: Webhooks awkward in GraphQL
- REST only: Frontend makes too many requests

**Impact**: More code but better UX and performance

### JWT Authentication

**Decision**: JWT with refresh tokens  
**Date**: Day 1  
**Rationale**:
- Stateless (better for microservices)
- Any service can verify token
- Refresh tokens enable revocation
- Industry standard for APIs
- Works across domains

**Alternatives Considered**:
- Session cookies: Stateful, harder with multiple services
- OAuth: Overkill for this project

**Impact**: Need to implement token refresh logic

---

## Development Practices

### Conventional Commits

**Decision**: Use conventional commit format  
**Date**: Day 1  
**Format**: `type(scope): subject`  
**Types**: feat, fix, docs, refactor, test, chore  
**Rationale**:
- Clear commit history
- Automated changelog generation
- Semantic versioning integration
- Professional standard

### Test Coverage Target

**Decision**: 80% minimum coverage  
**Date**: Day 1  
**Rationale**:
- Realistic and achievable
- Industry standard
- Focus on critical paths
- Not 100% (diminishing returns)

### CI/CD Strategy

**Decision**: Progressive CI/CD (Week 3, 6, 13, 15)  
**Date**: Day 1  
**Rationale**:
- Week 3: Basic lint/format (establish habits)
- Week 6: Add tests (once code exists)
- Week 13: Add security + build (before K8s)
- Week 15: Complete pipeline (production ready)

---

## Template for New Decisions
```markdown
### [Decision Title]
**Decision**: [What we decided]
**Date**: Day X
**Rationale**:
- Reason 1
- Reason 2
**Alternatives Considered**:
- Option A: Why not
- Option B: Why not
**Impact**: [How this affects the system]
```

---

**Last Updated**: Day 1
**Next Review**: After each major milestone