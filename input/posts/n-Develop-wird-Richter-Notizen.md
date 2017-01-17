Title: n-Develop wird Richter-Notizen. Und statisch.
Published: 2017-01-16
Image: ../images/blog-title-1.jpg
---
Neues Jahr, neuer Blog. Oder so ähnlich.
Es war jedenfalls mal wieder Zeit für etwas Neues. Nachdem ich im Mai 2016 von WordPress [auf eine Eigenentwicklung umstieg](/posts/n-develop-reworked.html), war es nun mal wieder an der Zeit etwas neues auszuprobieren.

## n-Develop wird Richter-Notizen

Der Name **n-Develop** hat sich in der Vergangenheit als nicht besonders "schmissig" herausgestellt. Das Motto "Geht ins Ohr. Bleibt im Kopf." trifft doch deutlich weniger zu, als ich es mir seiner Zeit erhofft hatte. 

## Vielleicht was mit "Richter"?
Ein neuer Name sollte es also sein. Und mal wieder stellte sich die Frage: "Wie soll das Kind denn heißen?"
Mir persönlich fällt so eine Entscheidung immer schwer. Man will ja nicht einfach irgendeine Domain. Aber bitte auch nicht zu ausgefallen. Mein im Juni 2016 neu erworbener Nachname bot sich als Domain-Bestandteil an. Nach **sehr viel** herumprobieren [uniteddomains.com](https://www.uniteddomains.com/) musste ich feststellen, dass bereits fast alle brauchbaren Domains mit "Richter" vergeben sind. Nur eine nicht. Richter-Notizen.de.

Klingt gut. Ist gekauft. Und irgendwie trifft es ja den Nagel auf den Kopf.

## Technischer Umbau
Aber nicht nur der Name ist anders. Auch die Technik unter der Haube hat sich grundlegend geändert. Wie oben bereits beschrieben, habe ich im Mai letzten Jahres von WordPress auf eine Eigenentwicklung umgestellt. Aber warum sollte ich die ganze Arbeit jetzt schon wieder über den Haufen werfen?

Aus mehreren Gründen. Ja, mein eigenes kleines Blogsystem war nicht übel. Es konnte eben genau das, was ich brauchte. Aber wer solch ein System nutzt und entwickelt hat, will es natürlich auch weiterentwickeln. Aber mein Hauptfokus sollte eigentlich auf dem Schreiben von Blogposts und nicht auf der Weiterentwicklung liegen.

Außerdem handelte es sich um ein Blogsystem auf Basis von ASP.NET MVC. Das bedeutet natürlich, das man kein billiges Webhosting-Paket nehmen kann. Die meisten vernünftigen ASP.NET-Hostings sind doch nicht gerade billig.
Letzendlich habe ich mich für Hosting in Azure entschieden. Die Performance war allerdings eher mäßig.

### Wyam kommt zur Hilfe
Über einen [Blogpost](http://www.hanselman.com/blog/ExploringWyamANETStaticSiteContentGenerator.aspx) meinen Lieblingsblogs, den [Blog](http://www.hanselman.com/blog) von [Scott Hanselmann](https://twitter.com/shanselman), werde ich auf [Wyam](https://wyam.io/) aufmerksam.

Wyam ist ein sogenannter "static content generator". Auf Basis von Markdown- und Razor-Files werden statische HTML-Dateien generiert, welche dann über einen einfachen Webserver, ganz traditionell ohne weiteres Kompilieren oder ähnliches, an den User ausgeliefert wird.
Aber welche Vorteile bietet das? 
Insbesondere geht es um Performance. Denn warum sollte jedes Mal der Content aus einer Datenbank gelesen und daraus dynamisch eine View generiert werden? Wie oft ändert sich der Content wie in diesem Blog wirklich?

Und es ist natürlich nicht so, dass der Content nur einmal erstellt und danach nicht wieder geändert wird. Es wird nur nicht bei jedem Request generiert, sondern nur dann, wenn er sich auch wirklich ändert.