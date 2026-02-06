# Job Market Intelligence Platform
## 90-Day Detailed Implementation Plan

**Last Updated**: Day 1 - 06/02/2026 (sixth of february)  
**Architecture**: Multi-repo (9 repositories)  
**Total Time**: 130-195 hours @ 10-15 hours/week  
**Completion Target**: 13 weeks

---

## 📋 Table of Contents

- [Repository Structure](#repository-structure)
- [Week 1: Foundation](#week-1-foundation-days-1-7)
- [Week 2-3: Scraper Service](#week-2-3-scraper-service-days-8-14)
- [Week 4-5: NLP Service](#week-4-5-nlp-service-days-15-22)
- [Week 6: Aggregation Service](#week-6-aggregation-service-days-23-28)
- [Week 7-8: Auth Service](#week-7-8-auth-service-days-29-37)
- [Week 9-10: API Gateway](#week-9-10-api-gateway-days-38-47)
- [Week 11-12: Frontend](#week-11-12-frontend-days-48-60)
- [Week 13: Kubernetes](#week-13-kubernetes-days-61-67)
- [Week 14: Observability](#week-14-observability-days-68-73)
- [Week 15: Production](#week-15-production-ready-days-74-90)
- [Summary](#summary)

---

## 📁 Repository Structure
```
GitHub Organization: job-market-intelligence

Service Repositories (5):
├── scraper-service         # Typescript - Job scraping
├── nlp-service             # Python - Skill extraction
├── aggregation-service     # Go - Trend calculation
├── auth-service            # Go - Authentication
└── api-gateway             # Typescript - GraphQL/REST API

Supporting Repositories (4):
├── frontend                # React - Dashboard UI
├── infrastructure          # Docker, K8s, Terraform
├── shared                  # Types & utilities
└── .github                 # Organization docs
```

---

## Week 1: Foundation (Days 1-7)

**Goal**: Set up organization, repositories, and local development infrastructure  
**Total Time**: 15-20 hours

---

### Day 1: Multi-Repo Organization Setup ✅

**Time**: 3-4 hours  
**Repository**: All (initialization)  
**Status**: Complete

#### Tasks

- [x] Create GitHub organization `job-market-intelligence`
- [x] Create 9 repositories with descriptions
- [x] Initialize each repository with structure
- [x] Set up `.github/docs` for system-wide documentation
- [x] Set up `infrastructure/docs` for operational tracking
- [x] Create organization profile README
- [x] Configure Git with conventional commits

#### Deliverable

✅ 9 repositories with enterprise structure

#### Validation

All repositories visible at: https://github.com/OferGM-job-market-intelligence

---

### Day 2: Docker Compose Infrastructure

**Time**: 2-3 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Navigate to infrastructure repository
- [ ] Create `docker/docker-compose.yml` with services:
  - Zookeeper (port 2181)
  - Kafka (ports 9092, 29092)
  - Redis (port 6379)
  - Elasticsearch (port 9200)
  - Kibana (port 5601)
  - LocalStack (port 4566)
- [ ] Create `docker/.env.example` with all environment variables
- [ ] Configure volume mounts for data persistence
- [ ] Test: `docker-compose -f docker/docker-compose.yml up -d`
- [ ] Verify all services healthy
- [ ] Document startup/shutdown procedures in README
- [ ] Commit with message: `feat(infra): add docker-compose for local development`
- [ ] Push to GitHub

#### Deliverable

Working local infrastructure with 6 services

#### Validation
```bash
cd infrastructure
docker-compose -f docker/docker-compose.yml ps
# All services should show "Up" status
```

---

### Day 3: MongoDB Atlas Setup

**Time**: 2 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Create MongoDB Atlas account at https://www.mongodb.com/cloud/atlas
- [ ] Create free M0 cluster (512MB)
- [ ] Configure cluster:
  - Cloud Provider: AWS
  - Region: Closest to you
  - Cluster Name: `job-market-cluster`
- [ ] Create database: `job_market`
- [ ] Create collections:
  - `users`
  - `jobs`
  - `skills`
  - `skill_trends`
- [ ] Create database user with read/write permissions
- [ ] Whitelist IP: `0.0.0.0/0` (allow from anywhere)
- [ ] Copy connection string
- [ ] Add to `docker/.env.example` as `MONGODB_URI`
- [ ] Test connection with mongosh
- [ ] Document setup in `infrastructure/docs/mongodb-setup.md`
- [ ] Commit and push

#### Deliverable

MongoDB Atlas cluster configured and accessible

#### Validation
```bash
mongosh "mongodb+srv://your-connection-string"
# Should connect successfully
```

---

### Day 4: MongoDB Testing & Indexes

**Time**: 1-2 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Connect to MongoDB Atlas
- [ ] Create test documents in each collection
- [ ] Add indexes:
  - `jobs`: `job_id` (unique), `scraped_at`, `skills_extracted`
  - `skills`: `skill_id` (unique), `canonical_name`
  - `skill_trends`: `skill_id`, `date`
  - `users`: `email` (unique)
- [ ] Test queries on each collection
- [ ] Verify index performance with `.explain()`
- [ ] Document schema in `infrastructure/docs/mongodb-schema.md`
- [ ] Update `PROGRESS.md` in infrastructure
- [ ] Commit and push

#### Deliverable

MongoDB with proper indexes and test data

#### Validation

Collections exist with indexes configured

---

### Day 5: Shared Types Repository

**Time**: 2-3 hours  
**Repository**: `shared`

#### Tasks

- [ ] Navigate to shared repository
- [ ] Create `types/job.ts`:
  - `JobPosting` interface
  - `Location` interface
  - `Salary` interface
- [ ] Create `types/skill.ts`:
  - `Skill` interface
  - `SkillTrend` interface
  - `SkillCategory` enum
- [ ] Create `types/user.ts`:
  - `User` interface
  - `TokenPair` interface
  - `LoginRequest` interface
  - `SignupRequest` interface
- [ ] Create `types/analytics.ts`:
  - `SalaryStats` interface
  - `MarketInsights` interface
- [ ] Create `index.ts` to export all types
- [ ] Update `package.json`
- [ ] Test imports work
- [ ] Commit with message: `feat(shared): add TypeScript type definitions`
- [ ] Push to GitHub

#### Deliverable

Complete type definitions for all services

#### Validation

Types can be imported: `import { JobPosting } from '@job-market/shared'`

---

### Day 6: Skill Taxonomy Data

**Time**: 2-3 hours  
**Repository**: `shared`

#### Tasks

- [ ] Navigate to shared repository
- [ ] Create `data/skill-taxonomy.json`
- [ ] Add 500+ skills across categories:
  - Programming Languages (50+): Python, JavaScript, TypeScript, Go, Rust, Java, C++, C#, PHP, Ruby, Swift, Kotlin, etc.
  - Frameworks (100+): React, Angular, Vue, Django, Flask, FastAPI, Spring, Express, Next.js, etc.
  - Databases (30+): MongoDB, PostgreSQL, MySQL, Redis, Cassandra, DynamoDB, etc.
  - Cloud Platforms (20+): AWS, Azure, GCP, DigitalOcean, Heroku, etc.
  - DevOps Tools (50+): Docker, Kubernetes, Terraform, Ansible, Jenkins, GitLab CI, GitHub Actions, etc.
  - ML/Data Science (50+): TensorFlow, PyTorch, scikit-learn, Pandas, NumPy, Jupyter, etc.
  - Testing (20+): Jest, Pytest, JUnit, Selenium, Cypress, etc.
  - Soft Skills (50+): Leadership, Communication, Problem Solving, etc.
- [ ] For each skill include:
  - `canonical` name
  - `aliases` array
  - `category`
  - `related` skills array
- [ ] Create `data/constants.ts` for shared constants
- [ ] Commit and push

#### Deliverable

Comprehensive skill taxonomy with 500+ skills

#### Validation

JSON file parses correctly and contains 500+ skills

---

### Day 7: Scripts & Automation

**Time**: 2-3 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Navigate to infrastructure repository
- [ ] Create `scripts/install-all.sh`:
  - Check for Bun, Go, Python installations
  - Provide installation instructions if missing
  - Clone all repositories (if not already)
  - Run install commands per repo
- [ ] Create `scripts/start-all.sh`:
  - Start Docker Compose
  - Provide commands for starting each service
- [ ] Create `scripts/check-health.sh`:
  - Verify Docker services running
  - Check port availability
  - Test connections
- [ ] Make scripts executable: `chmod +x scripts/*.sh`
- [ ] Update root `Makefile` with commands:
  - `make install` → run install-all.sh
  - `make dev` → start infrastructure
  - `make health` → check health
- [ ] Test each script
- [ ] Document usage in README
- [ ] Update `infrastructure/docs/PROGRESS.md`:
  - Mark Week 1 complete
  - Add completion time
- [ ] Commit and push

#### Deliverable

✅ Foundation complete with automation scripts

#### Validation
```bash
make dev
# Infrastructure should start successfully
```

---

## Week 2-3: Scraper Service (Days 8-14)

**Goal**: Build job scraping service with Bun.js  
**Total Time**: 20-25 hours  
**Repository**: `scraper-service`

---

### Day 8: Scraper Project Setup

**Time**: 2-3 hours  
**Repository**: `scraper-service`

#### Tasks

- [ ] Navigate to scraper-service repository
- [ ] Initialize Bun project: `bun init -y`
- [ ] Install dependencies:
```bash
bun add kafkajs ioredis cheerio axios zod
bun add -d bun-types @types/node
```
- [ ] Create directory structure:
```
src/
├── index.ts
├── scrapers/
│   ├── base-scraper.ts
│   ├── linkedin-scraper.ts
│   └── indeed-scraper.ts
├── kafka/
│   └── producer.ts
├── redis/
│   └── client.ts
└── utils/
    ├── rate-limiter.ts
    └── logger.ts
```
- [ ] Create `tsconfig.json`
- [ ] Update `package.json` with scripts:
  - `"dev": "bun --watch src/index.ts"`
  - `"start": "bun src/index.ts"`
  - `"test": "bun test"`
- [ ] Create `.env.example`:
```
KAFKA_BROKERS=localhost:29092
REDIS_HOST=localhost
REDIS_PORT=6379
SCRAPE_INTERVAL=3600000
```
- [ ] Test: `bun run src/index.ts`
- [ ] Commit and push

#### Deliverable

Scraper project initialized with proper structure

#### Validation

`bun run src/index.ts` executes without errors

---

### Day 9: Kafka & Redis Integration

**Time**: 2 hours  
**Repository**: `scraper-service`

#### Tasks

- [ ] Create `src/kafka/producer.ts`:
  - Initialize Kafka client
  - Create producer
  - Implement `publishToKafka()` function
  - Add error handling and retries
- [ ] Create `src/redis/client.ts`:
  - Initialize Redis client
  - Export client instance
  - Add connection error handling
- [ ] Create `src/utils/logger.ts`:
  - Structured JSON logging
  - Include timestamp, level, service name
- [ ] Create basic `src/index.ts`:
  - Initialize connections
  - Test publish to Kafka
  - Test Redis get/set
- [ ] Test Kafka: Publish test message, verify with console consumer
- [ ] Test Redis: Set/get test key
- [ ] Commit and push

#### Deliverable

Working Kafka producer and Redis client

#### Validation
```bash
# In one terminal
kafka-console-consumer --topic jobs.raw --bootstrap-server localhost:29092

# In another terminal
bun run src/index.ts
# Should see test message in consumer
```

---

### Day 10: Base Scraper Class

**Time**: 3-4 hours  
**Repository**: `scraper-service`

#### Tasks

- [ ] Create `src/scrapers/base-scraper.ts`:
  - Abstract class with common methods
  - `scrape()` abstract method
  - `checkRateLimit()` method
  - `checkDuplicate()` method
  - `publishJob()` method
  - Error handling
- [ ] Implement rate limiting logic:
  - Use Redis INCR
  - Set TTL for rate limit window
  - Max 50 requests/hour per source
- [ ] Implement deduplication:
  - Hash job URL
  - Check Redis for existence
  - Store with 30 day TTL
- [ ] Add comprehensive logging
- [ ] Write unit tests for base class
- [ ] Commit and push

#### Deliverable

Reusable base scraper with rate limiting

#### Validation

Tests pass for rate limiting and deduplication

---

### Day 11: LinkedIn Scraper

**Time**: 3-4 hours  
**Repository**: `scraper-service`

#### Tasks

- [ ] Create `src/scrapers/linkedin-scraper.ts`:
  - Extend base scraper
  - Implement `scrape()` method
- [ ] Use Cheerio to parse HTML:
  - Extract job title
  - Extract company name
  - Extract location
  - Extract description
  - Extract URL
  - Extract posted date
- [ ] Handle pagination (if applicable)
- [ ] Parse location string into structured format
- [ ] Add specific error handling
- [ ] Test with 10 real LinkedIn job listings
- [ ] Log all extracted data
- [ ] Commit and push

#### Deliverable

Working LinkedIn scraper

#### Validation

Scraper successfully extracts data from 10 LinkedIn jobs

---

### Day 12: LinkedIn Integration & Testing

**Time**: 2-3 hours  
**Repository**: `scraper-service`

#### Tasks

- [ ] Integrate LinkedIn scraper into main flow
- [ ] Add to scheduler (run every hour)
- [ ] Implement full pipeline:
  1. Check rate limit
  2. Scrape jobs
  3. Check duplicates
  4. Publish to Kafka `jobs.raw`
- [ ] Add structured error logging
- [ ] Monitor Kafka with console consumer
- [ ] Test end-to-end with 20+ jobs
- [ ] Verify rate limiting works (attempt to exceed limit)
- [ ] Verify deduplication (scrape same jobs twice)
- [ ] Commit and push

#### Deliverable

LinkedIn scraper publishing to Kafka

#### Validation
```bash
kafka-console-consumer --topic jobs.raw --bootstrap-server localhost:29092
# Should see jobs appearing
```

---

### Day 13: Indeed Scraper

**Time**: 2-3 hours  
**Repository**: `scraper-service`

#### Tasks

- [ ] Create `src/scrapers/indeed-scraper.ts`:
  - Extend base scraper
  - Handle Indeed's HTML structure
  - Extract same fields as LinkedIn
- [ ] Add Indeed-specific parsing logic
- [ ] Add to scheduler with 2-hour offset (to stagger requests)
- [ ] Test with 20+ Indeed job listings
- [ ] Verify rate limiting works across sources
- [ ] Update README with both scrapers
- [ ] Commit and push

#### Deliverable

Indeed scraper working alongside LinkedIn

#### Validation

Both LinkedIn and Indeed jobs appearing in Kafka

---

### Day 14: Docker & Documentation

**Time**: 2 hours  
**Repository**: `scraper-service`

#### Tasks

- [ ] Create `Dockerfile`:
```dockerfile
FROM oven/bun:1 AS base
WORKDIR /app

# Install dependencies
COPY package.json bun.lockb ./
RUN bun install --frozen-lockfile --production

# Copy source
COPY . .

# Run
CMD ["bun", "run", "src/index.ts"]
```
- [ ] Create `.dockerignore`
- [ ] Build Docker image:
```bash
docker build -t job-market/scraper:v1 .
```
- [ ] Test in Docker:
```bash
docker run --env-file .env job-market/scraper:v1
```
- [ ] Write tests in `tests/scrapers.test.ts`
- [ ] Run tests: `bun test`
- [ ] Update README with:
  - Complete usage instructions
  - Environment variables
  - Docker instructions
- [ ] Update `infrastructure/docs/PROGRESS.md`:
  - Mark Week 2-3 complete
  - Add actual time spent
  - Note any challenges
- [ ] Commit and push

#### Deliverable

✅ Scraper service complete with Docker support

#### Validation
```bash
docker run job-market/scraper:v1
# Scraper runs successfully in container
# Jobs appear in Kafka
```

---

## Week 4-5: NLP Service (Days 15-22)

**Goal**: Build skill extraction service with Python  
**Total Time**: 25-30 hours  
**Repository**: `nlp-service`

---

### Day 15: Python Environment Setup

**Time**: 2-3 hours  
**Repository**: `nlp-service`

#### Tasks

- [ ] Navigate to nlp-service repository
- [ ] Create virtual environment:
```bash
python3 -m venv venv
source venv/bin/activate  # On Mac/Linux
# or
venv\Scripts\activate  # On Windows
```
- [ ] Create `requirements.txt`:
```
spacy==3.7.0
kafka-python==2.0.2
pymongo==4.6.0
scikit-learn==1.3.2
nltk==3.8.1
python-dotenv==1.0.0
```
- [ ] Install dependencies:
```bash
pip install -r requirements.txt
```
- [ ] Download spaCy model:
```bash
python -m spacy download en_core_web_lg
```
- [ ] Test imports in Python REPL
- [ ] Create `.env.example`
- [ ] Commit and push

#### Deliverable

Python environment with all NLP dependencies

#### Validation
```python
import spacy
nlp = spacy.load("en_core_web_lg")
# Should load without errors
```

---

### Day 16: Project Structure & Kafka Consumer

**Time**: 2 hours  
**Repository**: `nlp-service`

#### Tasks

- [ ] Create directory structure:
```
src/
├── main.py
├── kafka_consumer.py
├── nlp/
│   ├── __init__.py
│   ├── skill_extractor.py
│   ├── salary_parser.py
│   └── experience_classifier.py
├── storage/
│   └── mongodb_client.py
└── utils/
    └── logger.py
```
- [ ] Create `src/kafka_consumer.py`:
  - KafkaConsumer setup
  - Subscribe to `jobs.raw` topic
  - Message deserialization
- [ ] Create `src/storage/mongodb_client.py`:
  - MongoDB connection
  - Insert/update methods
- [ ] Create `src/utils/logger.py`:
  - Python logging setup
  - JSON structured logging
- [ ] Create basic `src/main.py`:
  - Initialize connections
  - Test Kafka consumption
  - Test MongoDB connection
- [ ] Test consuming messages from Kafka
- [ ] Commit and push

#### Deliverable

Python project structure with Kafka consumer

#### Validation

Consumes messages from `jobs.raw` topic successfully

---

### Day 17: Skill Extraction - Foundation

**Time**: 4 hours  
**Repository**: `nlp-service`

#### Tasks

- [ ] Create `src/nlp/skill_extractor.py`
- [ ] Load spaCy model in class constructor
- [ ] Load skill taxonomy from `shared/data/skill-taxonomy.json`
- [ ] Implement token-level matching:
  - Tokenize job description
  - Check each token against taxonomy
  - Case-insensitive matching
- [ ] Implement Named Entity Recognition:
  - Use spaCy's NER
  - Train custom entity recognizer (optional)
- [ ] Create regex patterns for common skills:
  - Programming languages
  - Frameworks
  - Tools
- [ ] Test with 10 sample job descriptions
- [ ] Log extraction results
- [ ] Commit and push

#### Deliverable

Basic skill extraction working

#### Validation

Extracts at least 60% of skills correctly from test data

---

### Day 18: Skill Extraction - Normalization

**Time**: 3-4 hours  
**Repository**: `nlp-service`

#### Tasks

- [ ] Implement skill normalization:
  - "python" → "Python"
  - "react.js" → "React"
  - "node js" → "Node.js"
- [ ] Handle aliases from taxonomy:
  - Map all aliases to canonical name
  - Case handling
- [ ] Implement confidence scoring:
  - 1.0 for exact match
  - 0.8 for alias match
  - 0.6 for partial match
- [ ] Remove duplicates from results
- [ ] Sort by confidence score
- [ ] Test with 50 diverse job descriptions:
  - Software engineering
  - Data science
  - Product management
  - DevOps
- [ ] Measure accuracy (target: 80%+)
- [ ] Tune patterns and rules
- [ ] Commit and push

#### Deliverable

Skill extraction with normalization and confidence scoring

#### Validation

Achieves 80%+ accuracy on test dataset

---

### Day 19: Salary Parser

**Time**: 2-4 hours  
**Repository**: `nlp-service`

#### Tasks

- [ ] Create `src/nlp/salary_parser.py`
- [ ] Implement regex patterns for salary formats:
  - "$100k - $150k"
  - "$100,000 - $150,000"
  - "$50-60/hour"
  - "$100k-$150k per year"
  - "100k-150k"
- [ ] Parse into structured format:
```python
{
  "min": 100000,
  "max": 150000,
  "currency": "USD",
  "period": "year"
}
```
- [ ] Handle edge cases:
  - Missing max value
  - Hourly vs yearly
  - Different currencies
- [ ] Test with 100+ salary strings
- [ ] Log parsing results and edge cases
- [ ] Commit and push

#### Deliverable

Salary parser with 90%+ success rate

#### Validation

Correctly parses 90+ out of 100 test salary strings

---

### Day 20: Experience Classifier

**Time**: 3 hours  
**Repository**: `nlp-service`

#### Tasks

- [ ] Create `src/nlp/experience_classifier.py`
- [ ] Define keyword mappings:
  - Entry/Junior: "entry level", "0-1 years", "junior", "graduate"
  - Mid: "2-4 years", "mid level", "intermediate"
  - Senior: "5+ years", "senior", "experienced"
  - Lead: "lead", "principal", "staff", "7+ years"
- [ ] Implement classification logic:
  - Search for keywords in description
  - Weight by frequency
  - Return highest confidence level
- [ ] Optional: Simple ML classifier with sklearn:
  - TF-IDF vectorization
  - Logistic regression
- [ ] Test on 100 job descriptions
- [ ] Measure accuracy (target: 75%+)
- [ ] Commit and push

#### Deliverable

Experience level classifier

#### Validation

Achieves 75%+ accuracy on test dataset

---

### Day 21: NLP Pipeline Integration

**Time**: 2-3 hours  
**Repository**: `nlp-service`

#### Tasks

- [ ] Update `src/main.py` to integrate all NLP components
- [ ] Implement complete pipeline:
  1. Consume job from Kafka `jobs.raw`
  2. Extract skills with `SkillExtractor`
  3. Parse salary with `SalaryParser`
  4. Classify experience with `ExperienceClassifier`
  5. Create enriched job object
  6. Save to MongoDB `jobs` collection
  7. Publish to Kafka `jobs.enriched` topic
- [ ] Add error handling for each step
- [ ] Add comprehensive logging
- [ ] Test end-to-end with 20+ real jobs
- [ ] Verify data in MongoDB
- [ ] Monitor `jobs.enriched` topic
- [ ] Commit and push

#### Deliverable

Complete NLP pipeline processing jobs

#### Validation
```bash
# Check MongoDB
mongosh
use job_market
db.jobs.find().limit(5)
# Should show enriched jobs with skills_extracted field

# Check Kafka
kafka-console-consumer --topic jobs.enriched --bootstrap-server localhost:29092
# Should see enriched jobs
```

---

### Day 22: Docker, Testing & Documentation

**Time**: 3-4 hours  
**Repository**: `nlp-service`

#### Tasks

- [ ] Create `Dockerfile`:
```dockerfile
FROM python:3.11-slim
WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
RUN python -m spacy download en_core_web_lg

# Copy source
COPY . .

# Run
CMD ["python", "src/main.py"]
```
- [ ] Create `.dockerignore`
- [ ] Build image:
```bash
docker build -t job-market/nlp:v1 .
```
- [ ] Test in Docker
- [ ] Write tests in `tests/`:
  - `test_skill_extractor.py`
  - `test_salary_parser.py`
  - `test_experience_classifier.py`
- [ ] Run tests: `pytest tests/`
- [ ] Measure final accuracy:
  - Skill extraction: 85%+
  - Salary parsing: 90%+
  - Experience classification: 75%+
- [ ] Update README with:
  - Setup instructions
  - Environment variables
  - Accuracy metrics
  - Docker usage
- [ ] Update `infrastructure/docs/PROGRESS.md`:
  - Mark Week 4-5 complete
  - Add time spent
  - Note accuracy achieved
- [ ] Commit and push

#### Deliverable

✅ NLP service complete with 85%+ accuracy

#### Validation
```bash
# Run tests
pytest tests/
# All tests pass

# Docker test
docker run job-market/nlp:v1
# Processes jobs successfully
```

---

## Week 6: Aggregation Service (Days 23-28)

**Goal**: Build trend calculation service with Go  
**Total Time**: 15-18 hours  
**Repository**: `aggregation-service`

---

### Day 23: Go Project Setup

**Time**: 2-3 hours  
**Repository**: `aggregation-service`

#### Tasks

- [ ] Navigate to aggregation-service repository
- [ ] Initialize Go module:
```bash
go mod init github.com/job-market-intelligence/aggregation-service
```
- [ ] Install dependencies:
```bash
go get go.mongodb.org/mongo-driver/mongo
go get github.com/go-redis/redis/v8
go get github.com/gofiber/fiber/v2
```
- [ ] Create directory structure:
```
cmd/
└── main.go
internal/
├── aggregator/
│   ├── skill_trends.go
│   └── salary_stats.go
└── storage/
    ├── mongodb.go
    └── redis.go
```
- [ ] Create `cmd/main.go` with basic setup
- [ ] Test: `go run cmd/main.go`
- [ ] Commit and push

#### Deliverable

Go project initialized with proper structure

#### Validation

`go run cmd/main.go` executes without errors

---

### Day 24: Storage Clients

**Time**: 2 hours  
**Repository**: `aggregation-service`

#### Tasks

- [ ] Create `internal/storage/mongodb.go`:
  - MongoDB client initialization
  - Connection pool configuration
  - Health check method
- [ ] Create `internal/storage/redis.go`:
  - Redis client initialization
  - Connection configuration
  - Health check method
- [ ] Test MongoDB connection
- [ ] Test Redis connection
- [ ] Add graceful shutdown handling
- [ ] Commit and push

#### Deliverable

Working MongoDB and Redis clients for Go

#### Validation

Both connections establish and health checks pass

---

### Day 25: Skill Trends Aggregation

**Time**: 4 hours  
**Repository**: `aggregation-service`

#### Tasks

- [ ] Create `internal/aggregator/skill_trends.go`
- [ ] Build MongoDB aggregation pipeline:
```go
pipeline := []bson.M{
  {"$match": bson.M{"scraped_at": bson.M{"$gte": yesterday}}},
  {"$unwind": "$skills_extracted"},
  {"$group": bson.M{
    "_id": "$skills_extracted",
    "count": bson.M{"$sum": 1},
    "avg_salary": bson.M{"$avg": "$salary.min"},
  }},
  {"$sort": bson.M{"count": -1}},
  {"$limit": 100},
}
```
- [ ] Execute aggregation
- [ ] Map results to `SkillTrend` structs
- [ ] Test with sample data in MongoDB
- [ ] Log aggregation results
- [ ] Commit and push

#### Deliverable

Skill trends aggregation pipeline working

#### Validation

Aggregation returns top 100 trending skills from sample data

---

### Day 26: Parallel Processing & Caching

**Time**: 3-4 hours  
**Repository**: `aggregation-service`

#### Tasks

- [ ] Implement goroutines for parallel processing:
```go
for _, trend := range trends {
  go func(t SkillTrend) {
    saveTrend(t)
    updateCache(t)
  }(trend)
}
```
- [ ] Save results to MongoDB `skill_trends` collection
- [ ] Update Redis cache with trending skills
- [ ] Add error handling for goroutines using `sync.WaitGroup`
- [ ] Add mutex for shared state protection
- [ ] Test concurrent writes to MongoDB
- [ ] Test Redis cache updates
- [ ] Commit and push

#### Deliverable

Parallel trend processing with caching

#### Validation

Trends saved to MongoDB and cached in Redis concurrently

---

### Day 27: Salary Statistics

**Time**: 2-4 hours  
**Repository**: `aggregation-service`

#### Tasks

- [ ] Create `internal/aggregator/salary_stats.go`
- [ ] Calculate salary statistics:
  - Min, max, median, mean
  - Percentiles (25th, 75th)
  - By experience level
  - By location
- [ ] Store results in Redis with 15-minute TTL
- [ ] Test aggregations with varied salary data
- [ ] Verify Redis cache expiration
- [ ] Commit and push

#### Deliverable

Salary statistics calculator with Redis caching

#### Validation

Salary stats calculated and cached with correct TTL

---

### Day 28: Scheduler & Docker

**Time**: 3-4 hours  
**Repository**: `aggregation-service`

#### Tasks

- [ ] Add scheduler to run every 15 minutes:
```go
ticker := time.NewTicker(15 * time.Minute)
for range ticker.C {
  calculateTrends()
}
```
- [ ] Create `Dockerfile`:
```dockerfile
FROM golang:1.21-alpine AS builder
WORKDIR /app
COPY go.* ./
RUN go mod download
COPY . .
RUN go build -o aggregation cmd/main.go

FROM alpine:latest
COPY --from=builder /app/aggregation /aggregation
CMD ["/aggregation"]
```
- [ ] Build image:
```bash
docker build -t job-market/aggregation:v1 .
```
- [ ] Test: Process 10K jobs in <500ms
- [ ] Write tests in `internal/aggregator/trends_test.go`
- [ ] Run tests: `go test ./...`
- [ ] Update README with:
  - Setup instructions
  - Environment variables
  - Performance benchmarks
  - Docker usage
- [ ] Update `infrastructure/docs/PROGRESS.md`:
  - Mark Week 6 complete
  - Add time spent
  - Note performance metrics
- [ ] Commit and push

#### Deliverable

✅ Trending skills updated every 15min in Redis

#### Validation
```bash
docker run job-market/aggregation:v1
# Aggregation runs successfully
# Trends appear in Redis cache
```

---

## Week 7-8: Auth Service (Days 29-37)

**Goal**: Build JWT authentication service with Go  
**Total Time**: 25-30 hours  
**Repository**: `auth-service`

---

### Day 29: Auth Project Setup

**Time**: 2-3 hours  
**Repository**: `auth-service`

#### Tasks

- [ ] Navigate to auth-service repository
- [ ] Initialize Go module:
```bash
go mod init github.com/job-market-intelligence/auth-service
```
- [ ] Install dependencies:
```bash
go get github.com/gofiber/fiber/v2
go get github.com/golang-jwt/jwt/v5
go get golang.org/x/crypto/bcrypt
go get go.mongodb.org/mongo-driver/mongo
go get github.com/go-redis/redis/v8
```
- [ ] Create directory structure:
```
cmd/
└── main.go
internal/
├── handlers/
│   └── auth.go
├── middleware/
│   ├── jwt.go
│   └── ratelimit.go
├── models/
│   └── user.go
└── services/
    ├── password.go
    ├── token.go
    └── user.go
```
- [ ] Create `cmd/main.go` with Fiber server
- [ ] Test: `go run cmd/main.go`
- [ ] Commit and push

#### Deliverable

Auth project initialized with proper structure

#### Validation

`go run cmd/main.go` starts Fiber server without errors

---

### Day 30: User Model & Database

**Time**: 2 hours  
**Repository**: `auth-service`

#### Tasks

- [ ] Create `internal/models/user.go`:
  - `User` struct with BSON tags
  - Fields: ID, Email, PasswordHash, Role, CreatedAt, LastLogin
- [ ] Create MongoDB `users` collection
- [ ] Add indexes: unique on `email`
- [ ] Implement user CRUD operations
- [ ] Test user create, read, update
- [ ] Commit and push

#### Deliverable

User model with MongoDB integration

#### Validation

User CRUD operations work against MongoDB Atlas

---

### Day 31: Password Service

**Time**: 3-4 hours  
**Repository**: `auth-service`

#### Tasks

- [ ] Create `internal/services/password.go`
- [ ] Implement `HashPassword(password string)` using bcrypt cost 12
- [ ] Implement `VerifyPassword(password, hash string)`
- [ ] Implement `ValidateStrength(password string)`:
  - Min 8 characters
  - Uppercase, lowercase, number, special char
- [ ] Test password hashing: aim for <150ms per hash
- [ ] Write unit tests in `internal/services/password_test.go`
- [ ] Commit and push

#### Deliverable

Password hashing and validation service

#### Validation

Tests pass, hashing completes in <150ms

---

### Day 32: Token Service

**Time**: 3-4 hours  
**Repository**: `auth-service`

#### Tasks

- [ ] Create `internal/services/token.go`
- [ ] Implement `GenerateAccessToken()` - JWT, 15min expiry
- [ ] Implement `GenerateRefreshToken()` - JWT, 7-day expiry
- [ ] Store refresh tokens in Redis
- [ ] Implement `VerifyAccessToken()`
- [ ] Implement `VerifyRefreshToken()` with Redis check
- [ ] Implement `RevokeRefreshToken()` for logout
- [ ] Test token generation and verification
- [ ] Write unit tests in `internal/services/token_test.go`
- [ ] Commit and push

#### Deliverable

JWT token generation with Redis-backed refresh tokens

#### Validation

Tokens generate, verify, and revoke correctly

---

### Day 33: User Service

**Time**: 3-4 hours  
**Repository**: `auth-service`

#### Tasks

- [ ] Create `internal/services/user.go`
- [ ] Implement `Create(user)` - save to MongoDB
- [ ] Implement `FindByEmail(email)` - query user
- [ ] Implement `UpdateLastLogin(userID)`
- [ ] Implement `LogFailedLogin(userID)` for security
- [ ] Add proper error handling
- [ ] Write unit tests in `internal/services/user_test.go`
- [ ] Commit and push

#### Deliverable

User service with security logging

#### Validation

All user service operations tested and passing

---

### Day 34: Auth Handlers - Signup

**Time**: 4 hours  
**Repository**: `auth-service`

#### Tasks

- [ ] Create `internal/handlers/auth.go`
- [ ] Implement Signup endpoint:
  - Validate input
  - Check password strength
  - Hash password
  - Create user
  - Generate tokens
  - Return user + tokens
- [ ] Test signup flow with curl/Postman
- [ ] Add error responses for validation failures
- [ ] Test duplicate email handling
- [ ] Commit and push

#### Deliverable

Working signup endpoint

#### Validation
```bash
curl -X POST http://localhost:3001/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"SecurePass123!"}'
# Returns user + tokens
```

---

### Day 35: Auth Handlers - Login, Refresh, Logout

**Time**: 3-4 hours  
**Repository**: `auth-service`

#### Tasks

- [ ] Implement Login endpoint:
  - Find user by email
  - Verify password
  - Generate tokens
  - Update last login
  - Return user + tokens
- [ ] Implement Refresh endpoint:
  - Verify refresh token
  - Check if revoked
  - Generate new access token
- [ ] Implement Logout endpoint:
  - Revoke refresh token from Redis
- [ ] Test all endpoints with curl/Postman
- [ ] Test error cases (wrong password, expired token, etc.)
- [ ] Commit and push

#### Deliverable

Complete auth flow: login, refresh, logout

#### Validation

All auth endpoints return correct responses for success and error cases

---

### Day 36: Middleware

**Time**: 2-4 hours  
**Repository**: `auth-service`

#### Tasks

- [ ] Create `internal/middleware/jwt.go`
- [ ] Implement JWT verification middleware:
  - Extract token from Authorization header
  - Verify signature
  - Add user to context
- [ ] Create `internal/middleware/ratelimit.go`
- [ ] Implement rate limiting for login (max 5 attempts/min)
- [ ] Implement role-based middleware:
  - `requireRole([]string{"admin"})`
  - `requireRole([]string{"premium", "admin"})`
- [ ] Test middleware with protected routes
- [ ] Commit and push

#### Deliverable

JWT and rate limiting middleware

#### Validation

Protected routes reject unauthenticated requests and rate limiting triggers correctly

---

### Day 37: Docker & Testing

**Time**: 3-4 hours  
**Repository**: `auth-service`

#### Tasks

- [ ] Create `Dockerfile`:
```dockerfile
FROM golang:1.21-alpine AS builder
WORKDIR /app
COPY go.* ./
RUN go mod download
COPY . .
RUN go build -o auth cmd/main.go

FROM alpine:latest
COPY --from=builder /app/auth /auth
CMD ["/auth"]
```
- [ ] Build image:
```bash
docker build -t job-market/auth:v1 .
```
- [ ] Test all endpoints in Docker
- [ ] Write integration tests
- [ ] Load test: 10K concurrent logins
- [ ] Measure performance: aim for <50ms per login
- [ ] Document API endpoints in README
- [ ] Update `infrastructure/docs/PROGRESS.md`:
  - Mark Week 7-8 complete
  - Add time spent
  - Note performance metrics
- [ ] Commit and push

#### Deliverable

✅ Complete auth system with signup, login, JWT, logout

#### Validation
```bash
docker run job-market/auth:v1
# Auth service runs in container
# All endpoints respond correctly
```

---

## Week 9-10: API Gateway (Days 38-47)

**Goal**: Build unified GraphQL + REST API with Bun.js  
**Total Time**: 28-35 hours  
**Repository**: `api-gateway`

---

### Day 38: GraphQL Server Setup

**Time**: 3-4 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Navigate to api-gateway repository
- [ ] Initialize Bun project: `bun init -y`
- [ ] Install dependencies:
```bash
bun add graphql graphql-yoga hono dataloader ioredis mongodb jsonwebtoken
```
- [ ] Create directory structure:
```
src/
├── index.ts
├── graphql/
│   ├── schema.ts
│   ├── resolvers/
│   │   ├── query.ts
│   │   └── mutation.ts
│   └── dataloaders/
│       ├── skill-loader.ts
│       └── job-loader.ts
├── rest/
│   └── routes.ts
└── middleware/
    └── jwt-verify.ts
```
- [ ] Create `src/index.ts` with GraphQL Yoga server
- [ ] Test: Visit `http://localhost:4000/graphql`
- [ ] Commit and push

#### Deliverable

GraphQL Yoga server running with playground

#### Validation

GraphQL Playground accessible at `http://localhost:4000/graphql`

---

### Day 39: GraphQL Schema

**Time**: 3-4 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Create `src/graphql/schema.ts`
- [ ] Define GraphQL types:
  - `type Job`
  - `type Skill`
  - `type User`
  - `type SalaryStats`
  - `type SkillTrend`
- [ ] Define Query type:
  - `trendingSkills(timeframe, limit, category)`
  - `jobs(filters, limit, offset)`
  - `skill(id, name)`
  - `salaryStats(role, location, skills)`
  - `marketInsights(timeframe)`
- [ ] Define Mutation type:
  - `trackSkill(skillId)`
  - `reportSkillError(jobId, incorrectSkill)`
- [ ] Define Subscription type:
  - `skillTrendsUpdated`
- [ ] Test schema in GraphQL Playground
- [ ] Commit and push

#### Deliverable

Complete GraphQL schema definition

#### Validation

Schema loads without errors in GraphQL Playground

---

### Day 40: DataLoaders

**Time**: 2 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Create `src/graphql/dataloaders/skill-loader.ts`
- [ ] Implement DataLoader for skills (prevent N+1 queries)
- [ ] Create `src/graphql/dataloaders/job-loader.ts`
- [ ] Implement DataLoader for jobs
- [ ] Test DataLoader batching
- [ ] Verify query count reduction
- [ ] Commit and push

#### Deliverable

DataLoaders preventing N+1 query problems

#### Validation

DataLoader batches multiple individual lookups into single queries

---

### Day 41: Query Resolvers - Trending Skills

**Time**: 4 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Create `src/graphql/resolvers/query.ts`
- [ ] Implement `trendingSkills` resolver:
  - Check Redis cache first
  - Query MongoDB if cache miss
  - Use DataLoader for skill details
  - Cache result for 15 minutes
- [ ] Implement `skill` resolver
- [ ] Test queries in Playground:
```graphql
query {
  trendingSkills(limit: 10) {
    canonicalName
    category
    mentionsCount
  }
}
```
- [ ] Commit and push

#### Deliverable

Trending skills resolver with caching

#### Validation

Trending skills query returns data with cache hit on second request

---

### Day 42: Query Resolvers - Jobs & Salary

**Time**: 3-4 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Implement `jobs` resolver:
  - Build MongoDB query from filters
  - Support pagination (limit, offset)
  - Return `JobsConnection` with total count
- [ ] Implement `salaryStats` resolver:
  - MongoDB aggregation for statistics
  - Calculate percentiles
  - Cache results
- [ ] Test complex queries with filters
- [ ] Verify pagination works correctly
- [ ] Commit and push

#### Deliverable

Jobs search and salary statistics resolvers

#### Validation

Jobs query supports filtering, pagination, and returns correct totals

---

### Day 43: Market Insights & Mutations

**Time**: 2-4 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Implement `marketInsights` resolver:
  - Aggregate data from multiple sources
  - Calculate metrics (total jobs, avg salary, etc.)
  - Cache for 1 hour
- [ ] Create `src/graphql/resolvers/mutation.ts`
- [ ] Implement `trackSkill` mutation
- [ ] Implement `reportSkillError` mutation
- [ ] Test all resolvers end-to-end
- [ ] Commit and push

#### Deliverable

All GraphQL resolvers implemented

#### Validation

All queries and mutations return expected data

---

### Day 44: REST Endpoints

**Time**: 3-4 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Add Hono for REST endpoints
- [ ] Create `src/rest/routes.ts`
- [ ] Implement REST endpoints:
  - `GET /api/health` - health check
  - `GET /api/skills/trending` - simple trending list
  - `GET /api/jobs` - job search
  - `GET /api/export/skills.csv` - CSV export
  - `POST /api/webhooks/job-alert` - webhook endpoint
- [ ] Test all REST endpoints with curl
- [ ] Commit and push

#### Deliverable

REST API alongside GraphQL

#### Validation
```bash
curl http://localhost:4000/api/health
# Returns { "status": "ok" }

curl http://localhost:4000/api/skills/trending
# Returns trending skills array
```

---

### Day 45: JWT Authentication Integration

**Time**: 3 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Create `src/middleware/jwt-verify.ts`
- [ ] Implement JWT verification:
  - Extract token from Authorization header
  - Verify signature
  - Add user to context
- [ ] Apply to protected GraphQL resolvers
- [ ] Apply to protected REST endpoints
- [ ] Test authenticated requests
- [ ] Test rejection of invalid tokens
- [ ] Commit and push

#### Deliverable

JWT authentication protecting API endpoints

#### Validation

Protected endpoints reject unauthenticated requests and accept valid tokens

---

### Day 46: Authorization & Security

**Time**: 2-3 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Implement role-based middleware:
  - `requireRole(['admin'])`
  - `requireRole(['premium', 'admin'])`
- [ ] Protect admin-only endpoints
- [ ] Add rate limiting middleware
- [ ] Implement CORS configuration
- [ ] Test with different user roles
- [ ] Test rate limiting triggers
- [ ] Commit and push

#### Deliverable

Role-based access control and security middleware

#### Validation

Admin endpoints reject non-admin users, rate limiting works correctly

---

### Day 47: WebSocket & Docker

**Time**: 3-4 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Implement WebSocket subscriptions
- [ ] Create real-time skill trends subscription:
  - Subscribe to `skillTrendsUpdated`
  - Push updates every 15 minutes
- [ ] Test WebSocket connection from browser
- [ ] Create `Dockerfile`:
```dockerfile
FROM oven/bun:1 AS base
WORKDIR /app

COPY package.json bun.lockb ./
RUN bun install --frozen-lockfile --production

COPY . .

CMD ["bun", "run", "src/index.ts"]
```
- [ ] Build image:
```bash
docker build -t job-market/api-gateway:v1 .
```
- [ ] Test in Docker
- [ ] Generate TypeScript types for frontend:
```bash
graphql-codegen --config codegen.yml
```
- [ ] Document API in README
- [ ] Update `infrastructure/docs/PROGRESS.md`:
  - Mark Week 9-10 complete
  - Add time spent
- [ ] Commit and push

#### Deliverable

✅ GraphQL + REST API with auth, real-time subscriptions

#### Validation
```bash
docker run job-market/api-gateway:v1
# API Gateway runs in container
# GraphQL Playground, REST endpoints, and WebSocket all functional
```

---

## Week 11-12: Frontend (Days 48-60)

**Goal**: Build interactive React dashboard  
**Total Time**: 25-30 hours  
**Repository**: `frontend`

---

### Day 48: Frontend Project Setup

**Time**: 3 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Navigate to frontend repository
- [ ] Create Vite project:
```bash
npm create vite@latest . -- --template react-ts
```
- [ ] Install dependencies:
```bash
npm install @apollo/client graphql recharts tailwindcss
npm install react-router-dom @headlessui/react lucide-react
```
- [ ] Set up TailwindCSS:
```bash
npx tailwindcss init -p
```
- [ ] Configure `tailwind.config.js`
- [ ] Test dev server: `npm run dev`
- [ ] Commit and push

#### Deliverable

React + TypeScript project with Vite and Tailwind

#### Validation

Dev server runs at `http://localhost:5173` without errors

---

### Day 49: Apollo Client Setup

**Time**: 2-3 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Create `src/apollo-client.ts` - Apollo Client setup
- [ ] Add auth link (add JWT to headers)
- [ ] Add error link (handle token expiry)
- [ ] Implement auto token refresh
- [ ] Create `src/utils/auth.ts` - AuthManager class
- [ ] Test Apollo Client connection to API Gateway
- [ ] Commit and push

#### Deliverable

Apollo Client configured with authentication

#### Validation

Apollo Client connects to GraphQL API and handles auth headers

---

### Day 50: Login Page

**Time**: 3-4 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Create `src/pages/Login.tsx`
- [ ] Build login form with validation
- [ ] Integrate with Auth Service API
- [ ] Handle login errors
- [ ] Store JWT on successful login
- [ ] Redirect to dashboard
- [ ] Style with Tailwind
- [ ] Commit and push

#### Deliverable

Working login page

#### Validation

User can log in and is redirected to dashboard

---

### Day 51: Signup Page

**Time**: 3 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Create `src/pages/Signup.tsx`
- [ ] Build signup form
- [ ] Add password strength indicator
- [ ] Validate email format
- [ ] Handle signup errors
- [ ] Auto-login after signup
- [ ] Test signup flow
- [ ] Commit and push

#### Deliverable

Working signup page with password strength indicator

#### Validation

User can sign up and is automatically logged in

---

### Day 52: Auth Context & Routing

**Time**: 2-3 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Create `src/contexts/AuthContext.tsx`
- [ ] Implement auth state management:
  - `user`, `isAuthenticated`, `login()`, `logout()`
- [ ] Create `ProtectedRoute` component
- [ ] Set up React Router with protected routes
- [ ] Test authentication flow end-to-end
- [ ] Commit and push

#### Deliverable

Auth context with protected routing

#### Validation

Unauthenticated users are redirected to login, authenticated users access dashboard

---

### Day 53: Skills Trend Chart

**Time**: 4 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Create `src/components/SkillsTrendChart.tsx`
- [ ] Use Recharts LineChart
- [ ] Fetch data with GraphQL query:
```graphql
query {
  trendingSkills(limit: 10) {
    canonicalName
    trendData(days: 30) {
      date
      mentionsCount
    }
  }
}
```
- [ ] Display interactive chart with hover tooltips
- [ ] Add loading state with skeleton
- [ ] Style component with Tailwind
- [ ] Commit and push

#### Deliverable

Interactive skills trend line chart

#### Validation

Chart displays trending skills data with interactive tooltips

---

### Day 54: Salary Heat Map

**Time**: 4 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Create `src/components/SalaryHeatMap.tsx`
- [ ] Fetch salary data by location
- [ ] Use Recharts or custom SVG
- [ ] Show salary ranges by city
- [ ] Add tooltips with salary details
- [ ] Make responsive
- [ ] Commit and push

#### Deliverable

Salary heat map visualization

#### Validation

Heat map displays salary data by location with interactive tooltips

---

### Day 55: Jobs Table

**Time**: 2-4 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Create `src/components/JobsTable.tsx`
- [ ] Fetch jobs with filters
- [ ] Add pagination
- [ ] Add sorting (by date, salary)
- [ ] Add search by skill
- [ ] Show job details modal
- [ ] Make table responsive
- [ ] Commit and push

#### Deliverable

Interactive jobs table with filtering and pagination

#### Validation

Table displays jobs with working filters, sorting, and pagination

---

### Day 56: Skills Gap Analyzer

**Time**: 2-4 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Create `src/components/SkillsGapAnalyzer.tsx`
- [ ] Let user select their skills
- [ ] Compare with trending skills
- [ ] Show missing skills
- [ ] Suggest learning path
- [ ] Visualize gap
- [ ] Commit and push

#### Deliverable

Skills gap analysis tool

#### Validation

User can input skills and see gap analysis against market demand

---

### Day 57: Dashboard Page

**Time**: 3-4 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Create `src/pages/Dashboard.tsx`
- [ ] Layout: sidebar + main content
- [ ] Add SkillsTrendChart
- [ ] Add quick stats cards (total jobs, top skill, avg salary)
- [ ] Add recent jobs list
- [ ] Make responsive
- [ ] Commit and push

#### Deliverable

Main dashboard page with all components

#### Validation

Dashboard displays all components in responsive layout

---

### Day 58: Skills Explorer Page

**Time**: 3 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Create `src/pages/SkillsExplorer.tsx`
- [ ] Show all skills with filters
- [ ] Show skill details on click
- [ ] Display skill trend over time
- [ ] Show related skills
- [ ] Show top companies using skill
- [ ] Commit and push

#### Deliverable

Skills explorer page with detailed skill views

#### Validation

Skills page shows searchable list with detailed drill-down

---

### Day 59: Real-time Updates

**Time**: 2-3 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Implement WebSocket connection
- [ ] Subscribe to skill trends updates
- [ ] Auto-update charts when new data arrives
- [ ] Show notification on update
- [ ] Test real-time functionality
- [ ] Commit and push

#### Deliverable

Real-time data updates via WebSocket

#### Validation

Charts update automatically when new trend data is published

---

### Day 60: Polish & Testing

**Time**: 3-4 hours  
**Repository**: `frontend`

#### Tasks

- [ ] Add loading skeletons
- [ ] Add error boundaries
- [ ] Add 404 page
- [ ] Improve mobile responsiveness
- [ ] Add dark mode toggle (optional)
- [ ] Test on Chrome, Firefox, Safari
- [ ] Create `Dockerfile`:
```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
CMD ["nginx", "-g", "daemon off;"]
```
- [ ] Build for production: `npm run build`
- [ ] Test production build
- [ ] Update `infrastructure/docs/PROGRESS.md`:
  - Mark Week 11-12 complete
  - Add time spent
- [ ] Commit and push

#### Deliverable

✅ Full-featured dashboard with auth, charts, real-time updates

#### Validation
```bash
npm run build
# Build completes successfully
# Production build serves correctly
```

---

## Week 13: Kubernetes Deployment (Days 61-67)

**Goal**: Deploy all services to Kubernetes  
**Total Time**: 15-20 hours  
**Repository**: `infrastructure`

---

### Day 61: Namespaces, ConfigMaps & Secrets

**Time**: 3-4 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Create namespace manifests:
```yaml
# kubernetes/namespaces/job-market.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: job-market
```
- [ ] Create ConfigMaps for each service
- [ ] Create Secrets:
  - JWT secrets
  - MongoDB URI
  - API keys
- [ ] Apply: `kubectl apply -f kubernetes/namespaces/`
- [ ] Verify namespace created
- [ ] Commit and push

#### Deliverable

Kubernetes namespace with ConfigMaps and Secrets

#### Validation
```bash
kubectl get namespace job-market
kubectl get configmaps -n job-market
kubectl get secrets -n job-market
```

---

### Day 62: Deployment Manifests

**Time**: 3-4 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Create Deployment manifests for all 5 services:
  - `kubernetes/deployments/scraper-deployment.yaml` (replicas: 2)
  - `kubernetes/deployments/nlp-deployment.yaml` (replicas: 3)
  - `kubernetes/deployments/aggregation-deployment.yaml` (replicas: 2)
  - `kubernetes/deployments/auth-deployment.yaml` (replicas: 3)
  - `kubernetes/deployments/api-gateway-deployment.yaml` (replicas: 3)
- [ ] Configure resource limits (CPU, memory)
- [ ] Add health check probes (liveness, readiness)
- [ ] Add environment variables from ConfigMap/Secret
- [ ] Commit and push

#### Deliverable

Deployment manifests for all microservices

#### Validation

Manifests pass `kubectl apply --dry-run=client` validation

---

### Day 63: Service & Ingress Manifests

**Time**: 2 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Create Service manifests:
  - ClusterIP for internal services
  - NodePort for API Gateway
- [ ] Create Ingress (optional, if using)
- [ ] Apply all manifests
- [ ] Test: `kubectl get pods -n job-market`
- [ ] Verify service discovery
- [ ] Commit and push

#### Deliverable

Kubernetes Services exposing all microservices

#### Validation
```bash
kubectl get services -n job-market
kubectl get pods -n job-market
```

---

### Day 64: Kafka & Zookeeper StatefulSets

**Time**: 3-4 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Create Kafka StatefulSet:
```yaml
# kubernetes/statefulsets/kafka-statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: kafka
  namespace: job-market
```
- [ ] Create Zookeeper StatefulSet
- [ ] Configure persistent volumes for data
- [ ] Apply StatefulSets
- [ ] Test: `kubectl logs kafka-0 -n job-market`
- [ ] Verify Kafka is accepting connections
- [ ] Commit and push

#### Deliverable

Kafka and Zookeeper running as StatefulSets

#### Validation
```bash
kubectl get statefulsets -n job-market
kubectl logs kafka-0 -n job-market
```

---

### Day 65: Redis & Elasticsearch StatefulSets

**Time**: 3 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Deploy Redis as StatefulSet
- [ ] Configure Redis persistence
- [ ] Deploy Elasticsearch StatefulSet
- [ ] Deploy Kibana Deployment
- [ ] Allocate appropriate storage (PV/PVC)
- [ ] Verify all pods running
- [ ] Commit and push

#### Deliverable

Redis, Elasticsearch, and Kibana running in Kubernetes

#### Validation
```bash
kubectl get statefulsets -n job-market
kubectl get pods -n job-market
# All infrastructure pods should be Running
```

---

### Day 66: Service Communication Testing

**Time**: 2-3 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Test all pod-to-pod communication:
  - Scraper → Kafka
  - NLP → Kafka → MongoDB
  - Auth → MongoDB, Redis
  - API Gateway → MongoDB, Redis
  - Aggregation → MongoDB, Redis
- [ ] Verify DNS resolution between services
- [ ] Test external access to API Gateway
- [ ] Document any networking issues and fixes
- [ ] Commit and push

#### Deliverable

Verified inter-service communication in Kubernetes

#### Validation

All services can communicate with their dependencies

---

### Day 67: Build & Deploy All Services

**Time**: 3-4 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Build all Docker images:
```bash
docker build -t job-market/scraper:v1 ../scraper-service
docker build -t job-market/nlp:v1 ../nlp-service
docker build -t job-market/aggregation:v1 ../aggregation-service
docker build -t job-market/auth:v1 ../auth-service
docker build -t job-market/api-gateway:v1 ../api-gateway
docker build -t job-market/frontend:v1 ../frontend
```
- [ ] Tag images for local registry (or push to Docker Hub)
- [ ] Deploy all services:
```bash
kubectl apply -f kubernetes/
```
- [ ] Monitor rollout:
```bash
kubectl rollout status deployment/scraper -n job-market
```
- [ ] Verify all pods running:
```bash
kubectl get pods -n job-market
```
- [ ] Check logs for errors:
```bash
kubectl logs -f deployment/nlp -n job-market
```
- [ ] Update `infrastructure/docs/PROGRESS.md`:
  - Mark Week 13 complete
  - Add time spent
- [ ] Commit and push

#### Deliverable

✅ All services running in Kubernetes

#### Validation
```bash
kubectl get pods -n job-market
# All pods should show Running status
```

---

## Week 14: Observability (Days 68-73)

**Goal**: Set up ELK stack for logging, dashboards, and alerts  
**Total Time**: 13-16 hours  
**Repository**: `infrastructure`

---

### Day 68: Elasticsearch Installation

**Time**: 3-4 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Add Elastic Helm repository:
```bash
helm repo add elastic https://helm.elastic.co
helm repo update
```
- [ ] Install Elasticsearch:
```bash
helm install elasticsearch elastic/elasticsearch \
  --namespace monitoring --create-namespace \
  --set replicas=1 \
  --set resources.requests.memory=2Gi
```
- [ ] Wait for pods:
```bash
kubectl get pods -n monitoring -w
```
- [ ] Port-forward to test:
```bash
kubectl port-forward svc/elasticsearch 9200:9200 -n monitoring
```
- [ ] Verify Elasticsearch is healthy
- [ ] Commit and push

#### Deliverable

Elasticsearch running in Kubernetes monitoring namespace

#### Validation
```bash
curl http://localhost:9200/_cluster/health
# Should return green or yellow status
```

---

### Day 69: Kibana Installation

**Time**: 3 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Install Kibana:
```bash
helm install kibana elastic/kibana \
  --namespace monitoring \
  --set elasticsearchHosts=http://elasticsearch:9200
```
- [ ] Access Kibana:
```bash
kubectl port-forward svc/kibana 5601:5601 -n monitoring
```
- [ ] Open `http://localhost:5601`
- [ ] Create index pattern: `logs-*`
- [ ] Verify Kibana connects to Elasticsearch
- [ ] Commit and push

#### Deliverable

Kibana running and connected to Elasticsearch

#### Validation

Kibana UI accessible at `http://localhost:5601` with index pattern created

---

### Day 70: Structured Logging

**Time**: 2-3 hours  
**Repository**: All service repositories

#### Tasks

- [ ] Configure structured logging in all services:
  - JSON format
  - Include: timestamp, level, service, message, context
  - Example:
```json
{
  "timestamp": "2026-02-10T10:30:00Z",
  "level": "info",
  "service": "nlp-service",
  "message": "Skills extracted",
  "job_id": "linkedin_123",
  "skills_count": 8
}
```
- [ ] Verify logs from scraper-service appear in Elasticsearch
- [ ] Verify logs from nlp-service appear in Elasticsearch
- [ ] Verify logs from aggregation-service appear in Elasticsearch
- [ ] Verify logs from auth-service appear in Elasticsearch
- [ ] Verify logs from api-gateway appear in Elasticsearch
- [ ] Commit and push changes to each service repository

#### Deliverable

All services producing structured JSON logs visible in Elasticsearch

#### Validation

Logs from all 5 services appear in Kibana discover view

---

### Day 71: Kibana Dashboards

**Time**: 3 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Create Kibana dashboards:
  - **System Health Dashboard**:
    - Total requests by service
    - Error rate by service
    - Average response time
    - Pod restart count
  - **Auth Dashboard**:
    - Login success/failure rate
    - Failed login attempts by user
    - Token generation rate
    - Active sessions
  - **Job Processing Dashboard**:
    - Jobs scraped per hour
    - NLP processing rate
    - Skill extraction accuracy
    - Trending skill changes
- [ ] Save all dashboards
- [ ] Document dashboard descriptions
- [ ] Commit and push

#### Deliverable

Three Kibana dashboards for monitoring

#### Validation

All dashboards load and display real-time data

---

### Day 72: Alerts

**Time**: 2-3 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Create alerts in Kibana:
  - Alert on error rate > 5%
  - Alert on failed logins > 10/min (brute force detection)
  - Alert on pod crash loops
  - Alert on Kafka lag > 1000 messages
- [ ] Test alerts by triggering conditions
- [ ] Configure notification channel (email/Slack)
- [ ] Document alert rules and thresholds
- [ ] Commit and push

#### Deliverable

Alerting system for critical conditions

#### Validation

Alerts trigger correctly when thresholds are exceeded

---

### Day 73: Metrics & Monitoring

**Time**: 3-4 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Add Prometheus metrics to Go services:
  - Request count
  - Request duration
  - Active connections
- [ ] Install Prometheus (optional):
```bash
helm install prometheus prometheus-community/prometheus \
  --namespace monitoring
```
- [ ] Create Grafana dashboards (optional)
- [ ] Test metrics scraping
- [ ] Document observability setup in `docs/observability.md`
- [ ] Update `infrastructure/docs/PROGRESS.md`:
  - Mark Week 14 complete
  - Add time spent
- [ ] Commit and push

#### Deliverable

✅ Full observability with logs, dashboards, alerts

#### Validation

Metrics flowing into Prometheus, dashboards showing real-time data

---

## Week 15: Production Ready (Days 74-90)

**Goal**: Testing, documentation, polish, and portfolio preparation  
**Total Time**: 26-32 hours  
**Repository**: All repositories

---

### Day 74: Unit Tests

**Time**: 3-4 hours  
**Repository**: All service repositories

#### Tasks

- [ ] Write unit tests for each service:
  - **scraper-service**: Test scraping logic, rate limiting
  - **nlp-service**: Test skill extraction (target: 85% accuracy)
  - **aggregation-service**: Test trend calculations
  - **auth-service**: Test password hashing, JWT generation
  - **api-gateway**: Test resolvers, middleware
- [ ] Run tests: aim for >80% code coverage
- [ ] Fix failing tests
- [ ] Commit and push to each repository

#### Deliverable

Unit tests with >80% code coverage across all services

#### Validation

All tests pass in every service repository

---

### Day 75: Integration Tests

**Time**: 3 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Write integration tests:
  - Test scraper → Kafka → NLP → MongoDB flow
  - Test auth signup → login → protected request
  - Test GraphQL queries end-to-end
- [ ] Test error scenarios:
  - Database connection failure
  - Kafka unavailable
  - Invalid JWT token
- [ ] Document test results
- [ ] Commit and push

#### Deliverable

Integration tests covering cross-service flows

#### Validation

All integration tests pass with services running

---

### Day 76: Load Testing & Benchmarks

**Time**: 2-3 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Load testing with Apache Bench or k6:
  - Test 1000 concurrent API requests
  - Test 100 concurrent logins
  - Measure response times
  - Check for memory leaks
- [ ] Performance benchmarks:
  - Scraper: 500+ jobs/hour
  - NLP: 50 jobs/sec
  - Aggregation: 10K jobs in <500ms
  - Auth: <50ms per login
  - API: <50ms (uncached), <10ms (cached)
- [ ] Document results in `docs/performance-benchmarks.md`
- [ ] Commit and push

#### Deliverable

Performance benchmarks documented

#### Validation

All performance targets met or documented with explanations

---

### Day 77: Main Documentation

**Time**: 4 hours  
**Repository**: `.github` and `infrastructure`

#### Tasks

- [ ] Write comprehensive README.md for organization:
  - Project overview and goals
  - Architecture diagram
  - Tech stack explanation
  - Quick start guide
  - Installation instructions
  - Usage examples
  - Contributing guidelines
- [ ] Create architecture diagrams:
  - System overview (all services)
  - Data flow diagram
  - Security architecture
  - Deployment topology
- [ ] Use draw.io or Mermaid for diagrams
- [ ] Commit and push

#### Deliverable

Professional project documentation with architecture diagrams

#### Validation

README provides clear path from clone to running system

---

### Day 78: Service Documentation

**Time**: 2-3 hours  
**Repository**: All service repositories

#### Tasks

- [ ] Document each service README:
  - `scraper-service/README.md`
  - `nlp-service/README.md`
  - `aggregation-service/README.md`
  - `auth-service/README.md`
  - `api-gateway/README.md`
- [ ] Include for each: Purpose, Tech stack, API, Environment variables, Testing
- [ ] Commit and push to each repository

#### Deliverable

Complete documentation for all 5 services

#### Validation

Each service README is self-contained and comprehensive

---

### Day 79: API Documentation

**Time**: 2-3 hours  
**Repository**: `api-gateway`

#### Tasks

- [ ] Write API documentation:
  - GraphQL schema with descriptions
  - REST endpoints with examples
  - Authentication flow
  - Error codes and messages
  - Rate limiting rules
- [ ] Create `docs/api-documentation.md`
- [ ] Generate GraphQL schema docs automatically
- [ ] Commit and push

#### Deliverable

Complete API documentation

#### Validation

API documentation covers all endpoints with examples

---

### Day 80: Terraform Configuration

**Time**: 3-4 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Write Terraform configurations:
  - `terraform/main.tf`
  - Define AWS resources (optional for portfolio):
    - EKS cluster
    - VPC and subnets
    - S3 buckets
    - IAM roles
  - Or keep LocalStack configs for local demo
- [ ] Test: `terraform plan`
- [ ] Document Terraform setup
- [ ] Commit and push

#### Deliverable

Infrastructure as Code with Terraform

#### Validation
```bash
terraform plan
# Plan executes without errors
```

---

### Day 81: Helm Charts

**Time**: 3 hours  
**Repository**: `infrastructure`

#### Tasks

- [ ] Create Helm charts:
  - `helm/job-market-platform/Chart.yaml`
  - `helm/job-market-platform/values.yaml` (configurable replicas, resources)
  - `helm/job-market-platform/templates/` (Deployments, Services, ConfigMaps)
- [ ] Test Helm installation:
```bash
helm install job-market ./helm/job-market-platform
```
- [ ] Document Helm usage
- [ ] Commit and push

#### Deliverable

Helm chart for one-command deployment

#### Validation
```bash
helm install job-market ./helm/job-market-platform
# All services deploy successfully
```

---

### Day 82: CI/CD Pipeline

**Time**: 2-3 hours  
**Repository**: `.github`

#### Tasks

- [ ] Set up CI/CD with GitHub Actions:
  - `.github/workflows/ci.yml`
  - Trigger on push to main
  - Jobs:
    - Lint all code
    - Run unit tests
    - Build Docker images
    - Push to registry
    - (Optional) Deploy to staging
- [ ] Test workflow
- [ ] Add status badges to README
- [ ] Commit and push

#### Deliverable

Automated CI/CD pipeline

#### Validation

GitHub Actions workflow runs successfully on push

---

### Day 83: Demo Video

**Time**: 3-4 hours  
**Repository**: `.github`

#### Tasks

- [ ] Record demo video (10-15 minutes):
  - Introduction to project
  - Architecture walkthrough
  - Live demo:
    - User signup/login
    - Dashboard with charts
    - Real-time updates
    - Job search
  - Show Kubernetes deployment
  - Show logs in Kibana
  - Explain technology choices
- [ ] Edit video
- [ ] Upload to YouTube
- [ ] Add link to organization README
- [ ] Commit and push

#### Deliverable

Professional demo video on YouTube

#### Validation

Video is polished and demonstrates all key features

---

### Day 84: Blog Post

**Time**: 3 hours  

#### Tasks

- [ ] Write blog post: "Building a Polyglot Microservices Platform"
  - Why I chose each language
  - Challenges faced (e.g., NLP accuracy, rate limiting)
  - Performance optimizations
  - What I learned about Go, Python, Bun.js
  - Security considerations
  - Lessons learned
- [ ] Publish on Medium/Dev.to
- [ ] Share on LinkedIn

#### Deliverable

Published blog post about the project

#### Validation

Blog post is live and shared on social media

---

### Day 85: Interview Presentation

**Time**: 2-3 hours  

#### Tasks

- [ ] Create presentation slides (for interviews):
  - Problem statement
  - Architecture overview
  - Technology choices (with rationale)
  - Key features
  - Performance metrics
  - Challenges solved
  - Future improvements
- [ ] Practice presentation (aim for 10 minutes)
- [ ] Prepare for Q&A

#### Deliverable

10-minute interview presentation ready

#### Validation

Presentation is polished and well-rehearsed

---

### Day 86-87: End-to-End Testing

**Time**: 6-8 hours  
**Repository**: All repositories

#### Tasks

- [ ] End-to-end testing:
  - Fresh deployment from scratch
  - Test every user flow
  - Test all API endpoints
  - Verify all charts and visualizations
  - Test on mobile devices
- [ ] Fix any bugs found
- [ ] Performance tuning:
  - Optimize slow queries
  - Add missing indexes
  - Tune cache TTLs
  - Optimize Docker images
- [ ] Commit and push fixes to affected repositories

#### Deliverable

Bug-free system verified through end-to-end testing

#### Validation

All user flows complete successfully from fresh deployment

---

### Day 88: Security Review

**Time**: 2-3 hours  
**Repository**: All repositories

#### Tasks

- [ ] Security review:
  - Check for exposed secrets
  - Review JWT implementation
  - Test rate limiting
  - Verify HTTPS enforcement (if deployed)
  - Check CORS configuration
  - Review password strength requirements
- [ ] Fix any security issues
- [ ] Commit and push fixes

#### Deliverable

Security audit complete with all issues resolved

#### Validation

No exposed secrets, all security measures verified

---

### Day 89: Code Cleanup

**Time**: 2 hours  
**Repository**: All repositories

#### Tasks

- [ ] Code cleanup across all repositories:
  - Remove commented code
  - Fix linting errors
  - Ensure consistent formatting
  - Update dependencies to latest versions
  - Remove unused imports
- [ ] Final commit: `git commit -m "chore: production ready v1.0"`
- [ ] Create Git tag: `git tag v1.0.0` in each repository
- [ ] Push all tags

#### Deliverable

Clean, production-ready codebase

#### Validation

No linting errors, consistent formatting across all repositories

---

### Day 90: Final Review & Launch

**Time**: 2 hours  
**Repository**: All repositories

#### Tasks

- [ ] Final documentation review
- [ ] Update README with final metrics
- [ ] Add screenshots to README
- [ ] Create `CHANGELOG.md` in each repository
- [ ] Prepare portfolio showcase:
  - GitHub README looks professional
  - Video is polished
  - Blog post is published
  - Presentation is ready
- [ ] 🎉 PROJECT COMPLETE!

#### Deliverable

✅ Production-ready Job Market Intelligence Platform

#### Validation
```bash
make deploy
# Entire system deploys successfully
# Dashboard accessible with real data
# All features functional
```

---

## 📊 Final Deliverables

After 90 days, you will have:

### ✅ Working Platform

- 5 microservices in production
- Processing 10,000+ jobs daily
- 500+ skills tracked
- Real-time analytics dashboard
- Complete authentication system

### ✅ Technical Skills Demonstrated

- **Languages**: TypeScript, Python, Go (3 languages)
- **Runtimes**: Bun.js, Node.js alternatives
- **Databases**: MongoDB, Redis, Elasticsearch
- **Messaging**: Apache Kafka
- **APIs**: GraphQL, REST, WebSocket
- **Security**: JWT, bcrypt, RBAC, rate limiting
- **DevOps**: Docker, Kubernetes, Helm, Terraform
- **Observability**: ELK stack, Prometheus, Grafana
- **ML/NLP**: spaCy, scikit-learn, transformers
- **Frontend**: React, TypeScript, Apollo Client

### ✅ Portfolio Materials

- Professional GitHub organization
- Comprehensive documentation
- 10-minute demo video
- Published blog post
- Interview presentation
- Live deployment (optional)

### ✅ Interview Talking Points

- "Built polyglot microservices platform using 3 languages strategically"
- "Chose Go for auth because it's 7x faster at password hashing than Node.js"
- "Implemented NLP pipeline with 85%+ accuracy using spaCy"
- "Designed GraphQL API that reduced frontend API calls by 10x"
- "Achieved <50ms API response times with Redis caching"
- "Deployed to Kubernetes with full observability via ELK stack"

---

## 📈 Time Breakdown

| Phase | Duration | Hours | Percentage |
|-------|----------|-------|------------|
| Foundation | Week 1 | 15-20h | 10% |
| Scraper (Bun) | Week 2-3 | 20-25h | 15% |
| NLP (Python) | Week 4-5 | 25-30h | 18% |
| Aggregation (Go) | Week 6 | 15-18h | 11% |
| Auth (Go) | Week 7-8 | 25-30h | 18% |
| API Gateway (Bun) | Week 9-10 | 28-35h | 21% |
| Frontend (React) | Week 11-12 | 25-30h | 18% |
| Kubernetes | Week 13 | 15-20h | 12% |
| Observability | Week 14 | 13-16h | 10% |
| Testing & Docs | Week 15 | 26-32h | 20% |
| **TOTAL** | **90 days** | **207-256h** | **100%** |

Average: 2.3-2.8 hours per day or 16-19 hours per week

---

## 🎯 Success Criteria

By Day 90, you should be able to:

- [ ] Run entire system with one command: `make deploy`
- [ ] Show working dashboard with real data
- [ ] Demonstrate user signup, login, protected routes
- [ ] Show trending skills chart updating in real-time
- [ ] Query GraphQL API from Playground
- [ ] Show Kubernetes pods all running
- [ ] Show logs aggregated in Kibana

---

## Summary

**Total Duration**: 90 days (13 weeks)  
**Total Time**: 130-195 hours  
**Work Schedule**: 10-15 hours/week  

**Repositories**: 9  
**Services**: 5  
**Languages**: TypeScript, Python, Go  
**Infrastructure**: Kafka, MongoDB, Redis, Elasticsearch, Kubernetes  

**Completion**: Production-ready job market intelligence platform ✅

---

**Last Updated**: Day 1 - 06/02/2026(sixth of february)
