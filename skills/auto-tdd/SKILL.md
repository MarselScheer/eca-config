---
name: auto-tdd
description: Performs autonomous Test-Driven Development. Use this skill when you need to implement new features or fix bugs by strictly following the Red-Green-Refactor cycle.
license: MIT
metadata:
  workflow: iterative-coding
---

# TDD Cycle Instructions

Follow this strict iterative process for every feature or bug fix. Do not write implementation code before a failing test exists.

## The Process

### 1. Red: Define the Test
- Identify the smallest possible unit of functionality to implement.
- Write a test case that describes this functionality.
- Run the test and confirm it **fails** (or fails to compile).
- **Constraint**: Each TDD cycle should start by creating **one and only one** test. Each cycle MUST starts with EXACTLY ONE failing test.
- **Output:** The test code and the failure message.

### 2. Green: Minimal Implementation
- Write the **absolute minimum** amount of code required to make the failing test pass.
- Do not worry about code quality or future requirements yet.
- Run the tests and confirm they **pass**.
- **Output:** The minimal implementation code and the passing test result.
- **Constraint**: DO NOT implement "nice-to-have" features or extra logic outside the scope of the current failing test.

### 3. Refactor: Clean Up
- Review the code for duplication, readability, and architectural standards.
- Improve the code while ensuring the tests remain green.
- **Output:** The refactored code and confirmation that tests still pass.
- **Constraint**: **Pure refactoring only.** Do not add new functionality or change logic during this step. Run tests after every change to ensure they remain green.


## Guidelines
- **Granularity:** Keep cycles small. A single cycle should ideally take less than 5 minutes of "thought."
- **State Management:** Always verify the current state of the codebase before starting a new cycle.

## Example Cycle
- **Test:** `test_addition_adds_two_numbers` (NOTE: ONLY ONE test is created)
- **Failure:** `NameError: global name 'add' is not defined`
- **Implementation:** `def add(a, b): return a + b`
- **Refactor:** (If necessary) Add type hints: `def add(a: int, b: int) -> int:`


## Pytest Naming Conventions

Follow these naming conventions to keep tests organized and discoverable.

### File Naming
- **Pattern:** `test_<module>.py`
- **Example:** `test_calculator.py`, `test_auth.py`

### Test Function/Method Naming
- **Pattern:** Start with `test_`
- **Structure:** `test_<what>_<conditions>_<expected_result>`
- **Examples:**
  - `test_add_two_numbers_returns_sum()` - Simple functionality
  - `test_calculator_add_returns_sum()` - Class-based method
  - `test_empty_input_returns_zero()` - Scenario-based

### Test Class Naming
- **Pattern:** Start with `Test` (PascalCase)
- **Example:** `class TestCalculator:`
- **Note:** Methods within classes still use the `test_` prefix

### Best Practices
- Use **descriptive names** that explain the expected behavior
- Keep names concise but informative
- Group related tests in classes for better organization
- One test per function/method to ensure isolation
