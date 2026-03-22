# PRD-MVP — `zs-data-rxnorm`

> **Document:** Product Requirements (MVP) | **Version:** 1.0.0-mvp
> **Repository:** [https://github.com/zarishsphere/zs-data-rxnorm](https://github.com/zarishsphere/zs-data-rxnorm)
> **Layer:** Layer 6B — Terminology | **Catalog #:** 158
> **Language:** SQL / CSV / Go | **License:** Apache 2.0

---

## Executive Summary

**RxNorm NLM API client and medication normalization.**

This document defines the **Minimum Viable Product (MVP)** scope for `zs-data-rxnorm` within the ZarishSphere sovereign digital health platform. It covers what must be built first, acceptance criteria, user stories, and the complete repository file structure.


### Platform Non-Negotiables (apply to every repository)

| Constraint | Rule |
|-----------|------|
| **Zero Cost** | All tooling, hosting, and services must use genuinely free tiers |
| **Open Source** | Apache 2.0 license; all code public |
| **FHIR R5 Native** | All clinical data modelled as FHIR R5 resources |
| **Offline-First** | Must function without network connectivity |
| **No-Coder Friendly** | GUI-first, template-driven, automatable |
| **Documentation as Code** | All decisions in GitHub via RFC/ADR |
| **Multi-tenant** | tenant_id scoping on all data operations |
| **HIPAA/GDPR** | AuditEvent on all PHI access; field-level encryption |

---

## Problem Statement

Without loaded terminology data, the terminology service cannot validate codes, the FHIR engine cannot expand ValueSets, and clinical forms cannot auto-complete coded fields. This blocks all clinical data quality.

## MVP Goals

1. PostgreSQL migration scripts load the terminology into the database
2. Go client provides a simple Lookup(code) API with Valkey caching
3. CI validates data integrity (no duplicates, UTF-8 encoding)
4. Annual update process is documented and automatable

## MVP Functional Requirements

| ID | Requirement | Acceptance Criteria | Priority |
|----|------------|---------------------|---------|
| M-01 | PostgreSQL migration loads terminology | `migrate up` creates table + data | P0 |
| M-02 | Go client Lookup returns correct entry | `client.Lookup(ctx, code)` returns display | P0 |
| M-03 | No duplicate codes in seed data | Validation script exits 0 | P0 |
| M-04 | UTF-8 encoding verified | All data files are valid UTF-8 | P0 |
| M-05 | Valkey cache (24h TTL) on lookups | Second lookup < 1ms (cache hit) | P1 |

## Data Source

- **Source:** https://rxnav.nlm.nih.gov/REST/
- **Update Frequency:** Weekly via API

## MVP Complete Repository Tree

```
zs-data-rxnorm/
├── README.md
├── LICENSE
├── go.mod
├── go.sum
├── .gitignore
├── CHANGELOG.md
├── .github/
│   ├── CODEOWNERS
│   └── workflows/
│       ├── ci.yml
│       └── update-check.yml               # Detect new upstream release
├── data/
│   └── (source data files)
├── migrations/
│   ├── 001_create_rxnorm_table.sql
│   └── 002_seed_rxnorm_data.sql
├── client/
│   ├── client.go
│   ├── client_test.go
│   └── cache.go                           # Valkey caching layer
├── scripts/
│   ├── download.sh                        # Download from https://rxnav.nlm.nih.gov/REST/
│   ├── import.sh
│   └── validate.sh
├── tests/
│   └── integration/
│       └── lookup_test.go
└── docs/
    ├── SCHEMA.md
    └── UPDATE-PROCESS.md
```

---


## Owners & Governance

| Role | GitHub Handle | Responsibility |
|------|--------------|----------------|
| Platform Lead | `@arwa-zarish` | Final approval, RFC votes |
| Technical Lead | `@code-and-brain` | Architecture, Go/TS review |
| DevOps Lead | `@DevOps-Ariful-Islam` | CI/CD, infra, deployment |
| Health Programs | `@BGD-Health-Program` | Clinical content, country programs |

**PR Policy:** All changes via Pull Request. Minimum 1 owner review. CI must pass. No direct commits to `main`.


---

## MVP Acceptance Checklist

- [ ] All MVP files exist in repository with real content (not placeholders)
- [ ] CI pipeline passes on `main` branch
- [ ] No secrets, credentials, or PHI committed
- [ ] README.md reflects current state with setup instructions
- [ ] CODEOWNERS file present
- [ ] All MVP functional requirements verified manually or via automated tests
- [ ] Linked to `CATALOGS.md` and `TODO.md` in `zs-docs-platform`

---

*This document is the authoritative MVP specification for `zs-data-rxnorm`.*
*Changes require a Pull Request with at least 1 owner approval.*
