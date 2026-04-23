---
name: obsidian
description: Read, search, and create notes in the Obsidian vault.
---

# Obsidian Vault

**Location:** `/home/hancheng/Documents/ObsidianThoughts`

**Git Sync:** Vault is a git repo synced with `git@github.com-hermes:HanchengYANG/thoughts.git`. Hermes uses a dedicated SSH key (`~/.ssh/id_ed25519_hermes`, comment `hermes-agent`) so commits from Hermes vs the user are distinguishable. The SSH host alias `github.com-hermes` in `~/.ssh/config` routes through this key.

**Git workflow for Hermes:**
1. Before writing: `git pull origin main`
2. After writing: `git add -A && git commit -m "hermes: <message>" && git push origin main`
3. Always prefix commit messages with `hermes:` to distinguish from user commits.

Note: Vault paths may contain spaces - always quote them.

## Read a note

```bash
VAULT="${OBSIDIAN_VAULT_PATH:-$HOME/Documents/Obsidian Vault}"
cat "$VAULT/Note Name.md"
```

## List notes

```bash
VAULT="${OBSIDIAN_VAULT_PATH:-$HOME/Documents/Obsidian Vault}"

# All notes
find "$VAULT" -name "*.md" -type f

# In a specific folder
ls "$VAULT/Subfolder/"
```

## Search

```bash
VAULT="${OBSIDIAN_VAULT_PATH:-$HOME/Documents/Obsidian Vault}"

# By filename
find "$VAULT" -name "*.md" -iname "*keyword*"

# By content
grep -rli "keyword" "$VAULT" --include="*.md"
```

## Create a note

```bash
VAULT="${OBSIDIAN_VAULT_PATH:-$HOME/Documents/Obsidian Vault}"
cat > "$VAULT/New Note.md" << 'ENDNOTE'
# Title

Content here.
ENDNOTE
```

## Append to a note

```bash
VAULT="${OBSIDIAN_VAULT_PATH:-$HOME/Documents/Obsidian Vault}"
echo "
New content here." >> "$VAULT/Existing Note.md"
```

## Wikilinks

Obsidian links notes with `[[Note Name]]` syntax. When creating notes, use these to link related content.
