# Eval item: issue-04

- source: zxcalc/zxlive#555
- captured: 2026-08-05
- calibration: false

## Repo facts (captured 2026-08-05)

- repo: zxcalc/zxlive (101 stars, archived: no)
- description: A graphical tool for the ZX calculus
- last push to any branch: 2026-08-04
- latest release: v1.0.0 (2026-04-29)
- open issues + PRs: 64
- last 5 default-branch commits:
  - 2026-08-04 by RazinShaikh: Edge tool can draw multiple parallel edges at once (#521)
  - 2026-08-04 by RazinShaikh: Merge pull request #554 from zxcalc/fix/package-runtime-assets
  - 2026-08-04 by RazinShaikh: Fix resource packaging for release binaries
  - 2026-08-04 by RazinShaikh: Merge pull request #551 from 96-LB/magic-hopf-fix
  - 2026-08-04 by 96-LB: Update Hopf rule matching for X spiders
- maintainer first-response sample (5 recently updated issues, days to first owner/member/collaborator comment):
  - #462 (opened 2026-03-03 by a maintainer): 25.8 days
  - #507 (opened 2026-05-01): 0.4 days
  - #513 (opened 2026-05-04 by a maintainer): 92.1 days
  - #512 (opened 2026-05-04 by a maintainer): no maintainer comment in thread
  - #553 (opened 2026-08-04 by a maintainer): no maintainer comment in thread
- contribution policy (CONTRIBUTING.md): no statement on AI or contribution tooling
- this issue: assignees: none; linked PRs: none

## Issue

### Missing several basic rule previews (#555)

opened by RazinShaikh (COLLABORATOR) on 2026-08-04, state open, labels: Type: bug, good first issue, Category: Proof mode, Priority: Medium

Including remove identity, fuse spiders, remove self loops, etc.

## Comments (3 total, first 2 shown)

Hi, I'd like to take this as a first contribution. In Proof mode, I'll check which of the basic rules (remove identity, fuse spiders, remove self loops) show no preview, then look at how the rules that do have previews are set up, and report back what I find. I will set up the environment to run tests and investigate the bug.

What I found: A rule's preview comes from its "picture" key in zxlive/rewrite_data.py, and only 4 Basic rules have one. zxlive/tooltips/ already contains remove_id.gif, fuse_spiders.gif, change_color_x.gif, change_color_z.gif and decompose_hadamard.gif, but no rule references them. I haven't checked whether they're the intended previews or whether GIFs render in the tooltip. I found no picture files for self-loops, parallel edges or unfuse.

<- Hi! Here is my plan for fixing this issue:

**What I found (Diagnosis):**
Based on the reproduction (`QT_QPA_PLATFORM=offscreen .venv/bin/python check_previews.py` on `d2f302c` printed `NO PREVIEW` for 8 of the 12 Basic rules), the root cause is in `zxlive/rewrite_action.py` within `RewriteAction.from_rewrite_data()`. That function only sets `picture_path` when the rule's data has a `"picture"` file or `"custom_rule"`, and those 8 entries in `rules_basic` have neither, so the tooltip falls back to plain text.

One thing I checked: the GIFs in `zxlive/tooltips/` are not the missing previews. They're multi-frame demo clips, and `QPixmap.load()` only reads frame 0, which shows the old sidebar and the graph *before* the rewrite.

**What I'll do (Scope & Approach):**
- Generate the previews in code instead of adding images. `tooltip` can already render a `lhs` = `rhs` picture from two graphs (the `'custom'` path used by custom rules).
  - New `zxlive/rule_previews.py`: a tiny example graph for each of Remove identity, Fuse spiders, Remove self-loops, Remove parallel edges, Colour change and Decompose Hadamard. Each `rhs` is made by applying the real pyzx rule to the example, so the picture always matches what the rule does.
  - Unfuse spider reuses the fuse example in reverse, since `UnfusionRewrite.apply()` is interactive.
  - `rewrite_data.py` attaches these as `lhs`/`rhs` on `rules_basic`.
  - Update `RewriteAction.from_rewrite_data()` in `zxlive/rewrite_action.py` with one branch: `lhs`/`rhs` present and no `"picture"` → `picture_path = 'custom'`. I won't set `custom_rule`, so `is_custom_rule` stays `False`.
- Add a regression test in `test/test_rule_previews.py`. It checks that each of the 7 rules' tooltip has an `<img>`, and that each generated `rhs` is tensor-equal to its `lhs` (`pyzx.compare_tensors`).
- Out of scope: I will not modify any external APIs, CLI flags, or unrelated files. That includes the existing image assets, the look of the `'custom'` renderer, other rule groups, and "Save changed positions", which isn't a graph rewrite, so it stays text-only.

**How I'll prove it (Test Plan):**
- Re-run the reproduction steps (`QT_QPA_PLATFORM=offscreen .venv/bin/python check_previews.py`). The output currently shows 8 `NO PREVIEW` lines, and after the fix will show `PREVIEW` for 11 of 12 rules, with only `NO PREVIEW  Save changed positions` left.
- Run `pytest` on `test/test_rule_previews.py` to ensure all tests pass, plus `pytest test/`, `mypy zxlive`, `ruff check` and `complexipy . --max-complexity-allowed 15` as in CI.

**Before I start:** I prototyped this locally and the pictures look right, but they're in the app's own graph style rather than your hand-drawn PNGs. Are generated previews OK for these rules, or would you rather have PNGs to match the existing ones?

*(Note: Prepared with AI assistance via Claude Code.)*
 ->

