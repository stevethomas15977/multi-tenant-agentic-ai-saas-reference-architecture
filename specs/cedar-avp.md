# Cedar and Amazon Verified Permissions Provisioning

This file defines the Cedar schema and policy artifact expectations that Amazon Verified Permissions provisioning must follow.

## Scope

For `SLICE-001`, Amazon Verified Permissions is aligned to:

- the `App` Cedar namespace
- tenant-scoped users, groups, actions, resources, and tenants
- the static authorization backing fixture in `authorization-backing.md`
- the policy patterns required by `REQ-AUTHZ-001` through `REQ-AUTHZ-012`

## Policy Store Provisioning

The Amazon Verified Permissions policy store shall be provisioned with a version-controlled Cedar schema before tenant authorization policies are evaluated.

For the first slice, one environment-scoped policy store is sufficient. Tenant isolation is enforced by Cedar entity attributes and policies, not by creating a separate policy store per tenant.

Future architecture work may introduce tenant-specific policy stores only if the operational, deployment, and lifecycle tradeoffs are explicitly accepted.

## Cedar Namespace

The policy store uses the Cedar namespace `App`.

## Entity Types

### `App::User`

Required attributes:

- `user_id: String`
- `tenant_id: String`

Relationships:

- May be a member of one or more `App::Group` entities.

### `App::Group`

Required attributes:

- `group_id: String`
- `tenant_id: String`
- `name: String`

Relationships:

- May include `App::User` members through Cedar entity hierarchy.
- Grants one or more `App::Action` identifiers through version-controlled grant configuration.

### `App::Action`

Required attributes:

- `action_id: String`
- `category: String`

Starter actions:

- `app.navigate.dashboard`
- `app.navigate.reports`
- `app.navigate.admin`
- `api.orders.read`
- `api.orders.create`
- `membership.assign`
- `membership.revoke`
- `user.read`
- `user.update`

### `App::Resource`

Required attributes:

- `resource_id: String`
- `resource_type: String`
- `tenant_id: String`

### `App::Tenant`

Required attributes:

- `tenant_id: String`

## Action Applicability

Every starter action applies to:

- principal type: `App::User`
- resource type: `App::Resource`

This keeps the first slice simple. Future slices may split resources into narrower entity types such as `App::Application`, `App::Order`, `App::Membership`, or `App::UserProfile` after protected operation mapping is mature enough to justify the additional schema detail.

## Cedar Schema Artifact Shape

The provisioned schema artifact should follow this shape and must be validated with Cedar or Amazon Verified Permissions tooling before use:

```json
{
  "App": {
    "entityTypes": {
      "User": {
        "memberOfTypes": ["Group"],
        "shape": {
          "type": "Record",
          "attributes": {
            "user_id": { "type": "String", "required": true },
            "tenant_id": { "type": "String", "required": true }
          }
        }
      },
      "Group": {
        "shape": {
          "type": "Record",
          "attributes": {
            "group_id": { "type": "String", "required": true },
            "tenant_id": { "type": "String", "required": true },
            "name": { "type": "String", "required": true }
          }
        }
      },
      "Resource": {
        "shape": {
          "type": "Record",
          "attributes": {
            "resource_id": { "type": "String", "required": true },
            "resource_type": { "type": "String", "required": true },
            "tenant_id": { "type": "String", "required": true }
          }
        }
      },
      "Tenant": {
        "shape": {
          "type": "Record",
          "attributes": {
            "tenant_id": { "type": "String", "required": true }
          }
        }
      }
    },
    "actions": {
      "app.navigate.dashboard": {
        "appliesTo": {
          "principalTypes": ["User"],
          "resourceTypes": ["Resource"]
        }
      },
      "app.navigate.reports": {
        "appliesTo": {
          "principalTypes": ["User"],
          "resourceTypes": ["Resource"]
        }
      },
      "app.navigate.admin": {
        "appliesTo": {
          "principalTypes": ["User"],
          "resourceTypes": ["Resource"]
        }
      }
    }
  }
}
```

The implementation artifact should include the full starter action set from this file, not only the three navigation actions shown in the fragment.

## Policy Patterns

### P1: Permit Same-Tenant Group Action Grants

Intent:

- Permit a requested action when the principal is a member of a same-tenant group that grants the action.

Conceptual Cedar shape:

```cedar
permit(
  principal in App::Group::"<group-id>",
  action in [
    App::Action::"<action-id-1>",
    App::Action::"<action-id-2>"
  ],
  resource
)
when {
  principal.tenant_id == resource.tenant_id
};
```

Provisioning note:

- Group-action grant policies are generated or instantiated from the version-controlled backing fixture or future durable authorization backing source.
- The generated policy must reference only action identifiers present in the action catalog.

### P2: Forbid Cross-Tenant Access

Intent:

- Deny access whenever principal and resource tenants do not match, even if another permit policy appears to allow the action.

Conceptual Cedar shape:

```cedar
forbid(
  principal,
  action,
  resource
)
when {
  principal.tenant_id != resource.tenant_id
};
```

Provisioning note:

- This policy is a baseline policy for the policy store.
- It is provisioned before any tenant group-action grant policy is relied on.

### P3: Deny by Default

Intent:

- If no permit policy applies, the request is denied.

Provisioning note:

- No explicit Cedar policy is required for deny by default.
- Tests must verify that unknown actions, missing grants, and users without groups are denied.

## Schema and Policy Validation

Before an Amazon Verified Permissions policy store, policy, or policy template is accepted for an implementation slice:

- the Cedar schema is version controlled
- policies and templates are validated against the schema
- every referenced action ID exists in the action catalog
- every protected UI route or backend operation maps to one canonical action ID
- missing `tenant_id` in principal or resource context fails closed
- cross-tenant access is denied even when a permit policy would otherwise match

## Terraform Alignment

Terraform should manage stable Amazon Verified Permissions infrastructure and bootstrap policy artifacts, including:

- policy store
- Cedar schema
- baseline cross-tenant forbid policy
- stable policy templates or generated bootstrap policies required by the active implementation slice

Terraform should not manage high-churn tenant authorization grants unless they are explicitly classified as bootstrap/reference configuration.
