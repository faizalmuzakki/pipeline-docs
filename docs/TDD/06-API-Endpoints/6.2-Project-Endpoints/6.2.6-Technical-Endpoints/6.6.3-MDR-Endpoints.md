# Project MDR (Material Data Record) Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get MDR list | /api/v3/project/mdr/datatables | GET |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create MDR | /api/v3/project/mdr | POST |
| Update MDR | /api/v3/project/mdr/{id} | POST |
| Delete MDR | /api/v3/project/mdr/{id} | DELETE |
| Copy MDR | /api/v3/project/mdr/copy/{id} | POST |

## MDR Detail & Edit

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get MDR details | /api/v3/project/mdr/{id} | GET |
| Get MDR for edit | /api/v3/project/mdr/{id}/edit | GET |
| Get MDR edit data | /api/v3/project/mdr_edit/{id} | GET |

## Section Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Submit MDR section | /api/v3/project/mdr_section | POST |
| Submit section document | /api/v3/project/mdr_section_document | POST |

## Document Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Download MDR document | /api/v3/project/mdr/download_file/{id}/{section_index}/{sub_section_index} | GET |

## Options & Selectors

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get SOW options | /api/v3/project/sow_options | GET |
| Get no duplicate SOW options | /api/v3/project/no_duplicate_sow_options | GET |
| Get lot options | /api/v3/project/wo/lots/filter/{id}/show | GET |
