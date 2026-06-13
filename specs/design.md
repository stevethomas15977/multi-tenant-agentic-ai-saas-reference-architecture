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

1. The user authenticates through the configured Amazon Cognito User Pool.
2. After successful authentication, the system resolves the user's tenant context, including `tenant_id`.
3. The system requests action authorization from Amazon Verified Permissions.
4. The UI authorization layer receives the resulting action permissions.
5. The UI enables only the tenant-scoped features or actions authorized for the authenticated user.

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

## DNS Routing

Amazon Route 53 Alias DNS records are created when externally addressable AWS service endpoints are provisioned.

The CloudFront Alias record routes Angular UI access to the Amazon CloudFront Distribution URI. The Cognito authorization Alias record routes authentication authorization traffic to the Cognito authorization endpoint. API Gateway Alias records route REST and WebSocket API traffic to the intended Amazon API Gateway URI.

Route 53 Alias records should be updated when the underlying service endpoint URI changes during replacement or reprovisioning.

## Fail-Closed UI Behavior

Until authorization is successfully retrieved and evaluated, tenant-scoped UI features and actions should not be enabled by default.

This behavior prevents optimistic UI access when authorization state is missing, stale, or unavailable.

## Design Notes

- UI enablement should not be treated as the only authorization control.
- Backend/API enforcement starts at API Gateway through Amazon Cognito Authorizers and may be extended with downstream action authorization requirements.
- Authorization checks should use a stable action naming scheme that can be mapped to both UI features and protected operations.
