Title: Eigene Sitecore-Pipelines entwickeln
Published: 2016-06-04
Tags: ["sitecore", "pipelines"]
Image: ../images/blog-title-4.jpg
---
Wer sich mit Sitecore-Entwicklung befasst, kommt nicht drum herum, sich auch mit den Sitecore-Pipelines zu beschäftigen.
Die Pipelines bieten oft einen guten Einstiegspunkt, wenn man eigene Funktionalität irgendwo einklinken will.
Doch hin und wieder reichen die vorhandenen Pipelines nicht aus. Denn dann möchte man vielleicht selbst Funktionalität
hinzufügen, die von anderen erweiterbar ist.

Aber wie erstellt man eigentlich eine eigene Pipeline? Das ist insgesamt sogar recht einfach.

## Das Kind braucht einen Namen
Wir beginnen damit die Pipeline selbst zu definieren. Dazu erstellt man eine neue .config-Datei unter 
`App_Config/Include`. In unserem Beispiel soll diese Datei `Custom.SamplePipeline.config` heißen. Die Pipeline selbst soll entsprechend `samplePipeline` heißen.<!-- Read More -->

```xml
<configuration>
    <sitecore>
        <pipelines>
            <samplePipeline>
            
            </samplePipeline>
        </pipelines>
    </sitecore>
</configuration>
```

Das soll tatsächlich schon alles sein? Mehr braucht man nicht um eine Pipeline mit Namen `samplePipeline` zu definieren. Man erstellt einen neuen XML-Tag innerhalb der `pipelines`-Tags mit dem Namen der neuen Pipeline.

## Argumente muss man haben
So eine Pipeline erfüllt traditionell ja einen konkreten Zweck. Und für diesen Zweck braucht man meistens ganz konkrete Daten.
Diese werden bei den Sitecore-Pipelines über die `PipelineArgs` übergeben.

Im nächsten Schritt wollen wir über eigene PipelineArgs definieren welche Werte durchgereicht werden.
Dazu erstellen wir eine eigene Klasse, die von der Klasse `Sitecore.Pipelines.PipelineArgs` erbt.

```csharp
using Sitecore.Pipelines;

namespace N.Develop.SitecorePipeline
{
    public class SamplePipelineArgs : PipelineArgs
    {
        public string SomePath { get; set; }
        public int SomeNumber { get; set; }
        public int ResultCode { get; set; }
    }
}
``` 

## Jetzt muss aber auch mal was passieren
Aber so ganz ohne Pipeline-Processor macht so eine Pipeline natürlich keinen Spaß. Unser neuer Pipeline-Processor soll den in `SomeNumber` übergebenen Wert nehmen und verdoppeln. Das Ergebnis wird dann wiederum in die Property `ResultCode` geschrieben.
 
```csharp
public class SampleProcessor
{
    public void Process(SamplePipelineArgs args)
    {
        args.ResultCode = GetCodeFromNumber(args.SomeNumber);
    }
    
    private int GetCodeFromNumber(int number)
    {
        return number * 2;
    }
}
```

Nun haben wir eine Pipeline und einen Processor. Es wird Zeit, die beiden zusammen zu bringen. Dazu gehen wir in die vorhin erstellte `Custom.SamplePipeline.config` und fügen den Processor hinzu.

```xml
<configuration>
    <sitecore>
        <pipelines>
            <samplePipeline>
                <processor type="N.Develop.SitecorePipeline.SampleProcessor, N.Develop.SitecorePipeline" />
            </samplePipeline>
        </pipelines>
    </sitecore>
</configuration>
```
Wie man am obigen Beispiel sehen kann, ist der `processor`-Tag wie folgt aufgebaut:
`<processor type="[NAMESPACE].[CLASSNAME], [ASSEMBLY] />`

Wenn nicht anders angegeben, wird nach einer Methode mit Namen `Process` gesucht. Möchte man die auszuführende Methode anders nennen (warum auch immer...), kann man dies über das `method`-Attribut des `processor`-Tags angeben.

## Rufen Sie nicht uns auf! Wir rufen Sie auf!
So, die Pipeline ist fertig. Nun muss sie nur noch aufgerufen werden. Das ist aber mindestens genauso einfach wie alle anderen Schritte zur Erstellung einer eigenen Pipeline. Gebraucht werden nur 3 Schritte: 
* PipelineArgs erstellen
* Pipeline über `CorePipeline.Run` ausführen
* PipelineArgs auswerten

Den folgenden Code fügt man einfach an der Stelle ein, wo die Pipeline gestartet werden soll.

```csharp
var pipelineArgs = new SamplePipelineArgs { SomeNumber = 4 };
CorePipeline.Run("samplePipeline", pipelineArgs);
Log.Info(pipelineArgs.ResultCode.ToString(), this);
```

Nun sollte man im Log die Meldung `INFO  8` sehen, da wir die übergebene `4` in der Pipeline verdoppeln.

Das war es schon. Viel Spaß beim Erstellen eigener Sitecore-Pipelines.