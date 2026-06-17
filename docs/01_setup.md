# Setup

## Voraussetzungen

- Python 3.11 oder neuer
- Neo4j Aura Instanz mit Bolt/Neo4j URI
- OpenAI API-Key oder GitHub Personal Access Token mit `models: read`
- die mitgelieferten Demoquellen in `data/`; eigene `.pdf`-, `.md`-, `.txt`- oder `.xml`-Dateien sind optional

## Secrets

Secrets gehören in `.env`, nicht ins Notebook.

Kopiere:

```bash
cp .env.example .env
```

Trage danach ein:

```bash
LLM_PROVIDER=openai
OPENAI_API_KEY=...
OPENAI_ANSWER_MODEL=gpt-5-nano
NEO4J_URI=neo4j+s://...
NEO4J_USER=neo4j
NEO4J_PASSWORD=...
```

Für GitHub Models stellst du stattdessen den Provider um und nutzt einen GitHub PAT mit `models: read`:

```bash
LLM_PROVIDER=github
GITHUB_MODELS_API_KEY=...
GITHUB_EMBEDDING_MODEL=openai/text-embedding-3-small
GITHUB_ANSWER_MODEL=openai/gpt-4o-mini
```

Alternativ akzeptieren die Notebooks auch `GITHUB_TOKEN` oder `LLM_API_KEY`. Provider-unabhängige Overrides sind `EMBEDDING_MODEL`, `ANSWER_MODEL`, `EMBEDDING_DIM` und `ANSWER_TEMPERATURE`.

`.env` ist in `.gitignore` eingetragen.

Die Szenario-Notebooks bringen jeweils eine eigene Standardfrage mit. Optional kannst du sie szenariospezifisch überschreiben:

```bash
KLIMA_KOMMUNALE_KLIMAANPASSUNG_QUESTION=...
BSI_IT_GRUNDSCHUTZ_QUESTION=...
SOZIALBERICHT_2024_QUESTION=...
```

Eine globale `QUESTION` wird für die Szenario-Notebooks nicht mehr verwendet, damit Klima, BSI und Sozialbericht nicht versehentlich dieselbe Frage bekommen.

## Notebook ausführen

```bash
jupyter lab
```

Dann für den ersten Lauf `notebooks/01_klima_kommunale_klimaanpassung.ipynb` öffnen und von oben nach unten ausführen.

Die Szenario-Notebooks leeren vor den Embeddings die aktuell konfigurierte Neo4j-Testdatenbank und schreiben ihre Ergebnisse in einen eigenen Output-Ordner, z. B. `outputs/klima_kommunale_klimaanpassung/`.

Der letzte fachliche Schritt erzeugt eine LLM-Antwort. Dafür nutzt das Notebook je nach Provider `OPENAI_ANSWER_MODEL` oder `GITHUB_ANSWER_MODEL`; mit `ANSWER_MODEL` kannst du beide überschreiben. Der Default ist ein günstiges kleines Antwortmodell; du kannst das Modell in `.env` ändern, wenn du Antwortqualität, Kosten oder Geschwindigkeit vergleichen willst.


## Neo4j Aura Credentials

Wenn eine Neo4j-Aura-Credentials-Datei im Projektordner liegt, z. B. `Neo4j-...-Created-....txt`, liest das Notebook sie automatisch. Unterstützt werden unter anderem `NEO4J_URI`, `NEO4J_USERNAME`, `NEO4J_PASSWORD` und `NEO4J_DATABASE`. Nicht-leere Werte aus `.env` haben Vorrang; die Credentials-Datei füllt fehlende oder leer gelassene Werte auf.
