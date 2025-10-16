# 2. System Configuration

## 2.1 Server Configuration

### Table 1. Hosting Specification

| Environment | Hosting Provider | Region | Processor | Server Name | RAM | Disk Size |
|------------|------------------|--------|-----------|-------------|-----|-----------|
| Production | AWS | ap-southeast-2 | 2 vCPU | linepipe-server | 4 GB | 48 GB |
| Staging | AWS | ap-southeast-2 | 2 vCPU | linepipe-staging | 4 GB | 48 GB |
| Test | AWS | ap-southeast-2 | 2 vCPU | linepipe-test | 8 GB | 154 GB |
| Devel | AWS | ap-southeast-2 | 2 vCPU | linepipe-devel | 8 GB | 154 GB |

### Table 2. Server Configuration

| Environment | Server Type | Backend Framework | Port Configuration | SSL Certificate | Environment Variable |
|------------|-------------|-------------------|-------------------|-----------------|---------------------|
| Production, Staging, Test, Devel | AWS | Laravel 11.39.1 | 80 (HTTP)<br>443 (HTTPS) | Enable | APP_ENV: production<br>PORT: -<br>DEBUG_MODE: False |

## 2.2 Database Configuration

### Table 3. Database Configuration

| Environment | Database Type | Host | Port |
|------------|---------------|------|------|
| Production | MongoDB 7.x | MongoDB Atlas (cluster1.a2zso.mongodb.net) | 27017 |
| Staging | MongoDB 7.x | localhost (self-hosted) | 27017 |
| Test | MongoDB 7.x | localhost (self-hosted) | 27017 |
| Devel | MongoDB 7.x | localhost (self-hosted) | 27017 |

In production, Pipeline uses Managed MongoDB Atlas, while for other environments (Staging, Test, Devel), self-hosted MongoDB is used within the server itself.

**Multi-Tenancy Architecture:**
- **Global Database**: `pipeline_global` - Contains shared data across all projects (19 collections)
- **Project Databases**: `{project_name}_{project_identifier}` - One database per project containing project-specific data (51+ collections per project)

## 2.3 Authentication and Security

### Table 4. Authentication and Security

| Authentication Method | Password Encryption | User Roles | Rate Limiting |
|----------------------|-------------------|------------|--------------|
| Laravel Passport (OAuth 2.0) | Bcrypt | Super Admin, Project Manager, Non MI Team, MI Team, Global Viewer, Vendor Approver, Customer | Default by Laravel |

## 2.4 Logging and Monitoring

### Table 5. Logging and Monitoring

| Logging Framework | Log Level | Monitoring Tool | Error Tracking |
|------------------|-----------|-----------------|----------------|
| Monolog by Laravel | Error | Grafana Stack | Sentry |

## 2.5 API Configuration

### Table 6. API Configuration

| Base URL | Rate Limiting | Allowed Origin | API Key Management |
|----------|---------------|----------------|-------------------|
| https://pipeline.tubestream.com/api | Default by Laravel | https://pipeline.tubestream.com | Enforce via environment variables |
