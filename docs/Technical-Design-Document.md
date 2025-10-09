# Technical Design Document - MCMS Linepipe

## Document Information
- **Project Name**: MCMS Linepipe (Material/Manufacturing Control Management System)
- **Version**: 1.0
- **Date**: 2025-10-08
- **Application Type**: Monolithic Full-Stack Web Application
- **Status**: Production

---

## Table of Contents
1. [Introduction](#1-introduction)
2. [System Configuration](#2-system-configuration)
3. [Coding Standards](#3-coding-standards)
4. [Architecture Overview](#4-architecture-overview)
5. [Component Design](#5-component-design)
6. [API Endpoints](#6-api-endpoints)
7. [Data Flow Diagrams](#7-data-flow-diagrams)
8. [Database Schema](#8-database-schema)

---

## 1. Introduction

### 1.1 Purpose
This Technical Design Document (TDD) provides a comprehensive overview of the MCMS Linepipe system - a specialized manufacturing and material control management platform for pipe and linepipe manufacturing operations. The system manages end-to-end workflows including:
- Material procurement and vendor management
- Manufacturing work orders and quality control
- Testing and inspection tracking
- Logistics and material transfer
- Commercial management (contracts, invoices, variations)
- Progress reporting and analytics

### 1.2 Scope
This document covers:
- Complete system architecture and technology stack
- Database schema across 110+ MongoDB collections
- 887 lines of API routes across 13+ major business domains
- Application layer organization (Controllers, Services, Repositories, Models)
- Vue.js frontend component structure
- Integration patterns and workflows

### 1.3 Definitions and Acronyms
| Term | Description |
|------|-------------|
| API  | Application Programming Interface |
| SOW  | Scope of Work - Manufacturing order definition |
| MDR  | Material Data Record - Manufacturing documentation |
| NCR  | Non-Conformance Report - Quality issue tracking |
| PQT  | Process Qualification Test |
| LOCODE | UN Location Code - International location standard |
| JWT  | JSON Web Token - Authentication mechanism |
| SPA  | Single Page Application |
| MCMS | Material/Manufacturing Control Management System |
| OD   | Outside Diameter (pipe specification) |
| WT   | Wall Thickness (pipe specification) |

---

## 2. System Configuration

### 2.1 Server Configuration

#### Hosting Specification
| Environment | Provider | Region | CPU | RAM | Disk |
|------------|----------|--------|-----|-----|------|
| Production | TBD | TBD | TBD | TBD | TBD |
| Staging | TBD | TBD | TBD | TBD | TBD |
| Development | Local/WSL | N/A | Variable | Variable | Variable |

#### Framework & Stack
- **Backend Framework**: Laravel 11 (PHP 8.2+)
- **Frontend Framework**: Vue.js 3.x (Composition API)
- **Server Type**: Apache2
- **Build Tool**: Vite 4.5.5
- **Port Configuration**:
  - HTTP: 80
  - HTTPS: 443
  - Vite Dev Server: Configurable via VITE_APP_HOST

### 2.2 Database Configuration
- **Primary Database**: MongoDB 4.x+
- **Version**: 4.x (with extension: mongodb 1.7+)
- **Architecture**: Dual database strategy
  - `mongodb_global`: Shared/global data (19 collections)
  - `mongodb_project`: Project-specific data (85+ collections)
- **DSN**: Configurable via `MONGO_DB_DSN` environment variable
- **Default**: `mongodb://localhost:27017`
- **Features**: Retry writes enabled, write concern: majority
- **MySQL**: Used exclusively for Laravel migrations tracking

### 2.3 Authentication and Security
- **Authentication Method**: Laravel Passport (OAuth 2.0)
- **Token Type**: JWT Bearer tokens
- **Password Encryption**: Bcrypt (Laravel default)
- **Token Storage**: MongoDB (`oauth_access_tokens` collection)
- **Session Management**: File-based sessions
- **Session Lifetime**: 120 minutes (configurable)
- **Client Session Max Time**: 10800 seconds (3 hours)
- **CORS Policy**: Enabled for Vite dev server

### 2.4 Logging and Monitoring
- **Logging Framework**: Laravel Log (Monolog)
- **Log Channel**: Stack (configurable)
- **Error Tracking**: Sentry Laravel SDK
- **Browser Tracking**: Whichbrowser/parser for user agent analysis
- **Security Scanning**: Laravel Security Checker (Jorijn)
- **Development Tools**: Laravel Dump Server, Telescope (optional)

### 2.5 File Storage
- **Storage Driver**: Local filesystem (primary)
- **Cloud Storage**: AWS S3 v3 (via Flysystem)
- **Azure Storage**: Available via matthewbdaly/laravel-azure-storage
- **Storage Paths**:
  - File Path: `/home/solork/Projects/mcms-lp/storage/app/`
  - Script Path: `/home/solork/Projects/mcms-lp/scripts/`
  - Backup Directory: `/home/solork/Databases/`

---

## 3. Coding Standards

### 3.1 Backend Standards (PHP/Laravel)
- **PSR Compliance**: PSR-12 Extended Coding Style
- **Static Analysis**: PHPStan (Larastan) - Level configurable
- **Code Formatting**: Laravel Pint
- **Pre-commit Hooks**: Husky + lint-staged
  - Automatic PHPStan analysis
  - Automatic Pint formatting

### 3.2 Frontend Standards (JavaScript/Vue)
- **ESLint Configuration**: Standard JS style
- **Plugins**:
  - eslint-plugin-vue (Vue 3 recommended rules)
  - eslint-plugin-import
  - eslint-plugin-promise
- **Auto-fix**: Enabled on pre-commit via lint-staged
- **Component Registration**: Auto-import via unplugin-vue-components

### 3.3 Code Style
- **Indentation**: 2 spaces (JavaScript/Vue), 4 spaces (PHP)
- **Line Length**: No strict limit, but aim for readability
- **Naming Conventions**:
  - **PHP Variables**: camelCase
  - **PHP Classes**: PascalCase
  - **PHP Constants**: UPPER_SNAKE_CASE
  - **JavaScript Variables**: camelCase
  - **Vue Components**: PascalCase (file naming)
  - **Database Collections**: snake_case
  - **API Routes**: snake_case with kebab-case segments

### 3.4 Best Practices
- **Repository Pattern**: Service → Repository → Model separation
- **Dependency Injection**: Constructor injection for services
- **API Versioning**: Version-specific route files (api.v3.php)
- **Soft Deletes**: Enabled on critical models (users, projects, mills)
- **Audit Trail**: Track created_by, updated_by, deleted_by fields
- **Error Handling**: Centralized exception handling
- **Code Splitting**: Feature-based chunks in Vite configuration
- **Type Safety**: MongoDB model casting for data types

---

## 4. Architecture Overview

### 4.1 System Architecture

This is a **monolithic full-stack web application** with the following architecture:

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                             │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Vue 3 SPA (Single Page Application)                      │  │
│  │  - Vue Router (client-side routing)                       │  │
│  │  - Vuex (state management)                                │  │
│  │  - Element Plus (UI components)                           │  │
│  │  - Axios (HTTP client)                                    │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ↕ HTTP/HTTPS + JWT
┌─────────────────────────────────────────────────────────────────┐
│                      APPLICATION LAYER                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Laravel 11 (PHP 8.2)                                     │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  HTTP Layer                                         │  │  │
│  │  │  - Routes (api.v3.php - 887 lines)                 │  │  │
│  │  │  - Middleware (Auth, CORS, API versioning)         │  │  │
│  │  │  - Controllers (53 controllers)                    │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  Business Logic Layer                              │  │  │
│  │  │  - Services (75+ service classes)                  │  │  │
│  │  │  - Event Handlers                                  │  │  │
│  │  │  - Jobs (Queue processing)                         │  │  │
│  │  │  - Imports/Exports (Excel, CSV)                    │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  Data Access Layer                                 │  │  │
│  │  │  - Repositories (Pattern implementation)           │  │  │
│  │  │  - Models (110+ Eloquent MongoDB models)           │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ↕ MongoDB Driver
┌─────────────────────────────────────────────────────────────────┐
│                       DATABASE LAYER                             │
│  ┌──────────────────┐              ┌──────────────────────┐     │
│  │  mongodb_global  │              │  mongodb_project     │     │
│  │  - 19 collections│              │  - 85+ collections   │     │
│  │  - Users         │              │  - Work Orders       │     │
│  │  - Projects      │              │  - SOW               │     │
│  │  - Mills         │              │  - Inspections       │     │
│  │  - Master Data   │              │  - Transfers         │     │
│  └──────────────────┘              └──────────────────────┘     │
│                                                                   │
│  ┌──────────────────┐                                            │
│  │  MySQL           │  (OAuth tokens & migrations only)          │
│  └──────────────────┘                                            │
└─────────────────────────────────────────────────────────────────┘
```

### 4.2 Application Layer Structure

```
app/
├── Console/              # Artisan commands
├── Contracts/            # Interface definitions
│   ├── Globals/
│   └── Projects/
├── Enums/                # Enumeration classes
├── Exceptions/           # Custom exception handlers
├── Exports/              # Excel/CSV export classes (Maatwebsite)
├── Helpers/              # Helper functions
├── Http/
│   ├── Controllers/      # 53 API controllers
│   │   └── Api/
│   │       └── Projects/ # Project-specific controllers
│   ├── Middleware/       # Request middleware
│   └── Requests/         # Form request validation
├── Imports/              # Excel/CSV import classes
├── Jobs/                 # Queue job classes
├── Logging/              # Custom logging
├── Mail/                 # Email templates
├── Models/               # 110+ Eloquent models
│   ├── Globals/          # 19 global models
│   ├── Projects/         # 85+ project models
│   └── Passport/         # 5 OAuth models
├── Providers/            # Service providers
├── Repositories/         # Repository pattern
│   ├── Globals/          # Global repositories
│   └── Projects/         # Project repositories
├── Services/             # 75+ business logic services
│   ├── Commons/
│   ├── Databases/
│   ├── Globals/
│   └── Projects/
└── Traits/               # Reusable traits
```

### 4.3 Frontend Layer Structure

```
resources/js/
├── app.js                # Entry point
├── bootstrap.js          # Bootstrap configuration
├── router.js             # Vue Router configuration
├── store/                # Vuex store modules
├── components/
│   ├── auth/             # Authentication components
│   ├── errors/           # Error pages
│   ├── global/           # Global module components
│   │   ├── dashboard/
│   │   ├── forecast_d365/
│   │   ├── item_attributes/
│   │   ├── locodes/
│   │   ├── mill/
│   │   ├── project/
│   │   └── user/
│   ├── project/          # Project module components
│   │   ├── commercial/   # Contracts, invoices, variations
│   │   ├── dashboard/
│   │   ├── documentation/# Procedures, NCR, reports
│   │   ├── progress/     # Progress tracking
│   │   ├── setup/        # Project configuration
│   │   ├── sow/          # Scope of Work
│   │   ├── technical/    # MDR, inspections, product trace
│   │   ├── transfer/     # Material transfers
│   │   └── work_order/   # Work order management
│   └── reusable/         # Shared components
│       ├── daterange/
│       └── vue-good-table/
└── mixins/               # Vue mixins
```

### 4.4 Technology Stack

#### Backend Technologies
- **Core Framework**: Laravel 11
- **PHP Version**: 8.2+
- **Database ORM**: MongoDB Laravel (Eloquent MongoDB)
- **Authentication**: Laravel Passport (OAuth 2.0)
- **API Documentation**: ApiDoc.js
- **Queue**: MongoDB-based queue
- **Excel Processing**: Maatwebsite Excel
- **PDF Generation**: Barryvdh Laravel Snappy (wkhtmltopdf)
- **HTTP Client**: Guzzle 7.3
- **UUID Generation**: Webpatser Laravel UUID

#### Frontend Technologies
- **Core Framework**: Vue.js 3.5.13
- **Router**: Vue Router 4.2.5
- **State Management**: Vuex 4.1.0
- **UI Library**: Element Plus 2.4.1
- **HTTP Client**: Axios 1.5.0
- **Charts**: Highcharts 11.1.0 + Highcharts Vue
- **Maps**: Vue Google Maps, Leaflet 1.9.4
- **Date Handling**: date-fns 2.30.0, vue-moment 4.1.0
- **Form Validation**: Simple Vue3 Validator, Vuelidate 2.0
- **Utilities**: Lodash 4.17.21
- **Notifications**: SweetAlert2 11.6.13
- **File Processing**: XLSX 0.18.5
- **CSS Framework**: Bootstrap 5.3.3
- **Build Tool**: Vite 4.5.5

#### Development Tools
- **Static Analysis**: PHPStan (Larastan 2.9.4)
- **Code Formatting**: Laravel Pint 1.10
- **Linting**: ESLint 8.49.0
- **Git Hooks**: Husky 8.0.3
- **Testing**: PHPUnit 10.0

#### Cloud Services
- **File Storage**: AWS S3, Azure Storage
- **Error Tracking**: Sentry
- **Analytics**: Google Tag Manager, Google Analytics (Vue GTM/GTag)

---

## 5. Component Design

### 5.1 Global Module

The Global module manages cross-project data and configurations accessible by all projects.

#### 5.1.1 User Management

##### User Interface
- **Login Page**: Email/password authentication
- **User List**: Datatable with search, filter, pagination
- **User Form**: Create/edit user with role assignment
- **User Profile**: View/edit personal information

##### Security
- **Authentication**: Laravel Passport OAuth 2.0
- **Authorization**: Role-based access control (RBAC)
- **Roles**: super_admin, project_manager, mi_team, global_viewer, vendor_approver, customer, project_team variants
- **Middleware**: JWT token validation on all protected routes
- **Session Management**: 3-hour client session timeout

##### Application Services
1. **UserService**: User CRUD operations
2. **AuthenticationService**: Login, logout, token refresh
3. **RoleService**: Role management
4. **PrivilegeService**: Permission management

**Service Flow:**
1. User submits login credentials
2. AuthenticationService validates credentials
3. Passport generates OAuth tokens
4. Client stores token in secure local storage
5. Subsequent requests include Bearer token
6. Middleware validates token and loads user context

##### Database
- **Collection**: `users` (mongodb_global)
- **Related**: `user_sessions`, `oauth_access_tokens`, `oauth_refresh_tokens`
- **Fields**: email, first_name, last_name, role, active, db_access, photo, additional_privileges
- **Relationships**: HasMany tokens, user_projects; BelongsToMany projects

#### 5.1.2 Project Management

##### User Interface
- **Project List**: All accessible projects with status indicators
- **Project Form**: Create/edit project with database configuration
- **Project Dashboard**: Overview of project metrics
- **Project Access**: Assign users to projects with roles

##### Security
- **Authorization**: Project-level access control
- **Database Isolation**: Each project can have separate database
- **Role Mapping**: Global roles map to project-specific permissions

##### Application Services
1. **ProjectService**: Project CRUD
2. **ProjectUserService**: User-project assignment
3. **DatabaseAccessService**: Manage project database connections

**Service Flow:**
1. User creates project with configuration
2. System creates project record in global database
3. System optionally creates dedicated project database
4. Project manager assigns users with project roles
5. Users see project in their accessible projects list

##### Database
- **Collection**: `projects` (mongodb_global)
- **Related**: `project_users`, `database_access`
- **Fields**: name, color, db_name, project_manager_id, status, show_type, welding_method
- **Relationships**: BelongsToMany users; HasOne project_manager

#### 5.1.3 Mill Management

This module implements **Pattern 1: CRUD Operations with Approval Flow** from the Component Patterns document.

##### User Interface
- **Mill List**: Datatable with approval status
- **Mill Form**: Create/edit mill information
- **Mill Detail**: View mill with history
- **Approval Interface**: Approve/reject pending mills

##### Security
- **Authorization**: vendor_approver role required for approval
- **Audit Trail**: Full history tracking of changes

##### Application Services
1. **MillService**: Mill CRUD operations
2. **MillApprovalService**: Approval workflow
3. **MillHistoryService**: History tracking
4. **NotificationService**: Alert approvers

**Service Flow:**
1. User creates mill record → Status: Pending for Approval
2. NotificationService alerts vendor_approver users
3. Approver reviews mill details
4. Approver approves/rejects → Status: Approved/Rejected
5. MillHistoryService logs all changes

##### Database
- **Collection**: `mills` (mongodb_global)
- **Related**: `mill_histories`
- **Fields**: api_no, name, location, short_name, status, type
- **Status Values**: Approved, Rejected, Pending for Approval, To Review
- **Relationships**: HasMany histories

#### 5.1.4 Location Code Management

##### User Interface
- **Locode List**: UN/LOCODE registry
- **Locode Form**: Create/edit location codes
- **Country Selector**: Dropdown with country associations

##### Application Services
1. **LocodeService**: LOCODE CRUD
2. **CountryService**: Country reference data

##### Database
- **Collection**: `locodes` (mongodb_global)
- **Related**: `countries`
- **Relationships**: BelongsTo country

### 5.2 Project Module - Scope of Work (SOW)

This module implements **Pattern 2: Inspection/Quality Control Workflow**.

#### 5.2.1 SOW Management

##### User Interface
- **SOW List**: Active and completed SOWs
- **SOW Form**: Define scope with items, specifications, quantities
- **SOW Detail**: View SOW with lots and progress
- **SOW Schedule**: Gantt chart or timeline view
- **SOW Progress**: Real-time progress tracking

##### Security
- **Authorization**: Project team member or higher
- **Validation**: Duplicate SOW detection

##### Application Services
1. **SOWService**: SOW CRUD operations
2. **SOWLotService**: Lot management
3. **SOWProgressService**: Progress tracking
4. **SOWScheduleService**: Scheduling
5. **SOWActualService**: Actual vs planned tracking

**Service Flow:**
1. User creates SOW with specification and quantity
2. System checks for duplicate SOWs
3. User submits SOW → Status: Completed
4. SOW is available for work order assignment
5. Progress tracking begins as work orders execute
6. Real-time updates via SOWProgressService

##### Database
- **Collections**:
  - `sow` - Main SOW records
  - `sow_lot` - Production lots
  - `sow_progress` - Progress tracking
  - `sow_schedule` - Scheduling data
  - `sow_actual` - Actual execution data
  - `sow_coating` - Coating specifications
- **Status Values**: Draft, Completed

### 5.3 Project Module - Work Order

#### 5.3.1 Work Order Management

##### User Interface
- **Vendor Tabs**: Tabbed interface per vendor
- **Production Detail**: Work order execution details
- **Lot Management**: Create/edit production lots
- **PQT Management**: Process Qualification Tests
- **Length History**: Track pipe length changes
- **Inspection Interface**: Quality inspection records

##### Security
- **Authorization**: Vendor-specific access control
- **Data Isolation**: Vendor users see only their work orders

##### Application Services
1. **WorkOrderService**: Work order CRUD
2. **WorkOrderLotService**: Lot management
3. **WorkOrderProgressService**: Progress tracking
4. **PQTService**: PQT form submission and tracking
5. **LengthHistoryService**: Length tracking and reporting
6. **InspectionPerWOService**: Inspection management

**Service Flow:**
1. Project team creates work order from SOW
2. Work order assigned to vendor
3. Vendor creates production lots
4. Vendor submits PQT if required
5. Vendor tracks length history and inspection results
6. Progress updates flow back to SOW
7. Work order completion triggers overall progress update

##### Database
- **Collections**:
  - `work_order` - Main work order records
  - `work_order_sow` - SOW linkage
  - `work_order_lot` - Production lots
  - `work_order_progress` - Progress tracking
  - `work_order_schedule` - Scheduling
  - `work_order_actual` - Actual execution
  - `work_order_inspection_child` - Inspection records
- **Status Values**: Completed (and others)

### 5.4 Project Module - Technical/Quality

#### 5.4.1 MDR (Material Data Record)

This module implements **Pattern 4: Document Management with Versioning**.

##### User Interface
- **MDR List**: Datatable of all MDRs
- **MDR Form**: Create/edit MDR with sections
- **MDR Detail**: View MDR with document attachments
- **Section Management**: Add/edit sections dynamically
- **Document Upload**: Attach files per section

##### Security
- **Authorization**: MI team or project manager
- **File Storage**: Secure file storage with access control

##### Application Services
1. **MDRService**: MDR CRUD operations
2. **MDRSectionService**: Section management
3. **DocumentService**: File upload/download
4. **CopyService**: Duplicate MDR for new lots

**Service Flow:**
1. User creates MDR for specific SOW/lot
2. User adds sections with required documentation
3. User uploads documents per section
4. MDR marked as complete when all sections filled
5. MDR can be copied for similar lots
6. Document download tracked for audit

##### Database
- **Collections**:
  - `mdr` - Main MDR records
  - `mdr_section` - MDR sections
- **Related**: `queue_file` for async file processing

#### 5.4.2 Inspection Management

This module implements **Pattern 2: Inspection/Quality Control Workflow**.

##### User Interface
- **Inspection List**: All inspections with status
- **Inspection Form**: Create inspection request
- **Screening Interface**: Perform initial screening
- **Review Interface**: Final inspection review
- **Inspection Results**: View test results and reports

##### Application Services
1. **InspectionService**: Inspection CRUD
2. **InspectionMappingService**: Map results to reports
3. **TestService**: Test result management
4. **TestRawService**: Raw data import
5. **MaterialTestService**: Material testing

**Service Flow:**
1. Inspection created → Status: New
2. Screening performed → Status: Screening
3. Screening completed → Status: For Review
4. Final review → Status: Yard Follow Up or Completed
5. Test results linked via inspection mapping

##### Database
- **Collections**:
  - `inspection` - Inspection records
  - `inspection_mapping` - Result mapping
  - `testing` - Test results
  - `testing_raw` - Raw test data
  - `testing_table` - Test result tables
  - `material_test` - Material tests

#### 5.4.3 Product Traceability

##### User Interface
- **Trace View**: Trace pipe from raw material to delivery
- **Heat Number Search**: Find all items by heat number
- **Lot Traceability**: Track lot through processes

##### Application Services
1. **ProductTraceService**: Traceability queries
2. **HeatNumberService**: Heat number tracking

##### Database
- **Collection**: `product_trace`
- **Fields**: Links pipes to mills, lots, SOWs, work orders

### 5.5 Project Module - Transfer/Logistics

This module implements **Pattern 3: Transfer/Movement Tracking**.

#### 5.5.1 Transfer Management

##### User Interface
- **Transfer List**: All material transfers
- **Transfer Form**: Create transfer with route
- **Transfer Route**: Define origin, destination, waypoints
- **Transfer Cargo**: Assign materials to transfer
- **Transfer Tracking**: Real-time location tracking

##### Security
- **Authorization**: Project team or customer (view-only)
- **Validation**: Ensure sufficient inventory

##### Application Services
1. **TransferService**: Transfer CRUD
2. **TransferRouteService**: Route planning
3. **TransferCargoService**: Cargo assignment
4. **TransferTrackService**: GPS tracking
5. **TallyService**: Material tallying

**Service Flow:**
1. Create transfer with from/to locations
2. Define transfer route with waypoints
3. Assign cargo (SOWs, work orders, lots)
4. Dispatch transfer → Status: in_progress
5. Track transfer progress
6. Receive transfer → Status: completed
7. Inventory updated at destination

##### Database
- **Collections**:
  - `transfer` - Main transfer records
  - `transfer_move` - Individual movements
  - `transfer_route` - Route definitions
  - `transfer_cargo` - Cargo assignments
  - `transfer_track` - Tracking data
  - `transfer_track_cargo` - Cargo tracking
  - `transfer_track_sow` - SOW tracking
  - `transfer_track_work_order` - Work order tracking
  - `tally_list` - Material tallies

#### 5.5.2 Cargo & Shipment Management

##### User Interface
- **Cargo List**: All cargo shipments
- **Cargo Form**: Create cargo with loading details
- **Shipment Form**: Create shipment with vessel info
- **Loading/Unloading**: Track loading operations
- **Vessel Tracking**: Track vessel location

##### Application Services
1. **CargoService**: Cargo CRUD
2. **ShipmentService**: Shipment CRUD
3. **CargoLoadingService**: Loading operations
4. **ShipmentProgressService**: Shipment tracking
5. **VesselTrackingService**: Vessel tracking

##### Database
- **Collections**:
  - `cargo` - Cargo records
  - `cargo_loading` - Loading operations
  - `cargo_unloading` - Unloading operations
  - `cargo_progress` - Cargo progress
  - `shipment` - Shipment records
  - `shipment_loading` - Shipment loading
  - `shipment_progress` - Shipment tracking
  - `vessel_tracking` - Vessel locations

### 5.6 Project Module - Commercial

#### 5.6.1 Contract Management

This module implements **Pattern 4: Document Management with Versioning**.

##### User Interface
- **Contract List**: All contracts with status
- **Contract Form**: Create/edit contract
- **Contract Detail**: View contract with attachments
- **File Management**: Upload/download contract files

##### Application Services
1. **ContractService**: Contract CRUD
2. **DocumentService**: File management
3. **CopyService**: Duplicate contracts

##### Database
- **Collection**: `contract`

#### 5.6.2 Invoice Management

##### User Interface
- **Invoice List**: All invoices with payment status
- **Invoice Form**: Create/edit invoice
- **Invoice Detail**: View invoice with line items

##### Application Services
1. **InvoiceService**: Invoice CRUD
2. **PaymentService**: Payment tracking

##### Database
- **Collection**: `invoice`

#### 5.6.3 Variation Management

##### User Interface
- **Variation List**: All contract variations
- **Variation Form**: Create variation with impact analysis
- **Approval Flow**: Variation approval workflow

##### Application Services
1. **VariationService**: Variation CRUD
2. **ApprovalService**: Approval workflow

##### Database
- **Collection**: `variation`

#### 5.6.4 Key Terms & Indicators

##### User Interface
- **Key Terms**: Define contract key terms
- **Key Indicators**: KPI dashboard

##### Application Services
1. **KeyTermsService**: Terms management
2. **KeyIndicatorService**: KPI tracking

##### Database
- **Collections**: `key_terms`, `key_indicator`

### 5.7 Project Module - Documentation

#### 5.7.1 Procedure Management

##### User Interface
- **Procedure List**: All procedures
- **Procedure Form**: Create/edit procedure
- **Procedure Viewer**: View procedure documents

##### Application Services
1. **ProcedureService**: Procedure CRUD

##### Database
- **Collection**: `procedure`

#### 5.7.2 NCR (Non-Conformance Report)

##### User Interface
- **NCR List**: All NCRs with status
- **NCR Form**: Create NCR with details
- **NCR Response**: Respond to NCR
- **NCR Resolution**: Close NCR

##### Application Services
1. **NCRService**: NCR CRUD
2. **NCRResponseService**: Response handling
3. **NCRResolutionService**: Resolution tracking

##### Database
- **Collection**: `ncr`

#### 5.7.3 2nd Party Inspection

##### User Interface
- **Inspection List**: External inspection records
- **Inspection Form**: Record external inspection

##### Application Services
1. **SecondPartyInspectionService**: Inspection CRUD

##### Database
- **Collection**: `second_party_inspection`

#### 5.7.4 Reporting

##### User Interface
- **Report List**: Generated reports
- **Report Builder**: Create custom reports
- **Report Viewer**: View/download reports

##### Application Services
1. **ReportingService**: Report generation
2. **ExportService**: Export to PDF/Excel

##### Database
- **Collection**: `reporting`

### 5.8 Project Module - Progress Tracking

#### 5.8.1 Dashboard

##### User Interface
- **Dashboard Overview**: Key metrics and charts
- **Progress Charts**: SOW and work order progress
- **S-Curve**: Planned vs actual progress
- **KPI Cards**: Real-time indicators

##### Application Services
1. **DashboardService**: Dashboard data aggregation
2. **ChartService**: Chart data generation
3. **OverallService**: Overall progress calculation

**Service Flow:**
1. User opens dashboard
2. DashboardService queries multiple collections
3. Aggregates progress from SOW, work order, transfer
4. Calculates overall progress metrics
5. Returns chart-ready data
6. Frontend renders charts with Highcharts

##### Database
- **Collections**:
  - `dashboard` - Dashboard configuration
  - `overall` - Overall progress
  - `overall_sow` - SOW progress summary
  - `overall_work_order` - Work order progress summary
  - `overall_tabs` - Dashboard tabs
  - `progress_sow_chart` - SOW chart data
  - `progress_work_order_chart` - Work order chart data

---

## 6. API Endpoints

### 6.1 API Structure

- **Base URL**: `/api/v3`
- **Authentication**: Bearer token (OAuth 2.0)
- **Response Format**: JSON
- **API Version**: v3 (current), v2 (backup)
- **Total Routes**: 887 lines in api.v3.php

### 6.2 Global APIs

#### 6.2.1 Authentication Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/login` | POST | User login | No |
| `/api/v3/logout` | POST | User logout | Yes |
| `/api/v3/refresh` | POST | Refresh token | Yes |
| `/api/v3/me` | GET | Get current user | Yes |

**Example Request:**
```json
POST /api/v3/login
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Example Response:**
```json
{
  "success": true,
  "data": {
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
    "token_type": "Bearer",
    "expires_at": "2025-10-09T00:00:00.000000Z",
    "user": {
      "_id": "user_id",
      "email": "user@example.com",
      "name": "John Doe",
      "role": ["project_manager"]
    }
  }
}
```

#### 6.2.2 Project Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/projects` | GET | List all projects | Yes |
| `/api/v3/project` | POST | Create project | Yes (super_admin) |
| `/api/v3/project/{id}` | GET | Get project | Yes |
| `/api/v3/project/{id}` | PUT | Update project | Yes (project_manager) |
| `/api/v3/project/{id}` | DELETE | Delete project | Yes (super_admin) |

#### 6.2.3 User Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/users` | GET | List users | Yes |
| `/api/v3/user` | POST | Create user | Yes (super_admin) |
| `/api/v3/user/{id}` | GET | Get user | Yes |
| `/api/v3/user/{id}` | PUT | Update user | Yes |
| `/api/v3/user/{id}` | DELETE | Delete user | Yes (super_admin) |

#### 6.2.4 Mill Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/mills/datatables` | GET | List mills | Yes |
| `/api/v3/mill` | POST | Create mill | Yes |
| `/api/v3/mill/{id}` | GET | Get mill | Yes |
| `/api/v3/mill/{id}` | PUT | Update mill | Yes |
| `/api/v3/mill/{id}/approve` | POST | Approve mill | Yes (vendor_approver) |
| `/api/v3/mill/{id}/reject` | POST | Reject mill | Yes (vendor_approver) |

### 6.3 Project Module APIs

#### 6.3.1 SOW Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/project/sows` | GET | List SOWs | Yes |
| `/api/v3/project/sow` | POST | Create SOW | Yes |
| `/api/v3/project/sow/{id}` | GET | Get SOW | Yes |
| `/api/v3/project/sow/{id}` | POST | Update SOW | Yes |
| `/api/v3/project/sow/{id}` | DELETE | Delete SOW | Yes |
| `/api/v3/project/submit_sow` | POST | Submit SOW | Yes |
| `/api/v3/project/duplicate_sow` | POST | Check duplicate | Yes |
| `/api/v3/project/lot` | POST | Create lot | Yes |

**Example Request:**
```json
POST /api/v3/project/sow
{
  "specification_id": "spec_001",
  "item_id": "item_001",
  "quantity": 1000,
  "unit": "MT",
  "delivery_date": "2025-12-31"
}
```

**Example Response:**
```json
{
  "success": true,
  "data": {
    "_id": "sow_001",
    "specification_id": "spec_001",
    "item_id": "item_001",
    "quantity": 1000,
    "status": "Draft",
    "created_at": 1696800000,
    "created_by": "user_id"
  }
}
```

#### 6.3.2 Work Order Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/project/work_order/get_vendor_list` | GET | Get vendor tabs | Yes |
| `/api/v3/project/work_order/vendor_list_data` | GET | Get vendor data | Yes |
| `/api/v3/project/work_order/production_detail_data/{id}` | GET | Get production detail | Yes |
| `/api/v3/project/lot_work_order` | POST | Create/update lot | Yes |
| `/api/v3/project/lot_work_order/{id}` | DELETE | Delete lot | Yes |
| `/api/v3/project/work_order/pqt_form` | POST | Submit PQT | Yes |
| `/api/v3/project/work_order/length_history_list_data` | GET | Get length history | Yes |
| `/api/v3/project/work_order/submit_length_report` | POST | Submit length report | Yes |

#### 6.3.3 MDR Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/project/mdr/datatables` | GET | List MDRs | Yes |
| `/api/v3/project/mdr` | POST | Create MDR | Yes |
| `/api/v3/project/mdr/{id}` | GET | Get MDR | Yes |
| `/api/v3/project/mdr/{id}` | POST | Update MDR | Yes |
| `/api/v3/project/mdr/{id}` | DELETE | Delete MDR | Yes |
| `/api/v3/project/mdr/copy/{id}` | POST | Copy MDR | Yes |
| `/api/v3/project/mdr_section` | POST | Submit section | Yes |
| `/api/v3/project/mdr_section_document` | POST | Submit document | Yes |
| `/api/v3/project/mdr/download_file/{id}/{section}/{subsection}` | GET | Download document | Yes |

#### 6.3.4 Transfer Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/project/transfer` | POST | Create transfer | Yes |
| `/api/v3/project/transfer/{id}/edit` | GET | Get transfer | Yes |
| `/api/v3/project/transfer/{id}` | DELETE | Delete transfer | Yes |
| `/api/v3/project/transfer_route_details/{id}` | GET | Get route | Yes |
| `/api/v3/project/transfer_cargo/{id}` | GET | Get cargo | Yes |
| `/api/v3/project/cargo` | POST | Create cargo | Yes |
| `/api/v3/project/cargo/{id}/edit` | GET | Get cargo | Yes |
| `/api/v3/project/cargo/{id}` | DELETE | Delete cargo | Yes |
| `/api/v3/project/shipment` | POST | Create shipment | Yes |
| `/api/v3/project/shipment/{id}` | GET | Get shipment | Yes |
| `/api/v3/project/shipment/{id}` | POST | Update shipment | Yes |
| `/api/v3/project/shipment/{id}` | DELETE | Delete shipment | Yes |

#### 6.3.5 Inspection Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/project/inspection` | POST | Create inspection | Yes |
| `/api/v3/project/inspection/{id}` | GET | Get inspection | Yes |
| `/api/v3/project/inspection/{id}` | POST | Update inspection | Yes |
| `/api/v3/project/inspection/{id}` | DELETE | Delete inspection | Yes |
| `/api/v3/project/inspection_mapping` | POST | Create mapping | Yes |

#### 6.3.6 Commercial Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/project/contract/datatables` | GET | List contracts | Yes |
| `/api/v3/project/contract` | POST | Create contract | Yes |
| `/api/v3/project/contract/{id}` | GET | Get contract | Yes |
| `/api/v3/project/contract/{id}` | POST | Update contract | Yes |
| `/api/v3/project/contract/{id}` | DELETE | Delete contract | Yes |
| `/api/v3/project/invoice` | POST | Create invoice | Yes |
| `/api/v3/project/invoice/{id}` | GET | Get invoice | Yes |
| `/api/v3/project/variation` | POST | Create variation | Yes |
| `/api/v3/project/variation/{id}` | GET | Get variation | Yes |

#### 6.3.7 Progress Endpoints

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/api/v3/project/dashboard` | GET | Get dashboard data | Yes |
| `/api/v3/project/overall` | GET | Get overall progress | Yes |
| `/api/v3/project/progress_chart` | GET | Get progress charts | Yes |

### 6.4 API Response Standards

#### Success Response Format
```json
{
  "success": true,
  "data": {
    // Response data
  },
  "meta": {
    "timestamp": 1696800000,
    "page": 1,
    "total": 100,
    "per_page": 20
  }
}
```

#### Error Response Format
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human readable error message",
    "field": "field_name",
    "details": {}
  }
}
```

#### Common Error Codes
| Code | HTTP Status | Description |
|------|-------------|-------------|
| VALIDATION_ERROR | 400 | Input validation failed |
| UNAUTHORIZED | 401 | Authentication required |
| FORBIDDEN | 403 | Insufficient permissions |
| NOT_FOUND | 404 | Resource not found |
| DUPLICATE_ENTRY | 409 | Duplicate record |
| SERVER_ERROR | 500 | Internal server error |

---

## 7. Data Flow Diagrams

### 7.1 Authentication Flow - Level 1 DFD

```
┌─────────┐         ┌────────────────┐         ┌──────────────┐
│  User   │         │   Laravel API   │         │   MongoDB    │
└────┬────┘         └───────┬────────┘         └──────┬───────┘
     │                      │                         │
     │  1. POST /api/v3/login                        │
     │  {email, password}   │                         │
     ├─────────────────────>│                         │
     │                      │                         │
     │                      │  2. Query user by email │
     │                      ├────────────────────────>│
     │                      │                         │
     │                      │  3. Return user record  │
     │                      │<────────────────────────┤
     │                      │                         │
     │                      │  4. Verify password     │
     │                      │     (bcrypt)            │
     │                      │                         │
     │                      │  5. Generate OAuth token│
     │                      ├────────────────────────>│
     │                      │     (oauth_access_tokens)│
     │                      │                         │
     │  6. Return token     │                         │
     │  {access_token, user}│                         │
     │<─────────────────────┤                         │
     │                      │                         │
     │  7. Store token in   │                         │
     │     localStorage     │                         │
     │                      │                         │
```

**Process Steps:**
1. User submits email and password
2. Laravel validates credentials against users collection
3. Password verified using bcrypt
4. Passport generates OAuth 2.0 access token
5. Token stored in oauth_access_tokens collection
6. Token returned to client with user data
7. Client stores token for subsequent requests

### 7.2 SOW Creation Flow - Level 1 DFD

```
┌─────────┐    ┌────────────┐    ┌──────────────┐    ┌──────────┐
│  User   │    │ Controller │    │   Service    │    │ MongoDB  │
└────┬────┘    └─────┬──────┘    └──────┬───────┘    └────┬─────┘
     │               │                  │                  │
     │ 1. Create SOW │                  │                  │
     ├──────────────>│                  │                  │
     │               │                  │                  │
     │               │ 2. Validate      │                  │
     │               │    input         │                  │
     │               │                  │                  │
     │               │ 3. Check         │                  │
     │               │    duplicate     │                  │
     │               ├─────────────────>│                  │
     │               │                  │                  │
     │               │                  │ 4. Query existing│
     │               │                  ├─────────────────>│
     │               │                  │                  │
     │               │                  │ 5. Return results│
     │               │                  │<─────────────────┤
     │               │                  │                  │
     │               │ 6. No duplicate  │                  │
     │               │<─────────────────┤                  │
     │               │                  │                  │
     │               │ 7. Create SOW    │                  │
     │               ├─────────────────>│                  │
     │               │                  │                  │
     │               │                  │ 8. Insert record │
     │               │                  ├─────────────────>│
     │               │                  │                  │
     │               │                  │ 9. Increment     │
     │               │                  │    counter       │
     │               │                  ├─────────────────>│
     │               │                  │                  │
     │               │                  │ 10. Return SOW   │
     │               │                  │<─────────────────┤
     │               │                  │                  │
     │               │ 11. Success      │                  │
     │               │<─────────────────┤                  │
     │               │                  │                  │
     │ 12. Return    │                  │                  │
     │     SOW data  │                  │                  │
     │<──────────────┤                  │                  │
     │               │                  │                  │
```

**Process Steps:**
1. User submits SOW form with specification and quantity
2. Controller validates input data
3. Service checks for duplicate SOW (same spec + lot)
4. Query MongoDB for existing SOW
5. If no duplicate, proceed with creation
6. Service creates SOW record
7. Counter service increments SOW number
8. SOW saved to database with status "Draft"
9. Success response returned to user

### 7.3 Work Order Progress Update Flow - Level 1 DFD

```
┌───────────┐   ┌─────────────┐   ┌──────────────┐   ┌─────────┐
│  Vendor   │   │  WO Service │   │ Progress Svc │   │ MongoDB │
└─────┬─────┘   └──────┬──────┘   └──────┬───────┘   └────┬────┘
      │                │                 │                 │
      │ 1. Update WO   │                 │                 │
      │    progress    │                 │                 │
      ├───────────────>│                 │                 │
      │                │                 │                 │
      │                │ 2. Update WO    │                 │
      │                │    record       │                 │
      │                ├────────────────────────────────────>│
      │                │                 │                 │
      │                │ 3. Trigger      │                 │
      │                │    recalculation│                 │
      │                ├────────────────>│                 │
      │                │                 │                 │
      │                │                 │ 4. Aggregate WO │
      │                │                 │    progress     │
      │                │                 ├────────────────>│
      │                │                 │                 │
      │                │                 │ 5. Update SOW   │
      │                │                 │    progress     │
      │                │                 ├────────────────>│
      │                │                 │                 │
      │                │                 │ 6. Update       │
      │                │                 │    overall      │
      │                │                 ├────────────────>│
      │                │                 │                 │
      │                │                 │ 7. Update charts│
      │                │                 ├────────────────>│
      │                │                 │                 │
      │                │ 8. Complete     │                 │
      │                │<────────────────┤                 │
      │                │                 │                 │
      │ 9. Success     │                 │                 │
      │<───────────────┤                 │                 │
      │                │                 │                 │
```

**Process Steps:**
1. Vendor updates work order progress
2. Work order record updated in database
3. Progress service triggered for recalculation
4. Aggregate all work order progress for parent SOW
5. Update SOW progress based on child work orders
6. Update overall project progress
7. Update chart data collections
8. Return success response

### 7.4 Transfer Creation Flow - Level 1 DFD

```
┌────────┐  ┌───────────┐  ┌────────────┐  ┌─────────────┐  ┌────────┐
│  User  │  │Controller │  │  Transfer  │  │   Tally     │  │ MongoDB│
│        │  │           │  │  Service   │  │   Service   │  │        │
└───┬────┘  └─────┬─────┘  └─────┬──────┘  └──────┬──────┘  └───┬────┘
    │             │              │                │              │
    │1. Create    │              │                │              │
    │  transfer   │              │                │              │
    ├────────────>│              │                │              │
    │             │              │                │              │
    │             │2. Validate   │                │              │
    │             │   route &    │                │              │
    │             │   cargo      │                │              │
    │             │              │                │              │
    │             │3. Create     │                │              │
    │             │   transfer   │                │              │
    │             ├─────────────>│                │              │
    │             │              │                │              │
    │             │              │4. Check        │              │
    │             │              │   inventory    │              │
    │             │              ├───────────────>│              │
    │             │              │                │              │
    │             │              │                │5. Query      │
    │             │              │                │  itemcard    │
    │             │              │                ├─────────────>│
    │             │              │                │              │
    │             │              │                │6. Return qty │
    │             │              │                │<─────────────┤
    │             │              │                │              │
    │             │              │7. Sufficient   │              │
    │             │              │<───────────────┤              │
    │             │              │                │              │
    │             │              │8. Insert       │              │
    │             │              │   transfer     │              │
    │             │              ├───────────────────────────────>│
    │             │              │                │              │
    │             │              │9. Create       │              │
    │             │              │   tally        │              │
    │             │              ├───────────────>│              │
    │             │              │                │              │
    │             │              │                │10. Insert    │
    │             │              │                │   tally_list │
    │             │              │                ├─────────────>│
    │             │              │                │              │
    │             │              │11. Complete    │              │
    │             │              │<───────────────┤              │
    │             │              │                │              │
    │             │12. Success   │                │              │
    │             │<─────────────┤                │              │
    │             │              │                │              │
    │13. Return   │              │                │              │
    │   transfer  │              │                │              │
    │<────────────┤              │                │              │
    │             │              │                │              │
```

**Process Steps:**
1. User creates transfer with route and cargo
2. Controller validates input data
3. Transfer service initiates creation
4. Tally service checks inventory availability
5. Query itemcard collection for available quantity
6. If sufficient inventory, proceed
7. Insert transfer record with status "schedule"
8. Create tally list for cargo
9. Transfer creation complete
10. Return transfer data to user

---

## 8. Database Schema

### 8.1 Database Architecture

This application uses a **dual MongoDB database architecture**:

- **`mongodb_global`**: 19 collections for cross-project data
- **`mongodb_project`**: 85+ collections for project-specific data

Each project can optionally use its own database instance for data isolation.

### 8.2 Global Database Collections

#### 8.2.1 users (mongodb_global)

**Purpose:** Global user accounts with authentication and authorization

| Field | Type | Description | Required | Default |
|-------|------|-------------|----------|---------|
| _id | ObjectId | Unique identifier | Yes | Auto |
| email | String | User email (unique) | Yes | - |
| password | String | Bcrypt hashed password | Yes | - |
| first_name | String | First name | Yes | - |
| last_name | String | Last name | Yes | - |
| role | Array | User roles | Yes | [] |
| active | String | Active status (Y/N) | Yes | Y |
| db_access | Array | Database access list | No | [] |
| photo | String | Profile photo path | No | null |
| additional_privileges | Object | Extra permissions | No | {} |
| created_by | String | Creator user email | No | null |
| updated_by | String | Updater user ID | No | null |
| deleted_by | String | Deleter user email | No | null |
| created_at | DateTime | Creation timestamp | Yes | Now |
| updated_at | DateTime | Update timestamp | Yes | Now |
| deleted_at | DateTime | Soft delete timestamp | No | null |

**Indexes:**
- `_id`: Primary key
- `email`: Unique index
- `role`: Regular index
- `active`: Regular index

**Relationships:**
- HasMany: `user_sessions`, `oauth_access_tokens`
- HasMany: `user_projects`
- BelongsToMany: `projects` (via project_users)

**Roles:**
- super_admin
- project_manager
- mi_team
- global_viewer
- vendor_approver
- customer
- project_team (various types)

#### 8.2.2 projects (mongodb_global)

**Purpose:** Master project registry

| Field | Type | Description | Required | Default |
|-------|------|-------------|----------|---------|
| _id | ObjectId | Unique identifier | Yes | Auto |
| name | String | Project name | Yes | - |
| color | String | Project color code | No | null |
| db_name | String | Database name | Yes | - |
| project_manager_id | String | PM user ID | No | null |
| status | String | Project status | Yes | active |
| show_type | Array | Display types | No | [] |
| welding_method | String | Welding method | No | null |
| testings | Array | Test types | No | [] |
| s_curve_graph | Boolean | S-curve enabled | No | false |
| created_by | String | Creator user email | No | null |
| updated_by | String | Updater user ID | No | null |
| deleted_by | String | Deleter user email | No | null |
| created_at | DateTime | Creation timestamp | Yes | Now |
| updated_at | DateTime | Update timestamp | Yes | Now |
| deleted_at | DateTime | Soft delete timestamp | No | null |

**Indexes:**
- `_id`: Primary key
- `db_name`: Unique index
- `status`: Regular index

**Relationships:**
- BelongsToMany: `users` (via project_users)
- HasOne: `user` (project_manager)

**Status Values:**
- active
- closed
- incomplete

#### 8.2.3 mills (mongodb_global)

**Purpose:** Mill/manufacturer registry with approval workflow

| Field | Type | Description | Required | Default |
|-------|------|-------------|----------|---------|
| _id | ObjectId | Unique identifier | Yes | Auto |
| api_no | String | API number | No | null |
| name | String | Mill name | Yes | - |
| location | String | Location | Yes | - |
| short_name | String | Short name | No | null |
| status | String | Approval status | Yes | Pending |
| type | String | Mill type | Yes | Mill |
| created_by | String | Creator user email | No | null |
| updated_by | String | Updater user ID | No | null |
| deleted_by | String | Deleter user email | No | null |
| created_at | DateTime | Creation timestamp | Yes | Now |
| updated_at | DateTime | Update timestamp | Yes | Now |
| deleted_at | DateTime | Soft delete timestamp | No | null |

**Indexes:**
- `_id`: Primary key
- `api_no`: Unique index
- `status`: Regular index
- `type`: Regular index

**Relationships:**
- HasMany: `mill_histories`

**Status Values:**
- Approved
- Rejected
- Pending for Approval
- To Review

**Type Values:**
- Mill
- Bender
- Coater

### 8.3 Project Database Collections

#### 8.3.1 sow (mongodb_project)

**Purpose:** Scope of Work - Manufacturing order definitions

| Field | Type | Description | Required | Default |
|-------|------|-------------|----------|---------|
| _id | ObjectId | Unique identifier | Yes | Auto |
| sow_no | String | SOW number | Yes | Auto |
| specification_id | String | Specification ID | Yes | - |
| item_id | String | Item ID | Yes | - |
| quantity | Number | Quantity | Yes | - |
| unit | String | Unit of measurement | Yes | - |
| delivery_date | DateTime | Delivery date | No | null |
| status | String | SOW status | Yes | Draft |
| progress | Number | Progress % | Yes | 0 |
| created_by | String | Creator user email | No | null |
| updated_by | String | Updater user ID | No | null |
| createdate | Timestamp | Creation timestamp | Yes | Now |
| updatedate | Timestamp | Update timestamp | Yes | Now |

**Indexes:**
- `_id`: Primary key
- `sow_no`: Unique index
- `specification_id`: Regular index
- `status`: Regular index

**Relationships:**
- BelongsTo: `specification`, `item`
- HasMany: `sow_lots`, `work_orders`
- HasOne: `sow_progress`, `sow_schedule`

**Status Values:**
- Draft
- Completed

#### 8.3.2 work_order (mongodb_project)

**Purpose:** Work order management for vendors

| Field | Type | Description | Required | Default |
|-------|------|-------------|----------|---------|
| _id | ObjectId | Unique identifier | Yes | Auto |
| wo_no | String | Work order number | Yes | Auto |
| vendor_id | String | Vendor ID | Yes | - |
| sow_id | String | SOW ID | Yes | - |
| quantity | Number | Quantity | Yes | - |
| status | String | Status | Yes | Draft |
| progress | Number | Progress % | Yes | 0 |
| created_by | String | Creator user email | No | null |
| updated_by | String | Updater user ID | No | null |
| createdate | Timestamp | Creation timestamp | Yes | Now |
| updatedate | Timestamp | Update timestamp | Yes | Now |

**Indexes:**
- `_id`: Primary key
- `wo_no`: Unique index
- `vendor_id`: Regular index
- `sow_id`: Regular index
- `status`: Regular index

**Relationships:**
- BelongsTo: `vendor`, `sow`
- HasMany: `work_order_lots`, `inspections`
- HasOne: `work_order_progress`, `work_order_schedule`

#### 8.3.3 inspection (mongodb_project)

**Purpose:** Quality inspection records

| Field | Type | Description | Required | Default |
|-------|------|-------------|----------|---------|
| _id | ObjectId | Unique identifier | Yes | Auto |
| inspection_no | String | Inspection number | Yes | Auto |
| work_order_id | String | Work order ID | Yes | - |
| lot_id | String | Lot ID | Yes | - |
| inspection_type | String | Type | Yes | - |
| status | String | Status | Yes | New |
| inspector | String | Inspector user ID | No | null |
| inspection_date | Timestamp | Inspection date | No | null |
| created_by | String | Creator user email | No | null |
| updated_by | String | Updater user ID | No | null |
| createdate | Timestamp | Creation timestamp | Yes | Now |
| updatedate | Timestamp | Update timestamp | Yes | Now |

**Indexes:**
- `_id`: Primary key
- `inspection_no`: Unique index
- `work_order_id`: Regular index
- `status`: Regular index

**Relationships:**
- BelongsTo: `work_order`, `work_order_lot`
- HasMany: `inspection_mappings`, `tests`

**Status Values:**
- New
- Screening
- For Review
- Yard Follow Up
- Completed

#### 8.3.4 mdr (mongodb_project)

**Purpose:** Material Data Report - Manufacturing documentation

| Field | Type | Description | Required | Default |
|-------|------|-------------|----------|---------|
| _id | ObjectId | Unique identifier | Yes | Auto |
| mdr_no | String | MDR number | Yes | Auto |
| sow_id | String | SOW ID | Yes | - |
| lot_id | String | Lot ID | Yes | - |
| sections | Array | MDR sections | No | [] |
| status | String | Status | Yes | Draft |
| created_by | String | Creator user email | No | null |
| updated_by | String | Updater user ID | No | null |
| createdate | Timestamp | Creation timestamp | Yes | Now |
| updatedate | Timestamp | Update timestamp | Yes | Now |

**Indexes:**
- `_id`: Primary key
- `mdr_no`: Unique index
- `sow_id`: Regular index
- `status`: Regular index

**Relationships:**
- BelongsTo: `sow`, `sow_lot`
- HasMany: `mdr_sections`

#### 8.3.5 transfer (mongodb_project)

**Purpose:** Material transfer/logistics tracking

| Field | Type | Description | Required | Default |
|-------|------|-------------|----------|---------|
| _id | ObjectId | Unique identifier | Yes | Auto |
| transfer_no | String | Transfer number | Yes | Auto |
| from_location | String | Origin location | Yes | - |
| to_location | String | Destination location | Yes | - |
| transfer_date | DateTime | Transfer date | No | null |
| status | String | Status | Yes | schedule |
| progress | Number | Progress % | Yes | 0 |
| created_by | String | Creator user email | No | null |
| updated_by | String | Updater user ID | No | null |
| createdate | Timestamp | Creation timestamp | Yes | Now |
| updatedate | Timestamp | Update timestamp | Yes | Now |

**Indexes:**
- `_id`: Primary key
- `transfer_no`: Unique index
- `status`: Regular index

**Relationships:**
- HasOne: `transfer_route`
- HasMany: `transfer_cargo`, `transfer_tracks`

**Status Values:**
- schedule
- in_progress
- completed

#### 8.3.6 contract (mongodb_project)

**Purpose:** Contract documents and terms

| Field | Type | Description | Required | Default |
|-------|------|-------------|----------|---------|
| _id | ObjectId | Unique identifier | Yes | Auto |
| contract_no | String | Contract number | Yes | Auto |
| title | String | Contract title | Yes | - |
| customer | String | Customer name | Yes | - |
| contract_date | DateTime | Contract date | No | null |
| files | Array | Attached files | No | [] |
| status | String | Status | Yes | Draft |
| created_by | String | Creator user email | No | null |
| updated_by | String | Updater user ID | No | null |
| createdate | Timestamp | Creation timestamp | Yes | Now |
| updatedate | Timestamp | Update timestamp | Yes | Now |

**Indexes:**
- `_id`: Primary key
- `contract_no`: Unique index
- `status`: Regular index

**Relationships:**
- HasMany: `variations`, `invoices`

### 8.4 Common Field Patterns

#### Audit Fields (Most Collections)
```javascript
{
  created_by: "user_id",
  updated_by: "user_id",
  deleted_by: "user_id",  // For soft deletes
  createdate: 1696800000,  // Unix timestamp
  updatedate: 1696800000,
  deleted_at: null         // For soft deletes
}
```

#### Status Tracking
```javascript
{
  status: "draft|submitted|approved|completed",
  progress: 0-100  // Percentage
}
```

#### File Attachments
```javascript
{
  files: [
    {
      filename: "document.pdf",
      path: "/storage/files/...",
      uploaded_by: "user_id",
      uploaded_at: 1696800000
    }
  ]
}
```

#### Approval Chain
```javascript
{
  approved: false,
  approvedate: null,
  approvewho: null,
  rejected: {
    user_id: null,
    reason: null,
    date_rejected: null
  }
}
```

---

## 9. Error Handling

### 9.1 Error Response Format
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable message",
    "field": "field_name",
    "details": {}
  }
}
```

### 9.2 Common Error Codes
| Code | HTTP Status | Description |
|------|-------------|-------------|
| VALIDATION_ERROR | 400 | Input validation failed |
| UNAUTHORIZED | 401 | Authentication required |
| FORBIDDEN | 403 | Insufficient permissions |
| NOT_FOUND | 404 | Resource not found |
| DUPLICATE_ENTRY | 409 | Duplicate record exists |
| SERVER_ERROR | 500 | Internal server error |
| DATABASE_ERROR | 500 | Database operation failed |

---

## 10. Deployment

### 10.1 Environment Variables
| Variable | Description | Required |
|----------|-------------|----------|
| APP_ENV | Application environment | Yes |
| APP_DEBUG | Debug mode | Yes |
| APP_URL | Application URL | Yes |
| MONGO_DB_DATABASE | MongoDB database name | Yes |
| MONGO_DB_DSN | MongoDB connection string | Yes |
| DB_CONNECTION | Default database connection | Yes |
| VITE_APP_HOST | Vite dev server host | No |
| VITE_APP_ORIGIN | Vite dev server origin | No |

### 10.2 Build Commands
```bash
# Install PHP dependencies
composer install --no-dev --optimize-autoloader

# Install Node dependencies
npm ci

# Build frontend assets
npm run build

# Clear and cache Laravel config
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

---

## Appendices

### Appendix A: Glossary
| Term | Definition |
|------|------------|
| Monolithic Architecture | Single unified application with all components |
| Repository Pattern | Data access abstraction layer |
| Soft Delete | Mark records as deleted without physical removal |
| OAuth 2.0 | Industry-standard authorization protocol |
| SPA | Single Page Application - client-side routing |
| Linepipe | Pipe used for conveying oil, gas, or water |

### Appendix B: References
- Laravel 11 Documentation: https://laravel.com/docs/11.x
- Vue 3 Documentation: https://vuejs.org/
- MongoDB Documentation: https://docs.mongodb.com/
- Laravel Passport: https://laravel.com/docs/11.x/passport
- Vite Documentation: https://vitejs.dev/

### Appendix C: Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-10-08 | System Analysis | Initial comprehensive TDD |

---

**Document End**
