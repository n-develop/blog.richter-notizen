Title: Auto-Scroll mit jQuery
Published: 2017-04-23
Image: ../images/blog-title-8.jpg
Tags: [javascript, jquery]
---

Vor ein paar Tagen hatte ich mir vorgenommen, eine Auto-Scroll-Funktionalität auf einer Webseite einzubauen.
In einem `div` werden dort nach und nach alle druchgeführten Schritte eines Prozesses eingefügt. Oderhalb des Contents findet sich
ein sticky Header. Im Süden entsprechend ein sticky Footer.

Die gesamte Website mit Hilfe von JavaScript zum Ende zu scrollen, ist nicht sonderlich schwierig. Aber hier haben wir durch den
sticky Footer den Fall, dass die Website ja insgesamt nur so groß ist wie unser Browser. Was gescrollt werden muss ist,
ist das `div` in der Mitte.

Das HTML-Gerüst sieht, etwas vereinfacht, so aus:

```html
<div id="header">
  <!-- Navigation und so weiter... -->
</div>
<div id="content">
  <div id="scrolling">
    <!-- Hier kommt der wachsende Content rein -->
  </div>
</div>
<div id="footer">
  <!-- Copyright und verschiedene Links. -->
</div>
```

Die CSS-Styles sind ebenfalls recht übersichtlich und sehen wie folgt aus:

```css
#header,#footer,#content {
  position:absolute;
  right:0;
  left:0;
  background:#F00;
}

#header {
  top:0;
  height:100px;
}

#footer {
  bottom:0;
  height:50px;
}

#content {
  top:100px;
  bottom:50px;
  background:#FFF;
}

#scrolling {
  height:100%;
  overflow:auto;
}
```

So, nun wird es erst so richtig interessant. Kommen wir zum JavaScript.
Fassen wir nochmal kurz die Funktionalität zusammen.

Der Content des `div` mit der ID `scrolling` erweitert sich nach und nach. Da der aktuellste Eintrag immer am Ende eingefügt wird,
wollen wir natürlich auch immer bis zum letzten Eintrag scrollen, sobald ein neuer Eintrag angefügt wird.

Wir erstellen also eine Funktion `scrollToBottom()`, die wir immer aufrufen können, wenn ein neuer Eintrag angefügt wird.
Diese Funktion könnte wie folgt aussehen:

```javascript
scrollToBottom() {
    var content = $('#scrolling');
    var scrollHeight = content[0].scrollHeight;
    content.scrollTop(scrollHeight);
}
```

Zuerst ermitteln wir die Gesamthöhe des `div`-Elements, indem wir `scrollHeight` auf dem DOM-Element aufrufen. Wichtig ist, dass
wir die Property nicht direkt auf dem jQuery-Objekt aufrufen können, sondern mit Hilfe von `[0]` auf das DOM-Objekt zugreifen.

:::{.alert .alert-info}
Wir müssen an dieser Stelle unbedingt `scrollHeight` und nicht `offsetHeight` oder die jQuery-Funktion `height()` benutzen,
da die zuletzt genannten Funktionen die Höhe des sichtbaren Bereiches liefern. Wir wollen allerdings die Gesamthöhe
wissen. Diese erhalten wir über `scrollHeight`.
:::

Über die Funktion `scrollTop()` scrollen wir nun einfach das `div` bis zum Ende.

Es gibt aber noch eine kleine Anpassung, um die Benutzerfreundlichkeit zu erhöhen. Es kann natürlich auch vorkommen, dass ein User
eine Zeit lang nicht immer den letzten Eintrag sehen will, sondern sich, aus welchem Grund auch immer, einen Eintrag in der Mitte
näher anschauen will. In diesem Fall wäre es sehr störend, wenn bei jedem Einfügen das `div` bis nach unten gescrollt wird.

Es soll also nur gescrollt werden, wenn das `div` bereits bis zum Ende gescrollt ist. Dazu fragen wir zuerst ab, ob wir uns bereits am
Ende des `div`s befinden. Wir bauen eine kleine Toleranz von 50 Pixeln ein, damit der User nicht immer bis zum letzten Pixel runter
scrollen muss. Nur wenn wir uns innerhalb dieser Toleranz befinden, scrollen wir das `div` weiter nach unten. Die neue Version
der Funktion sieht jetzt so aus.

```javascript
scrollToBottom() {
    var content = $('#scrolling');
    var currentBottom = content.scrollTop() + content.height();
    var scrollHeight = content[0].scrollHeight;

    if (currentBottom >= scrollHeight - 50) {
        content.scrollTop(scrollHeight);
    }
}
```

In diesem jsFiddle könnt ihr euch das Ganze einmal live in Aktion ansehen.
[https://jsfiddle.net/vLk76j8m/2/](https://jsfiddle.net/vLk76j8m/2/)
Klickt einfach wiederholt auf den Button und fügt beliebig viele neue Einträge ein.

Viel Spaß beim hinterherprogrammieren.