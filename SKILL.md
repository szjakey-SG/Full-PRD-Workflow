---
name: full-prd-workflow
description: End-to-end PRD deliverable suite workflow covering requirements gathering, multi-file PRD generation (PRD doc, ER diagram, API design, acceptance tests, DDL), cross-reference verification, issue remediation, technical architecture diagrams, business-facing guides with demo UI mockups, and final packaging. Use when the user asks to write a PRD, create product requirements, generate technical specifications, or produce a complete PRD deliverable set.
---

# Full-Stack PRD Delivery Workflow

A 6-phase methodology for producing professional-grade PRD deliverable suites with built-in quality assurance.

## Workflow Overview

```
Phase 1: Requirements Scoping
Phase 2: Core Document Generation (5 files, parallelizable)
Phase 3: Cross-Reference Verification
Phase 4: Issue Remediation
Phase 5: Architecture Diagrams & Business Guide
Phase 6: Integration & Packaging
```

## Phase 1: Requirements Scoping

Before writing any document, gather the following through structured questions:

**Must-collect information:**
- Product name, version, and one-sentence positioning
- Target users and personas
- Functional module list (with priority/version phasing if multi-version)
- Core business entities and their relationships
- Key business workflows (state machines, approval flows, etc.)
- Integration points with upstream/downstream systems
- Non-functional requirements (performance, security, compliance)

**Scope boundary decisions:**
- Clarify what is explicitly out-of-scope (e.g., UI implementation, mobile apps)
- Identify which modules belong to V1.0 vs. later versions
- Determine if multi-currency, multi-language, or multi-tenant support is needed

**Output:** A confirmed scope summary that both parties agree on before proceeding.

---

## Phase 2: Core Document Generation

Generate 5 core deliverable files. These can be worked on in parallel using subagents for efficiency.

### 2.1 PRD Main Document (.md)

Follow the structure in [templates/prd-template.md](templates/prd-template.md). Key principles:

- Use H1 for document title, H2 for major chapters, H3 for sub-sections
- Every functional module gets: description, business rules, data model reference, workflow/state machine, edge cases
- Include a Non-Functional Requirements chapter covering: performance targets, security model, idempotency strategy, data retention/archival, disaster recovery
- Maintain an Appendix with cross-references to all companion documents
- Target length: 1500-3000 lines depending on system complexity

### 2.2 ER Diagram (.mermaid)

Follow conventions in [templates/er-conventions.md](templates/er-conventions.md). Key rules:

- Use Mermaid `erDiagram` syntax
- Include ALL fields with types and constraints (PK, FK, UK, NN)
- Document relationship cardinality: `||--o{`, `||--|{`, `}|--|{`, etc.
- Add header comment with entity count and relationship count
- Group related entities visually with comments

### 2.3 API Design Document (.md)

Follow [templates/api-design-template.md](templates/api-design-template.md). Key rules:

- Group endpoints by functional module (one section per module)
- Every endpoint includes: method + path, description, auth requirement, request schema (JSON), response schema (JSON), error codes
- Include common sections: pagination/filtering spec, authentication matrix, rate limiting policy, webhook events, error code catalog
- Target: complete request/response examples for all endpoints

### 2.4 Acceptance Tests Document (.md)

Follow [templates/test-case-template.md](templates/test-case-template.md). Key rules:

- Organize by test category with prefixed IDs (e.g., PAY-001, STF-003)
- Each test case: ID, title, precondition, steps, expected result, priority
- Cover: positive paths, negative/boundary cases, state machine transitions, concurrency, precision/rounding
- Include test data appendix (sample accounts, sample events, sample rules)
- Target: minimum 10 test cases per functional module

### 2.5 Database DDL (.sql)

Follow [templates/ddl-conventions.md](templates/ddl-conventions.md). Key rules:

- Use `CREATE TABLE` with explicit column types, constraints, and comments
- All tables include: `id BIGINT PRIMARY KEY`, `created_at`, `updated_at`
- Use `COMMENT ON TABLE/COLUMN` for documentation
- Include index definitions for frequently queried fields
- Header comment: file version, table count, generation date

---

## Phase 3: Cross-Reference Verification

After generating all 5 files, run a multi-dimension consistency check.

### Verification Dimensions

**Dimension 1: Entity Consistency**
- PRD entity descriptions vs. ER diagram entities vs. DDL tables
- Every entity in PRD must exist in ER and DDL
- Entity counts in comments must match actual counts

**Dimension 2: Field Consistency**
- Field names in PRD field definition tables vs. DDL column names
- Field types and constraints match across PRD and DDL
- ER diagram includes all fields defined in DDL

**Dimension 3: Relationship Consistency**
- ER diagram relationships vs. foreign keys in DDL
- PRD-described relationships are reflected in ER cardinality
- No orphan entities (every entity has at least one relationship)

**Dimension 4: API & Test Coverage**
- Every PRD functional module has corresponding API endpoints
- Every API endpoint has at least one acceptance test
- Test case IDs are sequential with no gaps

### Completeness Report Format

Generate a report with:
- Summary statistics (pass/fail counts by dimension)
- Issue list with severity: Critical / Major / Minor
- Each issue: dimension, description, affected files, recommended fix
- Save as `*_Completeness_Report.md`

See [templates/completeness-checklist.md](templates/completeness-checklist.md) for the full checklist.

---

## Phase 4: Issue Remediation

Fix all issues identified in the completeness report, ordered by severity.

### Fix Priority Order

1. **Critical** first — data model inconsistencies, missing entities, broken relationships
2. **Major** next — missing API endpoints, incomplete test coverage, field name mismatches
3. **Minor** last — documentation gaps, comment corrections, formatting issues

### Fix Workflow

```
For each issue:
  1. Identify all affected files
  2. Determine the source of truth (usually DDL or PRD)
  3. Apply fix across ALL affected files to maintain consistency
  4. Log the fix with before/after description
```

### Parallelization Strategy

Use subagents for independent fix streams:
- Subagent A: API design fixes
- Subagent B: Acceptance test fixes
- Main agent: PRD, ER, DDL fixes

After all fixes, run a focused re-verification on the changed areas.

---

## Phase 5: Architecture Diagrams & Business Guide

### 5.1 Technical Architecture Diagrams

Create an HTML file with interactive Mermaid diagrams covering:

| Diagram Type | Purpose | Mermaid Syntax |
|---|---|---|
| System Architecture | Layer/component overview | `graph TD` with subgraphs |
| Core Data Flow | How data moves between entities | `flowchart LR` |
| Sequence Diagrams | Key workflows (2-4 diagrams) | `sequenceDiagram` |
| Module Dependencies | Inter-module relationships | `graph TD` |
| Deployment Topology | Infrastructure layout | `graph TD` with subgraphs |

Implementation guidelines:
- Use Mermaid.js CDN: `<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js">`
- Provide a dark/light theme toggle or match project branding
- Wrap each diagram in a titled container with description text
- Initialize: `mermaid.initialize({ theme: 'dark' })` or `'default'`

### 5.2 Business-Facing Guide (HTML)

Create a single-file HTML document for non-technical stakeholders:

**Structure per functional module:**
1. Module name with icon and version badge
2. Business capability description (plain language, no jargon)
3. Capability cards grid (3-5 per module)
4. Operation logic flow (step-by-step visual)
5. Demo UI mockup (styled browser frame with wireframe elements)

**Demo UI mockup conventions:**
- Browser chrome frame (colored dots + URL bar)
- Search/filter bar with realistic controls
- Data table with sample rows and status badges
- Action buttons matching real workflows
- Use CSS variables for consistent theming

**Navigation:**
- Fixed sidebar with module links
- Smooth scroll + active section highlighting on scroll
- Hero section with system statistics

---

## Phase 6: Integration & Packaging

### 6.1 PRD Integration

Merge technical diagrams into the PRD main document:
- Insert each Mermaid diagram at its contextually relevant chapter
- Add diagram title and brief description above each
- Update Appendix B with complete companion document index

### 6.2 Final Packaging

Create a ZIP archive containing all deliverables:

```
{Project}_Deliverables_{version}.zip
├── {Project}_PRD.md
├── {Project}_ER.mermaid
├── {Project}_API_Design.md
├── {Project}_Acceptance_Tests.md
├── {Project}_DDL.sql
├── {Project}_Technical_Diagrams.html
├── {Project}_Business_Guide.html
└── {Project}_Completeness_Report.md
```

### 6.3 Delivery Checklist

Before delivering, verify:
- [ ] All files present in archive
- [ ] Cross-reference report shows 0 Critical / 0 Major issues
- [ ] PRD Appendix references all companion documents
- [ ] HTML files render correctly in browser (self-contained, no external deps except CDN)
- [ ] File naming is consistent across all deliverables

---

## Efficiency Tips

1. **Parallelize aggressively** — Use subagents for independent documents (API + Tests can run in parallel)
2. **DDL as source of truth** — When PRD and DDL conflict on field names/types, DDL usually wins
3. **Incremental verification** — Don't wait until all files are done; verify each pair as it completes
4. **Template reuse** — Keep the templates directory as a starting point; customize per project
5. **Version tracking** — Use semantic versioning in file headers (v1.0 initial, v1.1 after fixes)
