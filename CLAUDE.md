# Claude Code Lab (documentation repo)

A 90-minute beginner lab for Claude Code. This repo contains **only Markdown**: no app code, no build, no tests. Learners build the TaskBoard app themselves following the lab.

## Layout
- `README.md` — agenda and index
- `theory/` — short readings, one per topic (`01`–`06` + glossary/cheatsheet)
- `lab/` — numbered step-by-step labs (`00`–`08`)
- `project-base/` — `spec.md` (user stories + API) and `stack.md` handed to learners

## Conventions
- All content in **English**.
- Lab order is fixed: basics → harness → skills → MCP → loops → custom agents → plugins. Keep the agenda in `README.md` in sync if you add or reorder files.
- Every lab file follows: Goal · Time · Theory link · Numbered steps with copy-paste prompts · Checkpoint · What to observe · If something fails.
- Theory files: ≤ 1 page, end with "Check yourself" and a "Next" link.
- Use relative links between files; keep them valid after renames.
- Claude Code syntax (frontmatter, hooks, MCP, plugin layout) changes fast: verify against https://code.claude.com/docs before editing those parts.

## Verifying changes
Check internal links resolve:
```bash
for f in README.md theory/*.md lab/*.md; do d=$(dirname $f); grep -o '](\([^)#]*\.md\))' $f | sed 's/](\(.*\))/\1/' | while read l; do [ -f "$d/$l" ] || echo "BROKEN $f -> $l"; done; done
```
