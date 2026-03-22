# PRD — `zs-data-rxnorm`

> **Document Class:** PRD | **Version:** 1.0.0 | **Status:** Bootstrapping
> **Repository:** [https://github.com/zarishsphere/zs-data-rxnorm](https://github.com/zarishsphere/zs-data-rxnorm)
> **Layer:** Layer 6B — Terminology Data | **Catalog #:** 158
> **License:** Apache 2.0 | **Governance:** RFC-0001

---

## 1. Overview

RxNorm — NLM API client and medication name normalization with local cache.

---

## 2. Repository Metadata

- **Name:** `zs-data-rxnorm`
- **Organization:** [https://github.com/zarishsphere](https://github.com/zarishsphere)
- **Language / Runtime:** SQL / CSV / JSON / Go
- **Visibility:** Public
- **License:** Apache 2.0
- **Default Branch:** `main`
- **Branch Protection:** Required (2-owner review for critical paths)

---

## 3. Platform Context

This repository is part of the **ZarishSphere** sovereign digital health operating platform — a free, open-source, FHIR R5-native system for South and Southeast Asia.

**Non-negotiable constraints:**
- Zero cost — all tooling must use genuinely free tiers
- FHIR R5 native — all clinical data modelled as FHIR R5 resources
- Offline-first — must work without network connectivity
- No-coder friendly — GUI-first, template-driven
- Documentation as Code — all decisions in GitHub

---

## 4. Goals & Objectives

- Load and index RXNORM terminology into PostgreSQL
- Provide a Go client library for other services to use
- Automate annual/biannual updates via CI pipeline

## 5. Functional Requirements

| ID | Requirement | Priority |
|----|------------|---------|
| F-01 | PostgreSQL seed scripts for latest release | P0 |
| F-02 | GIN-indexed tables for fast code lookup | P0 |
| F-03 | Go client with caching (Valkey TTL 24h) | P0 |
| F-04 | CI pipeline: detect new release, open PR automatically | P1 |
| F-05 | Update documentation with release notes | P1 |
| F-06 | UTF-8 encoding validation on all data files | P0 |

## 6. Repository Tree

```
zs-data-rxnorm/
├── README.md
├── LICENSE
├── go.mod
├── go.sum
├── .gitignore
├── .github/
│   ├── CODEOWNERS
│   └── workflows/
│       ├── ci.yml                     # Validate + lint
│       └── update-check.yml           # Detect new upstream release
├── data/
│   └── (source data files: CSV, JSON, RF2, etc.)
├── migrations/
│   ├── 001_create_rxnorm_table.sql
│   └── 002_seed_rxnorm_data.sql
├── client/
│   ├── client.go                      # Go client for rxnorm lookups
│   └── client_test.go
├── scripts/
│   ├── download.sh                    # Download latest release
│   ├── import.sh                      # Import to PostgreSQL
│   └── validate.sh                    # Validate data integrity
├── tests/
│   └── integration/
│       └── lookup_test.go             # testcontainers-go
└── docs/
    ├── SCHEMA.md                      # Database schema
    └── UPDATE-PROCESS.md              # How to update terminology
```

**Source:** NLM RxNav REST API (rxnav.nlm.nih.gov). **Update:** Weekly via API.

## 9. Ownership & Governance

| Role | GitHub User |
|------|-------------|
| Platform Lead | `@arwa-zarish` |
| Technical Lead | `@code-and-brain` |
| DevOps Lead | `@DevOps-Ariful-Islam` |
| Health Programs | `@BGD-Health-Program` |

All changes go through Pull Request → 1+ owner review → CI pass → merge.
Breaking changes require RFC + ADR.

---

## 10. Definition of Done

- [ ] All listed files exist with content
- [ ] CI pipeline passes (test + lint + security)
- [ ] README.md reflects current state
- [ ] OpenAPI / AsyncAPI spec present (services only)
- [ ] At least 1 integration test using testcontainers-go (Go) or Playwright (UI)
- [ ] No secrets committed (GitGuardian verified)
- [ ] CODEOWNERS file present
- [ ] Linked to CATALOGS.md and TODO.md

---

*This PRD is the canonical source of truth for this repository's purpose, structure, and requirements.*
*Changes require a PR against this file with owner approval.*
