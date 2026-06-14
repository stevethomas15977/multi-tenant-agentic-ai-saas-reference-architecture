# Requirements

This file is the canonical source for system requirements. Requirements use EARS-style wording and stable identifiers.

## Authentication and Authorization

### REQ-AUTH-000: Authenticate Angular Users with Cognito

When a user accesses the Angular UI Web Application, the system shall authenticate the user with Amazon Cognito and obtain tokens required for Angular UI and API Gateway access.

**Acceptance Criteria**

- Given an unauthenticated user accesses protected Angular UI functionality, then the system initiates authentication with Amazon Cognito.
- Given Amazon Cognito successfully authenticates the user, then the Angular UI receives tokens required to identify the user.
- Given the Angular UI issues backend requests to Amazon API Gateway, then the Angular UI uses Cognito-issued tokens required by the configured API Gateway authorizer.

### REQ-AUTH-001: Retrieve Action Authorization

When the system authenticates a user via an Amazon Cognito User Pool, the system shall retrieve action authorization from Amazon Verified Permissions.

**Acceptance Criteria**

- Given a user successfully authenticates through the configured Amazon Cognito User Pool, when the authentication result is processed, then the system requests the user's action authorization from Amazon Verified Permissions.
- Given the authorization request is made, then the request includes tenant context sufficient for tenant-scoped policy evaluation.
- Given Amazon Verified Permissions returns authorization data, then the system makes that authorization data available to the UI authorization layer.

### REQ-AUTH-002: Enable Tenant-Scoped Authorized UI Actions

If the user is authenticated, then the system shall enable only the UI features or actions specified in the Amazon Verified Permissions configuration based on `tenant_id`.

**Acceptance Criteria**

- Given an authenticated user has a resolved `tenant_id`, when the UI renders tenant-scoped functionality, then only features or actions authorized by Amazon Verified Permissions for that `tenant_id` are enabled.
- Given a UI feature or action is not authorized for the authenticated user's `tenant_id`, then the system does not enable that feature or action for the user.
- Given authorization cannot be retrieved or evaluated, then tenant-scoped UI features and actions are not enabled by default.

### REQ-AUTH-003: Enforce Backend Action Authorization

When a backend operation requires tenant-scoped action authorization, the system shall enforce Amazon Verified Permissions authorization decisions before completing the operation.

**Acceptance Criteria**

- Given a backend operation requires tenant-scoped action authorization, when the authenticated user is authorized by Amazon Verified Permissions for the requested action and tenant context, then the system allows the operation to proceed.
- Given a backend operation requires tenant-scoped action authorization, when the authenticated user is not authorized by Amazon Verified Permissions for the requested action and tenant context, then the system rejects the operation.
- Given Amazon Verified Permissions cannot be evaluated for a backend operation that requires tenant-scoped action authorization, then the system does not allow the operation by default.

### REQ-AUTH-004: Resolve Tenant Context from Cognito Claims

When Amazon Cognito successfully authenticates a user, the system shall use a Cognito-issued custom claim as the primary source for the user's `tenant_id`.

**Acceptance Criteria**

- Given Amazon Cognito successfully authenticates a user, then the issued token includes the custom claim used as the primary `tenant_id` source.
- Given the system builds authorization context for a user, then the system resolves `tenant_id` from the Cognito-issued custom claim.
- Given backend validation against an authorization backing source is required, when the Cognito-issued `tenant_id` is evaluated, then the backend validates the claim before completing tenant-scoped operations.
- Given the Cognito-issued `tenant_id` claim is missing or cannot be validated when validation is required, then tenant-scoped operations fail closed.

## Authorization Policy Taxonomy

### REQ-AUTHZ-001: Evaluate Tenant-Scoped Authorization

When an authorization request is evaluated, the system shall evaluate access within tenant scope and include `tenant_id` for principal and resource context.

**Acceptance Criteria**

- Given principal `tenant_id` is `T1` and resource `tenant_id` is `T1`, when an action is granted by policy, then the request is permitted.
- Given principal `tenant_id` is `T1` and resource `tenant_id` is `T2`, when any action is requested, then the request is denied.
- Given a request is missing principal or resource `tenant_id`, when authorization evaluation occurs, then the request is denied.

### REQ-AUTHZ-002: Permit Group-Based Action Grants

When a user requests an action, the system shall permit the action only if at least one associated group grants that action.

**Acceptance Criteria**

- Given user `U1` belongs to groups `G1` and `G2`, and `G2` grants action `app.navigate.reports`, when `U1` requests `app.navigate.reports`, then the request is permitted.
- Given user `U1` belongs to groups `G1` and `G2`, and neither group grants action `user.disable`, when `U1` requests `user.disable`, then the request is denied.
- Given user `U1` has no group memberships, when any protected action is requested, then the request is denied.

### REQ-AUTHZ-003: Deny by Default

If no applicable permit policy exists for a request, then the system shall deny the request.

**Acceptance Criteria**

- Given a valid tenant-scoped request has no matching action grant, when authorization evaluation occurs, then the decision is deny.
- Given a request uses an unknown action identifier, when authorization evaluation occurs, then the decision is deny.

### REQ-AUTHZ-004: Forbid Cross-Tenant Access

If principal tenant and resource tenant are different, then the system shall deny the request regardless of any permit policy outcome.

**Acceptance Criteria**

- Given `principal.tenant_id` does not equal `resource.tenant_id`, when policy is evaluated, then the decision is deny.
- Given a misconfigured action grant exists for cross-tenant access, when policy is evaluated, then the decision remains deny.

### REQ-AUTHZ-005: Include Same-Tenant User Group Associations

When user identity and group memberships are loaded for authorization, the system shall include all groups associated with the user within the same tenant.

**Acceptance Criteria**

- Given user `U1` is mapped to `G1`, `G2`, and `G3` in tenant `T1`, when authorization context is built, then all three groups are included.
- Given user `U1` has a stale mapping to group `G9` in tenant `T2`, when authorization context is built for tenant `T1`, then `G9` is excluded.

### REQ-AUTHZ-006: Support Group-to-Action Mappings

When group permissions are configured, the system shall support one-to-many mappings between a group and action identifiers.

**Acceptance Criteria**

- Given group `manager` is mapped to `app.navigate.dashboard`, `api.orders.read`, and `membership.assign`, when mappings are loaded for authorization evaluation, then all mappings are available for evaluation.
- Given a duplicate mapping exists for the same `group_id` and `action_id`, when mappings are loaded for authorization evaluation, then only one effective mapping exists.

### REQ-AUTHZ-007: Render Cedar-Authorized UI Navigation

When the Angular UI layout is rendered, the system shall display left-side action links only for actions authorized for the current user by Cedar policy evaluation.

**Acceptance Criteria**

- Given user `U1` is authorized for `app.navigate.dashboard` and `app.navigate.reports`, when the UI menu is rendered, then both links are visible.
- Given user `U1` is not authorized for `app.navigate.admin`, when the UI menu is rendered, then `app.navigate.admin` is not visible.

### REQ-AUTHZ-008: Render Selected Action View

When a user clicks an authorized menu action link, the system shall display the selected action view in the center panel.

**Acceptance Criteria**

- Given user clicks `app.navigate.reports`, when the route or action is handled, then the center panel shows the reports action view.
- Given user clicks another authorized action after reports, when the route or action is handled, then the reports view is replaced by the newly selected action view.

### REQ-AUTHZ-009: Protect Unauthorized Backend Action Invocation

If a user attempts to invoke an unauthorized backend REST or WebSocket action, then the system shall deny execution and return an authorization error response.

**Acceptance Criteria**

- Given an action is not granted by any user group, when a REST endpoint is called, then the response is denied with HTTP 403.
- Given an action is not granted by any user group, when a WebSocket action is invoked, then the response indicates denied authorization and no business operation executes.

### REQ-AUTHZ-010: Govern Canonical Action Taxonomy

When action identifiers are defined, the system shall use a canonical action taxonomy with unique action IDs and stable naming conventions for policy and UI/API mappings.

**Acceptance Criteria**

- Given an action catalog update, when the action catalog is validated, then each action ID is unique.
- Given a UI route and API endpoint mapping process is completed, then each protected operation references a canonical action ID.
- Given a breaking action rename, when change review occurs, then impacted policies and mappings are identified before release.

### REQ-AUTHZ-011: Provision Verified Permissions with Cedar Schema

When an Amazon Verified Permissions policy store is provisioned, the system shall configure the policy store with a version-controlled Cedar schema that defines the entity types, action identifiers, principal/resource applicability, and tenant attributes required by the active implementation slice.

**Acceptance Criteria**

- Given the Amazon Verified Permissions policy store is provisioned, then the policy store has a Cedar schema aligned to the `App::User`, `App::Group`, `App::Action`, `App::Resource`, and `App::Tenant` model.
- Given a Cedar schema is provisioned, then the schema includes `tenant_id` attributes for principals and resources used in tenant-scoped authorization.
- Given a starter action is used by the Angular UI or backend authorization flow, then the action is represented in the Cedar schema or schema-aligned action catalog.
- Given a policy or authorization request references an action that is not in the schema-aligned action catalog, then the request fails closed or is rejected before authorization succeeds.

### REQ-AUTHZ-012: Provision Schema-Aligned Cedar Policies

When Cedar policies or policy templates are provisioned in Amazon Verified Permissions, the system shall validate that the policy artifacts align with the version-controlled Cedar schema and implement the required same-tenant group-action permit, cross-tenant forbid, and default-deny behavior.

**Acceptance Criteria**

- Given policy artifacts are provisioned, then they validate against the version-controlled Cedar schema before they are accepted.
- Given same-tenant group-action grants are configured, then the provisioned policy artifacts permit only the matching action for users in a granting same-tenant group.
- Given principal and resource tenants differ, then the provisioned policy artifacts deny access regardless of any apparent group-action permit.
- Given no policy grants the requested action, then Amazon Verified Permissions returns a deny decision by default.
- Given Terraform provisions Amazon Verified Permissions resources, then the policy store, Cedar schema, baseline cross-tenant forbid policy, and stable bootstrap policy artifacts are managed or explicitly documented as lifecycle exceptions.

## Authorization Policy Taxonomy Assumptions

- Canonical action identifiers use lowercase dot-separated segments in the format `<domain>.<resource>.<verb>` or `app.navigate.<view>`.
- Initial action identifiers include `app.navigate.dashboard`, `app.navigate.reports`, `app.navigate.admin`, `api.orders.read`, `api.orders.create`, `membership.assign`, `membership.revoke`, `user.read`, and `user.update`.
- Cedar policy evaluation denies by default when no permit policy matches.
- Before durable persistence is introduced, `SLICE-001` uses the version-controlled static authorization fixture defined in `authorization-backing.md` as the backing source for users, groups, memberships, grants, resources, and action catalog entries.
- Amazon Verified Permissions provisioning is aligned to the Cedar schema and policy patterns in `cedar-avp.md`.

## Authorization Policy Taxonomy Open Questions

- Should the starter action taxonomy be versioned as `v1` with migration metadata for future action renames?
- Are delegated administration workflows for tenant-defined custom actions out of scope for the initial implementation?
- Are attribute-based constraints beyond tenant isolation and group action grants out of scope for the initial implementation?
- Are time-based, location-based, or risk-adaptive policy conditions out of scope for the initial implementation?

## Multi-Tenant SaaS

### REQ-TENANT-001: Enforce Tenant Isolation

The system shall enforce tenant isolation across identity, authorization, API access, data storage, DNS/resource scoping, and long-term memory.

**Acceptance Criteria**

- Given a request includes tenant context, then the system uses that tenant context when evaluating identity, authorization, API access, data access, and long-term memory access.
- Given data, preferences, session state, or long-term memory belongs to one tenant, then users from another tenant cannot access it.
- Given infrastructure resources are shared across tenants, then the system applies tenant-scoped controls that prevent cross-tenant access.
- Given tenant context is missing or cannot be validated, then tenant-scoped operations fail closed.

## Tenant Isolation Open Questions

- Which resources are tenant-specific versus shared multi-tenant resources?
- What tenant isolation verification evidence is required before implementation is accepted?

## Assumptions

- `tenant_id` is available to the authorization flow after authentication through a Cognito-issued custom claim.
- Amazon Cognito User Pool authentication establishes user identity, but Amazon Verified Permissions remains the source of truth for action authorization.
- UI enablement is treated as a usability control. Backend authorization enforcement is specified separately by `REQ-AUTH-003`.

## Open Questions

- Should unauthorized UI features be hidden, disabled, or shown with an access-request path?
- Which additional UI, API, membership, and user-management operations belong in the initial action catalog?
- What is the required behavior when Amazon Verified Permissions is unavailable?

## Angular UI Delivery

### REQ-UI-001: Deliver Tenant Angular UI Through CloudFront

When the Angular UI Web Application is deployed for tenant access, the system shall host the Angular UI as an Amazon S3 Static Website and deliver the Angular UI through an Amazon CloudFront Distribution whose origin is the S3 Static Website endpoint URI for that application.

**Acceptance Criteria**

- Given the Angular UI Web Application is deployed for tenant access, when an end user accesses the Angular UI, then the Angular UI is delivered through an Amazon CloudFront Distribution.
- Given the CloudFront Distribution delivers the Angular UI Web Application, then its origin is configured as the S3 Static Website endpoint URI for that application.
- Given the Angular UI Web Application is deployed for tenant access, then the application static assets are hosted as an Amazon S3 Static Website.
- Given an end user accesses the Angular UI Web Application, then the system does not require the end user to access the S3 Static Website endpoint URI directly.

### REQ-UI-002: Configure CloudFront HTTPS Certificate

When the Amazon CloudFront Distribution is provisioned, the system shall configure it with a TLS/SSL certificate issued by Amazon Certificate Manager to enable secure HTTPS connections.

**Acceptance Criteria**

- Given the Amazon CloudFront Distribution is provisioned, then the distribution is configured with a TLS/SSL certificate issued by Amazon Certificate Manager.
- Given an end user accesses the Angular UI through the Amazon CloudFront Distribution over HTTPS, then the distribution uses the configured Amazon Certificate Manager certificate for TLS/SSL.
- Given the Amazon CloudFront Distribution is provisioned for tenant access, then the certificate configuration supports secure HTTPS connections for the distribution's intended domain name.

### REQ-UI-003: Display Angular Layout Top Navigation Bar

When the Angular UI layout is rendered, the system shall display a top navigation bar containing a tenant name field, a Profile link, a Logout button, and a Help link.

**Acceptance Criteria**

- Given the Angular UI layout is rendered for an authenticated user, then the top navigation bar is visible.
- Given the top navigation bar is visible, then it contains a tenant name field.
- Given the top navigation bar is visible, then it contains a Profile link.
- Given the top navigation bar is visible, then it contains a Logout button.
- Given the top navigation bar is visible, then it contains a Help link.

### REQ-UI-004: Display Authorized Action Menu

When the Angular UI layout is rendered, the system shall display a center panel with a left-side menu list of action links authorized for the current user by the applicable Amazon Verified Permissions Cedar policy.

**Acceptance Criteria**

- Given the Angular UI layout is rendered for an authenticated user, then the center panel is visible.
- Given the center panel is visible, then it contains a left-side menu list.
- Given Amazon Verified Permissions Cedar policy authorizes actions for the current user, then the left-side menu list displays action links for the authorized actions.
- Given action authorization is evaluated, then the menu list reflects the current user's tenant-scoped authorization context.

### REQ-UI-005: Omit Unauthorized Menu Actions

If an action is not permitted for the current user by the Amazon Verified Permissions Cedar policy, then the system shall omit that action from the left-side menu list.

**Acceptance Criteria**

- Given an action is not permitted for the current user by the applicable Cedar policy, then the left-side menu list does not render a link for that action.
- Given an action authorization decision cannot be retrieved or evaluated, then the left-side menu list does not render a link for that action by default.
- Given menu actions are rendered, then unauthorized actions are not present in the DOM as selectable left-side menu links.

### REQ-UI-006: Replace Center Panel Content on Menu Action

When a user clicks a left-side menu action link, the system shall replace the current center panel action view with the selected action view.

**Acceptance Criteria**

- Given a permitted left-side menu action link is visible, when the user clicks the action link, then the center panel displays the selected action view.
- Given the selected action view is displayed, then the prior action view is no longer the active center panel view.
- Given the user selects a different permitted action link, then the center panel transitions to the newly selected action view.

### REQ-UI-007: Preserve Top Navigation During Action Transitions

While the user remains on the same page session, the system shall keep the top navigation bar visible and unchanged during center panel transitions between action views.

**Acceptance Criteria**

- Given the user remains in the same page session, when the center panel transitions between action views, then the top navigation bar remains visible.
- Given the center panel transitions between action views, then the tenant name field, Profile link, Logout button, and Help link remain present in the top navigation bar.
- Given the center panel transitions between action views, then the top navigation bar is not re-rendered in a way that changes its visible content.

## Global Assumptions

- Each deployed Angular UI Web Application has a corresponding S3 Static Website endpoint URI.

## Global Open Questions

- Should direct end-user access to the S3 Static Website endpoint URI be blocked, redirected, or treated as out of scope?
- Should tenant-specific Angular UI deployments use separate CloudFront distributions, separate origins, separate S3 buckets, or a shared distribution with tenant-aware routing?
- Should CloudFront redirect HTTP viewer requests to HTTPS, reject HTTP requests, or support both HTTP and HTTPS?
- Does the requirement intentionally prefer the S3 Static Website endpoint over the S3 REST endpoint with CloudFront Origin Access Control?
- Which end-user HTTP/HTTPS requests should be routed through Amazon API Gateway instead of the Angular UI CloudFront Distribution?
- Are Amazon Certificate Manager certificates tenant-specific, application-specific, or shared across tenant domains?
- What source provides the tenant display name shown in the Angular top navigation bar?
- Should Profile, Logout, and Help be fixed global actions or also governed by authorization policy?

## Backend REST Routing

### REQ-API-001: Configure API Gateway Cognito Authorizer

When an Amazon API Gateway endpoint is provisioned, the system shall configure the endpoint to use an Amazon Cognito Authorizer.

**Acceptance Criteria**

- Given an Amazon API Gateway REST endpoint is provisioned, then the endpoint is configured to use an Amazon Cognito Authorizer.
- Given an Amazon API Gateway WebSocket endpoint is provisioned where Cognito authorization is supported by the selected API Gateway configuration, then the endpoint is configured to use an Amazon Cognito Authorizer.
- Given a backend request is made to a provisioned Amazon API Gateway endpoint, when the request does not satisfy the configured Amazon Cognito Authorizer, then Amazon API Gateway does not authorize the request.
- Given a backend request is made to a provisioned Amazon API Gateway endpoint, when the request satisfies the configured Amazon Cognito Authorizer, then Amazon API Gateway authorizes the request for downstream processing.

### REQ-API-002: Integrate API Gateway with Lambda Backend

When an Amazon API Gateway endpoint is provisioned, the system shall integrate the backend with Python-based and/or TypeScript-based Amazon Lambda functions for request processing and business logic execution.

**Acceptance Criteria**

- Given an Amazon API Gateway endpoint is provisioned, then the endpoint is integrated with one or more Amazon Lambda functions.
- Given an Amazon Lambda function is integrated with the Amazon API Gateway endpoint, then the Lambda function uses a Python or TypeScript runtime.
- Given an authorized request is received by the Amazon API Gateway endpoint, when the request is routed for backend processing, then the integrated Python-based or TypeScript-based Amazon Lambda function processes the request.
- Given backend business logic is required for an API operation, then the business logic executes in a Python-based or TypeScript-based Amazon Lambda function.

### REQ-API-003: Integrate WebSocket Backend with Bedrock Agent Memory

When an Amazon API Gateway WebSocket endpoint is provisioned, the system shall integrate the endpoint with Python-based and/or TypeScript-based Amazon Lambda functions and a simple Amazon Bedrock Agent that uses an Amazon Bedrock Knowledge Base backed by an Amazon S3 vector index for long-term memory.

**Acceptance Criteria**

- Given an Amazon API Gateway WebSocket endpoint is provisioned, then the endpoint is integrated with one or more Python-based and/or TypeScript-based Amazon Lambda functions.
- Given a Python-based or TypeScript-based Amazon Lambda function handles WebSocket backend processing, then the function integrates with a simple Amazon Bedrock Agent.
- Given the Amazon Bedrock Agent requires long-term memory, then the agent uses an Amazon Bedrock Knowledge Base.
- Given the Amazon Bedrock Knowledge Base stores or retrieves long-term memory, then the knowledge base is backed by an Amazon S3 vector index.
- Given a WebSocket request requires agentic processing, when the backend invokes the Amazon Bedrock Agent, then the agent can use the Amazon Bedrock Knowledge Base for long-term memory retrieval.

### REQ-API-004: Support Optional Durable REST Session Preferences

Where durable user session state or user preference persistence is included, the system shall persist session and preference data using tenant-scoped storage.

**Acceptance Criteria**

- Given durable user session state persistence is included, then persisted session records are associated with user and tenant context.
- Given durable user preference persistence is included, then persisted preference records are associated with user and tenant context.
- Given durable session or preference persistence is not included, then the system shall not require a DynamoDB table for REST backend operation.
- Given DynamoDB is later selected for durable session or preference persistence, then the DynamoDB table design shall preserve tenant isolation.

## Backend REST Routing Open Questions

- Which Cognito User Pool, app client, token type, scopes, and claims are used by the Amazon Cognito Authorizer?
- How should Amazon Cognito Authorizer authentication combine with Amazon Verified Permissions action authorization?
- Should API Gateway authorization differ for REST endpoints and WebSocket endpoints?
- Should API Gateway use Lambda proxy integration or non-proxy integration for Python-based and/or TypeScript-based Amazon Lambda functions?
- Are Lambda functions organized per API route, per bounded context, per tenant, or shared across tenants?
- What behavior qualifies the Amazon Bedrock Agent as "simple"?
- What tenant isolation, retention, and deletion rules apply to long-term memory in the Amazon Bedrock Knowledge Base and Amazon S3 vector index?
- Which WebSocket routes require agentic processing versus non-agentic Lambda processing?
- When should durable user session state and user preference persistence be introduced?
- If DynamoDB is selected later, what key schema, indexes, TTL, retention policy, and tenant isolation model should support user session and preferences management?
- If DynamoDB is selected later, are user session and preferences stored in a shared multi-tenant table or tenant-specific tables?

## DNS Routing

### REQ-DNS-001: Create CloudFront DNS Record

When the Amazon CloudFront Distribution is provisioned, the system shall create an Amazon Route 53 Alias or CNAME DNS record that routes to the Amazon CloudFront Distribution URI.

**Acceptance Criteria**

- Given the Amazon CloudFront Distribution is provisioned, then an Amazon Route 53 Alias or CNAME DNS record exists for the distribution.
- Given the Amazon Route 53 DNS record for the Angular UI is resolved, then it routes to the Amazon CloudFront Distribution URI.
- Given the Amazon CloudFront Distribution URI changes during replacement or reprovisioning, then the Amazon Route 53 DNS record is updated to route to the current distribution URI.

### REQ-DNS-002: Create Cognito Authorization Endpoint DNS Record

When Amazon Cognito is provisioned, the system shall create an Amazon Route 53 Alias or CNAME DNS record that routes to the Cognito authorization endpoint.

**Acceptance Criteria**

- Given Amazon Cognito is provisioned for authentication, then an Amazon Route 53 Alias or CNAME DNS record exists for the Cognito authorization endpoint.
- Given the Amazon Route 53 DNS record for Cognito authorization is resolved, then it routes to the Cognito authorization endpoint.
- Given the Cognito authorization endpoint changes during replacement or reprovisioning, then the Amazon Route 53 DNS record is updated to route to the current Cognito authorization endpoint.

### REQ-DNS-003: Create API Gateway Endpoint DNS Record

When an Amazon API Gateway REST or WebSocket endpoint is provisioned, the system shall create an Amazon Route 53 Alias or CNAME DNS record that routes to the Amazon API Gateway URI.

**Acceptance Criteria**

- Given an Amazon API Gateway REST endpoint is provisioned, then an Amazon Route 53 Alias or CNAME DNS record exists that routes to the Amazon API Gateway URI.
- Given an Amazon API Gateway WebSocket endpoint is provisioned, then an Amazon Route 53 Alias or CNAME DNS record exists that routes to the Amazon API Gateway URI.
- Given the Amazon Route 53 DNS record for an API endpoint is resolved, then it routes to the intended Amazon API Gateway URI.
- Given an Amazon API Gateway URI changes during replacement or reprovisioning, then the Amazon Route 53 DNS record is updated to route to the current Amazon API Gateway URI.

## DNS Routing Open Questions

- What hosted zones and domain naming conventions should be used for tenant, application, Cognito authorization, REST API, and WebSocket API records?
- Should Route 53 records be tenant-specific, environment-specific, application-specific, or shared?
- Does the Cognito authorization endpoint use a Cognito-managed domain or a custom domain backed by Amazon CloudFront?
- Should DNS records be created only for public endpoints, or are private/internal endpoints also in scope?

## Infrastructure as Code

### REQ-IAC-001: Manage AWS Resources with Terraform

When AWS infrastructure resources are provisioned for the system, the system shall manage those resources using Terraform infrastructure-as-code definitions.

**Acceptance Criteria**

- Given an AWS infrastructure resource is required by the architecture, then the resource is defined in version-controlled Terraform unless an exception is documented.
- Given Terraform provisions or updates an AWS infrastructure resource, then Terraform state tracks the managed resource lifecycle.
- Given an AWS infrastructure resource cannot be managed by Terraform, then the exception is documented with the reason, operational owner, and lifecycle process.
- Given infrastructure changes are proposed, then the changes are reviewed using Terraform plan output before apply.
- Given tenant-specific infrastructure resources are provisioned, then the Terraform design preserves the tenant isolation requirements defined by `REQ-TENANT-001`.

## Infrastructure as Code Open Questions

- What Terraform backend, state locking, workspace, and environment strategy should be used?
- Which AWS resources are shared platform resources versus tenant-specific resources?
- Which bootstrap resources, if any, are created before Terraform state exists?
- Which AWS resources or runtime configurations are explicitly excluded from Terraform lifecycle management?
