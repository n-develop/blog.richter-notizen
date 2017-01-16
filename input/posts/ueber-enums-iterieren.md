Title: Über Enum-Values iterieren
Published: 2016-03-17
Tags: ["CSharp"]
---
Hin und wieder kann es vorkommen, dass man gerne über die Werte einer Enum iterieren würde, um diese beispielsweise in 
eine Liste mit Auswahlmöglichkeiten umzuwandeln oder die der Werte einfach auszugeben. In diesem Artikel wollen wir genau das machen. 
Betrachten wir folgendes Beispiel:

Wir haben eine Enum, deren Werte Freizeitbeschäftigungen repräsentieren. Der User soll in einer Konsolenanwendung eine 
Auswahltreffen können. Um nicht die, oft sehr technischen Namen der Enum-Werte ausgeben zu müssen, versehen wir 
jeden Wert mit einem Description-Attribut.

```csharp
enum LeisureTime
{
    [Description("Learn? Seriously?")]
    Learn,
    [Description("Being lazy")]
    DoNothing,
    [Description("Shall we dance?")]
    Dance,
    [Description("Have a cup of coffee")]
    DrinkCoffee,
    [Description("Party hard!")]
    Party
}
```

Um den Wert des Description-Attributes auch komfortabel abfragen<!-- Read More --> zu können,
erstellen wir uns noch eine handliche Extension-Method für Enums:

```csharp
public static class EnumExtensions
{
    public static string GetDescription(this Enum value)
    {
        Type type = value.GetType();
        string name = Enum.GetName(type, value);
        if (name != null)
        {
            FieldInfo field = type.GetField(name);
            if (field != null)
            {
                var attr = Attribute.GetCustomAttribute(field, typeof(DescriptionAttribute)) as DescriptionAttribute;
                if (attr != null)
                {
                    return attr.Description;
                }
            }
        }
        throw new Exception("Description attribute coud not be read from enum");
    }
}
```

Nun kommen wir zum spannenden Teil. Das .NET-Framework bietet uns von Haus aus schon die richtigen Methoden um die Werte einer 
Enum in einer Liste zu bekommen. Die statische Methode **GetValues** der Klasse **Enum** erfüllt genau diesen Zweck. 
Man übergibt einfach den Typ der gewünschten Enum und bekommt eine passende Rückgabe. In der Ausgabe nutzen wir dann unsere 
eben erstellte **GetDescription**-Methode.

```csharp
Console.WriteLine("What shall we do today?");
foreach (LeisureTime leisureTime in Enum.GetValues(typeof(LeisureTime)))
{
    Console.WriteLine("{0} : {1}", (int)leisureTime, leisureTime.GetDescription());
}
var input = Console.ReadLine();
// ... process Input ...
```

Das war dann auch schon der ganze Zauber. Ich hoffe der Artikel konnte eure offenen Fragen beantworten. 
Wenn nicht, hinterlasst doch einfach einen Kommentar.

Happy Coding!