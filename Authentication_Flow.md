🔐 Authentication Flow (Request → Response)


1️⃣ User Login Request

Endpoint: /api/token/ (or your login endpoint)

Method: POST

Payload (JSON):

{
  "username": "user1",
  "password": "password123"
}


2️⃣ Server Verifies Credentials

Django REST Framework checks if the username exists.

Password is hashed and compared with the stored hash in the database.

If invalid → returns 401 Unauthorized

{
  "detail": "No active account found with the given credentials"
}


3️⃣ Server Issues Tokens

If valid, server generates:

Access Token: Short-lived, used for API requests (e.g., 5-15 min)
Refresh Token: Long-lived, used to get new access tokens (e.g., 1 day)

Response Example:

{
  "access": "<access_token>",
  "refresh": "<refresh_token>"
}


4️⃣ Client Sends Authenticated Requests

For protected endpoints (create/update/delete posts/comments), the client includes the access token in the request headers:

Authorization: Bearer <access_token>

Example:
POST /api/posts/
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "title": "New Post",
  "content": "This is the content of the post."
}


5️⃣ Server Validates Token

DRF SimpleJWT middleware checks:

Is the token valid (signature, expiration)?

Does the token correspond to an active user?

If invalid → returns 401 Unauthorized:

{
  "detail": "Given token not valid for any token type"
}

If valid → user identity is retrieved and request proceeds.


6️⃣ Authorization Check

After token validation, DRF checks permissions:

Example: Updating a post → only the author can do it

If not permitted → returns 403 Forbidden:

{
  "detail": "You do not have permission to perform this action."
}


7️⃣ Response to Request

If authentication and authorization pass → action executed

Example (creating a post):

{
  "id": 1,
  "title": "New Post",
  "content": "This is the content of the post.",
  "author": "user1",
}


8️⃣ Token Refresh

When access token expires, client uses refresh token:

POST /api/token/refresh/
{
  "refresh": "<refresh_token>"
}

Server validates refresh token and issues new access token:

{
  "access": "<new_access_token>"
}
