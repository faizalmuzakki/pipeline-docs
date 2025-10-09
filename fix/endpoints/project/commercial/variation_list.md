# Project Variation List Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get variations for datatables | /api/v3/project/variation/datatables | GET |
| Download variation file | /api/v3/project/variation/download_file/{id} | GET |
| Copy variation (direct) | /api/v3/project/variation/direct_copy/{id} | POST |
| Delete variation | /api/v3/project/variation/{id} | DELETE |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create new variation | /api/v3/project/variation | POST |
| Update variation | /api/v3/project/variation/{id} | POST |

## Variation Detail

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get variation detail | /api/v3/project/variation/{id} | GET |