# Global Port & Locode Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>Locodes List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Create Locode"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit Locode"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>Search Locodes"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>LocodeService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>LocodeService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>LocodeService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>LocodeService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>EmailService"]
    AS6["&lt;&lt;Application Service&gt;&gt;<br/>EmailService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: Locodes List
    UI1 -->|request locodes list| SEC1
    SEC1 -.->|get locodes data| AS1
    AS1 -.->|get locodes from DB| DB1

    %% Flow 2: Create Locode
    UI2 -->|send locode data| SEC2
    SEC2 -.->|create locode| AS2
    AS2 -.->|save locode to DB| DB1
    AS2 -.->|send notification| AS5
    AS5 -.->|email notification| DB1

    %% Flow 3: Edit Locode
    UI3 -->|send update data| SEC3
    SEC3 -.->|update locode| AS3
    AS3 -.->|update locode in DB| DB1
    AS3 -.->|send notification| AS6
    AS6 -.->|email notification| DB1

    %% Flow 4: Search Locodes
    UI4 -->|search request| SEC4
    SEC4 -.->|search locodes| AS4
    AS4 -.->|search in DB| DB1

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
