Title: von zerbrochenen Fenstern und Quellcode
Published: 2016-08-12
Tags: ["Clean Code"]
---
Fast jeder kennt ein Haus in seinem Ort oder in der direkten Nachbarschaft, das schon eine ganze Weile nicht mehr bewohnt ist. Was haben diese Häuser in den unterschiedlichsten Städten gemeinsam? Zerbrochene Fenster. Aber warum sind all die Fenster zerbrochen. Klare Antwort: Weil sie von Vandalen eingeworfen wurden. Ihr habt sie alle vor Augen. Diese halbstarken Jugendlichen, die keinen Respekt vorm Besitz anderer Leute haben.

## Die These
Heute verrate ich euch ein Geheimnis. Die gerade beschriebenen Halbstarken haben in der Regel nur das erste Fenster zerbrochen.
Alle anderen können genauso gut vom freundlichen Nachbarn, dem netten Betreiber des kleinen Kiosks oder vom ständig gestressten Geschäftsmann eingeworfen worden sein. 
Glaubt ihr nicht? Philip Zimbardo hat das Experiment gewagt<!-- Read More -->.
Nachlesen kann man das in seinem Buch [The Lucifer Effect: How Good People Turn Evil](https://www.amazon.de/Lucifer-Effect-Good-People-Turn/dp/1846041031/).

## Das Experiment
Er hat zwei identische Autos ohne Kennzeichen und mit leicht geöffneter Motorhaube in zwei völlig unterschiedlichen Gegenden abgestellt. Das eine stand mitten in den Bronx, das andere in Palo Alto. Das Auto in der Bronx wurde bereits nach 10 Minuten von Vandalen malträtiert. Nach 24 Stunden war so ziemlich alles geklaut, was man von so einem Auto abmontieren kann.

Der Wagen in Palo Alto hingegen stand fast eine Woche unberührt da. Doch nachdem Zimbardo dem Auto einige große Beulen verpasste, lief es für das Gefährt nicht besser als für sein Gegenstück in der Bronx. Nach wenigen Stunden lag das Auto auf dem Dach und war völlig zerstört.

Besonders interessant ist die folgende Beobachtung Zimbardos:
> In both cases the "vandals" appeared to be primarily respectable
whites.

Wie oben also beschrieben, sind es nicht immer die "typischen" Vandalen, die in solchen Fällen aktiv werden. Manchmal braucht es nur den ersten Anstoß.

## Die *Broken Window Theory*
Das oben beschriebene Experiment sollte die Richtigkeit der *Broken Window Theory* belegen. Diese wurde im Jahr 1982 von George L. Kelling und James Q. Wilson im [Atlantic Monthly](http://www.theatlantic.com/magazine/archive/1982/03/broken-windows/304465/) wie folgt beschrieben:

> [...] if a window in a building is broken and is left
unrepaired, all the rest of the windows will soon be broken. This is as true in nice neighborhoods as in rundown ones. [...] **one unrepaired broken window is a signal that no one cares, and so breaking more
windows costs nothing**.

## Wayne...
Der letzte Teil des oben genannten Zitats ist der springende Punkt. Ein nicht repariertes Fenster weckt den Eindruck, dass der Zustand des Hauses niemanden interessiert. Und genau hier schlagen wir **endlich** den Bogen zum Quellcode.

Ein schlampig geschriebenes Modul ist, auf den ersten Blick, erstmal nichts, was ein Projekt scheitern lässt. Aber was genau bewirkt dieses Modul? Jeder Entwickler, der sich den Quellcode anschaut, bekommt den Eindruck, dass es in diesem Projekt völlig okay ist solchen Code zu schreiben. Dieses Modul ist somit ein "*broken window*". Es muss auch nicht immer ein ganzes Modul sein. Auch eine unübersichtliche und unschön programmierte Klasse oder Funktion kann bereits diesen Eindruck erwecken.

## Repariere alle *broken windows*
Wer genau das in seinem eigenen Projekt vermeiden möchte, sollte alle *broken windows* so schnell wie möglich reparieren. Jeder Entwickler des Teams sollte dazu angehalten werden, schlampigen oder unschönen Code zu verbessern, sobald man ihn sieht. 

Genau an dieser Stelle kommt die beliebte *Boy Scout Rule* zum Tragen:

> Always leave the campground cleaner than you found it.

Wenn jedes Team-Mitglied nach dieser Regel arbeitet, wird der Quellcode sehr schnell aufgeräumter und lesbarer sein. Voraussetzung dafür ist natürlich, dass jedes Team-Mitglied in der Lage ist guten Code zu schreiben. Aber das steht auf einem ganz anderen Blatt...