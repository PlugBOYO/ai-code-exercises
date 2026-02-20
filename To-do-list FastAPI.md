Here is a **formal assignment submission write-up** including database documentation, structured professionally for academic submission.

-

# 1. Introduction

This assignment involved developing a RESTful To-Do List Management API using the Python framework **FastAPI**. The system allows users to create, view, update, and delete to-do items while ensuring proper validation, filtering, and documentation.

The project demonstrates:

* CRUD operations
* Input validation using Pydantic
* Error handling with appropriate HTTP status codes
* Filtering capabilities
* Automatic API documentation
* Database integration using SQLite and SQLAlchemy

---

# 2. Technologies Used

* **Python 3.x**
* **FastAPI**
* **Uvicorn (ASGI Server)**
* **Pydantic (Data validation)**
* **SQLAlchemy (ORM)**
* **SQLite (Database)**

---

# 3. System Architecture Overview

The application follows a layered architecture:

```
Client (Swagger UI / Postman)
        ↓
FastAPI Application (Routes & Validation)
        ↓
SQLAlchemy ORM
        ↓
SQLite Database
```

---

# 4. Functional Requirements Implementation

## 4.1 Create a To-Do Item

**Endpoint:** `POST /todos`

Allows users to create a new to-do item with:

* Title
* Description
* Due Date
* Default status (pending)

Validation rules:

* Title: 1–100 characters
* Description: Required
* Due Date: Valid date format

Returns HTTP 201 upon successful creation.

---

## 4.2 List To-Do Items

**Endpoint:** `GET /todos`

Returns all to-do items.

Optional filtering:

```
/todos?status=pending
/todos?status=completed
```

---

## 4.3 Mark To-Do as Completed

**Endpoint:** `PATCH /todos/{todo_id}/complete`

* Updates task status to completed
* Returns 404 if not found
* Returns 400 if already completed

---

## 4.4 Delete To-Do Item

**Endpoint:** `DELETE /todos/{todo_id}`

* Deletes a task by ID
* Returns 204 on success
* Returns 404 if task does not exist

---

# 5. API Documentation

The API uses FastAPI’s built-in OpenAPI integration to automatically generate documentation.

### Available Documentation Interfaces:

| Documentation Type | URL             |
| ------------------ | --------------- |
| Swagger UI         | `/docs`         |
| ReDoc              | `/redoc`        |
| OpenAPI JSON       | `/openapi.json` |

The documentation automatically includes:

* Endpoint summaries and descriptions
* Request body schemas
* Response models
* Validation rules
* Query parameters
* Enum values
* Error responses
* Interactive testing capability

This improves usability and developer experience.

---

# 6. Database Integration Documentation

## 6.1 Database Choice

SQLite was selected because:

* Lightweight
* No server configuration required
* Suitable for small-scale applications
* Easy integration with FastAPI

---

## 6.2 ORM: SQLAlchemy

SQLAlchemy was used to:

* Map Python classes to database tables
* Handle CRUD operations
* Abstract raw SQL queries
* Improve maintainability

---

## 6.3 Database Schema Design

### Table: `todos`

| Column Name | Data Type     | Constraints         |
| ----------- | ------------- | ------------------- |
| id          | UUID / String | Primary Key         |
| title       | String        | Not Null            |
| description | Text          | Not Null            |
| due_date    | Date          | Not Null            |
| status      | String        | Default = "pending" |

---

## 6.4 SQLAlchemy Model Example

```python
from sqlalchemy import Column, String, Date
from sqlalchemy.ext.declarative import declarative_base
import uuid

Base = declarative_base()

class TodoModel(Base):
    __tablename__ = "todos"

    id = Column(String, primary_key=True, default=lambda: str(uuid.uuid4()))
    title = Column(String(100), nullable=False)
    description = Column(String, nullable=False)
    due_date = Column(Date, nullable=False)
    status = Column(String, default="pending")
```

---

## 6.5 Database Configuration

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "sqlite:///./todos.db"

engine = create_engine(
    DATABASE_URL, connect_args={"check_same_thread": False}
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
```

---

## 6.6 Database Initialization

```python
Base.metadata.create_all(bind=engine)
```

This automatically creates the `todos` table if it does not exist.

---

## 6.7 Database Session Dependency

FastAPI dependency injection is used to manage sessions:

```python
from fastapi import Depends
from sqlalchemy.orm import Session

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

This ensures:

* Proper session management
* Automatic closing of connections
* Clean transaction handling

---

# 7. Validation and Error Handling

The application implements:

* Pydantic field validation
* Enum validation for status
* HTTPException handling
* Appropriate HTTP status codes
* Input type validation (UUID, date)

Example:

```python
raise HTTPException(status_code=404, detail="Todo not found")
```

---

# 8. Testing

The API was tested using:

* Swagger UI interactive testing
* Manual endpoint testing
* Validation error checks
* Filtering verification

---

# 9. Security Considerations

Although authentication was not required for this assignment, the following improvements are recommended:

* JWT Authentication
* Role-based access control
* Input sanitization
* Rate limiting

---

# 10. Challenges Encountered

* Implementing filtering with enums
* Managing database sessions correctly
* Ensuring proper status codes
* Structuring models separately from database schemas

---

# 11. Learning Outcomes

Through this project, the following skills were developed:

* REST API design principles
* FastAPI framework usage
* Pydantic validation
* SQLAlchemy ORM integration
* Database schema design
* API documentation using OpenAPI
* Error handling best practices

---

