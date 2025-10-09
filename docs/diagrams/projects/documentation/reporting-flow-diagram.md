# Project Documentation Reporting Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>Report List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Create Report"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit Report"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>View Report Detail"]
    UI5["&lt;&lt;UI&gt;&gt;<br/>Delete Report"]
    UI6["&lt;&lt;UI&gt;&gt;<br/>Copy Report"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC5["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC6["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>ReportingService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>ReportingService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>ReportingService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>ReportingService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>ReportingService"]
    AS6["&lt;&lt;Application Service&gt;&gt;<br/>ReportingService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: Report List (DataTables)
    UI1 -->|request report list| SEC1
    SEC1 -.->|get datatables| AS1
    AS1 -.->|get report data| DB1

    %% Flow 2: Create Report
    UI2 -->|send report data| SEC2
    SEC2 -.->|create report| AS2
    AS2 -.->|save report to DB| DB1

    %% Flow 3: Edit Report
    UI3 -->|send update data| SEC3
    SEC3 -.->|update report| AS3
    AS3 -.->|update report in DB| DB1

    %% Flow 4: View Report Detail
    UI4 -->|request report detail| SEC4
    SEC4 -.->|get report detail| AS4
    AS4 -.->|fetch report from DB| DB1

    %% Flow 5: Delete Report
    UI5 -->|send delete request| SEC5
    SEC5 -.->|delete report| AS5
    AS5 -.->|delete report from DB| DB1

    %% Flow 6: Copy Report
    UI6 -->|send copy request| SEC6
    SEC6 -.->|copy report| AS6
    AS6 -.->|create new report in DB| DB1

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

The **Reporting** module within the Documentation section is used for managing and distributing formal project reports. It allows users to upload, categorize, and track various reports throughout the project lifecycle.

### Key Features:
- **Report Listing**: A data table view of all project reports, showing details like report name, date, description, and tags.
- **CRUD and Copy Operations**: Full capabilities to Create, Read, Update, Delete, and Copy reports.
- **Tagging**: Enables the categorization of reports for easier filtering and organization.
- **File Management**: Supports attaching and downloading report files.

### Data Flow:
1.  **UI Request**: An action is initiated from the Vue.js frontend.
2.  **Security**: The request is validated by a middleware layer.
3.  **Service Processing**: The `ReportingService` handles the core business logic.
4.  **Database Interaction**: The service performs the necessary CRUD operations on the MongoDB database.
5.  **Response**: The service returns a response to the frontend, which updates the UI.

### Technical Components:
-   **Frontend**: A suite of Vue.js components for managing reports.
-   **Backend**: A Laravel API with a `ReportingController` and `ReportingService`.
-   **Database**: MongoDB for storing report metadata and file paths.
-   **Security**: Standard Laravel middleware for API route protection.
