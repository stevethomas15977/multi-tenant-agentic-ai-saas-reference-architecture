# Authorization Backing Source

This file defines the authorization backing source for the first implementation slice before durable persistence is introduced.

## SLICE-001 Backing Source

For `SLICE-001`, authorization backing data shall be a version-controlled static fixture used by the Angular UI, authorization service, and tests.

The fixture represents the same conceptual records that a future durable backing source may store:

- users
- groups
- user-group memberships
- group-action grants
- resources
- action catalog entries

This keeps the first slice deterministic and reviewable while deferring DynamoDB or another durable persistence option.

## Data Model

### Users

| Field | Description |
| --- | --- |
| `user_id` | Stable user identifier. |
| `tenant_id` | Tenant associated with the user for the fixture scenario. |
| `display_name` | Human-readable user label for tests or UI fixtures. |

### Groups

| Field | Description |
| --- | --- |
| `group_id` | Stable group identifier. |
| `tenant_id` | Tenant that owns the group. |
| `name` | Human-readable group name. |

### User Group Memberships

| Field | Description |
| --- | --- |
| `user_id` | User identifier. |
| `group_id` | Group identifier. |
| `tenant_id` | Tenant context for the membership. |

Memberships are tenant constrained. A membership whose `tenant_id` does not match the active authorization tenant is excluded from the authorization context.

### Group Action Grants

| Field | Description |
| --- | --- |
| `group_id` | Group identifier. |
| `action_id` | Canonical action identifier granted to the group. |

Duplicate `group_id` and `action_id` pairs collapse to one effective grant.

### Resources

| Field | Description |
| --- | --- |
| `resource_id` | Stable resource identifier. |
| `resource_type` | Resource category or type. |
| `tenant_id` | Tenant that owns the resource. |

### Action Catalog

| Field | Description |
| --- | --- |
| `action_id` | Canonical action identifier. |
| `category` | Action category such as `navigation`, `api`, `membership`, or `user`. |
| `label` | Human-readable action label. |
| `route` | Angular child route path when the action maps to navigation. |

## Starter Fixture Records

The first slice should include at least the following scenario records:

| Record Type | Required Records |
| --- | --- |
| Tenants | `tenant-a`, `tenant-b` |
| Users | `user-admin-a`, `user-reporter-a`, `user-viewer-b` |
| Groups | `tenant-a-admins`, `tenant-a-reporters`, `tenant-b-viewers` |
| Actions | `app.navigate.dashboard`, `app.navigate.reports`, `app.navigate.admin` |
| Resources | `tenant-a-app`, `tenant-b-app` |

## Required Fixture Scenarios

The fixture must support these authorization scenarios:

- Same-tenant user with a group grant can access the granted action.
- Same-tenant user without a group grant cannot access the action.
- User with no granting group is denied by default.
- Cross-tenant access is denied even when a misconfigured grant appears to allow it.
- Missing `tenant_id` in principal or resource context is denied.
- Authorized navigation actions map to left-side menu links.
- Unauthorized navigation actions are omitted from selectable menu links.

## Future Durable Persistence

When durable persistence is introduced, this fixture model should become the migration reference for the persistent authorization backing source.

Any future DynamoDB or alternate persistence design must preserve:

- tenant-constrained user-group memberships
- unique effective group-action grants
- canonical action IDs
- resource tenant ownership
- fail-closed behavior for missing or invalid tenant context
