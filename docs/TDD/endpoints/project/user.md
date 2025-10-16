# Project User Endpoints

## List/Index Page

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get users for datatables | /api/v3/project/users/datatables | GET |
| Get user role options | /api/v3/project/user/role_options | GET |

## Create & Edit Form

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Check email availability | /api/v3/project/check_email | POST |
| Check duplicate user | /api/v3/project/duplicate_user | POST |
| Create new user | /api/v3/project/user/register | POST |
| Update user | /api/v3/project/user/{id} | POST |
| Delete user | /api/v3/project/user/{id} | DELETE |
| Add user from global | /api/v3/project/add_user | POST |

## User Detail/Profile

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get specific user | /api/v3/project/user/{id} | GET |
| Get user last seen | /api/v3/project/user/{id}/lastseen | GET |
| Get user profile | /api/v3/project/user_profile/{email} | GET |
| Get user profile last seen | /api/v3/project/user_profile/{email}/lastseen | GET |
