# Traceability

This file maps requirements to design notes, tasks, and verification coverage.

| Requirement | Design Reference | Tasks | Verification |
| --- | --- | --- | --- |
| REQ-AUTH-001 | `design.md` Authentication and Authorization Flow | Define `tenant_id` source; define AVP action model; define verification cases | Pending |
| REQ-AUTH-002 | `design.md` Authentication and Authorization Flow; Fail-Closed UI Behavior | Define AVP action model; specify fail-closed behavior; add server-side authorization requirements; define verification cases | Pending |
| REQ-UI-001 | `design.md` Angular UI Delivery; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define tenant deployment topology; define direct S3 endpoint access behavior; define CloudFront viewer/cache behavior; define Angular UI delivery verification cases | Pending |
| REQ-UI-002 | `design.md` Angular UI Delivery; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define CloudFront viewer protocol policy; define ACM certificate scope; define HTTPS certificate verification cases | Pending |
| REQ-API-001 | `design.md` Backend REST Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define Cognito authorizer configuration; define Cognito and AVP authorization relationship; define fail-closed authorizer behavior; define API Gateway authorizer verification cases | Pending |
| REQ-DNS-001 | `design.md` DNS Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define hosted zone and domain naming conventions; define DNS record scope; define Route 53 verification cases | Pending |
| REQ-DNS-002 | `design.md` DNS Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define Cognito authorization endpoint domain strategy; define DNS record scope; define Route 53 verification cases | Pending |
| REQ-DNS-003 | `design.md` DNS Routing; `/architecture/context-diagram.drawio`; `/architecture/container-diagram.drawio` | Define API Gateway domain naming conventions; define DNS record scope; define Route 53 verification cases | Pending |
