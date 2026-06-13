# Requirements

This file is the canonical source for system requirements. Requirements use EARS-style wording and stable identifiers.

## Authentication and Authorization

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

## Assumptions

- `tenant_id` is available to the authorization flow after authentication.
- Amazon Cognito User Pool authentication establishes user identity, but Amazon Verified Permissions remains the source of truth for action authorization.
- UI enablement is treated as a usability control. Server-side enforcement requirements still need to be specified separately.

## Open Questions

- Where does `tenant_id` originate: Cognito token claims, application tenant membership lookup, request context, or another source?
- Should unauthorized UI features be hidden, disabled, or shown with an access-request path?
- What action model should Amazon Verified Permissions use: coarse UI feature flags, domain actions, API operations, or a combination?
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

## Global Assumptions

- Each deployed Angular UI Web Application has a corresponding S3 Static Website endpoint URI.

## Global Open Questions

- Should direct end-user access to the S3 Static Website endpoint URI be blocked, redirected, or treated as out of scope?
- Should tenant-specific Angular UI deployments use separate CloudFront distributions, separate origins, separate S3 buckets, or a shared distribution with tenant-aware routing?
- Should CloudFront redirect HTTP viewer requests to HTTPS, reject HTTP requests, or support both HTTP and HTTPS?
- Does the requirement intentionally prefer the S3 Static Website endpoint over the S3 REST endpoint with CloudFront Origin Access Control?
- Which end-user HTTP/HTTPS requests should be routed through Amazon API Gateway instead of the Angular UI CloudFront Distribution?
- Are Amazon Certificate Manager certificates tenant-specific, application-specific, or shared across tenant domains?

## Backend REST Routing

### REQ-API-001: Configure API Gateway Cognito Authorizer

When an Amazon API Gateway endpoint is provisioned, the system shall configure the endpoint to use an Amazon Cognito Authorizer.

**Acceptance Criteria**

- Given an Amazon API Gateway REST endpoint is provisioned, then the endpoint is configured to use an Amazon Cognito Authorizer.
- Given an Amazon API Gateway WebSocket endpoint is provisioned where Cognito authorization is supported by the selected API Gateway configuration, then the endpoint is configured to use an Amazon Cognito Authorizer.
- Given a backend request is made to a provisioned Amazon API Gateway endpoint, when the request does not satisfy the configured Amazon Cognito Authorizer, then Amazon API Gateway does not authorize the request.
- Given a backend request is made to a provisioned Amazon API Gateway endpoint, when the request satisfies the configured Amazon Cognito Authorizer, then Amazon API Gateway authorizes the request for downstream processing.

### REQ-API-002: Integrate API Gateway with Python Lambda Backend

When an Amazon API Gateway endpoint is provisioned, the system shall integrate the backend with Python-based Amazon Lambda functions for request processing and business logic execution.

**Acceptance Criteria**

- Given an Amazon API Gateway endpoint is provisioned, then the endpoint is integrated with one or more Amazon Lambda functions.
- Given an Amazon Lambda function is integrated with the Amazon API Gateway endpoint, then the Lambda function uses a Python runtime.
- Given an authorized request is received by the Amazon API Gateway endpoint, when the request is routed for backend processing, then the integrated Python-based Amazon Lambda function processes the request.
- Given backend business logic is required for an API operation, then the business logic executes in a Python-based Amazon Lambda function.

### REQ-API-003: Integrate WebSocket Backend with Bedrock Agent Memory

When an Amazon API Gateway WebSocket endpoint is provisioned, the system shall integrate the endpoint with Python-based Amazon Lambda functions and a simple Amazon Bedrock Agent that uses an Amazon Bedrock Knowledge Base backed by an Amazon S3 vector index for long-term memory.

**Acceptance Criteria**

- Given an Amazon API Gateway WebSocket endpoint is provisioned, then the endpoint is integrated with one or more Python-based Amazon Lambda functions.
- Given a Python-based Amazon Lambda function handles WebSocket backend processing, then the function integrates with a simple Amazon Bedrock Agent.
- Given the Amazon Bedrock Agent requires long-term memory, then the agent uses an Amazon Bedrock Knowledge Base.
- Given the Amazon Bedrock Knowledge Base stores or retrieves long-term memory, then the knowledge base is backed by an Amazon S3 vector index.
- Given a WebSocket request requires agentic processing, when the backend invokes the Amazon Bedrock Agent, then the agent can use the Amazon Bedrock Knowledge Base for long-term memory retrieval.

## Backend REST Routing Open Questions

- Which Cognito User Pool, app client, token type, scopes, and claims are used by the Amazon Cognito Authorizer?
- How should Amazon Cognito Authorizer authentication combine with Amazon Verified Permissions action authorization?
- Should API Gateway authorization differ for REST endpoints and WebSocket endpoints?
- Should API Gateway use Lambda proxy integration or non-proxy integration for Python-based Amazon Lambda functions?
- Are Lambda functions organized per API route, per bounded context, per tenant, or shared across tenants?
- What behavior qualifies the Amazon Bedrock Agent as "simple"?
- What tenant isolation, retention, and deletion rules apply to long-term memory in the Amazon Bedrock Knowledge Base and Amazon S3 vector index?
- Which WebSocket routes require agentic processing versus non-agentic Lambda processing?

## DNS Routing

### REQ-DNS-001: Create CloudFront Alias Record

When the Amazon CloudFront Distribution is provisioned, the system shall create an Amazon Route 53 Alias DNS record that routes to the Amazon CloudFront Distribution URI.

**Acceptance Criteria**

- Given the Amazon CloudFront Distribution is provisioned, then an Amazon Route 53 Alias DNS record exists for the distribution.
- Given the Amazon Route 53 Alias DNS record for the Angular UI is resolved, then it routes to the Amazon CloudFront Distribution URI.
- Given the Amazon CloudFront Distribution URI changes during replacement or reprovisioning, then the Amazon Route 53 Alias DNS record is updated to route to the current distribution URI.

### REQ-DNS-002: Create Cognito Authorization Endpoint Alias Record

When Amazon Cognito is provisioned, the system shall create an Amazon Route 53 Alias DNS record that routes to the Cognito authorization endpoint.

**Acceptance Criteria**

- Given Amazon Cognito is provisioned for authentication, then an Amazon Route 53 Alias DNS record exists for the Cognito authorization endpoint.
- Given the Amazon Route 53 Alias DNS record for Cognito authorization is resolved, then it routes to the Cognito authorization endpoint.
- Given the Cognito authorization endpoint changes during replacement or reprovisioning, then the Amazon Route 53 Alias DNS record is updated to route to the current Cognito authorization endpoint.

### REQ-DNS-003: Create API Gateway Endpoint Alias Record

When an Amazon API Gateway REST or WebSocket endpoint is provisioned, the system shall create an Amazon Route 53 Alias DNS record that routes to the Amazon API Gateway URI.

**Acceptance Criteria**

- Given an Amazon API Gateway REST endpoint is provisioned, then an Amazon Route 53 Alias DNS record exists that routes to the Amazon API Gateway URI.
- Given an Amazon API Gateway WebSocket endpoint is provisioned, then an Amazon Route 53 Alias DNS record exists that routes to the Amazon API Gateway URI.
- Given the Amazon Route 53 Alias DNS record for an API endpoint is resolved, then it routes to the intended Amazon API Gateway URI.
- Given an Amazon API Gateway URI changes during replacement or reprovisioning, then the Amazon Route 53 Alias DNS record is updated to route to the current Amazon API Gateway URI.

## DNS Routing Open Questions

- What hosted zones and domain naming conventions should be used for tenant, application, Cognito authorization, REST API, and WebSocket API records?
- Should Route 53 records be tenant-specific, environment-specific, application-specific, or shared?
- Does the Cognito authorization endpoint use a Cognito-managed domain or a custom domain backed by Amazon CloudFront?
- Should DNS records be created only for public endpoints, or are private/internal endpoints also in scope?
