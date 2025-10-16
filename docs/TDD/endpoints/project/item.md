# Project Item Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get list of items | /api/v3/project/items | GET |
| Get items for datatables | /api/v3/project/items/datatables | GET |
| Filter items by type | /api/v3/project/items/filter/{type} | GET |
| Get existing items | /api/v3/project/items/existing | GET |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create new item | /api/v3/project/item | POST |
| Delete item | /api/v3/project/item/{id} | DELETE |
| Copy item | /api/v3/project/item/{id}/copy | POST |
| Create items in bulk | /api/v3/project/item-bulk-create | POST |

## Item Detail

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get specific item | /api/v3/project/item/{id} | GET |
| Test item functionality | /api/v3/project/item_test | GET |
