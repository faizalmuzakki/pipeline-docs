# Project Reporting Documentation Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get reporting documents for datatables | /api/v3/project/reporting/datatables | GET |
| Download reporting document file | /api/v3/project/reporting/download_file/{id} | GET |
| Copy reporting document | /api/v3/project/reporting/copy/{id} | POST |
| Delete reporting document | /api/v3/project/reporting/{id} | DELETE |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create new reporting document | /api/v3/project/reporting | POST |
| Update reporting document | /api/v3/project/reporting/{id} | POST |

## Reporting Document Detail

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get reporting document detail | /api/v3/project/reporting/{id} | GET |
