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


**Decision**: Managed Inheritance with GitHub Actions Reusable Workflows  
**Date**: Day 8 (updated from Day 1)  
**Rationale**:
- **Original plan** (Day 1): Progressive CI/CD across Weeks 3, 6, 13, 15
- **Updated approach**: Set up centralized reusable workflow templates *before* writing any service code
- Templates live in `.github` repo, service repos inherit via `workflow_call` (by reference, not by copy)
- Based on the Harness Pipeline Reuse Maturity Model — targeting Level 4: Managed Inheritance
- Update a template once → all 5 services get the change automatically on next CI run
- Prevents "pipeline sprawl" — 7 templates + 5 thin callers
- Progressive enablement still applies (enable jobs as services mature)

**Alternatives Considered**:
- Copy/Paste: Copy YAML per repo — leads to drift, maintenance hell with 5+ services
- Component Reuse: Shared GitHub Actions only — reuses steps but not flows, logic still fragmented
- Static Scaffolding: IDP/cookiecutter generates pipeline per repo — great Day 1, terrible Day 2 (Maintenance Wall)
- Flexible Governance: Insert blocks + OPA policies — overkill for 5 services, revisit if I scale

**Impact**: 
- Platform-level CI/CD changes require editing only 1 file (the template)
- Each service repo has a ~60-line caller YAML instead of 300+ lines of duplicated logic
- Supports all 3 languages (Bun.js, Python, Go) from the same template set
- Security scanner updates, tool changes, and compliance additions propagate instantly

**Reference**: [Harness Pipeline Reuse Maturity Model](https://www.harness.io/harness-devops-academy/scaling-ci-cd-templates-the-pipeline-reuse-maturity-model)

---

### CI/CD Trigger Strategy

**Decision**: Three-tier trigger model based on job cost and purpose  
**Date**: Day 8  
**Rationale**:

**Every push (any branch)** — fast feedback:
- `formatAndLint` — seconds to run, catches style issues immediately
- `unitTests` — fast, no infrastructure, catches regressions early

**Pull requests (targeting main/develop)** — quality gates before merge:
- `formatAndLint` + `unitTests` (same as above)
- `codeCoverageReport` — shows reviewers the coverage impact
- `integrationTests` — validates with real infrastructure (Kafka, Redis, MongoDB, ES)
- `scan` — catches vulnerabilities before they land on default branch

**Push to main only** — expensive/irreversible:
- `build` — compiles artifacts, builds and pushes Docker image to GHCR
- `deploy` — deploys to staging (and eventually production)

**Alternatives Considered**:
- Run everything on every push: Wastes CI minutes, builds Docker images nobody will use
- Run nothing until PR: Too late for feedback, developers find formatting issues after opening PR

**Impact**: Fast developer feedback loop (push → lint + tests in ~2 min), thorough PR gates, no wasted build/deploy on feature branches

---

### CI/CD Tool Selection

**Decision**: GitHub Actions with reusable workflows (`workflow_call`)  
**Date**: Day 8  
**Rationale**:
- Native to GitHub (where all repos live) — zero additional infrastructure
- `workflow_call` trigger enables true template-by-reference
- Free tier: 2,000 minutes/month for private repos (more than enough)
- Rich marketplace for actions (setup-bun, setup-go, trivy, gitleaks, etc.)
- Built-in GitHub Environments for deployment approvals
- SARIF upload for security findings in the Security tab

**Alternatives Considered**:
- Jenkins: Self-hosted, complex, perhaps overkill for this project
- GitLab CI: Would require migrating repos
- CircleCI: External service, less native GitHub integration
- Harness: Enterprise-grade but commercial product, overkill
- ArgoCD: Better for GitOps CD, not a full CI solution

**Impact**: No additional tooling to manage, all CI/CD visible in GitHub UI

---

### Security Scanning Pipeline

**Decision**: Four-layer security scanning in CI  
**Date**: Day 8  
**Rationale**:
1. **Secret Detection** (gitleaks) — prevents committed API keys, tokens, passwords
2. **Dependency Audit** (npm audit / pip-audit / govulncheck) — finds known CVEs in dependencies
3. **SAST** (Semgrep) — static analysis for OWASP Top 10, security anti-patterns
4. **Container Scanning** (Trivy) — OS and library vulnerabilities in Docker images

- Initially non-blocking (continue-on-error) to avoid blocking development
- Tighten to blocking on HIGH/CRITICAL severity by Week 12
- SARIF upload to GitHub Security tab for visibility

**Alternatives Considered**:
- Snyk: Commercial, free tier is limited
- SonarQube: Heavy, self-hosted, overkill for this stage
- CodeQL: GitHub-native but slower analysis
- No scanning: Unacceptable for enterprise-grade project

**Impact**: Security gates established from day 1, progressively enforced as project matures

---

### Docker Registry

**Decision**: GitHub Container Registry (GHCR) for Docker images  
**Date**: Day 8  
**Rationale**:
- Native to GitHub — same auth, same org, same permissions
- Free for public repos, generous limits for private
- Supports multi-platform images (linux/amd64)
- Automatic cleanup policies
- Docker Buildx with GHA cache for fast builds

**Alternatives Considered**:
- Docker Hub: Rate limits on pulls, separate auth
- AWS ECR: Vendor lock-in, requires AWS account
- Self-hosted: Maintenance overhead

**Impact**: Docker images live alongside code in the same GitHub org
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