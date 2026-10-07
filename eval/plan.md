# Implementation Plan — Issue #<ISSUE_NUMBER>

## 1. Diagnosis
Based on my Unit 2 reproduction on `codepath/pathreview-ai301-fa26-s1`:
> "<Paste 1–3 exact lines/output from your Unit 2 reproduction report here so Claude can verify your evidence!>"

Tracing upstream from this reproduced output, the root cause is in `<path/to/file.py>` inside `<function_or_class_name>()`, where `<explain the exact bug mechanism—for example, a missing check, off-by-one condition, or unhandled case>`. Fixing it here addresses the root cause rather than painting over the symptom.

## 2. Scope
- **In scope:** Updating `<function_or_class_name>()` in `<path/to/file.py>` to `<state the exact change>` and adding/updating unit test coverage in `<tests/path/to/test_file.py>`.
- **Not in scope (Boundary):** Modifying public CLI flags, changing unrelated helper functions in `<path/to/file.py>`, or refactoring other modules in the repository.

## 3. Files to Touch
- `<path/to/file.py>`: `<1-line summary of the code edit>`
- `<tests/path/to/test_file.py>`: `<1-line summary of the unit/regression test to add>`

## 4. Approach
1. In `<path/to/file.py>`, locate `<function_or_class_name>()`.
2. Change `<describe current buggy logic>` to `<describe fixed logic>`.
3. In `<tests/path/to/test_file.py>`, add a test function `<test_name>()` that passes the reproduction input and asserts `<expected behavior>`.

## 5. Test Plan
1. Re-run the exact reproduction command from Unit 2:
   ```bash
   <paste your exact Unit 2 repro command, e.g., pytest ... or python3 -m ...>
