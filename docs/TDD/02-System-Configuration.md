# 2. System Configuration

## 2.1 Server Configuration

### Table 1. Hosting Specification

| Environment | Hosting Provider | Region | Processor | Server Name | RAM | Disk Size |
|------------|------------------|--------|-----------|-------------|-----|-----------|
| Production | AWS | TBD | TBD vCPU | TBD | TBD GB | TBD GB |
| Staging | AWS | TBD | TBD vCPU | TBD | TBD GB | TBD GB |
| Test | AWS | TBD | TBD vCPU | TBD | TBD GB | TBD GB |
| Devel | AWS | TBD | TBD vCPU | TBD | TBD GB | TBD GB |

### Table 2. Server Configuration

| Environment | Server Type | Backend Framework | Port Configuration | SSL Certificate | Environment Variable |
|------------|-------------|-------------------|-------------------|-----------------|---------------------|
| Production, Staging, Test, Devel | Nginx 1.24.0 | Laravel 11.39.1 | 80 (HTTP)<br>443 (HTTPS) | Enable | APP_ENV: production<br>PORT: -<br>DEBUG_MODE: False |

## 2.2 Database Configuration

### Table 3. Database Configuration

| Environment | Database Type | Host | Port |
|------------|---------------|------|------|
| Production | MongoDB 7.x | MongoDB Atlas (cluster TBD) | 27017 |
| Staging | MongoDB 7.x | localhost (self-hosted) | 27017 |
| Test | MongoDB 7.x | localhost (self-hosted) | 27017 |
| Devel | MongoDB 7.x | localhost (self-hosted) | 27017 |

In production, MCMS Linepipe uses Managed MongoDB Atlas, while for other environments (Staging, Test, Devel), self-hosted MongoDB is used within the server itself.

**Multi-Tenancy Architecture:**
- **Global Database**: `mongodb_global` - Contains shared data across all projects (19 collections)
- **Project Databases**: `mongodb_project_{project_code}` - One database per project containing project-specific data (85+ collections per project)

## 2.3 Authentication and Security

### Table 4. Authentication and Security

| Authentication Method | Password Encryption | User Roles | Rate Limiting |
|----------------------|-------------------|------------|--------------|
| Laravel Passport (OAuth 2.0) | Bcrypt | super_admin, project_manager, non_mi_team, mi_team, global_viewer, vendor_approver, customer, project_team, project_team_bu, project_team_misi, project_team_non_mi | Default by Laravel |

## 2.4 Logging and Monitoring

### Table 5. Logging and Monitoring

| Logging Framework | Log Level | Monitoring Tool | Error Tracking |
|------------------|-----------|-----------------|----------------|
| Monolog by Laravel | Error | Tick stack | Sentry |

## 2.5 API Configuration

### Table 6. API Configuration

| Base URL | Rate Limiting | Allowed Origin | API Key Management |
|----------|---------------|----------------|-------------------|
| https://mcms-lp.example.com/api | Default by Laravel | https://mcms-lp.example.com | Enforce via environment variables |

**API Versioning:**
- Current version: **v3** (`/api/v3`)
- Legacy versions (v1, v2) are deprecated
- Breaking changes are communicated through API documentation updates
