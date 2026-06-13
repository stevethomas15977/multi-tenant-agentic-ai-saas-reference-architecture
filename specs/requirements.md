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

