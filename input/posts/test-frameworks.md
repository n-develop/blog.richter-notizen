Title: xUnit, NUnit und MSTest im Vergleich
Published: 2016-03-17
Tags: ["xunit", "nunit", "mstest", "tdd", "unit-testing"]
Image: ../images/blog-title-13.jpg
---

Das Internet ist voll von Vergleichen der beliebtesten Test-Frameworks. Warum genau muss ich hier dann auch so 
einen Vergleich starten? Weil mir persönlich bei den meisten Vergleichen die Praxis fehlt. Hier soll es nicht 
einfach nur um den Vergleich von Attributen und Assert-Methoden gehen. Denn genau so sehen viele Vergleiche aus. 
Da werden einfach nur Tabellen "hingeklatscht" und gesagt, dass xUnit keine Kennzeichnung von Test-Klassen benötigt. 
Aber so richtig hilft einem so eine Tabelle nicht weiter, wenn man nach einem neuen Test-Framework sucht, 
oder das Look-and-Feel eines Test-Frameworks erleben will. Natürlich werde auch ich hier die verschiedenen Attribute der 
Frameworks nennen, aber mir geht es viel mehr darum, den entstandenen Code mit den unterschiedlichen Frameworks zu zeigen.

Deswegen wollen wir hier ein einfaches Beispiel sowohl mit [MSTest](https://msdn.microsoft.com/en-us/library/ms243147(v=vs.90)), 
mit [NUnit](http://www.nunit.org/), als auch mit [xUnit](https://github.com/xunit/xunit) umsetzen. 
Die folgende Klasse implementiert ein Bankkonto und soll einigen Tests unterzogen werden<!-- Read More -->.

```csharp
public class Account
{
    public decimal Balance { get; private set; }
    public void Deposit(decimal amount)
    {
        Balance += amount;
    }
    public void Withdraw(decimal amount)
    {
        amount = Math.Round(amount, 2, MidpointRounding.AwayFromZero);
        if (amount > 2000)
        {
            throw new ArgumentOutOfRangeException();
        }
        if (amount <= 0)
        {
            throw new ArgumentOutOfRangeException();
        }
        Balance -= amount;
    }
}
```

Auch wenn ich persönlich ein Fan davon bin, wenn man den Code in solchen Tutorials abtippt und nicht kopiert (es prägt sich doch 
irgendwie besser ein), habe ich für alle die, die keine Lust haben den Code abzutippen, die fertige Visual Studio-Solution 
[hier zum Download](../sourcecode/TestFrameworks.zip) vorbereitet.

## Ein einfacher Test
Fangen wir mal mit einem einfachen Test an. Wir erwarten natürlich, dass das Konto einen Saldo von 0 Euro aufweist, 
wenn es initialisiert wird. Im Test-Framework MSTest sieht das die Klasse wie folgt aus:

```csharp
[TestClass]
public class AccountTests
{
    [TestMethod]
    public void InitAccountTest()
    {
        Account account = new Account();
        Assert.AreEqual(0m, account.Balance);
    }
}
```

Ein Test-Klasse wird, wenig überraschend, durch das TestClass-Attribut markiert. Das TestMethod-Attribut kennzeichnet 
dementsprechend eine Test-Methode. Und das war es auch schon. Schauen wir uns das gleiche Beispiel mal mit NUnit an:

```csharp
[TestFixture]
public class AccountTests
{
    [Test]
    public void InitAccountTest()
    {
        Account account = new Account();
        Assert.AreEqual(0m, account.Balance);
    }
}
```

Zur Kennzeichnung von Test-Klassen in NUnit kommt das TextFixture-Attribut zum Einsatz. Test-Methoden werden 
wiederum einfach mit [Test] gekennzeichnet. Soweit, so gut. Dann bleibt noch xUnit:

```csharp
public class AccountTests
{
    [Fact]
    public void InitAccountTest()
    {
        Account account = new Account();
        Assert.Equal(0m, account.Balance);
    }
}
```

Der erste "größere" Unterschied von xUnit gegenüber den anderen beiden Frameworks ist, dass die Test-Klassen 
nicht explizit gekennzeichnet werden. Tests werden mit dem Fact-Attribut markiert.

## Theorien und Data-Driven Tests
Aber das ist nur die halbe Wahrheit. Nicht alle Test-Methoden werden in xUnit mit [Fact] markiert. 
Denn xUnit unterscheidet bei Test-Methoden zwischen Fakten (Fact) und Theorien (Theory). Genauer betrachtet 
ergibt diese Aufteilung durchaus Sinn. Manche Tests prüfen Fakten eines Software-Systems. Fakten sind Tests, 
die immer wahr sind. Ein gutes Beispiel ist der oben genannte Test. Es ist ein Fakt, dass ein Bankkonto nach 
der Initialisierung immer ein Saldo von 0 Euro aufweist.

Aber sind nicht alle Tests "Fakten"? Das ist eine berechtigte Frage. Eine "Theorie" ist, laut der Definition von xUnit, 
etwas, das nur unter bestimmten Voraussetzungen zutrifft. Betrachten wir einmal die WithDraw-Methode unserer zu testenden 
Klasse. Wenn ein Betrag > 2000 Euro abgehoben werden soll, wirft diese eine ArgumentOutOfRangeException. Ob das allerdings 
nur Theorie ist, sollen entsprechende Unit-Tests zeigen. Wichtig an dieser Stelle: Es müssen verschiedene Grenzwerte 
getestet werden. Diesmal starten wir mit xUnit, um die Nutzung des Theory-Attributes zu zeigen.

```csharp
[Theory]
[InlineData(2000.1)]
[InlineData(2000.01)]
[InlineData(2000.009)]
[InlineData(2000.005)]
public void ToHighAmountForWithdraw(decimal amount)
{
    Account account = new Account();
    Assert.Throws<ArgumentOutOfRangeException>(() => account.Withdraw(amount));
}
[Theory]
[InlineData(2000.004)]
[InlineData(2000)]
[InlineData(1999.99)]
[InlineData(1999.999)]
public void NotToHighAmountForWithdraw(decimal amount)
{
    Account account = new Account();
    account.Withdraw(amount);
}
```

Wie man sieht, haben wir genau zwei Tests (genauer gesagt zwei Theories) geschrieben, die über das 
InlineData-Attribut verschiedene Eingabedaten bekommen. Bei den Eingabedaten handelt es sich um einige Grenzwerte. 
Im ersten Test, wird für alle diese Eingabedaten erwartet, dass die aufgerufene Methode eine 
ArgumentOutOfRangeException wirft. Dies wird, anders als bei den anderen beiden Test-Frameworks, nicht über Attribute, 
sondern über eine entsprechende Assert-Methode erreicht.

Nun werfen wir einen Blick auf den gleichen Test mit NUnit:

```csharp
[TestCase(2000.1)]
[TestCase(2000.01)]
[TestCase(2000.009)]
[TestCase(2000.005)]
[ExpectedException(typeof(ArgumentOutOfRangeException))]
public void ToHighAmountForWithdraw(decimal amount)
{
    Account account = new Account();
    account.Withdraw(amount);
}
[TestCase(2000.004)]
[TestCase(2000)]
[TestCase(1999.99)]
[TestCase(1999.999)]
public void NotToHighAmountForWithdraw(decimal amount)
{
    Account account = new Account();
    account.Withdraw(amount);
}
```

Auch hier können wir für alle Grenzwerte einen Test nutzen. Die Eingabedaten werden bei NUnit über das 
TestCase-Attribut bereitgestellt. Wie oben bereits beschrieben, werden Exceptions bei NUnit mit Hilfe eines 
Attributes erwartet. Hier kommt das ExpectedException-Attribut zum Einsatz, welches als Parameter den Typ der 
erwarteten Exception aufnimmt.

Das letzte Framework in der Runde ist MSTest. Hier ist das Vorgehen bei sogenannten data-driven Tests allerdings 
etwas anders. Hier können wir die Daten nicht einfach über ein Attribut bereitstellen. Stattdessen kann eine Datasource 
angegeben werden, dessen Werte im Test genutzt werden. Diese Datasources können eine Datenbanktabelle, eine Exceldatei, 
eine CVS-Datei oder ähnliches sein. In diesem Beispiel nutzen wir eine CSV-Datei names "MSTestValues.csv" mit folgendem Inhalt:

```csv
Amount
2000.1
2000.01
2000.005
2000.009
```

Für jede Zeile in der CSV-Datei wir der Test einmal ausgeführt. Die Daten können dann einfach über den Spaltenindex 
abgerufen werden. Genau wie bei NUnit werden auch bei MSTest erwartete Exceptions über das ExpectedException-Attribut 
verwaltet. Wichtig ist auch, dass der TestContext als Property verfügbar gemacht wird. Dazu muss man nicht mehr machen 
als eine entsprechende Property vom gleichnamigen Typ anlegen. Über den TestContext kann dann auf die Zeilen der 
DataSource zugegriffen werden.

```csharp
public TestContext TestContext { get; set; }
[TestMethod]
[DataSource("Microsoft.VisualStudio.TestTools.DataSource.CSV", "|DataDirectory|\\MSTestValues.csv", "MSTestValues#csv", DataAccessMethod.Sequential)]
[DeploymentItem("MSTestValues.csv")]
[ExpectedException(typeof(ArgumentOutOfRangeException))]
public void ShouldThrowExceptionIfAmountIsTooHigh()
{
    Account account = new Account();
    decimal amount = Convert.ToDecimal(TestContext.DataRow[0]);
    account.Withdraw(amount);
}
```

## Tests vorrübergehend ignorieren
Hin und wieder kann es vorkommen, dass man einen oder mehrere Tests vorrübergehend deaktivieren / ignorieren will. 
Dies ist in allen drei Frameworks denkbar einfach.

NUnit:

```csharp
[Ignore]
[Test]
public void IgnorableTest()
{
    /* ... some test logic ...*/
}
```

xUnit:

```csharp
[Fact(Skip = "API hat sich geändert")]
public void IgnorableTest()
{
    /* ... some test logic ... */
}
```

MSTest:

```csharp
[Ignore]
[TestMethod]
public void IgnorableTest()
{
    /* ... some test logic ... */
}
```

## SetUp und TearDown
Jeder der schon einmal Unit-Tests geschrieben hat kennt vermutlich das Konzept von SetUp- und TearDown-Methoden. 
Diese Methoden werden vor bzw. nach jeder Test-Methode ausgeführt. Sie werden meistens dafür genutzt um eine 
einheitliche Ausgangssituation herzustellen.

In NUnit werden diese Methoden über die gleichnamigen Attribute identifiziert:

```csharp
[SetUp]
public void SetUp()
{
    /* let's set up something */
}
[TearDown]
public void TearDown()
{
    /* time to clean up */
}
```

Ähnlich verhält es sich mit mit MSTest hier heißen die Attribute allerdings TestInitialize und TestCleanup.

```csharp
[TestInitialize]
public void SetUp()
{
    /* let's set up something */
}
[TestCleanup]
public void TearDown()
{
    /* time to clean up */
}
```

In xUnit verhält es sich etwas anders. Hier werden für diese Zwecke der Konstruktor sowie die Dispose-Methode genutzt. 
Vor jeder Testausführung wird die Klasse also neu instanziiert:

```csharp
public class AccountTests : IDisposable
{
    public AccountTests()
    {
        /* let's set up something */
    }
    public void Dispose()
    {
        /* time to clean up */
    }
}
```

Der ein oder andere mag diesen Weg vielleicht als wenig elegant empfinden. Aber dazu sei noch gesagt, 
dass die Macher von xUnit die Nutzung von SetUp- und TearDown-Methoden für einen schlechten Stil halten. 
In ihren Augen gibt es selten sinnvolle Einsatzgebiete für Code, der vor wirklich JEDEM Test in der Klasse 
ausgeführt werden muss. Auf diese Weise wird, in der Realität, sehr oft Code ausgeführt, der eigentlich nicht 
gebraucht wird. Sollte es aber doch mal nötig sein, kann dies, wie oben beschrieben, über den Konstruktor und die 
Dispose-Methode geschehen.

## Fazit
Natürlich gibt es über jedes einzelne Framework noch so viel mehr zu sagen, als ich in diesem Tutorial abdecken konnte. 
Wer jetzt also einen Favoriten hat, sollte sich diesen trotzdem noch etwas genauer anschauen.

Wie immer haben alle Frameworks ihre Vor- und Nachteile. Aber ich persönlich arbeite inzwischen am liebsten mit xUnit. 
Die Trennung von Tests und Fixtures (ein Thema, dass wir in einem anderen Beitrag beleuchten werden), 
sowie die Unterscheidung von Facts und Theories fühlen sich meiner Meinung nach einfach richtig an. 
Ich hoffe dieser Artikel konnte euch bei der Entscheidung für ein Test-Framework unterstützen.

Wer sich den gezeigten Code in seiner Gänze zu Gemüte führen will, sei nochmal auf die im ersten Abschnitt 
genannte ZIP-Datei verwiesen.

Happy Coding!