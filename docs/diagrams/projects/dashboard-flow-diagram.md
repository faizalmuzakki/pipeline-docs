# Project Dashboard Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>Dashboard View"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Add Widget"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit Widget"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>Rearrange Widgets"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>DashboardService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>DashboardService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>DashboardService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>DashboardService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>OverallSowService"]
    AS6["&lt;&lt;Application Service&gt;&gt;<br/>SOWService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: Dashboard View
    UI1 -->|request dashboard| SEC1
    SEC1 -.->|get widgets| AS1
    AS1 -.->|get widget data from DB| DB1
    AS1 -.->|get progress stats| AS5
    AS5 -.->|get progress from DB| DB1

    %% Flow 2: Add Widget
    UI2 -->|send widget data| SEC2
    SEC2 -.->|create widget| AS2
    AS2 -.->|save widget to DB| DB1

    %% Flow 3: Edit Widget
    UI3 -->|send update data| SEC3
    SEC3 -.->|update widget| AS3
    AS3 -.->|update widget in DB| DB1

    %% Flow 4: Rearrange Widgets
    UI4 -->|send arrangement| SEC4
    SEC4 -.->|rearrange widgets| AS4
    AS4 -.->|update positions in DB| DB1
    AS4 -.->|get chart data| AS6
    AS6 -.->|get chart stats from DB| DB1

    %% Styling to match original colors
    classDef uiClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef secClass fill:#2d5a5a,stroke:#fff,stroke-width:2px,color:#fff
    classDef appClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff
    classDef dbClass fill:#4a9999,stroke:#fff,stroke-width:2px,color:#fff

    class UI1,UI2,UI3,UI4 uiClass
    class SEC1,SEC2,SEC3,SEC4 secClass
    class AS1,AS2,AS3,AS4,AS5,AS6 appClass
    class DB1 dbClass
```
