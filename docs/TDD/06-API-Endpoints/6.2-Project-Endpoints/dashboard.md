# Project Dashboard Endpoints

## Main Dashboard Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|--------|
| Get dashboard widgets | /api/v3/project/dashboard/get_widgets | GET |
| Get overall progress statistics (circle charts) | /api/v3/project/dashboard/progress/stats | GET |
| Get overall chart statistics (S-curve charts) | /api/v3/project/dashboard/chart/stats | GET |

## Widget Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|--------|
| Get item type options for widget creation | /api/v3/project/dashboard/get_item_type_options | GET |
| Create new dashboard widget | /api/v3/project/dashboard | POST |
| Delete dashboard widget | /api/v3/project/dashboard/{id} | DELETE |
| Rearrange dashboard widgets | /api/v3/project/dashboard/rearrange | POST |

## Transfer Progress

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|--------|
| Get transfer progress percentage | /api/v3/project/transfer/overall_percentage | GET |