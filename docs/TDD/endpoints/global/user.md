# Global User Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get users for datatables | /api/v2/global/users/datatables | GET |
| Get user export log | /api/v2/global/user/export_log | GET |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get user role options | /api/v2/global/user/role_options | GET |
| Check duplicate user | /api/v2/global/duplicate_user | POST |
| Create new user | /api/v2/global/user/register | POST |
| Update user | /api/v2/global/user/{id} | POST |
| Delete user | /api/v2/global/user_delete/{id} | DELETE |
| Resend user invitation | /api/v2/global/user/resend_invitation | POST |
| Check user attached projects | /api/v2/global/user_projects/{id} | POST |

## User Detail/Profile

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get specific user | /api/v2/global/user/{id} | GET |
| Get user profile | /api/v2/global/user_profile/{id} | GET |
| Get user assigned project | /api/v2/global/user/{id}/assigned_project | GET |

## User Session & Navigation

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get user project info | /api/v2/global/get_user_project_info | POST |
| Get user global info | /api/v2/global/get_user_global_info | POST |
