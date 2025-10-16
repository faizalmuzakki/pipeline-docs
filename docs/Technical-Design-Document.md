# Technical Design Document - Pipeline

## Document Information
- **Project Name**: Pipeline (Material/Manufacturing Control Management System)
- **Version**: 2.0
- **Date**: 2025-10-14
- **Application Type**: Monolithic Full-Stack Web Application
- **Status**: Production

---

## Table of Contents

1. [Introduction](TDD/01-Introduction.md)
2. [System Configuration](TDD/02-System-Configuration.md)
3. [Coding Standards](TDD/03-Coding-Standards.md)
4. [Interaction Protocols](TDD/04-Interaction-Protocols.md)
5. [Component Design](TDD/05-Component-Design/README.md)
   - 5.1 [Global Module](TDD/05-Component-Design/5.1-Global-Module.md)
   - 5.2 [SOW Module](TDD/05-Component-Design/5.2-SOW-Module.md)
   - 5.3 [Work Order Module](TDD/05-Component-Design/5.3-Work-Order-Module.md)
   - 5.4 [MDR Module](TDD/05-Component-Design/5.4-MDR-Module.md)
   - 5.5 [Inspection Module](TDD/05-Component-Design/5.5-Inspection-Module.md)
   - 5.6 [Product Trace Module](TDD/05-Component-Design/5.6-Product-Trace-Module.md)
   - 5.7 [Transfer Module](TDD/05-Component-Design/5.7-Transfer-Module.md)
   - 5.8 [Commercial Module](TDD/05-Component-Design/5.8-Commercial-Module.md)
   - 5.9 [NCR Module](TDD/05-Component-Design/5.9-NCR-Module.md)
   - 5.10 [Procedure Module](TDD/05-Component-Design/5.10-Procedure-Module.md)
   - 5.11 [Progress Module](TDD/05-Component-Design/5.11-Progress-Module.md)
   - 5.12 [Report Module](TDD/05-Component-Design/5.12-Report-Module.md)
6. [API Endpoints](TDD/06-API-Endpoints/README.md)
7. [Data Flow Diagrams](TDD/07-Data-Flow-Diagrams/README.md)
8. [Database Collections](TDD/08-Database-Collections.md)

---

## Quick Links

- [QnA Document](QnA.md) - Technical questions and answers
- [Database Schema](Database-Schema.md) - Detailed database documentation
- [TS_MITME Reference](TS_MITME/) - Reference TDD structure

---

## Document Version History

| Version | Date | Description |
|---------|------|-------------|
| 2.0 | 2025-10-14 | Restructured into modular files; expanded to match 366-page reference TDD |
| 1.0 | 2025-10-08 | Initial comprehensive documentation |
