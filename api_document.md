# Universal API Document

## Overview

Since all three backends serve the same purpose, they must adhere to the exact same API contract. This document standardizes the interface using a "Task" resource.

---

## Base URL

```
http://localhost/api/{language}/v1
```

Where `{language}` is one of: `java`, `node`, or `python`

---

## API Endpoints

### POST /tasks
Create a new task.

**Request Body:**
```json
{
  "title": "string",
  "description": "string"
}
```

**Success Response (201):**
```json
{
  "id": "uuid",
  "title": "string",
  "status": "PENDING"
}
```

---

### GET /tasks/{id}
Retrieve a specific task by ID. Checks Redis cache first.

**Request Body:** None

**Success Response (200):**
```json
{
  "id": "uuid",
  "title": "string",
  "status": "string"
}
```

---

### GET /tasks
Retrieve all tasks. (Optionally add pagination).

**Request Body:** None

**Success Response (200):**
```json
[
  {
    "id": "uuid",
    "title": "string",
    "status": "string"
  }
]
```

---

### PUT /tasks/{id}
Update task status (e.g., to "COMPLETED").

**Request Body:**
```json
{
  "status": "string"
}
```

**Success Response (200):**
```json
{
  "id": "uuid",
  "title": "string",
  "status": "string"
}
```

---

### DELETE /tasks/{id}
Delete a task. Invalidate Redis cache for this ID.

**Request Body:** None

**Success Response:** 204 No Content

---

## Endpoint Summary

| Method | Endpoint | Description | Cache Behavior |
|--------|----------|-------------|-----------------|
| POST | `/tasks` | Create a new task | Write-through |
| GET | `/tasks/{id}` | Retrieve specific task | Read-through (Redis) |
| GET | `/tasks` | Retrieve all tasks | Read-through (Redis) |
| PUT | `/tasks/{id}` | Update task status | Invalidate & update |
| DELETE | `/tasks/{id}` | Delete a task | Invalidate cache |