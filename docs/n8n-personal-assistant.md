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

## Wijzigingslogboek

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
