# Architecture

This file captures architecture context and decisions for the Multi-tenant Agentic AI SaaS Reference Architecture.

## Authentication and Authorization Context

The system uses Amazon Cognito User Pools for user authentication and Amazon Verified Permissions for action authorization.

Authentication identifies the user. Authorization determines which tenant-scoped actions the authenticated user may perform.

## Tenant Boundary

Authorization decisions must include tenant context. The initial requirements identify `tenant_id` as the tenant-scoping value used to determine which UI features or actions are enabled.

## Architecture Notes

- Amazon Cognito User Pools are responsible for authenticating users.
- Amazon Verified Permissions is responsible for evaluating action authorization.
- UI feature/action enablement depends on authorization decisions scoped by `tenant_id`.
- Server-side authorization enforcement is not yet specified and should be added before implementation begins.

## Open Decisions

- Source and validation rules for `tenant_id`.
- Policy model and action taxonomy for Amazon Verified Permissions.
- Caching strategy and failure behavior for authorization decisions.
- Relationship between UI enablement and backend/API authorization enforcement.

