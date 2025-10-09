# Project Documentation NCR Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>NCR List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Create NCR"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit NCR"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>View NCR Detail"]
    UI5["&lt;&lt;UI&gt;&gt;<br/>Delete NCR"]
    UI6["&lt;&lt;UI&gt;&gt;<br/>Copy NCR"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC5["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC6["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>NCRService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>NCRService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>NCRService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>NCRService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>NCRService"]
    AS6["&lt;&lt;Application Service&gt;&gt;<br/>NCRService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: NCR List (DataTables)
    UI1 -->|request ncr list| SEC1
    SEC1 -.->|get datatables| AS1
    AS1 -.->|get ncr data| DB1

    %% Flow 2: Create NCR
    UI2 -->|send ncr data| SEC2
    SEC2 -.->|create ncr| AS2
    AS2 -.->|save ncr to DB| DB1

    %% Flow 3: Edit NCR
    UI3 -->|send update data| SEC3
    SEC3 -.->|update ncr| AS3
    AS3 -.->|update ncr in DB| DB1

    %% Flow 4: View NCR Detail
    UI4 -->|request ncr detail| SEC4
    SEC4 -.->|get ncr detail| AS4
    AS4 -.->|fetch ncr from DB| DB1

    %% Flow 5: Delete NCR
    UI5 -->|send delete request| SEC5
    SEC5 -.->|delete ncr| AS5
    AS5 -.->|delete ncr from DB| DB1

    %% Flow 6: Copy NCR
    UI6 -->|send copy request| SEC6
    SEC6 -.->|copy ncr| AS6
    AS6 -.->|create new ncr in DB| DB1

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

The **NCR (Non-Conformance Report)** module is used to document and track any deviations from the specified requirements or standards during the project. It provides a formal process for managing non-conformances.

### Key Features:
- **NCR Listing**: A data table view of all Non-Conformance Reports, showing details like reference number, document name, date, and status.
- **CRUD and Copy Operations**: Full capabilities to Create, Read, Update, Delete, and Copy NCRs.
- **Status Tracking**: Allows for tracking the status of each NCR (e.g., In Progress, Completed).
- **File Management**: Supports attaching and downloading relevant evidence or documentation.

### Data Flow:
1.  **UI Request**: An action is initiated from the Vue.js frontend.
2.  **Security**: The request is validated by a middleware layer.
3.  **Service Processing**: The `NCRService` handles the core business logic.
4.  **Database Interaction**: The service performs the necessary CRUD operations on the MongoDB database.
5.  **Response**: The service returns a response to the frontend, which updates the UI.

### Technical Components:
-   **Frontend**: A suite of Vue.js components for managing NCRs.
-   **Backend**: A Laravel API with an `NCRController` and `NCRService`.
-   **Database**: MongoDB for storing all NCR data.
-   **Security**: Standard Laravel middleware for API route protection.
