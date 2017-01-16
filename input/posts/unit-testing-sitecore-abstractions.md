Title: Unit-Testing mit Sitecore.Abstractions
Published: 2017-01-01
Tags: ["Unit-Testing", "TDD", "Sitecore"]
Image: ../images/blog-title-3.jpg
---
Ganz zu Beginn meiner "Sitecore-Karriere", habe ich ein interessantes Gespräch mit einem Arbeitskollegen zum Thema "Unit-Tests für Sitecore" geführt.
Nach einiger Zeit fiel die Aussage "Natürlich ist es theoretisch möglich Sitecore-Systeme mit Unit-Tests zu testen,
aber in der Praxis ist das einfach nicht möglich, da es mit viel zu hohem Aufwand verbunden ist.".
Diese Aussage hat mich dann doch ein bisschen schockiert. Aber ich war noch neu im Sitecore-Umfeld und wollte mich nicht zu weit aus dem Fenster lehnen.

Nach mehr als 1,5 Jahren intensiver Arbeit mit Sitecore kann ich sagen: "Natürlich ist es auch in der Praxis möglich, Unit-Tests für Sitecore-Systeme
zu schreiben."
Wie kam der Kollege also zu seiner Aussage? Ich habe da ein paar Vermutungen und entsprechende Lösungsansätze.

## Problem: Statische Methoden der Sitecore-API

Ein großes Problem bei dem Versuch Unit-Tests für Sitecore-Systeme zu schreiben, sind die vielen statischen Methoden der Sitecore-API.
Typische Aufrufe sind `Sitecore.Configuration.Settings.GetSetting(settingsName);` oder `LinkManager.GetItemUrl(item);`.
Diese Methoden können von den meisten Isolation Frameworks wie Moq oder NSubstitute nicht "gemockt" werden. Somit ist jede Methode,
die eine dieser statischen API-Methoden benutzt, erstmal untestbar<!-- Read More -->.

## Lösung: Sitecore.Abstractions

Diese Problematik ist Sitecore sehr gut bekannt. Und genau deswegen wurde mit Sitecore 7.5 eine neue Library namens `Sitecore.Abstractions` ausgeliefert.
Darin enthalten ist zum Beispiel das Interface `ISetting`, welches alle bekannten Methoden der bisherigen `Settings`-Klasse definiert.
Im gleichen Namespace findet sich auch die entsprechende Implementierung namens `SettingsWrapper`.
Wie der Name schon vermuten lässt, wrappt die Klasse zwar nur die Aufrufe der statischen Methoden, die Abstraktion ermöglicht es uns nun aber auch,
die Implementierung in unseren Unit-Tests durch eine eigene zu ersetzen. Dazu lassen wir uns einfach eine Implementierung des `ISettings`-Interfaces,
per Constructor Injection in unsere Klasse hereinreichen.

```csharp
public class MyCoolModule
{
  private ISettings _settings;
  public MyCoolModule(ISettings settings)
  {
    _settings = settings;
  }

  public string DoSettingsStuff()
  {
    var isEnabled = _settings.GetBoolSetting("Integration.Enabled", false);
    /*
    * insert more code here
    */
  }
}
```

Bis einschließlich Sitecore 8.1 ist der Namespace `Sitecore.Abstractions` allerdings doch sehr übersichtlich.
Schaut man sich die [Release Notes zu Sitecore 8.2](https://dev.sitecore.net/Downloads/Sitecore%20Experience%20Platform/82/Sitecore%20Experience%20Platform%2082%20Initial%20Release/Release%20Notes) an,
findet man im Bereich **Breaking Changes** folgende Aussage:

> ​More than 45 abstractions (namespace: Sitecore.Abstractions) introduced to replace static APIs.
> The static APIs are still available but will be phased out over the next releases.

Wer also bisher keinen Grund gefunden hat auf Sitecore 8.2 zu upgraden, hat spätestens jetzt einen guten Grund.

## Pre-7.5-Lösung

Aber was tun, wenn man weder Sitecore 8.2, oder auch nur Sitecore 7.5 zur Verfügung hat? Was tun, ohne Sitecore.Abstractions?
Die Lösung ist einfach. Man führt diese Abstraktionsschicht selbst ein.

Beispiel `LinkManager.GetItemUrl(item)`:

```csharp
public interface IMySettings
{
  string GetItemUrl(Item item);
}

public class MySettings : IMySettings
{
  public string GetItemUrl(Item item)
  {
    return LinkManager.GetItemUrl(item);
  }
}
```

Der Code ist einfach. Wir erstellen ein einfaches Interface (`IMySettings`), welches genau die statischen Methodenaufrufe kapselt,
die wir für unsere Funktionalität brauchen. Passend dazu erstellen wir dann natürlich eine Implementierung (`MySettings`), die nur
die statischen Methoden aufruft. Hier ist darauf zu achten, dass möglichst keine weitere Funktionalität in die Klasse übernommen wird.
Denn in diesem Fall müssten wir auch diese Methoden testen, was durch die statischen Sitecore-Methoden wieder schwierig wäre.

Durch die Einführung und Nutzung des Interfaces `IMySettings`, können wir nun für unsere Tests eine eigene Implementierung schreiben,
die die gewünschten Rückgabewerte liefert.
