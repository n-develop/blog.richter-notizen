Title: xUnit-Tests und Console.WriteLine
Published: 2016-03-17
Tags: ["xUnit", "Unit-Testing"]
Image: ../images/blog-title-6.jpg
---
Wie bereits in meinem letzten Artikel Test-Frameworks im Vergleich beschrieben, 
bin ich für Unit-Tests von MSTest auf xUnit umgestiegen. Während der Test-Entwicklung kam ich auch zu dem Punkt, 
wo ich Infos im Test ausgeben wollte. Wie von MSTest gewohnt habe ich natürlich erstmal das 
gute alte *Console.WriteLine* probiert. Leider erfolglos. Nichts im Output-Window, nichts in den Test-Results.
 
Eine ausgiebige Suche auf Stack Overflow brachte mir die Lösung: Um Output in xUnit-Tests zu erzeugen,
lässt man sich eine Instanz<!-- Read More --> vom Typ `ITestOutputHelper` in den Konstruktor injizieren. 
Das Interface bietet die entsprechende `WriteLine` – Methode an. Der dorthin übergebene String wird dann im Output-Bereich 
der Test-Results angezeigt.

## Beispiel mit dem ITestOutputHelper von xUnit
Hier ein Beispiel im ReSharper Test-Runner:
```csharp
using Xunit;
using Xunit.Abstractions;
namespace xUnitTestOutput
{
    public class OutputTests
    {
        private readonly ITestOutputHelper _output;
        public OutputTests(ITestOutputHelper output)
        {
            _output = output;
        }
        [Fact]
        public void FirstOutputTest()
        {
            _output.WriteLine("Dies ist eine Test-Ausgabe mit xUnit!");
        }
    }
}
```

![Output Window des "xUnit Test Support for ReSharper 9"](../images/081015_0948_ConsoleWrit1.png)
Output Window des "xUnit Test Support for ReSharper 9"