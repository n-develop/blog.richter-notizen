Title: n-Develop Reworked - Komplett-Überarbeitung des Blogs
Published: 2016-05-25
Tags: ["asp.net mvc", "csharp", "blog"]
Image: ../images/blog-title-9.jpg
---
Es wurde mal wieder Zeit für was Neues. Wie ihr vielleicht gemerkt habt, sieht es hier mal wieder völlig anders aus.
Nein, es handelt sich nicht um ein neues WordPress-Theme oder einen Umstieg auf Joomla oder ähnliches.
Ich habe in den letzten Wochen mein eigenes kleinen Blog-System entwickelt. Aber fangen wir mal von vorne an...

## Wie es begann
Eine ganze Weile habe ich nach einem schönen, aber verhältnismäßig kleinen Projekt gesucht<!-- Read More -->.
Etwas, das ich in wenigen Arbeitstagen umsetzen könnte. Die Projektsuche field in eine Zeit, in der ich mal wieder
eine gewisse Unzufriedenheit mit WordPress verspürte (das passiert alle 3-4 Monate).
Da lag es irgendwie nah, ein eigenes Blog-System zu entwickeln. Und ja, ich habe mir vorher selbst auch die Frage gesellt
"Wie viele Blog-Systeme braucht die Welt noch?". Die Antwort ist einfach: "Vermutlich keines".
Aber warum dann gerade ein Blog-System? Das wichtigste Argument für mich war: *Weil ich gerade Bock darauf hatte*.
Dazu kam noch der geringe Umfang, der praktische Nutzen und mein Verlangen nach einem Blog-System, welches möglichst simpel
ist (Ich will nur Blog-Posts und Content-Pages erstellen. Mehr nicht.) und Formattierung der Pages und Posts mit Hilfe von
Markdown zulässt.

## It's all about simplicity
Wichtig war mir vor allem eines: Der Blog sollte simpel sein. Diese Eigenschaft sollte sowohl für die Usability, als auch
für den Code gelten. Da ASP.NET MVC als Technologie feststand, schied die ASP.NET MVC-Projektvorlage des Visual Studio aus.
Diese enthält unheimlich viel Code, den man entweder nicht brauchst, oder hätte deutlich einfacher schreiben können.
Der Start war also klar. Die leere ASP.NET Projektvorlage sollte es sein. Völlig nackt ohne überflüssige 
Abhängigkeiten und Referenzen. Somit allerdings auch ohne MVC-Referenzen. MVC musste ich also ebenso konfigurieren, 
wie alles andere auch. Ich wollte für alle nötigen Frameworks eine möglichst schlanke Implementierung finden und nutzen.

Aber was heißt "Simplicity" für die Usability. Für den Besucher meines Blogs heißt das
* schwarze Schrift auf weißem Grund
* schlichtes Design
* einfache Navigation.

Ganz ähnlich sieht es für den Admin/Redakteur aus. Ein Blogt-Post besteht, zumindest in der ersten Version, wirklich nur
aus einem Titel und dem, mit Markdown formattierten Inhalt. Weitere Informationen wie Autor und Erstellungsdatum werden
automatisch eingefügt und können nicht verändert werden.
Auf einer Art *Dashboard* hat man dann einen Überblick über alle Seiten und Posts un dkann diese bearbeiten und löschen.

## Technologie-Stack
Folgende Technologien und Frameworks haben den Weg in dieses Projekt gefunden:
* [ASP.NET MVC](http://www.asp.net/) als Basis
* [Autofac](http://autofac.org) als IoC-Container
* [Entity Framework](https://msdn.microsoft.com/de-de/data/ef.aspx) für den Datenzugriff
* [ASP.NET Identity](http://www.asp.net/identity) zur Benutzerverwaltung
* [xUnit.NET](https://github.com/xunit/xunit) als Test-Framework
* [Boostrap](http://getbootstrap.com/) für das Frontend
* [SQL Server 2014](https://www.microsoft.com/de-de/server-cloud/products/sql-server/overview.aspx) als Datenbank
* [CommonMark.NET](https://github.com/Knagis/CommonMark.NET) für das Markdown-Parsing
* [highlight.js](https://highlightjs.org/) für Syntax-Highlighting der Code-Snippets in den Blog-Posts
* [Microsoft Azure](https://azure.microsoft.com/de-de/) für Hosting der Web-Anwendung und der Datenbank

Auf den ersten Blick könnte man sagen: "So viele Frameworks für so ein kleines Projekt?". Aber alle genannten Frameworks
tragen ihren Teil zur Einfachheit des Quellcodes bei.

## Fazit
Kurz und knapp: "Zufriedenheit durch Einfachheit".

Nach einigen Tagen produktivem Einsatz bin ich noch immer sehr zufrieden. Insbesondere die Markdown-Unterstützung ist mein
persönliches Highlight. Es gibt zwar einen kleine Liste mit zukünftigen Features, aber für den ersten Anlauf
bin ich schon sehr zufrieden.

In den folgenden Wochen werde ich noch ein paar Blog-Posts zu den oben genannten Frameworks veröffentlichen, in denen es
um die manuelle Integration und Konfiguration gehen wird. Denn wie oben schon beschrieben, sind die Projekttemplates
oft mehr als überladen und viel größer als eigentlich nötig.

Nun wisst ihr, warum hier mal wieder alles so anders aussieht. Ich hoffe es gefällt euch und freue mich über jegliches
konstruktives Feedback.

Happy Coding!
