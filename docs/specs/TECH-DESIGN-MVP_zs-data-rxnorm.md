# TECH-DESIGN-MVP — `zs-data-rxnorm`

> **Document:** Technical Design (MVP) | **Version:** 1.0.0-mvp
> **Repository:** [https://github.com/zarishsphere/zs-data-rxnorm](https://github.com/zarishsphere/zs-data-rxnorm)
> **Layer:** Layer 6B | **Catalog #:** 158
> **Language:** Go 1.26.1 / SQL | **License:** Apache 2.0

---

## Technical Summary

**RxNorm NLM API client and medication normalization.**

This document defines the **technical architecture, implementation design, complete repository tree, and acceptance criteria** for the MVP of `zs-data-rxnorm`.

---

## Database Schema

```sql
CREATE TABLE terminology.rxnorm (
    rxcui        TEXT PRIMARY KEY,
    name         TEXT NOT NULL,
    tty          TEXT,
    language     TEXT DEFAULT 'ENG'
);
```

## Go Client Interface

```go
type Client interface {
    Lookup(ctx context.Context, code string) (*Entry, error)
    Search(ctx context.Context, query string, limit int) ([]*Entry, error)
    Validate(ctx context.Context, code string) (bool, error)
}
```

## Valkey Caching Pattern

```go
func (c *client) Lookup(ctx context.Context, code string) (*Entry, error) {
    key := fmt.Sprintf("rxnorm:lookup:%s", code)
    // Try cache first
    cached, err := c.cache.Get(ctx, key)
    if err == nil {
        return deserialize(cached), nil
    }
    // Fall back to PostgreSQL
    entry, err := c.db.LookupCode(ctx, code)
    if err != nil {
        return nil, err
    }
    // Store in cache with 24h TTL
    c.cache.Set(ctx, key, serialize(entry), 24*time.Hour)
    return entry, nil
}
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
