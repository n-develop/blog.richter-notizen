Published: 2016-09-01
Title: Code Kata - Bowling Game
Tags: ["code kata", "tdd"]
Image: ../images/blog-title-2.jpg
---
Der regelmäßige Leser dieses Blogs weiß, dass ich ein großer Fan des Test-Driven Developments bin.
Aber wie alles im Leben muss man auch TDD und andere Techniken üben. Aber wie übt man TDD am besten?
Meiner Erfahrung nach ist es schwierig ein Buch zum Thema zu lesen und die beschriebenen Techniken sofort produktiv einzusetzen.
Üben sollte man also an kleinen, einfachen "Versuchsobjekten". Für diesen Zweck gibt es sogenannte *Code Katas*.

## Was genau ist eine Kata?
Der Begriff "Kata" kommt ursprünglich aus den japanischen Kampfkünsten. Dort beschreibt er meist einen einstudierten Kampf gegen
einen imaginären Gegner. Die häufige Wiederholung dieser Kata dient also dazu die Bewegungsabläufe zu verinnerlichen.

Eine Code Kata funktioniert ähnlich. Das Ziel dabei ist eben **nicht** die Aufgabe mit dem perfekten Ergebnis zu lösen.
Es geht viel mehr darum, die eingesetzten Techniken zu verinnerlichen. Ein gutes Beispiel ist<!-- Read More --> hier die Nutzung von TDD.
Man übt also an einem kleinen, abgeschlossenen Beispiel die Umsetzung mittels TDD.

## Let's go Bowling
Eine Code Kata, die ich heute gerne vorstellen möchte, ist die *Bowling Game Kata*.
In der *Bowling Game Kata* geht es darum die Ergebnisse von Bowling-Spielen zu ermitteln.
Zur Auffrischung hier kurz nochmal die Regeln:
* Jedes Spiel besteht aus 10 Frames.
* In jedem Frame hat der Spieler bis zu zwei Würfe um alle 10 Pins abzuräumen.
* Schafft der Spieler es nicht mit zwei Würfen alle 10 Pins abzuräumen, ist die Summe der abgeräumten Pins des Ergebnis für diesen Frame.
* Schafft der Spieler die 10 Pins mit genau Würfen (genannt **Spare**), ist das Ergebnis des Frame 10 plus die Anzahl der abgeräumten Pins mit dem nächsten Wurf.
* Schafft der Spieler die 10 Pins mit einem Wurf (genannt **Strike**), ist das Ergebnis des Frame 10 plus die Anzahl der abgeräumten Pins mit den nächsten zwei Würfen.
* Der 10. Frame hat bis zu drei würfen, um den Möglichen Bonus für einen Strike oder Spare ermitteln zu können.
* Die Summe aller Frame-Ergebnisse ist das Endergebnis für den Spieler.

## Let's code
### Eine "Pumpe" nach der anderen
Der wohl leichteste Fall, ist jedes Mal eine "Pumpe" (oder "Rinne") zu werfen. Dazu schreiben wir folgenden Test.

``` csharp
[Test]
public void GutterGame() 
{
    var bowling = new BowlingGame();

    for (int i = 0; i < 20; i++) 
    {
        bowling.Roll(0);
    }

    Assert.AreEqual(0, bowling.Score());
}
```

Nun legen wir alle nötigen Klassen und Methoden an, damit der Test erfolgreich durchläuft. Meine Lösung sieht dabei wie folgt aus:

```csharp
public class BowlingGame
{
    public int Score()
    {
        return 0;
    }

    public void Roll(int roll)
    {
    }
}
```

Der Test ist grün. Der nächste Test steht an.

### Keine Sonderfälle
Nun sollten wir testen, ob alle Würfe korrekt zusammengezählt werden. Damit die Sache einfach bleibt, werfen wir weder einen **Spare**, noch einen **Strike**.

```csharp
[Test]
public void NoSpareNoStrike()
{
    var bowling = new BowlingGame();

    for (int i = 0; i < 20; i++)
    {
        bowling.Roll(3);
    }

    Assert.AreEqual(60, bowling.Score());
}
```

Der Test schlägt fehl. Ran an die Implementierung.

```csharp
public class BowlingGame
{
    private int _pins;

    public int Score()
    {
        return _pins;
    }

    public void Roll(int roll)
    {
        _pins += roll;
    }
}
```
Alle Tests sind wieder grün. Auf zum nächsten Test!

### Just "Spare". No "Ribs".
Nun wenden wir uns der Berechnung eines Spares zu. Unser Test wird im ersten Frame eine 7 und eine 3 werfen. Der Spare-Bonus aus dem nächsten Frame soll eine 5 sein. Alle restlichen Würfe sind "Pumpen". Am Ende hat der Spieler also 20 Punkte.
* Erster Frame: 10 + 5 (Spare Bonus aus dem nächsten Wurf)
* Zweiter Frame: 5

```csharp
[Test]
public void SingleSpare()
{
    var bowling = new BowlingGame();

    bowling.Roll(3);
    bowling.Roll(7);
    bowling.Roll(5);

    for (int i = 0; i < 17; i++)
    {
        bowling.Roll(0);
    }

    Assert.AreEqual(20, bowling.Score());
}
```

Eine mögliche Implementierung für die "Spare-Logik" sieht wie folgt aus.

```csharp
public class BowlingGame
{
    private readonly int[] _rolls = new int[21];
    private int _currentRoll = 0;

    public int Score()
    {
        var score = 0;

        for (int roll = 0; roll < _rolls.Length - 1; roll += 2)
        {
            if (_rolls[roll] + _rolls[roll + 1] == 10)
            {
                score += 10 + SpareBonus(roll);
            }
            else
            {
                score += _rolls[roll] + _rolls[roll + 1];
            }
        }

        return score;
    }

    public int SpareBonus(int roll)
    {
        return _rolls[roll + 2];
    }

    public void Roll(int roll)
    {
        _rolls[_currentRoll] = roll;
        _currentRoll++;
    }
}
``` 

Die Tests sind wieder grün. Weiter geht's.

### Strike!
Die Überschrift spricht für sich selbst. Let's go!

```csharp
[Test]
public void SingleStrike()
{
    var bowling = new BowlingGame();

    bowling.Roll(10);
    bowling.Roll(3);
    bowling.Roll(5);

    for (int i = 0; i < 16; i++)
    {
        bowling.Roll(0);
    }

    Assert.AreEqual(26, bowling.Score());
}
```

Der Test ist selbsterklärend, wenn man mit den Bowling-Regeln vertraut ist. Soweit so gut. Spannender wird es nun bei der Implementierung. Der erste Frame hat nun nämlich nur noch einen Wurf und nicht zwei. Daraus ergeben sich einige Möglichkeiten zur Umsetzung. Meine heutige Umsetzung sieht so aus:

```csharp
public class BowlingGame
{
    private readonly int[] _rolls = new int[21];
    private int _currentRoll = 0;

    public int Score()
    {
        var score = 0;
        var roll = 0;

        for (int frame = 0; frame < 10; frame++)
        {
            if (IsStrike(roll))
            {
                score += 10 + StrikeBonus(roll);
                roll++;
            }
            else if (IsSpare(roll))
            {
                score += 10 + SpareBonus(roll);
                roll += 2;
            }
            else
            {
                score += _rolls[roll] + _rolls[roll + 1];
                roll += 2;

            }
        }

        return score;
    }

    private bool IsSpare(int roll)
    {
        return _rolls[roll] + _rolls[roll + 1] == 10;
    }

    private bool IsStrike(int roll)
    {
        return _rolls[roll] == 10;
    }

    private int StrikeBonus(int roll)
    {
        return _rolls[roll + 1] + _rolls[roll + 2];
    }

    public int SpareBonus(int roll)
    {
        return _rolls[roll + 2];
    }

    public void Roll(int roll)
    {
        _rolls[_currentRoll] = roll;
        _currentRoll++;
    }
}
```

Alle Tests sind grün. Was will man mehr?

### Perfekt soll es sein

Ein interessanter Test ist zum Beispiel noch das **perfekte Spiel**. Dabei wird mit jedem Wurf ein Strike erzielt. Da wir 10 Frames haben und im 10. Frame die Möglichkeit den Strike-Bonus zu bekommen, kommen wir auf insgesamt 12 Würfe.

```csharp
[Test]
public void PerfectGame()
{
    var bowling = new BowlingGame();

    for (int i = 0; i < 12; i++)
    {
        bowling.Roll(10);
    }

    Assert.AreEqual(300, bowling.Score());
}
```

Soweit so gut. Lassen wir die Tests laufen. Wie immer kümmern wir uns erst danach um die Implementierung.

Aber wer hätte das gedacht? Alle Tests sind grün. :-)

## Das reicht uns erstmal
Das soll uns als kleines Beispiel reichen. Nun ist es an euch vielleicht noch weitere Tests hinzuzufügen. Es gibt sicher noch ein paar interessante Grenzfälle. Oder probiert doch beim nächsten Mal eine andere Implementierung aus. Meine Empfehlung ist, regelmäßig die eine oder andere Code Kata zu machen. Das ist immer eine nette Fingerübung und festigt beispielsweise eure TDD-Skills.