# Verification Matrix

This file defines verification scenarios for requirements that are ready for test design.

## Authorization Policy Taxonomy

| Test ID | Requirement | Scenario | Expected Result | Evidence Type |
| --- | --- | --- | --- | --- |
| TM-AUTHZ-001 | REQ-AUTHZ-001 | Same-tenant user requests granted action | Permit | Cedar policy test |
| TM-AUTHZ-002 | REQ-AUTHZ-001, REQ-AUTHZ-004 | Cross-tenant request with apparent grant | Deny | Cedar policy test |
| TM-AUTHZ-003 | REQ-AUTHZ-001, REQ-AUTHZ-003 | Missing `tenant_id` in principal or resource context | Deny | Cedar policy test |
| TM-AUTHZ-004 | REQ-AUTHZ-002 | User has one group granting action | Permit | Cedar policy test |
| TM-AUTHZ-005 | REQ-AUTHZ-002, REQ-AUTHZ-003 | User has no granting groups | Deny | Cedar policy test |
| TM-AUTHZ-006 | REQ-AUTHZ-005 | User mapped to multiple same-tenant groups | All memberships included | Unit test |
| TM-AUTHZ-007 | REQ-AUTHZ-006 | Group assigned multiple actions | All grants available for decision | Unit test |
| TM-AUTHZ-008 | REQ-AUTHZ-006 | Duplicate group-action mapping | Single effective mapping | Unit or persistence test |
| TM-AUTHZ-009 | REQ-AUTHZ-007, REQ-UI-004 | Render menu for authorized actions | Only authorized links displayed | UI component or e2e test |
| TM-AUTHZ-010 | REQ-AUTHZ-007, REQ-UI-005 | Unauthorized action exists in catalog | Link not displayed | UI component or e2e test |
| TM-AUTHZ-011 | REQ-AUTHZ-008, REQ-UI-006 | Click authorized menu link | Center panel displays selected action view and prior action view is no longer active | UI component or e2e test |
| TM-AUTHZ-012 | REQ-AUTHZ-009, REQ-AUTH-003 | Unauthorized REST action call | HTTP 403 and no operation execution | API integration test |
| TM-AUTHZ-013 | REQ-AUTHZ-009, REQ-AUTH-003 | Unauthorized WebSocket action call | Authorization denied response and no operation execution | WebSocket integration test |
| TM-AUTHZ-014 | REQ-AUTHZ-010 | Validate action catalog uniqueness | No duplicate action IDs | Unit or static validation test |
| TM-AUTHZ-015 | REQ-AUTHZ-010 | Trace operation-to-action mapping | Every protected UI/API operation maps to a canonical action | Static validation or architecture review |
