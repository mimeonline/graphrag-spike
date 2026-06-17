# graphrag-spike

Kleiner Playground-Spike für einen kompletten GraphRAG-Durchstich. Das Projekt ist als Lernstrecke gedacht: Du sollst beim Durcharbeiten praktisch sehen, wie Dokumentgraph, Ontologiegraph, Vector Retrieval und LLM-Antwort zusammenspielen.

1. 1-3 Quellen in `data/` ablegen (`.pdf`, `.md`, `.txt` oder `.xml`).
2. Chunks erzeugen.
3. Embeddings berechnen, wahlweise direkt über OpenAI oder über GitHub Models.
4. Szenario-Ontologie als Kontextkarte prüfen.
5. Dokumentgraph und Ontologiegraph nach Neo4j Aura schreiben.
6. Vector Search mit 1-Hop-Graph-Kontext ausführen.
7. Provenienzpfad als JSON und statische SVG-HTML exportieren.
8. Antworten als LLM-only, RAG-only und GraphRAG erzeugen und vergleichen.

Das Ziel ist nicht perfektes GraphRAG, sondern praktisches Verständnis: Welche Architektur-Schichten braucht ein quellenbasiertes GraphRAG-Pattern, und hilft ein sichtbarer Belegpfad für den Graf-Kompass?

Hinweis: Dieses Projekt zeigt GraphRAG mit Neo4j/Cypher und Embeddings. Es ist kein GraphQL-Beispiel.

## Schnellstart

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m ipykernel install --user --name graphrag-spike
cp .env.example .env
```

Danach:

- Trage `OPENAI_API_KEY`, `NEO4J_URI` und `NEO4J_PASSWORD` in `.env` ein.
- Für GitHub Models setze stattdessen `LLM_PROVIDER=github` und `GITHUB_MODELS_API_KEY` mit einem PAT, der `models: read` darf.
- Lege mindestens eine `.pdf`, `.md`, `.txt` oder `.xml` in `data/`.
- Öffne für den ersten Lauf `notebooks/01_klima_kommunale_klimaanpassung.ipynb`.
- Führe das Notebook von oben nach unten aus.

Erwartete Outputs:

- `outputs/<szenario>/provenance.json`
- `outputs/<szenario>/provenance.html`
- `outputs/<szenario>/retrieval_brief.md`
- `outputs/<szenario>/answer_rag_context.md`
- `outputs/<szenario>/answer_graphrag_context.md`
- `outputs/<szenario>/answer_llm_only.md`
- `outputs/<szenario>/answer_rag_only.md`
- `outputs/<szenario>/answer_graphrag.md`
- `outputs/<szenario>/answer_comparison.md`

Die Szenario-Notebooks leeren vor den Embeddings die aktuell konfigurierte Neo4j-Testdatenbank und bauen ihr jeweiliges Szenario neu auf. Das ist für isolierte Demo-Läufe bewusst so gewählt.

## Neo4j Aura Datenbank erstellen

Neo4j Aura nennt eine Cloud-Datenbank im UI meist `Instance`. Für diesen Spike reicht eine kleine AuraDB-Instanz als isolierte Testdatenbank.

1. Öffne die Neo4j Aura Console: <https://console.neo4j.io/>.
2. Lege ein Konto an oder melde dich an.
3. Erstelle bei Bedarf ein neues Projekt, z. B. `graphrag-spike`.
4. Wähle `Create instance` und als Produkttyp `AuraDB`.
5. Für einen ersten Test genügt meist `AuraDB Free`, sofern in deinem Account noch eine freie Instanz verfügbar ist. Alternativ nimm eine kleine bezahlte AuraDB-Instanz.
6. Wähle Region und Instanznamen, z. B. `graphrag-spike-dev`.
7. Aktiviere optional `Vector optimization`, falls die Option angeboten wird. Der Spike legt den Neo4j-Vector-Index selbst im Notebook an.
8. Erstelle die Instanz und lade die Credentials-Datei direkt herunter. Das Passwort wird später nicht erneut angezeigt.
9. Lege die heruntergeladene Datei optional direkt in dieses Projektverzeichnis. Sie heißt oft ähnlich wie `Neo4j-...-Created-....txt` und enthält unter anderem Connection URI, User, Passwort, Datenbankname, Instance ID und Instance Name.
10. Die Notebooks lesen diese Datei beim Start automatisch und übernehmen fehlende Werte in die Laufzeitumgebung. Das ist praktisch für lokale Experimente, weil du die Aura-Parameter nicht einzeln abtippen musst.
11. Alternativ oder zusätzlich kannst du die Werte manuell in `.env` eintragen:

```bash
NEO4J_URI=neo4j+s://<deine-instanz>.databases.neo4j.io
NEO4J_USER=neo4j
NEO4J_PASSWORD=<passwort-aus-der-credentials-datei>
NEO4J_DATABASE=neo4j
```

Werte aus `.env` haben Vorrang vor der Credentials-Datei. So kannst du z. B. eine heruntergeladene Aura-Datei im Ordner lassen, aber einzelne Parameter lokal überschreiben. Committe weder `.env` noch die Aura-Credentials-Datei.

Zum kurzen Verbindungstest kannst du in Aura `Query` öffnen, die Instanz verbinden und ausführen:

```cypher
RETURN 1 AS ok;
```

Wichtig: Die Szenario-Notebooks führen zu Beginn des Graph-Aufbaus `MATCH (n) DETACH DELETE n` aus. Nutze dafür also wirklich eine eigene Testinstanz oder eine Datenbank, deren Inhalt gelöscht werden darf.

## Entscheidung nach dem Spike

Nach dem ersten Lauf ist der Spike nützlich, wenn:

- die Top-Treffer fachlich plausibel sind,
- der Graph eine verständliche Belegspur aus Dokumentgraph und Ontologiegraph zeigt,
- `provenance.html` als Demo-Screenshot oder interaktive Grafik funktioniert,
- `answer_comparison.md` sichtbar macht, was LLM-only, RAG-only und GraphRAG jeweils leisten,
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
4. Antwortvergleich: `Frage -> LLM-only`, `Chunks -> RAG-only`, `Chunks + Entitäten + Pfade -> GraphRAG`

Das generische All-Sources-Notebook liegt nur noch als Vorlage unter `templates/01_graphrag_quickstart_template.ipynb`.

## Aktuelle Beispielquellen

Im `data/`-Ordner liegen als geeignete deutsche Experimentierquellen:

- BSI IT-Grundschutz-Kompendium 2023 als XML
- Umweltbundesamt: Kommunale Klimaanpassung
- Sozialbericht 2024

Diese drei Quellen sind bewusst anschlussfähiger als ein einzelnes Fachbuch: Security, Klimaresilienz und gesellschaftliche Daten funktionieren jeweils als eigenständige Demo-Szenarien.

## Quellen und Rechtehinweis

Die Demo-Dateien in `data/` sind öffentlich zugängliche Publikationen externer Herausgeber. Sie werden hier als kleine, nachvollziehbare Lern- und Demoquellen für den GraphRAG-Spike mitgeführt.

| Datei | Inhalt | Herausgeber | Originalquelle |
| --- | --- | --- | --- |
| `data/48_2024_cc_kommunale_klimaanpassung.pdf` | `Kommunale Klimaanpassung. Bestandsaufnahme, Einflussfaktoren und Hebelpunkte`, Reihe `Climate Change 48/2024` | Umweltbundesamt | <https://www.umweltbundesamt.de/publikationen/kommunale-klimaanpassung> |
| `data/IT_Grundschutz_Kompendium_2023.xml` | `IT-Grundschutz-Kompendium`, Edition 2023, XML-Fassung | Bundesamt für Sicherheit in der Informationstechnik (BSI) | <https://www.bsi.bund.de/grundschutz> |
| `data/Sozialbericht_2024_bf_k2.pdf` | `Sozialbericht 2024. Ein Datenreport für Deutschland` | Bundeszentrale für politische Bildung (bpb), Destatis, WZB, BiB, SOEP/DIW Berlin | <https://www.bpb.de/kurz-knapp/zahlen-und-fakten/sozialbericht-2024/> |

Die Rechte an den Publikationen verbleiben bei den jeweiligen Herausgebern. Für eine Weiterverwendung, Veröffentlichung oder Bearbeitung außerhalb dieser Demo sollten die jeweils aktuellen Nutzungsbedingungen der Originalquellen geprüft werden. Die Dateien werden im Projekt nicht als eigene Inhalte ausgegeben, sondern als belegbare Testquellen für Retrieval, Graphaufbau und Provenienzvisualisierung genutzt.
