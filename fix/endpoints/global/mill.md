# Global Mill Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get mills for datatables | /api/v2/global/mill/datatables | GET |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Check duplicate mill | /api/v2/global/duplicate_mill | POST |
| Create new mill | /api/v2/global/mill | POST |
| Update mill | /api/v2/global/mill/{id} | POST |
| Delete mill | /api/v2/global/mill/{id} | DELETE |

## Review & Approval

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get specific mill | /api/v2/global/mill/{id} | GET |
| Mill vendor approval | /api/v2/global/mill/approval | POST |
