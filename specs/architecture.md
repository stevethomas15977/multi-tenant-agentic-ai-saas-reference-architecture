# Architecture

This file captures architecture context and decisions for the Multi-tenant Agentic AI SaaS Reference Architecture.

## Authentication and Authorization Context

The system uses Amazon Cognito User Pools for user authentication and Amazon Verified Permissions for action authorization.

Authentication identifies the user. Authorization determines which tenant-scoped actions the authenticated user may perform.

## Tenant Boundary

Authorization decisions must include tenant context. The initial requirements identify `tenant_id` as the tenant-scoping value used to determine which UI features or actions are enabled.

## Angular UI Delivery Context

The Angular UI Web Application is deployed for tenant access as static website content hosted by Amazon S3.

All end-user HTTP/HTTPS requests for the Angular UI route through an Amazon CloudFront Distribution. The distribution uses the S3 Static Website endpoint URI for that Angular application as its origin.

## Architecture Notes

- Amazon Cognito User Pools are responsible for authenticating users.
- Amazon Verified Permissions is responsible for evaluating action authorization.
- UI feature/action enablement depends on authorization decisions scoped by `tenant_id`.
- Server-side authorization enforcement is not yet specified and should be added before implementation begins.
- Amazon CloudFront is the end-user entry point for the Angular UI Web Application.
- Amazon S3 Static Website hosting is the origin hosting model for Angular UI static assets.

## Open Decisions

- Source and validation rules for `tenant_id`.
- Policy model and action taxonomy for Amazon Verified Permissions.
- Caching strategy and failure behavior for authorization decisions.
- Relationship between UI enablement and backend/API authorization enforcement.
- Whether direct S3 Static Website endpoint access is allowed, blocked, or redirected.
- Whether tenant access uses one CloudFront Distribution per tenant, one distribution per application, or a shared distribution with tenant-aware routing.
- Whether HTTPS-only viewer protocol policy is required.
