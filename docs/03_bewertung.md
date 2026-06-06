# Bewertung für den Graph-Kompass

Der Spike beantwortet eine einfache Frage:

> Ist ein sichtbarer GraphRAG-Provenienzpfad interessant genug, um daraus eine Graf-Kompass- oder Grafik-Lab-Demo zu bauen?

## Gute Zeichen

- Die ähnlichsten Chunks passen zur Frage.
- Der Provenienzgraph zeigt nachvollziehbare Quellen, Textstellen und Ontologie-Entitäten.
- Die Ontologie wirkt als verständliche Kontextkarte, nicht nur als Schlagwortliste.
- Die Visualisierung kann ohne viel Erklärung in einer Demo gezeigt werden.
- Die Ausgabe macht Lust auf bessere Entity-Linking- und Chunking-Logik.

## Warnzeichen

- Der Graph besteht fast nur aus irrelevanten Term-Knoten.
- Die Nachbarschaften erklären nichts Zusätzliches gegenüber normalem RAG.
- Die Visualisierung ist schwer lesbar oder wirkt beliebig.
- Die Ontologie-Relationen wirken frei erfunden oder passen nicht zum Dokument.
- Die Antwort nennt Behauptungen, die in `answer_context.md` nicht belegbar sind.

## Entscheidung

Wenn der Spike positiv ist, sollte der nächste Schritt kein größeres Notebook sein, sondern ein kleines, kuratiertes Demo-Dataset:

- 1 PDF oder Markdown-Dokument
- 10-30 sauber kuratierte Entitäten
- belegte Relationen zwischen den Entitäten
- feste Graf-Kompass-Kategorien
- eine schöne, stabile Demo-Visualisierung
