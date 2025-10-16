# Technical Design Document - Questions & Answers

This document contains technical specification questions that need to be answered before expanding the Technical Design Document. The focus is on **technical implementation details**, not business objectives.

**Reference:** TS_MITME-TDD.pdf structure (366-page specification)
**Target:** Expand Pipeline Technical-Design-Document.md to match detail level

---

## SECTION 1: INTRODUCTION

### 1.1 System Overview

**Q1.1.1:** System Architecture Summary:
- Current architecture description accuracy?
- Technology stack complete? (Laravel 11, Vue.js 3, MongoDB 4.x, Laravel Passport)
- Is "monolithic architecture" accurate description?
- Any missing core technologies?
**A1.1.1:**

**Q1.1.2:** System Purpose (Technical):
- Primary technical functions the system provides?
- Core modules/features from technical perspective?
- Integration points with other systems?
**A1.1.2:**

**Q1.1.3:** Document Scope:
- Should TDD cover frontend (Vue.js) equally as backend (Laravel)?
- Should TDD include database query examples?
- Should TDD include deployment procedures?
**A1.1.3:**

### 1.2 Technology Stack Details

**Q1.2.1:** Backend Stack Versions:
- PHP version: 8.2.x? Exact version?
- Laravel version: 11.x? Exact version?
- Composer version requirements?
**A1.2.1:**

**Q1.2.2:** Frontend Stack Versions:
- Vue.js: 3.x? Exact version?
- Vite: 4.5.5 confirmed?
- Node.js version requirement?
- npm/pnpm/yarn?
**A1.2.2:**

**Q1.2.3:** Database Stack:
- MongoDB version: 4.x, 5.x, 6.x, or 7.x?
- MongoDB PHP extension version?
- MySQL version (for migrations only)?
**A1.2.3:**

**Q1.2.4:** Authentication & Authorization:
- Laravel Passport version?
- OAuth 2.0 grant types used?
- JWT token implementation details?
**A1.2.4:**

**Q1.2.5:** Supporting Libraries:
- HTTP client library? (Guzzle?)
- Date/Time library? (Carbon?)
- Excel library? (PhpSpreadsheet?)
- PDF generation? (TCPDF/DomPDF?)
- Queue system? (Laravel Queue with MongoDB?)
**A1.2.5:**

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
- Same as production or different specifications?
- Staging database: real data copy or fixtures?
**A2.1.2:**

**Q2.1.3:** Load Balancing:
- Is load balancing configured?
- What type? (Application/Network)
- How many server instances?
**A2.1.3:**

**Q2.1.4:** Web Server Details:
- Apache version? (e.g., Apache/2.4.x)
- Apache modules enabled? (mod_rewrite, mod_ssl, mod_headers, etc.)
- PHP-FPM configuration? (pm.max_children, pm.start_servers, etc.)
- Virtual host configuration specifics?
**A2.1.4:**

**Q2.1.5:** SSL/TLS Configuration:
- Certificate provider? (Let's Encrypt, DigiCert, etc.)
- SSL termination point? (Load balancer or web server)
- HTTPS enforcement? (All requests redirected?)
- TLS version? (1.2, 1.3?)
**A2.1.5:**

**Q2.1.6:** PHP Configuration:
- php.ini settings (memory_limit, max_execution_time, upload_max_filesize, post_max_size)?
- OPcache configuration?
- Error reporting level in production?
**A2.1.6:**

### 2.2 Database Configuration

**Q2.2.1:** MongoDB Production Setup:
- MongoDB version (exact, e.g., 4.4.x, 5.0.x, 6.0.x)?
- Deployment type (Standalone/Replica Set/Sharded Cluster)?
- Number of nodes?
- Replica set name (if applicable)?
- Read preference configuration?
**A2.2.1:**

**Q2.2.2:** MongoDB Connection:
- Connection string format?
- Connection pool settings (maxPoolSize, minPoolSize)?
- Timeout settings (connectTimeoutMS, socketTimeoutMS)?
**A2.2.2:**

**Q2.2.3:** Database Backup Strategy:
- Backup method (mongodump, snapshots, continuous backup)?
- Backup frequency?
- Backup retention policy?
- Disaster recovery RTO/RPO targets?
**A2.2.3:**

**Q2.2.4:** Database Naming Convention:
- Pattern for project databases? (e.g., `mcms_project_{project_code}`?)
- Global database name confirmed as `mongodb_global`?
- Example of 3-5 actual production database names?
**A2.2.4:**

**Q2.2.5:** Database Performance:
- Current largest collection sizes (document count & GB)?
- Average query response time benchmarks?
- Index strategy (all indexes documented in Database-Schema.md)?
- Any sharding configuration?
**A2.2.5:**

**Q2.2.6:** MySQL Usage:
- MySQL version?
- Used only for Laravel migrations table?
- Any other tables in MySQL?
**A2.2.6:**

**Q2.2.7:** Data Migration Strategy:
- How are MongoDB schema changes managed? (Migration files? Manual scripts?)
- Migration rollback process?
- Data seeding strategy for testing?
**A2.2.7:**

### 2.3 Authentication and Security

**Q2.3.1:** OAuth Token Configuration:
- Access token lifetime (hours/days)?
- Refresh token lifetime?
- Token storage location (MongoDB collection)?
- Token encryption method?
**A2.3.1:**

**Q2.3.2:** Password Policy:
- Minimum length?
- Complexity requirements (uppercase, lowercase, numbers, symbols)?
- Password expiration policy?
- Account lockout (after N failed attempts)?
- Password history (prevent reuse of last N passwords)?
**A2.3.2:**

**Q2.3.3:** API Security:
- Rate limiting enabled? (requests per minute/hour?)
- Rate limiting storage (Redis/MongoDB/Cache)?
- CORS configuration (allowed origins)?
- CORS preflight caching?
**A2.3.3:**

**Q2.3.4:** Session Management:
- Session driver (file/database/MongoDB)?
- Session lifetime?
- Concurrent session policy (allow multiple devices)?
- Session invalidation on password change?
**A2.3.4:**

**Q2.3.5:** Security Headers:
- X-Frame-Options configured?
- Content-Security-Policy configured?
- X-Content-Type-Options configured?
- Strict-Transport-Security configured?
**A2.3.5:**

**Q2.3.6:** Encryption:
- Laravel encryption cipher (AES-256-CBC)?
- Sensitive field encryption (which fields)?
- Database-level encryption?
**A2.3.6:**

### 2.4 Logging and Monitoring

**Q2.4.1:** Log Configuration:
- Log channel (single/daily/stack)?
- Log level in production (info/warning/error)?
- Log retention period (days)?
- Log rotation policy?
- Log storage location (/storage/logs/?)?
**A2.4.1:**

**Q2.4.2:** Monitoring Tools:
- Sentry DSN configured in production?
- Sentry environment tags?
- Other monitoring (New Relic, DataDog, Prometheus)?
- Uptime monitoring service?
**A2.4.2:**

**Q2.4.3:** Alert Configuration:
- Who receives alerts (emails/Slack channels)?
- Alert severity levels configured?
- Alert threshold examples (e.g., 5xx errors > 10 in 5 minutes)?
**A2.4.3:**

**Q2.4.4:** Performance Metrics:
- Response time tracking enabled?
- Database query time tracking?
- Memory usage monitoring?
- APM (Application Performance Monitoring) tool?
**A2.4.4:**

**Q2.4.5:** Error Tracking:
- Exception logging format?
- Stack trace depth?
- Context data captured with errors?
**A2.4.5:**

### 2.5 File Storage

**Q2.5.1:** File Storage Backend:
- Current production storage (Local filesystem/S3/Azure Blob/DigitalOcean Spaces)?
- Laravel filesystem disk configuration?
- Storage path structure?
**A2.5.1:**

**Q2.5.2:** File Upload Constraints:
- Maximum file size per upload (MB)?
- Allowed MIME types?
- File name sanitization rules?
- Virus scanning enabled?
**A2.5.2:**

**Q2.5.3:** File Management:
- File retention policy?
- Automatic cleanup of orphaned files?
- File versioning strategy?
**A2.5.3:**

**Q2.5.4:** File Delivery:
- CDN usage? (CloudFlare, AWS CloudFront, etc.)
- Signed URLs for private files?
- Image thumbnail generation?
- File compression strategy?
**A2.5.4:**

### 2.6 Cache Configuration

**Q2.6.1:** Cache Driver:
- Cache driver in production (redis/memcached/file/MongoDB)?
- Cache server specifications?
- Cache TTL strategy?
**A2.6.1:**

**Q2.6.2:** Cache Usage:
- What data is cached? (user sessions, query results, config, routes, views)?
- Cache invalidation strategy?
- Cache warming strategy?
**A2.6.2:**

### 2.7 Queue Configuration

**Q2.7.1:** Queue Driver:
- Queue driver (database/redis/MongoDB/sync)?
- Queue worker configuration (processes, timeout, memory)?
- Queue prioritization?
**A2.7.1:**

**Q2.7.2:** Queue Jobs:
- What tasks are queued? (emails, notifications, file processing, reports)?
- Job retry configuration (max attempts, backoff)?
- Failed job handling strategy?
**A2.7.2:**

### 2.8 API Configuration

**Q2.8.1:** API Versioning:
- Current version: v3
- What happened to v1 and v2? (deprecated, removed, still active?)
- Version deprecation policy?
- How are breaking changes communicated?
**A2.8.1:**

**Q2.8.2:** API Documentation:
- ApiDoc.js actively maintained?
- Generated docs URL (internal/public)?
- Postman collection maintained?
- OpenAPI/Swagger spec?
**A2.8.2:**

**Q2.8.3:** API Response Standards:
- Standard success response format?
- Standard error response format?
- HTTP status code conventions?
- Response time targets (p50, p95, p99)?
**A2.8.3:**

---

## SECTION 3: CODING STANDARDS

### 3.1 PHP Coding Standards

**Q3.1.1:** PSR Standards:
- PSR-12 fully enforced?
- Any exceptions/deviations from PSR-12?
- PSR-4 autoloading confirmed?
- Other PSRs followed? (PSR-3 for logging, PSR-7/PSR-15 for HTTP?)
**A3.1.1:**

**Q3.1.2:** PHPStan Configuration:
- PHPStan level (0-9)? Exact level?
- Custom rules defined in phpstan.neon?
- Baseline file used?
- PHPStan extensions installed? (phpstan-laravel, phpstan-strict-rules?)
**A3.1.2:**

**Q3.1.3:** Code Style Tools:
- PHP-CS-Fixer configured? (config file present?)
- Rules configured in .php-cs-fixer.php?
- Automated formatting on save or pre-commit?
**A3.1.3:**

**Q3.1.4:** Naming Conventions:
- Controller naming: `{Entity}Controller` or `{Action}Controller`?
- Service naming: `{Entity}Service` or different pattern?
- Repository naming convention?
- Model naming: singular or plural?
- Variable naming: camelCase confirmed?
- Constant naming: UPPER_SNAKE_CASE confirmed?
**A3.1.4:**

**Q3.1.5:** Class Structure:
- Class organization order (properties, constructor, public methods, protected methods, private methods)?
- Method ordering convention?
- Maximum methods per class guideline?
**A3.1.5:**

**Q3.1.6:** Method Guidelines:
- Maximum lines per method guideline?
- Maximum parameters per method?
- Return type declarations required?
- Nullable type usage policy?
**A3.1.6:**

**Q3.1.7:** Dependency Injection:
- Constructor injection preferred over method injection?
- Service provider registration pattern?
- Facade usage policy?
**A3.1.7:**

### 3.2 JavaScript/Vue.js Coding Standards

**Q3.2.1:** JavaScript Style:
- ESLint configuration present?
- Prettier configuration?
- Airbnb style guide or custom rules?
- Semi-colons required or omitted?
**A3.2.1:**

**Q3.2.2:** Vue.js Conventions:
- Vue 3 Composition API exclusively or Options API allowed?
- Single-file component structure order (template, script, style)?
- Component naming: PascalCase or kebab-case?
- Props definition: runtime or type-based?
**A3.2.2:**

**Q3.2.3:** Vue Component Guidelines:
- Maximum lines per component?
- Component splitting guidelines (when to split)?
- Composables naming convention?
- Component props validation requirements?
**A3.2.3:**

**Q3.2.4:** State Management:
- Vuex module structure?
- Action/Mutation naming conventions?
- When to use Vuex vs local state?
**A3.2.4:**

### 3.3 Database/MongoDB Standards

**Q3.3.1:** Query Standards:
- Raw MongoDB queries or Laravel MongoDB builder?
- Query result format: arrays or objects?
- Aggregation pipeline usage guidelines?
**A3.3.1:**

**Q3.3.2:** Collection Naming:
- snake_case confirmed (e.g., `work_orders`, `mill_test_reports`)?
- Plural or singular collection names?
- Naming prefix/suffix conventions?
**A3.3.2:**

**Q3.3.3:** Field Naming:
- snake_case confirmed (e.g., `created_by`, `updated_at`)?
- Date field naming convention (`*_date`, `*_at`, or mixed)?
- Boolean field naming (`is_*`, `has_*`, or plain?)?
**A3.3.3:**

**Q3.3.4:** Data Types:
- Date storage: MongoDB Date, ISODate, or string?
- Numeric storage: int, float, or Decimal128?
- Boolean storage: true/false or 1/0 or string?
- Null vs missing field policy?
**A3.3.4:**

### 3.4 Git Workflow Standards

**Q3.4.1:** Branching Strategy:
- Git Flow, GitHub Flow, or custom?
- Main branch name: `main`, `master`, or `pipeline-devel` (currently)?
- Protected branches?
- Branch naming format (e.g., `feature/LT-123-description`, `bugfix/*`, `hotfix/*`)?
**A3.4.1:**

**Q3.4.2:** Commit Standards:
- Conventional Commits? (feat:, fix:, chore:, etc.)
- Commit message format required?
- Link to issue tracker (e.g., `[LT-123]`)?
- Maximum commit message length?
**A3.4.2:**

**Q3.4.3:** Pull Request Process:
- PR template defined?
- Required reviewers count?
- Automated checks on PR (tests, lint, PHPStan)?
- Merge strategy (merge commit, squash, rebase)?
**A3.4.3:**

### 3.5 Testing Standards

**Q3.5.1:** Test Coverage:
- Current test coverage percentage?
- Target coverage percentage?
- Coverage tool (PHPUnit coverage report)?
**A3.5.1:**

**Q3.5.2:** Test Types:
- Unit tests required?
- Feature/integration tests required?
- E2E tests implemented?
- Browser tests (Dusk)?
**A3.5.2:**

**Q3.5.3:** Test Organization:
- Test file naming: `*Test.php` or `Test*.php`?
- Test method naming: `test_*` or `testCamelCase`?
- Test data factories used?
**A3.5.3:**

**Q3.5.4:** Test Requirements:
- Tests required for all new features?
- Minimum test coverage for new code?
- Test database: separate MongoDB or in-memory?
**A3.5.4:**

### 3.6 Documentation Standards

**Q3.6.1:** Code Documentation:
- PHPDoc required for all classes?
- PHPDoc required for all public methods?
- PHPDoc for private methods?
- Parameter and return type descriptions required?
**A3.6.1:**

**Q3.6.2:** Inline Comments:
- When are inline comments required?
- Comment style (//  or /* */)?
- TODO/FIXME comment policy?
**A3.6.2:**

**Q3.6.3:** API Documentation:
- ApiDoc.js annotations required for all routes?
- Request/response examples required?
- Error response examples required?
**A3.6.3:**

### 3.7 Security Standards

**Q3.7.1:** Input Validation:
- Form Request validation for all user input?
- Custom validation rules documented?
- Sanitization strategy?
**A3.7.1:**

**Q3.7.2:** SQL/NoSQL Injection Prevention:
- MongoDB query injection prevention (how handled?)?
- Parameter binding required?
- Raw query usage policy?
**A3.7.2:**

**Q3.7.3:** XSS Prevention:
- Output escaping strategy?
- Blade {{ }} vs {!! !!} usage policy?
- Vue.js v-html usage policy?
**A3.7.3:**

**Q3.7.4:** CSRF Protection:
- CSRF tokens required for all forms?
- API routes excluded from CSRF?
- SPA CSRF handling (Sanctum/Passport)?
**A3.7.4:**

**Q3.7.5:** Sensitive Data:
- .env file security (never committed)?
- Secrets management in production?
- Logging sensitive data prevention?
**A3.7.5:**

### 3.8 Error Handling Standards

**Q3.8.1:** Exception Handling:
- Custom exception classes? (list main ones)
- Exception handler customization (app/Exceptions/Handler.php)?
- Exception logging strategy?
**A3.8.1:**

**Q3.8.2:** User-Facing Errors:
- Error message format for end users?
- Display stack traces in production? (should be No)
- Friendly error pages configured?
**A3.8.2:**

**Q3.8.3:** API Error Responses:
- Consistent error response structure?
- Error codes defined? (where documented?)
- HTTP status code mapping?
**A3.8.3:**

### 3.9 Performance Standards

**Q3.9.1:** Query Optimization:
- N+1 query detection tool?
- Eager loading policy?
- Aggregation pipeline optimization guidelines?
**A3.9.1:**

**Q3.9.2:** Caching Guidelines:
- When to cache query results?
- Cache key naming convention?
- Cache invalidation pattern?
**A3.9.2:**

**Q3.9.3:** Asset Optimization:
- JavaScript/CSS minification in production?
- Image optimization requirements?
- Lazy loading strategy?
**A3.9.3:**

### 3.10 Code Review Standards

**Q3.10.1:** Review Process:
- Code review checklist defined?
- Automated checks (CI/CD) before review?
- Review approval required before merge?
**A3.10.1:**

**Q3.10.2:** Review Criteria:
- Code readability requirements?
- Performance considerations?
- Security review checklist?
**A3.10.2:**

---

## SECTION 4: INTERACTION PROTOCOLS

### 4.1 Client-Server Communication

**Q4.1.1:** Request/Response Cycle:
- Standard request headers (Accept, Content-Type, Authorization)?
- Response headers (Content-Type, X-Request-ID)?
- Request ID tracking?
**A4.1.1:**

**Q4.1.2:** Data Format Standards:
- Always JSON for API responses?
- JSON encoding options (JSON_UNESCAPED_UNICODE, etc.)?
- Date/timestamp format in API (ISO 8601, Unix timestamp, custom)?
- Null handling (include null fields or omit)?
- Empty array vs null vs missing field?
**A4.1.2:**

**Q4.1.3:** Pagination Standards:
- Pagination method (offset/limit or cursor-based)?
- Pagination response format (`data`, `meta`, `links`)?
- Default page size?
- Maximum page size?
**A4.1.3:**

**Q4.1.4:** Filtering and Sorting:
- Filter query parameter format?
- Sort query parameter format?
- Search query parameter format?
**A4.1.4:**

**Q4.1.5:** Response Structure:
- Success response format (always `{"data": {...}}`)?
- Metadata included (timestamps, request_id)?
- Response wrapping strategy?
**A4.1.5:**

**Q4.1.6:** Error Response Structure:
- Error response format (message, code, errors)?
- HTTP status codes used (200, 201, 400, 401, 403, 404, 422, 500)?
- Validation error format?
**A4.1.6:**

### 4.2 Authentication Flow

**Q4.2.1:** Login Flow:
- Step 1: POST /api/v3/login with credentials?
- Step 2: Receive access_token and refresh_token?
- Step 3: Store token where? (localStorage, cookie, Vuex?)
- Step 4: Include token in subsequent requests (Authorization: Bearer)?
**A4.2.1:**

**Q4.2.2:** Token Management:
- Token storage client-side (localStorage/sessionStorage/httpOnly cookie)?
- Token sent in header or body or query param?
- Token refresh flow (automatic or manual)?
- Token expiration handling (401 response → refresh → retry)?
**A4.2.2:**

**Q4.2.3:** Logout Flow:
- Logout endpoint (/api/v3/logout)?
- Token invalidation server-side?
- Client-side cleanup (clear Vuex state, localStorage)?
**A4.2.3:**

**Q4.2.4:** Authentication Middleware:
- Middleware name (`auth:api`)?
- Token verification process?
- User loading mechanism?
**A4.2.4:**

### 4.3 Authorization Flow

**Q4.3.1:** Role-Based Access Control:
- Roles defined (admin, manager, user, vendor, etc.)?
- Role checking middleware?
- Role stored in users collection?
**A4.3.1:**

**Q4.3.2:** Permission-Based Access:
- Permissions system implemented?
- Permission checking pattern?
- Route-level or method-level permissions?
**A4.3.2:**

**Q4.3.3:** Resource Authorization:
- Policy classes used?
- Owner-based authorization (user can only edit own resources)?
- Project-based authorization (user can only access own project data)?
**A4.3.3:**

### 4.4 File Upload/Download Flow

**Q4.4.1:** File Upload:
- Upload endpoint(s)?
- Request format (multipart/form-data)?
- File validation (size, type)?
- Progress tracking?
- Synchronous or asynchronous processing?
**A4.4.1:**

**Q4.4.2:** File Download:
- Download endpoint pattern?
- File streaming or redirect to storage URL?
- Access control for file downloads?
- Signed URLs for private files?
**A4.4.2:**

**Q4.4.3:** File Processing:
- Queue used for large file processing?
- Processing status endpoint?
- Notification upon completion?
**A4.4.3:**

### 4.5 Real-Time Communication

**Q4.5.1:** WebSocket/Broadcasting:
- Laravel Broadcasting configured?
- Pusher/Soketi/Laravel Echo Server?
- Which events are broadcast?
**A4.5.1:**

**Q4.5.2:** Polling:
- Any long-polling endpoints?
- Polling intervals?
**A4.5.2:**

---

## SECTION 5: COMPONENT DESIGN

### 5.1 General Architecture Questions

**Q5.1.1:** Layered Architecture:
- Confirmed flow: Route → Middleware → Controller → Service → Repository → Model → Database?
- Any direct Controller → Model access?
- Any Service → Service calls?
**A5.1.1:**

**Q5.1.2:** Service Layer Organization:
- Services/Projects/ contains project-specific services?
- Services/Globals/ contains cross-project services?
- Service naming: `{Entity}Service` or `{Action}Service`?
- Example: SOWService, WorkOrderService, InspectionService?
**A5.1.2:**

**Q5.1.3:** Repository Pattern:
- Repository pattern used consistently?
- BaseRepository exists?
- Repository naming: `{Entity}Repository`?
- Repository vs direct Model access policy?
**A5.1.3:**

**Q5.1.4:** Middleware:
- Custom middleware list (auth, role, project access, etc.)?
- Middleware execution order?
- Middleware for project context switching?
**A5.1.4:**

**Q5.1.5:** Event/Listener System:
- Events implemented (UserCreated, SOWSubmitted, etc.)?
- Synchronous or queued listeners?
- Event naming convention?
**A5.1.5:**

**Q5.1.6:** Job Queue:
- Jobs implemented (SendEmail, GenerateReport, etc.)?
- Job naming convention?
- Queue names/channels used?
**A5.1.6:**

**Q5.1.7:** Notifications:
- Notification channels (database, email, SMS)?
- Notification classes list?
- Notification queueing policy?
**A5.1.7:**

### 5.2 Module Inventory

**Q5.2.1:** Complete Module List:
Based on routes and codebase, confirm all modules:
1. Global Module (Users, Projects, Mills, Locodes, Roles)
2. SOW Module
3. Work Order Module
4. MDR Module (Mill Test Reports)
5. Inspection Module
6. Product Trace Module
7. Transfer/Logistics Module
8. Commercial Module (Contracts, Invoices, Variations)
9. NCR Module (Non-Conformance Reports)
10. Procedure Module
11. Progress/Dashboard Module
12. Report Generation Module
- Any missing modules?
- Any deprecated modules?
**A5.2.1:**

### 5.3 Component Design Detail Level

**Q5.3.1:** For each module, document:
- User Interface forms/pages list?
- Security (middleware/auth checks)?
- Application Services (list all services)?
- Database collections used?
- Similar to TS_MITME-TDD.pdf format?
**A5.3.1:**

**Q5.3.2:** Service Documentation Detail:
For each service (e.g., SOWService), document:
- Every public method?
- Method purpose description?
- Method parameters and return types?
- Called by which controllers?
- Calls which other services/repositories?
**A5.3.2:**

**Q5.3.3:** UI Component Documentation:
For each module's UI:
- List main Vue components?
- Component hierarchy?
- Component props and events?
- Vuex modules used?
**A5.3.3:**

### 5.4 Module-Specific Business Logic Questions

**Q5.4.1:** Global Module:
- User management workflow (create, activate, deactivate)?
- Project creation workflow?
- Role assignment mechanism?
- Mill/Locode data management?
**A5.4.1:**

**Q5.4.2:** SOW Module:
- Complete SOW lifecycle (draft → submitted → assigned → completed)?
- SOW editing rules (can edit in what states)?
- SOW deletion rules?
- Duplicate detection logic?
- Bulk SOW import process?
**A5.4.2:**

**Q5.4.3:** Work Order Module:
- Work order creation (from SOW or standalone)?
- Vendor assignment process?
- Progress tracking mechanism?
- PQT (Production Quality Test) approval workflow?
- Work order completion criteria?
**A5.4.3:**

**Q5.4.4:** MDR Module:
- MDR document structure (sections/subsections depth)?
- MDR approval workflow (who approves)?
- MDR versioning strategy?
- MDR template management?
**A5.4.4:**

**Q5.4.5:** Inspection Module:
- Inspection types (visual, dimensional, NDT, etc.)?
- Inspection workflow (create → assign → perform → review → approve)?
- Test result upload process?
- Failed inspection handling (re-inspection)?
- Inspection report generation?
**A5.4.5:**

**Q5.4.6:** Product Trace Module:
- Product tracking mechanism (by heat number, serial, etc.)?
- Trace data captured at each stage?
- Trace query/search functionality?
**A5.4.6:**

**Q5.4.7:** Transfer Module:
- Transfer types (yard-to-yard, yard-to-customer, etc.)?
- Transfer status lifecycle (draft → dispatched → in-transit → received)?
- Inventory deduction timing (at dispatch or receipt)?
- GPS tracking (if implemented)?
- Discrepancy handling?
**A5.4.7:**

**Q5.4.8:** Commercial Module:
- Contract management (creation, amendment, approval)?
- Invoice generation (automatic or manual)?
- Invoice approval workflow?
- Variation order workflow?
- Payment tracking?
**A5.4.8:**

**Q5.4.9:** NCR Module:
- NCR creation process?
- NCR investigation workflow?
- NCR resolution (corrective actions)?
- NCR closure criteria?
**A5.4.9:**

**Q5.4.10:** Procedure Module:
- Procedure document management (create, review, approve)?
- Procedure versioning?
- Procedure access control?
**A5.4.10:**

**Q5.4.11:** Progress Module:
- Overall progress calculation method?
- S-curve generation algorithm?
- KPI definitions (on-time delivery, quality pass rate, etc.)?
- Dashboard data refresh frequency?
**A5.4.11:**

**Q5.4.12:** Report Generation:
- Available reports list?
- Report generation (on-demand or scheduled)?
- Report formats (PDF, Excel, CSV)?
- Report data sources?
**A5.4.12:**

---

## SECTION 6: API ENDPOINTS

### 6.1 API Documentation Scope

**Q6.1.1:** Documentation Coverage:
- Document all 887 route lines?
- OR document main/representative endpoints (~50-100 key endpoints)?
- OR group similar endpoints (e.g., CRUD for each resource)?
**A6.1.1:**

**Q6.1.2:** Endpoint Documentation Format:
For each endpoint, include:
- HTTP method and path
- Route name
- Controller method
- Middleware applied
- Request body schema
- Request example (JSON)
- Success response example
- Error response examples
- Required authentication?
- Required permissions/roles?
**A6.1.2:**

**Q6.1.3:** API Grouping Strategy:
- Group by module (Global, SOW, Work Order, etc.)?
- Group by resource type (RESTful grouping)?
- Group by user role/access level?
**A6.1.3:**

### 6.2 Endpoint Examples

**Q6.2.1:** Request/Response Examples:
- Use real production data (sanitized/anonymized)?
- Use realistic fictional examples?
- Include all response fields or summary?
**A6.2.1:**

**Q6.2.2:** Query Parameters:
- Document pagination params (`page`, `per_page`)?
- Document filter params?
- Document sort params?
- Document search params?
- Optional vs required parameters?
**A6.2.2:**

**Q6.2.3:** Validation Rules:
- Document validation rules for each endpoint?
- Document error responses for validation failures?
**A6.2.3:**

---

## SECTION 7: DATA FLOW DIAGRAMS

### 7.1 DFD Format and Tools

**Q7.1.1:** Diagram Format:
- Mermaid syntax (rendered in Markdown)?
- ASCII art diagrams?
- PlantUML?
- External tool (Draw.io, Lucidchart) → exported PNG?
- Preference?
**A7.1.1:**

**Q7.1.2:** DFD Coverage:
- Level 0 (context diagram) for entire system?
- Level 1 (detailed DFD) for each major module?
- Level 2 (sub-process) for complex workflows?
- How many total DFDs expected (rough estimate)?
**A7.1.2:**

**Q7.1.3:** DFD Detail Level:
- Show individual methods (e.g., SOWService::create())?
- Show only service/class level?
- Show database collections accessed?
- Show external systems?
**A7.1.3:**

### 7.2 Critical Workflows

**Q7.2.1:** Priority Workflows for DFD:
Rank by priority (1 = highest):
- User Authentication Flow - Priority: __
- Project Context Switching - Priority: __
- SOW Creation & Submission - Priority: __
- Work Order Creation from SOW - Priority: __
- Work Order Progress Update - Priority: __
- MDR Document Approval - Priority: __
- Inspection Workflow (Creation to Completion) - Priority: __
- Transfer Workflow (Creation to Receipt) - Priority: __
- Invoice Generation - Priority: __
- NCR Workflow - Priority: __
- Report Generation - Priority: __
- Other critical workflows? - Priority: __
**A7.2.1:**

**Q7.2.2:** Workflow Documentation Order:
- Start with highest priority workflow?
- OR start with simplest workflow as template?
**A7.2.2:**

### 7.3 Component Interaction Diagrams

**Q7.3.1:** Component Diagrams:
- Create component interaction diagrams (similar to Figure 21 in TS_MITME-TDD.pdf)?
- Show: UI → Middleware → Controller → Service → Repository → Model → Database?
- One diagram per module or per feature?
**A7.3.1:**

**Q7.3.2:** Sequence Diagrams:
- Create sequence diagrams for complex workflows?
- Show time-ordered interactions?
**A7.3.2:**

---

## SECTION 8: DATABASE SCHEMA

### 8.1 Schema Documentation Review

**Q8.1.1:** Current Database-Schema.md:
- Is the existing Database-Schema.md complete?
- Any collections missing?
- Any fields missing or incorrect?
- Relationship documentation sufficient?
**A8.1.1:**

**Q8.1.2:** Schema Enhancement:
Should I add to Database-Schema.md:
- Sample MongoDB queries (find, aggregate)?
- Index usage examples?
- Query performance considerations?
- Data migration scripts?
**A8.1.2:**

**Q8.1.3:** Schema Diagrams:
- Create ER diagrams (entity-relationship)?
- Show collection relationships visually?
- Use Mermaid ER diagram syntax?
**A8.1.3:**

---

## SECTION 9: DEPLOYMENT & CI/CD

### 9.1 CI/CD Pipeline

**Q9.1.1:** CI/CD Platform:
- GitHub Actions?
- GitLab CI?
- Jenkins?
- Other?
- Configuration file present (.github/workflows/, .gitlab-ci.yml)?
**A9.1.1:**

**Q9.1.2:** Automated Testing:
- Tests run on every commit/PR?
- Test suites run (unit, feature, integration)?
- Test coverage reporting?
- Coverage threshold enforcement?
**A9.1.2:**

**Q9.1.3:** Static Analysis:
- PHPStan runs in CI/CD?
- ESLint runs in CI/CD?
- Security scanning?
- Dependency vulnerability check?
**A9.1.3:**

**Q9.1.4:** Build Process:
- Composer install in CI?
- npm install & build in CI?
- Asset compilation (Vite build)?
- Build artifacts stored where?
**A9.1.4:**

**Q9.1.5:** Deployment Process:
- Automated deployment to staging?
- Automated deployment to production?
- Manual approval required?
- Deployment script/tool (Deployer, Envoyer, custom)?
**A9.1.5:**

**Q9.1.6:** Deployment Steps:
- Typical deployment steps (git pull, composer install, migrate, cache clear, etc.)?
- Zero-downtime deployment?
- Database migration strategy during deployment?
- Rollback procedure?
**A9.1.6:**

**Q9.1.7:** Environment Promotion:
- Environment flow (dev → staging → production)?
- Environment-specific configurations?
- Smoke tests after deployment?
**A9.1.7:**

---

## SECTION 10: FILE ORGANIZATION

### 10.1 TDD File Structure

**Q10.1.1:** Single File vs Multiple Files:
Option A: Keep everything in single Technical-Design-Document.md (will be 300+ pages)
Option B: Split into multiple files by major section
Option C: Create structured TDD/ directory with organized subdirectories

Preferred option?
**A10.1.1:**

**Q10.1.2:** If splitting (Option B or C), preferred structure:
```
docs/
  Technical-Design-Document.md (main TOC + links to sections)
  TDD/
    01-Introduction.md
    02-System-Configuration.md
    03-Coding-Standards.md
    04-Interaction-Protocols.md
    05-Architecture-Overview.md
    06-Component-Design/
      README.md (Component Design overview)
      06.1-Global-Module.md
      06.2-SOW-Module.md
      06.3-Work-Order-Module.md
      06.4-MDR-Module.md
      06.5-Inspection-Module.md
      06.6-Product-Trace-Module.md
      06.7-Transfer-Module.md
      06.8-Commercial-Module.md
      06.9-NCR-Module.md
      06.10-Procedure-Module.md
      06.11-Progress-Module.md
      06.12-Report-Module.md
    07-API-Endpoints/
      README.md (API overview)
      07.1-Global-APIs.md
      07.2-SOW-APIs.md
      07.3-Work-Order-APIs.md
      ... (one file per module)
    08-Data-Flow-Diagrams/
      README.md (DFD overview)
      diagrams/ (Mermaid files or PNG images)
      08.1-Authentication-Flow.md
      08.2-SOW-Workflow.md
      08.3-Work-Order-Workflow.md
      ... (one file per major workflow)
    09-Database-Schema.md (link to existing or move existing here)
    10-Deployment.md (CI/CD and deployment)
```

OR different structure?
**A10.1.2:**

### 10.2 Diagram Storage

**Q10.2.1:** Diagram Files:
- Store in docs/diagrams/?
- Store in docs/TDD/diagrams/?
- Embed as Mermaid code in markdown (no separate files)?
- Combination (Mermaid for simple, PNG for complex)?
**A10.2.1:**

---

## SECTION 11: PRIORITY & EXECUTION PLAN

### 11.1 Expansion Priority

**Q11.1.1:** Pilot Section:
Which section should I expand FIRST as proof-of-concept to confirm approach?
- Section 3: Coding Standards (comprehensive expansion)?
- Section 4: Interaction Protocols (new section)?
- Section 5: Component Design - ONE module (e.g., SOW Module complete)?
- Section 6: API Endpoints (one module's APIs fully documented)?
- Section 7: Data Flow Diagrams (one critical workflow)?

**A11.1.1:**

**Q11.1.2:** Overall Priority Ranking:
Rank all sections by priority (1 = highest, 8 = lowest):
- Section 2: System Configuration (expand) - Priority: __
- Section 3: Coding Standards (expand significantly) - Priority: __
- Section 4: Interaction Protocols (create new) - Priority: __
- Section 5: Component Design (expand dramatically) - Priority: __
- Section 6: API Endpoints (expand to cover all) - Priority: __
- Section 7: Data Flow Diagrams (expand significantly) - Priority: __
- Section 8: Database Schema (review/enhance existing) - Priority: __
- Section 9: Deployment & CI/CD (expand) - Priority: __
**A11.1.2:**

### 11.2 Execution Approach

**Q11.2.1:** Execution Strategy:
Option A: Complete one section entirely before moving to next
Option B: Create outline/structure for all sections first, then fill in details iteratively
Option C: Do one module end-to-end (Component Design + APIs + DFD for that module), then next module

Preferred option?
**A11.2.1:**

**Q11.2.2:** Review Checkpoints:
- Review after each section is complete?
- Review after each module is complete?
- Review after complete draft of all sections?
- Multiple review points?
**A11.2.2:**

**Q11.2.3:** Iterative Refinement:
- First pass: create structure and fill with high-level content
- Second pass: add detailed examples and diagrams
- Third pass: review and refine
OR complete each section to final quality before moving to next?
**A11.2.3:**

---

## SECTION 12: ADDITIONAL TECHNICAL DETAILS

### 12.1 External Integrations

**Q12.1.1:** External Systems:
- List all external systems integrated (ERP, payment gateway, email service, etc.)?
- Integration method for each (REST API, SOAP, FTP, etc.)?
- Authentication method for each?
**A12.1.1:**

**Q12.1.2:** Email System:
- Email driver (SMTP, Mailgun, SES, etc.)?
- Email queuing?
- Email templates (Blade, Mailable classes)?
**A12.1.2:**

**Q12.1.3:** SMS/Notifications:
- SMS provider (Twilio, Nexmo, etc.)?
- Push notifications?
**A12.1.3:**

### 12.2 Reporting

**Q12.2.1:** Report Generation:
- Report types (progress, quality, financial, inventory, etc.)?
- Report generation library (Laravel Excel, TCPDF, DomPDF)?
- On-demand or scheduled reports?
- Report storage and retention?
**A12.2.1:**

### 12.3 Import/Export

**Q12.3.1:** Data Import:
- Excel import functionality (which modules)?
- Import validation process?
- Import error handling?
**A12.3.1:**

**Q12.3.2:** Data Export:
- Excel export (which modules)?
- CSV export?
- PDF export?
**A12.3.2:**

### 12.4 Localization

**Q12.4.1:** Multi-Language Support:
- Localization implemented (Laravel localization)?
- Languages supported (English only, or others)?
- Translation file structure?
**A12.4.1:**

**Q12.4.2:** Timezone Handling:
- Timezone storage (UTC in database, convert on display)?
- User timezone preference?
**A12.4.2:**

### 12.5 Mobile and Accessibility

**Q12.5.1:** Responsive Design:
- All pages mobile-responsive?
- Specific mobile-optimized pages?
- Touch-friendly UI?
**A12.5.1:**

**Q12.5.2:** Accessibility:
- WCAG compliance level (A, AA, AAA)?
- Screen reader support?
- Keyboard navigation support?
**A12.5.2:**

---

## NOTES & INSTRUCTIONS

### How to Fill This Document:
1. Answer each question under its respective **A[number]** line
2. If a question is not applicable, write "N/A" with brief reason
3. If you don't know the answer yet, write "TBD" and mark for follow-up
4. Add additional questions if needed using the same numbering format
5. Mark critical questions that block documentation work

### Priority Markers (Optional):
- 🔴 CRITICAL - Must answer before starting expansion
- 🟡 HIGH - Needed for specific sections
- 🟢 LOW - Can document later or use placeholders

### Question Status (Optional):
- [ ] Unanswered
- [x] Answered
- [~] Partially Answered
- [?] Needs Clarification

---

**Document Version:** 2.0 (Revised to focus on technical specifications)
**Last Updated:** 2025-10-14
**Total Questions:** 200+
**Focus:** Technical implementation details, not business objectives
