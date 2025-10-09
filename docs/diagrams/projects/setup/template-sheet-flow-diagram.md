# Project Setup Template Sheet Module Flow Diagram

```mermaid
flowchart TD
    %% UI Component
    UI1["&lt;&lt;UI&gt;&gt;<br/>TemplateSheetComponent"]

    %% Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Application Service
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>TemplateSheetService"]

    %% File System
    FS1["&lt;&lt;File System&gt;&gt;<br/>Local Storage"]

    %% Flow 1: List Templates
    UI1 -->|request template list| SEC1
    SEC1 -.->|get datatables| AS1
    AS1 -.->|read template files| FS1

    %% Flow 2: Download Template(s)
    UI1 -->|request download| SEC2
    SEC2 -.->|download| AS1
    AS1 -.->|read & zip files| FS1

    %% Styling
    classDef uiClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef secClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef appClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff
    classDef dbClass fill:#994a4a,stroke:#fff,stroke-width:2px,color:#fff

    class UI1 uiClass
    class SEC1,SEC2 secClass
    class AS1 appClass
    class FS1 dbClass
```

## Module Description

The **Template Sheet** module provides a centralized location for users to download predefined template files for various project activities. This module is read-only and interacts with the local file system, not the database.

### Key Features:
- **Template Listing**: Displays a list of available template sheets, grouped by activity (e.g., Manufacturing, Coating).
- **File Download**: Allows users to download individual template files or select multiple files to download as a single zip archive.

### Data Flow:
1.  **List Request**: The UI requests the list of templates.
2.  **Service Processing**: The `TemplateSheetService` reads a predefined configuration constant (`TEMPLATE_SHEET`) to identify the available templates.
3.  **File System Interaction**: The service checks the local file system for the existence and last-modified date of each template file.
4.  **Response**: The service returns the structured list of activities and their associated templates to the UI.
5.  **Download Request**: The user initiates a download for one or more files.
6.  **File Handling**: The `TemplateSheetService` retrieves the requested file(s) from the file system. If multiple files are requested, it creates a zip archive before sending the download response.

### Technical Components:
-   **Frontend**: A single Vue.js component (`TemplateSheetComponent`) to list and select templates.
-   **Backend**: The `TemplateSheetService` handles file system interactions and zipping.
-   **Storage**: Relies on the local server file system to store the template files.
-   **Security**: Standard Laravel middleware protects the API endpoints.
