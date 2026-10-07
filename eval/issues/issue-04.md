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
Based on the reproduction (`QT_QPA_PLATFORM=offscreen .venv/bin/python check_previews.py` on `main` at `d2f302c` printed `NO PREVIEW` for 8 of the 12 Basic rules, including Remove identity, Fuse spiders and Remove self-loops), the root cause is in `zxlive/rewrite_data.py` within the `rules_basic` dict, where those entries have no `"picture"` key. `RewriteAction.from_rewrite_data()` then sets `picture_path = None`, so the `tooltip` property returns plain text.

**What I'll do (Scope & Approach):**
- Update `rules_basic` in `zxlive/rewrite_data.py` to add a `"picture"` key for the rules that already have an image in `zxlive/tooltips/`:
  - `id_simp` → `remove_id.gif`
  - `fuse_simp` → `fuse_spiders.gif`
  - `euler` → `decompose_hadamard.gif`
  - `cc` → `change_color_z.gif`
- Add a regression test in `test/test_rewrite_previews.py`. It builds each of those four rules with `RewriteAction.from_rewrite_data()` and asserts that its `tooltip` contains an `<img`. It also checks that the pixmap loaded from the picture file is not null, so a GIF that Qt can't read fails the test instead of showing a blank preview.
- Out of scope: I will not modify any external APIs, CLI flags, or unrelated files. I also won't create new artwork. Remove self-loops, Remove parallel edges and Unfuse spider have no image in `zxlive/tooltips/`, so they will still show text only after this change.

**How I'll prove it (Test Plan):**
- Re-run the reproduction steps (`QT_QPA_PLATFORM=offscreen .venv/bin/python check_previews.py`). The output currently shows `NO PREVIEW  Remove identity`, `NO PREVIEW  Fuse spiders`, `NO PREVIEW  Colour change` and `NO PREVIEW  Decompose Hadamard`. After the fix it will show `PREVIEW` for those four. The 4 rules that already show a preview stay `PREVIEW`, and the 4 without an image stay `NO PREVIEW`.
- Run `pytest` on `test/test_rewrite_previews.py` to ensure all tests pass, then run the full `pytest test/` suite to check nothing else broke.

**Questions before I start:**
- The existing previews are all `.png`, but the matching files here are `.gif`. Are these GIFs the intended previews? If not, would you rather I leave those rules for now?
- For Colour change there are both `change_color_x.gif` and `change_color_z.gif`. Is one of them preferred?
- Remove self-loops is named in the issue, but there's no image for it. Should I make one in the style of the existing previews as a follow-up, or is someone else handling the artwork?

*(Note: Prepared with AI assistance via Claude Code.)* ->

