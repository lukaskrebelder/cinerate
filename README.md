# CineRate

CineRate ist eine Web-Anwendung zum Suchen, Bewerten und Kommentieren von Filmen, ähnlich wie IMDB. Die Filmdaten kommen von der TMDB-API. Entstanden ist sie im Portfolio Web-Programmierung an der DHBW Mosbach (WS 2026/27).

Dieses Repository enthält keinen eigenen Code. Es bündelt die fünf Repositories von CineRate als Git-Submodule, damit sich alles mit einem Befehl holen lässt. Entwickelt und deployt wird weiter in den einzelnen Repositories.

| Ordner | Inhalt | Zuständig |
| --- | --- | --- |
| [`cinerate-spa`](https://github.com/lukaskrebelder/cinerate-spa) | Single-Page-App (Vanilla JavaScript) | Nico Weber |
| [`cinerate-mpa`](https://github.com/lukaskrebelder/cinerate-mpa) | Multi-Page-App (Express, EJS) | Nico Weber |
| [`cinerate-bff`](https://github.com/lukaskrebelder/cinerate-bff) | Backend for Frontend, REST und GraphQL | Madlen Hahn |
| [`cinerate-backend`](https://github.com/lukaskrebelder/cinerate-backend) | REST-API, Fachlogik, Anbindung an TMDB | Lukas Krebelder |
| [`cinerate-db`](https://github.com/lukaskrebelder/cinerate-db) | SQL-Migrationen für PostgreSQL | Lukas Krebelder |

SPA und MPA sprechen nur mit dem BFF, das BFF nur mit dem Backend, und nur das Backend greift auf die Datenbank und auf TMDB zu. Die Gründe dafür stehen in den Architecture Decision Records (ADR-01 bis ADR-07, Anlage `ADRs-Portfolio.pdf` der Abgabe).

## Code holen

```
git clone --recurse-submodules https://github.com/lukaskrebelder/cinerate.git
```

> **Wichtig:** Ohne `--recurse-submodules` sind die fünf Ordner leer. Wer schon ohne die Option geklont hat, holt den Inhalt nachträglich mit `git submodule update --init`.

## Code holen und lokal starten

### 0. Ohne Installation: online ausprobieren

Die Anwendung läuft auf Heroku:

| Teil | Adresse |
| --- | --- |
| SPA | https://cinerate-spa-35a2aec816a9.herokuapp.com |
| MPA | https://cinerate-mpa-dfbdbd6de171.herokuapp.com |
| BFF | https://cinerate-bff-be82e8357e5c.herokuapp.com |
| Backend | https://cinerate-backend-b2344b03c9f7.herokuapp.com (nimmt Anfragen nur vom BFF an, nur `/api/status` ist frei) |

Die Anwendungen laufen ab der Abgabe am 08.11.2026 bis zum Ende der Bewertung. Antwortet eine Adresse nicht, ist der Heroku-Dyno gerade aus. Dann bitte kurz dem Team Bescheid geben (Kontakt in der Abgabe-E-Mail), wir schalten ihn ein.

### 1. Voraussetzungen

- Git
- Node.js 24 (die MPA verlangt Version 24, die übrigen Teile sind damit getestet)
- PostgreSQL mit einer leeren Datenbank, zum Beispiel `createdb cinerate`
- ein eigener TMDB-API-Schlüssel (kostenlos unter themoviedb.org). Der Schlüssel steht aus Sicherheitsgründen nicht im Code.

### 2. Code holen

```
git clone --recurse-submodules https://github.com/lukaskrebelder/cinerate.git
cd cinerate
```

### 3. Starten

Die Teile werden in dieser Reihenfolge gestartet, jeweils in einem eigenen Terminal im Ordner des Repositorys. In jedem Ordner zuerst `npm install` ausführen und, wo es eine Datei `.env.example` gibt, daraus die Datei `.env` anlegen:

```
cp .env.example .env       # Linux, macOS
copy .env.example .env     # Windows
```

| Schritt | Ordner | Eintrag in `.env` | Befehl | Adresse |
| --- | --- | --- | --- | --- |
| a | `cinerate-db` | `DATABASE_URL` der lokalen Datenbank | `npm run migrate` | |
| b | `cinerate-backend` | `DATABASE_URL` wie bei a, `TMDB_API_KEY`; `INTERNAL_API_KEY` leer lassen | `npm start` | http://localhost:3000 |
| c | `cinerate-bff` | `BACKEND_URL=http://localhost:3000`; `SPA_URL` und `INTERNAL_API_KEY` leer lassen | `npm start` | http://localhost:4000 |
| d | `cinerate-spa` | keine | `npm start` | Adresse steht in der Ausgabe |
| e | `cinerate-mpa` | `BFF_URL=http://localhost:4000` | `npm start` | http://localhost:5000 |

Hinweise zu den Schritten:

- **a:** Die Migrationen legen alle Tabellen an. Sie werden nur einmal ausgeführt.
- **b, c:** Bleibt `INTERNAL_API_KEY` auf beiden Seiten leer, prüft das Backend den Schlüssel nicht und gibt beim Start eine Warnung aus. Lokal ist das gewollt.
- **d:** Port 3000 ist schon vom Backend belegt. `serve` nimmt dann einen anderen freien Port und zeigt ihn an. Einen festen Port gibt `npm start -- -l 8080` vor. Die SPA im Browser immer über `http://localhost:<Port>` öffnen, nicht über `0.0.0.0` oder die Netzwerkadresse: Nur unter `localhost` spricht sie mit dem lokalen BFF auf Port 4000, sonst mit dem BFF auf Heroku.

### 4. Tests

In `cinerate-spa`, `cinerate-mpa`, `cinerate-bff` und `cinerate-backend`:

```
npm test
```

Die Tests brauchen weder eine Datenbank noch Internet. `cinerate-db` enthält nur SQL-Dateien und keine Tests.

### 5. Optional: Moderator anlegen

Ein Moderator darf fremde Kommentare löschen. Wie man einen Nutzer zum Moderator macht, steht in der README von `cinerate-backend` im Abschnitt „Moderator anlegen“.

## Stand der Submodule

Das Repository merkt sich für jedes Submodul einen bestimmten Commit. Den zur Abgabe gehörenden Stand zeigt GitHub neben jedem Ordner an.
