# Project Progress Endpoints

## Overall SOW Progress

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get overall SOW data | /api/v3/project/overall_sow/{type} | GET |
| Submit overall SOW progress | /api/v3/project/overall_sow | POST |
| Get reverse progress data | /api/v3/project/reverse/progress/{id} | GET |
| Submit reverse overall SOW | /api/v3/project/reverse/overall_sow | POST |
| Get SOW progress statistics | /api/v3/project/overall_sow/progress/stats | GET |
| Get SOW chart statistics | /api/v3/project/overall_sow/chart/stats/{key} | GET |
| Update SOW forecast date | /api/v3/project/overall_sow/update_forecast_date | POST |
| Check SOW curve status | /api/v3/project/overall_sow/check_curve_status | GET |
| Download SOW report file | /api/v3/project/overall_sow/download_report_file | POST |

## Overall Work Order Progress

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get overall work orders | /api/v3/project/overall_work_order | GET |
| Submit overall work orders | /api/v3/project/overall_work_order | POST |
| Get reverse overalls | /api/v3/project/reverse/progress/{id}/overall_work_order | GET |
| Submit reverse overalls | /api/v3/project/reverse/overall_work_order | POST |
| Get progress statistics | /api/v3/project/overall_work_order/progress/stats | GET |
| Get chart statistics | /api/v3/project/overall_work_order/chart/stats/{key} | GET |
| Check receiving progress | /api/v3/project/overall_work_order/check_receiving/{id} | GET |
| Update forecast date | /api/v3/project/overall_work_order/update_forecast_date | POST |
| Check curve status | /api/v3/project/overall_work_order/check_curve_status | GET |
| Update document submission | /api/v3/project/overall_work_order/update_doc_sub | POST |
| Download report file | /api/v3/project/overall_work_order/download_report_file | POST |

## Tab & Section Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get overall SOW tabs | /api/v3/project/overall_sow/get_overall_tabs | GET |
| Rearrange SOW tabs | /api/v3/project/overall_sow/rearrange_tabs | POST |
| Get section options | /api/v3/project/overall_sow_section_options | GET |

## General Progress Data

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get overall progress data | /api/v3/project/overall/{type} | GET |
