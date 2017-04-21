Title: Systemweite Mutexe zur Synchronisierung von Prozessen
Published: 2017-04-21
Image: ../images/blog-title-7.jpg
Tags: [csharp, mutex]
---

Hin und wieder hat man das Problem, dass bestimmte Code-Abschnitte nicht zeitgleich laufen dürfen.
Dies kann innerhalb einer Anwendung, aber auch systemweit sein. So könnte es sein, dass das zwei verschiedene Prozesse auf die gleiche
Ressource zugreifen wollen. Dies muss in manchen Fällen synchronisiert werden, da es Ressourcen gibt, die keinen zeitgleichen Zugriff
zulassen. Genau für diese Fälle gibt es Mutexe.

Schauen wir uns dafür am besten direkt ein Beispiel an. Nehmen wir an, dass wir eine Methode `UseResource()` haben, die eine
Ressource nutzt, dessen Zugriff systemweit synchronisiert werden soll. Die Nutzung des Mutex verpacken wir in der Methode
`MutexMagic()`.

```csharp
private static Mutex _mutex = null;

public void MutexMagic()
{
    _mutex = new Mutex(false, "My.Special.Resource");
    bool lockTaken = false;

    try
    {
        lockTaken = _mutex.WaitOne();
         if (lockTaken)
         {
             UseResource();
         }
    }
    finally
    {
        if (lockTaken)
        {
            _mutex.ReleaseMutex();
        }
    }
}
```

Über `new Mutex(false, "My.Special.Resource")` legen wir eine Referenz auf einen Mutex mit dem Namen **My.Special.Resource** an.
Durch die Angabe des Namens handelt es sich automatisch um einen systemweiten Mutex. Brauchen wir einen Mutex um
Quellcode innerhalb einer Anwendung (z.B. bei der Nutzung von Threads) zu synchronisieren, kann der Name auf weggelassen werden.
Der erste Parameter (**false**) besagt, dass nicht sofort versucht werden soll den Mutex in Anspruch zu nehmen. Dies wird
einige Zeilen später über `_mutex.WaitOne()` versucht. Diese Methode gibt einen boolschen Wert zurück, der zeigt, ob der Mutex
gelockt werden konnte oder nicht.

Außerdem erstellen wir eine Variable `lockTaken`, in der wir speichern, ob der Mutex in Anspruch genommen werden konnte oder nicht. 
Diese brauchen wir spätestens im `finally`-Block, um zu prüfen, ob der Mutex wieder freigegeben werden muss.

:::{.alert .alert-warning}
WICHTIG! Jeder Mutex, der erfolgreich gelockt werden konnte **MUSS** im Anschloss über `ReleaseMutex()` wieder freigegeben werden.
Dies gilt insbesondere für systemweite Mutexe, da der Mutex ansonsten gesperrt bleibt und von keinem anderen Prozess genutzt werden kann.
:::

Ansonsten ist das Beispiel selbsterklärend und wenig überraschend. Sollten noch Fragen offen sein, nutzt gerne die Kommentarfunktion.

Happy Coding!