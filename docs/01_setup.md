# Setup

## Voraussetzungen

- Python 3.11 oder neuer
- Neo4j Aura Instanz mit Bolt/Neo4j URI
- OpenAI API-Key
- 1-3 kleine Testquellen als `.pdf`, `.md`, `.txt` oder `.xml`

## Secrets

Secrets gehören in `.env`, nicht ins Notebook.

Kopiere:

```bash
cp .env.example .env
```

Trage danach ein:

```bash
OPENAI_API_KEY=...
OPENAI_ANSWER_MODEL=gpt-5-nano
NEO4J_URI=neo4j+s://...
NEO4J_USER=neo4j
NEO4J_PASSWORD=...
```

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

Die Szenario-Notebooks leeren vor den OpenAI-Embeddings die aktuell konfigurierte Neo4j-Testdatenbank und schreiben ihre Ergebnisse in einen eigenen Output-Ordner, z. B. `outputs/klima_kommunale_klimaanpassung/`.

Der letzte fachliche Schritt erzeugt eine LLM-Antwort. Dafür nutzt das Notebook `OPENAI_ANSWER_MODEL`. Der Default ist ein günstiges kleines Antwortmodell; du kannst das Modell in `.env` ändern, wenn du Antwortqualität, Kosten oder Geschwindigkeit vergleichen willst.


## Neo4j Aura Credentials

Wenn eine Neo4j-Aura-Credentials-Datei im Projektordner liegt, z. B. `Neo4j-...-Created-....txt`, liest das Notebook sie automatisch. Unterstützt werden unter anderem `NEO4J_URI`, `NEO4J_USERNAME`, `NEO4J_PASSWORD` und `NEO4J_DATABASE`. Werte aus `.env` haben Vorrang; die Credentials-Datei füllt nur fehlende Werte auf.
