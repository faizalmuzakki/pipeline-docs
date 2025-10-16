# Project Invoice Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get invoices for datatables | /api/v3/project/invoice/datatables | GET |
| Download invoice file | /api/v3/project/invoice/download_file/{id} | GET |
| Delete invoice | /api/v3/project/invoice/{id} | DELETE |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get all SOWs for selector | /api/v3/project/sows/getAllForSelector | GET |
| Get lots by SOW | /api/v3/project/sow/lots/filter/{id_sow} | GET |
| Check duplicate invoice | /api/v3/project/invoice/duplicate_invoice | POST |
| Create/Update invoice | /api/v3/project/invoice | POST |

## Invoice Detail

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get invoice detail | /api/v3/project/invoice/{id} | GET |
