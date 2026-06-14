# Design

This file describes intended behavior and design details derived from the requirements.

## Design Standards

Every design update shall include the following architecture views when the change affects system structure, boundaries, integrations, or deployment responsibilities:

- Context Diagram
- Container Diagram

Use the Draw.io MCP service to generate diagrams.

Use the latest AWS 2026 (Open Library) icon set within Draw.io for each specified AWS service when a corresponding service icon exists.

Draw.io diagrams should use AWS service icons rather than generic boxes for AWS services, an AWS Cloud group/container for the AWS boundary, external user icons for users outside AWS, concise directional connectors, and small callout boxes for important assumptions or deferred decisions.

Store architecture diagram artifacts under `/architecture`.

After creating or updating a Draw.io diagram, export an SVG preview image and add or update a Markdown image reference so GitHub reviewers can visualize the diagram without opening Draw.io.

## Authentication and Authorization Flow

1. The user accesses protected Angular UI functionality.
2. The system authenticates the user through the configured Amazon Cognito User Pool.
3. The Angular UI receives Cognito-issued tokens required for UI identity and API Gateway access.
4. After successful authentication, the system resolves the user's tenant context, including `tenant_id`, from a Cognito-issued custom claim.
5. The system requests action authorization from Amazon Verified Permissions.
6. The UI authorization layer receives the resulting action permissions.
7. The UI enables only the tenant-scoped features or actions authorized for the authenticated user.

Backend operations that require tenant-scoped action authorization must enforce Amazon Verified Permissions decisions before completing the operation.

## Tenant Context Source

The primary source of `tenant_id` is a Cognito-issued custom claim in the authenticated user's token.

Backend components use the Cognito-issued `tenant_id` claim when building authorization context for Amazon Verified Permissions. When a tenant-scoped operation requires stronger assurance, the backend validates the claimed `tenant_id` against the configured authorization backing source before completing the operation.

If the `tenant_id` claim is missing or cannot be validated when validation is required, tenant-scoped operations fail closed.

## Cedar Authorization Model

Amazon Verified Permissions uses a Cedar policy model with tenant-scoped users, groups, actions, resources, and tenants.

### Entity Types

- `App::User`: includes `user_id` and `tenant_id`; may be a member of one or more `App::Group` entities.
- `App::Group`: includes `group_id`, `tenant_id`, and `name`; grants one or more `App::Action` entities.
- `App::Action`: includes `action_id` and `category`; represents UI and backend operations such as `app.navigate.dashboard`, `api.orders.read`, and `membership.assign`.
- `App::Resource`: includes `resource_id`, `resource_type`, and `tenant_id`; represents the protected target of an action.
- `App::Tenant`: includes `tenant_id`; represents the tenant isolation boundary.

### Relationships

- `App::User` to `App::Group` is many-to-many and tenant constrained.
- `App::Group` to `App::Action` is many-to-many.
- `App::Resource` to `App::Tenant` is many-to-one.
- `App::User` to `App::Tenant` is many-to-one.
- `App::Group` to `App::Tenant` is many-to-one.

### Policy Patterns

- Permit by group action and tenant match: allow an action only when the principal and resource share the same `tenant_id` and at least one same-tenant user group grants the requested action.
- Forbid cross-tenant access: deny when `principal.tenant_id` and `resource.tenant_id` differ, regardless of any apparent group-action grant.
- Deny by default: rely on Cedar's default deny behavior when no permit policy matches.

The Cedar schema, policy patterns, and Amazon Verified Permissions provisioning alignment are defined in `cedar-avp.md`. AVP policy stores, policies, and stable templates must be provisioned from version-controlled artifacts that match that schema.

### Action Taxonomy

Canonical action identifiers use lowercase dot-separated segments in the format `<domain>.<resource>.<verb>` or `app.navigate.<view>`. Action identifiers are stable; breaking renames require migration and mapping review.

The starter action taxonomy is `app.navigate.dashboard`, `app.navigate.reports`, `app.navigate.admin`, `api.orders.read`, `api.orders.create`, `membership.assign`, `membership.revoke`, `user.read`, and `user.update`.

### Authorization Data Backing Model

The authorization backing model includes `Users`, `Groups`, `UserGroupMembership`, `GroupActionGrant`, and `Resources` records. These records provide the user groups, same-tenant memberships, group-action grants, resource tenant context, and action identifiers used to build Cedar entities and authorization requests.

For `SLICE-001`, the configured authorization backing source is the version-controlled static fixture model defined in `authorization-backing.md`. This fixture backs users, groups, user-group memberships, group-action grants, resources, and action catalog entries until durable persistence is introduced.

Any future durable authorization backing source must preserve the same tenant-constrained membership behavior, effective group-action grant semantics, canonical action IDs, resource tenant ownership, and fail-closed handling for missing or invalid tenant context.

### Evaluation Flow

1. The user authenticates with Amazon Cognito.
2. The Angular UI or backend requests authorization for the current context.
3. The backend loads user groups and group action grants from the configured authorization backing source.
4. The backend builds Cedar entities and context for Amazon Verified Permissions.
5. Amazon Verified Permissions returns a decision.
6. The decision controls Angular menu rendering and REST/WebSocket backend action execution.

## Tenant Isolation

Tenant isolation is a system-wide requirement across identity, authorization, API access, data storage, DNS/resource scoping, and long-term memory.

Tenant-scoped operations must fail closed when tenant context is missing or cannot be validated.

## Angular UI Delivery

The Angular UI Web Application is hosted as static website content in Amazon S3.

Angular UI access is delivered through an Amazon CloudFront Distribution before reaching the Angular UI static website content. The CloudFront Distribution origin is the S3 Static Website endpoint URI for the deployed Angular application.

The Amazon CloudFront Distribution uses a TLS/SSL certificate issued by Amazon Certificate Manager to enable secure HTTPS connections for Angular UI access.

This design scope applies to Angular UI delivery only. Amazon API Gateway routing for API or service HTTP/HTTPS requests should be specified separately.

Editable Draw.io architecture views for this design are stored in `/architecture`:

- Context Diagram: `/architecture/context-diagram.drawio`
- Container Diagram: `/architecture/container-diagram.drawio`

## Angular UI Layout

The Angular UI layout uses a persistent shell with a top navigation bar and a center panel.

The top navigation bar contains the tenant name field, Profile link, Logout button, and Help link. These elements remain visible and unchanged while the user stays in the same page session and switches between center panel action views.

The center panel contains a left-side menu list and a selected action view region. The left-side menu list is derived from the current user's tenant-scoped Amazon Verified Permissions Cedar policy authorization results.

Only permitted action links are rendered in the left-side menu list. Actions that are not permitted, cannot be evaluated, or are missing authorization data are omitted from the menu list by default and should not appear as selectable DOM links.

When a user selects a permitted left-side menu action link, the Angular UI replaces the active action view in the center panel while preserving the top navigation bar.

The Angular implementation uses a persistent shell layout with Angular Router child routes rendered through a center panel router outlet. Dynamic component loading is reserved for a future plugin-like action view model and is not the default composition strategy.

## Backend REST Routing

Provisioned Amazon API Gateway endpoints use an Amazon Cognito Authorizer to authorize backend requests before downstream processing.

The Amazon Cognito Authorizer validates the caller's Cognito-authenticated identity at the API Gateway boundary. Amazon Verified Permissions remains the action authorization source for tenant-scoped feature and action decisions unless a later requirement changes that responsibility.

Unauthorized API Gateway requests should fail closed: if a request does not satisfy the configured Amazon Cognito Authorizer, API Gateway should not authorize the request for downstream processing.

After API Gateway authorizes a backend request, the request is processed by one or more Python-based and/or TypeScript-based Amazon Lambda functions. These Lambda functions contain backend request processing and business logic execution responsibilities.

The API Gateway to Lambda integration style, function granularity, and tenant isolation model remain open design decisions.

For Amazon API Gateway WebSocket endpoints, Python-based and/or TypeScript-based Amazon Lambda functions integrate with a simple Amazon Bedrock Agent when agentic processing is required. The Bedrock Agent uses an Amazon Bedrock Knowledge Base for long-term memory.

The Amazon Bedrock Knowledge Base is backed by an Amazon S3 vector index. Long-term memory access must preserve tenant boundaries and should use explicit retention, deletion, and retrieval policies before implementation.

For Amazon API Gateway REST endpoints, Python-based and/or TypeScript-based Amazon Lambda functions may use durable tenant-scoped persistence for user session and preferences data when that capability is included.

Session and preferences data must be associated with user and tenant context when persisted. DynamoDB-based persistence is deferred and remains an optional later capability.

## DNS Routing

Amazon Route 53 Alias or CNAME DNS records are created when externally addressable AWS service endpoints are provisioned.

The CloudFront DNS record routes Angular UI access to the Amazon CloudFront Distribution URI. The Cognito authorization DNS record routes authentication authorization traffic to the Cognito authorization endpoint. API Gateway DNS records route REST and WebSocket API traffic to the intended Amazon API Gateway URI.

Route 53 DNS records should be updated when the underlying service endpoint URI changes during replacement or reprovisioning.

## Terraform Infrastructure as Code

Terraform is the infrastructure lifecycle mechanism for AWS resources required by this architecture.

Terraform should manage stable AWS infrastructure resources, including Amazon S3 buckets, Amazon CloudFront distributions, Amazon Route 53 records, Amazon Certificate Manager certificates and DNS validation records, Amazon Cognito User Pools and app clients, Amazon Verified Permissions policy stores and stable policy templates, Amazon API Gateway REST and WebSocket APIs, Amazon Lambda functions and execution roles, Amazon Bedrock Agent and Knowledge Base resources where provider support is sufficient, IAM roles and policies, KMS keys, CloudWatch log groups and alarms, AWS WAF resources when used, and Amazon DynamoDB tables when durable persistence is included.

Terraform should not manage high-churn runtime data such as Cognito users, tenant runtime memberships, DynamoDB session or preference records, Bedrock Knowledge Base documents or embeddings, or tenant authorization grants that change during normal application operation unless they are explicitly defined as bootstrap/reference configuration.

Terraform module boundaries should reflect tenant isolation, environment separation, and lifecycle ownership. The Terraform design should document the backend, state locking, environment strategy, shared versus tenant-specific resources, and any resource exceptions.

## Fail-Closed UI Behavior

Until authorization is successfully retrieved and evaluated, tenant-scoped UI features and actions should not be enabled by default.

This behavior prevents optimistic UI access when authorization state is missing, stale, or unavailable.

## Design Notes

- UI enablement should not be treated as the only authorization control.
- Backend/API enforcement starts at API Gateway through Amazon Cognito Authorizers and may be extended with downstream action authorization requirements in Python-based and/or TypeScript-based Amazon Lambda functions, optional durable state operations, and Bedrock agent workflows.
- Authorization checks should use a stable action naming scheme that can be mapped to both UI features and protected operations.
