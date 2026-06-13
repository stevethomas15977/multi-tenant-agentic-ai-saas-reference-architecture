# Design

This file describes intended behavior and design details derived from the requirements.

## Design Standards

Every design update shall include the following architecture views when the change affects system structure, boundaries, integrations, or deployment responsibilities:

- Context Diagram
- Container Diagram

Use the Draw.io MCP service to generate diagrams.

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

This design scope applies to Angular UI delivery only. Amazon API Gateway routing for API or service HTTP/HTTPS requests should be specified separately.

Editable Draw.io architecture views for this design are stored in `/architecture`:

- Context Diagram: `/architecture/context-diagram.drawio`
- Container Diagram: `/architecture/container-diagram.drawio`

## Backend REST Routing

When the Angular UI Web Application issues a backend REST request, the application uses only Amazon API Gateway REST endpoints authorized for the authenticated user and tenant context.

Endpoint authorization should align with the action authorization model used for tenant-scoped UI features. The precise mapping between UI action, backend REST route, HTTP method, and Amazon Verified Permissions action remains an open design decision.

Unauthorized endpoints should fail closed: the Angular UI should not route to an endpoint that is missing from the authorized endpoint set, and server-side enforcement should still reject unauthorized requests if a client bypasses the UI.

## Fail-Closed UI Behavior

Until authorization is successfully retrieved and evaluated, tenant-scoped UI features and actions should not be enabled by default.

This behavior prevents optimistic UI access when authorization state is missing, stale, or unavailable.

## Design Notes

- UI enablement should not be treated as the only authorization control.
- Backend/API enforcement requirements should be added so authorization is enforced even if a user bypasses the UI.
- Authorization checks should use a stable action naming scheme that can be mapped to both UI features and protected operations.
