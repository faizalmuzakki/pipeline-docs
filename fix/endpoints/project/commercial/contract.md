# Project Contract Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get contracts for datatables | /api/v3/project/contract/datatables | GET |
| Download contract file | /api/v3/project/contract/download_file/{id} | GET |
| Copy contract | /api/v3/project/contract/copy/{id} | POST |
| Delete contract | /api/v3/project/contract/{id} | DELETE |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create new contract | /api/v3/project/contract | POST |
| Update contract | /api/v3/project/contract/{id} | POST |

## Contract Detail

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get contract detail | /api/v3/project/contract/{id} | GET |