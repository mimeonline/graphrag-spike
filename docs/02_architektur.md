# Architektur

Der Spike zeigt GraphRAG als vier Lernschichten. Jede Schicht beantwortet eine andere Frage.

```text
Dokumentgraph:
(Document)-[:HAS_CHUNK]->(Chunk)

Ontologiegraph:
(Ontology)-[:HAS_CLASS]->(OntologyClass)
(Entity:Concept)-[:INSTANCE_OF]->(OntologyClass)
(Entity)-[:ONTOLOGY_RELATION]->(Entity)

Retrievalgraph:
(Question embedding) -> Neo4j Vector Index -> (Chunk)
(Chunk)-[:HAS_CONCEPT]->(Entity)
(Chunk)-[:MENTIONS]->(Term)

Antwortsynthese:
Top-Chunks + Ontologie-Kontext + Textbelege -> LLM -> answer.md
```

`Chunk.embedding` wird mit einem Neo4j Vector Index durchsuchbar gemacht. Die Ontologie ist kein Ersatz für Retrieval, sondern ein Kontextmodell: Sie hilft, gefundene Textstellen fachlich einzuordnen und Nachbarschaften sichtbar zu machen.

## Warum diese Form?

- `Document` hält die Quelle sichtbar.
- `Chunk` ist die retrieval-fähige Textebene.
- `OntologyClass` erklärt, welcher Typ von Ding ein Knoten ist, zum Beispiel Risiko, Akteur, Prozess oder Indikator.
- `Entity`/`Concept` sind die fachlichen Knoten der Kontextkarte.
- `ONTOLOGY_RELATION` beschreibt bewusst kuratierte Beziehungen zwischen Entitäten.
- `Term` bleibt als einfacher, automatisch extrahierter Vergleichskanal erhalten. Er zeigt, warum reine Wort-Nachbarschaft oft weniger sauber ist als ein kuratierter Ontologiegraph.
- Das LLM formuliert am Ende eine Antwort nur aus den gefundenen Belegen.

## Mentales Modell

Klasse bedeutet: Welche Art von Ding ist das?

Entität bedeutet: Welches konkrete fachliche Ding steht im Graph?

Relation bedeutet: Wie hängt eine Entität mit einer anderen zusammen?

Beleg bedeutet: Welcher Chunk aus welcher Quelle stützt die Antwort?

## Was ist noch nicht produktionsreif?

- Das Chunking ist wortbasiert, nicht semantisch nach Kapiteln, Tabellen oder XML-Strukturen.
- Die Term-Extraktion ist nur ein Regex-/Frequenz-Platzhalter.
- Die Ontologien sind kuratierte Lern-Kontextkarten, keine vollständigen Fachontologien.
- `HAS_CONCEPT` wird über Alias-Matching erzeugt; produktiv bräuchte es robustere Entity Linking-Logik.
- Die LLM-Antwort nutzt Belege, hat aber noch keine harte automatische Zitierprüfung.
- Die statische SVG-Grafik ist Demo-orientiert, nicht final kuratiert.

## Nächster Schritt

Für den Graf-Kompass wäre nach dem Spike wahrscheinlich die wichtigste Verbesserung:

```text
Chunk -> Entity -> CompassArea / Pattern / Method / Decision
```

Also nicht beliebige häufige Wörter, sondern Begriffe, die wirklich in die Graf-Kompass-Taxonomie passen und mit belegten Quellenpfaden verbunden sind.
