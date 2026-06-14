# Implementation Slices

This file defines focused implementation slices derived from the requirements, decisions, and verification matrix.

## SLICE-001: Cognito Tenant Authorization to Angular Authorized Menu

**Status:** Ready for implementation planning

### Goal

Implement the first end-to-end tenant authorization path from user authentication through authorized Angular menu rendering.

### User Outcome

An authenticated tenant user sees only the Angular left-side menu actions authorized for that user's `tenant_id` and can select an authorized action view while the top navigation remains visible.

### In Scope

- Cognito login for the Angular UI.
- Cognito-issued custom claim as the primary `tenant_id` source.
- Amazon Verified Permissions / Cedar action evaluation for the current user and tenant context.
- Cedar tenant-scope, group-action permit, default-deny, and cross-tenant forbid behavior.
- Authorized left-side menu rendering.
- Unauthorized action omission from selectable menu links.
- Angular Router child route action view rendering in the center panel.
- Persistent top navigation during center panel action view transitions.

### Out of Scope

- DynamoDB durable persistence.
- Bedrock Agent integration.
- Bedrock Knowledge Base and S3 vector index memory.
- WebSocket backend behavior.
- Route 53, CloudFront, S3 static website deployment, and ACM certificate provisioning.
- Terraform module implementation.
- Full production API Gateway backend implementation beyond any stub or mocked authorization boundary needed for the slice.
- Tenant-defined custom actions, delegated administration, adaptive policy conditions, and action catalog migration workflows.

### Included Requirements

- `REQ-AUTH-000`
- `REQ-AUTH-001`
- `REQ-AUTH-002`
- `REQ-AUTH-004`
- `REQ-AUTHZ-001`
- `REQ-AUTHZ-002`
- `REQ-AUTHZ-003`
- `REQ-AUTHZ-004`
- `REQ-AUTHZ-007`
- `REQ-AUTHZ-008`
- `REQ-UI-003`
- `REQ-UI-004`
- `REQ-UI-005`
- `REQ-UI-006`
- `REQ-UI-007`

### Supporting Decisions

- `DEC-001`: Use Cognito Custom Claim as Primary Tenant Source.
- `DEC-002`: Use Cedar Group-Action Taxonomy for Initial Authorization.
- `DEC-003`: Use Angular Router Child Routes for Action Views.
- `DEC-004`: Defer DynamoDB Durable Persistence.

### Verification Coverage

- `TM-AUTH-001`
- `TM-AUTH-002`
- `TM-AUTHZ-001`
- `TM-AUTHZ-002`
- `TM-AUTHZ-003`
- `TM-AUTHZ-004`
- `TM-AUTHZ-005`
- `TM-AUTHZ-009`
- `TM-AUTHZ-010`
- `TM-AUTHZ-011`
- `TM-UI-001`

### Implementation Assumptions

- The first slice may use stubbed or static authorization backing data for users, groups, group-action grants, resources, and action catalog entries until a durable backing source is selected.
- The Angular UI can use a development or mocked Cognito/AVP integration during local implementation, provided the interfaces preserve the required token, `tenant_id`, and authorization decision behavior.
- Backend enforcement beyond the authorized menu path may be stubbed for this slice, but authorization semantics must match the Cedar policy expectations.

### Definition of Done

- An authenticated user has a resolved `tenant_id` from a Cognito-style custom claim.
- Authorization evaluation uses tenant context and Cedar-compatible group-action semantics.
- Same-tenant granted actions are permitted.
- Cross-tenant and missing-tenant authorization attempts are denied.
- The Angular left-side menu renders only authorized action links.
- Unauthorized action links are not rendered as selectable menu links.
- Selecting an authorized menu action displays the corresponding Angular child route action view in the center panel.
- The top navigation remains visible and unchanged during action view transitions.
- Verification scenarios listed for this slice are implemented or explicitly mapped to pending test work.
