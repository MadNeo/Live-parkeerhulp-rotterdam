# Prompt — Live parkeerhulp Rotterdam

Onderstaande prompt is een uitgewerkte, herbruikbare versie van de opdracht waarmee dit dashboard is gemaakt, aangevuld met de gevonden bronnen en gemaakte ontwerpkeuzes. Het is geen letterlijk transcript. Bronbevindingen dateren van 6 november 2026; controleer ze opnieuw bij verdere ontwikkeling.

---

Ontwerp en bouw een complete Nederlandstalige website **Parkeerwijzer Rotterdam**. De bezoeker moet in één oogopslag zien hoeveel parkeerplaatsen in totaal beschikbaar zijn en hoeveel daarvan momenteel vrij zijn. De bestemming is **Delftseplein 17, Rotterdam**.

## Locaties

Neem precies deze vier P+R-locaties en zes centrumgarages op:

| Type | Locatie | Aangeleverd adres | Looptijd naar bestemming |
| --- | --- | --- | --- |
| P+R | Meijersplein | Fairoaksbaan 300 | Niet opgegeven |
| P+R | Blijdorp | Blijdorplaan 5 | Niet opgegeven |
| P+R | Alexander | Prins Alexanderlaan 35 | Niet opgegeven |
| P+R | Kralingse Zoom | Kralingse Zoom 70 | Niet opgegeven |
| Garage | ParkBee Holiday Inn Express | Delftsestraat 26 | circa 4 minuten |
| Garage | Interparking Central Plaza | Kruisstraat 17 | circa 6 minuten |
| Garage | Q-Park Weena | Karel Doormanstraat 10 | circa 5 minuten |
| Garage | Interparking Kruiskade | Kruiskade 21 | circa 7 minuten |
| Garage | Parkeergarage Schouwburgplein | Schouwburgplein 2 | circa 8 minuten |
| Garage | Groot Handelsgebouw P1 | Conradstraat 52 | circa 7 minuten |

De looptijden zijn indicaties uit het aangeleverde overzicht, geen live routeberekeningen. Straatparkeren is geen afzonderlijke garage en krijgt geen fictieve telling. Neem oude tarieven uit de afbeelding niet als actuele tarieven over.

## Livegegevens en bronnen

Gebruik de gemeentelijke parkeerkaart als primaire bron:

https://kaartlaag.rotterdam.nl/parkeren#51.9097/4.3782/51.9553/4.5430/brt/15597//

De openbare GeoJSON-feed die tijdens de ontwikkeling is gevonden:

https://kaartlaag.rotterdam.nl/api/maps/38.json

Lees uit `features[].properties` de locatie-ID en de metavelden:

- `vacant-spaces`: vrije plaatsen.
- `parking-capacity`: capaciteit volgens de feed.

Koppel locaties op hun ID, niet alleen op een gedeeltelijke naam. Zo worden bijvoorbeeld fietsenstallingen en laadpalen met vergelijkbare namen uitgesloten.

| Locatie | Gemeentelijke feature-ID |
| --- | --- |
| P+R Meijersplein | 5834808 |
| P+R Blijdorp | 10704251 |
| P+R Alexander | 5834811 |
| P+R Kralingse Zoom | 5834826 |
| Schouwburgplein I | 5904582 |

Onderzoek voor de commerciële garages de officiële website en eventuele openbare bezettingsfeed. Gebruik alleen aantoonbare numerieke tellingen. Een reserveringsknop, de tekst ‘beschikbaar’ of een beschikbare tijdslotboeking is geen totaal aantal vrije plaatsen.

### Vastgestelde bronnen en beperkingen

| Garage | Website | Bevinding bij ontwikkeling |
| --- | --- | --- |
| ParkBee Holiday Inn Express | https://parkbee.com/nl/city/rotterdam/poi/holiday-inn-express-rotterdam | Geen openbare numerieke live-telling gevonden; dit is een pagina over parkeren nabij het hotel. |
| Interparking Central Plaza | https://www.interparking.nl/nl/parkeren/rotterdam/central-plaza/ | Gepubliceerde capaciteit: 470; geen openbare numerieke live-telling gevonden. |
| Q-Park Weena | https://www.q-park.nl/nl-nl/parkeren/rotterdam/weena/ | Gepubliceerde capaciteit: 480; de gevonden RDW-livefeed vereist authenticatie (HTTP 401). |
| Interparking Kruiskade | https://www.interparking.nl/nl/parkeren/rotterdam/kruiskade/ | Gepubliceerde capaciteit: 269; geen openbare numerieke live-telling gevonden. |
| Schouwburgplein I | https://www.rotterdam.nl/parkeergarage-schouwburgplein-1 | Vrije plaatsen en capaciteit beschikbaar via de gemeentelijke kaartfeed. |
| Groot Handelsgebouw P1 | https://ghg.nl/ | Geen openbare afzonderlijke P1-telling of bevestigde P1-capaciteit gevonden. Gebruik geen capaciteit van het hele complex als P1-capaciteit. |

Toon statische capaciteit als statisch, met bronvermelding. Houd ontbrekende gegevens onbekend; maak nooit voorbeeldwaarden die live lijken.

### Adresverschillen

- Central Plaza: de exploitant vermeldt **Kruisstraat 13**, terwijl het aangeleverde overzicht nummer 17 noemt. Gebruik het exploitantadres voor de route en vermeld het verschil.
- Schouwburgplein: voor het dashboard is **garage I** aangenomen. De gemeente vermeldt **Schouwburgplein 22**, terwijl het aangeleverde overzicht nummer 2 noemt. Maak deze aanname zichtbaar en voeg een bronlink toe.
- Capaciteit op een algemene informatiepagina kan afwijken van de capaciteit van de livefeed. Gebruik voor iedere meter capaciteit én vrije plaatsen uit dezelfde feed; presenteer die capaciteit als capaciteit volgens de feed.

## Functionele eisen

1. Toon per locatie een kaart met naam, adres, type, bronstatus, vrije plaatsen, capaciteit en een meter.
2. Bereken het vrije percentage als `vrij / capaciteit × 100`. Toon `capaciteit − vrij` als **niet vrij**: dit hoeft niet uitsluitend fysieke bezetting te betekenen.
3. Gebruik halve cirkelmeters: groen boven 20% vrij, oranje boven 5% tot en met 20%, rood tot en met 5%. Ontbrekende tellingen krijgen een neutrale meter en een streepje.
4. Maak een samenvatting met vrije plaatsen op P+R, vrije plaatsen in centrumgarages en het aantal aangesloten livebronnen. Vermeld hoeveel locaties in elke som zijn meegenomen, zodat een gedeeltelijk totaal niet als volledig totaal wordt gelezen.
5. Haal gegevens direct bij openen en vervolgens elke 60 seconden op zolang de pagina zichtbaar is. Vernieuw ook bij terugkeer naar de pagina en via een handmatige knop.
6. Voorkom overlappende aanvragen en gebruik een timeout. Toon laadstatus en ontvangsttijd in de tijdzone Europe/Amsterdam.
7. De gemeentelijke bron vermeldt geen meettijdstip. Maak expliciet dat ontvangsttijd niet hetzelfde is als meettijd en dat de telling geen garantie bij aankomst biedt.
8. Valideer getallen: nul vrije plaatsen is geldig; ontbrekende waarden worden nooit nul. Negatieve waarden, ongeldige waarden en vrije aantallen groter dan capaciteit mogen niet als geldige telling verschijnen. Bereken geen percentage bij nul of onbekende capaciteit.
9. Bij verbindingsfouten of verouderde ontvangst mogen eerdere tellingen niet als actueel worden getoond. Toon een duidelijke melding en laat opnieuw proberen toe. Gebruik voor de ontvangst een grens van 150 seconden; dit bewijst niet de actualiteit van de onderliggende meting.
10. Voeg zoeken op naam/adres, filters Alles/P+R/Garages en sortering op standaardvolgorde, meeste vrije plaatsen en naam toe. Zet onbekende aantallen onderaan bij sortering op vrije plaatsen.
11. Voeg per locatie een bronlink en Google Maps-routeknop toe. Open externe links in een nieuw tabblad met `rel="noopener"`.
12. Voeg een uitlegsectie toe over bronnen, meters, ontbrekende tellingen, adresverschillen en looptijden.

## Vormgeving

Maak een rustige, verzorgde website die zowel op desktop als mobiel prettig werkt:

- Gebroken witte achtergrond, donkergroene tekst en lichtgroene accenten.
- Typografie met Manrope voor koppen en DM Sans voor tekst, met bruikbare systeemfont-fallbacks.
- Compact P-logo met richtingpijl en de naam ‘parkeerwijzer ROTTERDAM’.
- Korte kop **‘Vrije parkeerplaatsen’** met compacte bestemmingslink. Geen groot introductieblok; de tellingen moeten direct zichtbaar zijn.
- Bestemmingsblok voor Delftseplein 17.
- Donkergroene samenvattingsbalk met goed leesbare cijfers.
- Witte locatiekaarten met subtiele randen, afgeronde hoeken en beperkte tussenruimte, zonder de leesbaarheid te verliezen.
- Responsief raster: één kolom op mobiel, twee op middelgrote schermen en drie op kleinere desktops en vijf vanaf 1280 px. Toon standaard de vijf livekoppelingen eerst.
- Duidelijke focusstijlen, gelabelde bediening en tekstuele status naast kleuren. Meters moeten ook zonder kleur begrijpelijk zijn.
- Geen decoratieve foto's nodig; de tellingen en meters staan centraal.

## Techniek en oplevering

Lever één zelfstandig `index.html` met ingesloten CSS en JavaScript, zonder backend, buildstap of geheime API-sleutels. De gemeentelijke feed ondersteunde bij ontwikkeling cross-origin-verzoeken; controleer dit bij ingebruikname. De website moet op GitHub Pages kunnen draaien onder een repository-subpad en mag niet afhankelijk zijn van een lokale server.

Doelrepository: https://github.com/MadNeo/Live-parkeerhulp-rotterdam

Voeg `.nojekyll` en een Nederlandstalige `README.md` toe. Documenteer GitHub Pages: Deploy from a branch → main → / (root). Publiceer alleen projectbestanden; geen credentials of lokale configuratie.

Controleer voor oplevering de mapping van de vijf livebronnen, nulwaarden, ontbrekende capaciteit, ongeldige tellingen, foutafhandeling, zoeken, sorteren, filters en mobiele weergave. Meld eerlijk welke controles niet konden worden uitgevoerd. Behoud bij wijzigingen bestaande functionaliteit en documenteer nieuwe bronbeperkingen.


## Aanvulling: tekens en compacte weergave

Bewaar tekst zonder verlies van tekens. Gebruik voor pictogrammen SVG of HTML-entiteiten en controleer op vervangtekens (U+FFFD) en ongewenste vraagtekens in labels. Gebruik geen onbedoelde systeemencoding bij het lezen of uploaden. Houd de bovenkant compact: navigatie circa 64 px, korte titelstrook en een compacte samenvatting. De vijf livekaarten horen op een breed desktopscherm direct in beeld te komen.
