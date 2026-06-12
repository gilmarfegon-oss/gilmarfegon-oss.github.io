# Personal Assistant (Telegram) — persona & prompt-logboek

Dit bestand houdt bij hoe Gilmars persoonlijke AI-assistent in n8n is opgebouwd,
welke persoonlijkheid hij heeft en welke wijzigingen er door de tijd zijn gedaan.
Bedoeld als naslag voor mij (Claude) en voor Gilmar, zodat we op latere wijzigingen
kunnen voortbouwen zonder de context kwijt te raken.

## Waar zit het?

- **n8n-omgeving:** https://fegon.app.n8n.cloud
- **Workflow:** `Personal Assistant (Telegram)`
- **Workflow-ID:** `iMZuYThBXfBl5zS3`
- **Workflow-URL:** https://fegon.app.n8n.cloud/workflow/iMZuYThBXfBl5zS3
- **Brein:** node **`AI Assistent`** (`@n8n/n8n-nodes-langchain.agent`)
- **Persoonlijkheid / gedrag:** staat volledig in de **System Message** van die node
  (`AI Assistent` → Options → System Message).
- **Taalmodel:** node `Claude` (`lmChatAnthropic`).
- **Geheugen:** node `Gespreksgeheugen` (`memoryBufferWindow`) — geeft een gevoel
  van continuïteit binnen een gesprek.

> Het karakter van de assistent wonen we NIET in losse nodes; het zit in de
> System Message-tekst. Wil je de toon of het gedrag veranderen, pas dan die tekst aan.

## Hoe pas ik de prompt aan (voor Claude)

1. `get_workflow_details` met workflowId `iMZuYThBXfBl5zS3` (output is groot → met jq de
   `AI Assistent`-node en `.parameters.options.systemMessage` eruit halen).
2. Wijzig de tekst.
3. `update_workflow` met operatie `setNodeParameter`, node `AI Assistent`,
   path `/options/systemMessage`, value = de volledige nieuwe tekst (begint met `=`
   omdat het een n8n-expressie is i.v.m. de datum/tijd-placeholder).
4. `publish_workflow` om de wijziging live te zetten.
5. Werk dit logboek bij met datum + samenvatting van de wijziging.

## Persona — huidige uitgangspunten

- **Toon:** pittig, eigenwijs, een echte sparringpartner i.p.v. onderdanige dienaar.
  Tutoyeert Gilmar, praat als een slimme, directe collega.
- **Eigen mening:** durft ongevraagd advies, kanttekeningen en kritiek te geven,
  altijd kort en met reden. Durft tegen te spreken.
- **Humor:** droge, lichte humor mag; pittig maar nooit bot of grof, geen gevloek.
- **Empathie:** leeft kort mee bij slecht nieuws, schouderklopje bij goed nieuws,
  begroeting aangepast op het tijdstip.
- **Beknopt blijft heilig:** karakter zit in toon/woordkeuze, niet in lange teksten.
- **Verbanden leggen ("web", niet losse feiten):** beantwoordt niet alleen de
  letterlijke vraag, maar verbindt mail ↔ agenda ↔ taken ↔ reminders ↔ notities.
  Wijst op conflicten, patronen en gevolgen en sluit af met gebundelde vervolgstappen
  i.p.v. telkens één losse vraag. Selectief: alleen verbanden die er echt toe doen.

## Functionele scope (samengevat)

- **Werk = leidend** (Outlook, gilmar.korts@fegon.nl); privé = Google
  (gilmar.fegon@gmail.com), tweede Google-account Gkorts14 (gkorts14@gmail.com).
- Mail (Outlook + Gmail), agenda's (Outlook + 2× Google), taken-databank,
  reminders/wekker/bellen via Telegram, notities (databank + OneDrive),
  afbeeldingen-databank, spraaktranscriptie.

## Autonomie — huidige stand

Niveau 1 (reactief autonoom) + niveau 2 (proactief meedenken) staan AAN.
Niveau 3 ("maximaal autonoom", o.a. mail versturen zonder concept-check) is
door Gilmar **expliciet afgewezen** — nooit aanzetten zonder dat hij er
opnieuw om vraagt.

- **Zelf doen, melden achteraf:** taken aanmaken/bijwerken, reminders,
  notities/afbeeldingen opslaan, mail labelen/sorteren/verplaatsen, opzoeken.
- **Eigen-tijd-afspraken** (tijdblok, wekker) direct inplannen bij duidelijke bedoeling.
- **Harde grens, altijd eerst bevestigen:** e-mail versturen (concept eerst),
  afspraken met andere mensen (of met uitnodigingen), en alles wat verwijderen is.
  Wel maximaal voorbereiden zodat Gilmar alleen "ja" hoeft te zeggen.
- Bij twijfel: behandelen als "buiten de grens" en eerst vragen.

## Wijzigingslogboek

### 2026-06-12 (avond) — Inbox Triage: concept-antwoord bij urgente mail
Workflow **Inbox Triage (Outlook)** (`TkEuAWpSh92qTees`), urgente tak uitgebreid.
- **Gedrag:** bij categorie `Urgent` schrijft Claude nu een concept-antwoord dat als
  **Outlook-concept (draft) in de Drafts-map** wordt klaargezet. De concepttekst
  komt ook in de Telegram-alert te staan.
- **HARDE GARANTIE:** er staat **geen enkele verzend-node** in de keten. De reply
  gebruikt `message → reply` met optie `saveAsDraft: true`, dus er wordt nooit
  automatisch iets verstuurd. Gilmar beslist altijd zelf of/wanneer het eruit gaat.
- **Nieuwe nodes:** `Haal Urgente Mail` (message get, volledige body),
  `Schrijf Concept` (langchain agent) + `Claude Concept` (lmChatAnthropic,
  Claude Sonnet 4.6, temp 0.3, Anthropic-cred `qEKvcM2lIgC6MSqY`),
  `Maak Concept Antwoord` (message reply, saveAsDraft). De drie nieuwe
  bewerkingsnodes staan op `onError: continueRegularOutput`, zodat de
  urgent-alert altijd blijft afgaan, ook als concept-generatie hapert.
- **Keten urgent:** Routeer(Urgent) -> Haal Urgente Mail -> Schrijf Concept ->
  Maak Concept Antwoord -> Alert Urgent (incl. concepttekst).
- Het concept-prompt verzint geen feiten/prijzen/data: onbekende zaken worden
  `[placeholder]` zodat Gilmar ze makkelijk aanvult. Ondertekening: Gilmar Korts,
  Fegon Waterbehandeling.
- Gepubliceerd: activeVersionId `49c3db74-17c8-4035-b20e-928a25c8bb38`.

### 2026-06-12 (avond) — Autonomie niveau 1+2 + tool "Outlook Mail Ophalen"
- **Toegevoegd:** sectie `AUTONOMIE & GRENZEN` in de system prompt: laag-risico
  acties (taken, reminders, notities, mail sorteren) doet de assistent nu zelf
  met melding achteraf; e-mail versturen, afspraken met anderen en verwijderen
  blijven achter een verplichte bevestiging. Niveau 3 expliciet afgewezen.
- **Aangepast:** `TAKEN`-sectie — acties/verplichtingen worden direct als taak
  aangemaakt i.p.v. voorgesteld; `VERBANDEN` — stelt bij krappe deadlines
  concreet een tijdslot voor.
- **Nieuwe tool-node:** `Outlook Mail Ophalen` (`microsoftOutlookTool` v2,
  resource `message`, operation `get`, output `fields` incl. `body`).
  Haalt één mail volledig op via message-id, inclusief complete HTML-body met
  links — lost het probleem op dat zoekresultaten alleen een afgekapte
  `bodyPreview` geven. Credential: `Microsoft Outlook account`
  (`c1kbC5Q7s4eQJhRW`), gekoppeld aan `AI Assistent` via `ai_tool`.
  De prompt-sectie `WERK MAIL` beschrijft wanneer de tool gebruikt wordt en
  dat links als kale URL teruggegeven worden.
- Gepubliceerd: activeVersionId `f82f614d-f718-40ce-ab4c-1f3090f8d2c1`.

### 2026-06-12 — Persoonlijkheid + verbanden toegevoegd
- **Toegevoegd:** sectie `PERSOONLIJKHEID & TOON` (pittig & eigenwijs, sparringpartner,
  droge humor, empathie, beknopt).
- **Toegevoegd:** sectie `VERBANDEN & SAMENHANG` — assistent legt actief verbanden
  tussen mail, agenda, taken, reminders en notities; wijst op patronen/gevolgen en
  sluit af met gebundelde vervolgstappen i.p.v. één losse vraag.
- **Uitgebreid:** regel 6 onder `REGELS` noemt nu ook samenhangende mail/taken bij
  proactiviteit.
- Wijziging doorgevoerd via `setNodeParameter` op `/options/systemMessage` en
  ge-`publish`-t (activeVersionId `c48c250c-ca94-4e87-a37c-5acb742580b1`).
- **Open punt:** n8n gaf pre-existing validatiewaarschuwingen op losse nodes
  (`Gmail Verstuur Mail`, `Gmail Labels Ophalen`, `Google Agenda Aanmaken`,
  `Stuur antwoord`, `Google Agenda Gkorts14 Aanmaken`) — niet veroorzaakt door deze
  wijziging; nog op te ruimen indien gewenst.

## Ideeën voor toekomstige wijzigingen

- Emoji-gebruik fijn-tunen (nu spaarzaam toegestaan; eventueel expliciet aan/uit).
- Persona-intensiteit kunnen schakelen (bv. "zakelijke modus" tijdens werktijd).
- Langetermijngeheugen/voorkeuren persistent opslaan (databank) i.p.v. alleen
  het gespreksgeheugen-venster.
- De pre-existing node-validatiewaarschuwingen opschonen.
