# duallogic-hermes

Hermes Agent backup — synced nightly.

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

## What's excluded (secrets)

`.env`, `auth.json`, `gateway_state.json`, `state.db*`, `cache/`, `logs/`, `audio_cache/`, `image_cache/`, `pairing/`, `bin/`, `cron/`, `hooks/`, `home/`, `webui/`, `sandboxes/`, `workspace/`

---
*Backed up automatically — do not edit manually*
