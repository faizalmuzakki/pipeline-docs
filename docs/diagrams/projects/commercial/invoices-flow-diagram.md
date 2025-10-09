# Project Commercial Invoices Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>Invoice List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Create Invoice"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit Invoice"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>View Invoice Detail"]
    UI5["&lt;&lt;UI&gt;&gt;<br/>Delete Invoice"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC5["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>InvoiceService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>InvoiceService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>InvoiceService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>InvoiceService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>InvoiceService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: Invoice List (DataTables)
    UI1 -->|request invoice list| SEC1
    SEC1 -.->|get datatables| AS1
    AS1 -.->|get invoice data| DB1

    %% Flow 2: Create Invoice
    UI2 -->|send invoice data| SEC2
    SEC2 -.->|create invoice| AS2
    AS2 -.->|save invoice to DB| DB1

    %% Flow 3: Edit Invoice
    UI3 -->|send update data| SEC3
    SEC3 -.->|update invoice| AS3
    AS3 -.->|update invoice in DB| DB1

    %% Flow 4: View Invoice Detail
    UI4 -->|request invoice detail| SEC4
    SEC4 -.->|get invoice detail| AS4
    AS4 -.->|fetch invoice from DB| DB1

    %% Flow 5: Delete Invoice
    UI5 -->|send delete request| SEC5
    SEC5 -.->|delete invoice| AS5
    AS5 -.->|delete invoice from DB| DB1

    %% Styling to match original colors
    classDef uiClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef secClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef appClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff
    classDef dbClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff

    class UI1,UI2,UI3,UI4,UI5 uiClass
    class SEC1,SEC2,SEC3,SEC4,SEC5 secClass
    class AS1,AS2,AS3,AS4,AS5 appClass
    class DB1 dbClass
```

## Module Description

The **Invoices** module handles the creation and management of project invoices. It allows for tracking financial transactions, linking them to specific Scopes of Work (SOW), and managing their payment status.

### Key Features:
- **Invoice Listing**: A comprehensive data table view of all project invoices, showing details like invoice number, date, SOW, value, and status.
- **CRUD Operations**: Full capabilities to Create, Read, Update, and Delete invoices.
- **Status Tracking**: Allows for tracking the payment status of each invoice (e.g., Unpaid, Paid).
- **File Management**: Supports attaching and downloading invoice documents.

### Data Flow:
1.  **UI Request**: An action is initiated from the Vue.js frontend.
2.  **Security**: The request is validated by a middleware layer.
3.  **Service Processing**: The `InvoiceService` handles the business logic for the requested action.
4.  **Database Interaction**: The service performs the necessary CRUD operations on the MongoDB database.
5.  **Response**: The service returns a response to the frontend, which updates the UI.

### Technical Components:
-   **Frontend**: Vue.js components for listing, creating, editing, and viewing invoices.
-   **Backend**: A Laravel API with an `InvoiceController` and `InvoiceService`.
-   **Database**: MongoDB for storing all invoice data.
-   **Security**: Standard Laravel middleware for API route protection.
