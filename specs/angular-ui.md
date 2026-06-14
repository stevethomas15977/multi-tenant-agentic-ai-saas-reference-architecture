# Angular Shell and Authorized Menu Design

This file defines the Angular shell, authorized menu, and route composition contract for `SLICE-001`.

## Scope

For `SLICE-001`, the Angular UI implements a persistent authenticated shell with:

- top navigation
- tenant name display
- Profile link
- Logout button
- Help link
- left-side authorized action menu
- center panel Angular Router child route outlet

## Shell Layout

The authenticated shell is the parent route for tenant-scoped action views.

Required shell regions:

| Region | Purpose |
| --- | --- |
| Top navigation | Persistent authenticated user controls and tenant context. |
| Left-side menu | Authorized action links derived from Cedar/AVP decisions. |
| Center panel | Active action view rendered through an Angular child route outlet. |

The top navigation is outside the child route outlet. Child route changes must not remove or replace the top navigation.

## Top Navigation Contract

The top navigation contains:

| Element | Source / Behavior |
| --- | --- |
| Tenant name field | Display name resolved from authenticated tenant context. For `SLICE-001`, this may be derived from the Cognito `tenant_id` claim or a static tenant display mapping in the authorization fixture. |
| Profile link | Fixed global shell action. Not controlled by Cedar action authorization in `SLICE-001`. |
| Logout button | Fixed global shell action that clears local authenticated session state and returns the user to the unauthenticated state. |
| Help link | Fixed global shell action. Not controlled by Cedar action authorization in `SLICE-001`. |

If tenant display name cannot be resolved, the UI may display the raw `tenant_id`. If neither tenant display name nor `tenant_id` is available, the authenticated shell must fail closed and avoid rendering tenant-scoped action views.

## Authorized Menu Contract

The left-side menu is generated from the authorized navigation actions returned by the authorization layer.

Only actions with the `app.navigate.*` prefix are eligible to render as menu links in `SLICE-001`.

The menu must:

- render only authorized navigation actions
- omit unauthorized, unknown, missing, or unavailable actions
- avoid rendering unauthorized actions as disabled selectable links
- preserve a stable order from the route/action configuration
- map each rendered link to one Angular child route

## Route and Action Mapping

Starter route/action mappings:

| Action ID | Route | View |
| --- | --- | --- |
| `app.navigate.dashboard` | `/app/dashboard` | Dashboard view |
| `app.navigate.reports` | `/app/reports` | Reports view |
| `app.navigate.admin` | `/app/admin` | Admin view |

The route/action mapping is version controlled and must stay aligned with:

- `authorization-backing.md` action catalog entries
- `cedar-avp.md` starter action identifiers
- `requirements.md` action taxonomy requirements

Each protected action route maps to exactly one canonical action ID.

## Route Guard Behavior

Angular route guards or equivalent route resolution logic must enforce the same authorized action set used by the left-side menu.

If a user navigates directly to a child route whose action is not authorized:

- the protected action view is not rendered
- the top navigation remains visible for the authenticated session
- the UI transitions to a safe fallback such as the first authorized action view or an access-denied center panel

If authorization data is unavailable or cannot be evaluated, protected action child routes fail closed.

## Center Panel Behavior

The center panel renders the selected authorized child route view.

When the user selects a different authorized menu link:

- Angular Router activates the selected child route
- the prior action view is no longer active
- the top navigation remains visible and unchanged
- the left-side menu remains based on the same authorization result until authorization state changes

## Implementation Notes

Recommended Angular structure:

```text
/login
/app
  /dashboard
  /reports
  /admin
```

Recommended component responsibilities:

| Component / Service | Responsibility |
| --- | --- |
| `AppShellComponent` | Owns authenticated shell layout, top navigation, left menu, and center router outlet. |
| `TopNavComponent` | Renders tenant name, Profile, Logout, and Help controls. |
| `AuthorizedMenuComponent` | Renders permitted navigation links only. |
| `AuthService` | Exposes authenticated user, tokens, `user_id`, and `tenant_id`. |
| `AuthorizationService` | Exposes authorized action decisions for the current tenant/user context. |
| `ActionRouteGuard` | Prevents unauthorized direct route activation. |

These names are recommendations, not mandatory implementation names, but the responsibilities must remain explicit.
