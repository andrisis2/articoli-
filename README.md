# Articoli — l'app

PWA per scrivere articoli: sfoglia a tendine le cartelle del repo **privato**
`andrisis2/prep`, apre i file, li mostra in markdown (e le pagine HTML come
pagine) e li lascia modificare.
Ogni salvataggio è un commit vero su GitHub, così Claude ritrova le modifiche.

- Live: https://andrisis2.github.io/articoli-/
- Contenuti: repo `andrisis2/prep` (privato) — **qui non c'è nessun testo degli articoli**

## Perché è un repo a sé

Serve un sito su HTTPS per installare la PWA sul telefono, e Pages pubblica solo
da repo pubblici. `prep` è privato, quindi qui vive solo l'interfaccia — che non
contiene segreti — mentre i contenuti restano privati. Prima l'app stava dentro
il repo `russo` (a `https://andrisis2.github.io/russo/articoli/`), che però è il
progetto per imparare il russo e non c'entra niente: ora ha casa propria.

I percorsi dell'app sono tutti relativi, quindi funziona da qualsiasi
sottocartella: se il repo cambia nome, cambia solo l'indirizzo. Attenzione
però: il trattino finale in `articoli-` è voluto, non è un refuso. Rinominare
il repo cambierebbe l'indirizzo dell'app, che andrebbe reinstallata sul
telefono: non «correggerlo».

## File

- `index.html` — tutta l'app (interfaccia + logica, nessuna dipendenza esterna)
- `manifest.json`, `sw.js` — installazione sul telefono e guscio offline
- `icon.png`, `icon-192.png` — icona

## Come parla con GitHub

API `contents` e `git/trees`, autenticata con un token personale che l'utente
incolla una volta nelle impostazioni dell'app. Il token sta **solo** nel
`localStorage` del suo telefono: non è nel codice e non passa da nessun server.

Il service worker tiene in cache solo il guscio e lascia passare tutto ciò che
va verso `api.github.com`: intercettarlo romperebbe autenticazione e salvataggi.

## Le pagine HTML (il grafico, la mappa)

In `prep` alcune cose non sono testo ma pagine: il grafico sull'utilizzo del
robotaxi, la mappa normativa. Sotto **«Leggi»** l'app le mostra **come pagine**,
non come codice — è così che si guardano dal telefono. Il sorgente resta sotto
«Modifica», e resta modificabile come qualsiasi altro file.

⚠️ **L'iframe è in sandbox senza `allow-same-origin`, e le due cose non vanno
rimesse insieme.** Così la pagina gira su un'origine opaca e non può leggere il
`localStorage` dell'app, dove sta il token di GitHub. Con
`allow-scripts allow-same-origin` insieme la pagina potrebbe togliersi la
sandbox da sola e arrivare al token.

La sandbox ha anche `allow-popups` e `allow-popups-to-escape-sandbox`: servono
perché i link di una pagina (la bozza dell'articolo rimanda alla mappa e al
modello completo) si aprano in una scheda nuova. La scheda nuova è un sito a
sé, e la pagina dentro l'app resta sull'origine opaca: il token non si vede.

In `prep` le bozze interattive dell'articolo (`bozze/bozza-NN.html`) si aprono
allo stesso modo: si tocca il file e si legge.

Per lo stesso motivo non c'è un tasto «apri a schermo intero»: servirebbe un
indirizzo `blob:`, che erediterebbe l'origine dell'app e quindi vedrebbe il
token. Per aprire una pagina fuori dall'app si usa il suo link pubblico.

## Pubblicazione

GitHub Pages, dal branch `main`, cartella radice (`/`).
