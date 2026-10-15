# claude-qa

**A set of QA skills for Claude.** Skills live in `skills/`; more will be added over time. Currently included: `qa-checklist-from-diff`.

## qa-checklist-from-diff

**A Claude Agent Skill that turns a branch diff into a manual QA checklist.** It compares a branch against its base, works out what changed, finds where previously working behavior could have broken, and hands QA a prioritized list of checks with expected results and time estimates. Test planning and regression-risk review in Claude Code or claude.ai.

It is analytical only: it never proposes code changes. If it spots a bug, it describes it and how a user would experience it.

### What you get

| Section | What goes in it |
| --- | --- |
| What changed | Changes grouped by feature, with key files linked; infrastructure and config changes in a short separate block |
| Regression zones | Places where existing functionality could have broken, and why |
| Specific risks | Likely bug spots and how each would show up for the user |
| Manual test checklist | Numbered checks (action, expected result, time), ordered by risk, plus a minimal 2-3 check core for when time runs out |

### Run parameters

| Parameter | Meaning | Default |
| --- | --- | --- |
| `BRANCH` | Branch to analyze | current branch |
| `BASE_BRANCH` | Branch to compare against | first existing of develop, dev, main, master |
| `MODE` | `FULL` (regression + new behavior) or `REGRESSION_ONLY` | `FULL` |
| `BUDGET` | Minutes available for the checklist; a hard limit | no limit |
| `LANGUAGE` | Language of the answer | language of your request |
| `FORMAT` | Output format (markdown, plain text, table, .md/.docx file) | markdown, no tables |

`MODE` and `BUDGET` shape only the checklist; the change summary and risk sections are always given in full.

## Install (all skills)

**As a plugin** (Claude Code), through the [time2map marketplace](https://github.com/time2map/skills):

```
/plugin marketplace add time2map/skills
/plugin install claude-qa@time2map
```

**As a plain skill**: copy `skills/qa-checklist-from-diff/` into `~/.claude/skills/` (for yourself) or `.claude/skills/` (for a project).

**On claude.ai**: add the contents of `skills/qa-checklist-from-diff/SKILL.md` as a skill in your settings.

### Use it

```
Make a QA checklist for feature/booking-cancel against develop, regression only, 30 minutes, in English 
```

Or call it by name: `/qa-checklist-from-diff`.

## Author

Arina Kagarlitskaia — [@krivtsova-arina](https://github.com/krivtsova-arina), for [time2map](https://time2map.com)

MIT licensed.
