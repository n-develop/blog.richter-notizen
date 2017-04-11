Title: Branch-Namen in git-Commit-Messages
Published: 2017-04-03
Image: ../images/blog-title-10.jpg
Tags: [git, version control, branching]
---

Ich bin ein großer <a href="https://www.mercurial-scm.org/" target="_blank">Mercurial</a>-Fan. Nicht zuletzt, weil jeder Commit weiß, zu welchem Branch er gehört. Ja, das ist natürlich auch ein Overhead, aber den nehme ich gerne in Kauf.
In meinen git-Repositories fehlt mir diese Info ziemlich regelmäßig. Glücklicherweise bin ich vor einigen Tagen über einen Beitrag auf <a href="https://dev.to/" target="_blank">dev.to</a>, zum Thema "<a href="https://dev.to/ebud7/how-to-customise-your-git-commit-message-" target="_blank">How to customise your git commit message</a>" gestolpert. 

An dieser Stelle eine kurze Werbung für diese dev.to-Plattform: dev.to ist eine von <a href="https://twitter.com/bendhalpern" target="_blank">Ben Halpern</a> gegründete Community. Die offizielle Beschreibung lautet wie folgt:

> Where programmers share ideas and help each other grow. All developers are welcome to submit stories, tutorials, questions, or anything worth discussing

Eine ganz klare Empfehlung: Schaut euch dort auf jeden Fall einmal um. Über <a href="https://github.com/thepracticaldev" target="_blank">den offiziellen Twitter-Account</a> wird man immer über interessante Beiträge informiert.

## "How to customise your git commit message"

Der oben genannte Artikel zeigt, wie man mit <a href="https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks" target="_blank">git-hooks</a> eine automatisierte Anpassung der Commit-Messages erreichen kann. Das konkrete Beispiel beschreibt, wie man den Namen des aktuellen Commits an den Anfang der Commit-Message hängt.

<blockquote class="twitter-tweet" data-lang="de">
	<p lang="en" dir="ltr">
		How to customise your git commit message 🚀<br>
		{ author: <a href="https://twitter.com/ebud7">@ebud7</a> }
		<a href="https://t.co/cjPuBh4Fxu">https://t.co/cjPuBh4Fxu</a>
	</p>
	&mdash; The Practical Dev (@ThePracticalDev) 
	<a href="https://twitter.com/ThePracticalDev/status/845685838793129984">25. März 2017</a>
</blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

Somit hat jeder Commit endlich die Info, zu welchem Branch er gehört. Die Einleitung klang super und mit Freude habe ich mir die Schritt-für-Schritt-Anleitung durchglesen. Doch schon früh wurde meine Euphorie gedämpft...

## git, hooks und bash

Der genutzte git-Hook ist ein Shell-Script. Na toll. :pensive: Ich als .NET-Entwickler bin natürlich auf Windows unterwegs. Einen kurzen Moment wollte lang habe ich mit dem Gedanken gespielt, einfach den Browser-Tab wieder zu schließen. Aber ich habe mich dafür entschieden, den Artikel erstmal zu Ende zu lesen. Der Artikel hat mich dann dazu angespornt mal zu googlen, wie ich das Ganze auch auf Windows zum laufen bekomme.

Und wer hätte es gedacht: Das Shell-Script läuft genauso wie es ist auch auf Windows. Denn das "Git for Windows" führt die Hooks auf dem Windows-Port der Bash-Shell aus.

Somit konnte ich das Tutorial tatsächlich ohne Anpassungen testen. Und es funktioniert. :+1:

## Meine Konfiguration

Da ich eine leicht andere Konfiguration nutze, als sie im dev.to-Artikel beschrieben ist, stelle ich diesen hier auch kurz vor.
Der oben genannte Artikel geht davon aus, dass der Hook bei jedem Entwickler eines Projektes neu eingerichtet werden muss. Um einen kleinen Konfigurationsaufwand pro Entwickler-Maschine kommen wir mit meiner Lösung zwar auch nicht herum, jedoch muss nur eine Zeile in einer Konfigurationsdatei hinzugefügt werden.

### 1. Den Hook anlegen

Standardmäßig liegen die Hooks unter `.git/hooks` im Projekt-Root. Da ich diesen Hook natürlich auch mit allen anderen Entwicklern des Projekts teilen will, möchte ich diesen aber gerne mit einchecken. Daher legen wir einfach einen `hooks`-Ordner im Projekt-Root an. Dort drin erstellen wir eine Datei namens `prepare-commit-msg`.
Die Datei muss exakt diesen Namen tragen, da der Name definiert wann und mit welchen Parametern der Hook aufgerufen wird.

In die Datei fügen wir folgenden Inhalt ein:

```sh
#!/bin/sh

if [ -z "$BRANCHES_TO_SKIP" ]; then
  BRANCHES_TO_SKIP=(master)
fi

BRANCH_NAME=$(git symbolic-ref --short HEAD)
BRANCH_NAME="${BRANCH_NAME##*/}"

BRANCH_EXCLUDED=$(printf "%s\n" "${BRANCHES_TO_SKIP[@]}" | grep -c "^$BRANCH_NAME$")
BRANCH_IN_COMMIT=$(grep -c "\[$BRANCH_NAME\]" $1)

if [ -n "$BRANCH_NAME" ] && ! [[ $BRANCH_EXCLUDED -eq 1 ]] && ! [[ $BRANCH_IN_COMMIT -ge 1 ]]; then 
  sed -i.bak -e "1s/^/[$BRANCH_NAME] /" $1
fi
```

Diese Datei können wir nun mit in das Repository einchecken und so für alle Entwickler verfügbar machen.

### 2. Das neue Hooks-Verzeichnis nutzen

Um für dieses konkrete Projekt nun nicht `.git/hooks` sondern einfach `hooks` als Quelle für die Hooks zu nutzen öffnet man die Datei `.git/config` und fügt folgende Zeile in der Sektion `[core]` hinzu:

```
[core]
hooksPath = hooks/
```

Das war auch schon alles. Dem nächsten Commit-Message wird nun automatisch der Branch-Name in eckigen Klammern vorangestellt.

Viel Spaß beim Hinterherprogrammieren.