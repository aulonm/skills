---
name: devops-pr-workflow
description: Aulon's komplette workflow for oppgaver fra Azure DevOps til pushet PR. Bruk denne ALLTID når du jobber med en DevOps-ticket (AB#nummer eller lenke til dev.azure.com), når du skal lage en branch/commit/PR i et OBOS-repo, eller når du skal oppdatere et work item i DevOps. Dekker hele flyten: lese ticket, plan, branching (krever godkjenning), commits, draft PR via GitHub MCP, oppdatering av work item til "Fagfellevurdering". Bruker `aulon-tone-of-voice`-skillen for all tekst som blir postet under Aulons navn — les den skillen samtidig som denne.
---

# DevOps + PR Workflow

Komplett oppgaveflyt fra DevOps-ticket til pushet PR. Følg stegene i rekkefølge.

## Forutsetninger

- DevOps-prosjektet er ALLTID "Content Hub"
- Aulons DevOps user ID: `2db32ce1-5f0f-4384-8608-b696c83c1c4b`
- All tekst som blir postet under Aulons navn (PR-titler, beskrivelser, commits, DevOps-kommentarer, Slack) skal følge `aulon-tone-of-voice`-skillen. Les den før du skriver slik tekst.

## Steg 1 — Motta oppgave

Input kan være:
- En DevOps-ticket-lenke
- Et Work Item ID (`AB#<id>` eller bare `<id>`)
- En vanlig prompt uten ticket

Hvis ticket er gitt: les den via Azure DevOps MCP først for å forstå kontekst.

## Steg 2 — Analyser og planlegg

1. Pull seneste endringer og checkout main (hvis ikke allerede oppdatert og på main).
2. Forstå problemet, lag en plan, foreslå eller draft endringer.
3. Hvis DevOps-ticket finnes:
   - Sett work item status til **"In Progress"**
   - Tildel work item til Aulon Mujaj (ID: `2db32ce1-5f0f-4384-8608-b696c83c1c4b`)

## Steg 3 — Implementer og verifiser

- Gjør endringene.
- Legg til tester der det er mulig, kjør dem.
- For UI-endringer: be Aulon verifisere på localhost før du går videre. Ikke fortsett før det er bekreftet.

## Steg 4 — Lag branch (krever eksplisitt godkjenning)

Generer branch-navn på **engelsk**:

- Format: `<type>/<short-description>`
- Lowercase, kun bindestrek, maks 5 ord etter type-prefikset
- Gyldige typer: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`, `style`
- Eksempler: `feat/add-email-validation`, `fix/empty-api-response-crash`

**Godkjenningsflyt:**

1. Presenter foreslått branch-navn.
2. Vent på respons — Aulon kan godkjenne eller foreslå et annet navn.
3. Først etter godkjenning:
   - Stage relevante endringer og lag én commit med en beskrivende melding.
   - Lag branchen med `git checkout -b <name>`.
   - Push med `git push -u origin <name>`.

## Steg 5 — Lag PR via GitHub MCP

Bruk `mcp__github__create_pull_request`. Start ALLTID som **draft**.

**Formatering:** Når du sender tekst til MCP-verktøy (GitHub, DevOps, etc.), bruk aldri `\n` escape-sekvenser — de blir rendret bokstavelig. Bruk ekte linjeskift i parameter-verdien, eller hold teksten på én linje med naturlig punktuasjon (f.eks. kolon før lenke i stedet for tom linje).

Skriv tittel og beskrivelse på **norsk (bokmål)**, etter `aulon-tone-of-voice`. Tekniske termer som _border_, _commit_, _branch_ etc. oversettes ikke.

### Tittel
Kort, beskrivende, imperativ form, ~60 tegn. Ikke bruk prefiks som `feat:` eller `fix:` med mindre repoet bruker conventional commits konsekvent. Bruk konvensjonene fra `aulon-tone-of-voice`: lowercase OK, "funker" ikke "fungerer", scope-prefiks (`medlemsbonus:`, `sanity:`) når nyttig.

### Body

```markdown
### Oppsummering

2-4 setninger som forklarer **hvorfor** endringen ble gjort og **hva** den løser.
Hvis et ticketnummer finnes, lenk til det: `Løser AB#<nummer>`.

### Endringer

- Punktliste over de viktigste tekniske endringene.
- Vær spesifikk: navngi filer, funksjoner, komponenter der relevant.
- Grupper relaterte endringer under en overskrift hvis det er mange.

### Gjenstår

- Kun hvis det er gjenstående arbeid, TODO-kommentarer, eller kjente begrensninger.
- Hvis ingenting gjenstår, **utelat hele seksjonen**.

---

🤖 _Laget med Claude Code og kvalitetssikret av Aulon. Deler av teksten kan være skrevet eller redigert personlig av Aulon._
```

**Hvordan skrive innenfor malen:** Bruk `aulon-tone-of-voice` i hver seksjon. "Oppsummering" skal høres ut som Aulon som forklarer endringen til en kollega — pro-drop, casual, ingen "Denne pull requesten introduserer…". Bullets i "Endringer" bruker lowercase fragmenter, mix case er fint, foretrekk "funker" / "sånn at" / "litt" / "feks" naturlig.

**AI-disclosure-footer:** Legg ALLTID på footer-linja nederst i PR-body, eksakt sånn som i malen over (med `---` skille foran). Den skal stå selv om Aulon har redigert teksten manuelt — poenget er åpenhet om at AI har vært involvert, ikke at alt er AI-generert. Ikke endre formuleringen uten eksplisitt beskjed.

### Etter at draft PR er laget

1. Send Aulon PR-lenken og be om feedback.
2. Hvis godkjent: konverter fra draft til vanlig PR via `mcp__github__update_pull_request`.
3. Hvis endringer forespurt: gjør dem, push, vent på ny godkjenning. Husk å oppdatere PR-beskrivelsen så den reflekterer siste endringer.

## Steg 6 — Oppdater DevOps når PR er klar for review

Etter at PR-en er konvertert fra draft til vanlig (dvs. Aulon har godkjent):

1. Sett work item status til **"Fagfellevurdering"**
2. Legg til en kommentar på work item (på norsk, etter `aulon-tone-of-voice`) med:
   - 1-2 setninger om hva PR-en gjør
   - En klikkbar lenke til PR-en (markdown link-format, ikke ren tekst)

Bruk `mcp__devops__wit_update_work_item` og `mcp__devops__wit_add_work_item_comment`.

## Azure DevOps-formatering

Bruk alltid **Markdown** (ikke HTML) for tekstfelter i DevOps work items (descriptions, comments, acceptance criteria). Sett `format: "Markdown"` på alle rich-text-felt.

## Generelle prinsipper

- All PR-beskrivelse, DevOps-kommentar og brukerrettet tekst skrives på **norsk (bokmål)** etter `aulon-tone-of-voice`.
- Branch-navn og kode er alltid på **engelsk**.
- Teknisk og kortfattet tone — men i Aulons stemme (se `aulon-tone-of-voice`), ikke formell.
- AI-disclosure-footer skal alltid stå nederst i PR-body (se Steg 5 → Body-malen).
- Ikke gå til neste workflow-steg uten eksplisitt godkjenning (gjelder branch-navn og PR-beskrivelse).
- Hvis kontekst er utilstrekkelig: gjør kvalifiserte antakelser og marker dem med *(antatt)*.
- Let etter ticket-nummer (`AB#1234`, `#1234`, `1234`) i commit-meldinger, branch-navn og samtalehistorikk.
