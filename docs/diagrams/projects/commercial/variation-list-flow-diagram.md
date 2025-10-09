# Project Commercial Variation List Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>Variation List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Create Variation"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit Variation"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>View Variation Detail"]
    UI5["&lt;&lt;UI&gt;&gt;<br/>Delete Variation"]
    UI6["&lt;&lt;UI&gt;&gt;<br/>Copy Variation"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC5["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC6["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>VariationService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>VariationService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>VariationService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>VariationService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>VariationService"]
    AS6["&lt;&lt;Application Service&gt;&gt;<br/>VariationService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: Variation List (DataTables)
    UI1 -->|request variation list| SEC1
    SEC1 -.->|get datatables| AS1
    AS1 -.->|get variation data| DB1

    %% Flow 2: Create Variation
    UI2 -->|send variation data| SEC2
    SEC2 -.->|create variation| AS2
    AS2 -.->|save variation to DB| DB1

    %% Flow 3: Edit Variation
    UI3 -->|send update data| SEC3
    SEC3 -.->|update variation| AS3
    AS3 -.->|update variation in DB| DB1

    %% Flow 4: View Variation Detail
    UI4 -->|request variation detail| SEC4
    SEC4 -.->|get variation detail| AS4
    AS4 -.->|fetch variation from DB| DB1

    %% Flow 5: Delete Variation
    UI5 -->|send delete request| SEC5
    SEC5 -.->|delete variation| AS5
    AS5 -.->|delete variation from DB| DB1

    %% Flow 6: Copy Variation
    UI6 -->|send copy request| SEC6
    SEC6 -.->|copy variation| AS6
    AS6 -.->|create new variation in DB| DB1

    %% Styling to match original colors
    classDef uiClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef secClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef appClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff
    classDef dbClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff

    class UI1,UI2,UI3,UI4,UI5,UI6 uiClass
    class SEC1,SEC2,SEC3,SEC4,SEC5,SEC6 secClass
    class AS1,AS2,AS3,AS4,AS5,AS6 appClass
    class DB1 dbClass
```

## Module Description

The **Variation List** module manages variation orders within the project's commercial scope. It allows for the tracking of changes, additions, or modifications to the original contract.

### Key Features:
- **Variation Listing**: A data table view of all variation orders, including details like variation number, document name, date, and description.
- **CRUD and Copy Operations**: Full capabilities to Create, Read, Update, Delete, and Copy variation orders.
- **File Management**: Supports attaching and downloading relevant documents for each variation.
- **Search and Filter**: Provides tools to search and filter through the list of variations.

### Data Flow:
1.  **UI Request**: An action is initiated from the Vue.js frontend.
2.  **Security**: The request passes through a middleware layer for validation.
3.  **Service Processing**: The `VariationService` handles the business logic for the requested action (e.g., creating, deleting, copying).
4.  **Database Interaction**: The service performs the necessary operations on the MongoDB database.
5.  **Response**: The service returns a response to the frontend, which updates the UI.

### Technical Components:
-   **Frontend**: A suite of Vue.js components for listing, creating, editing, viewing, and copying variations.
-   **Backend**: A Laravel API with a `VariationController` and `VariationService`.
-   **Database**: MongoDB for storing variation order data and document metadata.
-   **Security**: Standard Laravel middleware for API route protection.
