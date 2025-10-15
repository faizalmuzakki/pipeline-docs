# 4. Interaction Protocols

## 4.1 Client-Server Communication

- The frontend communicates with the backend through RESTful API calls over HTTPS.
- API requests include authentication tokens to ensure secure access.
- Responses follow a standardized JSON format for consistency.

## 4.2 API Request and Response Flow

- Clients send requests to the backend API using HTTP methods (GET, POST, PUT, DELETE).
- The server processes the request, validates data, and returns a response.
- Responses include HTTP status codes to indicate success or failure.

**Standard HTTP Status Codes:**
- `200 OK` - Successful GET request
- `201 Created` - Successful POST request (resource created)
- `204 No Content` - Successful DELETE request
- `400 Bad Request` - Invalid request format
- `401 Unauthorized` - Missing or invalid authentication token
- `403 Forbidden` - Authenticated but not authorized for this resource
- `404 Not Found` - Resource does not exist
- `422 Unprocessable Entity` - Validation errors
- `500 Internal Server Error` - Server-side error

## 4.3 Authentication Flow

- Users log in using their credentials, and the system issues a JWT token via Laravel Passport (OAuth 2.0).
- The token is included in API requests to authenticate users via the `Authorization: Bearer {token}` header.
- Token expiration and refresh mechanisms are implemented for security.

**Authentication Flow Steps:**
1. User submits credentials (email/username and password) to `/api/v3/login`
2. Server validates credentials against MongoDB `users` collection
3. Upon success, Laravel Passport generates an access token and refresh token
4. Client stores token (typically in localStorage or Vuex store)
5. All subsequent API requests include token in Authorization header
6. Middleware validates token on each request
7. Token expiration triggers refresh flow or re-authentication
8. Logout invalidates token server-side via `/api/v3/logout`
