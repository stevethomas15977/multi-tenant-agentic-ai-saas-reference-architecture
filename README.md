# Multi-tenant Agentic AI SaaS Reference Architecture

This repository documents a reference architecture for a multi-tenant Agentic AI SaaS application on AWS.

It is also a portfolio project: the purpose is to demonstrate Spec-Driven Development skills, architecture thinking, and disciplined technical decision-making as I transition from a traditional software engineering role toward an architect role.

## Why This Architecture Matters

Modern SaaS platforms increasingly need to combine secure multi-tenancy, fine-grained authorization, cloud-native deployment, and AI-assisted user experiences. Those concerns can easily become tangled if the system starts with code before the product boundaries, tenant model, authorization model, and verification strategy are understood.

This repository treats the architecture as a first-class engineering artifact. Requirements, design decisions, verification scenarios, implementation slices, and diagrams are maintained together so that implementation can proceed from a clear, testable, and reviewable foundation.

The reference architecture explores:

- Multi-tenant SaaS boundaries and tenant isolation.
- Angular UI delivery and authorization-driven navigation.
- Amazon Cognito authentication with a tenant-aware identity model.
- Amazon Verified Permissions and Cedar for fine-grained action authorization.
- API Gateway and Lambda backend integration patterns.
- Agentic AI integration through Amazon Bedrock.
- Long-term memory concepts using a Bedrock Knowledge Base and S3 vector index.
- Terraform-based AWS infrastructure lifecycle management.

## Spec-Driven Development Focus

The main skill demonstrated in this repository is not only designing an AWS architecture. It is showing how to move from broad architectural intent to implementation-ready work through Spec-Driven Development.

The repository captures:

- EARS-style requirements with stable requirement IDs.
- Requirement status tracking using `Draft`, `Ready`, and `Blocked`.
- Architecture context and design standards.
- Lightweight ADR-style decision records.
- Traceability from requirements to design, tasks, and verification.
- Verification scenarios that describe evidence expected from tests or reviews.
- Focused implementation slices with explicit scope and out-of-scope boundaries.

This helps prevent common architecture failures: unclear tenant boundaries, authorization gaps, premature implementation detail, uncontrolled scope expansion, and missing verification evidence.

## Current Implementation Slice

The first formal implementation slice is:

`SLICE-001: Cognito Tenant Authorization to Angular Authorized Menu`

This slice focuses on:

- Cognito login for the Angular UI.
- `tenant_id` from a Cognito-issued custom claim.
- Amazon Verified Permissions / Cedar action evaluation.
- Authorized left-side menu rendering.
- Unauthorized action omission.
- Angular Router child route action view rendering.
- Persistent top navigation during action transitions.

The slice intentionally excludes DynamoDB durable persistence, Bedrock integration, WebSocket behavior, DNS, CloudFront deployment, Terraform implementation, and full production API Gateway backend work. That keeps the first implementation target narrow and measurable.

See [specs/implementation-slices.md](specs/implementation-slices.md) for the full slice definition.

## Repository Structure

- [AGENTS.md](AGENTS.md): Durable guidance for Codex and future AI-assisted work in this repository.
- [specs/requirements.md](specs/requirements.md): Canonical EARS-style requirements.
- [specs/requirements-status.md](specs/requirements-status.md): Requirement readiness status.
- [specs/architecture.md](specs/architecture.md): Architecture context and boundaries.
- [specs/design.md](specs/design.md): Design details derived from requirements.
- [specs/decisions.md](specs/decisions.md): Lightweight architecture decision log.
- [specs/implementation-slices.md](specs/implementation-slices.md): Scoped implementation slices.
- [specs/tasks.md](specs/tasks.md): Implementation and verification task backlog.
- [specs/traceability.md](specs/traceability.md): Requirement-to-design/task/verification mapping.
- [specs/verification.md](specs/verification.md): Verification matrix and expected evidence.
- [architecture/](architecture/): Editable Draw.io architecture diagrams.

## Key Decisions

The current architecture decisions include:

- Use a Cognito custom claim as the primary `tenant_id` source.
- Use a Cedar group-action taxonomy for the initial authorization model.
- Use Angular Router child routes for action views.
- Defer DynamoDB durable persistence until a clear lifecycle need exists.
- Use Terraform for AWS infrastructure lifecycle management.
- Maintain Draw.io diagrams for structural and deployment-relevant design changes.

See [specs/decisions.md](specs/decisions.md) for rationale and consequences.

## Current Status

This repository is currently in architecture and SDD definition mode. It is not yet a complete deployable implementation.

The next engineering step is implementation planning for `SLICE-001`, using the existing requirements, decisions, verification matrix, and definition of done as the guardrails.

## Portfolio Intent

This project is intended to show how I approach architecture work:

- Start with clear requirements before building.
- Make assumptions and open questions visible.
- Separate product requirements from design decisions.
- Record decisions with rationale and consequences.
- Keep implementation scope narrow enough to verify.
- Use AI coding tools responsibly by giving them durable repo guidance and explicit slice boundaries.

The goal is to demonstrate the shift from writing software features in isolation to shaping systems that can be understood, governed, implemented, and verified.
