# skills

Personlige agent-skills for Aulon. Fungerer på tvers av Claude Code, OpenCode, GitHub Copilot og 50+ andre agenter via [Vercel sin skills-CLI](https://github.com/vercel-labs/skills).

## Skills

- **devops-pr-workflow** — Komplett workflow fra DevOps-ticket til pushet PR
- **aulon-tone-of-voice** — Personlig tone of voice, brukes av andre skills

## Installasjon

Krever Node.js 18+.

```bash
# Installerer alle skills globalt for Claude Code, OpenCode og GitHub Copilot
npx skills add aulonm/skills -g -a claude-code -a opencode -a github-copilot -y
```

Skills installeres som symlinker til én kanonisk kopi (`~/.skills-cache/aulonm-skills/`), så oppdateringer treffer alle agenter samtidig.

## Oppdatering

```bash
npx skills update -g -y
```

### Auto-oppdatering

For å holde skills oppdatert automatisk, legg dette i `~/.zshrc` eller `~/.bashrc`:

```bash
# Oppdaterer skills maks én gang per dag når terminal åpnes
skills_autoupdate() {
  local stamp="$HOME/.cache/skills-last-update"
  mkdir -p "$(dirname "$stamp")"
  if [[ ! -f "$stamp" ]] || [[ $(find "$stamp" -mtime +1 2>/dev/null) ]]; then
    (npx -y skills update -g -y &>/dev/null && touch "$stamp") &
  fi
}
skills_autoupdate
```

## Legge til ny skill

```bash
# Fra rot av repoet
npx skills init skills/<navn-på-skill>
```

Rediger den genererte `SKILL.md`, push til main, og kjør `npx skills update -g -y` på maskiner som skal ha den.

### Krav til SKILL.md

- YAML frontmatter med `name` og `description`
- `description` skal være presis om når skill-en skal triggere — det er det agenten ser når den vurderer hvilken skill den skal bruke
- Innhold under frontmatter er instrukser til agenten

## Struktur

```
skills/
├── README.md
├── .github/workflows/validate.yml    # Validerer SKILL.md frontmatter på push
├── .gitignore
└── skills/
    ├── devops-pr-workflow/
    │   └── SKILL.md
    └── aulon-tone-of-voice/
        └── SKILL.md
```

## Lokal testing

Hvis du vil teste endringer før push:

```bash
# Installer fra lokal sti
npx skills add ./ -g -a claude-code -y
```

## Sletting

```bash
npx skills remove --global devops-pr-workflow
# eller alle:
npx skills remove --global --all
```
