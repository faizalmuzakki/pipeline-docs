# Project SOW (Scope of Work) Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get list of SOWs | /api/v3/project/sows | GET |
| Get inspection results | /api/v3/project/sow_inspection_result/{id} | GET |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get inspection child items | /api/v3/project/sow_inspection_child/{type?} | GET |
| Create new SOW | /api/v3/project/sow | POST |
| Update SOW | /api/v3/project/sow/{id} | POST |
| Delete SOW | /api/v3/project/sow/{id} | DELETE |
| Submit SOW | /api/v3/project/submit_sow | POST |
| Check duplicate SOW | /api/v3/project/duplicate_sow | POST |

## SOW Detail

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get specific SOW | /api/v3/project/sow/{id}/{type?} | GET |
| Create delivery lot | /api/v3/project/lot | POST |
