Title: Warum ich Unit-Tests für Third-Party-Code schreibe
Published: 2016-05-23
Tags: ["Unit-Testing", "TDD"]
---
Wer TDD lernt, stellt sich erstaunlich selten die Frage, ob Unit-Tests auch für bestehende Frameworks geschrieben werden sollten. Die meisten denken höchsten einen kurzen Augenblick darüber nach und kommen zu folgender, durchaus verständlicher Antwort.

> Die Korrektheit des Frameworks fällt nicht in meine Zuständigkeit. Die Entwickler des Frameworks sind selbst dafür verantwortlich, ihre Funktionen mit Unit-Tests zu testen.

Klingt absolut logisch. Und diesmal meine ich das nichtmal ironisch. :-)

## Aber...
Der Grundgedanke der oben genannten Aussage ist natürlich richtig. Als einfacher Nutzer des Frameworks, bin ich<!-- Read More --> natürlich nicht in der Pflicht Unit-Tests zu schreiben. Aber denken wir noch einmal kurz darüber nach, wie man normalerweise einen Unit-Test schreibt. Man formuliert eine Erwartung an das eigene System. 

> "Auf diese Weise möchte ich meine Klassen und Methoden benutzen und diese Rückgabewerte / dieses Verhalten erwarte ich von den Komponenten."

Wenn ich ein neues Framework einsetze tue ich genau das Gleiche. Ich schreibe einen Test in dem ich meine Erwartungen an das Framework formuliere.

## Weil...
Dieses Vorgehen hat zwei entscheidende Vorteile. 

### 1. Erwartungen vs Realität
Wenn ich die Tests laufen lasse, sehe ich genau ob das Framework so arbeitet, wie ich es erwarte. Musste vielleicht noch ein optionaler Parameter gesetzt werden, um das von mir gewünschte Verhalten zu erreichen? Hat der Entwickler des Frameworks an einigen Stellen vielleicht ein etwas anderes Verständnis von einem Begriff oder einem Prozess? All dies lässt sich hervorragend mit Unit-Tests überprüfen.

### 2. Neue Version, altes Verhalten?
Es kommt hin und wieder vor, dass ein Framework in einer neuen Version sein Verhalten ändert. Vielleicht wurde aus Performance-Gründen die Code-Basis auf den Kopf gestellt. Vielleicht hat der Entwickler sich mehr als das [Single Responsibility Principle](https://de.m.wikipedia.org/wiki/Single-Responsibility-Prinzip) gehalten und dafür eine große Methode in zwei kleinere aufgespalten. Gründe für Änderungen gibt es genug. Wenn ich die von mir genutzen Methoden mit Unit-Tests abgedeckt habe, kann ich bedenkenlos immer auf die neuste Version des Frameworks wechseln. Ich lasse im Anschluss einfach meine Test-Suite laufen. Wenn alle Tests grün 
sind, kann ich einfach weitermachen wie bisher.

Schreibt ihr Unit-Tests für die von euch benutzten Frameworks? Habt ihr vielleicht eine bessere Möglichkeit gefunden die oben genannten Punkte sicherzustellen? Lasst es mich über die Kommentarfunktion wissen.
