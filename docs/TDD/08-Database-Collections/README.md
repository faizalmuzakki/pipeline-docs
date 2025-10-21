# Section 8: Database Collections

TS Pipeline database is built using MongoDB, a NoSQL document-based database that stores data in JSON-like BSON format. Instead of traditional tables, MongoDB organizes data into collections containing flexible documents.

The system uses a **multi-tenant architecture** with two database tiers:

1. **Global Database** (`pipeline_global`) - Stores system-wide data including user accounts, project registry, reference data (mills, ports, countries), and shared configurations
2. **Project Databases** (one per project) - Each project has its own isolated database containing project-specific operational data such as SOWs, work orders, inspections, testing results, and logistics tracking

This architecture ensures data isolation between projects while maintaining centralized user management and reference data. Below is a comprehensive list of all collections in MCMS and their descriptions.