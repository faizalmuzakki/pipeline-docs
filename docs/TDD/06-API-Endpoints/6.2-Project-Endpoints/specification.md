# Project Specification Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get list of specifications | /api/v3/project/specs | GET |
| Get specifications for datatables | /api/v3/project/specs/datatables | GET |
| Filter specifications by type | /api/v3/project/specs/filter/{type} | GET |
| Filter coating specifications | /api/v3/project/specs/filter-coating/{type}/{spec_type} | GET |
| Get all project specifications by item type | /api/v3/project/spec/all-project/{item_type} | GET |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create new specification | /api/v3/project/spec | POST |
| Check duplicate specification | /api/v3/project/duplicate_spec | POST |
| Upload specification attachments | /api/v3/project/spec/upload_attachments | POST |
| Check inspection data | /api/v3/project/spec/inspection_data/{id} | POST |
| Delete specification | /api/v3/project/spec/{id} | DELETE |

## Specification Detail

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get specific specification | /api/v3/project/spec/{id} | GET |
| Get inspection types for specification | /api/v3/project/spec/inspection_types/{id} | GET |
| Download specification attachment | /api/v3/project/spec/download_attachment | POST |
