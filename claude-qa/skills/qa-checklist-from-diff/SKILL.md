---
name: "qa-checklist-from-diff"
description: "Analyze a branch diff against a base branch and produce a manual QA test checklist (full or regression-only mode, time budget, output language and format as run parameters). Use for test plans, QA checklists, regression zones or risk review of a branch/MR/PR."
---

# Manual QA checklist from a branch diff

The job is analytical: understand what changed on a branch, find where previously working behavior could have broken, and hand QA a checklist. Do not propose code changes. If you spot a bug, describe it and how a user would experience it, nothing more.

## Run parameters

- **BRANCH** and **BASE_BRANCH**: what to compare. If BRANCH is missing, use the current branch. If BASE_BRANCH is missing, check which candidate (develop, dev, main, master) actually exists in each repository, since names can differ between repos, and say which one you used or ask.
- **MODE**: `FULL` or `REGRESSION_ONLY`. Default `FULL`; state the default in the answer when the user did not choose.
- **BUDGET**: N minutes available for walking through the checklist. If not set, the checklist is exhaustive with no time limit.
- **LANGUAGE**: language of the whole answer. Default: the language the user wrote their request in.
- **FORMAT**: output format. Default: markdown with no tables, numbered lists for the checklist, and file references as markdown links with the full path. If the user names another format (plain text, a table, a file such as .md or .docx), follow it; if the format needs a file, create it and deliver it.

How the parameters shape the result:

- `REGRESSION_ONLY`: the checklist contains only checks of previously working functionality that could have broken. Tests that verify the new functionality on its own are excluded entirely, even when that functionality is complex or risky.
- `FULL`: regression plus new behavior.
- BUDGET is a hard limit: the sum of time estimates over all checklist items must not exceed it. Fit it by dropping lower-risk checks, never by splitting the remaining ones into shallow steps. Each item stays a meaningful scenario with a verifiable result.

MODE and BUDGET constrain only section 4 (the checklist). Always give sections 1-3 in full, so QA and developers can see what was left outside the checklist and why.

## Gathering the data

1. Build the real diff: commit list, changed files, and the full diff content across backend, frontend and shared packages. If several repositories are connected, do each one. Read new files in full.
2. Do not stop at the diff. For every changed place, search the code for its consumers and for whatever else depends on the changed types, fields, hooks and queries.
3. Separately check whether code or tests still reference removed fields or symbols.
4. Work on the files where they live. If the network is unavailable and a fetch fails, work from local branches and say so.

## What to return

### 1. What changed

Group by feature, not by file. For each feature: the essence of the change and key files with links. Then a separate short block for infrastructure and config minor changes.

### 2. Regression zones

Only places where previously working functionality could have broken. For each zone, explain why it is affected: which refactor, type change or new request parameter sits on the path of existing behavior. Pay special attention to:

- changes to the format or semantics of stored data, including overwriting of old values on save;
- changes to the type of sorted values, memoization keys, or query cache keys;
- refactoring extracted "along the way" from existing rendering or fetching logic;
- new optional parameters on existing APIs: behavior with an empty value must be identical to before;
- legacy data: old records, old ids, records missing newly required fields;
- competition for space in the UI: new overlays on top of existing elements.

### 3. Specific risks

Places where a bug is likely: mismatched value normalization, duplicate keys, option source out of sync with stored data, and similar. For each, say how it would show up for the user. Always output this section, even when the matching check did not fit the checklist budget.

### 4. Manual test checklist

A numbered list within the chosen mode and budget. Each item: action, expected result, time estimate. Order by descending risk. Show the total time next to the budget. End with a minimal core of 2-3 checks for when time runs out.