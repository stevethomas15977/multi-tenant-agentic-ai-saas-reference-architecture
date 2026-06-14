# Decision Log

This file records lightweight architecture decisions that guide implementation and future requirement changes.

## DEC-001: Use Cognito Custom Claim as Primary Tenant Source

**Status:** Accepted

**Decision**

Use a Cognito-issued custom claim as the primary source for `tenant_id` after user authentication.

Backend components use this claim when building Amazon Verified Permissions authorization context. Backend validation against the configured authorization backing source is performed when stronger tenant assurance is required.

**Rationale**

This keeps tenant context close to authenticated identity, avoids requiring a separate tenant lookup for every initial authorization flow, and supports fail-closed validation for sensitive tenant-scoped operations.

**Consequences**

- Cognito token design must include a stable custom claim for `tenant_id`.
- Backend authorization code must reject missing or invalid tenant context.
- Any future tenant membership source must stay consistent with the Cognito-issued claim.

**Related Requirements**

- `REQ-AUTH-004`
- `REQ-TENANT-001`
- `REQ-AUTHZ-001`

## DEC-002: Use Cedar Group-Action Taxonomy for Initial Authorization

**Status:** Accepted

**Decision**

Use a Cedar authorization model based on tenant-scoped users, groups, actions, resources, and tenants.

The initial authorization pattern permits actions through same-tenant group-action grants, forbids cross-tenant access, and relies on Cedar default-deny behavior when no permit applies.

**Rationale**

This model gives a clear starting point for multi-tenant action authorization without prematurely introducing delegated administration, adaptive policy conditions, or tenant-defined custom actions.

**Consequences**

- UI routes and backend operations must map to canonical action identifiers.
- Group membership and group-action grant data must be available to the authorization context builder.
- Action identifiers require governance before rename or migration.

**Related Requirements**

- `REQ-AUTHZ-001` through `REQ-AUTHZ-010`
- `REQ-AUTH-001`
- `REQ-AUTH-002`
- `REQ-AUTH-003`

## DEC-003: Use Angular Router Child Routes for Action Views

**Status:** Accepted

**Decision**

Use a persistent Angular shell layout with Angular Router child routes rendered through a center panel router outlet for authorized action views.

Dynamic component loading is reserved for a future plugin-like action view model and is not the default composition strategy.

**Rationale**

Angular Router child routes provide a standard, testable, and maintainable composition model for action views while preserving the top navigation shell.

**Consequences**

- Authorized left-side menu actions should map to child routes.
- The top navigation shell must remain outside the child route outlet.
- Tests should verify that route changes update the center panel while preserving the top navigation bar.

**Related Requirements**

- `REQ-UI-006`
- `REQ-UI-007`
- `REQ-AUTHZ-008`

## DEC-004: Defer DynamoDB Durable Persistence

**Status:** Accepted

**Decision**

Defer DynamoDB as a required capability for user sessions, preferences, and authorization backing data.

The initial architecture proceeds without DynamoDB as a prerequisite. Durable tenant-scoped persistence may be introduced later, with DynamoDB as one candidate option.

**Rationale**

This keeps the first implementation slice focused on authentication, tenant context, authorization, API enforcement, and authorized UI rendering. It avoids prematurely combining session metadata, short-term conversation memory, user preferences, and authorization backing data into one persistence design.

**Consequences**

- Requirements must not assume DynamoDB exists for the first implementation slice.
- Any temporary authorization backing source must be explicit.
- If durable persistence is introduced later, tenant isolation, TTL, retention, indexing, and Terraform ownership must be specified before implementation.

**Related Requirements**

- `REQ-API-004`
- `REQ-IAC-001`
- `REQ-AUTHZ-005`
- `REQ-AUTHZ-006`

## DEC-005: Use Terraform for AWS Infrastructure Lifecycle

**Status:** Accepted

**Decision**

Use Terraform as the infrastructure lifecycle mechanism for AWS resources required by the architecture.

Terraform manages stable AWS infrastructure unless an exception is documented. Runtime data and high-churn operational records are not Terraform-managed unless explicitly classified as bootstrap or reference configuration.

**Rationale**

Terraform gives reviewable infrastructure changes, state tracking, repeatable provisioning, and drift detection for the reference architecture.

**Consequences**

- AWS infrastructure changes require Terraform definitions or documented exceptions.
- Terraform plan output becomes part of infrastructure review.
- Terraform module boundaries must preserve tenant isolation and environment separation.

**Related Requirements**

- `REQ-IAC-001`

## DEC-006: Maintain Draw.io Architecture Diagrams

**Status:** Accepted

**Decision**

Maintain editable Draw.io context and container diagrams under `/architecture` when design changes affect system structure, boundaries, integrations, or deployment responsibilities.

Use the AWS 2026 Open Library within Draw.io for specified AWS services when a corresponding service shape exists.

**Rationale**

Keeping diagrams alongside requirements and design artifacts helps preserve architecture intent and makes structural changes reviewable.

**Consequences**

- Runtime or deployment structure changes should update both design text and Draw.io diagrams.
- Pure policy, wording, or internal UI behavior changes do not require diagram updates unless they affect architecture boundaries or integrations.

**Related Requirements**

- `specs/design.md` Design Standards
