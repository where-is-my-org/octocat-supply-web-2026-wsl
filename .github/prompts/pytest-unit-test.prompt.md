---
agent: 'agent'
description: 'Generate a suite of unit tests for FastAPI backend API endpoints using pytest. Follow project conventions, ensure test isolation, and achieve high coverage.'
---

## Project Context

### Technology Stack
- **Framework**: FastAPI
- **Testing Framework**: pytest
- **Test Client**: FastAPI TestClient
- **Coverage Tool**: pytest-cov
- **Data Validation**: Pydantic v2
- **Python Version**: 3.8+

### Project Structure
```
package.json                 # Node.js project configuration 
api/
├── app/
│   ├── main.py              # FastAPI application entry point
│   ├── seed_data.py         # Seed data for testing
│   ├── models/              # Pydantic models
│   │   └── {entity}.py
│   └── routes/              # API route handlers
│       └── {entity}.py
└── tests/
    ├── conftest.py          # Pytest fixtures
    └── test_{entity}.py     # Unit tests for each entity
    
```

### Coverage Configuration
The project uses pytest with coverage configured in `pytest.ini`:
```ini
[pytest]
testpaths = tests
python_files = test_*.py
addopts = --cov=app --cov-report=term-missing
```

### Execution Instructions
- Use npm scripts defined in `package.json` to run tests and check coverage:
```bash
npm run test:api
```


## Test Structure and Patterns

### 1. Import Requirements
Every test file should include:
```python
import pytest
from fastapi import status
from app.seed_data import {entities} as seed_{entities}
```

### 2. Required Fixtures (in conftest.py)
```python
import pytest
from fastapi.testclient import TestClient
from app.main import app
from app.routes import {entity}

@pytest.fixture(autouse=True)
def reset_{entity}_data():
    """Reset entity data before each test to ensure test isolation"""
    {entity}.{entities} = {entity}.{entities}.copy()
    yield
    {entity}.{entities} = list({entity}.seed_{entities})

@pytest.fixture
def client():
    """FastAPI test client"""
    return TestClient(app)

@pytest.fixture
def test_{entity}():
    """Sample entity data for testing"""
    return {
        # Entity fields with test data
        "id": 999,
        "field1": "test_value",
        # ... all required fields
    }
```

### 3. Test Naming Convention
- Test files: `test_{entity}.py`
- Test functions: `test_{action}_{entity}[_{scenario}]`

Examples:
- `test_get_all_branches()`
- `test_create_branch()`
- `test_get_branch_not_found()`
- `test_update_branch_with_invalid_data()`

## Required Test Coverage

For each API endpoint, generate tests covering:

### ✅ 1. GET All Items (`GET /{entities}`)
```python
def test_get_all_{entities}(client):
    """Test retrieving all entities"""
    response = client.get("/api/{entities}")
    assert response.status_code == status.HTTP_200_OK
    assert len(response.json()) == len(seed_{entities})
```

### ✅ 2. GET Single Item (`GET /{entities}/{id}`)
**Success case:**
```python
def test_get_{entity}(client):
    """Test retrieving a single entity by ID"""
    first_{entity} = seed_{entities}[0]
    response = client.get(f"/api/{entities}/{first_{entity}.{entityId}}")
    assert response.status_code == status.HTTP_200_OK
    assert response.json()["{entityId}"] == first_{entity}.{entityId}
```

**Not found case:**
```python
def test_get_{entity}_not_found(client):
    """Test retrieving non-existent entity returns 404"""
    response = client.get("/api/{entities}/99999")
    assert response.status_code == status.HTTP_404_NOT_FOUND
```

### ✅ 3. POST Create Item (`POST /{entities}`)
**Success case:**
```python
def test_create_{entity}(client, test_{entity}):
    """Test creating a new entity"""
    response = client.post("/api/{entities}", json=test_{entity})
    assert response.status_code == status.HTTP_201_CREATED
    assert response.json() == test_{entity}
```

**Validation error case:**
```python
def test_create_{entity}_invalid_data(client):
    """Test creating entity with invalid data returns 422"""
    invalid_{entity} = {
        # Missing required fields or invalid data types
        "{entityId}": "invalid",
    }
    response = client.post("/api/{entities}", json=invalid_{entity})
    assert response.status_code == status.HTTP_422_UNPROCESSABLE_ENTITY
```

### ✅ 4. PUT Update Item (`PUT /{entities}/{id}`)
**Success case:**
```python
def test_update_{entity}(client, test_{entity}):
    """Test updating an existing entity"""
    # First create
    create_response = client.post("/api/{entities}", json=test_{entity})
    assert create_response.status_code == status.HTTP_201_CREATED
    
    # Then update
    updated_{entity} = test_{entity}.copy()
    updated_{entity}["field"] = "Updated Value"
    response = client.put(
        f"/api/{entities}/{test_{entity}['{entityId}']}", 
        json=updated_{entity}
    )
    assert response.status_code == status.HTTP_200_OK
    assert response.json()["field"] == "Updated Value"
```

**Not found case:**
```python
def test_update_{entity}_not_found(client, test_{entity}):
    """Test updating non-existent entity returns 404"""
    response = client.put("/api/{entities}/99999", json=test_{entity})
    assert response.status_code == status.HTTP_404_NOT_FOUND
```

### ✅ 5. DELETE Item (`DELETE /{entities}/{id}`)
**Success case:**
```python
def test_delete_{entity}(client, test_{entity}):
    """Test deleting an entity"""
    # First create
    create_response = client.post("/api/{entities}", json=test_{entity})
    assert create_response.status_code == status.HTTP_201_CREATED
    
    # Then delete
    response = client.delete(f"/api/{entities}/{test_{entity}['{entityId}']}")
    assert response.status_code == status.HTTP_204_NO_CONTENT
    
    # Verify deletion
    get_response = client.get(f"/api/{entities}/{test_{entity}['{entityId}']}")
    assert get_response.status_code == status.HTTP_404_NOT_FOUND
```

**Not found case:**
```python
def test_delete_{entity}_not_found(client):
    """Test deleting non-existent entity returns 404"""
    response = client.delete("/api/{entities}/99999")
    assert response.status_code == status.HTTP_404_NOT_FOUND
```

## Complete Example: Branch Entity

### Model (app/models/branch.py)
```python
from pydantic import BaseModel, EmailStr

class Branch(BaseModel):
    branchId: int
    headquartersId: int
    name: str
    description: str
    address: str
    contactPerson: str
    email: str
    phone: str
```

### Routes (app/routes/branch.py)
```python
from fastapi import APIRouter, HTTPException
from typing import List
from app.models.branch import Branch
from app.seed_data import branches as seed_branches

router = APIRouter()
branches = list(seed_branches)

@router.get("/", response_model=List[Branch])
async def get_all_branches():
    return branches

@router.post("/", response_model=Branch, status_code=201)
async def create_branch(branch: Branch):
    branches.append(branch)
    return branch

@router.get("/{id}", response_model=Branch)
async def get_branch(id: int):
    branch = next((b for b in branches if b.branchId == id), None)
    if branch:
        return branch
    raise HTTPException(status_code=404, detail="Branch not found")

@router.put("/{id}", response_model=Branch)
async def update_branch(id: int, branch: Branch):
    index = next((i for i, b in enumerate(branches) if b.branchId == id), -1)
    if index != -1:
        branches[index] = branch
        return branch
    raise HTTPException(status_code=404, detail="Branch not found")

@router.delete("/{id}", status_code=204)
async def delete_branch(id: int):
    global branches
    index = next((i for i, b in enumerate(branches) if b.branchId == id), -1)
    if index != -1:
        branches.pop(index)
        return
    raise HTTPException(status_code=404, detail="Branch not found")
```

### Complete Test File (tests/test_branch.py)
```python
import pytest
from fastapi import status
from app.seed_data import branches as seed_branches

def test_get_all_branches(client):
    """Test retrieving all branches"""
    response = client.get("/api/branches")
    assert response.status_code == status.HTTP_200_OK
    assert len(response.json()) == len(seed_branches)

def test_create_branch(client, test_branch):
    """Test creating a new branch"""
    response = client.post("/api/branches", json=test_branch)
    assert response.status_code == status.HTTP_201_CREATED
    assert response.json() == test_branch

def test_get_branch(client):
    """Test retrieving a single branch by ID"""
    first_branch = seed_branches[0]
    response = client.get(f"/api/branches/{first_branch.branchId}")
    assert response.status_code == status.HTTP_200_OK
    assert response.json()["branchId"] == first_branch.branchId

def test_get_branch_not_found(client):
    """Test retrieving non-existent branch returns 404"""
    response = client.get("/api/branches/99999")
    assert response.status_code == status.HTTP_404_NOT_FOUND

def test_update_branch(client, test_branch):
    """Test updating an existing branch"""
    # First create a branch
    create_response = client.post("/api/branches", json=test_branch)
    assert create_response.status_code == status.HTTP_201_CREATED
    
    # Update the branch
    updated_branch = test_branch.copy()
    updated_branch["name"] = "Updated Test Branch"
    response = client.put(
        f"/api/branches/{test_branch['branchId']}", 
        json=updated_branch
    )
    assert response.status_code == status.HTTP_200_OK
    assert response.json()["name"] == "Updated Test Branch"

def test_update_branch_not_found(client, test_branch):
    """Test updating non-existent branch returns 404"""
    response = client.put("/api/branches/99999", json=test_branch)
    assert response.status_code == status.HTTP_404_NOT_FOUND

def test_delete_branch(client, test_branch):
    """Test deleting a branch"""
    # First create a branch
    create_response = client.post("/api/branches", json=test_branch)
    assert create_response.status_code == status.HTTP_201_CREATED
    
    # Delete the branch
    response = client.delete(f"/api/branches/{test_branch['branchId']}")
    assert response.status_code == status.HTTP_204_NO_CONTENT
    
    # Verify branch is deleted
    get_response = client.get(f"/api/branches/{test_branch['branchId']}")
    assert get_response.status_code == status.HTTP_404_NOT_FOUND

def test_delete_branch_not_found(client):
    """Test deleting non-existent branch returns 404"""
    response = client.delete("/api/branches/99999")
    assert response.status_code == status.HTTP_404_NOT_FOUND
```

### Fixture for Branch (tests/conftest.py)
```python
import pytest
from fastapi.testclient import TestClient
from app.main import app
from app.routes import branch

@pytest.fixture(autouse=True)
def reset_branch_data():
    """Reset branch data before each test"""
    branch.branches = branch.branches.copy()
    yield
    branch.branches = list(branch.seed_branches)

@pytest.fixture
def client():
    """FastAPI test client"""
    return TestClient(app)

@pytest.fixture
def test_branch():
    """Sample branch data for testing"""
    return {
        "branchId": 999,
        "headquartersId": 1,
        "name": "Test Branch",
        "description": "Test branch for unit tests",
        "address": "123 Test St",
        "contactPerson": "Test Person",
        "email": "test@example.com",
        "phone": "555-0123"
    }
```

## Running Tests with Coverage

### Command to Run Tests
```bash
# Run all tests with coverage
pytest

# Run specific test file
pytest tests/test_branch.py

# Run with verbose output
pytest -v

# Run with coverage report
pytest --cov=app --cov-report=term-missing

# Generate HTML coverage report
pytest --cov=app --cov-report=html

# Run specific test function
pytest tests/test_branch.py::test_create_branch
```

### Expected Coverage Output
```
---------- coverage: platform linux, python 3.x ----------
Name                              Stmts   Miss  Cover   Missing
---------------------------------------------------------------
app/__init__.py                       0      0   100%
app/main.py                          10      0   100%
app/models/__init__.py                0      0   100%
app/models/branch.py                  8      0   100%
app/routes/__init__.py                0      0   100%
app/routes/branch.py                 25      0   100%
---------------------------------------------------------------
TOTAL                                43      0   100%
```

## Coverage Goals
- **Target**: 80% minimum, 95%+ preferred
- **Critical paths**: 100% coverage required
- **New features**: Must include tests before merge

## Best Practices

### ✅ DO:
1. **Test isolation**: Each test should be independent
2. **Descriptive names**: Use clear, action-based test names
3. **Arrange-Act-Assert**: Follow the AAA pattern
4. **Test edge cases**: Include boundary conditions and error cases
5. **Use fixtures**: Leverage pytest fixtures for reusable test data
6. **Assert specifics**: Test specific values, not just status codes
7. **Document tests**: Add docstrings explaining test purpose
8. **Clean up**: Use fixtures with yield for setup/teardown

### ❌ DON'T:
1. **Test multiple concerns**: One test should verify one behavior
2. **Share state**: Tests should not depend on each other
3. **Hard-code IDs**: Use seed data or test fixtures
4. **Ignore validation**: Test both success and failure cases
5. **Skip error cases**: Always test 404, 422, and other errors
6. **Use production data**: Always use seed data or fixtures
7. **Leave commented code**: Remove or fix, don't comment out tests

## Prompt Instructions for AI

When asked to generate unit tests for a new API endpoint:

1. **Analyze the route file** in `app/routes/{entity}.py`
2. **Identify the Pydantic model** in `app/models/{entity}.py`
3. **Create/update conftest.py** with necessary fixtures
4. **Generate complete test file** `tests/test_{entity}.py` including:
   - All CRUD operations tests
   - Success cases (200, 201, 204)
   - Error cases (404, 422)
   - Edge cases specific to the entity
5. **Ensure test isolation** with proper fixture setup/teardown
6. **Follow naming conventions** from this document
7. **Include docstrings** for all test functions
8. **Verify coverage** by running `pytest --cov=app --cov-report=term-missing`

## Example Usage

**Prompt to AI:**
```
Generate unit tests for the Product API endpoint following the patterns in UNIT_TEST_GENERATION_PROMPT.md. 
The Product model has fields: productId, supplierId, name, description, unitPrice, and stockQuantity.
Ensure all CRUD operations are tested with both success and error cases.
```

**Expected Output:**
- Complete `tests/test_product.py` file
- Updated `tests/conftest.py` with product fixtures
- All 8+ test functions covering CRUD and error cases
- 100% coverage for `app/routes/product.py`

## Checklist for Generated Tests

Before submitting generated tests, verify:

- [ ] All HTTP methods are tested (GET, POST, PUT, DELETE)
- [ ] Success cases return correct status codes
- [ ] Error cases (404, 422) are tested
- [ ] Response data is validated, not just status codes
- [ ] Fixtures are properly defined in conftest.py
- [ ] Test names follow convention: `test_{action}_{entity}[_{scenario}]`
- [ ] All tests have docstrings
- [ ] Tests are independent (no shared state)
- [ ] Run `pytest` successfully
- [ ] Coverage report shows >90% for the route file
- [ ] No hard-coded values (use fixtures and seed data)

---

## Additional Testing Scenarios

### Query Parameters
If endpoints support query parameters:
```python
def test_get_{entities}_with_filter(client):
    """Test filtering entities by query parameters"""
    response = client.get("/api/{entities}?filter_field=value")
    assert response.status_code == status.HTTP_200_OK
    assert all(item["filter_field"] == "value" for item in response.json())
```

### Pagination
If endpoints support pagination:
```python
def test_get_{entities}_paginated(client):
    """Test pagination of entities"""
    response = client.get("/api/{entities}?page=1&size=10")
    assert response.status_code == status.HTTP_200_OK
    assert len(response.json()) <= 10
```

### Relationships
If entity has relationships:
```python
def test_get_{entity}_with_related_data(client):
    """Test retrieving entity with related data"""
    response = client.get(f"/api/{entities}/{id}?include=related")
    assert response.status_code == status.HTTP_200_OK
    assert "relatedData" in response.json()
```
