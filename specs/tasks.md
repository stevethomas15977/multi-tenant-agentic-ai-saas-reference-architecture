# Tasks

This file tracks implementation and verification work derived from requirements.

## Authentication and Authorization

- [ ] Create Draw.io context and container diagrams under `/architecture` for the authentication and authorization design. Related: REQ-AUTH-001, REQ-AUTH-002.
- [ ] Define the source of `tenant_id` after Cognito authentication. Related: REQ-AUTH-001, REQ-AUTH-002.
- [ ] Define the Amazon Verified Permissions action model for tenant-scoped UI features and actions. Related: REQ-AUTH-001, REQ-AUTH-002.
- [ ] Specify fail-closed behavior for unavailable or incomplete authorization responses. Related: REQ-AUTH-002.
- [ ] Add server-side authorization requirements for protected backend/API operations. Related: REQ-AUTH-002.
- [ ] Define verification cases for authenticated users with allowed, denied, and missing tenant-scoped actions. Related: REQ-AUTH-001, REQ-AUTH-002.
