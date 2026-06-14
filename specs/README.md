# Specification Index

This directory contains the spec-driven development artifacts for the Multi-tenant Agentic AI SaaS Reference Architecture.

## Artifacts

- [requirements.md](requirements.md): Canonical EARS requirements.
- [requirements-status.md](requirements-status.md): Draft, Ready, and Blocked status tracking for requirements.
- [architecture.md](architecture.md): Architecture context, boundaries, and major system decisions.
- [design.md](design.md): Design details that explain how requirements are intended to be satisfied.
- [decisions.md](decisions.md): Lightweight ADR-style decision log for accepted architecture and SDD decisions.
- [tasks.md](tasks.md): Implementation and verification work items derived from requirements.
- [traceability.md](traceability.md): Mapping between requirements, design notes, tasks, and verification evidence.
- [verification.md](verification.md): Test scenarios and evidence expectations for requirements that are ready for verification design.
- [../architecture](../architecture): Editable Draw.io architecture diagrams.

## Working Rules

- Requirements describe what the system must do, not how code should be implemented.
- Each requirement should be atomic, testable, and traceable.
- Tenant isolation and authorization behavior must be explicit.
- Ambiguities should be captured as assumptions or open questions until resolved.
