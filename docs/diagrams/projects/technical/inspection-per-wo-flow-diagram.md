# Project Inspection Per WO Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>Inspection Per WO List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>View Work Order Detail"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>InspectionPerWOService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>WorkOrderDetailService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: Inspection Per WO List (DataTables)
    UI1 -->|request inspection data| SEC1
    SEC1 -.->|get datatables| AS1
    AS1 -.->|get inspection data| DB1

    %% Flow 2: View Work Order Detail
    UI2 -->|request WO details| SEC2
    SEC2 -.->|get WO data| AS2
    AS2 -.->|fetch WO details| DB1

    %% Styling to match original colors
    classDef uiClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef secClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef appClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff
    classDef dbClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff

    class UI1,UI2 uiClass
    class SEC1,SEC2 secClass
    class AS1,AS2 appClass
    class DB1 dbClass
```

## Module Description

The **Inspection Per WO** module provides a comprehensive view of work orders that require inspection, displaying detailed information about manufacturing and coating processes.

### Key Features:
- **Work Order Listing**: Displays work orders with item descriptions, vendors, activities, quantities, specifications, and related SOW
- **Data Aggregation**: The service layer aggregates data from multiple database collections (WorkOrder, Item, Mill, SOW, Specification)
- **Advanced Sorting**: Custom sorting logic with priority-based ordering for related SOW
- **Process Filtering**: Filters work orders by production and coating processes
- **Navigation**: Direct navigation to detailed work order views

### Data Flow:
1. **UI Request**: Vue component requests inspection data via datatables endpoint
2. **Service Processing**: InspectionPerWOService processes the request and queries the database
3. **Data Aggregation**: Service combines data from work orders, items, manufacturers, specifications, and SOW from the database
4. **Priority Calculation**: Applies custom priority logic for sorting related SOW items
5. **Response**: Returns formatted data with inspection details for display

### Technical Components:
- **Frontend**: Vue.js component with DataTable integration
- **Backend**: Laravel service with repository pattern
- **Database**: MongoDB for data persistence
- **Security**: Middleware layer for request validation
