# Component Design Patterns Reference

This document provides reusable patterns extracted from the MITME Tubestream technical design document that you can adapt for your own application.

---

## Pattern 1: CRUD Operations with Approval Flow

### Use Case
When you need to create, review, and approve records with multiple stakeholders.

### Components
1. **User Interface**
   - Create Form
   - Detail/Review Page
   - List/Index Page

2. **Security Layer**
   - Token validation
   - Role-based access control

3. **Application Services**
   - CreateService: Handles initial creation
   - ReviewService: Manages review process
   - ApprovalService: Handles approval workflow
   - NotificationService: Sends alerts

4. **Database**
   - Main collection for records
   - Status tracking (Draft → Submitted → Reviewed → Approved)

### Example Flow
```
User → Create Form → Middleware (Auth) → CreateService → Database
                                              ↓
                                         Notification
                                              ↓
Reviewer → Review Form → ReviewService → Update Status
                              ↓
                         NotificationService
                              ↓
Approver → Approve → ApprovalService → Final Status Update
```

---

## Pattern 2: Inspection/Quality Control Workflow

### Use Case
Multi-step inspection process with screening and final review.

### Components
1. **User Interface**
   - Create Inspection Request
   - Screening Interface
   - Review Interface
   - Complete/Move Actions

2. **Application Services**
   - EventInspectionCreated: Initializes inspection
   - EventScreeningCompleted: Marks screening done
   - EventInspectionReviewed: Handles review
   - EventInspectionCompleted: Finalizes process

3. **Supporting Services**
   - YardService: Location data
   - LedgerService: Stock tracking
   - ItemTallyService: Item counting
   - NotificationService: Updates

### Status Progression
```
New → Screening → For Review → Yard Follow Up → Completed
```

---

## Pattern 3: Transfer/Movement Tracking

### Use Case
Tracking item movement between locations with dispatch and receipt.

### Components
1. **User Interface**
   - Create Transfer Form
   - Dispatch Form
   - Receipt Form

2. **Application Services**
   - TransferService: Creates transfer record
   - DispatchService: Handles dispatch
   - ReceiveService: Handles receipt
   - LedgerService: Updates inventory

3. **Database Collections**
   - itemtrans: Main transaction record
   - itemcard: Available stock
   - itemcard_rack: Physical location

### Flow
```
Create Transfer → Dispatch (reduce source) → In Transit → Receive (add destination)
```

---

## Pattern 4: Document Management with Versioning

### Use Case
Managing documents with multiple revisions and approvals.

### Components
1. **Database Collections**
   - docs: Main document metadata
   - revision_document: Version history
   - storage_files: Actual files

2. **Fields to Track**
   - Document number
   - Revision number
   - Status (Draft/Published/Archived)
   - Approval chain
   - File attachments

### Schema Example
```javascript
{
  _id: "doc_001",
  docno: "DOC-2025-001",
  revno: "v2.1",
  status: "published",
  approved_by: ["user1", "user2"],
  files: ["file1.pdf", "file2.docx"],
  revision_history: [...]
}
```

---

## Pattern 5: Forecast/Planning System

### Use Case
Managing forecasts with historical tracking and calculations.

### Components
1. **User Interface**
   - Create Forecast Form
   - Dashboard View
   - Sidebar Summary

2. **Application Services**
   - SaveForecast(): Create/update
   - MoveForecast(): Adjust period
   - DuplicateForecast(): Copy entries
   - DeleteForecast(): Remove entries
   - GetDashboardForecast(): Retrieve display data

3. **Database Collections**
   - forecast: Current forecasts
   - forecast_acc_hist: Historical accuracy
   - forecast_d365_arch: Archived data

### Operations
- CRUD on forecasts
- Period-based grouping
- Import/export capabilities
- Historical comparison

---

## Pattern 6: Account Balance Tracking

### Use Case
Real-time balance calculation from multiple transaction sources.

### Components
1. **Data Sources**
   - Opening balance
   - Incoming items
   - Outgoing items
   - Commitments
   - Forecasts

2. **Calculation Pattern**
```
Balance = Opening + Incoming - Outgoing - Committed - Forecasted
```

3. **Database Collections**
   - itemcard_balance: Transaction ledger
   - ordervims: Order tracking
   - commitment: Committed quantities

### API Response Structure
```json
{
  "item_id": "item_001",
  "opening_balance": 1000,
  "incoming": 200,
  "outgoing": 150,
  "committed": 300,
  "available": 750
}
```

---

## Pattern 7: Quarantine/Hold Management

### Use Case
Isolating items pending inspection or approval.

### Components
1. **User Interface**
   - Create Quarantine
   - Assign Quarantine
   - Release Request
   - Release Execution

2. **Status Flow**
```
Normal Stock → Quarantined → Release Requested → Released → Normal Stock
```

3. **Services**
   - EventQuarantineCreated
   - EventAssignQuarantine
   - EventAssignRelease
   - EventReleaseQuarantine

---

## Pattern 8: Vendor/3rd Party Management

### Use Case
Managing external vendor instructions and invoices.

### Components
1. **User Interface**
   - Create Instruction Form
   - Upload Invoice
   - Track Completion

2. **Document Structure**
```javascript
{
  _id: "instruction_001",
  vendor: "vendor_id",
  items: [...],
  vendor_invoices: [...],
  status: "open|completed|cancelled",
  cost_details: {...}
}
```

3. **Services**
   - CreateLogisticInstruction
   - AddVendorInvoice
   - CompleteInstruction

---

## Pattern 9: Report Generation

### Use Case
Generating various reports from transactional data.

### Report Types
1. **Summary Reports**: Aggregated data
2. **Detail Reports**: Line-item level
3. **Comparison Reports**: Period-over-period
4. **Aging Reports**: Time-based analysis

### Common Parameters
- Date range (start_date, end_date)
- Customer filter
- Location filter
- Item filter
- Format (PDF, Excel, JSON)

### API Pattern
```
GET /api/report/{report_type}?start_date={date}&end_date={date}&customer={id}
```

---

## Pattern 10: Notification System

### Use Case
Sending notifications to relevant users based on events.

### Components
1. **Notification Triggers**
   - Record created
   - Status changed
   - Approval required
   - Deadline approaching

2. **Notification Channels**
   - In-app notifications
   - Email
   - SMS (if applicable)

3. **Database Schema**
```javascript
{
  _id: "notif_001",
  userId: "user_001",
  message: "Approval required for...",
  read: false,
  timestamp: 1234567890,
  url: "/path/to/resource"
}
```

---

## Pattern 11: Mill/Manufacturer Management

### Use Case
Managing manufacturer data with audit and compliance tracking.

### Components
1. **Core Collections**
   - mill: Manufacturer master data
   - milladvisor: Audit records
   - mill_ncr: Non-conformance reports
   - mill_moc: Management of change

2. **Workflows**
   - Mill listing/registration
   - Audit request → Audit execution → Report
   - NCR creation → Response → Resolution
   - MoC submission → Evaluation → Approval

---

## Pattern 12: Item Master Data Management

### Use Case
Managing complex item specifications with multiple attributes.

### Key Fields to Include
- Basic info (name, code, description)
- Technical specs (OD, WT, grade, connection)
- Classification (type, product_level, range)
- Pricing (unit price, batch price)
- Relationships (parent items, assemblies)
- Status flags (active, trackable)

### Related Collections
- item: Main item data
- item_type: Item categories
- od, grade, connection: Master data tables

---

## Pattern 13: User & Role Management

### Components
1. **User Collection Schema**
```javascript
{
  _id: "user_001",
  email: "user@example.com",
  name: "John Doe",
  role: ["admin", "manager"],
  active: "Y",
  privilege: {...},
  customer: "customer_id", // if external user
  yard_list: [...] // assigned locations
}
```

2. **Privilege System**
   - Role-based access control
   - Menu-level permissions
   - Feature-level permissions

---

## Integration Patterns

### API Error Response Standard
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human readable message",
    "field": "field_name"
  }
}
```

### API Success Response Standard
```json
{
  "success": true,
  "data": {...},
  "meta": {
    "timestamp": 1234567890,
    "page": 1,
    "total": 100
  }
}
```

### Pagination Standard
```
GET /api/resource?page=1&limit=20&sort=created_at&order=desc
```

---

## Database Patterns

### Common Field Patterns

**Timestamps:**
```javascript
{
  createdate: 1234567890,
  updatedate: 1234567890,
  createwho: "user_id",
  updatewho: "user_id"
}
```

**Status Tracking:**
```javascript
{
  status: "draft|submitted|approved|rejected|completed",
  progress: 0-100
}
```

**Approval Chain:**
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

**File Attachments:**
```javascript
{
  files: [
    {
      filename: "document.pdf",
      path: "/storage/files/...",
      uploaded_by: "user_id",
      uploaded_at: 1234567890
    }
  ]
}
```

---

## Best Practices from the Reference Document

1. **Always include audit fields**: who, when, what
2. **Use event-driven architecture** for complex workflows
3. **Separate ledgers**: Available vs Physical vs Committed
4. **Version documents** with revision tracking
5. **Use status flags** to track workflow stages
6. **Include notification service** in all major actions
7. **Support bulk operations** where applicable
8. **Implement proper error handling** with meaningful messages
9. **Use middleware** for authentication and authorization
10. **Keep database schema flexible** (MongoDB document structure)

---

## Customization Notes

When adapting these patterns for your application:

1. **Simplify where needed** - Not all apps need complex approval workflows
2. **Add your domain logic** - These are templates, customize for your business rules
3. **Adjust status flows** - Match your actual business processes
4. **Choose appropriate collections** - Don't over-normalize or under-normalize
5. **Consider scale** - Add indexes, caching, and optimization as needed
6. **Document assumptions** - Note any business rules or constraints
7. **Plan for growth** - Design for future features and scale

---

## Quick Reference: Common Operations

### Create with Approval
1. User creates → Status: Draft
2. User submits → Status: Submitted
3. Manager reviews → Status: Reviewed
4. Manager approves → Status: Approved

### Transfer/Movement
1. Create transfer record
2. Dispatch (reduce source stock)
3. In transit (track)
4. Receive (add destination stock)

### Document Upload
1. Upload file to storage
2. Save metadata to database
3. Link to parent record
4. Track version if updated

### Notification Flow
1. Event occurs (create, update, approve)
2. Identify recipients
3. Create notification record
4. Send via channel (email, in-app)
5. Track read status