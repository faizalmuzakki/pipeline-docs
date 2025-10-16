# 6. API Endpoints

This section documents all RESTful API endpoints used in the Pipeline system. The endpoints are organized by functional modules and follow a consistent 3-column format: API Name, API Route, and HTTP Method.

**Base URL:** `https://mcms-lp.example.com/api/v3`

**API Version:** v3 (current)

**Authentication:** All endpoints require OAuth 2.0 Bearer token authentication via Laravel Passport, except where explicitly noted.

---

## 6.1 Global Endpoints

Global endpoints manage system-wide data accessible across all projects, including user management, project provisioning, vendor master data, and global reference data.

### 6.1.1 [Dashboard](./endpoints/global/dashboard.md)
Global dashboard statistics and project overview data.

### 6.1.2 [Project Management](./endpoints/global/project.md)
Project CRUD operations, project provisioning, and database management.

### 6.1.3 [User Management](./endpoints/global/user.md)
Global user account management, role assignment, and authentication.

### 6.1.4 [Vendor/Mill Management](./endpoints/global/mill.md)
Vendor and manufacturer master data management.

### 6.1.5 [Item Attributes](./endpoints/global/item-attributes.md)
Global item attribute definitions (OD, Grade, Specification, Connection, Special Conditions).

### 6.1.6 [Ports & Locodes](./endpoints/global/locodes.md)
UN/LOCODE port reference data for shipment tracking.

### 6.1.7 [Financial Forecast](./endpoints/global/forecast.md)
D365 financial forecast data integration and reporting.

---

## 6.2 Project Endpoints

Project endpoints operate within the context of a specific project database and require the `Database-Access` header for multi-tenancy routing.

### 6.2.1 [Project Dashboard](./endpoints/project/dashboard.md)
Project-specific dashboard with widgets, statistics, and progress charts.

### 6.2.2 [Project User Management](./endpoints/project/user.md)
Project-level user assignments and permissions.

### 6.2.3 [Item Management](./endpoints/project/item.md)
Project-specific item catalog and specifications.

### 6.2.4 [Specification Management](./endpoints/project/specification.md)
Technical specifications and inspection type definitions.

### 6.2.5 [Test Management](./endpoints/project/test.md)
Material testing and qualification test records.

---

## 6.3 SOW & Work Order Endpoints

### 6.3.1 [SOW (Scope of Work)](./endpoints/project/sow.md)
Contract line item management, SOW creation, and lot splitting.

### 6.3.2 Work Order Management
Work order endpoints are documented in the existing work_order.md file (previously completed).

---

## 6.4 Technical Endpoints

### 6.4.1 [MDR (Material Data Report)](./endpoints/project/technical/mdr.md)
Material certification document management, upload, and data extraction.

### 6.4.2 [Inspection Per Work Order](./endpoints/project/technical/inspection-per-wo.md)
Quality inspection management, test results, and certification.

### 6.4.3 [Product Trace](./endpoints/project/technical/product-trace.md)
Material traceability, heat number tracking, and location history.

---

## 6.5 Commercial Endpoints

### 6.5.1 [Contract Management](./endpoints/project/commercial/contract.md)
Contract CRUD operations, terms management, and documentation.

### 6.5.2 [Invoice Management](./endpoints/project/commercial/invoices.md)
Invoice generation, approval workflow, and payment tracking.

### 6.5.3 [Key Terms](./endpoints/project/commercial/key_terms.md)
Contract key terms and conditions management.

### 6.5.4 [Variation Orders](./endpoints/project/commercial/variation_list.md)
Change order management and contract amendments.

---

## 6.6 Documentation Endpoints

### 6.6.1 [NCR (Non-Conformance Report)](./endpoints/project/documentation/ncr.md)
Quality issue tracking, root cause analysis, and corrective actions.

### 6.6.2 [Procedure Management](./endpoints/project/documentation/procedure.md)
Quality procedures, work instructions, and compliance documentation.

### 6.6.3 [2nd Party Inspection](./endpoints/project/documentation/2nd_party_inspection.md)
Third-party inspection coordination and results.

### 6.6.4 [Reporting](./endpoints/project/documentation/reporting.md)
Report generation, export, and distribution.

### 6.6.5 [General Documentation](./endpoints/project/documentation/general.md)
General document management and file operations.

---

## 6.7 Transfer & Logistics Endpoints

### 6.7.1 [Transfer Management](./endpoints/project/transfer.md)
Shipment operations, cargo manifests, vessel tracking, and delivery notes.

---

## 6.8 Progress Tracking Endpoints

### 6.8.1 [Progress Management](./endpoints/project/progress.md)
Overall project progress tracking, schedule vs actual analysis, and progress reporting.

---

## API Conventions

### HTTP Methods

| Method | Purpose | Idempotent |
|--------|---------|------------|
| GET | Retrieve resources | Yes |
| POST | Create resources or submit data | No |
| PUT | Update resources (full replacement) | Yes |
| PATCH | Partial update | No |
| DELETE | Remove resources | Yes |

### Standard Response Codes

| Code | Meaning | Description |
|------|---------|-------------|
| 200 | OK | Successful GET request |
| 201 | Created | Successful POST request (resource created) |
| 204 | No Content | Successful DELETE request |
| 400 | Bad Request | Invalid request format or parameters |
| 401 | Unauthorized | Missing or invalid authentication token |
| 403 | Forbidden | Authenticated but not authorized for this resource |
| 404 | Not Found | Resource does not exist |
| 422 | Unprocessable Entity | Validation errors in request data |
| 500 | Internal Server Error | Server-side error |

### Authentication Headers

```
Authorization: Bearer {access_token}
Database-Access: {project_database_name}  // Required for project-specific endpoints
```

### Response Format

All API responses follow a consistent JSON structure:

**Success Response:**
```json
{
  "data": { /* response data */ },
  "message": "Success message",
  "status": "success"
}
```

**Error Response:**
```json
{
  "error": "Error description",
  "message": "User-friendly error message",
  "status": "error",
  "errors": { /* validation errors if applicable */ }
}
```

### Pagination

DataTables endpoints support server-side pagination with the following query parameters:

```
?draw=1
&start=0
&length=10
&search[value]=keyword
&order[0][column]=0
&order[0][dir]=asc
```

---

## Notes

- All endpoints use `/api/v3` prefix (API version 3)
- Legacy endpoints (`/api/v1`, `/api/v2`) are deprecated and should not be used
- Project-specific endpoints require `Database-Access` header with the project database name (e.g., `mongodb_project_ABC123`)
- File upload endpoints accept `multipart/form-data` content type
- File download endpoints return binary data with appropriate `Content-Type` and `Content-Disposition` headers
- Soft deletes are used throughout the system; deleted resources can be filtered using `deleted=0` query parameter
