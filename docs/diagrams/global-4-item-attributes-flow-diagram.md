# Global Item Attributes Module Flow Diagram

```mermaid
flowchart TD
    %% Top row - UI Components (left to right)
    UI1["&lt;&lt;UI&gt;&gt;<br/>Item Attributes List"]
    UI2["&lt;&lt;UI&gt;&gt;<br/>Create Item Attribute"]
    UI3["&lt;&lt;UI&gt;&gt;<br/>Edit Item Attribute"]
    UI4["&lt;&lt;UI&gt;&gt;<br/>View Item Attribute"]

    %% Second row - Security Middleware
    SEC1["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC2["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC3["&lt;&lt;Security&gt;&gt;<br/>Middleware"]
    SEC4["&lt;&lt;Security&gt;&gt;<br/>Middleware"]

    %% Third row - Application Services
    AS1["&lt;&lt;Application Service&gt;&gt;<br/>ItemAttributesService"]
    AS2["&lt;&lt;Application Service&gt;&gt;<br/>ItemAttributesService"]
    AS3["&lt;&lt;Application Service&gt;&gt;<br/>ItemAttributesService"]
    AS4["&lt;&lt;Application Service&gt;&gt;<br/>ItemAttributesService"]
    AS5["&lt;&lt;Application Service&gt;&gt;<br/>EmailService"]
    AS6["&lt;&lt;Application Service&gt;&gt;<br/>EmailService"]

    %% Bottom - Database
    DB1["&lt;&lt;Database&gt;&gt;<br/>MongoDB"]

    %% Flow 1: Item Attributes List
    UI1 -->|request attributes list| SEC1
    SEC1 -.->|get attributes data| AS1
    AS1 -.->|get attributes from DB| DB1

    %% Flow 2: Create Item Attribute
    UI2 -->|send attribute data| SEC2
    SEC2 -.->|create attribute| AS2
    AS2 -.->|save attribute to DB| DB1
    AS2 -.->|send notification| AS5
    AS5 -.->|email notification| DB1

    %% Flow 3: Edit Item Attribute
    UI3 -->|send update data| SEC3
    SEC3 -.->|update attribute| AS3
    AS3 -.->|update attribute in DB| DB1
    AS3 -.->|send notification| AS6
    AS6 -.->|email notification| DB1

    %% Flow 4: View Item Attribute
    UI4 -->|request attribute detail| SEC4
    SEC4 -.->|get attribute detail| AS4
    AS4 -.->|get attribute from DB| DB1

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
