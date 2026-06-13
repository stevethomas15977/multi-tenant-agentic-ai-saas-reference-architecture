# Tasks

This file tracks implementation and verification work derived from requirements.

## Authentication and Authorization

- [x] Create Draw.io context and container diagrams under `/architecture` for the authentication, authorization, Angular UI delivery, CloudFront HTTPS certificate, backend REST routing, Lambda backend processing, REST DynamoDB session preferences, WebSocket Bedrock agent memory, and DNS routing design. Related: REQ-AUTH-001, REQ-AUTH-002, REQ-UI-001, REQ-UI-002, REQ-API-001, REQ-API-002, REQ-API-003, REQ-API-004, REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
- [ ] Define Angular Cognito sign-in, token acquisition, and token use for API Gateway access. Related: REQ-AUTH-000, REQ-API-001.
- [ ] Define the source of `tenant_id` after Cognito authentication. Related: REQ-AUTH-000, REQ-AUTH-001, REQ-AUTH-002, REQ-TENANT-001.
- [ ] Define the Amazon Verified Permissions action model for tenant-scoped UI features, actions, and backend operations. Related: REQ-AUTH-001, REQ-AUTH-002, REQ-AUTH-003.
- [ ] Specify fail-closed behavior for unavailable or incomplete authorization responses. Related: REQ-AUTH-002.
- [ ] Define downstream action authorization requirements for protected backend/API operations. Related: REQ-AUTH-003, REQ-API-001.
- [ ] Define tenant isolation strategy across identity, authorization, API access, data storage, DNS/resource scoping, and long-term memory. Related: REQ-TENANT-001.
- [ ] Define verification cases for authenticated users with allowed, denied, and missing tenant-scoped actions. Related: REQ-AUTH-001, REQ-AUTH-002.

## Angular UI Delivery

- [ ] Define the tenant deployment topology for the Angular UI, CloudFront Distribution, and S3 Static Website origin. Related: REQ-UI-001.
- [ ] Define whether end users may access the S3 Static Website endpoint URI directly. Related: REQ-UI-001.
- [ ] Define CloudFront viewer protocol policy, cache behavior, and default root object for the Angular UI. Related: REQ-UI-001, REQ-UI-002.
- [ ] Define Amazon Certificate Manager certificate scope for CloudFront domains. Related: REQ-UI-002.
- [ ] Define verification cases for CloudFront origin configuration, HTTPS certificate configuration, and Angular UI delivery. Related: REQ-UI-001, REQ-UI-002.
- [x] Add API Gateway routing requirements for API or service HTTP/HTTPS requests. Related: REQ-UI-001, REQ-API-001.

## Backend REST Routing

- [ ] Define the Cognito User Pool, app client, token type, scopes, and claims used by Amazon Cognito Authorizers. Related: REQ-API-001.
- [ ] Define how Amazon Cognito Authorizer authentication combines with Amazon Verified Permissions action authorization. Related: REQ-API-001, REQ-AUTH-001, REQ-AUTH-002.
- [ ] Define fail-closed behavior for API Gateway requests that do not satisfy the Amazon Cognito Authorizer. Related: REQ-API-001.
- [ ] Define verification cases for allowed and denied API Gateway requests using the Amazon Cognito Authorizer. Related: REQ-API-001.
- [ ] Define API Gateway to Python-based and/or TypeScript-based Amazon Lambda integration style. Related: REQ-API-002.
- [ ] Define Lambda function granularity and tenant isolation strategy. Related: REQ-API-002.
- [ ] Define verification cases for API Gateway requests invoking Python-based and/or TypeScript-based Amazon Lambda functions. Related: REQ-API-002.
- [ ] Define WebSocket routes that require Bedrock Agent processing. Related: REQ-API-003.
- [ ] Define what qualifies the Amazon Bedrock Agent as simple. Related: REQ-API-003.
- [ ] Define tenant isolation, retention, deletion, and retrieval policies for long-term memory. Related: REQ-API-003.
- [ ] Define verification cases for WebSocket requests invoking Python-based and/or TypeScript-based Lambda, Amazon Bedrock Agent, Amazon Bedrock Knowledge Base, and Amazon S3 vector index memory. Related: REQ-API-003.
- [ ] Define DynamoDB key schema, indexes, and item model for user sessions and preferences. Related: REQ-API-004.
- [ ] Define DynamoDB TTL and retention policies for user sessions and preferences. Related: REQ-API-004.
- [ ] Define tenant isolation strategy for DynamoDB-backed user sessions and preferences. Related: REQ-API-004.
- [ ] Define verification cases for REST requests using Python-based and/or TypeScript-based Lambda and DynamoDB-backed user session and preferences management. Related: REQ-API-004.

## DNS Routing

- [ ] Define hosted zones and domain naming conventions for CloudFront, Cognito authorization, REST API, and WebSocket API Alias or CNAME records. Related: REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
- [ ] Define whether Route 53 Alias or CNAME records are tenant-specific, environment-specific, application-specific, or shared. Related: REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
- [ ] Define Cognito authorization endpoint domain strategy. Related: REQ-DNS-002.
- [ ] Define verification cases for Route 53 Alias or CNAME DNS records routing to CloudFront, Cognito authorization, REST API, and WebSocket API endpoints. Related: REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
