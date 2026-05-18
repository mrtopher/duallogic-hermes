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

## Restore Instructions

### 1. Clone the repo

```bash
git clone https://github.com/mrtopher/duallogic-hermes.git ~/hermes-restore
cd ~/hermes-restore
```

### 2. Identify what's yours vs. Hermes defaults

```bash
# Files that go back to ~/.hermes/
cp hermes-backup/SOUL.md ~/.hermes/
cp hermes-backup/config.yaml ~/.hermes/
cp hermes-backup/channel_directory.json ~/.hermes/
cp hermes-backup/kanban.db ~/.hermes/

# Optional: memories, plans, kanban state
cp -r hermes-backup/memories/* ~/.hermes/memories/
cp -r hermes-backup/plans/* ~/.hermes/plans/

# Custom skills (skill_manage will find them)
cp -r hermes-backup/skills/* ~/.hermes/skills/

# LLM wiki
cp -r hermes-backup/wiki/* /home/hermeswebui/wiki/
cp hermes-backup/wiki-skill.md ~/.hermes/skills/research/llm-wiki/SKILL.md
```

### 3. What you'll need to reconfigure manually

| Item | Why | How |
|---|---|---|
| `.env` | Contains API keys, secrets | Recreate from `.env.example` or re-enter keys |
| `auth.json` | Channel credentials | Re-authenticate channels |
| `state.db` | Runtime state | Regenerates automatically |

### 4. Verify

```bash
# Check skills are visible
hermes skills list | grep -i wiki

# Restart the agent
hermes restart
```

---

*Backed up automatically — do not edit manually*
