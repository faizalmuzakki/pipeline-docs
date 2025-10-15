# Project Test Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get list of tests | /api/v3/project/tests | GET |
| Get tests for datatables | /api/v3/project/tests/datatables/{test_type}/{id_sow}/{key} | GET |
| Get detail result tabs | /api/v3/project/test/detail_result_tabs/{id_sow}/{key} | GET |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create new test | /api/v3/project/test | POST |
| Update test | /api/v3/project/test/{id} | POST |
| Delete test | /api/v3/project/test/{id}/delete | POST |

## Test Detail

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get specific test | /api/v3/project/test/{id} | GET |
