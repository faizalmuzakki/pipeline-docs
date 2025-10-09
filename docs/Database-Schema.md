# Database Schema - Production Analysis

## Document Information
- **Source**: Production MongoDB Dump
- **Date Dumped**: October 8, 2025
- **Global Database**: `pipeline_global` (24 collections)
- **Sample Project Database**: `xxx_2b767300-4bf5-11f0-8e64-231ee426764a` (51 collections)

---

## 8. Database Schema

### 8.1 Database Architecture

This application uses a **multi-tenant MongoDB architecture** with database isolation per project:

- **`pipeline_global`**: 24 collections for system-wide data (users, projects, mills, OAuth)
- **`[project_db_name]`**: 51+ collections per project for project-specific operational data

**Key Architecture Features:**
- Each project has its own dedicated MongoDB database
- Database name stored in `projects.db_name` (e.g., `xxx_2b767300-4bf5-11f0-8e64-231ee426764a`)
- User access controlled via `users.db_access` field
- References between databases use string-based IDs (ObjectId converted to string)

**Production Statistics (from sample project):**
- **Global Users**: 158 active users
- **Active Projects**: 10 projects
- **SOW Items**: 60 contract line items
- **Work Orders**: 180 manufacturing orders
- **Progress Records**: 3,015 daily progress entries
- **Largest Collection**: `work_order_actual` (3,015 documents for detailed progress tracking)

---

### 8.2 Global Database Collections (pipeline_global)

#### 8.2.1 users

**Purpose:** Global user accounts with authentication and role-based access control

**Production Count:** 158 users

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | ObjectId | Primary key, auto-generated unique identifier |
| first_name | String | User's first name |
| last_name | String | User's last name |
| name | String | Full name (concatenated from first_name and last_name) |
| email | String | Login email address (unique) |
| role | String | Primary role identifier (sample: "super_admin", "mi_team", "global_viewer", "non_mi_team") |
| role_name | String | Role display name (sample: "Super Admin", "MI Team", "Global Viewer") |
| additional_privileges | Array, null | Array of extra privilege IDs or null |
| photo | String | Avatar filename (default: "avatar.png") |
| photo_name | String, null | Photo upload placeholder text or null |
| active | String | Account status (sample: "y" for active, "n" for inactive) |
| token | String, null | Current session token hash or null |
| token_exp | Number | Token expiration timestamp (Unix epoch) |
| session_exp | Number | Session expiration timestamp (Unix epoch) |
| activation_code | Number | 4-digit OTP code for account activation |
| activation_code_exp | Number | Activation code expiration timestamp (Unix epoch) |
| is_locked | Boolean, Number | Account lock status (Boolean or 0/1) |
| last_attempt | Number | Last login attempt timestamp (Unix epoch) |
| attempt_number | Number | Count of failed login attempts |
| passcode_resend_last_attempt | Number, null | Last OTP resend timestamp or null |
| passcode_resend_attempt_number | Number, null | Count of OTP resend attempts or null |
| db_access | String | Last accessed project database name |
| allowed_projects | String | Project access list (sample: "all" or comma-separated project IDs) |
| manufacturer | String, null | Associated manufacturer ID for vendor users, null for internal users |
| invite_by | String | Email of user who sent invitation |
| invite_date | Number | Invitation timestamp (Unix epoch) |
| app_token | String, null | Mobile app authentication token or null |
| app_token_exp | Number | Mobile app token expiration timestamp (Unix epoch) |
| last_seen_at | String | Last activity date in DD/MM/YY format |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null for system-created |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |
| deleted | Number | Soft delete flag (0 = active, 1 = deleted) |
| deleted_at | Date, null | Deletion timestamp (ISODate) or null |
| deleted_by | String, null | Deleter user email or null |

**Indexes:**
```javascript
{ _id: 1 } // Primary key (default only)
```

**Recommended Additional Indexes:**
```javascript
{ email: 1 } // Unique - for login
{ role: 1 } // For role filtering
{ active: 1 } // For active user queries
{ session_exp: 1 } // For session cleanup
{ "deleted": 1 } // For soft delete queries
```

**Relationships:**
- `user_id` → `user_sessions.user_id` (1:N)
- `user_id` → `project_users.user_id` (1:N)
- `user_id` → `oauth_access_tokens.user_id` (1:N)

**Role Types:**
- `super_admin` - Full system access
- `mi_team` - Internal MI team member
- `non_mi_team` - Internal non-MI member
- `global_viewer` - Read-only global access
- Additional project-specific roles

---

#### 8.2.2 projects

**Purpose:** Global project registry and database routing

**Production Count:** 10 projects

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | ObjectId | Primary key, auto-generated unique identifier |
| name | String | Project display name (sample: "Te Mihi Power Station Stage 2A") |
| desc | String | Project description (full description text) |
| business_unit | String | Business unit code (sample: "MIOS", "MITO") |
| company | String | Client company name (sample: "Contact Energy Ltd") |
| db_name | String | Project database name (sample: "xxx_2b767300-4bf5-11f0-8e64-231ee426764a...") |
| status | String | Project status (sample: "active", "closed") |
| mi_contract_code | String | MI internal contract code (sample: "MI-2023-045") |
| customer_contract_code | String | Client contract reference (sample: "CE-PS-2023") |
| customer_account_number | String | Client account number (sample: "ACC-12345") |
| total_value | String | Total project value as numeric string (sample: "5000000.00") |
| currency | String | Currency code (sample: "USD", "EUR", "AUD", "NZD") |
| version | Number | System version (sample: 1.0, 2.0) |
| uom | String | Unit system (sample: "m_mm", "ft_in") |
| pipe_length_unit | String | Pipe length unit (sample: "mm", "in") |
| default_date | String | Baseline date in YYYY-MM-DD format (sample: "2023-01-01") |
| project_manager_id | String | PM user ObjectId as string |
| color | String | UI color code in hex format (sample: "#3498db") |
| show_type | String | Display category (sample: "Linepipe", "OCTG") |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |
| deleted_at | Date, null | Deletion timestamp (ISODate) or null |
| deleted_by | String, null | Deleter user email or null |

**Indexes:**
```javascript
{ _id: 1 } // Primary key (default only)
```

**Recommended Additional Indexes:**
```javascript
{ db_name: 1 } // Unique - for database routing
{ status: 1 } // For active project filtering
{ project_manager_id: 1 } // For PM queries
```

**Relationships:**
- `_id` → `project_users.project_id` (1:N)
- `project_manager_id` → `users._id` (N:1)
- `db_name` → [Project Database] (1:1)

---

#### 8.2.3 project_users

**Purpose:** Many-to-many relationship between users and projects

**Production Count:** ~100 assignments

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | ObjectId | Primary key, auto-generated unique identifier |
| user_id | String | User ObjectId as string |
| project_id | String | Project ObjectId as string |
| role | String | Project role (sample: "project_manager", "project_team") |
| created_at | Date | Creation timestamp (ISODate) |
| updated_at | Date | Last update timestamp (ISODate) |

**Indexes:**
```javascript
{ _id: 1 } // Primary key (default only)
```

**Recommended Additional Indexes:**
```javascript
{ user_id: 1, project_id: 1 } // Unique compound
{ project_id: 1 } // For project team queries
{ user_id: 1 } // For user's projects queries
```

---

#### 8.2.4 mills

**Purpose:** Global mill/manufacturer registry (API 5L licensed facilities)

**Production Count:** ~200 mills

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | ObjectId | Primary key, auto-generated unique identifier |
| api_no | String | API monogram number (sample: "5L-0011", "5L-0537") |
| type | String | Facility type (sample: "Mill", "Manufacturer") |
| name | String | Full legal entity name (sample: "ABC Steel Manufacturing Co.") |
| short_name | String | Display name (sample: "ABC Steel") |
| location | String | City/region (sample: "Houston, TX, USA") |
| status | String | Approval status (sample: "Approved", "Withdrawn", "Canceled") |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |
| deleted_at | Date, null | Deletion timestamp (ISODate) or null |
| deleted_by | String, null | Deleter user email or null |

**Indexes:**
```javascript
{ _id: 1 } // Primary key (default only)
```

**Recommended Additional Indexes:**
```javascript
{ api_no: 1 } // Unique - API number lookup
{ status: 1 } // For approved mills filtering
{ type: 1 } // For mill type filtering
```

---

#### 8.2.5 roles

**Purpose:** System role definitions

**Production Count:** 4 roles

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Role identifier as primary key (sample: "super_admin", "mi_team") |
| name | String | Display name (sample: "Super Admin", "MI Team") |
| default_access | String | Default permission level (sample: "RW", "R", "N") |
| created_at | Date | Creation timestamp (ISODate) |
| updated_at | Date | Last update timestamp (ISODate) |

**System Roles:**
1. `super_admin` - Super Admin (RW)
2. `mi_team` - MI Team (RW)
3. `non_mi_team` - Non MI Team (R)
4. `global_viewer` - Global Viewer (R)

---

#### 8.2.6 privileges

**Purpose:** Role-based privilege assignments

**Production Count:** ~400 privilege entries

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | ObjectId | Primary key, auto-generated unique identifier |
| role | String | Role identifier (sample: "super_admin", "mi_team") |
| role_name | String | Role display name (sample: "Super Admin") |
| privilege | String | Module/feature identifier (sample: "invite_user", "project_setup", "scope_of_work") |
| default_access | String | Access level (sample: "RW", "R", "N") |
| created_at | Date | Creation timestamp (ISODate) |
| updated_at | Date | Last update timestamp (ISODate) |

**Common Privilege Identifiers:**
- `invite_user` - User management
- `setup_global` - Global settings
- `create_project` - Project creation
- `project_setup` - Project configuration
- `scope_of_work` - SOW management
- `contractual` - Contract management
- `key_terms` - Terms management
- `project_control` - Project control
- `linepipe`, `bends`, `fittings` - Module access

**Recommended Index:**
```javascript
{ role: 1, privilege: 1 } // For role privilege lookup
```

---

#### 8.2.7 locodes

**Purpose:** UN/LOCODE port and location codes for vessel tracking

**Production Count:** ~100,000+ locations

**Size:** 31 MB

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | ObjectId | Primary key, auto-generated unique identifier |
| code | String | UN/LOCODE 5-character code |
| name | String | Location name |
| country_id | String | Country reference (ObjectId as string) |
| created_at | Date | Creation timestamp (ISODate) |
| updated_at | Date | Last update timestamp (ISODate) |

---

#### 8.2.8 countries

**Purpose:** Country reference data

**Production Count:** ~250 countries

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | ObjectId | Primary key, auto-generated unique identifier |
| code | String | ISO 2-letter code (sample: "US", "GB", "AU") |
| name | String | Country name (sample: "United States", "United Kingdom") |
| created_at | Date, null | Creation timestamp (ISODate) or null |
| updated_at | Date, null | Last update timestamp (ISODate) or null |

---

#### 8.2.9 item_attributes

**Purpose:** Dynamic custom item attribute definitions

**Production Count:** ~30 attributes

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | ObjectId | Primary key, auto-generated unique identifier |
| label | String | Display label (sample: "Grade for Carbon (API-5L PSL2)") |
| desc | String | Description (full description text) |
| type | String | Attribute type (sample: "Numeric", "Single Selection", "Multiple Selection") |
| data | String | Possible values (sample: "B, X42, X46, X52, X56, X60, X65, X70, X80") |
| required | Number | Required flag (0 = optional, 1 = required) |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |
| deleted_at | Date, null | Deletion timestamp (ISODate) or null |
| deleted_by | String, null | Deleter user email or null |

---

#### 8.2.10 user_sessions

**Purpose:** Active user sessions tracking

**Production Count:** ~300 sessions

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | ObjectId | Primary key, auto-generated unique identifier |
| user_id | String | User ObjectId as string |
| session_id | String | Long session hash string |
| session_exp | Number | Expiration timestamp (Unix epoch) |
| created_at | Date | Creation timestamp (ISODate) |
| updated_at | Date | Last update timestamp (ISODate) |

**Recommended Index:**
```javascript
{ user_id: 1 } // For user session lookup
{ session_exp: 1 } // For expired session cleanup
```

---

#### 8.2.11 OAuth Collections

**oauth_access_tokens**
- Purpose: OAuth 2.0 access tokens
- Count: ~300 tokens

**oauth_refresh_tokens**
- Purpose: OAuth refresh tokens

**oauth_clients**
- Purpose: OAuth client applications

**oauth_personal_access_clients**
- Purpose: Personal access client configurations

---

#### 8.2.12 counters

**Purpose:** Global auto-increment counters for generating unique item axaptano values across entire system

**Production Count:** Variable

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Counter name (primary key) (sample: "item_counter", "axaptano_counter") |
| seq | Number | Current sequence number (auto-incremented) |
| created_at | Date | Creation timestamp (ISODate) |
| updated_at | Date | Last update timestamp (ISODate) |

**Key Features:**
- Ensures unique axaptano values for items across ALL projects
- Global counter prevents duplicate item codes system-wide
- Used primarily for item master data numbering

**Recommended Index:**
```javascript
{ _id: 1 } // Primary key (default)
```

---

#### 8.2.13 System Collections

**caches / cache**
- Purpose: Application cache storage

**jobs**
- Purpose: Queue job tracking

**job_lockers**
- Purpose: Distributed lock mechanism for jobs

**failed_jobs**
- Purpose: Failed background job logs
- Size: 247 MB (very large - consider cleanup)

**forecast_dates**
- Purpose: Delivery forecast tracking with financial projections (delivered dates, quantities, amounts, pricing per SOW lot)

---

### 8.3 Project Database Collections (per project)

Each project has its own database with 51+ collections. Below are the key collections from the production sample.

---

#### 8.3.1 sow (Scope of Work)

**Purpose:** Contract line items / scope of work definitions

**Production Count:** 60 SOW items

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| type | String | Item type (sample: "linepipe", "elbow", "flange", "tee", "reducer", "bends", "buckle_arrestor", "clad_pipe", "anode") |
| type_name | String | Type display name (sample: "Linepipe", "Elbow") |
| desc | String | Full item description (sample: "16\" SCH STD API 5L X60 PSL2 SAWL") |
| key | String | Processing key (sample: "base_pipe", "mother_pipe", "coated_pipe") |
| id_item | String | Item master reference (ObjectId as string) |
| id_manufacturer | String | Manufacturer reference (ObjectId as string) |
| id_spec | String | Specification reference (ObjectId as string) |
| mto_item_no | String | MTO line number (sample: "001", "002") |
| customer_item_no | String | Client item reference (sample: "ITEM-A-001") |
| customer_item_code | String, null | Client's internal item code or null |
| company_line_item_no | String, null | Internal reference or null |
| qty | Number | Primary quantity (sample: 1000.0) |
| qty_unit | String | Primary unit (sample: "PCS", "M", "MT") |
| qty_pcs | Number, null | Quantity in pieces or null |
| qty_m | Number, null | Quantity in meters or null |
| qty_mt | Number, null | Quantity in metric tons or null |
| conversion | Object | Unit conversion object containing conversion factors |
| conversion.pcs | Number, null | Conversion factor to pieces or null |
| conversion.mt | Number, null | Conversion factor to metric tons or null |
| conversion.m | Number, null | Conversion factor to meters or null |
| qty_mother_pipe | Number, null | Mother pipe quantity (for coated items) or null |
| qty_unit_mother_pipe | String | Mother pipe unit (sample: "PCS") |
| manufacturer_mother_pipe | String, null | Mother pipe manufacturer reference or null |
| spec_mother_pipe | String, null | Mother pipe specification reference or null |
| qty_pup_pipe | Number, null | Pup pipe quantity (for coated items) or null |
| qty_unit_pup_pipe | String | Pup pipe unit (sample: "PCS") |
| manufacturer_pup_pipe | String, null | Pup pipe manufacturer reference or null |
| spec_pup_pipe | String, null | Pup pipe specification reference or null |
| qty_centre_pipe | Number, null | Centre pipe quantity (for coated items) or null |
| qty_unit_centre_pipe | String | Centre pipe unit (sample: "PCS") |
| manufacturer_centre_pipe | String, null | Centre pipe manufacturer reference or null |
| spec_centre_pipe | String, null | Centre pipe specification reference or null |
| price | Number | Unit price value |
| price_unit | String | Price unit (sample: "$/PCS", "$/MT", "$/M") |
| unit_price | Number | Unit price (duplicate of price field) |
| total_price | Number | Extended price (qty × unit_price) |
| delivery_tolerance | Object | Over/under delivery tolerance object |
| delivery_tolerance.plus | Number | Over-delivery tolerance (positive number) |
| delivery_tolerance.minus | Number | Under-delivery tolerance (positive number) |
| delivery_tolerance_unit | String | Tolerance unit (sample: "PCS", "%") |
| any_coating | Boolean | Flag indicating if item has coating (true/false) |
| any_inspection_childs | Object | Inspection requirements per sub-item |
| any_inspection_childs.base_pipe | Boolean | Base pipe requires inspection (true/false) |
| any_inspection_childs.mother_pipe | Boolean | Mother pipe requires inspection (true/false) |
| any_inspection_childs.pup_pipe | Boolean | Pup pipe requires inspection (true/false) |
| any_inspection_childs.centre_pipe | Boolean | Centre pipe requires inspection (true/false) |
| mpqt | Number, null | Mill Production Qualification Test percentage or null |
| rwpqt | Number, null | Repair/Weld Production Qualification Test percentage or null |
| status | String | SOW status (sample: "In Progress", "Completed") |
| form_status | String | Form completion status (sample: "Complete", "Incomplete") |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

**Indexes:**
```javascript
{ _id: 1 } // Primary key (default only)
```

**Recommended Additional Indexes:**
```javascript
{ id_item: 1 } // For item lookup
{ id_manufacturer: 1 } // For manufacturer filtering
{ status: 1 } // For status filtering
{ type: 1 } // For item type filtering
```

**Relationships:**
- `id_item` → `item._id` (N:1)
- `id_manufacturer` → `manufacturer._id` (N:1)
- `id_spec` → `specification._id` (N:1)
- `_id` → `sow_lot.id_parent` (1:N)
- `_id` → `work_order.id_sow` (1:N)

---

#### 8.3.2 sow_lot

**Purpose:** Delivery lot breakdown of SOW items

**Production Count:** ~40 lots

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_parent | String | Parent SOW reference (ObjectId as string) |
| id_sow | String | SOW reference, same as id_parent (ObjectId as string) |
| delivery_term | Object | Incoterm object |
| delivery_term._id | String | Incoterm code (sample: "DDP", "CIF", "FOB") |
| delivery_term.name | String | Incoterm name (sample: "Delivered Duty Paid") |
| delivery_address | String | Delivery location (sample: "Project Site, New Zealand") |
| required_date | Date | Required delivery date (ISODate) |
| qty | Number | Lot quantity (sample: 250.0) |
| qty_unit | String | Unit (sample: "PCS", "M", "MT") |
| qty_pcs | Number, null | Pieces or null |
| qty_m | Number, null | Meters or null |
| qty_mt | Number, null | Metric tons or null |
| conversion | Object | Unit conversion object |
| conversion.pcs | Number, null | Conversion to pieces or null |
| conversion.mt | Number, null | Conversion to metric tons or null |
| conversion.m | Number, null | Conversion to meters or null |
| id_cargo | String, null | Cargo reference (ObjectId as string) or null |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

**Relationships:**
- `id_parent` → `sow._id` (N:1)
- `id_sow` → `sow._id` (N:1)
- `id_cargo` → `cargo._id` (N:1)

---

#### 8.3.3 work_order

**Purpose:** Manufacturing work orders assigned to vendors/mills

**Production Count:** 180 work orders

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| type | String | Work order type (sample: "manufacturing", "coating", "inspection") |
| stage | String | Production stage (sample: "production", "coating") |
| process | String | Process type (sample: "production", "raw_material_production") |
| item_type | String | Item type (sample: "linepipe", "elbow", "flange", "tee", "reducer", "bends", "buckle_arrestor", "clad_pipe", "anode") |
| id_item | String | Item reference (ObjectId as string) |
| id_manufacturer | String | Manufacturer reference (ObjectId as string) |
| id_spec_manufacturing | String | Manufacturing specification (ObjectId as string) |
| qty | Number | Work order quantity (sample: 1000.0) |
| qty_unit | String | Unit (sample: "PCS", "M", "MT") |
| conversion | Object | Unit conversion object |
| conversion.pcs | Number, null | Conversion to pieces or null |
| conversion.mt | Number, null | Conversion to metric tons or null |
| conversion.m | Number, null | Conversion to meters or null |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |

**Recommended Indexes:**
```javascript
{ id_item: 1 } // For item filtering
{ id_manufacturer: 1 } // For manufacturer filtering
{ type: 1, stage: 1 } // For work order type queries
```

**Relationships:**
- `id_item` → `item._id` (N:1)
- `id_manufacturer` → `manufacturer._id` (N:1)
- `id_spec_manufacturing` → `specification._id` (N:1)
- `_id` → `work_order_lot.id_wo` (1:N)
- `_id` → `work_order_schedule.id_wo` (1:N)

---

#### 8.3.4 work_order_lot

**Purpose:** Lot/heat breakdown for work orders

**Production Count:** ~30 lots

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_wo | String | Work order reference (ObjectId as string) |
| item_type | String | Item type (sample: "linepipe") |
| type | String | Type (sample: "production") |
| wo_type | String | Work order type (sample: "manufacturing") |
| qty | Number | Lot quantity |
| qty_unit | String | Unit (sample: "PCS", "M", "MT") |
| conversion | Object | Unit conversion object |
| conversion.pcs | Number, null | Conversion to pieces or null |
| conversion.mt | Number, null | Conversion to metric tons or null |
| conversion.m | Number, null | Conversion to meters or null |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

---

#### 8.3.5 work_order_schedule

**Purpose:** Work order production schedules (baseline, forecast, actual)

**Production Count:** ~250 schedule items

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_wo | String | Work order reference (ObjectId as string) |
| id_wo_lot | String | Work order lot reference (ObjectId as string) |
| schedule | String | Schedule phase (sample: "raw_material_production", "production", "cargo_readiness") |
| type | String | Type (sample: "production") |
| label | String | Display label (sample: "Production", "Raw Material") |
| base_schedule_start_date | Date | Baseline start date (ISODate) |
| base_schedule_end_date | Date | Baseline end date (ISODate) |
| forecast_schedule_start_date | Date | Forecast start date (ISODate) |
| forecast_schedule_end_date | Date | Forecast end date (ISODate) |
| actual_schedule_start_date | Date, null | Actual start date (ISODate) or null |
| actual_schedule_end_date | Date, null | Actual end date (ISODate) or null |
| input_progress_unit | String | Progress unit (sample: "%", "qty") |
| percent_real | Number | Actual progress percentage (0-100) |
| qty_real | Number, String | Actual quantity (850 or "850") |
| qty | Number, null | Planned quantity or null |
| qty_unit | String, null | Unit (sample: "PCS") or null |
| conversion | Object, null | Unit conversion object or null |
| conversion.pcs | Number, null | Conversion to pieces or null |
| conversion.mt | Number, null | Conversion to metric tons or null |
| conversion.m | Number, null | Conversion to meters or null |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

**Schedule Phases:**
- `raw_material_production` - Raw material preparation
- `production` - Main manufacturing
- `cargo_readiness` - Ready for shipment

**Recommended Indexes:**
```javascript
{ id_wo: 1 } // For work order lookup
{ id_wo_lot: 1 } // For lot lookup
```

---

#### 8.3.6 work_order_progress

**Purpose:** Work order progress date entries (parent for daily actuals)

**Production Count:** ~200 entries

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_wo | String | Work order reference (ObjectId as string) |
| input_date | Date | Progress entry date (ISODate) |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

---

#### 8.3.7 work_order_actual

**Purpose:** Detailed actual progress by date (LARGEST COLLECTION)

**Production Count:** 3,015 entries

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_wo | String | Work order reference (ObjectId as string) |
| id_wo_lot | String | Work order lot reference (ObjectId as string) |
| id_wo_progress | String | Progress parent reference (ObjectId as string) |
| id_wo_schedule | String | Schedule reference (ObjectId as string) |
| schedule | String | Schedule type (sample: "production", "raw_material_production") |
| input_date | Date | Date of this entry (ISODate) |
| base_schedule_start_date | Date | Baseline start date inherited from schedule (ISODate) |
| base_schedule_end_date | Date | Baseline end date inherited from schedule (ISODate) |
| forecast_schedule_start_date | Date | Forecast start date inherited from schedule (ISODate) |
| forecast_schedule_end_date | Date | Forecast end date inherited from schedule (ISODate) |
| qty | Number | Planned quantity |
| qty_real | Number | Actual quantity for this date |
| percent_real | Number | Progress percentage for this date (0-100) |
| total_qty_real | Number | Cumulative actual quantity to date |
| total_percent_real | Number | Cumulative progress percentage to date (0-100) |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

**Recommended Indexes:**
```javascript
{ id_wo: 1, input_date: 1 } // For date range queries
{ id_wo_schedule: 1 } // For schedule lookup
```

---

#### 8.3.8 item

**Purpose:** Item master data (product catalog)

**Production Count:** ~30 items

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| type | String | Item type (sample: "linepipe", "elbow", "flange", "tee", "reducer", "bends", "buckle_arrestor", "buckle_arrestor_single_piece", "buckle_arrestor_combi_joint", "clad_pipe", "anode") |
| type_name | String | Type display name (sample: "Linepipe", "Elbow", "Buckle Arrestor") |
| desc | String | Full description (sample: "16\" SCH STD ASTM A234 WPB LR 90°") |
| active | String | Active status ("y" for active, "n" for inactive) |
| axaptano | String | Unique item code reference (sample: "ITEM-001") |
| | | |
| **Common Attributes (vary by type)** | | |
| **For linepipe:** | | |
| od | Number | Outer diameter in mm (sample: 914, 762, 1067) |
| wt | Number | Wall thickness in mm (sample: 12.7, 17.48, 20.62) |
| unit | String | Unit of measurement (sample: "mm", "in") |
| grade | String | Material grade (sample: "X60", "X65", "X70") |
| standard_specification | String | Standard (sample: "API 5L PSL2", "ASTM A106") |
| manufacture_process | String | Manufacturing process (sample: "SAWH", "SAWL", "ERW", "SMLS") |
| length | Number | Pipe length (sample: 12000, 12.0) |
| | | |
| **For elbow, flange, tee, reducer (fittings):** | | |
| size | Number | Nominal size in mm (sample: 600, 750, 1050, 1200) |
| angle | Number | Elbow angle in degrees, elbow only (sample: 45, 90) |
| elbow_type | String | Connection type for elbow (sample: "BW" butt weld, "SW" socket weld) |
| radius_type | String | Radius type for elbow (sample: "LR" long radius, "SR" short radius) |
| design_code | String | Design standard (sample: "ASME B16.9") |
| material | String | Material specification (sample: "ASTM A234 WPB") |
| schedule | String | Schedule designation (sample: "STD", "XS", "Specify") |
| schedule_specify | Number, null | Actual wall thickness when schedule is "Specify" (sample: 12.7) or null |
| flange_type | String | Flange type, flange only (sample: "WN" weld neck, "SO" slip-on) |
| pressure_rating | String | Pressure class for flange (sample: "150#", "300#", "600#") |
| facing | String | Face type for flange (sample: "RF" raised face, "FF" flat face) |
| | | |
| **Audit Trail** | | |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

**Item Types:**
- linepipe
- elbow
- flange
- tee
- reducer
- bends
- buckle_arrestor
- buckle_arrestor_single_piece
- buckle_arrestor_combi_joint
- clad_pipe
- anode

---

#### 8.3.9 specification

**Purpose:** Technical specifications and test requirements (MOST COMPLEX COLLECTION)

**Production Count:** ~120 specifications

**Schema Complexity:** Highly nested with 50+ test parameters

**Key Structure:**
```javascript
{
  _id: String,                      // Primary key
  name: String,                     // Spec name
  reff_no: String,                  // Reference number
  type: String,                     // Item type
  spec_type: String,                // "manufacturing", "purchase"
  stage: String,                    // "Production", "Coating"
  status: String,                   // "Draft", "Submitted", "Approved"

  // Material & process
  grade: String,
  condition: String,                // "M" (as-manufactured), "Q" (quenched)
  pipe_form: String,                // "welded", "seamless"
  manufacture_process: String,
  standard_specification: String,

  // Dimensional tolerances
  od_body_min: {
    value: Number | null,
    type: String,                   // "Value", "N/A"
    unit: String,
    is_sampling: Boolean
  },
  od_body_max: { ... },
  wall_thickness_body_min: { ... },
  wall_thickness_body_max: { ... },
  length_min: { ... },
  length_max: { ... },
  oor_body_max: { ... },            // Out of roundness

  // Chemical composition (heat & product analysis)
  heat_analysis: {
    c_min: { value: Number | null, type: String },
    c_max: { value: Number | null, type: String },
    si_min: { ... }, si_max: { ... },
    mn_min: { ... }, mn_max: { ... },
    p_min: { ... }, p_max: { ... },
    s_min: { ... }, s_max: { ... },
    nb_min: { ... }, nb_max: { ... },
    v_min: { ... }, v_max: { ... },
    ti_min: { ... }, ti_max: { ... },
    // ... many more elements ...
    ce_min: { ... }, ce_max: { ... },      // Carbon equivalent
    pcm_min: { ... }, pcm_max: { ... },    // PCM
    iiw_min: { ... }, iiw_max: { ... }     // IIW
  },

  product_analysis: {
    // Same structure as heat_analysis
  },

  // Mechanical testing
  tensile_test: Array,              // Tensile test requirements
  charpy_test: {
    normal_set: Array,
    transition_set: Array
  },
  hardness_test: Array,
  dwtt_test: {                      // Drop weight tear test
    normal_set: Array,
    transition_set: Array
  },
  ctod_test: Array,                 // Crack tip opening displacement

  // Hydrostatic testing
  pressure: {
    value: Number | null,
    type: String,
    unit: String
  },
  holding_time: {
    value: Number | null,
    type: String
  },

  // Applicable tabs/sections
  tabs: {
    heat_analysis: {
      id: String,
      icon: String,
      title: String,
      done: Boolean,
      applicable: Boolean
    },
    product_analysis: { ... },
    hydrostatic: { ... },
    dimensional: { ... },
    tensile_test: { ... },
    charpy_test: { ... },
    hardness_test: { ... },
    // ... many more test types
  },

  // Attachments
  attachments: Array<{
    file_name: String,
    file_date: String,
    file: String,                   // File path
    _id: String
  }>,

  // Related SOWs
  submitted_sow: Array<{ _id: String, desc: String }>,
  draft_sow: Array<{ _id: String, desc: String }>,

  // Audit
  created_at: Date,
  created_by: String,
  updated_at: Date,
  updated_by: String
}
```

**Key Specification Components:**
- Chemical composition limits (C, Si, Mn, P, S, Nb, V, Ti, Cr, Ni, Cu, Mo, etc.)
- Mechanical properties (tensile strength, yield strength, elongation)
- Dimensional tolerances (OD, wall thickness, length, straightness, ovality)
- Test requirements (Charpy, DWTT, hardness, hydrostatic, CTOD)
- NDT requirements (X-ray, ultrasonic, MPI, visual)

---

#### 8.3.10 inspection

**Purpose:** Inspection records/certificates

**Production Count:** ~5 inspections

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| inspection_type | Object | Type definition object containing id and label (sample: { id: "product_analysis", label: "Product Analysis" }) |
| inspection_name | String | Display name (sample: "Product Analysis") |
| id_item | String | Item reference (ObjectId as string) |
| id_manufacturer | String | Manufacturer reference (ObjectId as string) |
| id_wo | String | Work order reference (ObjectId as string) |
| type | String | Item type (sample: "linepipe", "elbow") |
| type_name | String | Type display name (sample: "Linepipe") |
| item_type | String | Item type duplicate field (sample: "linepipe") |
| item_type_name | String | Type display name duplicate field (sample: "Linepipe") |
| stage | String | Production stage (sample: "production", "coating") |
| manufacturer_name | String | Manufacturer name (sample: "ABC Steel Mill") |
| uploaded_by | String | Uploader name (sample: "John Doe") |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

**Inspection Types:**
- product_analysis
- hydrostatic
- dimensional
- visual
- mpi (magnetic particle)
- ultrasonic
- charpy
- tensile
- hardness

**Recommended Indexes:**
```javascript
{ id_wo: 1 } // For work order inspections
{ id_item: 1 } // For item inspections
{ "inspection_type.id": 1 } // For type filtering
```

---

#### 8.3.11 testing

**Purpose:** Individual test results

**Production Count:** ~80 test results

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| test_type | String | Test type (sample: "product_analysis", "tensile", "charpy") |
| test_attr | String | Attribute code (sample: "Si", "C", "Mn", "YS", "TS") |
| test_label | String | Display label (sample: "Si (%)", "Yield Strength (MPa)") |
| test_value | Number | Measured value (sample: 0.35, 485.5) |
| test_result | Boolean | Pass/fail result (true for pass, false for fail) |
| spec_value | Object | Specification limits object (sample: { min: 0.10, max: 0.50 }) |
| id_queue | String | Inspection reference (ObjectId as string) |
| id_item | String | Item reference (ObjectId as string) |
| id_wo | String | Work order reference (ObjectId as string) |
| id_spec | String | Specification reference (ObjectId as string) |
| heat_no | String | Heat number (sample: "H123456") |
| pipe_no | String | Pipe/piece number (sample: "P001", "P002") |
| key | String | Item key (sample: "linepipe", "base_pipe") |
| type | String | Item type (sample: "linepipe") |
| stage | String | Production stage (sample: "production") |

**Test Types:**
- product_analysis (chemical composition)
- tensile (mechanical properties)
- charpy (impact testing)
- hardness
- hydrostatic
- dimensional

**Recommended Indexes:**
```javascript
{ id_wo: 1 } // For work order tests
{ id_spec: 1 } // For specification compliance
{ heat_no: 1 } // For heat number tracing
```

---

#### 8.3.12 transfer

**Purpose:** Transfer/shipment headers

**Production Count:** ~5 transfers

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| transfer_type | String | Transfer type (sample: "Delivery", "Move") |
| status | String | Transfer status (sample: "schedule", "ongoing", "complete") |
| from_type | String | Origin type (sample: "manufacturer", "warehouse", "yard") |
| id_from | String | Origin location ID (ObjectId as string) |
| consignee_type | String | Destination type (sample: "customer", "warehouse") |
| id_consignee | String | Destination ID or name (ObjectId as string or customer name) |
| customer_pickup | Boolean | Customer pickup flag (true/false) |
| etd | Date | Estimated departure date (ISODate) |
| eta | Date | Estimated arrival date (ISODate) |
| readable_etd | String | Display format ETD in DD/MM/YY (sample: "08/10/25") |
| readable_eta | String | Display format ETA in DD/MM/YY (sample: "15/10/25") |
| remark | String, null | Notes (sample: "Urgent delivery") or null |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

**Transfer Types:**
- `Delivery` - To customer/final destination
- `Move` - Between MI warehouses/yards

**Recommended Index:**
```javascript
{ status: 1 } // For status filtering
{ eta: 1 } // For arrival date queries
```

---

#### 8.3.13 transfer_cargo

**Purpose:** Cargo items for transfers

**Production Count:** ~30 cargo items

| Field | Type | Description |
|-------|------|-------------|
| _id | String | Primary key |
| id_transfer | String | Transfer reference |
| id_delivery_lot | String | Delivery lot reference |
| id_item | String | Item reference |
| id_sow | String | SOW reference |
| id_spec | String | Specification reference |
| id_wo | String | Work order reference |
| qty | Number | Cargo quantity |
| tracks_qty | Object | Quantity per track { track_id: qty } |
| created_at | Date | Creation timestamp |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp |
| updated_by | String | Last updater user email |

**Recommended Index:**
```javascript
{ id_transfer: 1 } // For transfer cargo lookup
{ id_wo: 1 } // For work order cargo
```

---

#### 8.3.14 vessel_tracking

**Purpose:** Real-time vessel position tracking

**Production Count:** ~10 vessels

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| mmsi | String | Maritime Mobile Service Identity number (unique vessel identifier) |
| mmsi_str | String | MMSI as string (duplicate) |
| mmsi_number | Number | MMSI as number |
| name | String | Vessel name (sample: "MSC MEDITERRANEAN", "MAERSK ESSEX") |
| vt_verbose | String | Vessel type description (sample: "Container Ship", "Bulk Carrier") |
| id_route | String | Reference to transfer_route._id |
| id_transfer | String | Reference to transfer._id |
| position | Object | Current vessel position object |
| position.latitude | String | Current latitude coordinate (sample: "-36.8485") |
| position.longitude | String | Current longitude coordinate (sample: "174.7633") |
| position.speed | String | Current speed in knots (sample: "15.5") |
| position.received | String | Last position update timestamp (ISO datetime format) |
| position.location_str | String | Route string (sample: "SHANGHAI>>AUCKLAND", "SGSIN>>AEJEA") |
| last_port | Object | Last port of call information |
| last_port.ata | String | Actual Time of Arrival (ISO datetime) |
| last_port.atd | String | Actual Time of Departure (ISO datetime) |
| last_port.locode | String | UN/LOCODE code for port (sample: "CNSHA", "SGSIN") |
| last_port.name | String | Port name (sample: "Shanghai", "Singapore") |
| next_port | Object | Next port destination information |
| next_port.eta_calc | String, null | Calculated Estimated Time of Arrival (ISO datetime) or null |
| next_port.locode | String | UN/LOCODE code for destination port |
| next_port.name | String | Destination port name |
| schedule | Array | Array of port call schedule objects |
| schedule[].locode | String | UN/LOCODE for this port call |
| schedule[].port_name | String | Port name for this call |
| schedule[].act_type | String | Activity type (sample: "Arrival", "Depart") |
| schedule[].ata | String, undefined | Actual Time of Arrival if passed (ISO datetime) |
| schedule[].atd | String, undefined | Actual Time of Departure if passed (ISO datetime) |
| schedule[].eta | String, undefined | Estimated Time of Arrival if future (ISO datetime) |
| schedule[].passed | Boolean | Flag indicating if this port call has passed (true/false) |
| schedule[].date | String | Display date in DD/MM/YY format |
| schedule[].time | String | Display time in HH:MM format |
| schedule[].sortable_datetime | Number, String | Unix timestamp for sorting or "-" if not applicable |
| status | String | Tracking status (sample: "active", "complete") |
| remaining_date | Object | Time remaining until arrival |
| remaining_date.years | Number | Years remaining |
| remaining_date.months | Number | Months remaining |
| remaining_date.days | Number | Days remaining |
| remaining_date.hours | Number | Hours remaining |
| updatewhen | Number | Last update timestamp (Unix epoch) |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |

**Key Features:**
- Real-time vessel position (latitude, longitude, speed)
- Multi-port schedule with ETAs/ATAs/ETDs/ATDs
- Integration with UN/LOCODE for port identification
- Automatic ETA calculation

**Recommended Indexes:**
```javascript
{ id_transfer: 1 } // For transfer tracking
{ mmsi: 1 } // For vessel lookup
{ status: 1 } // For active vessel filtering
```

---

#### 8.3.15 product_trace

**Purpose:** Individual pipe/product traceability from heat to delivery

**Production Count:** ~5 products

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| | | |
| **Product Identification** | | |
| heat_no | String | Heat number (sample: "H123456") |
| mill_pipe_no | String | Mill-assigned number (sample: "MP-001") |
| pipe_no | String, null | MI-assigned number (sample: "P-001") or null |
| | | |
| **Product Details** | | |
| item_type | String | Item type display name (sample: "Linepipe") |
| item_type_key | String | Item type key (sample: "linepipe") |
| item_desc | String | Full description (sample: "16\" SCH STD X60 PSL2") |
| | | |
| **SOW Reference** | | |
| sow_id | String | SOW reference (ObjectId as string) |
| sow_desc | String | SOW description (full text) |
| | | |
| **Status** | | |
| condition | String, null | Product condition status or null |
| pipe_status | Array | Status progression array (sample: ["Barepipe"], ["Coated"], ["Delivered"]) |
| | | |
| **Dimensions** | | |
| length | Number, null | Actual length (sample: 12.05) or null |
| | | |
| **Location** | | |
| location | String | Current location (sample: "Warehouse A", "In Transit") |
| | | |
| **History** | | |
| id_wos | Array | Work order history array of ObjectIds as strings |
| id_transfers | Array | Transfer history array of ObjectIds as strings |
| | | |
| **Audit Trail** | | |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |

**Key Features:**
- Full traceability from heat number to delivery
- Work order and transfer history
- Current location tracking
- Status progression tracking

**Recommended Indexes:**
```javascript
{ heat_no: 1 } // For heat number lookup
{ mill_pipe_no: 1 } // For mill pipe number lookup
{ sow_id: 1 } // For SOW tracing
```

---

#### 8.3.16 manufacturer

**Purpose:** Project-specific manufacturer records (API mill database copy)

**Production Count:** ~400 manufacturers

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| api | String | Full API number (sample: "5L-0011") |
| api_sel | String | API prefix (sample: "5L") |
| api_text | String | API suffix (sample: "0011") |
| name | String | Facility name (sample: "ABC Steel Manufacturing Co.") |
| city | String | City (sample: "Houston") |
| state | String | State/province (sample: "TX") |
| country | String | Country (sample: "USA") |
| status | String | License status (sample: "Approved", "Withdrawn", "Canceled") |
| detail | Object | Facility details object |
| detail.address | Object | Full address object |
| detail.address.addressLine1 | String | Address line 1 (sample: "123 Steel Road") |
| detail.address.addressLine2 | String | Address line 2 (sample: "Suite 100") |
| detail.address.cityLocality | String | City (sample: "Houston") |
| detail.address.stateProvince | String | State (sample: "TX") |
| detail.address.postalCode | String | ZIP code (sample: "77001") |
| detail.address.country | String | Country (sample: "United States") |
| detail.address.lat | String | Latitude coordinate (sample: "29.7604") |
| detail.address.lon | String | Longitude coordinate (sample: "-95.3698") |
| detail.facilityPhone | String | Phone number (sample: "+1-713-555-0100") |
| detail.salesEmail | String | Sales email (sample: "sales@abcsteel.com") |
| detail.websiteUrl | String | Website URL (sample: "https://www.abcsteel.com") |
| detail.numberOfEmployees | Number | Employee count (sample: 500) |
| licenseNumber | String | License number (sample: "5L-0011") |
| statusName | String | Status name (sample: "Approved") |
| originalIssueDate | String | Original issue date (sample: "2010-05-15") |
| expirationDate | String | Expiration date (sample: "2025-12-31") |
| programName | String | Program name (sample: "API Monogram") |
| spesificationName | String | Specification name (sample: "API-5L") |
| product | Array | Product capabilities array of objects |
| product[].name | String | Process name (sample: "SMLS", "ERW", "SAW") |
| product[].detail | Array | Certification details array of objects |
| product[].detail[].name | String | Certification type (sample: "PSL1", "PSL2") |
| product[].detail[].header | Array | Table headers (sample: ["Type", "Condition", "Max Grade"]) |
| product[].detail[].detail | Array | Detail rows array of objects |
| product[].detail[].detail[].id | Number | Row ID (sample: 1, 2, 3) |
| product[].detail[].detail[].detailrow | Array | Row data (sample: ["Seamless", "M", "X70"]) |
| grab_status | String | Data fetch status (sample: "success", "pending") |
| activation_mill | String, undefined | Active in project (sample: "0" for inactive, "1" for active, or undefined) |

**Key Features:**
- Full API monogram database integration
- Detailed facility information
- Product capability matrix
- License tracking

---

#### 8.3.17 overall_sow

**Purpose:** Aggregated SOW progress (baseline/forecast/actual)

**Production Count:** ~120 entries

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_sow | String | SOW reference (ObjectId as string) |
| id_process | String | Process identifier (ObjectId as string) |
| item_type | String | Item type (sample: "linepipe") |
| level | String | Level type (sample: "sow", "process") |
| process | String | Process type (sample: "manufacturing", "coating", "delivery") |
| progress | Object | Progress data object |
| progress.schedule | Number | Baseline progress percentage (0-100) |
| progress.forecast | Number | Forecast progress percentage (0-100) |
| progress.actual | Number | Actual progress percentage (0-100) |
| progress.qty_completed | Number, undefined | Completed quantity (optional) |
| progress.schedule_date | Object | Schedule date object |
| progress.schedule_date.start_date | Number | Start date (Unix epoch) |
| progress.schedule_date.end_date | Number | End date (Unix epoch) |
| progress.forecast_date | Object | Forecast date object |
| progress.forecast_date.start_date | Number | Start date (Unix epoch) |
| progress.forecast_date.end_date | Number | End date (Unix epoch) |
| progress.actual_date | Object | Actual date object |
| progress.actual_date.start_date | Number | Start date (Unix epoch) |
| progress.actual_date.end_date | Number | End date (Unix epoch) |
| progress.updated_date | Number | Last update timestamp (Unix epoch) |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

---

#### 8.3.18 overall_work_order

**Purpose:** Aggregated work order progress

**Production Count:** ~50 entries

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_wo | String | Work order reference (ObjectId as string) |
| id_manufacturer | String | Manufacturer reference (ObjectId as string) |
| id_process | String | Process identifier (ObjectId as string) |
| item_type | String | Item type (sample: "linepipe") |
| type | String | Type (sample: "manufacturing", "coating") |
| progress | Object | Progress data object |
| progress.schedule | Number | Baseline progress percentage (0-100) |
| progress.forecast | Number | Forecast progress percentage (0-100) |
| progress.actual | Number | Actual progress percentage (0-100) |
| progress.actual_raw | Number | Raw actual before adjustments |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

---

#### 8.3.19 dashboard

**Purpose:** User dashboard widget configurations

**Production Count:** ~100 widgets

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| user_id | String | User reference (ObjectId as string) |
| title | String | Widget title (sample: "SOW Progress", "WO Status") |
| seq | Number | Display sequence (sample: 1, 2, 3...) |
| widget_class | String | CSS class for sizing (sample: "col-md-6", "col-md-12") |
| is_enlarged | Boolean | Full screen mode flag (true/false) |
| graph_type | Object | Widget type object |
| graph_type.value | String | Widget type value (sample: "circle_progress") |
| graph_type.name | String | Widget type name (sample: "Circle Progress") |
| graph_data | Object | Data source object |
| graph_data.value | String | Data source value (sample: "sow_progress") |
| graph_data.name | String | Data source name (sample: "SOW Progress") |
| project_type | Object | Project filter object |
| project_type.value | String | Project type value (sample: "sow") |
| project_type.name | String | Project type name (sample: "SOW") |
| item_type | Object | Item filter object |
| item_type.value | String | Item type value (sample: "linepipe") |
| item_type.name | String | Item type name (sample: "Linepipe") |
| last_updated | String | Last update date in DD/MM/YY format (sample: "08/10/25") |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

---

#### 8.3.20 location

**Purpose:** Storage locations/warehouses

**Production Count:** 17 locations

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| name | String | Location name (sample: "Medan", "Jakarta", "Zimbabwe", "ROMANIA") |
| created_at | Date | Creation timestamp (ISODate) |
| updated_at | Date | Last update timestamp (ISODate) |

**Sample Locations:**
- "Jakarta", "Bandung", "Medan", "Balikpapan", "Malang"
- "Zimbabwe", "ROMANIA", "Chiba"

---

#### 8.3.21 transfer_route

**Purpose:** Shipping routes with vessel tracking

**Production Count:** ~5 routes

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_transfer | String | Transfer reference (ObjectId as string) |
| mmsi | String | Maritime Mobile Service Identity number (sample: "636023758", "371218000") |
| vessel_name | String | Vessel name (sample: "MSC QUITTERIE", "MSC EVA") |
| transport_type | String | Transport mode (sample: "Ship", "Truck", "Rail") |
| track_vessel | Boolean | Enable tracking flag (true/false) |
| point_loading | String | Loading port ID (ObjectId as string from locode collection) |
| point_discharge | String | Discharge port ID (ObjectId as string from locode collection) |
| etd | Date | Estimated departure date (ISODate) |
| eta | Date | Estimated arrival date (ISODate) |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

**Relationships:**
- `id_transfer` → `transfer._id` (N:1)
- `mmsi` → `vessel_tracking.mmsi` (1:1)
- `point_loading` → `locodes._id` (N:1)
- `point_discharge` → `locodes._id` (N:1)

---

#### 8.3.22 transfer_track

**Purpose:** Inventory tracking at locations (material ledger)

**Production Count:** ~40 tracks

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_item | String | Item reference (ObjectId as string) |
| item_type | String | Item type (sample: "linepipe", "elbow", "flange", "tee", "reducer", "bends", "buckle_arrestor", "clad_pipe", "anode") |
| id_sow | Array | Related SOWs (array of SOW ObjectIds as strings) |
| id_spec_manufacturing | String | Specification reference (ObjectId as string) |
| id_wo_loadout | String | Loadout work order (ObjectId as string) |
| location_type | String | Location type (sample: "manufacturer", "warehouse", "yard") |
| id_location | String | Location ID (ObjectId as string) |
| delivery_type | String | Destination type (sample: "customer", "warehouse") |
| id_delivery | String | Delivery destination (ObjectId or customer name as string) |
| process_type | String | Manufacturing process (sample: "manufacturing", "coating") |
| qty_unit | String | Quantity unit (sample: "PCS", "M", "MT") |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |

**Purpose:** This acts as an inventory ledger tracking material quantities at different locations throughout the supply chain.

**Recommended Indexes:**
```javascript
{ id_item: 1, id_location: 1 } // For inventory queries
{ id_wo_loadout: 1 } // For work order loadouts
```

---

#### 8.3.23 transfer_move

**Purpose:** Individual move transactions (material movements)

**Production Count:** ~80 moves

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| type | String | Move type (sample: "manufacturing", "coating") |
| id_location | String | From location (ObjectId as string) |
| id_delivery | String | To location/customer (ObjectId or customer name as string) |
| id_wo_loadout | String | Work order reference (ObjectId as string) |
| qty | Number | Quantity moved (sample: 11.0, 4.0, 12.0) |
| qty_unit | String | Unit (sample: "PCS", "M", "MT") |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |

**Relationships:**
- `id_wo_loadout` → `work_order._id` (N:1)
- `id_location` → `location._id` (N:1)

**Recommended Index:**
```javascript
{ id_wo_loadout: 1 } // For work order moves
{ id_location: 1 } // For location history
```

---

#### 8.3.24 transfer_track_sow

**Purpose:** SOW tracking within inventory transfers

**Production Count:** Variable

| Field | Type | Description |
|-------|------|-------------|
| _id | String | Primary key |
| id_transfer_track | String | Transfer track reference |
| id_sow | String | SOW reference |
| created_at | Date | Creation timestamp |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp |
| updated_by | String | Last updater user email |

**Relationships:**
- `id_transfer_track` → `transfer_track._id` (N:1)
- `id_sow` → `sow._id` (N:1)

---

#### 8.3.25 transfer_track_work_order

**Purpose:** Work order tracking within inventory transfers

**Production Count:** Variable

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_transfer_track | String | Transfer track reference (ObjectId as string) |
| id_wo | String | Work order reference (ObjectId as string) |
| next_track | Boolean | Has next tracking point flag (true/false) |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |

**Relationships:**
- `id_transfer_track` → `transfer_track._id` (N:1)
- `id_wo` → `work_order._id` (N:1)

---

#### 8.3.26 transfer_track_cargo

**Purpose:** Cargo tracking within transfers

**Production Count:** Variable

| Field | Type | Description |
|-------|------|-------------|
| _id | String | Primary key |
| id_transfer_track | String | Transfer track reference |
| id_transfer | String | Transfer reference |
| id_cargo | String | Cargo reference |
| created_at | Date | Creation timestamp |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp |
| updated_by | String | Last updater user email |

**Relationships:**
- `id_transfer_track` → `transfer_track._id` (N:1)
- `id_transfer` → `transfer._id` (N:1)
- `id_cargo` → `cargo._id` (N:1)

---

#### 8.3.27 progress_sow_chart

**Purpose:** SOW progress chart data for S-curve visualization

**Production Count:** Variable (one per SOW)

| Field | Type | Description |
|-------|------|-------------|
| _id | String | Primary key |
| id_sow | String | SOW reference |
| type | String | "sow" |
| | | |
| **Monthly Progress Arrays** | | |
| schedule | Array<Object> | Baseline progress by month |
| - date | String | Month date ("2025-05-01") |
| - value | Number | Progress % |
| forecast | Array<Object> | Forecast progress by month |
| - date | String | Month date |
| - value | Number | Progress % |
| actual | Array<Object> | Actual progress by month |
| - date | String | Month date |
| - value | Number | Progress % |
| | | |
| **Daily Progress Arrays** | | |
| daily | Object | Daily/weekly progress |
| - schedule | Array<Object> | Daily baseline |
| - forecast | Array<Object> | Daily forecast |
| - actual | Array<Object> | Daily actual |
| | | |
| **Audit Trail** | | |
| created_at | Date | Creation timestamp |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp |
| updated_by | String | Last updater user email |

**Sample Progress Entry:**
```javascript
{
  date: "2025-08-01",
  value: 51.0  // Progress percentage
}
```

**Purpose:** Provides time-series data for S-curve charts showing schedule vs forecast vs actual progress.

---

#### 8.3.28 progress_work_order_chart

**Purpose:** Work order progress chart data

**Production Count:** Variable (one per work order)

**Schema:** Similar to `progress_sow_chart` but at work order level

| Field | Type | Description |
|-------|------|-------------|
| _id | String | Primary key |
| id_wo | String | Work order reference |
| type | String | "work_order" |
| schedule | Array<Object> | Baseline progress |
| forecast | Array<Object> | Forecast progress |
| actual | Array<Object> | Actual progress |
| daily | Object | Daily progress breakdown |
| created_at | Date | Creation timestamp |
| created_by | String | Creator user email |
| updated_at | Date | Last update timestamp |
| updated_by | String | Last updater user email |

---

#### 8.3.29 unit_measurement

**Purpose:** Unit of measurement conversions and attribute definitions

**Production Count:** ~400 entries

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| attribute | String | Attribute name (sample: "28 Days - Average for 2 Cubes") |
| key | String | Attribute key identifier (sample: "28_days_avg_for_2_cubes") |
| m_mm | String | Metric unit (sample: "MPa", "mm", "kg") |
| ft_inch | String | Imperial unit (sample: "KSI", "in", "lb") |
| created_at | Date | Creation timestamp (ISODate) |
| updated_at | Date | Last update timestamp (ISODate) |

**Purpose:** Provides unit conversion mappings between metric and imperial units for various test attributes.

---

#### 8.3.30 vendor

**Purpose:** Vendor/supplier registry (project-specific)

**Production Count:** ~5 vendors

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| id_manufacturer | String | Manufacturer reference (ObjectId as string) |
| id_vendor | String | Vendor identifier (sample: "manufacturer_id_manufacturing") |
| name | String | Vendor name (sample: "CSSPG", "Heibei Tianlong") |
| type | String | Vendor type (sample: "manufacturing", "coating", "testing") |
| seq | Number | Display sequence number (sample: 1, 2, 3...) |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String | Last updater user email |

**Relationships:**
- `id_manufacturer` → `manufacturer._id` (N:1)

**Purpose:** Project-specific vendor configuration for work order assignment and UI organization.

---

#### 8.3.31 term

**Purpose:** Contract terms and conditions (Incoterms)

**Production Count:** ~5 terms

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Incoterm code as primary key (sample: "CIF", "DAP", "DDP", "FOB", "EXW") |
| name | String | Full term name (sample: "Cost, Insurance, Freight") |

**Standard Incoterms:**
- `CIF` - Cost, Insurance, Freight
- `DAP` - Delivered At Place
- `DDP` - Delivered Duty Paid
- `FOB` - Free On Board
- `EXW` - Ex Works

---

#### 8.3.32 roles (project-specific)

**Purpose:** Project-specific role definitions

**Production Count:** ~5 roles

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Role identifier as primary key |
| name | String | Role display name |
| default_access | String | Default permission level (sample: "RW" read-write, "R" read-only, "N" no access) |
| created_at | Date | Creation timestamp (ISODate) |
| updated_at | Date | Last update timestamp (ISODate) |

---

#### 8.3.34 cache

**Purpose:** Application cache storage

**Size:** 4.2 MB

**Schema:** Standard Laravel cache structure with key-value pairs.

---

#### 8.3.35 download_info

**Purpose:** File download tracking and audit

**Production Count:** 15 entries

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| user_id | String | User who downloaded (ObjectId as string) |
| file_path | String | Downloaded file path on server |
| file_name | String | Original file name |
| download_at | Date | Download timestamp (ISODate) |
| created_at | Date | Creation timestamp (ISODate) |
| updated_at | Date | Last update timestamp (ISODate) |

---

#### 8.3.36 Other Collections

**packing_list** - Packing list records (empty in sample)

**inspection_mapping** - Inspection to report mapping

**project_control_sow_list** - SOW control lists

**overall_tabs** - Dashboard tab configurations

**general** - General project documents/files (documented earlier in 8.3.20)

---

### 8.4 Common Field Patterns

#### 8.4.1 Audit Trail Pattern
```javascript
{
  created_at: Date,              // ISODate
  created_by: String,            // ObjectId as string or null
  updated_at: Date,              // ISODate
  updated_by: String,            // ObjectId as string or null
  deleted: Number,               // 0 = active, 1 = deleted (some collections)
  deleted_at: Date,              // ISODate or null
  deleted_by: String             // ObjectId as string or null
}
```

#### 8.4.2 Quantity Conversion Pattern
```javascript
{
  qty: Number,                   // Primary quantity
  qty_unit: String,              // Primary unit: "PCS", "M", "MT"
  qty_pcs: Number | null,        // Pieces
  qty_m: Number | null,          // Meters
  qty_mt: Number | null,         // Metric tons
  conversion: {
    pcs: Number | null,
    mt: Number | null,
    m: Number | null
  }
}
```

#### 8.4.3 Progress Tracking Pattern
```javascript
{
  progress: {
    schedule: Number,            // Baseline progress % (0-100)
    forecast: Number,            // Forecast progress % (0-100)
    actual: Number,              // Actual progress % (0-100)
    actual_raw: Number,          // Raw actual (optional)
    qty_completed: Number,       // Completed quantity (optional)
    schedule_date: {
      start_date: Number,        // Unix timestamp
      end_date: Number           // Unix timestamp
    },
    forecast_date: {
      start_date: Number,
      end_date: Number
    },
    actual_date: {
      start_date: Number,
      end_date: Number
    },
    updated_date: Number         // Last update timestamp
  }
}
```

#### 8.4.4 Schedule Pattern
```javascript
{
  base_schedule_start_date: Date,        // Baseline
  base_schedule_end_date: Date,
  forecast_schedule_start_date: Date,    // Forecast
  forecast_schedule_end_date: Date,
  actual_schedule_start_date: Date | null,  // Actual
  actual_schedule_end_date: Date | null
}
```

#### 8.4.5 Specification Value Pattern
```javascript
{
  value: Number | null,          // Numeric value
  type: String,                  // "Value", "N/A", "Calculated"
  unit: String | null,           // Unit of measurement
  is_sampling: Boolean           // For dimensional fields
}
```

#### 8.4.6 Soft Delete Pattern
```javascript
{
  deleted: Number,               // 0 = active, 1 = deleted
  deleted_at: Date | null,       // Deletion timestamp
  deleted_by: String | null      // Deleter user email
}
```

---

### 8.5 ID Reference Patterns

**All references between collections use String type** (ObjectId converted to string):

```javascript
// User references
"user_id": "507f1f77bcf86cd799439011"  // ObjectId as string

// Project references
"project_id": "507f1f77bcf86cd799439012"

// Document references
"id_item": "507f1f77bcf86cd799439013"
"id_manufacturer": "507f1f77bcf86cd799439014"
"id_spec": "507f1f77bcf86cd799439015"
```

**Naming Convention:**
- `id_[collection]` - Reference to another collection
- `_id` - Primary key (ObjectId)
- `id_parent` - Parent document in same collection

---

### 8.6 Index Analysis

#### 8.6.1 Current State
**ALL COLLECTIONS** have only the default `_id` index:
```javascript
{
  "v": 2,
  "key": { "_id": 1 },
  "name": "_id_"
}
```

#### 8.6.2 Critical Missing Indexes

This is a **significant performance concern** for production. Recommended indexes:

**Global Database:**
```javascript
// users
users.createIndex({ email: 1 }, { unique: true })
users.createIndex({ role: 1 })
users.createIndex({ active: 1 })
users.createIndex({ session_exp: 1 })
users.createIndex({ deleted: 1 })

// projects
projects.createIndex({ db_name: 1 }, { unique: true })
projects.createIndex({ status: 1 })
projects.createIndex({ project_manager_id: 1 })

// project_users
project_users.createIndex({ user_id: 1, project_id: 1 }, { unique: true })
project_users.createIndex({ project_id: 1 })

// user_sessions
user_sessions.createIndex({ user_id: 1 })
user_sessions.createIndex({ session_exp: 1 })

// privileges
privileges.createIndex({ role: 1, privilege: 1 })
```

**Project Database:**
```javascript
// sow
sow.createIndex({ id_item: 1 })
sow.createIndex({ id_manufacturer: 1 })
sow.createIndex({ status: 1 })
sow.createIndex({ type: 1 })

// work_order
work_order.createIndex({ id_item: 1 })
work_order.createIndex({ id_manufacturer: 1 })
work_order.createIndex({ type: 1, stage: 1 })

// work_order_schedule
work_order_schedule.createIndex({ id_wo: 1 })
work_order_schedule.createIndex({ id_wo_lot: 1 })

// work_order_actual (CRITICAL - largest collection)
work_order_actual.createIndex({ id_wo: 1, input_date: 1 })
work_order_actual.createIndex({ id_wo_schedule: 1 })

// inspection
inspection.createIndex({ id_wo: 1 })
inspection.createIndex({ id_item: 1 })
inspection.createIndex({ "inspection_type.id": 1 })

// testing
testing.createIndex({ id_wo: 1 })
testing.createIndex({ id_spec: 1 })
testing.createIndex({ heat_no: 1 })

// product_trace
product_trace.createIndex({ heat_no: 1 })
product_trace.createIndex({ mill_pipe_no: 1 })
product_trace.createIndex({ sow_id: 1 })

// transfer
transfer.createIndex({ status: 1 })
transfer.createIndex({ eta: 1 })

// transfer_cargo
transfer_cargo.createIndex({ id_transfer: 1 })
transfer_cargo.createIndex({ id_wo: 1 })

// vessel_tracking
vessel_tracking.createIndex({ id_transfer: 1 })
vessel_tracking.createIndex({ mmsi: 1 })
vessel_tracking.createIndex({ status: 1 })
```

---

### 8.7 Data Type Usage

| Type | Usage | Example Fields |
|------|-------|----------------|
| **ObjectId** | Primary keys only | `_id` |
| **String** | References, names, codes, status | `user_id`, `email`, `name`, `status` |
| **Number** | Quantities, prices, percentages, timestamps | `qty`, `price`, `percent_real`, `session_exp` |
| **Date** | Audit timestamps, schedule dates | `created_at`, `updated_at`, `etd`, `eta` |
| **Boolean** | Flags, toggles | `any_coating`, `is_locked`, `active` (some) |
| **Array** | Lists, collections | `role`, `additional_privileges`, `id_wos` |
| **Object** | Embedded documents, nested data | `progress`, `conversion`, `position`, `spec_value` |

**Type Inconsistencies Observed:**
- Boolean fields: Mix of Boolean, Number (0/1), and String ("y"/"n")
- Dates: Mix of Date objects, Unix timestamps (Number), and formatted strings
- References: All stored as String (ObjectId converted)

---

### 8.8 Collection Size Summary

**Global Database (pipeline_global):**
- **Largest**: `failed_jobs` (247 MB) - System logs (cleanup recommended)
- **Second**: `locodes` (31 MB) - Port reference data
- **Active Users**: 158 users
- **Active Projects**: 10 projects

**Project Database (sample):**
- **Largest**: `work_order_actual` (3,015 documents) - Daily progress tracking
- **Most Complex**: `specification` (120+ specs with 50+ nested test parameters)
- **Active SOWs**: 60 contract line items
- **Work Orders**: 180 manufacturing orders
- **Manufacturers**: ~400 API mills (full database copy)

---

### 8.9 Database Relationships

#### Global Database Relationships
```
users
  ↓ (user_id)
project_users
  ↓ (project_id)
projects
  → (db_name) → [Project Database]

users ← (role) → roles → (role id) → privileges
users → (user_id) → user_sessions
users → (user_id) → oauth_access_tokens
```

#### Project Database Relationships
```
sow
  ← (id_item) → item
  ← (id_manufacturer) → manufacturer
  ← (id_spec) → specification
  ↓ (sow._id)
sow_lot

sow ↓ (id_sow) → work_order
  ↓ (id_wo) → work_order_lot
  ↓ (id_wo_lot) → work_order_schedule
  ↓ (id_wo_schedule) → work_order_actual

work_order ↓ (id_wo) → inspection
  ↓ (id_queue) → testing

transfer ↓ (id_transfer) → transfer_cargo
  → (id_delivery_lot) → sow_lot
  → (id_wo) → work_order

transfer ↓ (id_transfer) → transfer_route
  ↓ (id_route) → vessel_tracking

work_order ↓ (id_wos array) → product_trace
  ← (heat_no) ← testing
```

---

## Appendix: Production Database Statistics

### Collection Count
- **Global Database**: 24 collections
- **Project Database**: 51 collections
- **Total System**: 75+ collections (with OAuth and system collections)

### Record Counts (Sample Project)
- **users**: 158 users
- **projects**: 10 projects
- **sow**: 60 line items
- **work_order**: 180 orders
- **work_order_actual**: 3,015 progress entries (largest)
- **specification**: 120 specifications
- **manufacturer**: ~400 mills
- **locodes**: ~100,000+ locations

### Database Sizes
- **Global**: ~300 MB (including 247 MB failed_jobs)
- **Project**: ~10-20 MB per active project

---

**End of Database Schema Documentation**
