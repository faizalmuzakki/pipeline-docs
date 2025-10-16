# Project Procedure Documentation Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get procedure documents for datatables | /api/v3/project/procedure/datatables | GET |
| Download procedure document file | /api/v3/project/procedure/download_file/{id} | GET |
| Copy procedure document | /api/v3/project/procedure/copy/{id} | POST |
| Delete procedure document | /api/v3/project/procedure/{id} | DELETE |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create new procedure document | /api/v3/project/procedure | POST |
| Update procedure document | /api/v3/project/procedure/{id} | POST |

## Procedure Document Detail

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get procedure document detail | /api/v3/project/procedure/{id} | GET |
