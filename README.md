# Live parkeerhulp Rotterdam

Statisch parkeerdashboard voor vier P+R-locaties en zes centrumgarages rond Delftseplein 17.

## Website publiceren

Ga naar **Settings → Pages** en kies:

- Source: **Deploy from a branch**
- Branch: **main**
- Folder: **/ (root)**
- Klik **Save**.

Na de eerste geslaagde publicatie is de website bereikbaar op https://madneo.github.io/Live-parkeerhulp-rotterdam/.

## Werking

`index.html` bevat de volledige website. Geen installatie, API-sleutel of build nodig. Open de website met internettoegang. De gemeentelijke feed wordt iedere 60 seconden opgehaald zolang de pagina zichtbaar is.

Bron: https://kaartlaag.rotterdam.nl/api/maps/38.json

Livekoppeling: P+R Meijersplein, Blijdorp, Alexander, Kralingse Zoom en Schouwburgplein I. Ontbrekende tellingen blijven onbekend; bij fouten worden eerdere tellingen niet als actueel getoond. De bron vermeldt geen meettijdstip. Ontvangsttijd is geen meettijd.

De overige garages tonen beschikbare statische capaciteit en bronlinks. Q-Park Weena heeft een afgeschermde RDW-feed. Voor de andere commerciële garages is geen openbare numerieke telling gevonden.

Schouwburgplein I is aangenomen bij het aangeleverde adres Schouwburgplein 2; de gemeente noemt Schouwburgplein 22. Central Plaza vermeldt bij de exploitant Kruisstraat 13 in plaats van het aangeleverde nummer 17.

Capaciteiten uit de livefeed kunnen afwijken van fysieke capaciteit op informatiepagina’s. Vrij en capaciteit worden voor de meters steeds uit dezelfde feed gebruikt.
