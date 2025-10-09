# Project Commercial Contract Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>Contract List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Create Contract"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit Contract"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>View Contract Detail"]
    UI5["&lt;&lt;UI&gt;&gt;<br/>Delete Contract"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC5["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>ContractService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>ContractService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>ContractService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>ContractService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>ContractService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: Contract List (DataTables)
    UI1 -->|request contract list| SEC1
    SEC1 -.->|get datatables| AS1
    AS1 -.->|get contract data| DB1

    %% Flow 2: Create Contract
    UI2 -->|send contract data| SEC2
    SEC2 -.->|create contract| AS2
    AS2 -.->|save contract to DB| DB1

    %% Flow 3: Edit Contract
    UI3 -->|send update data| SEC3
    SEC3 -.->|update contract| AS3
    AS3 -.->|update contract in DB| DB1

    %% Flow 4: View Contract Detail
    UI4 -->|request contract detail| SEC4
    SEC4 -.->|get contract detail| AS4
    AS4 -.->|fetch contract from DB| DB1

    %% Flow 5: Delete Contract
    UI5 -->|send delete request| SEC5
    SEC5 -.->|delete contract| AS5
    AS5 -.->|delete contract from DB| DB1

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

The **Contract** module within the Commercial section is designed to manage all contractual documents related to the project. It allows users to upload, track, and manage contracts and their associated metadata.

### Key Features:
- **Contract Listing**: A centralized data table displays all project contracts, with details like document number, name, description, and tags.
- **CRUD Operations**: Provides full capabilities to Create, Read, Update, and Delete contracts.
- **File Attachments**: Supports uploading, downloading, and managing contract document files.
- **Search and Filter**: Allows users to easily find contracts through searching and filtering on various attributes.

### Data Flow:
1.  **UI Request**: An action is initiated from the Vue.js frontend (e.g., listing contracts, creating a new one).
2.  **Security**: The request is validated by a middleware layer for authentication and authorization.
3.  **Service Processing**: The `ContractService` handles the core business logic for the requested action.
4.  **Database Interaction**: The service performs the necessary operations on the MongoDB database.
5.  **Response**: A response is sent back to the frontend, which updates the view for the user.

### Technical Components:
-   **Frontend**: Vue.js components for listing, creating, editing, and viewing contracts.
-   **Backend**: A Laravel API with a `ContractController` and `ContractService`.
-   **Database**: MongoDB for storing contract metadata and file paths.
-   **Security**: Standard Laravel middleware for protecting API routes.
