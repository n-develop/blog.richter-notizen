Title: Mein eigener Time-Tracker
Published: 2017-06-06
Tags: ["timetracking", "csharp"]
Image: ../images/blog-title-12.jpg
---
Es war mal wieder an der Zeit für mich, das Rad neu zu erfinden. Ich habe mich hingesetzt und überlegt, was die Welt unbedingt braucht.
Die Antwort war schnell gefunden. Die Welt braucht einen weiteren Time-Tracker. Und genau deswegen habe ich mich hingesetzt und ein solches Tool geschrieben.
Zugegeben, der Grund war ein anderer. Aber im Großen und Ganzen [war es einfach mal wieder Zeit](http://richter-notizen.de/posts/n-develop-reworked.html) für mich etwas zu machen, 
was schon viele andere vor mir gemacht haben. Aber, wie gewöhnlich, natürlich genau auf meine Bedürfnisse abgestimmt. 

## Jetzt mal im Ernst...

Wie oben schon angedeutet, hatte ich natürlich einen "echten" Grund, dieses Projekt anzugehen. Bei uns in der Firma nutzen wir [YouTrack](https://www.jetbrains.com/youtrack) als
Issue-Tracker. Dort erfassen wir auch unsere Zeiten. Außerdem bin ich im Moment sehr viel beim Kunden, der seinen eigenen Issue-Tracker in Form von
[Jira](http://www.atlassian.com/jira), mitbringt. Da wir auch dort unsere Zeiten erfassen dürfen/müssen, funktioniert unser bewährtes Timer-Tool leider nicht mehr wie bisher.
Wir haben eine firmeninterne WPF-Anwendung, die neben der einfachen Time-Tracking-Funktion viele YouTrack-Funktionen in die Desktop-Anwendung bringt.
Allerdings musste nun auch eine Jira-Integration her. Der Kollege, der den oben genannten YouTrack-Timer bei uns entwickelt hat, hat sehr schnell auch einen Jira-Integration hinzugefügt.
Da wir unsere Zeiten immer komplett in beiden System erfassen mussten, hat er beispielweise sogar eine Funktion implementiert, die automatisch das fehlende "Schwester-Issue" im 
jeweils anderen System angelegt hat. Das reduzierte den manuellen Aufwand natürlich enorm. Es hätte so schön einfach sein können. Aber einige Zeit später kam dann die Ansage,
dass die Zeiten für diesen speziellen Kunden nur noch in Jira und alle anderen Zeiten weiterhin in YouTrack gepflegt werden müssen. Nun musste aus dem "YouTrack-Timer", der
nebenbei auch noch Zeiten in Jira buchen kann, wieder herumgeschraubt werden, damit auch komplett unabhängig gebucht werden kann.
Da der Timer aber eben ein "YouTrack-Timer" ist, der, wie oben beschrieben ziemlich speziell auf YouTrack zugeschnitten war, träumte ich von einer anderen, flexibleren Lösung. 
Außerdem war ich schon immer ein [CLI](https://en.wikipedia.org/wiki/Command-line_interface)-Typ, wenn es um solche Anwendungen geht.

## Klein, erweiterbar und mein

Die Idee für meinen eigenen kleinen Time-Tracker war also geboren. Die Anforderungen waren recht schnell formuliert:

* Es müssen sowohl Zeiten in YouTrack, als auch in Jira (und potenziellen weiteren Systemen) getrackt werden können.
* Es sollte ein Command-Line-Interface sein.
* Es muss die Möglichkeit für "temporäre Tickets" (dazu später mehr) haben.
* Es muss die Möglichkeit bieten, erfasste Zeiten im Nachgang zu korrigieren, bevor diese in YouTrack/Jira gespeichert werden.
* Für den Fall, dass auch andere Leute/Entwickler Interesse an dem Tool bekommen, sollte es auch leicht erweiterbar sein.

Klingt einfach, oder? :smile:

## Das Ergebnis

Mit dem bisherigen Ergebnis bin ich recht zufrieden. Fast alle Punkte sind zu meiner Zufriedenheit gelöst. Sowie ich hier schreibe, ist die YouTrack-Integration zwar noch nicht implementiert,
aber die Jira-Integration war für mich auch der wichtigere Punkt. 

### Die Solution

Aktuell (Stand 11.06.2017) besteht die Solution aus 5 Projekten.

* TicketTimer
    * Dies ist das UI in Form einer `Console Application`
* TicketTimer.Core
    * Hier liegen alle Core-Services und Commands der Anwendung
* TicketTimer.Jira
    * Die Jira-Integration ist in dieses Projekt ausgelagert
* TicketTimer.Youtrack
    * Hier soll zeitnah die Youtrack-Integartion entstehen
* TicketTimer.Core.Tests
    * Hier habe ich mit den Unit-Tests für das Core-Projekt begonnen

Folgende Libraries und Frameworks kommen in der Solution zum Einsatz:

* [Autofac](https://autofac.org/)
    * Der IoC-Container meiner Wahl
* [ManyConsole](https://github.com/fschwiet/ManyConsole)
    * Erleichtert die Erstellung eigener Commands
* [Json.NET](http://www.newtonsoft.com/json)
    * Zum serialisieren und deserialisieren der bisher getrackten Zeiten ins JSON-Format
* [Atlassian.NET SDK](https://bitbucket.org/farmas/atlassian.net-sdk/wiki/Home)
    * Für die Jira-Integration
* [xUnit.NET](https://xunit.github.io/)
    * Framework für Unit-Tests

### Das Command-Line-Interface

Ich habe mir in meinem PowerShell-Profil den Alias `tt` für meinen Ticket-Timer angelegt. So ergeben sich für mich folgende Commands:

Wie der Name schon vermuten lösst, startet man mit `start` die Arbeit an einem Ticket. Der Parameter `-t` gibt dabei den Namen des Tickets an (hier ABC-123). 
Mit `-c` kann optional noch ein Kommentar angegeben werden.

```powershell
tt start -t ABC-123 -c "Mein Kommentar"
```

Die Arbeit am aktuellen Ticket stoppt man - wer hätte es gedacht - mit `stop`.

```powershell
tt stop
```

Hin und wieder möchte man wissen, wie lange der Timer aktuell auf welchem Ticket läuft. Dafür gibt es `status`

```powershell
tt status
```

Alle noch nich in ein Ticket-System übertragenen Zeiten, ruft man sich mit `show` auf.

```powershell
tt show
```

Da es natüralich passieren kann, dass man sich bei einer Ticket-Nummer vertippt, gibt es den `rename`-Command. Mit `-o` gibt man die aktuelle Ticket-Nummer 
und mit `-n` die neue Nummer an. 

```powershell
tt rename -o ABC-123 -n DEF-321
```

Mit dem `jira`-Command überträgt man alle gespeicherten Zeiten an Jira.

```powershell
tt jira
```

## Interesse?

Wer Interesse an dem hier beschriebenen Projekt hat, findet es auf GitHub unter [https://github.com/n-develop/tickettimer](https://github.com/n-develop/tickettimer).
Dort erstelle ich auch Issues für Verbesserungsvorschläge und Ideen die ich so habe. Auch hier dürft ihr euch natürlich gerne bedienen, wenn ihr Bock habt etwas beizutragen.
Die ensprechenden Issues sind mit `help wanted` und/oder [up-for-grabs](http://up-for-grabs.net/) markiert. Sobald ich alle entsprechenden Infos (README.md und CONTRIBUTING.md)
erstellt habe, werde ich auch hin und wieder Issues mit [first-timers-only](http://www.firsttimersonly.com/) bereitstellen. Mal sehen, ob sich ab und an jemand findet, den
man bei seiner ersten Open-Source-Contribution unterstützen kann. :smiley: