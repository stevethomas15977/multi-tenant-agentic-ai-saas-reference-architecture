# Tasks

This file tracks implementation and verification work derived from requirements.

## Authentication and Authorization

- [x] Create Draw.io context and container diagrams under `/architecture` for the authentication, authorization, Angular UI delivery, CloudFront HTTPS certificate, backend REST routing, Lambda backend processing, WebSocket Bedrock agent memory, and DNS routing design. Related: REQ-AUTH-001, REQ-AUTH-002, REQ-UI-001, REQ-UI-002, REQ-API-001, REQ-API-002, REQ-API-003, REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
- [ ] Export SVG preview images for each Draw.io diagram under `/architecture` and add Markdown image references so GitHub reviewers can visualize diagrams in the repository. Related: Design Standards.
- [ ] Define Angular Cognito sign-in, token acquisition, and token use for API Gateway access. Related: REQ-AUTH-000, REQ-API-001.
- [x] Define the source of `tenant_id` after Cognito authentication. Related: REQ-AUTH-000, REQ-AUTH-001, REQ-AUTH-002, REQ-AUTH-004, REQ-TENANT-001.
- [x] Define the Amazon Verified Permissions action model for tenant-scoped UI features, actions, and backend operations. Related: REQ-AUTH-001, REQ-AUTH-002, REQ-AUTH-003, REQ-AUTHZ-001, REQ-AUTHZ-002, REQ-AUTHZ-010.
- [ ] Specify fail-closed behavior for unavailable or incomplete authorization responses. Related: REQ-AUTH-002.
- [ ] Define downstream action authorization requirements for protected backend/API operations. Related: REQ-AUTH-003, REQ-API-001.
- [ ] Define tenant isolation strategy across identity, authorization, API access, data storage, DNS/resource scoping, and long-term memory. Related: REQ-TENANT-001.
- [ ] Define verification cases for authenticated users with allowed, denied, and missing tenant-scoped actions. Related: REQ-AUTH-001, REQ-AUTH-002.

## Authorization Policy Taxonomy

- [ ] Define Cedar schema for `App::User`, `App::Group`, `App::Action`, `App::Resource`, and `App::Tenant`. Related: REQ-AUTHZ-001, REQ-AUTHZ-002, REQ-AUTHZ-010.
- [ ] Define authorization backing source and data model for users, groups, user-group memberships, group-action grants, resources, and action catalog records before DynamoDB persistence is introduced. Related: REQ-AUTHZ-005, REQ-AUTHZ-006.
- [ ] Define Cedar policy templates for group-action permit, cross-tenant forbid, and default-deny behavior. Related: REQ-AUTHZ-001, REQ-AUTHZ-002, REQ-AUTHZ-003, REQ-AUTHZ-004.
- [ ] Define authorization context builder behavior for same-tenant group memberships, stale cross-tenant memberships, duplicate group-action mappings, and missing tenant context. Related: REQ-AUTHZ-001, REQ-AUTHZ-005, REQ-AUTHZ-006.
- [ ] Define canonical action catalog governance, starter action catalog ownership, and action rename migration review. Related: REQ-AUTHZ-010.
- [ ] Define UI route and API operation mappings to canonical action IDs. Related: REQ-AUTHZ-007, REQ-AUTHZ-008, REQ-AUTHZ-009, REQ-UI-004, REQ-UI-006, REQ-AUTH-003.
- [ ] Define authorization verification cases for tenant scope, group grants, default deny, cross-tenant forbid, group memberships, group-action mappings, UI menu rendering, action view rendering, backend denial, and action catalog governance. Related: REQ-AUTHZ-001, REQ-AUTHZ-002, REQ-AUTHZ-003, REQ-AUTHZ-004, REQ-AUTHZ-005, REQ-AUTHZ-006, REQ-AUTHZ-007, REQ-AUTHZ-008, REQ-AUTHZ-009, REQ-AUTHZ-010.

## Angular UI Delivery

- [ ] Define the tenant deployment topology for the Angular UI, CloudFront Distribution, and S3 Static Website origin. Related: REQ-UI-001.
- [ ] Define whether end users may access the S3 Static Website endpoint URI directly. Related: REQ-UI-001.
- [ ] Define CloudFront viewer protocol policy, cache behavior, and default root object for the Angular UI. Related: REQ-UI-001, REQ-UI-002.
- [ ] Define Amazon Certificate Manager certificate scope for CloudFront domains. Related: REQ-UI-002.
- [ ] Define verification cases for CloudFront origin configuration, HTTPS certificate configuration, and Angular UI delivery. Related: REQ-UI-001, REQ-UI-002.
- [x] Add API Gateway routing requirements for API or service HTTP/HTTPS requests. Related: REQ-UI-001, REQ-API-001.

## Angular UI Layout

- [ ] Define Angular shell layout structure for top navigation, center panel, left-side menu, and selected action view region. Related: REQ-UI-003, REQ-UI-004, REQ-UI-006, REQ-UI-007.
- [ ] Define tenant name display source and fallback behavior for the top navigation tenant name field. Related: REQ-UI-003, REQ-TENANT-001.
- [ ] Define Profile, Logout, and Help link routing and behavior. Related: REQ-UI-003.
- [ ] Define the mapping between Amazon Verified Permissions Cedar-authorized actions and left-side menu action links. Related: REQ-UI-004, REQ-UI-005, REQ-AUTH-002.
- [ ] Define verification cases for permitted, denied, missing, and unavailable menu action authorization results. Related: REQ-UI-004, REQ-UI-005.
- [x] Define center panel action view replacement behavior and Angular view composition mechanism. Related: REQ-UI-006, REQ-UI-007.
- [ ] Define transition verification cases for Angular Router child route action views. Related: REQ-UI-006, REQ-UI-007.

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
- [ ] Define whether and when durable user session and preference persistence is required. Related: REQ-API-004.
- [ ] Define candidate tenant-scoped persistence options for durable user sessions and preferences, including DynamoDB as a deferred option. Related: REQ-API-004.
- [ ] Define tenant isolation, TTL, retention, and verification expectations for durable session and preference persistence if introduced. Related: REQ-API-004.

## DNS Routing

- [ ] Define hosted zones and domain naming conventions for CloudFront, Cognito authorization, REST API, and WebSocket API Alias or CNAME records. Related: REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
- [ ] Define whether Route 53 Alias or CNAME records are tenant-specific, environment-specific, application-specific, or shared. Related: REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.
- [ ] Define Cognito authorization endpoint domain strategy. Related: REQ-DNS-002.
- [ ] Define verification cases for Route 53 Alias or CNAME DNS records routing to CloudFront, Cognito authorization, REST API, and WebSocket API endpoints. Related: REQ-DNS-001, REQ-DNS-002, REQ-DNS-003.

## Infrastructure as Code

- [ ] Define Terraform backend, state locking, workspace, and environment strategy. Related: REQ-IAC-001.
- [ ] Define Terraform module boundaries for shared platform resources and tenant-specific resources. Related: REQ-IAC-001, REQ-TENANT-001.
- [ ] Create a Terraform-managed AWS resource inventory for S3, CloudFront, Route 53, ACM, Cognito, Verified Permissions, API Gateway, Lambda, Bedrock, IAM, KMS, CloudWatch, AWS WAF, and DynamoDB resources when durable persistence is included. Related: REQ-IAC-001.
- [ ] Define Terraform lifecycle exceptions for runtime data, user records, tenant memberships, session records, preference records, knowledge base documents, embeddings, and operational authorization grants. Related: REQ-IAC-001.
- [ ] Define Terraform plan review and drift detection verification cases. Related: REQ-IAC-001.
