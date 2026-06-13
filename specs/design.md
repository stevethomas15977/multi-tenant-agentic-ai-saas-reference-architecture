# Design

This file describes intended behavior and design details derived from the requirements.

## Design Standards

Every design update shall include the following architecture views when the change affects system structure, boundaries, integrations, or deployment responsibilities:

- Context Diagram
- Container Diagram

Use the Draw.io MCP service to generate diagrams.

Use the AWS 2026 (Open Library) within Draw.io for each specified AWS service when a corresponding service shape exists.

Store architecture diagram artifacts under `/architecture`.

## Authentication and Authorization Flow

1. The user accesses protected Angular UI functionality.
2. The system authenticates the user through the configured Amazon Cognito User Pool.
3. The Angular UI receives Cognito-issued tokens required for UI identity and API Gateway access.
4. After successful authentication, the system resolves the user's tenant context, including `tenant_id`.
5. The system requests action authorization from Amazon Verified Permissions.
6. The UI authorization layer receives the resulting action permissions.
7. The UI enables only the tenant-scoped features or actions authorized for the authenticated user.

Backend operations that require tenant-scoped action authorization must enforce Amazon Verified Permissions decisions before completing the operation.

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

## Backend REST Routing

Provisioned Amazon API Gateway endpoints use an Amazon Cognito Authorizer to authorize backend requests before downstream processing.

The Amazon Cognito Authorizer validates the caller's Cognito-authenticated identity at the API Gateway boundary. Amazon Verified Permissions remains the action authorization source for tenant-scoped feature and action decisions unless a later requirement changes that responsibility.

Unauthorized API Gateway requests should fail closed: if a request does not satisfy the configured Amazon Cognito Authorizer, API Gateway should not authorize the request for downstream processing.

After API Gateway authorizes a backend request, the request is processed by one or more Python-based and/or TypeScript-based Amazon Lambda functions. These Lambda functions contain backend request processing and business logic execution responsibilities.

The API Gateway to Lambda integration style, function granularity, and tenant isolation model remain open design decisions.

For Amazon API Gateway WebSocket endpoints, Python-based and/or TypeScript-based Amazon Lambda functions integrate with a simple Amazon Bedrock Agent when agentic processing is required. The Bedrock Agent uses an Amazon Bedrock Knowledge Base for long-term memory.

The Amazon Bedrock Knowledge Base is backed by an Amazon S3 vector index. Long-term memory access must preserve tenant boundaries and should use explicit retention, deletion, and retrieval policies before implementation.

For Amazon API Gateway REST endpoints, Python-based and/or TypeScript-based Amazon Lambda functions use an Amazon DynamoDB table to manage user session and preferences data.

Session and preferences data must be associated with user and tenant context. DynamoDB key design, indexing, TTL, retention, and tenant isolation remain open design decisions.

## DNS Routing

Amazon Route 53 Alias or CNAME DNS records are created when externally addressable AWS service endpoints are provisioned.

The CloudFront DNS record routes Angular UI access to the Amazon CloudFront Distribution URI. The Cognito authorization DNS record routes authentication authorization traffic to the Cognito authorization endpoint. API Gateway DNS records route REST and WebSocket API traffic to the intended Amazon API Gateway URI.

Route 53 DNS records should be updated when the underlying service endpoint URI changes during replacement or reprovisioning.

## Terraform Infrastructure as Code

Terraform is the infrastructure lifecycle mechanism for AWS resources required by this architecture.

Terraform should manage stable AWS infrastructure resources, including Amazon S3 buckets, Amazon CloudFront distributions, Amazon Route 53 records, Amazon Certificate Manager certificates and DNS validation records, Amazon Cognito User Pools and app clients, Amazon Verified Permissions policy stores and stable policy templates, Amazon API Gateway REST and WebSocket APIs, Amazon Lambda functions and execution roles, Amazon DynamoDB tables, Amazon Bedrock Agent and Knowledge Base resources where provider support is sufficient, IAM roles and policies, KMS keys, CloudWatch log groups and alarms, and AWS WAF resources when used.

Terraform should not manage high-churn runtime data such as Cognito users, tenant runtime memberships, DynamoDB session or preference records, Bedrock Knowledge Base documents or embeddings, or tenant authorization grants that change during normal application operation unless they are explicitly defined as bootstrap/reference configuration.

Terraform module boundaries should reflect tenant isolation, environment separation, and lifecycle ownership. The Terraform design should document the backend, state locking, environment strategy, shared versus tenant-specific resources, and any resource exceptions.

## Fail-Closed UI Behavior

Until authorization is successfully retrieved and evaluated, tenant-scoped UI features and actions should not be enabled by default.

This behavior prevents optimistic UI access when authorization state is missing, stale, or unavailable.

## Design Notes

- UI enablement should not be treated as the only authorization control.
- Backend/API enforcement starts at API Gateway through Amazon Cognito Authorizers and may be extended with downstream action authorization requirements in Python-based and/or TypeScript-based Amazon Lambda functions, DynamoDB-backed state operations, and Bedrock agent workflows.
- Authorization checks should use a stable action naming scheme that can be mapped to both UI features and protected operations.
