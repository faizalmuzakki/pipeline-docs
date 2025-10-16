# 8. Database Collections

This section documents the MongoDB database schema used in the Pipeline system. The complete detailed schema with field definitions, data types, indexes, and relationships is maintained in the [Database-Schema.md](../Database-Schema.md) document.

---

## 8.1 Database Architecture Overview

Pipeline uses a **multi-tenant MongoDB architecture** with database isolation per project:

### 8.1.1 Database Structure

| Database | Purpose | Collections | Description |
|----------|---------|-------------|-------------|
| `pipeline_global` | Global/System-wide data | 24 collections | User accounts, projects, vendors, OAuth tokens, global reference data |
| `mongodb_project_{code}` | Project-specific data | 51+ collections | SOW, work orders, inspections, MDR, transfers, progress tracking |

### 8.1.2 Key Architecture Features

- **Database Isolation**: Each project has its own dedicated MongoDB database for data security and performance
- **Database Naming**: Project database names follow the pattern `mongodb_project_{project_code}` where code is a UUID
- **Dynamic Routing**: The `Database-Access` header routes API requests to the correct project database
- **Cross-Database References**: References between global and project databases use string-based IDs (ObjectId converted to string)
- **User Access Control**: User access is controlled via `users.allowed_projects` field and `db_access` permissions

### 8.1.3 Production Statistics

**From Production Database Analysis (October 2025):**
- **Global Users**: 158 active user accounts
- **Active Projects**: 10 projects with dedicated databases
- **Sample Project Data**:
  - SOW Items: 60 contract line items
  - Work Orders: 180 manufacturing orders
  - Progress Records: 3,015 daily progress snapshots
  - Largest Collection: `work_order_actual` (3,015 documents for granular progress tracking)

---

## 8.2 Global Database Collections (pipeline_global)

The global database contains **24 collections** for system-wide data management:

### 8.2.1 Authentication & User Management

| Collection | Count | Purpose |
|------------|-------|---------|
| `users` | 158 | User accounts with authentication credentials, roles, and project access permissions |
| `user_sessions` | ~500 | Active user session tracking with IP addresses and user agents |
| `oauth_access_tokens` | ~1,000 | Laravel Passport OAuth 2.0 access tokens |
| `oauth_refresh_tokens` | ~500 | OAuth refresh tokens for token renewal |
| `oauth_clients` | 4 | OAuth client applications |
| `oauth_personal_access_clients` | 2 | Personal access token clients |
| `oauth_auth_codes` | - | OAuth authorization codes (transient) |

### 8.2.2 Project Management

| Collection | Count | Purpose |
|------------|-------|---------|
| `projects` | 10 | Project master data with database names, project managers, and status |
| `project_users` | ~200 | User-to-project assignments for access control |

### 8.2.3 Master Data & Reference Tables

| Collection | Count | Purpose |
|------------|-------|---------|
| `manufacturer` | 50+ | Vendor/mill/manufacturer master data with certifications |
| `od` | 100+ | Outer diameter specifications |
| `grade` | 50+ | Material grade definitions (X65, L360, etc.) |
| `specification` | 80+ | Technical specifications (API 5L, ISO 3183, etc.) |
| `connection` | 30+ | Connection type definitions (Buttress, VAM TOP, etc.) |
| `special_condition` | 40+ | Special condition codes |
| `locode` | 10,000+ | UN/LOCODE port reference data |
| `country` | 250 | Country reference data |

### 8.2.4 System & Integration

| Collection | Count | Purpose |
|------------|-------|---------|
| `forecast_d365` | ~500 | D365 financial forecast data synchronized from ERP |
| `migrations` | 200+ | Laravel migration history |
| `privileges` | 100+ | Role-based privilege definitions |
| `global_counter` | ~10 | Global counter sequences for ID generation |
| `job_locker` | ~50 | Distributed job locking for background processes |

**Detailed Field Definitions**: See [Database-Schema.md Section 8.2](../Database-Schema.md#82-global-database-collections-pipeline_global)

---

## 8.3 Project Database Collections (mongodb_project_{code})

Each project database contains **51+ collections** for project-specific operational data:

### 8.3.1 SOW & Work Order Management

| Collection | Avg Count | Purpose |
|------------|-----------|---------|
| `sow` | 60 | Scope of Work - contract line items and manufacturing orders |
| `sow_lot` | 120 | Sub-lots created from parent SOW items for batch processing |
| `sow_progress` | 60 | Progress tracking per SOW with schedule vs actual arrays |
| `sow_schedule` | 180 | Planned schedule milestones |
| `sow_actual` | 200 | Actual completion milestones |
| `sow_coating` | 30 | Coating configuration for coated pipe SOW items |
| `work_order` | 180 | Manufacturing work orders assigned to vendors |
| `work_order_sow` | 200 | Many-to-many links between work orders and SOW items |
| `work_order_actual` | 3,015 | **Daily production progress snapshots** (largest collection) |
| `work_order_schedule` | 500 | Planned work order schedule milestones |

### 8.3.2 Quality & Inspection

| Collection | Avg Count | Purpose |
|------------|-----------|---------|
| `inspection` | 500+ | Inspection records (visual, dimensional, NDT, mechanical) |
| `inspection_result` | 2,000+ | Individual test result measurements |
| `inspection_queue` | 100 | Queued inspection uploads pending processing |
| `pipe_receiving` | 300 | Pipe receiving inspections at destination |
| `material_test` | 150 | Material test records (chemical, mechanical) |
| `weld_repair` | 200 | Weld repair records and tracking |
| `weld_repair_result` | 400 | Weld repair test results |
| `stress_strain_curve` | 100 | Stress-strain curve data from tensile tests |
| `ncr` | 80 | Non-conformance reports |
| `ncr_action` | 150 | Corrective actions for NCRs |

### 8.3.3 Material Data & Traceability

| Collection | Avg Count | Purpose |
|------------|-----------|---------|
| `mdr` | 200 | Material Data Reports from manufacturers |
| `mdr_section` | 800 | MDR document sections and sub-sections |
| `heat` | 500 | Heat number master records |
| `length_history` | 5,000+ | Individual pipe length records with heat numbers |
| `tally_list` | 300 | Tally list uploads for shipment verification |
| `tally_file` | 300 | Tally file uploads and processing status |
| `pipe_details` | 5,000+ | Individual pipe detail records from tally lists |

### 8.3.4 Transfer & Logistics

| Collection | Avg Count | Purpose |
|------------|-----------|---------|
| `transfer` | 50 | Transfer/shipment master records |
| `transfer_route` | 100 | Transfer routes with origin, destination, and waypoints |
| `transfer_cargo` | 200 | Cargo manifest line items |
| `transfer_track_sow` | 300 | SOW item tracking within transfers |
| `vessel_tracking` | 20 | Real-time vessel position tracking with AIS data |
| `shipment` | 100 | Shipment records with departure/arrival dates |
| `delivery_note` | 80 | Delivery note documents |

### 8.3.5 Commercial & Documentation

| Collection | Avg Count | Purpose |
|------------|-----------|---------|
| `contract` | 10 | Contract master records |
| `invoice` | 100 | Invoice records with line items |
| `key_term` | 50 | Contract key terms and conditions |
| `variation` | 30 | Variation orders (change orders) |
| `procedure` | 80 | Quality procedures and work instructions |
| `2nd_party_inspection` | 50 | Third-party inspection records |

### 8.3.6 Progress & Reporting

| Collection | Avg Count | Purpose |
|------------|-----------|---------|
| `overall_sow` | 200 | Overall SOW progress aggregation |
| `overall_work_order` | 500 | Overall work order progress aggregation |
| `progress` | 1,000+ | Historical progress snapshots for reporting |
| `report_queue` | 50 | Queued report generation jobs |

### 8.3.7 Project Setup & Configuration

| Collection | Avg Count | Purpose |
|------------|-----------|---------|
| `item` | 100 | Project-specific item catalog |
| `specification` | 150 | Project-specific technical specifications |
| `location` | 50 | Storage locations and yards |
| `project_control` | 20 | Project configuration settings |
| `tag` | 500 | Tagging and categorization |
| `tabs` | 30 | User-customized tab configurations |

**Detailed Field Definitions**: See [Database-Schema.md Section 8.3](../Database-Schema.md#83-project-database-collections)

---

## 8.4 Key Design Patterns

### 8.4.1 Soft Delete Pattern

All major collections implement soft delete using these fields:
- `deleted`: Number (0 = active, 1 = deleted)
- `deleted_at`: Date (deletion timestamp)
- `deleted_by`: String (email of user who deleted)

**Query Pattern:**
```javascript
// Get active records only
db.collection.find({ deleted: 0 })

// Get deleted records
db.collection.find({ deleted: 1 })
```

### 8.4.2 Audit Trail Pattern

All major collections track creation and modification:
- `created_at`: Date (ISO format)
- `created_by`: String (email or null)
- `updated_at`: Date (ISO format)
- `updated_by`: String (email or null)

### 8.4.3 Multi-Tenancy Routing Pattern

**Project Database Selection:**
1. User logs in → `users.db_access` is set to last accessed project
2. API request includes `Database-Access` header with project database name
3. Middleware validates user has access to specified project via `users.allowed_projects`
4. MongoDB connection switches to `mongodb_project_{code}` database
5. All queries execute in project-specific database context

**Example:**
```http
GET /api/v3/project/sows
Authorization: Bearer {access_token}
Database-Access: mongodb_project_ABC123
```

### 8.4.4 Progress Tracking Pattern

Progress collections store time-series data as arrays:

```javascript
{
  id_sow: "ObjectId",
  schedule: [
    { date: "2025-01-15", value: 10.5 },
    { date: "2025-01-16", value: 12.3 }
  ],
  actual: [
    { date: "2025-01-15", value: 8.2 },
    { date: "2025-01-16", value: 11.1 }
  ],
  last_updated: ISODate("2025-01-16T23:59:59Z")
}
```

This enables S-curve chart generation comparing schedule vs actual progress over time.

### 8.4.5 Reference Data Caching Pattern

Global reference data (manufacturers, specifications, OD, grade) is cached in application memory and invalidated on updates to improve performance and reduce database queries.

---

## 8.5 Database Relationships

### 8.5.1 Cross-Database Relationships

**Global → Project References:**
- `users._id` → `project_database.created_by` (string)
- `manufacturer._id` → `sow.id_manufacturer` (string)
- `item._id` → `sow.id_item` (string)
- `specification._id` → `sow.id_spec` (string)

### 8.5.2 Project-Internal Relationships

**SOW Hierarchy:**
- `sow._id` → `sow_lot.id_parent` (1:N)
- `sow._id` → `work_order.id_sow` (1:N)
- `sow._id` → `sow_progress.id_sow` (1:1)

**Work Order → Quality:**
- `work_order._id` → `inspection.id_work_order` (1:N)
- `work_order._id` → `mdr.id_work_order` (1:N)
- `work_order._id` → `length_history.id_work_order` (1:N)

**Traceability Chain:**
- `sow._id` → `work_order.id_sow` → `heat.id_work_order` → `length_history.id_heat` → `mdr.id_heat`

**Transfer Relationships:**
- `transfer._id` → `transfer_route.id_transfer` (1:N)
- `transfer._id` → `transfer_cargo.id_transfer` (1:N)
- `transfer_cargo._id` → `transfer_track_sow.id_cargo` (1:N)

---

## 8.6 Indexing Strategy

### 8.6.1 Recommended Indexes

**High-Priority Indexes (Performance Critical):**

```javascript
// Global Database
db.users.createIndex({ email: 1 }, { unique: true })
db.users.createIndex({ session_exp: 1 })
db.oauth_access_tokens.createIndex({ user_id: 1 })
db.manufacturer.createIndex({ name: 1 })

// Project Database
db.sow.createIndex({ id_manufacturer: 1, deleted: 1 })
db.work_order.createIndex({ id_sow: 1, deleted: 1 })
db.inspection.createIndex({ id_work_order: 1, deleted: 1 })
db.length_history.createIndex({ heat_number: 1 })
db.mdr.createIndex({ heat_number: 1 })
db.sow_progress.createIndex({ id_sow: 1 })
```

### 8.6.2 Compound Indexes for Common Queries

```javascript
// SOW filtering and sorting
db.sow.createIndex({ type: 1, status: 1, deleted: 1 })

// Work order vendor filtering
db.work_order.createIndex({ id_manufacturer: 1, status: 1, deleted: 1 })

// Inspection date range queries
db.inspection.createIndex({ created_at: 1, deleted: 1 })

// Transfer tracking
db.transfer.createIndex({ status: 1, created_at: -1 })
```

---

## 8.7 Data Volume & Scaling Considerations

### 8.7.1 Large Collections

Collections that grow significantly over project lifetime:

| Collection | Growth Rate | Mitigation Strategy |
|------------|-------------|---------------------|
| `work_order_actual` | ~10-20 records/day per WO | Archive old progress snapshots after 2 years |
| `length_history` | ~100-500 records/shipment | Indexed by heat_number and work_order |
| `pipe_details` | ~100-500 records/tally upload | Indexed by tally_file_id |
| `inspection_result` | ~50-200 records/inspection | Indexed by inspection_id |
| `progress` | ~20-50 snapshots/day | Archive after project completion |

### 8.7.2 Archival Strategy

- **Active Project**: All data in primary project database
- **Completed Project**: Move to archive database after 6 months of inactivity
- **Historical Data**: Progress snapshots older than 2 years moved to cold storage
- **Retention Policy**: Project data retained for 7 years for compliance

---

## 8.8 Data Integrity & Validation

### 8.8.1 Application-Level Constraints

MongoDB does not enforce foreign key constraints, so validation is handled at the application level:

- **Service Layer Validation**: All services validate references before creating/updating documents
- **Cascade Delete Prevention**: Prevent deletion of parent records with active children (e.g., cannot delete SOW if work orders exist)
- **Required Field Validation**: Laravel request validation ensures required fields are present
- **Data Type Validation**: MongoDB schema validation rules enforce data types

### 8.8.2 Referential Integrity Checks

**SOW Deletion Check:**
```javascript
// Check for work orders before deleting SOW
if (work_order.count({ id_sow: sow_id, deleted: 0 }) > 0) {
  throw new Error("Cannot delete SOW with active work orders");
}
```

---

## 8.9 Backup & Recovery

- **Backup Frequency**: Daily full backup, hourly incremental backups
- **Backup Retention**: 30 daily backups, 12 monthly backups
- **Recovery Time Objective (RTO)**: 4 hours
- **Recovery Point Objective (RPO)**: 1 hour
- **Backup Location**: AWS S3 with cross-region replication

---

## Complete Schema Reference

For complete field-level documentation including:
- All field data types
- Sample values
- Indexes and recommended indexes
- Relationships and foreign keys
- Collection purposes and production counts

**See:** [Database-Schema.md](../Database-Schema.md)
