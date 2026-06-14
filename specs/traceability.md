# Traceability

This file maps requirements to design notes, tasks, and verification coverage.

| Requirement | Design Reference | Tasks | Verification |
| --- | --- | --- | --- |
| REQ-AUTH-000 | `design.md` Authentication and Authorization Flow | Define Angular Cognito sign-in and token use; define API Gateway token behavior | Pending |
| REQ-AUTH-001 | `design.md` Authentication and Authorization Flow | Define `tenant_id` source; define AVP action model; define verification cases | Pending |
| REQ-AUTH-002 | `design.md` Authentication and Authorization Flow; Fail-Closed UI Behavior | Define AVP action model; specify fail-closed behavior; define verification cases | Pending |
| REQ-AUTH-003 | `design.md` Authentication and Authorization Flow; Design Notes | Define backend AVP enforcement model; define protected backend operation mapping; define fail-closed backend authorization cases | Pending |
| REQ-AUTHZ-001 | `design.md` Cedar Authorization Model; `architecture.md` Authorization Policy Context | Define Cedar schema; define tenant-scoped context builder; define policy templates | `verification.md` TM-AUTHZ-001, TM-AUTHZ-002, TM-AUTHZ-003 |
| REQ-AUTHZ-002 | `design.md` Cedar Authorization Model | Define group-action grant model; define group-action permit policy template | `verification.md` TM-AUTHZ-004, TM-AUTHZ-005 |
| REQ-AUTHZ-003 | `design.md` Cedar Authorization Model; `design.md` Fail-Closed UI Behavior | Define default-deny behavior; define unknown action handling | `verification.md` TM-AUTHZ-003, TM-AUTHZ-005 |
| REQ-AUTHZ-004 | `design.md` Cedar Authorization Model; `architecture.md` Tenant Boundary | Define cross-tenant forbid policy template; define misconfiguration protection cases | `verification.md` TM-AUTHZ-002 |
| REQ-AUTHZ-005 | `design.md` Cedar Authorization Model | Define same-tenant user-group membership loading; define stale membership filtering | `verification.md` TM-AUTHZ-006 |
| REQ-AUTHZ-006 | `design.md` Cedar Authorization Model | Define group-action persistence model; define duplicate mapping handling | `verification.md` TM-AUTHZ-007, TM-AUTHZ-008 |
| REQ-AUTHZ-007 | `design.md` Cedar Authorization Model; `design.md` Angular UI Layout | Define Cedar-authorized UI navigation mapping | `verification.md` TM-AUTHZ-009, TM-AUTHZ-010 |
| REQ-AUTHZ-008 | `design.md` Cedar Authorization Model; `design.md` Angular UI Layout | Define selected action view mapping and center panel replacement behavior | `verification.md` TM-AUTHZ-011 |
| REQ-AUTHZ-009 | `design.md` Cedar Authorization Model; `design.md` Backend REST Routing | Define protected REST and WebSocket action enforcement | `verification.md` TM-AUTHZ-012, TM-AUTHZ-013 |
| REQ-AUTHZ-010 | `design.md` Cedar Authorization Model | Define canonical action catalog governance and operation mapping review | `verification.md` TM-AUTHZ-014, TM-AUTHZ-015 |
| REQ-TENANT-001 | `design.md` Tenant Isolation; `architecture.md` Tenant Boundary | Define tenant isolation strategy; define shared versus tenant-specific resources; define tenant isolation verification evidence | Pending |
| REQ-UI-001 | `design.md` Angular UI Delivery; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define tenant deployment topology; define direct S3 endpoint access behavior; define CloudFront viewer/cache behavior; define Angular UI delivery verification cases | Pending |
| REQ-UI-002 | `design.md` Angular UI Delivery; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define CloudFront viewer protocol policy; define ACM certificate scope; define HTTPS certificate verification cases | Pending |
| REQ-UI-003 | `design.md` Angular UI Layout | Define Angular shell layout; define tenant name display source; define Profile, Logout, and Help behavior | Pending |
| REQ-UI-004 | `design.md` Angular UI Layout; `design.md` Authentication and Authorization Flow | Define Cedar-authorized action menu mapping; define authorized menu rendering cases | Pending |
| REQ-UI-005 | `design.md` Angular UI Layout; `design.md` Fail-Closed UI Behavior | Define denied, missing, and unavailable menu authorization behavior; verify unauthorized actions are omitted from DOM links | Pending |
| REQ-UI-006 | `design.md` Angular UI Layout | Define selected action view replacement behavior; define Angular view composition mechanism and transition verification cases | Pending |
| REQ-UI-007 | `design.md` Angular UI Layout | Define persistent shell behavior; verify top navigation remains visible and unchanged during action transitions | Pending |
| REQ-API-001 | `design.md` Backend REST Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define Cognito authorizer configuration; define Cognito and AVP authorization relationship; define fail-closed authorizer behavior; define API Gateway authorizer verification cases | Pending |
| REQ-API-002 | `design.md` Backend REST Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define API Gateway Lambda integration style; define Lambda runtime selection; define Lambda function granularity and tenant isolation; define Lambda invocation verification cases | Pending |
| REQ-API-003 | `design.md` Backend REST Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define WebSocket agentic route mapping; define simple Bedrock Agent behavior; define long-term memory isolation and retention; define Bedrock memory verification cases | Pending |
| REQ-API-004 | `design.md` Backend REST Routing | Define whether durable session and preference persistence is required; define candidate tenant-scoped persistence options; define persistence isolation, TTL, retention, and verification expectations if introduced | Pending |
| REQ-DNS-001 | `design.md` DNS Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define hosted zone and domain naming conventions; define DNS record scope; define Route 53 verification cases | Pending |
| REQ-DNS-002 | `design.md` DNS Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define Cognito authorization endpoint domain strategy; define DNS record scope; define Route 53 verification cases | Pending |
| REQ-DNS-003 | `design.md` DNS Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define API Gateway domain naming conventions; define DNS record scope; define Route 53 verification cases | Pending |
| REQ-IAC-001 | `design.md` Terraform Infrastructure as Code; `architecture.md` Infrastructure as Code Context | Define Terraform backend and state strategy; define module boundaries; define managed resource inventory; define lifecycle exceptions; define Terraform plan and drift verification cases | Pending |
