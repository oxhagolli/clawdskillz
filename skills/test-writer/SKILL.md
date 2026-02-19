---
name: test-writer
description: Use this skill when writing unit tests or integration tests for code. Prefers integration tests that reflect real usage, keeps tests fast and simple, and avoids over-mocking. Trigger after implementing features or when adding test coverage.
---

# Test Writer Protocol

Write tests that catch real bugs and run fast. Prefer integration tests. Keep it simple.

## Core Principles

- **Integration over unit** - Test how things work together, not internals
- **Real usage** - Tests should look like how code is actually used
- **Fast tests** - Slow tests are a permanent tax on the repo
- **Simple tests** - If a test is hard to understand, it's hard to maintain
- **Pragmatic coverage** - High coverage when it makes sense, don't chase numbers

## Test Hierarchy

Prefer tests in this order:

```
1. Integration tests (best)
   - Test real components working together
   - Use real database, real HTTP calls (to test servers)
   - Catch the bugs that actually happen

2. Functional tests
   - Test a module's public interface
   - Real dependencies where reasonable
   - Mock only external services

3. Unit tests (use sparingly)
   - Only for complex pure logic
   - Algorithms, calculations, parsing
   - Avoid for simple glue code
```

## Integration Test Philosophy

**Test the whole flow:**
```python
# Good - tests real behavior
async def test_create_order():
    # Setup: real database, real service
    user = await create_test_user()
    product = await create_test_product(price=100)

    # Act: real API call or service call
    order = await order_service.create(user_id=user.id, product_id=product.id)

    # Assert: check real outcomes
    assert order.total == 100
    assert await db.orders.exists(order.id)
    assert user.balance_changed()
```

```python
# Bad - tests nothing real
def test_create_order():
    mock_db = Mock()
    mock_user = Mock(id=1)
    mock_product = Mock(id=2, price=100)
    mock_db.orders.insert.return_value = Mock(id=99)

    # All you're testing is that mocks return what you told them to
    result = create_order(mock_db, mock_user, mock_product)
    assert result.id == 99  # Meaningless
```

## When to Unit Test

Unit tests make sense for:
- Pure functions with complex logic
- Algorithms (sorting, parsing, calculations)
- Edge case handling in isolated functions
- Code that's genuinely independent

```python
# Good unit test - pure logic
def test_calculate_discount():
    assert calculate_discount(100, percent=10) == 90
    assert calculate_discount(100, percent=0) == 100
    assert calculate_discount(0, percent=50) == 0

# Bad unit test - just testing wiring
def test_user_service_calls_repository():
    mock_repo = Mock()
    service = UserService(mock_repo)
    service.get_user(1)
    mock_repo.find.assert_called_with(1)  # Who cares?
```

## Keep Tests Fast

**Speed budget:**
- Unit test: < 10ms
- Integration test: < 500ms
- Full test suite: < 60 seconds (ideal), < 5 minutes (maximum)

**How to stay fast:**
```python
# Use test database with fast resets
@pytest.fixture
async def db():
    async with test_database() as db:
        yield db
        # Cleanup is fast - truncate, don't recreate

# Parallelize where possible
# pytest-xdist, vitest --pool=threads

# Avoid unnecessary setup
# Don't create 100 users if you need 1
```

**Slow test red flags:**
- Sleeping/waiting for time (`time.sleep`, `setTimeout`)
- Starting/stopping servers per test
- Network calls to external services
- Large dataset generation

**Fix slow tests:**
```python
# Bad - waits for real time
def test_cache_expires():
    cache.set("key", "value", ttl=60)
    time.sleep(61)
    assert cache.get("key") is None

# Good - mock time
def test_cache_expires(mock_time):
    cache.set("key", "value", ttl=60)
    mock_time.advance(61)
    assert cache.get("key") is None
```

## Keep Tests Simple

**One concept per test:**
```python
# Good - clear what's being tested
def test_user_can_place_order():
    ...

def test_user_cannot_order_out_of_stock_product():
    ...

def test_order_calculates_tax_correctly():
    ...

# Bad - tests everything at once
def test_order_system():
    # Creates user, product, order, checks tax, inventory, email...
    # If it fails, which part broke?
```

**Readable assertions:**
```python
# Good
assert user.is_active
assert order.total == 150
assert "error" not in response.json()

# Bad
assert response.json()["data"]["user"]["status"]["active"] == True and \
       response.json()["data"]["order"]["totals"]["final"] == 150
```

**Minimal setup:**
```python
# Good - only what matters
def test_discount_applied():
    order = Order(subtotal=100)
    order.apply_discount(10)
    assert order.total == 90

# Bad - irrelevant details
def test_discount_applied():
    user = User(name="John", email="john@example.com", created=datetime.now())
    product = Product(name="Widget", sku="W001", category="Tools")
    order = Order(user=user, products=[product], subtotal=100, ...)
    # None of this matters for testing discount logic
```

## What to Mock (and What Not To)

**Mock:**
- External APIs (payment providers, email services)
- Time-dependent behavior
- Random/non-deterministic operations
- Services you don't control

**Don't mock:**
- Your own database (use test DB)
- Your own services (test them together)
- Simple dependencies (just use the real thing)
- Return values you're asserting against

```python
# Good mocking - external service
@patch("stripe.Charge.create")
def test_payment_processed(mock_stripe):
    mock_stripe.return_value = {"id": "ch_123", "status": "succeeded"}
    result = process_payment(amount=100)
    assert result.success

# Bad mocking - your own code
@patch("myapp.services.user_service.get_user")
@patch("myapp.services.order_service.create_order")
def test_checkout(mock_user, mock_order):
    # You're not testing anything real
```

## Coverage Philosophy

**Don't chase numbers:**
```
80% coverage with good tests > 100% coverage with bad tests
```

**Cover what matters:**
- Business logic (revenue, security, data integrity)
- Error paths that could fail silently
- Integration points between components

**Skip coverage for:**
- Boilerplate/glue code
- Simple getters/setters
- Generated code
- Code that's obviously correct

**When to increase coverage:**
- After a bug (add test that would have caught it)
- For complex logic
- For security-sensitive code

## Response Format

When writing tests:

```
## Test Plan

**Type**: [Integration / Functional / Unit]
**Scope**: [What's being tested]

**Tests to write**:
1. [test_name] - [what it verifies]
2. [test_name] - [what it verifies]

**Mocking strategy**:
- External services: [what's mocked]
- Using real: [what's not mocked]

**Speed estimate**: [fast/medium/needs optimization]
```

## Checklist

Before finishing tests:

- [ ] Tests reflect real usage patterns
- [ ] Integration tests for main flows
- [ ] Unit tests only for complex pure logic
- [ ] All tests run in < 500ms each
- [ ] No unnecessary mocking
- [ ] Each test has one clear purpose
- [ ] Failures give useful error messages
- [ ] No flaky/timing-dependent tests

## Calibration

**Prefer fewer, better tests.** 10 meaningful integration tests beat 100 trivial unit tests.

**Test behavior, not implementation.** If you refactor and tests break but behavior didn't change, the tests were wrong.

**Make failures obvious.** A test that fails should tell you exactly what's wrong.

**Stay in your lane.** Don't cover:
- Which edge cases to test (test-engineer)
- Whether to build the feature (product-manager)
- Code style/linting (linting-engineer)
