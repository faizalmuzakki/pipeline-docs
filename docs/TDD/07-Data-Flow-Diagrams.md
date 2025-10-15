# 7. Data Flow Diagrams

This section illustrates the flow of data through the MCMS Linepipe system using hierarchical Data Flow Diagrams (DFD). Level 0 diagrams provide high-level overviews, while Level 1 diagrams show detailed process interactions.

**Diagram Conventions:**
- **External Entities**: Users, vendors, external systems (rectangles)
- **Processes**: System functions and business logic (rounded rectangles)
- **Data Stores**: Database collections (cylinders/parallel lines)
- **Data Flows**: Movement of data between components (arrows)

---

## 7.1 System Overview - Data Flow Diagram Level 0

```mermaid
graph TB
    subgraph External["External Entities"]
        USER[User/Inspector]
        VENDOR[Vendor/Mill]
        D365[D365 ERP System]
        AIS[AIS Vessel Tracking]
    end

    subgraph MCMS["MCMS Linepipe System"]
        AUTH[Authentication<br/>& Authorization]
        GLOBAL[Global Data<br/>Management]
        PROJECT[Project<br/>Operations]
        QUALITY[Quality<br/>Control]
        LOGISTICS[Transfer &<br/>Logistics]
        COMMERCIAL[Commercial<br/>Operations]
        REPORTING[Reporting &<br/>Analytics]
    end

    subgraph Data["Data Stores"]
        GLOBALDB[(Global DB<br/>mongodb_global)]
        PROJECTDB[(Project DB<br/>mongodb_project_X)]
    end

    USER -->|login credentials| AUTH
    AUTH -->|access token| USER
    AUTH -->|verify token| GLOBALDB

    USER -->|project/vendor data| GLOBAL
    GLOBAL -->|user/project/vendor| GLOBALDB

    USER -->|SOW/WO/inspection data| PROJECT
    PROJECT -->|operational data| PROJECTDB

    USER -->|quality records| QUALITY
    QUALITY -->|inspection/NCR/MDR| PROJECTDB

    USER -->|shipment data| LOGISTICS
    LOGISTICS -->|transfer/cargo| PROJECTDB

    USER -->|contract/invoice| COMMERCIAL
    COMMERCIAL -->|commercial data| PROJECTDB

    USER -->|request reports| REPORTING
    REPORTING -->|read data| PROJECTDB
    REPORTING -->|read data| GLOBALDB
    REPORTING -->|reports/dashboards| USER

    VENDOR -->|MDR/certificates| QUALITY
    QUALITY -->|MDR documents| PROJECTDB

    D365 -->|financial forecast| GLOBAL
    GLOBAL -->|forecast data| GLOBALDB

    AIS -->|vessel position| LOGISTICS
    LOGISTICS -->|tracking data| PROJECTDB

    style MCMS fill:#e1f5ff
    style Data fill:#fff4e6
    style External fill:#f0f0f0
```

*Figure 1: MCMS Linepipe System - Level 0 Data Flow Diagram*

---

## 7.2 SOW Management - Data Flow Diagram Level 0

```mermaid
graph LR
    USER[User] -->|create SOW| SOW_PROCESS[SOW Management<br/>Process]
    SOW_PROCESS -->|SOW data| SOW_DB[(sow collection)]

    SOW_DB -->|SOW details| SOW_PROCESS
    SOW_PROCESS -->|SOW list| USER

    GLOBAL_DB[(item/spec/mill<br/>global collections)] -->|reference data| SOW_PROCESS

    SOW_PROCESS -->|trigger| WO_PROCESS[Work Order<br/>Creation]
    WO_PROCESS -->|work orders| WO_DB[(work_order<br/>collection)]

    SOW_PROCESS -->|split SOW| LOT_PROCESS[Lot Management]
    LOT_PROCESS -->|lots| LOT_DB[(sow_lot<br/>collection)]

    style SOW_PROCESS fill:#4CAF50,color:#fff
    style WO_PROCESS fill:#2196F3,color:#fff
    style LOT_PROCESS fill:#FF9800,color:#fff
```

*Figure 2: SOW Management - Level 0 DFD*

---

## 7.3 SOW Management - Data Flow Diagram Level 1

```mermaid
graph TB
    subgraph UI["User Interface"]
        USER[Project Manager]
    end

    subgraph Security["Security Layer"]
        AUTH[Authentication<br/>Middleware]
        AUTHZ[Authorization<br/>Middleware]
    end

    subgraph Services["Application Services"]
        SOW_SVC[SOW Service]
        ITEM_SVC[Item Service]
        SPEC_SVC[Specification<br/>Service]
        MILL_SVC[Mill Service]
        PROG_SVC[Progress Service]
    end

    subgraph Database["Data Stores"]
        SOW_TBL[(sow)]
        ITEM_TBL[(item)]
        SPEC_TBL[(specification)]
        MILL_TBL[(manufacturer)]
        PROG_TBL[(sow_progress)]
    end

    USER -->|1. Submit SOW form<br/>+ auth token| AUTH
    AUTH -->|2. Validate token| AUTHZ
    AUTHZ -->|3. Check privileges| SOW_SVC

    SOW_SVC -->|4. Validate item| ITEM_SVC
    ITEM_SVC -->|5. Get item data| ITEM_TBL
    ITEM_TBL -->|6. Item details| ITEM_SVC
    ITEM_SVC -->|7. Item valid| SOW_SVC

    SOW_SVC -->|8. Validate spec| SPEC_SVC
    SPEC_SVC -->|9. Get spec data| SPEC_TBL
    SPEC_TBL -->|10. Spec details| SPEC_SVC
    SPEC_SVC -->|11. Spec valid| SOW_SVC

    SOW_SVC -->|12. Validate mill| MILL_SVC
    MILL_SVC -->|13. Get mill data| MILL_TBL
    MILL_TBL -->|14. Mill details| MILL_SVC
    MILL_SVC -->|15. Mill valid| SOW_SVC

    SOW_SVC -->|16. Calculate conversions<br/>17. Generate description| SOW_SVC
    SOW_SVC -->|18. Create SOW| SOW_TBL
    SOW_TBL -->|19. SOW created| SOW_SVC

    SOW_SVC -->|20. Initialize progress| PROG_SVC
    PROG_SVC -->|21. Create progress record| PROG_TBL
    PROG_TBL -->|22. Progress initialized| PROG_SVC

    SOW_SVC -->|23. SOW created successfully| USER

    style Services fill:#e1f5ff
    style Database fill:#fff4e6
```

*Figure 3: SOW Creation - Level 1 DFD*

---

## 7.4 Inspection Management - Data Flow Diagram Level 0

```mermaid
graph LR
    INSPECTOR[Inspector] -->|inspection data| INSP_PROCESS[Inspection<br/>Management]

    INSP_PROCESS -->|inspection records| INSP_DB[(inspection)]
    INSP_PROCESS -->|test results| RESULT_DB[(inspection_result)]

    WO_DB[(work_order)] -->|WO details| INSP_PROCESS
    SOW_DB[(sow)] -->|SOW details| INSP_PROCESS
    SPEC_DB[(specification)] -->|test requirements| INSP_PROCESS

    INSP_PROCESS -->|pass/fail| NCR_PROCESS[NCR Process]
    NCR_PROCESS -->|NCR records| NCR_DB[(ncr)]

    INSP_PROCESS -->|update progress| PROGRESS[Progress<br/>Calculation]
    PROGRESS -->|progress update| PROG_DB[(sow_progress)]

    INSP_PROCESS -->|certificates| INSPECTOR

    style INSP_PROCESS fill:#4CAF50,color:#fff
    style NCR_PROCESS fill:#f44336,color:#fff
    style PROGRESS fill:#2196F3,color:#fff
```

*Figure 4: Inspection Management - Level 0 DFD*

---

## 7.5 Transfer & Logistics - Data Flow Diagram Level 0

```mermaid
graph LR
    USER[Logistics<br/>Coordinator] -->|create transfer| TRANSFER_PROCESS[Transfer<br/>Management]

    TRANSFER_PROCESS -->|transfer data| TRANSFER_DB[(transfer)]
    TRANSFER_PROCESS -->|cargo manifest| CARGO_DB[(transfer_cargo)]
    TRANSFER_PROCESS -->|route data| ROUTE_DB[(transfer_route)]

    SOW_DB[(sow)] -->|items to ship| TRANSFER_PROCESS
    WO_DB[(work_order)] -->|produced items| TRANSFER_PROCESS

    AIS[AIS Vessel<br/>Tracking] -->|vessel position| TRACKING[Vessel Tracking<br/>Service]
    TRACKING -->|update position| VESSEL_DB[(vessel_tracking)]

    TRANSFER_PROCESS -->|MMSI query| TRACKING
    TRACKING -->|vessel ETA| TRANSFER_PROCESS

    TRANSFER_PROCESS -->|delivery note| USER

    LOCODE_DB[(locode<br/>port data)] -->|port details| TRANSFER_PROCESS

    style TRANSFER_PROCESS fill:#4CAF50,color:#fff
    style TRACKING fill:#2196F3,color:#fff
```

*Figure 5: Transfer & Logistics - Level 0 DFD*

---

## 7.6 Progress Tracking - Data Flow Diagram Level 1

```mermaid
graph TB
    subgraph Input["Data Sources"]
        SOW_DB[(sow)]
        WO_DB[(work_order)]
        INSP_DB[(inspection)]
        TRANS_DB[(transfer)]
        WO_ACTUAL[(work_order_actual)]
    end

    subgraph Processing["Progress Calculation Services"]
        PROG_SVC[Progress Service]
        WO_PROG[Work Order<br/>Progress Calc]
        INSP_PROG[Inspection<br/>Progress Calc]
        SHIP_PROG[Shipment<br/>Progress Calc]
        AGG[Aggregation &<br/>Weighting]
    end

    subgraph Output["Progress Storage"]
        SOW_PROG[(sow_progress)]
        OVERALL[(overall_sow)]
        CHART[(progress charts)]
    end

    SOW_DB -->|SOW quantities| PROG_SVC

    WO_DB -->|WO status| WO_PROG
    WO_ACTUAL -->|production data| WO_PROG
    WO_PROG -->|WO %| AGG

    INSP_DB -->|inspection results| INSP_PROG
    INSP_PROG -->|inspection %| AGG

    TRANS_DB -->|shipment status| SHIP_PROG
    SHIP_PROG -->|shipment %| AGG

    PROG_SVC -->|coordinate| AGG
    AGG -->|weighted progress| SOW_PROG
    AGG -->|overall project %| OVERALL
    AGG -->|time series data| CHART

    SOW_PROG -->|historical data| CHART

    USER[User] -->|view progress| CHART
    CHART -->|S-curve display| USER

    style Processing fill:#e1f5ff
    style Output fill:#c8e6c9
```

*Figure 6: Progress Tracking - Level 1 DFD*

---

## 7.7 MDR Management - Data Flow Diagram Level 1

```mermaid
graph TB
    VENDOR[Vendor/Mill] -->|1. Upload MDR PDF| UI[UI: MDR Upload]

    UI -->|2. PDF file<br/>+ metadata| MDR_SVC[MDR Service]

    MDR_SVC -->|3. Store PDF| FILE_STORAGE[(File Storage)]
    FILE_STORAGE -->|4. File path| MDR_SVC

    MDR_SVC -->|5. Extract text| OCR[OCR/PDF<br/>Extraction]
    OCR -->|6. Extracted data| MDR_SVC

    MDR_SVC -->|7. Parse heat numbers,<br/>chemical composition,<br/>mechanical properties| PARSER[Data Parser]

    PARSER -->|8. Structured data| MDR_SVC

    MDR_SVC -->|9. Create MDR record| MDR_DB[(mdr)]
    MDR_DB -->|10. MDR created| MDR_SVC

    MDR_SVC -->|11. Link to heat numbers| HEAT_DB[(heat)]
    MDR_SVC -->|12. Link to work orders| WO_DB[(work_order)]

    MDR_SVC -->|13. MDR uploaded| QA[Quality Reviewer]
    QA -->|14. Approve/Reject| MDR_SVC

    MDR_SVC -->|15. Update status| MDR_DB

    style MDR_SVC fill:#4CAF50,color:#fff
    style OCR fill:#2196F3,color:#fff
    style PARSER fill:#FF9800,color:#fff
```

*Figure 7: MDR Upload & Processing - Level 1 DFD*

---

## 7.8 Commercial Operations - Data Flow Diagram Level 0

```mermaid
graph LR
    PM[Project Manager] -->|contract terms| CONTRACT[Contract<br/>Management]
    CONTRACT -->|contract data| CONTRACT_DB[(contract)]

    CONTRACT -->|pricing| INVOICE_PROCESS[Invoice<br/>Generation]

    SOW_DB[(sow)] -->|delivered quantities| INVOICE_PROCESS
    TRANSFER_DB[(transfer)] -->|shipment dates| INVOICE_PROCESS

    INVOICE_PROCESS -->|invoices| INVOICE_DB[(invoice)]
    INVOICE_PROCESS -->|invoice PDF| PM

    PM -->|variation request| VARIATION[Variation Order<br/>Management]
    VARIATION -->|variation orders| VAR_DB[(variation)]

    VARIATION -->|update contract| CONTRACT

    D365[D365 ERP] -->|sync financial data| SYNC[D365 Sync<br/>Service]
    SYNC -->|forecast data| FORECAST_DB[(forecast_d365)]

    style CONTRACT fill:#4CAF50,color:#fff
    style INVOICE_PROCESS fill:#2196F3,color:#fff
    style VARIATION fill:#FF9800,color:#fff
    style SYNC fill:#9C27B0,color:#fff
```

*Figure 8: Commercial Operations - Level 0 DFD*

---

## 7.9 Authentication & Authorization Flow

```mermaid
graph TB
    USER[User] -->|1. Email + Password| LOGIN[Login API]

    LOGIN -->|2. Validate credentials| USER_DB[(users)]
    USER_DB -->|3. User found| LOGIN

    LOGIN -->|4. Check password hash| BCRYPT[Bcrypt Verify]
    BCRYPT -->|5. Password valid| LOGIN

    LOGIN -->|6. Generate tokens| PASSPORT[Laravel Passport]
    PASSPORT -->|7. Create access token| TOKEN_DB[(oauth_access_tokens)]
    PASSPORT -->|8. Create refresh token| REFRESH_DB[(oauth_refresh_tokens)]

    TOKEN_DB -->|9. Access token| PASSPORT
    PASSPORT -->|10. JWT tokens| LOGIN
    LOGIN -->|11. Auth response<br/>{access_token, refresh_token}| USER

    USER -->|12. API request<br/>+ Bearer token| API[Protected API]
    API -->|13. Verify token| PASSPORT
    PASSPORT -->|14. Query token| TOKEN_DB
    TOKEN_DB -->|15. Token valid + user_id| PASSPORT

    PASSPORT -->|16. Load user| USER_DB
    USER_DB -->|17. User data + role| PASSPORT

    PASSPORT -->|18. Check privilege| PRIV[Privilege<br/>Middleware]
    PRIV -->|19. Authorized| API
    API -->|20. Response data| USER

    style LOGIN fill:#4CAF50,color:#fff
    style PASSPORT fill:#2196F3,color:#fff
    style PRIV fill:#FF9800,color:#fff
```

*Figure 9: Authentication & Authorization - Level 1 DFD*

---

## 7.10 Multi-Tenancy Database Routing

```mermaid
graph TB
    USER[User] -->|1. API request<br/>+ Database-Access header| API[API Endpoint]

    API -->|2. Extract db_name| MIDDLEWARE[Project Session<br/>Middleware]

    MIDDLEWARE -->|3. Validate user<br/>has project access| USER_DB[(users<br/>allowed_projects)]

    USER_DB -->|4. Access granted| MIDDLEWARE

    MIDDLEWARE -->|5. Switch MongoDB<br/>connection| MONGO[MongoDB<br/>Connection Manager]

    MONGO -->|6. Connect to<br/>mongodb_project_X| PROJECT_DB[(Project Database<br/>mongodb_project_X)]

    PROJECT_DB -->|7. Connection ready| MONGO
    MONGO -->|8. Connection context| API

    API -->|9. Execute query<br/>on project DB| PROJECT_DB
    PROJECT_DB -->|10. Query results| API

    API -->|11. Response data| USER

    style MIDDLEWARE fill:#4CAF50,color:#fff
    style MONGO fill:#2196F3,color:#fff
    style PROJECT_DB fill:#FF9800,color:#fff
```

*Figure 10: Multi-Tenancy Database Routing - Level 1 DFD*

---

## Notes

- All data flows include implicit error handling and validation not shown in diagrams for clarity
- Database transactions ensure ACID compliance for critical operations (SOW creation, invoice generation, etc.)
- Caching layers (Redis/Laravel Cache) are used for frequently accessed reference data (not shown)
- File uploads go through queue processing for large files (MDR PDFs, inspection photos)
- Progress calculations run on-demand and are cached with invalidation triggers
