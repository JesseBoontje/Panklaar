# Koppeling Panklaar en Frits (voorstel, 14 november 2026)

Status: voorstel, nog niet besloten en nog niets gebouwd. Plaatjes: https://claude.ai/artifact/GcBVdbEcUTraYtfzxd6vZE

## Wat Panklaar nu is
- PWA in vanilla JS (`app.js`, `index.html`), gebouwd met `scripts/build.mjs` tot één Cloudflare Worker die alleen de appbestanden uitdeelt (`worker.mjs`, alleen GET/HEAD).
- Alle gegevens staan versleuteld in IndexedDB op het toestel (AES-256-GCM, sleutel uit het wachtwoord). Geen serveropslag, geen account, geen synchronisatie. De gebruikersnaam is lokaal.
- AI met eigen sleutel (Claude of OpenAI), alleen in het geheugen van de sessie.
- CSP `connect-src`: alleen `'self'`, api.openai.com en api.anthropic.com.
- Adres volgens README: https://panklaar.zsj4j2s4df.workers.dev/. Niet te bereiken vanuit de cloudsessie en niet te zien in de drie Cloudflare-accounts van de sessiesleutel.
- Gevolg: Frits kan Panklaar niets vragen. Panklaar moet zelf naar Frits sturen.

## Drie manieren
**A. Deelknop.** Panklaar deelt ingrediënten als tekst (`navigator.share`), de iPhone-opdracht "Naar Frits" (B7) stuurt ze naar `POST /api/inworp`. Frits: niets extra. Nadeel: AI per keer, `restricted`, geen automatisch weekmenu.

**B. Panklaar stuurt rechtstreeks (aanbevolen).**
- Panklaar: Frits-sleutel onder Jij, versleuteld in de kluis. Knop "Naar Frits" bij recept en lijst. Weekplanner stuurt bij wijzigen het menu mee, met per ingrediënt of het volgens de voorraad in huis is. `connect-src` krijgt `https://frits.djez.app` erbij.
- Frits: sleutel-soort "panklaar" in `api_tokens` (B7) die alleen twee dingen mag: boodschappen toevoegen en maaltijden doorgeven. Vaste ingang zonder AI, bijvoorbeeld `POST /api/koppeling/panklaar` met gestructureerde JSON, CORS alleen voor het Panklaar-adres, Bearer-sleutel (geen koekje). Een maaltijdregel op Vandaag en een voorstel-knop "ontbrekende op de lijst". Alles als pakket met kanaal `panklaar`, dus terug te draaien.
- Regels: het menu is informatie; ontbrekende ingrediënten zijn een voorstel met knop. Alleen een eigen tik in Panklaar zet direct iets op de lijst. De sleutel kan niets lezen.
- Na B4 en B7. Geen AI-kosten.

**C. Panklaar krijgt een server.** Worker + D1 + inloggen, recepten verhuizen. Dan kan Frits lezen, en kan Panklaar later een MCP-koppeling krijgen voor Claude ("plan de week uit mijn recepten en zet het in Frits"). MCP is voor Claude, niet voor app-naar-app. Groot werk; "alles alleen op je telefoon" verdwijnt.

## Moderniseren
Voor B niet nodig. Klein en handig: verhuizen naar panklaar.djez.app in het account van Frits. Gegevens gaan dan niet vanzelf mee (per adres opgeslagen): back-up maken en terugzetten. C pas als huisgenoten samen recepten willen of er op meer toestellen gekookt wordt.

## Open vragen voor Jesse
1. Wie gebruikt Panklaar, op hoeveel toestellen?
2. Welk adres en welk Cloudflare-account?
3. Blijft de boodschappenlijst in Panklaar na de koppeling?
4. Waar wordt het eten gepland: Panklaar of Frits?
