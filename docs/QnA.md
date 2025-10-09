# Technical Design Document - Questions & Answers

This document contains all questions that need to be answered before expanding the Technical Design Document to match the reference detail level.

---

## SECTION 1: INTRODUCTION

### 1.1 Project Scope & Objectives
**Q1.1.1:** What are the specific business objectives this system aims to achieve?
**A1.1.1:**

**Q1.1.2:** Who are the primary end users? (MI team, vendors, customers, QA inspectors, etc.)
**A1.1.2:**

**Q1.1.3:** What is the expected user base size? (concurrent users, total users)
**A1.1.3:**

**Q1.1.4:** Are there any specific compliance requirements? (ISO standards, industry regulations)
**A1.1.4:**

**Q1.1.5:** What is the expected project lifespan/longevity?
**A1.1.5:**

---

## SECTION 2: SYSTEM CONFIGURATION

### 2.1 Server Configuration

**Q2.1.1:** Production Environment Details:
- Cloud Provider? (AWS/Azure/GCP/On-premise)
- Server Specifications (CPU cores, RAM, Disk type & size)?
- Operating System & Version?
- Geographic location/region?
**A2.1.1:**

**Q2.1.2:** Staging Environment Details:
- Same questions as production
**A2.1.2:**

**Q2.1.3:** Load Balancing:
- Is load balancing configured?
- What type? (Application/Network)
- How many instances?
**A2.1.3:**

**Q2.1.4:** Web Server Details:
- Apache version?
- Any modules enabled? (mod_rewrite, mod_ssl, etc.)
- PHP-FPM configuration?
**A2.1.4:**

**Q2.1.5:** SSL/TLS Configuration:
- Certificate provider?
- SSL termination point?
- HTTPS enforcement?
**A2.1.5:**

### 2.2 Database Configuration

**Q2.2.1:** MongoDB Production Setup:
- MongoDB version (exact)?
- Deployment type (Standalone/Replica Set/Sharded Cluster)?
- Number of nodes?
- Backup strategy & frequency?
**A2.2.1:**

**Q2.2.2:** Database Naming Convention:
- How are project databases named?
- Pattern for `db_name` field?
- Example of actual production database names?
**A2.2.2:**

**Q2.2.3:** Data Migration Strategy:
- How are schema changes managed?
- Migration rollback process?
- Data seeding strategy?
**A2.2.3:**

**Q2.2.4:** Database Performance:
- Current collection sizes (largest collections)?
- Query performance benchmarks?
- Index strategy beyond defaults?
**A2.2.4:**

**Q2.2.5:** MySQL Usage:
- Is MySQL only for migrations, or other uses?
- MySQL version?
- Any other tables besides migrations?
**A2.2.5:**

### 2.3 Authentication and Security

**Q2.3.1:** OAuth Token Configuration:
- Access token lifetime?
- Refresh token lifetime?
- Token storage encryption?
**A2.3.1:**

**Q2.3.2:** Password Policy:
- Minimum length?
- Complexity requirements?
- Password expiration policy?
- Account lockout policy (after N failed attempts)?
**A2.3.2:**

**Q2.3.3:** API Security:
- Rate limiting configured?
- CORS allowed origins (specific domains or wildcard)?
- API key usage for external integrations?
**A2.3.3:**

**Q2.3.4:** Session Management:
- Concurrent session policy (allow multiple devices)?
- Session invalidation on password change?
- Remember me functionality?
**A2.3.4:**

**Q2.3.5:** Two-Factor Authentication:
- Is 2FA implemented or planned?
- If yes, what method? (TOTP, SMS, Email)
**A2.3.5:**

### 2.4 Logging and Monitoring

**Q2.4.1:** Log Retention:
- How long are logs retained?
- Log rotation policy?
- Log storage location?
**A2.4.1:**

**Q2.4.2:** Monitoring Tools:
- Is Sentry fully configured in production?
- Other monitoring tools? (New Relic, DataDog, etc.)
- Uptime monitoring?
**A2.4.2:**

**Q2.4.3:** Alert Configuration:
- Who receives alerts?
- Alert channels (Email/Slack/SMS)?
- Alert threshold configurations?
**A2.4.3:**

**Q2.4.4:** Performance Metrics:
- What metrics are tracked?
- Dashboard tools used?
- Response time SLAs?
**A2.4.4:**

### 2.5 File Storage

**Q2.5.1:** File Storage Strategy:
- Current production storage (Local/S3/Azure)?
- Storage capacity limits?
- File size limits per upload?
**A2.5.1:**

**Q2.5.2:** File Management:
- File retention policy?
- Automatic cleanup of old files?
- CDN usage for file delivery?
**A2.5.2:**

**Q2.5.3:** Backup Strategy:
- File backup frequency?
- Backup storage location?
- Disaster recovery plan?
**A2.5.3:**

### 2.6 API Configuration

**Q2.6.1:** API Versioning Strategy:
- Why v3 is current? What happened to v1, v2?
- Version deprecation policy?
- How are breaking changes handled?
**A2.6.1:**

**Q2.6.2:** API Documentation:
- Is ApiDoc.js actively maintained?
- Public API documentation URL?
- API changelog maintained?
**A2.6.2:**

**Q2.6.3:** Third-Party API Integrations:
- What external APIs are integrated? (Payment gateways, shipping, etc.)
- API keys management?
- Webhook configurations?
**A2.6.3:**

---

## SECTION 3: CODING STANDARDS

### 3.1 Code Quality Tools

**Q3.1.1:** PHPStan Configuration:
- Current level configured?
- Custom rules defined?
- Baseline file used?
**A3.1.1:**

**Q3.1.2:** Git Hooks (Husky):
- What checks run on pre-commit?
- What checks run on pre-push?
- Can developers skip hooks?
**A3.1.2:**

**Q3.1.3:** Code Review Process:
- Required number of reviewers?
- Automated checks on PR?
- Code review checklist?
**A3.1.3:**

### 3.2 Development Workflow

**Q3.2.1:** Git Branching Strategy:
- Branch naming convention?
- Main branches? (main, develop, staging?)
- Feature branch workflow?
- Hotfix process?
**A3.2.1:**

**Q3.2.2:** Commit Message Convention:
- Format required? (Conventional Commits?)
- Example of good commit message?
**A3.2.2:**

**Q3.2.3:** Pull Request Process:
- PR template used?
- Required PR checks?
- Merge strategy (squash/rebase/merge)?
**A3.2.3:**

### 3.3 Testing Standards

**Q3.3.1:** Testing Coverage:
- Current test coverage %?
- Target test coverage?
- What types of tests? (Unit/Feature/E2E)
**A3.3.1:**

**Q3.3.2:** Testing Tools:
- PHPUnit version and configuration?
- Frontend testing tools? (Jest/Vitest?)
- E2E testing? (Cypress/Playwright?)
**A3.3.2:**

**Q3.3.3:** Testing Requirements:
- Tests required for all new features?
- Test data management strategy?
- Testing environment setup?
**A3.3.3:**

### 3.4 Documentation Standards

**Q3.4.1:** Code Comments:
- PHPDoc required for all methods?
- JSDoc for frontend?
- Inline comment standards?
**A3.4.1:**

**Q3.4.2:** API Documentation:
- ApiDoc annotations required?
- Postman collections maintained?
- API examples provided?
**A3.4.2:**

**Q3.4.3:** Database Documentation:
- Schema documentation process?
- Field-level descriptions required?
- Relationship documentation?
**A3.4.3:**

### 3.5 Security Practices

**Q3.5.1:** Security Scanning:
- Laravel Security Checker runs when?
- Dependency vulnerability scanning?
- OWASP compliance checks?
**A3.5.1:**

**Q3.5.2:** Secure Coding Practices:
- SQL injection prevention (how handled with MongoDB)?
- XSS prevention strategy?
- CSRF protection configured?
**A3.5.2:**

**Q3.5.3:** Sensitive Data Handling:
- How are secrets managed? (.env only?)
- Encryption for sensitive fields?
- PII handling policy?
**A3.5.3:**

### 3.6 Performance Standards

**Q3.6.1:** Performance Guidelines:
- Maximum acceptable response time?
- Query optimization requirements?
- Caching strategy?
**A3.6.1:**

**Q3.6.2:** Database Query Standards:
- N+1 query prevention?
- Index usage requirements?
- Aggregation query optimization?
**A3.6.2:**

**Q3.6.3:** Frontend Performance:
- Bundle size limits?
- Lazy loading strategy?
- Image optimization requirements?
**A3.6.3:**

### 3.7 Error Handling Standards

**Q3.7.1:** Exception Handling:
- Custom exception classes used?
- Exception logging requirements?
- User-friendly error messages?
**A3.7.1:**

**Q3.7.2:** Validation Standards:
- Form request validation always required?
- Custom validation rules documented?
- Validation error message format?
**A3.7.2:**

---

## SECTION 4: INTERACTION PROTOCOLS

### 4.1 Client-Server Communication

**Q4.1.1:** Request/Response Cycle:
- Standard request headers required?
- Response time targets?
- Timeout configurations?
**A4.1.1:**

**Q4.1.2:** Data Format Standards:
- Always JSON?
- Date format standard? (ISO 8601?)
- Null handling convention?
**A4.1.2:**

**Q4.1.3:** Error Response Standards:
- Standard error response structure?
- HTTP status code usage guidelines?
- Error code naming convention?
**A4.1.3:**

### 4.2 Authentication Flow

**Q4.2.1:** Login Flow Details:
- Step-by-step login process?
- Token refresh mechanism?
- Logout cleanup process?
**A4.2.1:**

**Q4.2.2:** Token Management:
- Where is token stored on client? (localStorage/sessionStorage/cookie?)
- Token renewal strategy?
- Token expiration handling?
**A4.2.2:**

### 4.3 File Upload Flow

**Q4.3.1:** File Upload Process:
- Maximum file size per upload?
- Allowed file types?
- Upload validation process?
**A4.3.1:**

**Q4.3.2:** File Processing:
- Synchronous or asynchronous processing?
- Queue system used?
- Progress tracking for large files?
**A4.3.2:**

---

## SECTION 5: COMPONENT DESIGN

### 5.1 General Architecture Questions

**Q5.1.1:** Service Layer Organization:
- Why separate Services/Projects/ and Services/Globals/?
- Service naming convention?
- Service dependency injection pattern?
**A5.1.1:**

**Q5.1.2:** Repository Pattern:
- Is repository pattern used consistently?
- BaseRepository functionality?
- When to use Repository vs direct Model access?
**A5.1.2:**

**Q5.1.3:** Event System:
- What events are implemented?
- Event listeners vs subscribers?
- Synchronous vs queued events?
**A5.1.3:**

**Q5.1.4:** Job Queue:
- What jobs are queued?
- Queue driver (MongoDB/Redis/Database)?
- Failed job handling?
**A5.1.4:**

### 5.2 Module-Specific Questions

**Q5.2.1:** SOW Module:
- What are the key workflows? (Create ’ Submit ’ Assign to WO?)
- Status transition rules?
- Validation rules for duplicate SOW?
**A5.2.1:**

**Q5.2.2:** Work Order Module:
- Vendor assignment process?
- Progress calculation logic?
- PQT approval workflow?
**A5.2.2:**

**Q5.2.3:** MDR Module:
- Section structure (how many levels deep)?
- Document approval workflow?
- Versioning strategy?
**A5.2.3:**

**Q5.2.4:** Inspection Module:
- Complete workflow from creation to completion?
- Test result upload process?
- Inspection approval chain?
**A5.2.4:**

**Q5.2.5:** Transfer Module:
- Transfer status lifecycle?
- Inventory deduction timing?
- GPS tracking implementation (if any)?
**A5.2.5:**

**Q5.2.6:** Commercial Module (Contracts/Invoices/Variations):
- Contract lifecycle states?
- Invoice generation process?
- Variation approval workflow?
**A5.2.6:**

**Q5.2.7:** Progress Tracking:
- How is overall progress calculated?
- S-curve data generation process?
- Real-time vs batch updates?
**A5.2.7:**

### 5.3 UI Component Questions

**Q5.3.1:** Vue Component Structure:
- Component naming convention?
- Props validation requirements?
- Event emission standards?
**A5.3.1:**

**Q5.3.2:** State Management:
- What data is stored in Vuex?
- Module organization in store/?
- Local state vs global state guidelines?
**A5.3.2:**

**Q5.3.3:** Form Handling:
- Form validation approach?
- Form submission pattern?
- File upload components?
**A5.3.3:**

### 5.4 Services Documentation Depth

**Q5.4.1:** For each major service (SOWService, WorkOrderService, etc.), should I document:
- Every public method? OR
- Only key workflows? OR
- Grouped by functionality?
**A5.4.1:**

**Q5.4.2:** Service Method Documentation:
- Include method signatures?
- Include parameter descriptions?
- Include return type documentation?
**A5.4.2:**

**Q5.4.3:** Service Dependencies:
- Document all injected services?
- Document service call chains?
- Document circular dependency handling?
**A5.4.3:**

---

## SECTION 6: API ENDPOINTS

### 6.1 API Documentation Scope

**Q6.1.1:** Endpoint Documentation Level:
- Document all 887 route lines? OR
- Document main endpoints only? OR
- Group related endpoints?
**A6.1.1:**

**Q6.1.2:** For each endpoint, include:
- Request body examples?
- Response body examples?
- Error response examples?
- Authentication requirements?
- Permission requirements?
**A6.1.2:**

**Q6.1.3:** API Grouping:
- Group by module (SOW, Work Order, Transfer, etc.)?
- Group by CRUD operation?
- Group by user role?
**A6.1.3:**

### 6.2 Endpoint Details

**Q6.2.1:** Request/Response Examples:
- Use real production data (sanitized)?
- Use fictional examples?
- How detailed should examples be?
**A6.2.1:**

**Q6.2.2:** Query Parameters:
- Document all optional parameters?
- Document pagination standards?
- Document filtering standards?
**A6.2.2:**

---

## SECTION 7: DATA FLOW DIAGRAMS

### 7.1 DFD Format

**Q7.1.1:** Diagram Tool:
- ASCII art (text-based)?
- Mermaid syntax (rendered)?
- External tool (Draw.io, Lucidchart)?
- PNG images?
**A7.1.1:**

**Q7.1.2:** DFD Coverage:
- Level 0 and Level 1 for every feature?
- Only complex workflows?
- How many total DFDs expected?
**A7.1.2:**

**Q7.1.3:** DFD Detail Level:
- Show individual service methods?
- Show only services?
- Show database collections accessed?
**A7.1.3:**

### 7.2 Key Workflows to Document

**Q7.2.1:** Which workflows are CRITICAL and need detailed DFD?
- User Authentication?
- SOW Creation?
- Work Order Progress Update?
- Transfer Creation?
- Inspection Workflow?
- Invoice Generation?
- Others?
**A7.2.1:**

**Q7.2.2:** Workflow Priority:
- Rank workflows by importance (1-5)?
- Which should be documented first?
**A7.2.2:**

---

## SECTION 8: DATABASE SCHEMA

### 8.1 Schema Documentation (Already Done)

**Q8.1.1:** Is the current Database-Schema.md satisfactory?
- Any collections missing?
- Any fields needing more detail?
- Relationship documentation sufficient?
**A8.1.1:**

**Q8.1.2:** Should I add to Database-Schema.md:
- Sample queries?
- Index recommendations explained?
- Migration scripts?
**A8.1.2:**

---

## SECTION 9: DEPLOYMENT & CI/CD

### 9.1 CI/CD Pipeline

**Q9.1.1:** CI/CD Tool:
- GitHub Actions?
- GitLab CI?
- Jenkins?
- Other?
**A9.1.1:**

**Q9.1.2:** Automated Tests:
- Run on every commit?
- Run on PR only?
- Test environment setup?
**A9.1.2:**

**Q9.1.3:** Deployment Process:
- Manual or automated?
- Deployment frequency?
- Rollback procedure?
**A9.1.3:**

**Q9.1.4:** Environment Promotion:
- Dev ’ Staging ’ Production flow?
- Approval required?
- Smoke testing after deployment?
**A9.1.4:**

### 9.2 Build Process

**Q9.2.1:** Build Artifacts:
- What gets built?
- Build versioning?
- Artifact storage location?
**A9.2.1:**

**Q9.2.2:** Build Optimization:
- Production build size targets?
- Build time optimization?
- Asset compression?
**A9.2.2:**

---

## SECTION 10: FILE ORGANIZATION

### 10.1 TDD File Structure

**Q10.1.1:** Single File vs Multiple Files:
- Keep all in Technical-Design-Document.md? OR
- Split into multiple files by section? OR
- Create TDD/ directory with organized structure?
**A10.1.1:**

**Q10.1.2:** If splitting, preferred structure:
```
docs/TDD/
   00-Main.md (TOC + Introduction)
   01-System-Configuration.md
   02-Coding-Standards.md
   03-Interaction-Protocols.md
   04-Architecture-Overview.md
   05-Component-Design/
      README.md
      5.1-Global-Module.md
      5.2-SOW-Module.md
      5.3-Work-Order-Module.md
      ...
   06-API-Endpoints/
      README.md
      6.1-Global-APIs.md
      ...
   07-Data-Flow-Diagrams/
      README.md
      diagrams/
      ...
   08-Database-Schema.md (link to existing)
```
OR different structure?
**A10.1.2:**

### 10.2 Diagram Storage

**Q10.2.1:** Where to store diagrams:
- In docs/diagrams/?
- In docs/TDD/diagrams/?
- Embedded as Mermaid code in markdown?
**A10.2.1:**

---

## SECTION 11: PRIORITY & TIMELINE

### 11.1 Expansion Priority

**Q11.1.1:** Which section should I expand FIRST as pilot/proof-of-concept?
- Section 3: Coding Standards (comprehensive)
- Section 4: Interaction Protocols (new section)
- Section 5: Component Design - one module (e.g., SOW)
- Section 6: API Endpoints (complete documentation)
- Section 7: Data Flow Diagrams (start with critical flows)
**A11.1.1:**

**Q11.1.2:** Priority ranking for all sections (1 = highest):
- Section 3: Coding Standards - Priority: __
- Section 4: Interaction Protocols - Priority: __
- Section 5: Component Design - Priority: __
- Section 6: API Endpoints - Priority: __
- Section 7: Data Flow Diagrams - Priority: __
**A11.1.2:**

### 11.2 Iterative Approach

**Q11.2.1:** Should I:
- Complete one section entirely before moving to next? OR
- Create outline/structure for all sections first, then fill details? OR
- Do one module end-to-end (Component Design + APIs + DFD) then next module?
**A11.2.1:**

**Q11.2.2:** Review Checkpoints:
- Review after each section? OR
- Review after each module? OR
- Review after complete draft?
**A11.2.2:**

---

## SECTION 12: BUSINESS LOGIC & WORKFLOWS

### 12.1 Core Business Rules

**Q12.1.1:** SOW Business Rules:
- Can SOW be edited after submission?
- Can SOW be deleted if work orders exist?
- Duplicate SOW detection logic?
**A12.1.1:**

**Q12.1.2:** Work Order Business Rules:
- Can one SOW have multiple work orders?
- Work order splitting rules?
- Progress rollback rules?
**A12.1.2:**

**Q12.1.3:** Transfer Business Rules:
- Inventory reservation vs deduction timing?
- Can transfer be cancelled after dispatch?
- Partial delivery handling?
**A12.1.3:**

**Q12.1.4:** Inspection Business Rules:
- Failed inspection workflow?
- Re-inspection process?
- Inspection result dispute handling?
**A12.1.4:**

### 12.2 Approval Workflows

**Q12.2.1:** What requires approval?
- SOW creation?
- Work order creation?
- Variations?
- Invoices?
- Others?
**A12.2.1:**

**Q12.2.2:** Approval Chain Details:
- Single approver or multiple levels?
- Parallel or sequential approval?
- Timeout/escalation rules?
**A12.2.2:**

---

## SECTION 13: INTEGRATION & EXTERNAL SYSTEMS

### 13.1 External Integrations

**Q13.1.1:** What external systems integrate with MCMS?
- ERP systems?
- Payment gateways?
- Shipping/logistics systems?
- Email service providers?
- SMS providers?
**A13.1.1:**

**Q13.1.2:** Integration Patterns:
- Real-time API calls?
- Batch processing?
- Webhook subscriptions?
**A13.1.2:**

### 13.2 Data Exchange

**Q13.2.1:** Import/Export Formats:
- Excel templates used?
- CSV format specifications?
- EDI support?
**A13.2.1:**

**Q13.2.2:** API Consumption:
- Which external APIs consumed?
- API authentication methods?
- Rate limiting considerations?
**A13.2.2:**

---

## SECTION 14: REPORTING & ANALYTICS

### 14.1 Reports

**Q14.1.1:** What reports are generated?
- Progress reports?
- Quality reports?
- Financial reports?
- Inventory reports?
**A14.1.1:**

**Q14.1.2:** Report Generation:
- On-demand or scheduled?
- Export formats (PDF/Excel/CSV)?
- Report data retention?
**A14.1.2:**

### 14.2 Analytics

**Q14.2.1:** Analytics Implementation:
- Google Analytics configured?
- Custom event tracking?
- User behavior tracking?
**A14.2.1:**

**Q14.2.2:** Dashboards:
- Real-time dashboards?
- Data refresh frequency?
- KPI definitions?
**A14.2.2:**

---

## SECTION 15: MOBILE & ACCESSIBILITY

### 15.1 Mobile Support

**Q15.1.1:** Mobile Strategy:
- Responsive web only?
- Dedicated mobile app?
- PWA features?
**A15.1.1:**

**Q15.1.2:** Tablet Support:
- Tablet-specific UI?
- Touch optimization?
- Offline capabilities?
**A15.1.2:**

### 15.2 Accessibility

**Q15.2.1:** Accessibility Standards:
- WCAG compliance level?
- Screen reader support?
- Keyboard navigation?
**A15.2.1:**

---

## NOTES & INSTRUCTIONS

### How to Fill This Document:
1. Answer each question under its respective **A[number]** line
2. If a question is not applicable, write "N/A" with brief reason
3. If you don't know the answer yet, write "TBD" and mark priority
4. Add additional questions if needed using the same numbering format
5. Mark critical questions with P if they block documentation

### Priority Markers:
- =4 CRITICAL - Must answer before starting
- =á HIGH - Needed for specific sections
- =â LOW - Can document later or use placeholders

### Question Status:
- [ ] Unanswered
- [x] Answered
- [~] Partially Answered
- [?] Needs Clarification

---

**Document Version:** 1.0
**Last Updated:** 2025-10-08
**Total Questions:** 150+
