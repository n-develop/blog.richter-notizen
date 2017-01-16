Title: Ein eigener Telegram-Bot als Notification-Service
Published: 2016-10-04
Tags: ["Bots", "Telegram"]
Image: ../images/blog-title-11.jpg
---
Ich nutze bereits seit einiger Zeit [Telegram](https://telegram.org/) als meinen primären Messenger.
WhatsApp benutze ich tatsächlich nur noch für eine handvoll Freunde. Der Großteil meiner Freundeskreises und meine komplette Familie
ist inzwischen über Telegram zu erreichen.

Telegram bietet bereits seit einiger Zeit Bots an und in diesem Rahmen natürlich auch die Möglichkeit eigene Bots zu erstellen.
Bis vor ein paar Tagen hatte ich allerdings keinen konkreten Anwendungsfall um für mich persönlich einen Bot einzurichten.
Nachdem ich mich aber etwas mit der Thematik befasst habe, wurde mir schnell klar, dass ich den Bot für eigene Benachrichtigungen nutzen könnte.

Welche Schritte nötig sind um einen solchen Bot zu erstellen und wie ich meine Benachrichtigungen umgesetzt habe, möchte ich im Folgenden zeigen<!-- Read More -->.

## Schritt 1: Telegram installieren

Als aller erstes sollte man natürlich [Telegram](https://telegram.org/) installieren. Wer Lust und Zeit hat, sollte sich
ruhig mal mit den Möglichkeiten von Telegram auseinandersetzen. Der Dienst bietet deutlich mehr Features als z.B. WhatsApp.

Nach der Installation und Registrierung können wir bereits unseren eigenen Bot erstellen.

## Schritt 2: Bot erstellen

Um einen eigenen Bot zu erstellen, müssen wir mit einem anderen Bot interagieren. Dieser nennt sich passenderweise "BotFather".
Um mit dem BotFather Kontakt aufzunehmen, sucht man einfach in der Suchleiste nach "@botfather". Nachdem man den Chat geöffnet hat, beginnt man mit
`/start` die Kommunikation mit dem BotFather.

Nach einer freundlichen Begrüßung durch den BotFather sendet man einfach `/newbot` und folgt daraufhin den Aufforderungen des BotFathers.
Als erstes muss man dem Bot einen Namen geben. Dies ist der Name, der als Name in der Kontaktliste angezeigt wird.

Als nächstes möchte der BotFather einen "Username" von euch wissen. Dieser muss einzigartig sein, da er in Telegram darüber öffentlich ansprechbar ist.
Wichtig ist dabei, dass der Username auf "bot" endet. Als Sonderzeichen sind ausschließlich Unterstriche erlaubt.

Hat man diese beiden Fragen des BotFathers beantwortet, ist man auch schon fertig. Nun bekommt man einen Token, den man für die Nutzung der API braucht.

## Schritt 3: Chat ID ermitteln

Wir wollen den Bot in diesem Post ausschließlich für unsere privaten Einsatz nutzen. Er muss die Nachrichten also ausschließlich an meinen eigenen Account
senden.

Werfen wir einen kurzen Blick in die [Dokumentation](https://core.telegram.org/bots/api#sendmessage)
der `sendMessage`-Methode, die wir später für unsere Nachrichten benutzen wollen. Die Dokumentation verrät uns, das es beim Aufruf der
Methode zwei Pflichtfelder gibt. Zum einen natürlich den Text, der verschickt werden soll, zum anderen die Chat-ID, die den/die Empfänger definiert.

Diese Chat-ID gilt es nun zu ermitteln. Dazu sendet man einfach eine Nachricht an den zuvor erstellten Bot. Dazu sucht man in Telegram nach
dem, in Schritt 2 festgelegten Username.

Sobald wir die Nachricht an den Bot versendet haben, müssen wir diese nur noch im Namen des Bots abrufen. Dazu rufen wir die `getUpdates`-Methode der
[Bot-API](https://core.telegram.org/bots/api#getupdates) auf.

Dazu reicht der Aufruf einer URL mit entsprechenden GET-Parametern. Die URL baut sich dabei immer wie folgt zusammen.

`https://api.telegram.org/bot[BOT-TOKEN]/[METHODE]`

Wenn wir nun annehmen, dass unser Token, den wir vom BotFather zugewiesen bekommen haben, `ABCDEFG` ist, sieht unsere URL für den
Aufruf der `getUpdates`-Methode wie folgt aus:

`https://api.telegram.org/botABCDEFG/getUpdates`

Die Antwort vom Server müsste nun ungefähr so aussehen:

```json
{
	"ok": true,
	"result": [
		{
			"update_id": 12345678,
			"message": {
				"message_id": 12,
				"from": {
					"id": 3101948,
					"first_name": "Lars",
					"last_name": "Richter",
					"username": "verrate_ich_nicht"
				},
				"chat": {
					"id": 3101948,
					"first_name": "Lars",
					"last_name": "Richter",
					"username": "verrate_ich_nicht",
					"type": "private"
				},
				"date": 1475352901,
				"text": "Test"
			}
		}
	]
}
```

Wir haben diesen Aufruf gemacht, um an die `Chat_ID` zu kommen, die wir für unseren zukünftigen Bot-Nachrichten brauchen.
Diese findet sich, wenig überraschend im Feld `id` des `chat`-Objektes, der oben gezeigten Antwort vom Server.

## Schritt 4: Testlauf

Das war auch schon alles, was wir an Vorbereitung brauchen. Nun können wir, ebenfalls über einen einfachen URL-Aufruf, Nachrichten im Namen des Bots
versenden. Dazu nutzen wir die `sendMessage`-Methode der Bot-API. Diese bekommt als Parameter die Chat-ID und den zu sendenden Text. Dieser muss
natürlich URL-encoded sein.

Der folgende Aufruf sendet "Hello World" an die oben ermittelte Chat-ID.

`https://api.telegram.org/botABCDEFG/sendMessage?chat_id=3101948&text=Hello+World`

Nach dem Aufruf der URL sollte ihr unverzüglich eine Benachrichtigung von eurem Telegram-Client, über eine neue Nachricht bekommen.

## Fazit

Da alle Methoden einfach über eine URL zu nutzen sind, könnt ihr euch nun zu beliebigen Events einfach benachrichtigen lassen.

So könnte man sich vom PowerShell-Script für das Deployment unserer Webanwendung über Erfolg oder Misserfolg benachrichtigen lassen.

```ps
# Succeeded
curl "https://api.telegram.org/botABCDEFG/sendMessage?chat_id=3101948&text=Deployment+succeeded"

# Failed
curl "https://api.telegram.org/botABCDEFG/sendMessage?chat_id=3101948&text=Deployment+failed"
```

Natürlich können wir uns auch von einer beliebigen .NET-Anwendung per Telegram über Events benachrichtigen lassen...

```csharp
public void SendNotificationViaTelegram(string notificationMessage)
{
    var telegramNotification = WebRequest.Create(
        "https://api.telegram.org/botABCDEFG/sendMessage?chat_id=3101948&text="
	    + HttpUtility.UrlEncode(notificationMessage));
    telegramNotification.GetResponse();
}

```

Wie ihr gesehen habt, ist die Nutzung der Telegram-Bot-API recht einfach. Ihr solltet aber aufpassen, dass niemand an euren Bot-Token kommt, da
dieser die einzige Authentifizierung ist, die am Webservice der Bot-API existiert.

Nun wünsche ich euch viel Spaß beim Nachmachen. Hinterlasst mir gerne einen Kommentar, wofür ihr euren Telegram-Bot einsetzt.