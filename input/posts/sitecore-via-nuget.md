Title: Sitecore-Binaries via NuGet beziehen
Published: 2017-02-01
Image: ../images/blog-title-1.jpg
Tags: [sitecore, nuget, xunit, fakedb]
---

Seit ein paar Monaten ist es nun endlich soweit: Es gibt die Sitecore-Binaries auch über NuGet.
Die Tage, an denen man zahllose Sitecore-Binaries mit in sein Repository einchecken musste, sind also gezählt.
Am 8. November 2016 veröffentlichte Sitecore
[in dieser Meldung](https://doc.sitecore.net/sitecore_experience_platform/developing/developing_with_sitecore/sitecore_public_nuget_packages_faq),
dass nun viele Sitecore-Packages über einen NuGet-Server zur Verfügung stehen. 
In diesem Blog-Post möchte ich kurz die nötigen Schritte beschreiben und ein kleines Testing-Projekt mit
[Sitecore.FakeDb](https://github.com/sergeyshushlyapin/Sitecore.FakeDb) aufsetzen. Das hat den großen Vorteil, dass man kein vollwertiges Sitecore
hochfahren muss, um erste Erfolge zu sehen. Als Unit-Testing-Framework kommt [xUnit.NET](https://xunit.github.io/) zum Einsatz.

## NuGet-Server in Visual Studio einrichten

Sitecore stellt seine Pakete nicht über den allseits bekannten nuget.org-Server zur Verfügung, sondern über einen hauseigenen NuGet-Server.
Diesen müssen wir erst in Visual Studio konfigurieren, bevor wir die Packages beziehen können. Dazu klickt ihr auf `Tools > Options`
und wählt dort `NuGet Package Manager > Package Sources`.
Hier tragt ihr jetzt eine neue Package Source mit einem beliebigen Namen und dieser URL ein:

> https://sitecore.myget.org/F/sc-packages/api/v3/index.json

![Visual Studio Options](..\images\sitecore-nuget\options.jpg)

:::{.alert .alert-info}
Solltet ihr mit Visual Studio 2012 oder früher unterwegs sein, schaut bitte in den oben angegebenen Sitecore-Artikel für
entsprechend andere URLs.
:::

Das war es auch schon mit der Konfiguration. Nun wollen wir das Test-Projekt anlegen.

## Das Projekt und seine Packages

Nun legen wir ein *Class Library*-Projekt an und fügen das NuGet-Package `Sitecore.FakeDb` hinzu.
**FakeDb** ist ein Unit-Testing-Framework für Sitecore. Es ermöglicht Zugriff auf zuvor definierte Items über die Sitecore-API. Ich möchte hier nicht zu sehr ins
Detail gehen und werde nur die nötigten Eigenschaften des Frameworks erklären.

Neben **FakeDb** brauchen wir auch noch ein Unit-Testing-Framework wie **xUnit.NET** oder **NUnit**. Das macht jeder nach seinem Gusto. Ich nutze, wie oben 
erwähnt xUnit.NET.

Damit wir mit **FakeDb** auch wirklich die Sitecore-APIs nutzen können, müssen wir folgende Sitecore-Binaries referenzieren:

* Sitecore.Nexus
* Sitecore.Kernel
* Sitecore.Logging
* Sitecore.Analytics

Außerdem brauchen wir eine Referenz auf **Lucene.Net**. Die Sitecore-Packages bekommen wir vom Sitecore-Server, während wir **Lucene.Net**, 
genau wie **xUnit** und **FakeDb**, vom altbekannten nuget.org-Server beziehen.

:::{.alert .alert-info}
**Tipp:** Installiert man zum Beispiel das `Sitecore.Analytics`-NuGet-Packages, bekommt man auch einige andere Binaries ausgeliefert,
die als Abhängigkeiten zu diesem Package hinterlegt sind. Dies meisten davon brauchen wir allerdings nicht.
Deshalb gibt es im Grunde jedes Package auch als **NoReference**-Variante. Wählt man diese, bekommt man wirklich nur die eine Binary ausgeliefert.
:::

![Package NoReference](..\images\sitecore-nuget\package-noref.jpg)

Da für **FakeDb** viele "echte" Sitecore-Prozesse durchlaufen werden, brauchen wir auch hier ein License-File. Dieses legen wir, der Einfachheit halber,
ins Hauptverzeichnes unseres Projektes (nicht der Solution, sondern des Projektes).

Hier noch eine Warnung: Wenn ihr, wie ich, Sitecore 8.2 oder höher nutzt, müsst ihr in der `App.config` noch eine kleine Anpassung vornehmen.
Ihr müsst die Zeile

> <sc.variable name="databaseType" value="Sitecore.Data.Database, Sitecore.Kernel" />

durch

> <sc.variable name="databaseType" value="Sitecore.Data.**Default**Database, Sitecore.Kernel" />

ersetzen.

## Ein erster FakeDb-Test

Bei **FakeDb** ist der Name tatsächlich Programm. Es jubelt Sitecore, für die Ausführung der Tests, eine Fake-Datenbank unter. Diese Fake-Datenbank
wird in den Test-Cases befüllt und am Ende des Tests "entsorgt". FakeDb stellt dafür eine Klasse `Db` bereit. Diese implementiert `IDisposable` un dman muss
immer darauf achten, dass am Ende auch tatäslich `Dispose` aufgerufen wird. Damit man sich keine Sorgen darum machen muss, nutzt man
für gewöhnlich ein `using`-Statement. Im folgenden Listing findet ihr ein recht einfaches Beispiel.

```csharp
public class ExampleTests
{
    [Fact]
    public void CheckingItemsAndFields()
    {
        using (var db = GetDatabase())
        {
            Item home = db.GetItem("/sitecore/content/home");
            Assert.NotNull(home);

            Item firstSubitem = db.GetItem(ID.Parse("{3524F231-1961-45D6-98AC-17A40214D23B}"));
            Assert.Equal("This is the title of subitem 1", firstSubitem["Title"]);
        }
    }

    private static Db GetDatabase()
    {
        return new Db
        {
            new DbItem("Home")
            {
                new DbItem("subitem-1", ID.Parse("{3524F231-1961-45D6-98AC-17A40214D23B}"))
                {
                    { "Title", "This is the title of subitem 1" },
                    { "Description", "This is a description" }
                },
                new DbItem("subitem-2", ID.Parse("{EAE05A14-5286-4EAA-84E8-BE2F4140D60F}"))
                {
                    { "Title", "This is the title of subitem 2" },
                    { "Description", "This is a description" }
                }
            }
        };
    }
}
```

Wer mehr über **FakeDb** erfahren möchte, schaut am besten im [Wiki des Projektes](https://github.com/sergeyshushlyapin/Sitecore.FakeDb/wiki) vorbei. 
Dort sind alle Features gut dokumentiert.

Diese Tests können wir nun laufen lassen und sehen, dass alles wunderbar funktioniert hat. :smile:

Und das beste daran: Wir brauchen keinerlei Binaries im Repository einchecken. Alle Dependencies werden einfach über NuGet verwaltet.