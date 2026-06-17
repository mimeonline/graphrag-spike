# AGENTS.md

Projektanweisungen für Agents in diesem Repository.

## Projektkontext

Dies ist ein kleiner GraphRAG-Provenienz-Spike für den Graf-Kompass. Ziel ist ein nachvollziehbarer Durchstich:

1. Deutsche Quellen aus `data/` laden.
2. Texte in Chunks zerlegen.
3. OpenAI-Embeddings erzeugen.
4. `Document`, `Chunk` und `Term` nach Neo4j Aura schreiben.
5. Vector Search ausführen.
6. Den Provenienzpfad als JSON, HTML und Notebook-Grafik anzeigen.

Der Spike ist bewusst demo- und lernorientiert. Produktionsreife GraphRAG-Qualität ist nicht das Ziel.

## Einstieg

Für den ersten fachlichen Durchlauf ist dieses Notebook maßgeblich:

```text
notebooks/01_klima_kommunale_klimaanpassung.ipynb
```

Das generische Quickstart-Notebook liegt nur als Vorlage unter:

```text
templates/01_graphrag_quickstart_template.ipynb
```

Es soll nicht wieder als aktives Einstiegsnotebook unter `notebooks/` einsortiert werden.

## Szenario-Notebooks

Aktive Szenarien:

- `notebooks/01_klima_kommunale_klimaanpassung.ipynb`
- `notebooks/02_bsi_it_grundschutz.ipynb`
- `notebooks/03_sozialbericht_2024.ipynb`

Jedes Szenario soll:

- genau seine konfigurierte Quelle über `SOURCE_PATTERNS` laden,
- seine eigene eingebaute Szenariofrage verwenden,
- vor den OpenAI-Embeddings die konfigurierte Neo4j-Testdatenbank leeren,
- den Graphen danach Schritt für Schritt neu aufbauen,
- szenariospezifische Outputs unter `outputs/<scenario_id>/` schreiben.

Keine globale `QUESTION`-Variable als Standard verwenden. Falls Fragen überschrieben werden sollen, dann nur szenariospezifisch:

- `KLIMA_KOMMUNALE_KLIMAANPASSUNG_QUESTION`
- `BSI_IT_GRUNDSCHUTZ_QUESTION`
- `SOZIALBERICHT_2024_QUESTION`

## Neo4j-Verhalten

Die Szenario-Notebooks leeren die aktuell konfigurierte Testdatenbank mit:

```cypher
MATCH (n) DETACH DELETE n
```

Das ist für isolierte Demo-Läufe Absicht. Keine Notebook-Zelle ausführen, wenn der Nutzer das nicht ausdrücklich anfragt.

Wenn ein gemeinsamer Korpus über mehrere Quellen hinweg getestet werden soll, muss das explizit als neuer Modus umgesetzt werden, z. B. mit Source-Filtern, Szenario-IDs oder kuratierten Concept-Knoten.

## Outputs

Outputs dürfen nicht pauschal direkt unter `outputs/` überschrieben werden. Erwartetes Muster:

```text
outputs/klima_kommunale_klimaanpassung/provenance.json
outputs/klima_kommunale_klimaanpassung/provenance.html
outputs/klima_kommunale_klimaanpassung/answer.md
```

Analog für `bsi_it_grundschutz` und `sozialbericht_2024`.

## Secrets und Daten

- `.env` darf nicht committet werden.
- Neo4j-Aura-Credentials-Dateien dürfen nicht committet werden.
- Die Demoquellen unter `data/` sind absichtlich versioniert, damit das Projekt direkt nachspielbar ist.
- Keine Secrets in Notebook-Ausgaben, Markdown-Dateien oder Commit-Messages schreiben.

## Lokale Umgebung

Die lokale virtuelle Umgebung liegt unter `.venv/` und ist ignoriert.

Typischer Setup-Befehl:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Git

Nach Änderungen immer `git status --short` prüfen und kurz berichten. Nicht automatisch committen, außer der Nutzer bittet ausdrücklich darum.
