# 🎯 Job Market Intelligence Platform

> Enterprise-grade, real-time job market analytics with ML-powered skill extraction

[![Services](https://img.shields.io/badge/microservices-5-blue)](https://github.com/orgs/job-market-intelligence/repositories)
[![Languages](https://img.shields.io/badge/languages-3-green)](https://github.com/orgs/job-market-intelligence/repositories)
[![License](https://img.shields.io/badge/license-MIT-orange)](LICENSE)
[![Status](https://img.shields.io/badge/status-in%20development-yellow)](https://github.com/orgs/job-market-intelligence/repositories)

## 🌟 What I'm Building

A production-ready platform that aggregates job postings from multiple sources, uses ML/NLP to extract skills with high accuracy, and provides real-time market intelligence through a GraphQL API.

### Key Features

- 📊 **10,000+ jobs** processed daily
- 🤖 **500+ skills** tracked with ML extraction
- ⚡ **Real-time trends** updated every 15 minutes
- 🔒 **Enterprise security** with JWT authentication
- 📈 **Interactive dashboard** with WebSocket updates
- 🚀 **High performance** - <50ms API responses

## 🏗️ Architecture

**Polyglot Microservices** - The right language for each job:

| Service | Language | Purpose | Status |
|---------|----------|---------|--------|
| [scraper-service](https://github.com/OferGM-job-market-intelligence/scraper-service) | Typescript | Web scraping (I/O bound) | Week 2-3 |
| [nlp-service](https://github.com/OferGM-job-market-intelligence/nlp-service) | Python | ML skill extraction | Week 4-5 |
| [aggregation-service](https://github.com/OferGM-job-market-intelligence/aggregation-service) | Go | Trend calculation (CPU bound) | Week 6 |
| [auth-service](https://github.com/OferGM-job-market-intelligence/auth-service) | Go | JWT auth (security critical) | Week 7-8 |
| [api-gateway](https://github.com/OferGM-job-market-intelligence/api-gateway) | Typescript | GraphQL + REST API | Week 9-10 |

**Supporting**:
- [frontend](https://github.com/OferGM-job-market-intelligence/frontend) - React dashboard
- [infrastructure](https://github.com/OferGM-job-market-intelligence/infrastructure) - Docker, K8s, Terraform
- [shared](https://github.com/OferGM-job-market-intelligence/shared) - Types & utilities

## 🛠️ Tech Stack

**Languages**: TypeScript • Python • Go  
**Infrastructure**: Kafka • MongoDB • Redis • Elasticsearch • Kubernetes  
**APIs**: GraphQL • REST • WebSocket  
**Security**: JWT • bcrypt • RBAC • Rate Limiting  
**ML/NLP**: spaCy • scikit-learn • NLTK  

## 📊 System Architecture
```
┌─────────────┐
│   Frontend  │ React + TypeScript
└──────┬──────┘
       │ HTTPS + JWT
┌──────▼──────────────────────┐
│     API Gateway (Bun.js)    │ GraphQL + REST + WebSocket
└──────┬──────────────────────┘
       │
  ┌────┴─────┬─────────────┐
  │          │             │
┌─▼──┐  ┌───▼───┐    ┌────▼────┐
│Auth│  │Aggr.  │    │  Redis  │
│(Go)│  │ (Go)  │    │  Cache  │
└─┬──┘  └───┬───┘    └─────────┘
  │         │
┌─▼─────────▼──────────┐
│   MongoDB Atlas      │
└──────────────────────┘
         ▲
    ┌────┴────┐
┌───▼───┐ ┌──▼──┐
│  NLP  │ │Kafka│
│(Python)│└─────┘
└───▲───┘
    │
┌───┴────┐
│Scraper │
│(Bun.js)│
└────────┘
```

## 🚀 Quick Start
```bash
# Clone infrastructure
git clone https://github.com/OferGM-job-market-intelligence/infrastructure.git
cd infrastructure

# Start all services
docker-compose -f docker/docker-compose.yml up -d

# Verify
docker-compose ps
```

## 📈 Development Roadmap

- [x] **Week 1**: Project initialization ✅
- [ ] **Week 2-3**: Scraper service (Bun.js)
- [ ] **Week 4-5**: NLP service (Python)
- [ ] **Week 6**: Aggregation service (Go)
- [ ] **Week 7-8**: Auth service (Go)
- [ ] **Week 9-10**: API Gateway (GraphQL/REST)
- [ ] **Week 11-12**: Frontend dashboard (React)
- [ ] **Week 13**: Kubernetes deployment
- [ ] **Week 14**: ELK observability stack
- [ ] **Week 15**: Production ready 🎉

## 🎯 Use Cases

**For Job Seekers**: "What skills should I learn for Data Scientist roles?"  
**For Developers**: "Is Go more in-demand than Rust?"  
**For Educators**: "What skills should we teach in 2026?"  
**For Recruiters**: "What's market rate for Senior SWE in NYC?"  

## 📖 Documentation

- [Architecture Overview](https://github.com/OferGM-job-market-intelligence/infrastructure#readme)
- [API Documentation](https://github.com/OferGM-job-market-intelligence/api-gateway#readme)
- [Deployment Guide](https://github.com/OferGM-job-market-intelligence/infrastructure#kubernetes-deployment)

## 🤝 Contributing

This is a portfolio project demonstrating enterprise-grade microservices architecture. Feedback and suggestions welcome!

## 📄 License

MIT License - See individual repositories for details

## 👤 About

Building this to demonstrate:
- ✅ Polyglot microservices architecture
- ✅ ML/NLP with Python (spaCy)
- ✅ High-performance Go services
- ✅ Modern JavaScript (Bun.js)
- ✅ Production DevOps practices
- ✅ Enterprise security patterns

---

**Current Status**: 🚧 Week 1/15 - Active Development  
**Next Milestone**: Docker Compose Infrastructure (Day 3)  
**Completion Target**: 90 days (13 weeks)