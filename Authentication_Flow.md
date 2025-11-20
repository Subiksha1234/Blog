# 🔐 Authentication Flow (Request → Response)

1️⃣ **User Login Request**  
**Endpoint:** `/api/token/`  
**Method:** `POST`  
**Payload:**
```json
{
  "username": "user1",
  "password": "password123"
}

2️⃣ Server Verifies Credentials

DRF checks if the username exists.

Password is hashed and compared with stored hash.

If invalid → returns 401 Unauthorized

{
  "detail": "No active account found with the given credentials"
}

3️⃣ Server Issues Tokens

If credentials are valid, server returns:

Access Token (short-lived)

Refresh Token (long-lived)
Response:
{
  "access": "<access_token>",
  "refresh": "<refresh_token>"
}

4️⃣ Client Sends Authenticated Requests
Client includes token in request headers:
Authorization: Bearer <access_token>
Example Request:
POST /api/posts/
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "title": "New Post",
  "content": "This is the content of the post."
}

5️⃣ Server Validates Token
SimpleJWT checks:

Token signature

Expiry time

User active status
If invalid → 401 Unauthorized
{
  "detail": "Given token not valid for any token type"
}
If valid → request continues.

6️⃣ Authorization Check
DRF verifies permissions.
Example: Only the post author can update/delete.
If unauthorized → 403 Forbidden

{
  "detail": "You do not have permission to perform this action."
}

7️⃣ Response to Request
If authentication + authorization pass:
{
  "id": 1,
  "title": "New Post",
  "content": "This is the content of the post.",
  "author": "user1"
}

8️⃣ Token Refresh
When access token expires:
Request:
POST /api/token/refresh/
{
  "refresh": "<refresh_token>"
}
Response:
{
  "access": "<new_access_token>"
}


