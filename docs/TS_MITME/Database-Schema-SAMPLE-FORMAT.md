# Database Schema - Corrected 3-Column Format (SAMPLE)

## Example Collections with Correct Format

---

### Collection: users (Global Database)

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
| photo_name | String | Photo upload placeholder text |
| active | String | Account status (sample: "y" for active, "n" for inactive) |
| invite_by | String | Email of user who sent invitation |
| invite_date | Number | Invitation timestamp (Unix epoch) |
| activation_code | Number | 4-digit OTP code for account activation |
| activation_code_exp | Number | Activation code expiration timestamp (Unix epoch) |
| is_locked | Boolean, Number | Account lock status (Boolean or 0/1) |
| last_attempt | Number | Last login attempt timestamp (Unix epoch) |
| attempt_number | Number | Count of failed login attempts |
| passcode_resend_last_attempt | Number, null | Last OTP resend timestamp or null |
| passcode_resend_attempt_number | Number, null | Count of OTP resend attempts |
| token | String, null | Current session token hash or null |
| token_exp | Number | Token expiration timestamp (Unix epoch) |
| session_exp | Number | Session expiration timestamp (Unix epoch) |
| manufacturer | String, null | Associated manufacturer ID for vendor users, null for internal users |
| db_access | String | Last accessed project database name |
| allowed_projects | String | Project access list (sample: "all" or comma-separated project IDs) |
| app_token | String, null | Mobile app authentication token or null |
| app_token_exp | Number | Mobile app token expiration timestamp (Unix epoch) |
| last_seen_at | String | Last activity date in DD/MM/YYYY format |
| created_at | Date | Creation timestamp (ISODate) |
| created_by | String, null | Creator user email or null for system-created |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |
| deleted | Number | Soft delete flag (0 = active, 1 = deleted) |
| deleted_at | Date, null | Deletion timestamp (ISODate) or null |
| deleted_by | String, null | Deleter user email or null |

**Indexes:**
```javascript
{ _id: 1 } // Default primary key index
```

**Recommended Indexes:**
```javascript
{ email: 1 } // Unique - for login lookup
{ role: 1 } // For role-based queries
{ active: 1 } // For active user filtering
{ session_exp: 1 } // For session cleanup
{ deleted: 1 } // For soft delete queries
```

**Relationships:**
- `_id` → `user_sessions.user_id` (One-to-Many)
- `_id` → `project_users.user_id` (One-to-Many)
- `_id` → `oauth_access_tokens.user_id` (One-to-Many)

---

### Collection: sow (Project Database)

**Purpose:** Scope of Work - Contract line items and manufacturing order definitions

**Production Count:** 60 SOW items

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| type | String | Item type (sample: "linepipe", "elbow", "flange", "reducer", "tee", "cap") |
| type_name | String | Item type display name (sample: "Linepipe", "Elbow", "Flange") |
| desc | String | Full item description with specifications |
| key | String | Processing key indicating pipe stage (sample: "base_pipe", "mother_pipe", "coated_pipe") |
| id_item | String | Reference to item._id (ObjectId as string) |
| id_manufacturer | String | Reference to manufacturer._id (ObjectId as string) |
| id_spec | String | Reference to specification._id (ObjectId as string) |
| mto_item_no | String | Material Take-Off (MTO) line number |
| customer_item_no | String | Client's item reference number |
| customer_item_code | String, null | Client's internal item code or null |
| company_line_item_no | String, null | Company internal line item reference or null |
| qty | Number | Primary quantity value |
| qty_unit | String | Primary unit of measurement (sample: "PCS", "M", "MT") |
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
{ _id: 1 } // Default primary key index
```

**Recommended Indexes:**
```javascript
{ id_item: 1 } // For item lookup
{ id_manufacturer: 1 } // For manufacturer filtering
{ status: 1 } // For status filtering
{ type: 1 } // For item type filtering
```

**Relationships:**
- `id_item` → `item._id` (Many-to-One)
- `id_manufacturer` → `manufacturer._id` (Many-to-One)
- `id_spec` → `specification._id` (Many-to-One)
- `_id` → `sow_lot.id_parent` (One-to-Many)
- `_id` → `work_order.id_sow` (One-to-Many)

---

### Collection: vessel_tracking (Project Database)

**Purpose:** Real-time vessel position tracking and schedule management

**Production Count:** ~10 vessels

| Field | Data Type | Description |
|-------|-----------|-------------|
| _id | String | Primary key (ObjectId as string) |
| mmsi | String | Maritime Mobile Service Identity number (unique vessel identifier) |
| mmsi_str | String | MMSI as string (duplicate) |
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
| created_by | String, null | Creator user ID or null |
| updated_at | Date | Last update timestamp (ISODate) |
| updated_by | String, null | Last updater user email or null |

**Indexes:**
```javascript
{ _id: 1 } // Default primary key index
```

**Recommended Indexes:**
```javascript
{ id_transfer: 1 } // For transfer lookup
{ mmsi: 1 } // For vessel lookup
{ status: 1 } // For active vessel filtering
```

**Relationships:**
- `id_route` → `transfer_route._id` (Many-to-One)
- `id_transfer` → `transfer._id` (Many-to-One)
- `mmsi` → `transfer_route.mmsi` (One-to-One)

---

## Format Notes:

1. **Field Column**: Lists ALL fields in flat structure
   - Nested fields use dot notation (e.g., `position.latitude`)
   - Array elements shown as `array_name[].field_name`

2. **Data Type Column**: Shows exact MongoDB data types
   - Multiple types separated by comma (e.g., `String, null`)
   - Objects and Arrays listed as such, then sub-fields listed separately

3. **Description Column**: Includes:
   - Field purpose and meaning
   - Sample values in parentheses
   - Nullability notes (e.g., "or null")
   - Format specifications (e.g., "Unix epoch", "ISO datetime", "DD/MM/YY")
   - Valid enum values for status fields

This format ensures:
- ✅ All fields visible at one level (flat structure)
- ✅ Complete field coverage (nothing missing)
- ✅ Clear data types
- ✅ Practical descriptions with examples
