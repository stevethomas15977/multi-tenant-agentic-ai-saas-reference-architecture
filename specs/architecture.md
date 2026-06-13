# Architecture

This file captures architecture context and decisions for the Multi-tenant Agentic AI SaaS Reference Architecture.

## Authentication and Authorization Context

The system uses Amazon Cognito User Pools for user authentication and Amazon Verified Permissions for action authorization.

Authentication identifies the user. Authorization determines which tenant-scoped actions the authenticated user may perform.

The Angular UI Web Application uses Amazon Cognito authentication to obtain tokens for Angular UI identity and Amazon API Gateway access.

## Tenant Boundary

Authorization decisions must include tenant context. The initial requirements identify `tenant_id` as the tenant-scoping value used to determine which UI features or actions are enabled.

Tenant isolation applies across identity, authorization, API access, data storage, DNS/resource scoping, and long-term memory.

## Angular UI Delivery Context

The Angular UI Web Application is deployed for tenant access as static website content hosted by Amazon S3.

Angular UI access is delivered through an Amazon CloudFront Distribution. The distribution uses the S3 Static Website endpoint URI for that Angular application as its origin.

When the Amazon CloudFront Distribution is provisioned, it uses a TLS/SSL certificate issued by Amazon Certificate Manager to enable secure HTTPS connections for Angular UI access.

## Backend REST Routing Context

When Amazon API Gateway endpoints are provisioned, they use an Amazon Cognito Authorizer to authorize backend requests before downstream processing.

Provisioned Amazon API Gateway endpoints integrate with Python-based and/or TypeScript-based Amazon Lambda functions for request processing and business logic execution.

Provisioned Amazon API Gateway WebSocket endpoints integrate with Python-based and/or TypeScript-based Amazon Lambda functions and a simple Amazon Bedrock Agent. The Amazon Bedrock Agent uses an Amazon Bedrock Knowledge Base backed by an Amazon S3 vector index for long-term memory.

Provisioned Amazon API Gateway REST endpoints integrate with Python-based and/or TypeScript-based Amazon Lambda functions and an Amazon DynamoDB table for user session and preferences management.

## DNS Routing Context

Amazon Route 53 provides Alias or CNAME DNS records for provisioned service endpoints. DNS records route to the Amazon CloudFront Distribution URI, the Cognito authorization endpoint, and Amazon API Gateway REST or WebSocket endpoint URIs.

## Infrastructure as Code Context

Terraform manages the lifecycle of AWS infrastructure resources required by the architecture. Terraform state is the source of managed infrastructure resource ownership and drift detection.

Terraform-managed resources include stable AWS platform resources such as Amazon S3, Amazon CloudFront, Amazon Route 53, Amazon Certificate Manager, Amazon Cognito, Amazon Verified Permissions, Amazon API Gateway, Amazon Lambda, Amazon DynamoDB, Amazon Bedrock Agent and Knowledge Base resources where provider support is sufficient, IAM, KMS, CloudWatch, and AWS WAF when used.

High-churn runtime data, user records, tenant memberships, session records, preference records, knowledge base documents, embeddings, and operational authorization grants are not Terraform-managed unless they are explicitly classified as bootstrap/reference configuration.

## Architecture Notes

- Amazon Route 53 is responsible for DNS alias routing to provisioned AWS service endpoints.
- Terraform is responsible for AWS infrastructure resource lifecycle management unless an exception is documented.
- Amazon Cognito User Pools are responsible for authenticating users.
- Amazon Verified Permissions is responsible for evaluating action authorization.
- UI feature/action enablement depends on authorization decisions scoped by `tenant_id`.
- Backend operations that require tenant-scoped action authorization must enforce Amazon Verified Permissions decisions.
- Tenant isolation applies to identity, authorization, API access, data storage, DNS/resource scoping, and long-term memory.
- Amazon CloudFront is the end-user entry point for the Angular UI Web Application.
- Amazon S3 Static Website hosting is the origin hosting model for Angular UI static assets.
- Amazon Certificate Manager issues the TLS/SSL certificate configured on the Amazon CloudFront Distribution.
- Amazon API Gateway routing for non-UI HTTP/HTTPS requests is outside REQ-UI-001 and should be specified separately.
- Amazon API Gateway is the entry point for backend REST and WebSocket requests.
- Amazon Cognito Authorizers are responsible for authorizing requests received by provisioned Amazon API Gateway endpoints.
- Python-based and/or TypeScript-based Amazon Lambda functions process backend requests and execute business logic behind Amazon API Gateway.
- Amazon DynamoDB stores user session and preferences data for REST backend operations.
- Amazon Bedrock Agents provide agentic processing for WebSocket backend interactions.
- Amazon Bedrock Knowledge Bases backed by Amazon S3 vector indexes provide long-term memory for agentic WebSocket interactions.

## Open Decisions

- Source and validation rules for `tenant_id`.
- Tenant-specific versus shared resource strategy.
- Policy model and action taxonomy for Amazon Verified Permissions.
- Caching strategy and failure behavior for authorization decisions.
- Relationship between UI enablement and backend/API authorization enforcement.
- Whether direct S3 Static Website endpoint access is allowed, blocked, or redirected.
- Whether tenant access uses one CloudFront Distribution per tenant, one distribution per application, or a shared distribution with tenant-aware routing.
- Whether CloudFront should redirect HTTP viewer requests to HTTPS, reject HTTP requests, or support both HTTP and HTTPS.
- Whether certificates are tenant-specific, application-specific, or shared across tenant domains.
- Cognito User Pool, app client, token type, scopes, and claims used by API Gateway Cognito Authorizers.
- Relationship between Amazon Cognito Authorizer authentication and Amazon Verified Permissions action authorization.
- API Gateway Lambda integration style and Lambda function organization strategy.
- Scope of "simple" Amazon Bedrock Agent behavior.
- Tenant isolation, retention, deletion, and retrieval policy for long-term memory.
- WebSocket route mapping between Lambda-only processing and Bedrock Agent processing.
- DynamoDB key schema, indexing, TTL, retention, and tenant isolation model for user sessions and preferences.
- Hosted zone and domain naming strategy for Route 53 DNS records.
- Whether Route 53 DNS records are tenant-specific, environment-specific, application-specific, or shared.
- Whether the Cognito authorization endpoint uses a Cognito-managed domain or a custom domain.
- Terraform backend, state locking, workspace, and environment strategy.
- Terraform module boundaries for shared platform resources versus tenant-specific resources.
- Bootstrap resources required before Terraform state exists.
- AWS resource lifecycle exceptions that are not Terraform-managed.
