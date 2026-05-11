# Collective Decision-Making Platform — API Contracts

## Overview

- **Base URL:** `/api/v1`
- **Authentication:** Bearer token (JWT) via `Authorization: Bearer <token>` header, required on all endpoints except `POST /auth/register` and `POST /auth/login`
- **Content-Type:** `application/json`
- **Timestamps:** ISO 8601 format — `YYYY-MM-DDTHH:mm:ssZ`

---

## Response Conventions

### Success

```json
{
  "data": { ... }
}
```

### Error

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable description."
  }
}
```

### Common HTTP Status Codes

| Status | Meaning                                      |
|--------|----------------------------------------------|
| `200`  | OK — request succeeded                       |
| `201`  | Created — resource successfully created      |
| `204`  | No Content — resource deleted                |
| `400`  | Bad Request — validation error               |
| `401`  | Unauthorized — missing or invalid token      |
| `403`  | Forbidden — insufficient permissions         |
| `404`  | Not Found — resource does not exist          |
| `409`  | Conflict — duplicate or constraint violation |
| `422`  | Unprocessable Entity — business rule failure |

---

## Auth

### Register

```
POST /auth/register
```

**Request Body**

```json
{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "password": "securePassword123"
}
```

**Response `201`**

```json
{
  "data": {
    "id": 1,
    "name": "Jane Doe",
    "email": "jane@example.com"
  }
}
```

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `EMAIL_ALREADY_IN_USE` | `409` | An account with this email already exists |
| `INVALID_EMAIL` | `400` | Email format is invalid |
| `PASSWORD_TOO_SHORT` | `400` | Password must be at least 8 characters |

---

### Login

```
POST /auth/login
```

**Request Body**

```json
{
  "email": "jane@example.com",
  "password": "securePassword123"
}
```

**Response `200`**

```json
{
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIs...",
    "user": {
      "id": 1,
      "name": "Jane Doe",
      "email": "jane@example.com"
    }
  }
}
```

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `INVALID_CREDENTIALS` | `401` | Email or password is incorrect |

---

## Accounts

### Get Current User

```
GET /accounts/me
```

**Response `200`**

```json
{
  "data": {
    "id": 1,
    "name": "Jane Doe",
    "email": "jane@example.com"
  }
}
```

---

### Update Current User

```
PATCH /accounts/me
```

**Request Body** *(all fields optional)*

```json
{
  "name": "Jane Smith",
  "email": "janesmith@example.com",
  "password": "newSecurePassword123"
}
```

**Response `200`**

```json
{
  "data": {
    "id": 1,
    "name": "Jane Smith",
    "email": "janesmith@example.com"
  }
}
```

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `EMAIL_ALREADY_IN_USE` | `409` | Email is taken by another account |
| `PASSWORD_TOO_SHORT` | `400` | Password must be at least 8 characters |

---

### Delete Current User

```
DELETE /accounts/me
```

**Response `204`** — No body.

---

## Groups

### List My Groups

```
GET /groups
```

**Response `200`**

```json
{
  "data": [
    {
      "id": 1,
      "name": "Team Alpha",
      "memberCount": 5
    }
  ]
}
```

---

### Create Group

```
POST /groups
```

**Request Body**

```json
{
  "name": "Team Alpha"
}
```

**Response `201`**

```json
{
  "data": {
    "id": 1,
    "name": "Team Alpha",
    "memberCount": 1
  }
}
```

---

### Get Group

```
GET /groups/:groupId
```

**Response `200`**

```json
{
  "data": {
    "id": 1,
    "name": "Team Alpha",
    "members": [
      { "id": 1, "name": "Jane Doe" }
    ],
    "decisions": [
      { "id": 3, "title": "Pick sprint tool", "deadline": "2026-06-01T23:59:59Z" }
    ]
  }
}
```

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `GROUP_NOT_FOUND` | `404` | Group does not exist |
| `NOT_A_MEMBER` | `403` | Authenticated user is not a member |

---

### Update Group

```
PATCH /groups/:groupId
```

**Request Body**

```json
{
  "name": "Team Beta"
}
```

**Response `200`**

```json
{
  "data": {
    "id": 1,
    "name": "Team Beta"
  }
}
```

---

### Delete Group

```
DELETE /groups/:groupId
```

**Response `204`** — No body.

---

### Add Member to Group

```
POST /groups/:groupId/members
```

**Request Body**

```json
{
  "userId": 2
}
```

**Response `201`**

```json
{
  "data": {
    "groupId": 1,
    "userId": 2
  }
}
```

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `USER_NOT_FOUND` | `404` | Target user does not exist |
| `ALREADY_A_MEMBER` | `409` | User is already in the group |

---

### Remove Member from Group

```
DELETE /groups/:groupId/members/:userId
```

**Response `204`** — No body.

---

## Decisions

### List Decisions in a Group

```
GET /groups/:groupId/decisions
```

**Response `200`**

```json
{
  "data": [
    {
      "id": 3,
      "title": "Pick sprint tool",
      "deadline": "2026-06-01T23:59:59Z",
      "optionCount": 3,
      "voteCount": 7
    }
  ]
}
```

---

### Create Decision

```
POST /groups/:groupId/decisions
```

**Request Body**

```json
{
  "title": "Pick sprint tool",
  "deadline": "2026-06-01T23:59:59Z",
  "options": [
    { "label": "Jira" },
    { "label": "Linear" },
    { "label": "Trello" }
  ]
}
```

> At least 2 options are required.

**Response `201`**

```json
{
  "data": {
    "id": 3,
    "title": "Pick sprint tool",
    "deadline": "2026-06-01T23:59:59Z",
    "groupId": 1,
    "options": [
      { "id": 10, "label": "Jira" },
      { "id": 11, "label": "Linear" },
      { "id": 12, "label": "Trello" }
    ]
  }
}
```

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `NOT_A_MEMBER` | `403` | Authenticated user is not a group member |
| `DEADLINE_IN_THE_PAST` | `400` | Deadline must be a future date |
| `TOO_FEW_OPTIONS` | `400` | At least 2 options are required |

---

### Get Decision

```
GET /groups/:groupId/decisions/:decisionId
```

**Response `200`**

```json
{
  "data": {
    "id": 3,
    "title": "Pick sprint tool",
    "deadline": "2026-06-01T23:59:59Z",
    "groupId": 1,
    "options": [
      { "id": 10, "label": "Jira", "voteCount": 4 },
      { "id": 11, "label": "Linear", "voteCount": 2 },
      { "id": 12, "label": "Trello", "voteCount": 1 }
    ],
    "userVote": { "optionId": 10 }
  }
}
```

> `userVote` is `null` if the authenticated user has not voted yet.

---

### Delete Decision

```
DELETE /groups/:groupId/decisions/:decisionId
```

**Response `204`** — No body.

---

## Votes

### Cast a Vote

```
POST /groups/:groupId/decisions/:decisionId/votes
```

**Request Body**

```json
{
  "optionId": 10
}
```

**Response `201`**

```json
{
  "data": {
    "id": 55,
    "userId": 1,
    "optionId": 10
  }
}
```

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `DECISION_CLOSED` | `422` | Deadline has passed, voting is closed |
| `ALREADY_VOTED` | `409` | User has already voted on this decision |
| `OPTION_NOT_FOUND` | `404` | Option does not belong to this decision |
| `NOT_A_MEMBER` | `403` | Authenticated user is not a group member |

---

### Change Vote

```
PATCH /groups/:groupId/decisions/:decisionId/votes
```

**Request Body**

```json
{
  "optionId": 11
}
```

**Response `200`**

```json
{
  "data": {
    "id": 55,
    "userId": 1,
    "optionId": 11
  }
}
```

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `DECISION_CLOSED` | `422` | Deadline has passed, voting is closed |
| `NO_VOTE_FOUND` | `404` | User has not voted yet |
| `OPTION_NOT_FOUND` | `404` | Option does not belong to this decision |

---

### Remove Vote

```
DELETE /groups/:groupId/decisions/:decisionId/votes
```

**Response `204`** — No body.

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `DECISION_CLOSED` | `422` | Deadline has passed, voting is closed |
| `NO_VOTE_FOUND` | `404` | User has not voted yet |

---

## Friendships

### List My Friends

```
GET /friendships
```

**Query Params**

| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `status` | `pending` \| `accepted` \| `denied` | `accepted` | Filter by status |

**Response `200`**

```json
{
  "data": [
    {
      "id": 7,
      "user": { "id": 2, "name": "John Smith" },
      "status": "accepted"
    }
  ]
}
```

---

### Send Friend Request

```
POST /friendships
```

**Request Body**

```json
{
  "userId": 2
}
```

**Response `201`**

```json
{
  "data": {
    "id": 7,
    "user": { "id": 2, "name": "John Smith" },
    "status": "pending"
  }
}
```

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `USER_NOT_FOUND` | `404` | Target user does not exist |
| `CANNOT_ADD_SELF` | `400` | Users cannot send a friend request to themselves |
| `FRIENDSHIP_ALREADY_EXISTS` | `409` | A request between these users already exists |

---

### Respond to Friend Request

```
PATCH /friendships/:friendshipId
```

**Request Body**

```json
{
  "status": "accepted"
}
```

> Only the **recipient** of the request may respond. Valid values: `accepted`, `denied`.

**Response `200`**

```json
{
  "data": {
    "id": 7,
    "user": { "id": 1, "name": "Jane Doe" },
    "status": "accepted"
  }
}
```

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `FRIENDSHIP_NOT_FOUND` | `404` | Friendship does not exist |
| `FORBIDDEN` | `403` | Only the recipient can respond to the request |
| `INVALID_STATUS` | `400` | Status must be `accepted` or `denied` |

---

### Remove Friend / Cancel Request

```
DELETE /friendships/:friendshipId
```

**Response `204`** — No body.

**Errors**

| Code | Status | Description |
|------|--------|-------------|
| `FRIENDSHIP_NOT_FOUND` | `404` | Friendship does not exist |
| `FORBIDDEN` | `403` | Authenticated user is not part of this friendship |