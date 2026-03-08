# /project:sync

Run at the end of every work session to keep the repository as the single source of truth.

## What it does

1. **Sync any external artifacts** (n8n workflows, configs, generated files) to `src/`
2. **Update `memory/STATUS.md`** with current project state
3. **Append to `memory/YYYY-MM-DD.md`** with what changed this session (creates if doesn't exist, appends if it does — safe to run multiple times per day)
4. **Record Mulch entries** for anything new this session
5. **Commit and push** everything to GitHub

## Instructions

### Step 1 — Sync external artifacts

If this project has an n8n workflow, pull the latest JSON:

```bash
curl -s -H "X-N8N-API-KEY: $N8N_API_KEY" \
  "$N8N_INSTANCE/api/v1/workflows/$WORKFLOW_ID" | \
  python3 -c "import json,sys; print(json.dumps(json.loads(sys.stdin.read()), indent=2))" \
  > src/workflow.json
```

For other artifacts (scripts, configs, exports) — copy latest versions to `src/`.

### Step 2 — Update memory/STATUS.md

Rewrite `memory/STATUS.md` with the current snapshot:
- Current phase / status
- What's built and working
- What's blocked or waiting
- Immediate next actions

### Step 3 — Append to daily log

Append a new section to `memory/YYYY-MM-DD.md` (today's date):

```markdown
## Session — HH:MM

### What was done
- ...

### Decisions made
- ...

### Bugs fixed
- ...

### Next
- ...
```

### Step 4 — Record Mulch entries for new things

Only record things that are NEW this session:

```bash
# Decision
npx mulch-cli record <domain> --type decision --name "<name>" --title "<what>" --rationale "<why>"

# Bug / fix
npx mulch-cli record <domain> --type failure --name "<name>" --description "<what broke, what fixed it>"

# Pattern / convention
npx mulch-cli record <domain> --type convention --name "<name>" --description "<the rule>"

# Reference info
npx mulch-cli record <domain> --type reference --name "<name>" --description "<the info>"
```

Run `npx mulch-cli status` to see available domains.

### Step 5 — Commit and push

```bash
git add -A
git commit -m "chore: session sync YYYY-MM-DD — <one line summary>"
git push origin main
```

## Tips

- **Run this before ending every session** — not just when something big happened
- **Multiple sessions per day** = multiple appended blocks in the same daily log file
- **Mulch records** = the searchable brain for future agents picking up this project
- **Don't skip the commit** — uncommitted work is invisible to the next session
