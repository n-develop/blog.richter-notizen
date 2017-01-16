Title: cat und grep auf der PowerShell
Published: 2016-10-07
Tags: ["PowerShell"]
RedirectFrom: Post/cat-und-grep-powershell
---

Jeder, der im Enterprise-Umfeld hin und wieder Logs durchforsten darf, kennt das.
Bei bestimmten Kunden ist [log4net](https://logging.apache.org/log4net/) (oder [log4j](http://logging.apache.org/log4j/2.x/) oder was auch immer) so konfiguriert, dass am Ende des Tages
riesige Log-Files auf der Platte liegen. 100MB und mehr sind da keine Seltenheit. Spätestens, wenn man wegen eines Bugs oder wegen etwas anderem
das Log-File nach bestimmten Begriffen durchsuchen will, kommt man schnell an bestimmte Grenzen. Viele Editoren öffenen keine Text-Dateien über
einer bestimmten Grenze. Und selbst wenn sie die Datei öffnen, dauert jede Aktion darin mehrere Sekunden.

## cat und grep

Auf Linux würde ich für solche Fälle die Befehle `cat` und `grep` verwenden. Mal angenommen ich suche nach der Zeichenkette "Meldung vom Bug".
In dem Fall sähe der Aufruf vermutlich so aus:

```shell
cat riesigesLogFile.log | grep "Meldung vom Bug"
```

Aber wie erreicht man diese Funktionalität auf Windows?<!-- Read More -->

## Die PowerShell ist dein Freund

Natürlich könnte man sich zum Beispiel CygWin herunterladen und die oben genannten Befehle auch unter Windows nutzen.
Oder, wer mit seimem Windows 10 auf dem neusten Stand ist, kann auch das integrierte Ubuntu nutzen und die Befehle aus einem "echten" Linux heraus
aufrufen.

Aber es geht noch bequemer. Die PowerShell bietet genau das, was wir suchen. Die Alternative zu `cat` heißt in der PowerShell `Get-Content`.
Der Befehl macht nichts anderes, als den Inhalt einer Datei auszugeben. Dazu gibt man dem Befehl einfach den Dateinamen mit.

```powershell
Get-Content riesigesLogFile.log
```

Der spannende Part kommt jetzt. Der `grep`-Befehl auf der PowerShell heißt `Select-String` und wird ebenfalls genauso aufgerufen, wie der Linux-Command.

```powershell
Get-Content riesigesLogFile.log | Select-String "Meldung vom Bug"
```

Großartig, oder?

## Kontext ist wichtig

Wenn man Log-Files durchsucht, ist es oft hilfreich nicht nur die Zeile mit dem Error zu betrachten. Oft schaut man sich auch in den Zeilen darüber und
darunter an, was davor und danach passiert ist. Oder wenn ein Fehler geworfen wurde, sind wir natürlich auch an dem StackTrace interessiert.
Unsere bisherigen Aufrufe bieten uns diesen Kontext allerdings nicht.

Der PowerShell-Befehl `Select-String` bietet uns einen Parameter `-Context` an. Diesem können wir eine Zahl mitgeben. Die Zahl bestimmt die Zeilen
**vor und nach** der gefundenen Zeile, die ebenfalls mit ausgegeben werden. Die Zeilen, in denen die gesuchte Zeichenkette gefunden wurde,
werden am Anfang mit einem `>` markiert.

Der folgende Befehl gibt uns also nicht nur die Zeilen aus, in denen "Meldung vom Bug" gefunden wurde, sondern auch jeweils die 10 umgebenden Zeilen.

```powershell
Get-Content riesigesLogFile.log | Select-String "Meldung vom Bug" -Context 10
```

Hier der Output des Befehls

```text
PS> Get-Content .\riesigesLogFile.log | Select-String "Meldung vom Bug" -Context 10

  consectetuer adipiscing elit. Curabitur blandit mollis lacus. Vestibulum turpis,
  aliquet eget, lobortis pellentesque, rutrum eu, nisl.

  Proin sapien ipsum, porta a, auctor quis, euismod ut, mi. Aenean imperdiet.
  Praesent blandit laoreet nibh. Praesent blandit laoreet nibh. Vestibulum turpis,
  aliquet eget, lobortis pellentesque, rutrum eu, nisl.

  Praesent metus tellus, elementum eu, semper a, adipiscing nec,
  purus. Vestibulum volutpat pretium libero. Donec mollis hendrerit risus.
  Praesent egestas tristique nibh. Sed fringilla mauris sit amet nibh.
> ERROR: Meldung vom Bug
  Donec mollis hendrerit risus. Sed magna purus, fermentum eu, tincidunt eu,
  varius ut, felis. Phasellus ullamcorper ipsum rutrum nunc. Fusce commodo
  aliquam arcu. Etiam ultricies nisi vel augue.

  Pellentesque egestas, neque sit amet convallis pulvinar, justo nulla eleifend
  augue, ac auctor orci leo non est. Praesent egestas neque eu enim. Phasellus,
  metus eget egestas mollis, lacus lacus blandit dui, id egestas quam mauris ut.
  Suspendisse potenti. Nam commodo suscipit quam.

  Curabitur a felis in nunc fringilla tristique. Quisque id mi. Lorem ipsum dolor,
```