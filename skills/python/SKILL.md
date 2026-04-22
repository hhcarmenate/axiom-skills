---
name: Python
description: >
  Python 3.12+ with type hints, async/await, Pydantic validation, and best practices.
  Trigger: Building Python applications with modern type safety and async patterns.
license: MIT
metadata:
  author: Henry Carmenate
  version: "1.0"
  applies-to:
    - claude-code
    - cursor
    - codex
---

## When to Use
Use this skill when:
- Building Python 3.12+ applications with type hints
- Using async/await for concurrent operations
- Validating data with Pydantic v2
- Creating CLI tools, APIs, or data pipelines
- Writing testable code with dependency injection
- Managing configuration with environment variables

## Critical Patterns

### Pattern 1: Type Hints and Pydantic Models
Always use type hints. Use Pydantic for data validation and serialization.

```python
from typing import Optional, List
from datetime import datetime
from pydantic import BaseModel, Field, EmailStr

class UserCreate(BaseModel):
    name: str = Field(..., min_length=1, max_length=255)
    email: EmailStr
    age: Optional[int] = Field(None, ge=0, le=150)

class User(UserCreate):
    id: int
    created_at: datetime

class Post(BaseModel):
    id: int
    title: str = Field(..., min_length=3, max_length=500)
    content: str
    author_id: int
    published: bool = False
    tags: List[str] = Field(default_factory=list)

# Usage
try:
    user_data = {
        'name': 'John Doe',
        'email': 'john@example.com',
        'age': 30
    }
    user = UserCreate(**user_data)
    print(user.model_dump())  # Serialize to dict
except Exception as e:
    print(f'Validation error: {e}')
```

### Pattern 2: Async/Await Pattern
Use asyncio for concurrent I/O operations instead of threading.

```python
import asyncio
from typing import List
from aiohttp import ClientSession

class APIClient:
    def __init__(self, base_url: str):
        self.base_url = base_url

    async def fetch_user(self, user_id: int) -> dict:
        async with ClientSession() as session:
            async with session.get(f'{self.base_url}/users/{user_id}') as response:
                if response.status != 200:
                    raise ValueError(f'Failed to fetch user: {response.status}')
                return await response.json()

    async def fetch_multiple_users(self, user_ids: List[int]) -> List[dict]:
        tasks = [self.fetch_user(uid) for uid in user_ids]
        return await asyncio.gather(*tasks)

async def main():
    client = APIClient('https://api.example.com')
    users = await client.fetch_multiple_users([1, 2, 3])
    for user in users:
        print(f'User: {user["name"]}')

# Run async function
asyncio.run(main())
```

### Pattern 3: Dependency Injection
Pass dependencies as function arguments instead of using globals.

```python
from dataclasses import dataclass
from typing import Protocol

class UserRepository(Protocol):
    async def get_user(self, user_id: int) -> dict: ...
    async def create_user(self, data: dict) -> dict: ...

@dataclass
class DatabaseUserRepository:
    connection_string: str

    async def get_user(self, user_id: int) -> dict:
        # Query database
        async with asyncio.timeout(5):
            # ... database query
            return {'id': user_id, 'name': 'John Doe'}

    async def create_user(self, data: dict) -> dict:
        # Insert into database
        return {'id': 1, **data}

class UserService:
    def __init__(self, repository: UserRepository):
        self.repository = repository

    async def get_user_profile(self, user_id: int) -> dict:
        user = await self.repository.get_user(user_id)
        if not user:
            raise ValueError('User not found')
        return user

# Dependency setup
async def main():
    repo = DatabaseUserRepository('postgresql://user:pass@localhost/db')
    service = UserService(repo)
    user = await service.get_user_profile(1)
    print(user)
```

### Pattern 4: Custom Exceptions
Define domain-specific exceptions to handle errors explicitly.

```python
class ApplicationError(Exception):
    """Base exception for application errors"""
    pass

class ValidationError(ApplicationError):
    """Raised when input validation fails"""
    def __init__(self, field: str, message: str):
        self.field = field
        super().__init__(f'Validation error in {field}: {message}')

class NotFoundError(ApplicationError):
    """Raised when a resource is not found"""
    def __init__(self, resource_type: str, resource_id: int):
        super().__init__(f'{resource_type} with id {resource_id} not found')

class PermissionDeniedError(ApplicationError):
    """Raised when user lacks permission"""
    pass

# Usage
def get_user_or_raise(user_id: int) -> dict:
    if not isinstance(user_id, int):
        raise ValidationError('user_id', 'Must be an integer')
    
    user = database.query(User).filter(User.id == user_id).first()
    if not user:
        raise NotFoundError('User', user_id)
    
    return user
```

### Pattern 5: Configuration Management
Use environment variables for configuration, never hardcode secrets.

```python
from typing import Optional
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    debug: bool = False
    database_url: str
    api_key: str
    api_timeout: int = 30
    log_level: str = 'INFO'
    max_connections: int = 10

    class Config:
        env_file = '.env'
        env_file_encoding = 'utf-8'

# Usage
settings = Settings()

# In .env file:
# DATABASE_URL=postgresql://user:password@localhost/mydb
# API_KEY=sk-1234567890abcdef
# DEBUG=false
# LOG_LEVEL=DEBUG
```

## Folder Structure
```
src/
├── __init__.py
├── main.py                          # Entry point
├── models/
│   ├── __init__.py
│   ├── user.py
│   └── post.py
├── services/
│   ├── __init__.py
│   ├── user_service.py
│   └── post_service.py
├── repositories/
│   ├── __init__.py
│   ├── user_repository.py
│   └── post_repository.py
├── api/
│   ├── __init__.py
│   ├── routes/
│   │   ├── users.py
│   │   └── posts.py
│   └── dependencies.py
├── config.py
├── exceptions.py
└── utils.py

tests/
├── __init__.py
├── test_user_service.py
├── test_api.py
└── fixtures/
    └── test_data.py

.env.example
pyproject.toml
README.md
```

## Key Principles
- **Type hints everywhere**: Even for simple functions. Use `from typing import` for complex types.
- **No implicit None**: Always specify Optional[T] if None is possible.
- **Pydantic for validation**: Never manually validate input; use Pydantic models.
- **Async for I/O**: Use async/await for network, database, or file I/O. Don't use threading.
- **Dependency injection**: Pass dependencies as arguments, not global variables.
- **Custom exceptions**: Raise domain-specific exceptions, not generic Exception.
- **python-dotenv for config**: Load .env files in development, use environment variables in production.
- **No print() for logging**: Use Python's logging module for all output.

## Commands & Setup
```bash
# Create virtual environment
python3.12 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install pydantic pydantic-settings aiohttp python-dotenv pytest

# Run application
python -m src.main

# Run tests
pytest --cov=src

# Type check
mypy src/

# Lint
ruff check src/
```
