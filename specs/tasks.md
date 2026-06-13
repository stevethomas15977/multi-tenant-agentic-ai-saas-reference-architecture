# Tasks

This file tracks implementation and verification work derived from requirements.

## Authentication and Authorization

- [x] Create Draw.io context and container diagrams under `/architecture` for the authentication, authorization, and Angular UI delivery design. Related: REQ-AUTH-001, REQ-AUTH-002, REQ-UI-001.
- [ ] Define the source of `tenant_id` after Cognito authentication. Related: REQ-AUTH-001, REQ-AUTH-002.
- [ ] Define the Amazon Verified Permissions action model for tenant-scoped UI features and actions. Related: REQ-AUTH-001, REQ-AUTH-002.
- [ ] Specify fail-closed behavior for unavailable or incomplete authorization responses. Related: REQ-AUTH-002.
- [ ] Add server-side authorization requirements for protected backend/API operations. Related: REQ-AUTH-002.
- [ ] Define verification cases for authenticated users with allowed, denied, and missing tenant-scoped actions. Related: REQ-AUTH-001, REQ-AUTH-002.

## Angular UI Delivery

- [ ] Define the tenant deployment topology for the Angular UI, CloudFront Distribution, and S3 Static Website origin. Related: REQ-UI-001.
- [ ] Define whether end users may access the S3 Static Website endpoint URI directly. Related: REQ-UI-001.
- [ ] Define CloudFront viewer protocol policy, cache behavior, and default root object for the Angular UI. Related: REQ-UI-001.
- [ ] Define verification cases for CloudFront origin configuration and Angular UI delivery. Related: REQ-UI-001.
- [ ] Add API Gateway routing requirements for API or service HTTP/HTTPS requests. Related: REQ-UI-001.
