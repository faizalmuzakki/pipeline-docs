# Project Technical MDR Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>MDR List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Create MDR"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit MDR"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>View MDR Detail"]
    UI5["&lt;&lt;UI&gt;&gt;<br/>Delete MDR"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC5["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>MDRService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>MDRService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>MDRService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>MDRService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>MDRService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: MDR List (DataTables)
    UI1 -->|request mdr list| SEC1
    SEC1 -.->|get datatables| AS1
    AS1 -.->|get mdr data| DB1

    %% Flow 2: Create MDR
    UI2 -->|send mdr data| SEC2
    SEC2 -.->|create mdr| AS2
    AS2 -.->|save mdr to DB| DB1

    %% Flow 3: Edit MDR
    UI3 -->|send update data| SEC3
    SEC3 -.->|update mdr| AS3
    AS3 -.->|update mdr in DB| DB1

    %% Flow 4: View MDR Detail
    UI4 -->|request mdr detail| SEC4
    SEC4 -.->|get mdr detail| AS4
    AS4 -.->|fetch mdr from DB| DB1

    %% Flow 5: Delete MDR
    UI5 -->|send delete request| SEC5
    SEC5 -.->|delete mdr| AS5
    AS5 -.->|delete mdr from DB| DB1

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

The **MDR (Master Document Register)** module is used to manage and track all project-related documents. It provides a centralized repository for creating, viewing, updating, and deleting documents and their various revisions.

### Key Features:
- **Document Listing**: Displays a comprehensive list of all MDR documents with details like reference numbers, revision numbers, and status.
- **CRUD Operations**: Full capabilities to Create, Read, Update, and Delete MDR documents.
- **SOW Linking**: Ability to link MDR documents to specific Scope of Work (SOW) items.
- **File Management**: Supports uploading and downloading document attachments.

### Data Flow:
1.  **UI Request**: A request is initiated from the Vue.js frontend for an action (e.g., list, create, delete).
2.  **Security**: The request passes through a middleware layer for authentication and authorization.
3.  **Service Processing**: The `MDRService` handles the business logic, processing the request and interacting with the database.
4.  **Database Interaction**: The service performs the necessary operations (query, insert, update, delete) on the MongoDB database.
5.  **Response**: The service returns a response to the frontend, which then updates the UI accordingly.

### Technical Components:
-   **Frontend**: Vue.js components, including a data table for the list view and modals for creation/editing.
-   **Backend**: A Laravel-based API with a dedicated `MDRController` and `MDRService`.
-   **Database**: MongoDB for storing all MDR data and document metadata.
-   **Security**: Standard Laravel middleware for API route protection.
