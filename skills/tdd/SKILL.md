---
name: tdd
description: Facilitates Test-Driven Development (TDD) by running tests, implementing minimal code to pass failing tests, and performing pure refactoring. Use this when you need to develop features incrementally with high test coverage.
license: MIT
---

# TDD Assistant Skill

Follow this strict Red-Green-Refactor workflow to develop features. The core principle is to only add the code that is necssary to fix a test and not more. If that means you have to create a function without any implementation in order to resolve a function not found error then only create that function witout any implementation!

## 1. Red Phase (Verify Failure)
- **Action**: Run the existing test suite.
- **Goal**: Confirm that the newly added test fails as expected.
- **Constraint**: Do not write implementation code yet. If the test passes unexpectedly, stop and ask for clarification.

## 2. Green Phase (Make it Pass)
- **Action**: Write the **minimal** amount of code necessary to make the failing test pass.
- **Goal**: Get to a passing state as quickly as possible.
- **Constraint**: DO NOT implement "nice-to-have" features or extra logic outside the scope of the current failing test.

## 3. Refactor Phase (Clean Up)
- **Action**: Improve the code structure, readability, or performance without changing behavior. Pay extra attention to not new logic or functionalities!!
- **Goal**: Maintain a clean codebase while keeping tests green.
- **Constraint**: **Pure refactoring only.** Do not add new functionality or change logic during this step. Run tests after every change to ensure they remain green.

## Examples

### Input
"I've added a test for a `pop` function in `Stack`. Please implement it."

### Output Process
1. Run `make test` -> Observe failure (AttributeError: 'Stack' object has no attribute 'pop').
2. Create a function `pop` in the Stack-class. But not more!! No need to modify other code of the class or provide an implementation for `pop` yet.
3. Run `make test` -> Observe failure (Failed: DID NOT RAISE <class 'IndexError'>).
4. Add a raise statement to function `pop` in the Stack-class. But not more!! No need to modify other code of the class or implement more than raising an IndexError.
4. Run `make test` -> Observe success.
5. Review code for naming or style improvements.

## Edge Cases
- **Broken Tests**: If the test itself has a syntax error, ask the user to fix the test.
- **Multiple Failures**: Focus on fixing one test failure at a time unless they are logically inseparable.
