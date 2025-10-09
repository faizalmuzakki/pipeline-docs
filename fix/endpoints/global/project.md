# Global Project Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get list of projects | /api/v2/global/projects | GET |
| Close or open project | /api/v2/global/close_open_project | POST |
| Set project show type | /api/v2/global/projects/set_show_type | POST |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get project manager options | /api/v2/global/project_manager_options/{id?} | GET |
| Check duplicate project | /api/v2/global/check_duplicate_project | POST |
| Create new project | /api/v2/global/project | POST |
| Get specific project for edit | /api/v2/global/project/{id} | GET |
| Update project | /api/v2/global/project/{id} | POST |
| Delete project | /api/v2/global/project/{id}/{confirm?} | DELETE |
| Check project progress | /api/v2/global/check_project_progress/{id} | GET |

