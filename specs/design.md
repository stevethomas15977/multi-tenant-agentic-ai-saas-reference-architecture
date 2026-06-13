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

## Fail-Closed UI Behavior

Until authorization is successfully retrieved and evaluated, tenant-scoped UI features and actions should not be enabled by default.

This behavior prevents optimistic UI access when authorization state is missing, stale, or unavailable.

## Design Notes

- UI enablement should not be treated as the only authorization control.
- Backend/API enforcement requirements should be added so authorization is enforced even if a user bypasses the UI.
- Authorization checks should use a stable action naming scheme that can be mapped to both UI features and protected operations.
