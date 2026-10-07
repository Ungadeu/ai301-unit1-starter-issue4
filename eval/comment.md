#### Template for `comment.md`
In the same top-level folder of your fork, create **`comment.md`**:

```markdown
Hi! Here is my plan for fixing this issue:

**What I found (Diagnosis):**
Based on the reproduction (`<brief quote of your repro command/result>`), the root cause is in `<path/to/file.py>` within `<function_or_class_name>()`, where `<1-sentence explanation of why the bug happens>`.

**What I'll do (Scope & Approach):**
- Update `<function_or_class_name>()` in `<path/to/file.py>` to `<describe the specific fix>`.
- Add a regression test in `<tests/path/to/test_file.py>` covering this case.
- Out of scope: I will not modify any external APIs, CLI flags, or unrelated files.

**How I'll prove it (Test Plan):**
- Re-run the reproduction steps (`<repro command>`), where the output currently shows `<before output>` and after the fix will show `<expected after output>`.
- Run `pytest` on `<tests/path/to/test_file.py>` to ensure all tests pass.

*(Note: Prepared with AI assistance via Claude Code.)*
