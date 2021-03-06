---
title: LINQ (Language Integrated Query)
description: LINQ (Language Integrated Query)
keywords: .NET, .NET Core
author: cartermp
ms.author: wiwagn
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net
ms.technology: dotnet-standard
ms.devlang: dotnet
ms.assetid: c00939e1-59e3-4e61-8fe9-08ad6b3f1295
translationtype: Human Translation
ms.sourcegitcommit: 6992d907b152d67c51f43ec517902fb2effc6db2
ms.openlocfilehash: 0d941d7722353c68051bb761f4b05a6e304291ef

---

# <a name="linq-language-integrated-query"></a>LINQ (Language Integrated Query)

## <a name="what-is-it"></a>Qu’est-ce que c’est ?

LINQ fournit des fonctionnalités d’interrogation au niveau du langage et une API de [fonction d’ordre supérieur](https://en.wikipedia.org/wiki/Higher-order_function) pour C# et VB pour pouvoir écrire du code déclaratif, expressif.

Syntaxe de requête au niveau du langage :

```cs
var linqExperts = from p in programmers
                  where p.IsNewToLINQ
                  select new LINQExpert(p);

```

Même exemple en utilisant l’API `IEnumerable<T>` :

```cs
var linqExperts = programmers.Where(p => IsNewToLINQ)
                             .Select(p => new LINQExpert(p));

```

## <a name="linq-is-expressive"></a>LINQ est expressif

Imaginez que vous avez une liste d’animaux domestiques, mais que vous voulez la convertir en dictionnaire dans lequel vous pouvez accéder à un animal directement par sa valeur `RFID`.

Code impératif traditionnel :

```cs
var petLookup = new Dictionary<int, Pet>();

foreach (var pet in pets)
{
    petLookup.Add(pet.RFID, pet);
}

```

L’intention du code n’est pas de créer un `Dictionary<int, Pet>` et d’y ajouter des éléments par une boucle, c’est de convertir une liste existante en dictionnaire ! LINQ conserve l’intention contrairement au code impératif.

Expression LINQ équivalente :

```cs
var petLookup = pets.ToDictionary(pet => pet.RFID);

```

Le code qui utilise LINQ est utile, car il égalise le terrain entre l’intention et le code dans un contexte de programmation. Un autre bonus est la concision du code. Imaginez que vous pouvez réduire d’un tiers les grandes parties d’un code Base comme illustré ci-dessus. Intéressant, n’est-ce pas ?

## <a name="linq-providers-simplify-data-access"></a>Les fournisseurs LINQ simplifient l’accès aux données

Pour la plupart des logiciels en général, tout tourne autour du traitement de données à partir d’une source (bases de données, JSON, XML, etc.). Cela implique souvent d’apprendre une nouvelle API par source de données, ce qui peut s’avérer fastidieux. LINQ simplifie le problème en faisant abstraction de l’accès aux éléments de donnée communs dans une syntaxe de requête qui semble la même, quelle que soit la source de données choisie.

Imaginons que vous recherchez tous les éléments XML avec une valeur d’attribut spécifique.

```cs
public static IEnumerable<XElement> FindAllElementsWithAttribute(XElement documentRoot, string elementName,
                                           string attributeName, string value)
{
    return from el in documentRoot.Elements(elementName)
           where (string)el.Element(attributeName) == value
           select el;
}

```

Écrire du code pour parcourir manuellement le document XML afin de rechercher ces éléments est une tâche très complexe.

Les fournisseurs LINQ ne vous permettent pas seulement d’interagir avec le XML. [LINQ to SQL](https://msdn.microsoft.com/library/bb386976.aspx) est un mappeur ORM (Object-Relational Mapper) de base de données MSSQL relativement minimaliste. La bibliothèque [JSON.NET](http://www.newtonsoft.com/json/help/html/LINQtoJSON.htm) fournit un balayage efficace du document JSON via LINQ. Par ailleurs, si vous n’avez pas de bibliothèque pour faire ce dont vous avez besoin, vous pouvez également [écrire votre propre fournisseur LINQ](https://msdn.microsoft.com/library/Bb546158.aspx) !

## <a name="why-use-the-query-syntax"></a>Pourquoi utiliser la syntaxe de requête ?

C’est une question qui revient souvent. Après tout,

```cs
var filteredItems = myItems.Where(item => item.Foo);

```

est beaucoup plus concis que ce qui suit :

```cs
var filteredItems = from item in myItems
                    where item.Foo
                    select item;

```

La syntaxe d’API n’est-elle pas simplement un moyen plus concis d’effectuer la syntaxe de requête ?

Non. La syntaxe de requête permet d’utiliser la clause **let**, ce qui vous permet d’introduire et de lier une variable dans la portée de l’expression, en l’utilisant dans les parties suivantes de l’expression. Vous pouvez reproduire le même code avec la seule syntaxe d’API, mais vous obtiendrez très probablement du code difficile à lire.

Ce qui nous amène à la question suivante : **devez-vous vous contenter d’utiliser la syntaxe de requête ?**

La réponse à cette question est **oui**, si...

*   Votre code Base utilise déjà la syntaxe de requête
*   Vous devez définir l’étendue des variables dans vos requêtes en raison de la complexité
*   Vous préférez la syntaxe de requête et elle ne vous détournera pas de votre code Base

La réponse à cette question est **non**, si...

*   Votre code Base utilise déjà la syntaxe d’API
*   Vous n’avez pas besoin de définir l’étendue des variables dans vos requêtes
*   Vous préférez la syntaxe d’API et elle ne vous détournera pas de votre code Base

## <a name="essential-samples"></a>Exemples essentiels

Pour obtenir la liste complète des exemples LINQ, consultez [101 LINQ Samples](https://code.msdn.microsoft.com/101-LINQ-Samples-3fb9811b) (101 exemples LINQ).

Voici une démonstration rapide de quelques-uns des éléments essentiels de LINQ. Ces exemples ne sont pas exhaustifs, car LINQ fournit beaucoup plus de fonctionnalités que ce qui est présenté ici.

*   Les indispensables : `Where`, `Select` et `Aggregate` :

```cs
// Filtering a list
var germanShepards = dogs.Where(dog => dog.Breed == DogBreed.GermanShepard);

// Using the query syntax
var queryGermanShepards = from dog in dogs
                          where dog.Breed == DogBreed.GermanShepard
                          select dog;

// Mapping a list from type A to type B
var cats = dogs.Select(dog => dog.TurnIntoACat());

// Using the query syntax
var queryCats = from dog in dogs
                select dog.TurnIntoACat();

// Summing then lengths of a set of strings
int seed = 0;
int sumOfStrings = strings.Aggregate(seed, (s1, s2) => s1.Length + s2.Length);

```

*   Aplanissement d’une liste de listes :

```cs
// Transforms the list of kennels into a list of all their dogs.
var allDogsFromKennels = kennels.SelectMany(kennel => kennel.Dogs);

```

*   Union entre deux ensembles (avec un comparateur personnalisé) :

```cs
public class DogHairLengthComparer : IEqualityComparer<Dog>
{
    public bool Equals(Dog a, Dog b)
    {
        if (a == null && b == null)
        {
            return true;
        }
        else if ((a == null && b != null) ||
                 (a != null && b == null))
        {
            return false;
        }
        else
        {
            return a.HairLengthType == b.HairLengthType;
        }
    }

    public int GetHashCode(Dog d)
    {
        // default hashcode is enough here, as these are simple objects.
        return b.GetHashCode();
    }
}

...

// Gets all the short-haired dogs between two different kennels
var allShortHairedDogs = kennel1.Dogs.Union(kennel2.Dogs, new DogHairLengthComparer());

```

*   Intersection entre deux ensembles :

```cs
// Gets the volunteers who spend share time with two humane societies.
var volunteers = humaneSociety1.Volunteers.Intersect(humaneSociety2.Volunteers,
                                                     new VolunteerTimeComparer());

```

*   Tri :

```cs
// Get driving directions, ordering by if it's toll-free before estimated driving time.
var results = DirectionsProcessor.GetDirections(start, end)
              .OrderBy(direction => direction.HasNoTolls)
              .ThenBy(direction => direction.EstimatedTime);

```

*   Enfin, un exemple plus avancé : déterminer si les valeurs des propriétés de deux instances du même type sont égales (emprunté à [ce billet StackOverflow](http://stackoverflow.com/a/844855) et modifié) :

```cs
public static bool PublicInstancePropertiesEqual<T>(this T self, T to, params string[] ignore) where T : class
{
    if (self != null && to != null)
    {
        var type = typeof(T);
        var ignoreList = new List<string>(ignore);

        // Selects the properties which have unequal values into a sequence of those properties.
        var unequalProperties = from pi in type.GetProperties(BindingFlags.Public | BindingFlags.Instance)
                                where !ignoreList.Contains(pi.Name)
                                let selfValue = type.GetProperty(pi.Name).GetValue(self, null)
                                let toValue = type.GetProperty(pi.Name).GetValue(to, null)
                                where selfValue != toValue && (selfValue == null || !selfValue.Equals(toValue))
                                select new { Prop = pi.Name, selfValue, toValue };
        return !unequalProperties.Any();
    }

    return self == to;
}

```

## <a name="plinq"></a>PLINQ

PLINQ, ou Parallel LINQ, est un moteur d’exécution parallèle pour les expressions LINQ. En d’autres termes, des expressions régulières LINQ peuvent être parallélisées de manière simple sur n’importe quel nombre de threads. Cela s’effectue par un appel à `AsParallel()` avant l’expression.

Considérez ce qui suit :

```cs
public static string GetAllFacebookUserLikesMessage(IEnumerable<FacebookUser> facebookUsers)
{
    var seed = default(UInt64);

    Func<UInt64, UInt64, UInt64> threadAccumulator = (t1, t2) => t1 + t2;
    Func<UInt64, UInt64, UInt64> threadResultAccumulator = (t1, t2) => t1 + t2;
    Func<Uint64, string> resultSelector = total => $"Facebook has {total} likes!";

    return facebookUsers.AsParallel()
                        .Aggregate(seed, threadAccumulator, threadResultAccumulator, resultSelector);
}

```

Ce code partitionne `facebookUsers` entre les threads système si nécessaire, additionne le nombre total de mentions J’aime sur chaque thread en parallèle, additionne les résultats calculés par chaque thread et projette ce résultat dans une chaîne très pratique.

Sous forme de diagramme :

![Diagramme PLINQ](./media/using-linq/plinq-diagram.png)

Les tâches parallèles utilisant le processeur qui peuvent être facilement exprimées par LINQ (en d’autres termes, qui sont des fonctions pures et n’ont aucun effet secondaire) sont parfaites pour PLINQ. Pour les tâches qui _ont_ un effet secondaire, utilisez la [bibliothèque parallèle de tâches](https://msdn.microsoft.com/library/dd460717.aspx).

## <a name="further-resources"></a>Ressources supplémentaires :

*   [101 exemples LINQ](https://code.msdn.microsoft.com/101-LINQ-Samples-3fb9811b)
*   [Linqpad](https://www.linqpad.net/), environnement de laboratoire et moteur d’interrogation de base de données pour C#/F#/VB
*   [EduLinq](http://codeblog.jonskeet.uk/2011/02/23/reimplementing-linq-to-objects-part-45-conclusion-and-list-of-posts/), livre électronique pour apprendre comment LINQ-to-objects est implémenté



<!--HONumber=Nov16_HO3-->


