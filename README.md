# duallogic-hermes

Hermes Agent backup — synced nightly at 2:00 AM.

## What's included

| File/Dir | Description |
|---|---|
| `SOUL.md` | Agent personality & identity |
| `config.yaml` | Main Hermes configuration |
| `channel_directory.json` | Channel routing setup |
| `.skills_prompt_snapshot.json` | Skills prompt snapshot |
| `skills/` | Custom skills (SKILL.md files) |
| `plans/` | Saved implementation plans |
| `memories/` | Persistent memory entries |
| `kanban.db` | Kanban board state |
| `wiki/` | LLM wiki content (index, concepts, entities, queries, raw, comparisons, _archive) |
| `wiki-skill.md` | LLM wiki skill definition |

## What's excluded (secrets — never push these)

`.env`, `auth.json`, `gateway_state.json`, `state.db*`, `cache/`, `logs/`, `audio_cache/`, `image_cache/`, `pairing/`, `bin/`, `cron/`, `hooks/`, `home/`, `webui/`, `sandboxes/`, `workspace/`, `models_dev_cache.json`

---

## For the Agent — Restore Procedure

*This section is for automated/agent-led restores. The human user should share this repo or point an agent at it.*

### Pre-restoration check

1. Identify the target machine — this backup lives at `/home/hermeswebui/.hermes`
2. Clone the repo if not already present:
   ```
   git clone https://github.com/mrtopher/duallogic-hermes.git ~/duallogic-hermes
   cd ~/duallogic-hermes/hermes-backup
   ```
3. List all files before touching anything:
   ```bash
   find hermes-backup/ -type f | sort
   ls -la ~/.hermes/
   ```

### Restore sequence

**Step 1 — Config files (always safe, no secrets)**
```bash
cp hermes-backup/SOUL.md ~/.hermes/
cp hermes-backup/config.yaml ~/.hermes/
cp hermes-backup/channel_directory.json ~/.hermes/
```

**Step 2 — Skills**
```bash
# Custom skills from hermes-backup/skills/ — check if any are user-created
# vs. Hermes built-ins (built-ins ship with install, don't overwrite those)
ls hermes-backup/skills/

# Copy only non-built-in skills. Check each skill name against the skills_list.
# Safe to restore: any skill not in the hermes-agent core bundle.
cp -r hermes-backup/skills/* ~/.hermes/skills/
```

**Step 3 — Wiki**
```bash
# LLM wiki content
mkdir -p /home/hermeswebui/wiki
cp -r hermes-backup/wiki/* /home/hermeswebui/wiki/

# Wiki skill definition
mkdir -p ~/.hermes/skills/research/llm-wiki
cp hermes-backup/wiki-skill.md ~/.hermes/skills/research/llm-wiki/SKILL.md
```

**Step 4 — Plans, memories, kanban**
```bash
cp -r hermes-backup/memories/* ~/.hermes/memories/
cp -r hermes-backup/plans/* ~/.hermes/plans/
cp hermes-backup/kanban.db ~/.hermes/
```

### Post-restore verification

```bash
# Check skills are registered
hermes skills list  # or equivalent /skills endpoint

# Verify config is valid YAML
python3 -c "import yaml; yaml.safe_load(open('~/.hermes/config.yaml'))"

# Check SOUL.md is readable
cat ~/.hermes/SOUL.md | head -5

# Check wiki index
cat /home/hermeswebui/wiki/index.md
```

### What needs the human after restore

Tell the user to reconfigure these manually (secrets were excluded):
- `.env` — API keys, provider tokens
- `auth.json` — channel credentials (re-auth each channel)
- Any cron jobs referencing secrets (check `~/.hermes/cron/`)

### Rollback plan

If something breaks, the `.git` history in the backup repo has every prior commit. To revert:
```bash
cd ~/duallogic-hermes
git log --oneline
git revert <commit-hash>
git push origin main
```

---

## Manual Restore (human-friendly)

### 1. Clone the repo

```bash
git clone https://github.com/mrtopher/duallogic-hermes.git ~/hermes-restore
cd ~/hermes-restore
```

### 2. Restore your files

```bash
# Core config
cp hermes-backup/SOUL.md ~/.hermes/
cp hermes-backup/config.yaml ~/.hermes/
cp hermes-backup/channel_directory.json ~/.hermes/

# Custom skills
cp -r hermes-backup/skills/* ~/.hermes/skills/

# LLM wiki
cp -r hermes-backup/wiki/* /home/hermeswebui/wiki/
cp hermes-backup/wiki-skill.md ~/.hermes/skills/research/llm-wiki/SKILL.md

# Plans, memories, kanban
cp -r hermes-backup/memories/* ~/.hermes/memories/
cp -r hermes-backup/plans/* ~/.hermes/plans/
cp hermes-backup/kanban.db ~/.hermes/
```

### 3. Re-enter secrets manually

| Item | Why | How |
|---|---|---|
| `.env` | Contains API keys | Recreate from `.env.example` or re-enter keys |
| `auth.json` | Channel credentials | Re-authenticate each channel |
| `state.db` | Runtime state | Regenerates automatically |

### 4. Verify

```bash
hermes restart
```

---

*Backed up automatically — do not edit manually*
