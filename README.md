# Graf-Kompass GraphRAG Provenienz-Spike

Kleiner Playground-Spike für einen kompletten GraphRAG-Durchstich. Das Projekt ist als Lernstrecke gedacht: Du sollst beim Durcharbeiten praktisch sehen, wie Dokumentgraph, Ontologiegraph, Vector Retrieval und LLM-Antwort zusammenspielen.

1. 1-3 Quellen in `data/` ablegen (`.pdf`, `.md`, `.txt` oder `.xml`).
2. Chunks erzeugen.
3. OpenAI Embeddings berechnen.
4. Szenario-Ontologie als Kontextkarte prüfen.
5. Dokumentgraph und Ontologiegraph nach Neo4j Aura schreiben.
6. Vector Search mit 1-Hop-Graph-Kontext ausführen.
7. Provenienzpfad als JSON und statische SVG-HTML exportieren.
8. LLM-Antwort mit Quellenbelegen erzeugen.

Das Ziel ist nicht perfektes GraphRAG, sondern praktisches Verständnis: Welche Architektur-Schichten braucht ein quellenbasiertes GraphRAG-Pattern, und hilft ein sichtbarer Belegpfad für den Graf-Kompass?

Hinweis: Dieses Projekt zeigt GraphRAG mit Neo4j/Cypher und OpenAI Embeddings. Es ist kein GraphQL-Beispiel.

## Schnellstart

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m ipykernel install --user --name graf-kompass-graphrag-spike
cp .env.example .env
```

Danach:

- Trage `OPENAI_API_KEY`, `NEO4J_URI` und `NEO4J_PASSWORD` in `.env` ein.
- Lege mindestens eine `.pdf`, `.md`, `.txt` oder `.xml` in `data/`.
- Öffne für den ersten Lauf `notebooks/01_klima_kommunale_klimaanpassung.ipynb`.
- Führe das Notebook von oben nach unten aus.

Erwartete Outputs:

- `outputs/<szenario>/provenance.json`
- `outputs/<szenario>/provenance.html`
- `outputs/<szenario>/retrieval_brief.md`
- `outputs/<szenario>/answer_context.md`
- `outputs/<szenario>/answer.md`

Die Szenario-Notebooks leeren vor den OpenAI-Embeddings die aktuell konfigurierte Neo4j-Testdatenbank und bauen ihr jeweiliges Szenario neu auf. Das ist für isolierte Demo-Läufe bewusst so gewählt.

## Entscheidung nach dem Spike

Nach dem ersten Lauf ist der Spike nützlich, wenn:

- die Top-Treffer fachlich plausibel sind,
- der Graph eine verständliche Belegspur aus Dokumentgraph und Ontologiegraph zeigt,
- `provenance.html` als Demo-Screenshot oder interaktive Grafik funktioniert,
- `answer.md` eine quellenbasierte LLM-Synthese liefert,
- klar wird, welche bessere Entitäts- oder Begriffsextraktion als nächster Schritt nötig ist.

Wenn einfache Term-Knoten noch Rauschen erzeugen, ist das kein Scheitern. Dann zeigt der Spike, warum der kuratierte Ontologiegraph mit `Entity`-/`Concept`-Knoten für den Graf-Kompass wertvoll ist.



## Szenario-Notebooks

Es gibt drei fokussierte Lerneinheiten:

- `notebooks/01_klima_kommunale_klimaanpassung.ipynb`: UBA-Bericht zur kommunalen Klimaanpassung
- `notebooks/02_bsi_it_grundschutz.ipynb`: BSI IT-Grundschutz-Kompendium als XML
- `notebooks/03_sozialbericht_2024.ipynb`: Sozialbericht 2024

Alle drei Notebooks nutzen dieselbe GraphRAG-Pipeline, filtern aber jeweils eine Quelle und starten mit einer passenden Leitfrage. Für den ersten Durchlauf ist das Klima-Notebook der beste Einstieg.

Die Lernschichten in jedem Notebook sind:

1. Dokumentgraph: `Document -> Chunk`
2. Ontologiegraph: `Ontology -> OntologyClass -> Entity/Concept -> ONTOLOGY_RELATION`
3. Retrievalgraph: `Question -> Vector Search -> Chunk -> Entity -> Nachbarkontext`
4. Antwortsynthese: `Belege -> LLM -> answer.md`

Das generische All-Sources-Notebook liegt nur noch als Vorlage unter `templates/01_graphrag_quickstart_template.ipynb`.

## Aktuelle Beispielquellen

Im `data/`-Ordner liegen als geeignete deutsche Experimentierquellen:

- BSI IT-Grundschutz-Kompendium 2023 als XML
- Umweltbundesamt: Kommunale Klimaanpassung
- Sozialbericht 2024

Diese drei Quellen sind bewusst anschlussfähiger als ein einzelnes Fachbuch: Security, Klimaresilienz und gesellschaftliche Daten funktionieren jeweils als eigenständige Demo-Szenarien.
