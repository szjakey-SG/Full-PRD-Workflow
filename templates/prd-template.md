# PRD Document Template

## Document Header

```markdown
# {Product Name} — Product Requirements Document

| Item | Detail |
|------|--------|
| Version | v1.0 |
| Author | {author} |
| Date | {date} |
| Status | Draft / Review / Approved |
```

## Recommended Chapter Structure

### 1. Overview & Background
- Product positioning and value proposition
- Problem statement and business context
- Target users and personas
- Scope and boundaries (explicitly state what is out-of-scope)

### 2. Terminology & Conventions
- Domain-specific glossary (table format: Term | Definition | Example)
- Naming conventions used in the document
- Version phasing strategy (V1.0, V1.5, V2.0 ...)

### 3. System Architecture Overview
- High-level architecture description (reference companion Technical Diagrams)
- Core design principles (e.g., double-entry bookkeeping, event-driven, etc.)
- Integration points with upstream/downstream systems

### 4. Functional Modules

For **each module**, include:

```markdown
## Module N: {Module Name}

### N.1 Business Description
What this module does and why it matters.

### N.2 Business Rules
- Rule 1: ...
- Rule 2: ...

### N.3 Data Model
Field definition table:

| Field | Type | Constraint | Description |
|-------|------|------------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT | Primary key |
| ... | ... | ... | ... |

### N.4 Workflow / State Machine
Describe the lifecycle states and transitions.
Use Mermaid stateDiagram if applicable.

### N.5 Edge Cases & Error Handling
- Edge case 1: ...
- Edge case 2: ...

### N.6 Acceptance Criteria
Reference to acceptance test document, category and IDs.
```

### 5. Non-Functional Requirements
- Performance targets (latency, throughput, concurrency)
- Security model (authentication, authorization, encryption)
- Idempotency strategy (unique key composition, TTL, dedup logic)
- Data retention and archival policy
- Disaster recovery (RPO, RTO, backup strategy)
- Monitoring and alerting requirements
- Gray release / feature flag strategy

### 6. Version Roadmap
- V1.0: Core modules list
- V1.5: Enhancement modules
- V2.0+: Future modules

### Appendix A: Change Log
| Version | Date | Author | Changes |
|---------|------|--------|---------|

### Appendix B: Companion Documents
| Document | File | Description |
|----------|------|-------------|
| ER Diagram | {Project}_ER.mermaid | Entity relationship model |
| API Design | {Project}_API_Design.md | Endpoint specifications |
| Acceptance Tests | {Project}_Acceptance_Tests.md | Test cases |
| DDL | {Project}_DDL.sql | Database schema |
| Technical Diagrams | {Project}_Technical_Diagrams.html | Architecture visuals |
| Business Guide | {Project}_Business_Guide.html | Stakeholder presentation |

## Formatting Guidelines

- Use H2 (`##`) for top-level chapters, H3 (`###`) for sub-sections
- Field definition tables: always include Field, Type, Constraint, Description columns
- State machines: use Mermaid `stateDiagram-v2` with clear state names
- Keep prose concise; prefer tables over paragraphs for structured data
- Cross-reference format: "See [Section X.Y] for details" or "Ref: {companion doc}"
