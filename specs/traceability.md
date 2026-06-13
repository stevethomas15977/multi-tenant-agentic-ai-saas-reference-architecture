# Traceability

This file maps requirements to design notes, tasks, and verification coverage.

| Requirement | Design Reference | Tasks | Verification |
| --- | --- | --- | --- |
| REQ-AUTH-000 | `design.md` Authentication and Authorization Flow | Define Angular Cognito sign-in and token use; define API Gateway token behavior | Pending |
| REQ-AUTH-001 | `design.md` Authentication and Authorization Flow | Define `tenant_id` source; define AVP action model; define verification cases | Pending |
| REQ-AUTH-002 | `design.md` Authentication and Authorization Flow; Fail-Closed UI Behavior | Define AVP action model; specify fail-closed behavior; define verification cases | Pending |
| REQ-AUTH-003 | `design.md` Authentication and Authorization Flow; Design Notes | Define backend AVP enforcement model; define protected backend operation mapping; define fail-closed backend authorization cases | Pending |
| REQ-TENANT-001 | `design.md` Tenant Isolation; `architecture.md` Tenant Boundary | Define tenant isolation strategy; define shared versus tenant-specific resources; define tenant isolation verification evidence | Pending |
| REQ-UI-001 | `design.md` Angular UI Delivery; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define tenant deployment topology; define direct S3 endpoint access behavior; define CloudFront viewer/cache behavior; define Angular UI delivery verification cases | Pending |
| REQ-UI-002 | `design.md` Angular UI Delivery; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define CloudFront viewer protocol policy; define ACM certificate scope; define HTTPS certificate verification cases | Pending |
| REQ-API-001 | `design.md` Backend REST Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define Cognito authorizer configuration; define Cognito and AVP authorization relationship; define fail-closed authorizer behavior; define API Gateway authorizer verification cases | Pending |
| REQ-API-002 | `design.md` Backend REST Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define API Gateway Lambda integration style; define Lambda runtime selection; define Lambda function granularity and tenant isolation; define Lambda invocation verification cases | Pending |
| REQ-API-003 | `design.md` Backend REST Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define WebSocket agentic route mapping; define simple Bedrock Agent behavior; define long-term memory isolation and retention; define Bedrock memory verification cases | Pending |
| REQ-API-004 | `design.md` Backend REST Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define DynamoDB key schema and indexes; define session and preference retention; define DynamoDB tenant isolation; define REST state verification cases | Pending |
| REQ-DNS-001 | `design.md` DNS Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define hosted zone and domain naming conventions; define DNS record scope; define Route 53 verification cases | Pending |
| REQ-DNS-002 | `design.md` DNS Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define Cognito authorization endpoint domain strategy; define DNS record scope; define Route 53 verification cases | Pending |
| REQ-DNS-003 | `design.md` DNS Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define API Gateway domain naming conventions; define DNS record scope; define Route 53 verification cases | Pending |
