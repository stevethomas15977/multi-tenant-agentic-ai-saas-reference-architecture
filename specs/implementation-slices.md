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

### Build Tasks

| Task ID | Task | Related Requirements |
| --- | --- | --- |
| SLICE-001-B01 | Create or scaffold the Angular application shell with persistent top navigation, left-side menu region, and center panel router outlet. | REQ-UI-003, REQ-UI-004, REQ-UI-006, REQ-UI-007 |
| SLICE-001-B02 | Define Angular routes for starter authorized action views such as dashboard, reports, and admin. | REQ-AUTHZ-008, REQ-UI-006 |
| SLICE-001-B03 | Implement Cognito-style authentication adapter that produces user identity, token state, `user_id`, and `tenant_id` custom claim for the slice. | REQ-AUTH-000, REQ-AUTH-004 |
| SLICE-001-B04 | Implement authorization service interface that requests or simulates Amazon Verified Permissions / Cedar decisions for the current user, tenant, action, and resource context. | REQ-AUTH-001, REQ-AUTHZ-001 |
| SLICE-001-B05 | Implement starter Cedar-compatible authorization fixtures for users, groups, group-action grants, resources, and action catalog entries. | REQ-AUTHZ-001, REQ-AUTHZ-002, REQ-AUTHZ-003, REQ-AUTHZ-004 |
| SLICE-001-B06 | Implement fail-closed handling for missing `tenant_id`, cross-tenant access, unknown actions, unavailable authorization data, and users with no granting group. | REQ-AUTH-002, REQ-AUTH-004, REQ-AUTHZ-003, REQ-AUTHZ-004, REQ-UI-005 |
| SLICE-001-B07 | Map permitted Cedar action identifiers to left-side Angular menu links. | REQ-AUTH-002, REQ-AUTHZ-007, REQ-UI-004 |
| SLICE-001-B08 | Omit unauthorized actions from selectable left-side menu links. | REQ-AUTHZ-007, REQ-UI-005 |
| SLICE-001-B09 | Ensure selecting an authorized menu item updates the center panel route while preserving the top navigation shell. | REQ-AUTHZ-008, REQ-UI-006, REQ-UI-007 |

### Test Tasks

| Task ID | Task | Verification Coverage |
| --- | --- | --- |
| SLICE-001-T01 | Test that a Cognito-style authenticated user token exposes the configured `tenant_id` custom claim. | TM-AUTH-001 |
| SLICE-001-T02 | Test that missing or invalid tenant context fails closed when validation is required. | TM-AUTH-002, TM-AUTHZ-003 |
| SLICE-001-T03 | Test same-tenant granted action permits. | TM-AUTHZ-001, TM-AUTHZ-004 |
| SLICE-001-T04 | Test cross-tenant apparent grant denies. | TM-AUTHZ-002 |
| SLICE-001-T05 | Test default-deny behavior for no matching grant and users with no granting group. | TM-AUTHZ-005 |
| SLICE-001-T06 | Test authorized action menu renders only permitted links. | TM-AUTHZ-009 |
| SLICE-001-T07 | Test unauthorized catalog actions are not rendered as selectable links. | TM-AUTHZ-010 |
| SLICE-001-T08 | Test authorized menu click displays the selected Angular child route in the center panel. | TM-AUTHZ-011, TM-UI-001 |
| SLICE-001-T09 | Test top navigation remains visible and unchanged during center panel route transitions. | TM-UI-001 |

### Acceptance Evidence

| Evidence ID | Evidence | Source |
| --- | --- | --- |
| SLICE-001-E01 | Screenshot or test output showing authenticated tenant user and visible tenant context. | SLICE-001-T01 |
| SLICE-001-E02 | Cedar-compatible authorization test results for same-tenant permit, cross-tenant deny, missing-tenant deny, and default deny. | SLICE-001-T02 through SLICE-001-T05 |
| SLICE-001-E03 | UI test or screenshot showing only authorized left-side menu links. | SLICE-001-T06, SLICE-001-T07 |
| SLICE-001-E04 | UI test or screenshot showing selected action view rendered in the center panel. | SLICE-001-T08 |
| SLICE-001-E05 | UI test or screenshot showing top navigation remains visible across action view transitions. | SLICE-001-T09 |

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
