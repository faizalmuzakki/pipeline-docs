# Project Documentation Procedures Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>Procedure List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Create Procedure"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit Procedure"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>View Procedure Detail"]
    UI5["&lt;&lt;UI&gt;&gt;<br/>Delete Procedure"]
    UI6["&lt;&lt;UI&gt;&gt;<br/>Copy Procedure"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC5["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC6["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>ProcedureService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>ProcedureService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>ProcedureService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>ProcedureService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>ProcedureService"]
    AS6["&lt;&lt;Application Service&gt;&gt;<br/>ProcedureService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: Procedure List (DataTables)
    UI1 -->|request procedure list| SEC1
    SEC1 -.->|get datatables| AS1
    AS1 -.->|get procedure data| DB1

    %% Flow 2: Create Procedure
    UI2 -->|send procedure data| SEC2
    SEC2 -.->|create procedure| AS2
    AS2 -.->|save procedure to DB| DB1

    %% Flow 3: Edit Procedure
    UI3 -->|send update data| SEC3
    SEC3 -.->|update procedure| AS3
    AS3 -.->|update procedure in DB| DB1

    %% Flow 4: View Procedure Detail
    UI4 -->|request procedure detail| SEC4
    SEC4 -.->|get procedure detail| AS4
    AS4 -.->|fetch procedure from DB| DB1

    %% Flow 5: Delete Procedure
    UI5 -->|send delete request| SEC5
    SEC5 -.->|delete procedure| AS5
    AS5 -.->|delete procedure from DB| DB1

    %% Flow 6: Copy Procedure
    UI6 -->|send copy request| SEC6
    SEC6 -.->|copy procedure| AS6
    AS6 -.->|create new procedure in DB| DB1

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

The **Procedures** module within the Documentation section is used for managing project-specific procedures and instructions. It provides a centralized place to store, track, and distribute procedural documents.

### Key Features:
- **Procedure Listing**: A data table view of all project procedures, showing details like document number, name, date, and description.
- **CRUD and Copy Operations**: Full capabilities to Create, Read, Update, Delete, and Copy procedures.
- **Tagging**: Allows for the categorization of procedures for better organization.
- **File Management**: Supports attaching and downloading procedure documents.

### Data Flow:
1.  **UI Request**: An action is initiated from the Vue.js frontend.
2.  **Security**: The request is validated by a middleware layer.
3.  **Service Processing**: The `ProcedureService` handles the core business logic.
4.  **Database Interaction**: The service performs the necessary CRUD operations on the MongoDB database.
5.  **Response**: The service returns a response to the frontend, which updates the UI.

### Technical Components:
-   **Frontend**: A suite of Vue.js components for managing procedures.
-   **Backend**: A Laravel API with a `ProcedureController` and `ProcedureService`.
-   **Database**: MongoDB for storing procedure metadata and file paths.
-   **Security**: Standard Laravel middleware for API route protection.
