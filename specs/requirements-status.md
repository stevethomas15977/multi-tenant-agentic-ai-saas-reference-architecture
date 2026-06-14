# Requirements Status

This file tracks implementation readiness for requirements without cluttering the canonical requirements text.

## Status Definitions

- `Ready`: Requirement is clear enough for implementation planning or test design.
- `Draft`: Requirement is valid but still has open design, scope, or verification detail.
- `Blocked`: Requirement cannot reasonably proceed until a named decision or dependency is resolved.

## Status Matrix

| Requirement | Status | Rationale / Next Step |
| --- | --- | --- |
| REQ-AUTH-000 | Ready | Cognito authentication and token use are defined; implementation needs User Pool, app client, token, and route guard details. |
| REQ-AUTH-001 | Ready | Authorization retrieval is defined and tenant context source is resolved by `REQ-AUTH-004`; implementation needs AVP request shape. |
| REQ-AUTH-002 | Ready | UI enablement behavior is defined and Cedar-authorized action menu requirements refine it. |
| REQ-AUTH-003 | Ready | Backend AVP enforcement and fail-closed behavior are defined; implementation needs protected operation mapping. |
| REQ-AUTH-004 | Ready | `tenant_id` source decision is accepted in `DEC-001`; verification scenarios exist in `verification.md`. |
| REQ-AUTHZ-001 | Ready | Tenant-scoped authorization context and failure behavior are defined. |
| REQ-AUTHZ-002 | Ready | Group-based action grant behavior is defined. |
| REQ-AUTHZ-003 | Ready | Default-deny behavior is defined. |
| REQ-AUTHZ-004 | Ready | Cross-tenant forbid behavior is defined. |
| REQ-AUTHZ-005 | Ready | Same-tenant group loading behavior is defined and backed by the `authorization-backing.md` static fixture model for `SLICE-001`. |
| REQ-AUTHZ-006 | Ready | Group-action mapping behavior is defined and duplicate effective grants are handled by the `authorization-backing.md` static fixture model for `SLICE-001`. |
| REQ-AUTHZ-007 | Ready | Cedar-authorized UI navigation rendering is defined and traceable to UI verification. |
| REQ-AUTHZ-008 | Ready | Selected action view rendering is defined and Angular Router composition decision is accepted in `DEC-003`. |
| REQ-AUTHZ-009 | Ready | Unauthorized REST/WebSocket action denial behavior is defined. |
| REQ-AUTHZ-010 | Draft | Canonical action taxonomy is defined; action catalog ownership, versioning, and migration process remain open. |
| REQ-TENANT-001 | Draft | Tenant isolation behavior is defined; tenant-specific versus shared resource strategy and evidence expectations remain open. |
| REQ-UI-001 | Draft | CloudFront/S3 delivery is defined; direct S3 endpoint access and tenant deployment topology remain open. |
| REQ-UI-002 | Draft | ACM certificate requirement is defined; certificate scope across tenants/applications remains open. |
| REQ-UI-003 | Draft | Top navigation layout is defined; tenant display name source and fixed-versus-policy-controlled global links remain open. |
| REQ-UI-004 | Ready | Authorized action menu behavior is defined and backed by Cedar taxonomy requirements. |
| REQ-UI-005 | Ready | Unauthorized action omission is defined with fail-closed behavior. |
| REQ-UI-006 | Ready | Action view replacement is defined and Angular Router child route strategy is accepted in `DEC-003`. |
| REQ-UI-007 | Ready | Persistent top navigation behavior during action transitions is defined. |
| REQ-API-001 | Draft | Cognito Authorizer requirement is defined; token type, scopes, claims, and REST/WebSocket differences remain open. |
| REQ-API-002 | Draft | Lambda backend integration is defined; runtime selection, integration style, and function organization remain open. |
| REQ-API-003 | Draft | WebSocket Bedrock Agent memory integration is defined; simple agent scope, route mapping, and memory retention remain open. |
| REQ-API-004 | Draft | Durable REST session/preference persistence is explicitly optional; timing and storage choice remain open. |
| REQ-DNS-001 | Draft | CloudFront DNS record requirement is defined; hosted zone and naming strategy remain open. |
| REQ-DNS-002 | Draft | Cognito authorization DNS record requirement is defined; managed versus custom domain strategy remains open. |
| REQ-DNS-003 | Draft | API Gateway DNS record requirement is defined; endpoint domain naming and public/private scope remain open. |
| REQ-IAC-001 | Draft | Terraform lifecycle requirement is defined; backend, state locking, module boundaries, and bootstrap exceptions remain open. |

## Current Implementation Readiness Summary

- Ready: 18
- Draft: 13
- Blocked: 0

## Recommended Next Status Moves

- Move `REQ-AUTHZ-010` to `Ready` after action catalog ownership, versioning, and migration review are decided.
- Move `REQ-UI-003` to `Ready` after tenant display name source and Profile/Logout/Help policy behavior are decided.
- Use `SLICE-001` in `implementation-slices.md` as the first implementation planning target.
