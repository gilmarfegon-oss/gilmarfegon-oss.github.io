# n8n handoff — Persoonlijke AI-assistent

Voorbereide wijzigingen voor de n8n-workflow **Persoonlijke AI-assistent**
(`workflowId: iMZuYThBXfBl5zS3`, project Gilmar Korts `zQfXlblDHPMjBhBK`).

Deze konden in de oorspronkelijke sessie niet worden toegepast omdat de n8n
MCP-calls per stuk goedkeuring vroegen die in die sessie niet gegeven kon
worden. `.claude/settings.json` zet de n8n-tools nu op de allowlist, dus in een
**verse sessie** (die de settings bij het opstarten leest) werkt het wel.

## Al gedaan
- Kolom `details` (type string) toegevoegd aan de data table `taken`
  (`dataTableId: lqrTkEMFixJDZcJp`). NIET opnieuw doen.

## Nog toe te passen
Lees `update-operations.json` en geef het door aan `mcp__n8n__update_workflow`
(`workflowId` + `operations`). Daarna `mcp__n8n__publish_workflow` met
`workflowId: iMZuYThBXfBl5zS3`.

De 7 operaties:
1. Nieuwe tool-node **"Web Onderzoek (Gemini)"** (`googleGeminiTool` v1.2,
   model `models/gemini-2.5-flash`, credential `g8npIawnioIiMe1j`) met Google
   Search + URL-context aan.
2. Connection `Web Onderzoek (Gemini)` → `AI Assistent` (type `ai_tool`).
3+4. `Taak Toevoegen`: `details` toegevoegd aan column-mapping + schema.
5+6. `Taak Bijwerken`: `details` toegevoegd aan column-mapping + schema.
7. `AI Assistent` systeemprompt: details-gebruik bij taken, sectie
   WEB & ONDERZOEK (rapport → standaard als notitie, tag `research`), en de
   datum/tijd-regel verplaatst naar het EINDE (caching-prefix stabiel houden).

## Let op — caching
De n8n **Anthropic Chat Model-node (v1.5) heeft geen caching-instelling**
(`cache_control` is niet exposed). De datum/tijd-verplaatsing is goede praktijk
en helpt als n8n/LangChain de systeemprompt stilletjes cachet, maar de besparing
is niet hard te garanderen vanuit de node-config.

## Verificatie achteraf
- Na apply: controleer dat de node bestaat en gekoppeld is, en dat publish lukte.
- Verwijder daarna deze map `.claude/n8n-handoff/` gerust.
