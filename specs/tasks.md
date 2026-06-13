# Tasks

This file tracks implementation and verification work derived from requirements.

## Authentication and Authorization

- [x] Create Draw.io context and container diagrams under `/architecture` for the authentication, authorization, Angular UI delivery, CloudFront HTTPS certificate, backend REST routing, and DNS routing design. Related: REQ-AUTH-001, REQ-AUTH-002, REQ-UI-001, REQ-UI-002, REQ-API-001, REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
- [ ] Define the source of `tenant_id` after Cognito authentication. Related: REQ-AUTH-001, REQ-AUTH-002.
- [ ] Define the Amazon Verified Permissions action model for tenant-scoped UI features and actions. Related: REQ-AUTH-001, REQ-AUTH-002.
- [ ] Specify fail-closed behavior for unavailable or incomplete authorization responses. Related: REQ-AUTH-002.
- [ ] Add server-side authorization requirements for protected backend/API operations. Related: REQ-AUTH-002, REQ-API-001.
- [ ] Define verification cases for authenticated users with allowed, denied, and missing tenant-scoped actions. Related: REQ-AUTH-001, REQ-AUTH-002.

## Angular UI Delivery

- [ ] Define the tenant deployment topology for the Angular UI, CloudFront Distribution, and S3 Static Website origin. Related: REQ-UI-001.
- [ ] Define whether end users may access the S3 Static Website endpoint URI directly. Related: REQ-UI-001.
- [ ] Define CloudFront viewer protocol policy, cache behavior, and default root object for the Angular UI. Related: REQ-UI-001, REQ-UI-002.
- [ ] Define Amazon Certificate Manager certificate scope for CloudFront domains. Related: REQ-UI-002.
- [ ] Define verification cases for CloudFront origin configuration, HTTPS certificate configuration, and Angular UI delivery. Related: REQ-UI-001, REQ-UI-002.
- [x] Add API Gateway routing requirements for API or service HTTP/HTTPS requests. Related: REQ-UI-001, REQ-API-001.

## Backend REST Routing

- [ ] Define the source of truth for authorized Amazon API Gateway REST endpoints. Related: REQ-API-001.
- [ ] Define how Angular UI actions map to API Gateway REST paths, HTTP methods, and authorization actions. Related: REQ-API-001.
- [ ] Define fail-closed behavior when authorized endpoint data is missing or stale. Related: REQ-API-001.
- [ ] Define verification cases for allowed and denied Angular backend REST requests. Related: REQ-API-001.

## DNS Routing

- [ ] Define hosted zones and domain naming conventions for CloudFront, Cognito authorization, REST API, and WebSocket API Alias records. Related: REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
- [ ] Define whether Route 53 Alias records are tenant-specific, environment-specific, application-specific, or shared. Related: REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
- [ ] Define Cognito authorization endpoint domain strategy. Related: REQ-DNS-002.
- [ ] Define verification cases for Route 53 Alias records routing to CloudFront, Cognito authorization, REST API, and WebSocket API endpoints. Related: REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
