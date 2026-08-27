# VORNEAUX — sito

Sito statico a singolo file HTML (`index.html`), pensato per essere pubblicato
su **GitHub Pages** direttamente da questo repository, senza build step, senza
framework, senza dipendenze da installare.

## Struttura del repository

```
index.html              → il sito (struttura, stili, motore 3D, player audio)
data/
  books.json             → catalogo Editions (libri)
  signals.json            → catalogo Signals (brani musicali)
  notes.json               → catalogo Field Notes (blog)
assets/
  books/
    covers/                → immagini di copertina reali dei libri
    models/                 → riservata a futuri modelli 3D (.glb/.gltf)
  audio/
    tracks/                 → file audio reali dei brani (.mp3)
    covers/                  → riservata a eventuali copertine/artwork dei brani
    sfx/                      → riservata a eventuali effetti sonori dell'interfaccia
  notes/
    images/                 → riservata a immagini per i futuri articoli del blog
  products/                → riservata a immagini dei prodotti digitali (FLEO, OMNIMIND, VELLUM...)
  brand/                   → riservata a logo, favicon e altri asset di brand condivisi
```

Il sito legge automaticamente i tre file in `data/` a ogni caricamento della
pagina. Le cartelle in `assets/` contengono i file reali (immagini, audio)
a cui quei file JSON fanno riferimento.

**Nota su cartelle vuote:** git non traccia le cartelle vuote, quindi ognuna
contiene un file segnaposto `.gitkeep`. Puoi cancellarlo quando aggiungi il
primo file reale in quella cartella (non è obbligatorio, ma tiene il repo
più pulito).

## Come aggiungere un nuovo libro

1. Metti la copertina in `assets/books/covers/` (consigliato: JPG o WebP,
   proporzioni 2:3, es. 1000×1500px, sotto i 250–300KB).
2. Aggiungi una voce a `data/books.json`:

```json
{
  "id": "nome-libro",
  "title": "Titolo del libro",
  "subtitle": "Sottotitolo breve",
  "edition": "EDITION 03",
  "description": "Una o due frasi di descrizione, mostrate nella scheda del libro.",
  "cover": "assets/books/covers/nome-file.jpg",
  "model": "",
  "cta": { "label": "View on Amazon", "href": "https://..." }
}
```

Non serve toccare altro: il carosello 3D, le frecce, i puntini di
navigazione e il testo si aggiornano da soli in base a quante voci ci sono
in questo file. Se `cover` è vuoto o il file non si trova, il libro mostra
automaticamente una copertina generata come segnaposto (nessun errore
visibile per l'utente).

Il campo `model` è riservato per il futuro, se vorrai sostituire il libro
3D generato via codice con un vero modello tridimensionale (formato
`.glb`/`.gltf`) delle tue copertine reali — oggi non è ancora collegato a
nulla, va lasciato vuoto.

## Come aggiungere un nuovo brano

1. Metti il file audio in `assets/audio/tracks/` (consigliato: MP3,
   128–192kbps, che tiene i file leggeri senza perdita percepibile sul web).
2. Aggiungi una voce a `data/signals.json`:

```json
{
  "id": "nome-brano",
  "title": "Titolo del brano",
  "tag": "Signal 004 · 2026",
  "duration": "3:42",
  "src": "assets/audio/tracks/nome-file.mp3"
}
```

Il player, la lista, il dock in basso e le barre reattive del
visualizzatore leggono tutto da qui — nessun'altra modifica necessaria.
Se il file audio manca o non si carica, il sito passa automaticamente a un
suono generato di riserva, così l'esperienza non si rompe mai.

## Come aggiungere un nuovo articolo (Field Notes / blog)

Aggiungi una voce a `data/notes.json`:

```json
{
  "id": "titolo-articolo",
  "tag": "Field Notes",
  "title": "Titolo dell'articolo",
  "excerpt": "Una frase di anteprima.",
  "href": ""
}
```

Finché non hai una pagina reale per l'articolo, lascia `href` vuoto: la
card comparirà comunque, ma non sarà cliccabile. Quando pubblichi la
pagina dell'articolo, inserisci il suo URL in `href` e la card diventerà
automaticamente un link.

## Testare il sito in locale

Una volta che i contenuti arrivano da `data/*.json` tramite `fetch()`, il
sito **non funziona più aprendo `index.html` con un doppio click** (i
browser bloccano il caricamento di file locali via `fetch` per motivi di
sicurezza — protocollo `file://`). In quel caso il sito resta comunque
funzionante grazie ai contenuti di riserva incorporati nel codice, ma userai
un catalogo "demo", non i tuoi contenuti reali.

Per testare i contenuti reali in locale, avvia un piccolo server statico
nella cartella del progetto, ad esempio uno di questi (bastano pochi
secondi):

```bash
# Python (già presente su Mac/Linux, spesso anche su Windows)
python3 -m http.server 8000

# oppure Node
npx serve .
```

poi apri `http://localhost:8000` nel browser. Su **GitHub Pages** questo
problema non esiste: il sito viene servito via HTTPS e `fetch()` funziona
regolarmente.

## Perché questo approccio

Il sito resta statico, senza build system, senza CMS, senza database — in
linea con la filosofia "self-contained" di VORNEAUX. I tre file in `data/`
sono l'unico punto in cui si scrive quando si aggiunge un contenuto nuovo;
tutta la UI (carosello 3D, player audio, griglia articoli) è generata
automaticamente a partire da lì. Il codice di `index.html` non va più
toccato per pubblicare libri, brani o articoli futuri.
