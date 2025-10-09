# Project NCR Documentation Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get NCR documents for datatables | /api/v3/project/ncr/datatables | GET |
| Download NCR document file | /api/v3/project/ncr/download_file/{id} | GET |
| Copy NCR document | /api/v3/project/ncr/copy/{id} | POST |
| Delete NCR document | /api/v3/project/ncr/{id} | DELETE |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create new NCR document | /api/v3/project/ncr | POST |
| Update NCR document | /api/v3/project/ncr/{id} | POST |

## NCR Document Detail

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get NCR document detail | /api/v3/project/ncr/{id} | GET |
