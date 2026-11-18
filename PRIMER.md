# PRIMER — wd-ogham-static

Arbeitsplan für `n4o-rse/wd-ogham-static`. Wird zu Beginn jedes Chats hochgeladen (oder per Clone geholt) und am Ende zurückgeschrieben. Stand: 2026-11-18 (S0: Skeleton).

Sprache: dieses Dokument ist deutsch, alles andere im Repo (Code, Kommentare, README) ist britisches Englisch.

---

## Teil A — Immer gültig

### A1 Ausgangslage

| Repo | Rolle | Stand |
|---|---|---|
| `n4o-rse/wikibase-static` | Paket (ab 0.27.1): `seeds-from-sparql`, `import-wikidata --generic`, Seiten, RDF, SPARQL | vorhanden |
| `n4o-rse/wd-ogham-static` | dieses Repo: die Ogham-Steine des WikiProjects OghamStones (Q126491026) aus Wikidata | S0 |
| `n4o-rse/wd-govanstones-static` | Schwester-Site, gleiche Technik, online | S1 und S3a erledigt |
| `n4o-rse/osm-ogham-static` | Ogham-Steine als OSM-Elemente (Präfix `oos`) | vorhanden |

Quellen zum Modell der Steine (von Flo, 2026-11-18): Wikidata:WikiProject OghamStones (Property-Tabellen für einen Stein „in einer Ausstellung“ und „in der Wildnis“), Wikidata:WikiProject Irish Ogham Stones (Q100530634; Liste mit Orten, Concepts, Wörtern, rund 145 PDF-Seiten), Thiery 2022, „SPARQLing Ogham“, ZfdG Sonderband 5, DOI 10.17175/sb005_010.

### A2 Beschlusslage

| Thema | Beschluss | Datum |
|---|---|---|
| IDs | Eigene IDs (Items und Klassen `Q100` ff., Properties `P100` ff.); die Wikidata-ID steht als Zwilling `P4` (wie in `wd-govanstones-static`) | 2026-11-14 |
| RDF | Präfix `ogs` (`ogs:`, `ogst:`, `ogsp:` …; `oos` gehört `osm-ogham-static`), `rdf_base` `https://n4o-rse.github.io/wd-ogham-static`; `owl:sameAs` auf Wikidata ist noch nicht umgesetzt (Paket-PRIMER, S14d) | 2026-11-18 |
| Lizenz | Daten CC0 (Wikidata ist CC0), Code MIT; Bilder werden nicht kopiert, nur von Commons verlinkt | 2026-11-18 |
| Quellen | Query `data/seeds/wikidata-oghamstones.rq` (alles, was `P361` des WikiProjects OghamStones Q126491026 ist) plus `data/seeds/wikidata-qids.txt`; beide über `seeds-from-sparql` | 2026-11-18 |
| Umfang | Zuerst nur die Steine selbst. Orte (Ogham Sites), Concepts (CIIC, CISP, O3D), Landkreise und Sammlungen kommen als Stubs (Label, Beschreibung, Zwilling) mit, ohne eigene Aussagen | 2026-11-18 |
| Modell | Keins eigenes: `import-wikidata --generic` legt die Properties aus den Wikidata-Properties an (`main.py` hängt `--generic` selbst an) | 2026-11-18 |
| Logo | Federzeichnung eines Ogham-Steins auf einem irischen Hügel (`assets/logo.png`) | 2026-11-18 |

### A3 Arbeitsweise

Änderungen kommen als Patch-ZIP; Flo spielt sie selbst ein und pusht. Wikidata und der Query Service sind aus der Sandbox nicht erreichbar: die Läufe mit Netz macht Flo auf Windows `cmd`, die Ergebnisse (`dist/wikidata-import.txt`, `dist/wikidata-gaps.csv`, die Ausgabe der Befehle) kommen in den nächsten Chat.

---

## Teil B — Schritte

| Schritt | Inhalt | Stand |
|---|---|---|
| S0 | Skeleton: `main.py` mit den Wikidata-Verben, `site.yaml`, Query, Logo, Pin auf v0.27.1, Workflows (`deploy: false`), leer gebaut | erledigt 2026-11-18 |
| S1 | Erster Lauf: `seeds-from-sparql`, Zahl der Seeds prüfen, `import-wikidata`, Bericht lesen, Properties prüfen (`--skip-property`), `--strict` bauen, Pages einschalten, `deploy: true` | Lauf erledigt 2026-11-18 (8 Seeds, 127 Einträge, `--strict` grün); Query korrigiert, Rest offen |
| S2 | CRM-Overlay `data/crm.csv` (Ogham-Stein, archäologisches Artefakt, Ogham Site → CIDOC CRM), `check-crm` | offen |
| S3 | Beschreibungen, `data_as_of`, `queries.yaml` (Steine mit Bild, mit Inschrift, mit Sketchfab-Modell, nach Landkreis) | offen |
| S4 | Weitere Schichten aus dem WikiProject Irish Ogham Stones (Orte, Concepts, Wörter), Verbindung zu `osm-ogham-static` über die OSM-Node-ID (`P11693`) | später |

---

## Teil D — Offene Punkte

- **Query (1. Lauf 2026-11-18):** 8 Seeds: die 7 Steine der Beispiele auf der Seite des WikiProjects (UCC Stone Corridor #1 und IV, Cork Public Museum ×3, Coumeenole North, Ballinrannig VI) und das WikiProject Irish Ogham Stones (Q106922896, wurde `Q100`), das keine Stein ist. Die Query schließt jetzt Items aus, die `P31` WikiProject (Q16695773) sind. Weitere Steine (Trinity College, National Museum, Dingle stehen auf der Seite noch ohne Items) gibt es erst, wenn sie in Wikidata angelegt und mit `P361` an das WikiProject gehängt sind; bis dahin über `data/seeds/wikidata-qids.txt`.
- **Modell nach der Wikidata-Seite:** ein Stein „in einer Ausstellung“ hat `P625` mit Qualifikatoren (ex situ, on-site survey), Sammlung `P195`, Inventarnummer `P217`, Fundort `P189`, Sketchfab-Modell `P1325` mit Qualifikatoren, Inschrift `P1684` (monolingualtext, Latein), Zustand `P5816`, `P6500` (nicht freies Bild), `P1382` (teilweise deckungsgleich mit den Concepts CIIC und CISP). `--generic` nimmt alle diese Properties mit; die Rolle und Rang-Angaben (bevorzugter Rang) hängen am Import des Pakets.
- **Zahl der Properties:** `--generic` legt jede tragbare Property an, auch Identifikatoren. Sind es zu viele, `--skip-property P…,P…` am `import-wikidata`-Aufruf (Paket-PRIMER, Teil D).
- **GitHub Pages:** einmal Settings, Pages, Source „GitHub Actions“; erst nach S1 `deploy: true` in `.github/workflows/site.yml`.
- **Update-Workflow:** einmal Settings, Actions, General, „Allow GitHub Actions to create and approve pull requests“.
- **Paket:** `tools/satellites.txt` im Paket kennt die beiden Wikidata-Satelliten noch nicht (Rollout auf neue Versionen).
