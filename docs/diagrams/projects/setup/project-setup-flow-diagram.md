# Project Setup Module Flow Diagram

```mermaid
flowchart TD
    %% UI Component
    UI1["&lt;&lt;UI&gt;&gt;<br/>ProjectSetupComponent"]

    %% Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>DashboardService"]
    SUB_AS1["&lt;&lt;Application Service&gt;&gt;<br/>SOWService"]
    SUB_AS2["&lt;&lt;Application Service&gt;&gt;<br/>WorkOrderService"]
    SUB_AS3["&lt;&lt;Application Service&gt;&gt;<br/>SpecificationService"]
    SUB_AS4["&lt;&lt;Application Service&gt;&gt;<br/>ProjectControlService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow
    UI1 -->|request project setup info| SEC1
    SEC1 -.->|get_project_setup_info| AS1

    AS1 -.->|get setup status| SUB_AS1
    AS1 -.->|get setup status| SUB_AS2
    AS1 -.->|get setup status| SUB_AS3
    AS1 -.->|get setup status| SUB_AS4

    SUB_AS1 -.->|query SOWs| DB1
    SUB_AS2 -.->|query Work Orders| DB1
    SUB_AS3 -.->|query Specifications| DB1
    SUB_AS4 -.->|query Project Controls| DB1

    %% Styling
    classDef uiClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef secClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef appClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff
    classDef dbClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff

    class UI1 uiClass
    class SEC1 secClass
    class AS1,SUB_AS1,SUB_AS2,SUB_AS3,SUB_AS4 appClass
    class DB1 dbClass
```

## Module Description

The **Project Setup** module acts as a centralized checklist to display the completion status of key project areas. It is a read-only view that aggregates data from multiple other modules to provide a high-level summary of project readiness.

### Key Features:
- **Aggregated View**: Displays the setup status for Scopes of Work (SOWs), Work Orders, Specifications, and Project Controls.
- **Status Summary**: For each area, it shows a count of completed items versus the total number of items (e.g., "5 out of 10 SOWs Submitted").
- **Direct Navigation**: Provides direct links to the detail or edit pages of incomplete items, allowing users to quickly address missing setup.

### Data Flow:
1.  **UI Request**: The `ProjectSetupComponent` requests the project setup information.
2.  **Primary Service**: The request is handled by the `DashboardService`, which acts as an orchestrator.
3.  **Data Aggregation**: The `DashboardService` calls the `getProjectSetupInfo` method on several other services (`SOWService`, `WorkOrderService`, `SpecificationService`, `ProjectControlService`).
4.  **Database Queries**: Each of these sub-services queries the MongoDB database to get the status and count of its respective items.
5.  **Response**: The aggregated summary data is returned to the UI for display.

### Technical Components:
-   **Frontend**: A single Vue.js component (`ProjectSetupComponent`) to display the checklist.
-   **Backend**: The `DashboardService` orchestrates calls to multiple other services to gather the required data.
-   **Database**: MongoDB is queried by the various sub-services.
-   **Security**: Standard Laravel middleware protects the API endpoint.
