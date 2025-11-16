# Architecture Decision Records (ADRs)

Documents key technical decisions and their rationale.

## What is an ADR?

An Architecture Decision Record captures important architectural decisions along with their context and consequences.

## Example ADRs

- (Sample) `2025-11-13-lm-studio-api.md` – Example of an ADR documenting why a particular AI API was chosen over alternatives.

> **Note:** Treat the included ADR file(s) as examples of structure and depth. In each project, create your own ADRs that reflect that project’s actual decisions.

## ADR Format

Each ADR should include:

- **Status:** Proposed | Accepted | Deprecated | Superseded
- **Context:** What problem are we solving?
- **Decision:** What did we decide?
- **Consequences:** What are the tradeoffs?
- **Alternatives Considered:** What else did we evaluate?

## Creating New ADRs

1. Name: `YYYY-MM-DD-decision-title.md`
2. Follow format above
3. Link from this README
4. Reference in related plans/guides

## Proposed Future ADRs

Based on current architecture:

- **ADR-002**: Service layer pattern (1 service = 1 business logic)
- **ADR-003**: Repository pattern for database access only
- **ADR-004**: Resource vs raw JSON response strategy
- **ADR-005**: No repository layer for external APIs

---

Copyright (c) 2025 Viet Vu  
Company: JOOservices Ltd  
Licensed under the MIT License.
