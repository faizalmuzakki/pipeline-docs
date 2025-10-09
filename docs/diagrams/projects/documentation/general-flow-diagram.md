# Project Documentation General Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>General Document List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Create Document"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit Document"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>View Document Detail"]
    UI5["&lt;&lt;UI&gt;&gt;<br/>Delete Document"]
    UI6["&lt;&lt;UI&gt;&gt;<br/>Copy Document"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC5["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC6["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>GeneralService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>GeneralService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>GeneralService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>GeneralService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>GeneralService"]
    AS6["&lt;&lt;Application Service&gt;&gt;<br/>GeneralService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: Document List (DataTables)
    UI1 -->|request document list| SEC1
    SEC1 -.->|get datatables| AS1
    AS1 -.->|get document data| DB1

    %% Flow 2: Create Document
    UI2 -->|send document data| SEC2
    SEC2 -.->|create document| AS2
    AS2 -.->|save document to DB| DB1

    %% Flow 3: Edit Document
    UI3 -->|send update data| SEC3
    SEC3 -.->|update document| AS3
    AS3 -.->|update document in DB| DB1

    %% Flow 4: View Document Detail
    UI4 -->|request document detail| SEC4
    SEC4 -.->|get document detail| AS4
    AS4 -.->|fetch document from DB| DB1

    %% Flow 5: Delete Document
    UI5 -->|send delete request| SEC5
    SEC5 -.->|delete document| AS5
    AS5 -.->|delete document from DB| DB1

    %% Flow 6: Copy Document
    UI6 -->|send copy request| SEC6
    SEC6 -.->|copy document| AS6
    AS6 -.->|create new document in DB| DB1

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

The **General Documentation** module provides a repository for managing various project-related documents that do not fit into other specific categories. It allows for the uploading, tagging, and tracking of general documents.

### Key Features:
- **Document Listing**: A data table view of all general documents, showing details like document number, name, date, and description.
- **CRUD and Copy Operations**: Full capabilities to Create, Read, Update, Delete, and Copy documents.
- **Tagging**: Allows for the categorization of documents using tags.
- **File Management**: Supports attaching and downloading document files.

### Data Flow:
1.  **UI Request**: An action is initiated from the Vue.js frontend.
2.  **Security**: The request is validated by a middleware layer.
3.  **Service Processing**: The `GeneralService` handles the business logic for the requested action.
4.  **Database Interaction**: The service performs the necessary CRUD operations on the MongoDB database.
5.  **Response**: The service returns a response to the frontend, which updates the UI.

### Technical Components:
-   **Frontend**: A suite of Vue.js components for listing, creating, editing, and viewing general documents.
-   **Backend**: A Laravel API with a `GeneralController` and `GeneralService`.
-   **Database**: MongoDB for storing document data and metadata.
-   **Security**: Standard Laravel middleware for API route protection.
