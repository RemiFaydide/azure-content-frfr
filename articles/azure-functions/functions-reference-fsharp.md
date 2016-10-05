<properties
	pageTitle="Information de référence pour les développeurs F# sur Azure Functions | Microsoft Azure"
	description="Découvrez comment développer sur Azure Functions à l’aide de F#."
	services="functions"
	documentationCenter="fsharp"
	authors="sylvanc"
	manager="jbronsk"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur, F#"/>

<tags
	ms.service="functions"
	ms.devlang="fsharp"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="09/09/2016"
	ms.author="syclebsc"/>

# Informations de référence pour les développeurs F# sur Azure Functions

> [AZURE.SELECTOR]
- [Script C#](../articles/azure-functions/functions-reference-csharp.md)
- [Script F#](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.JS](../articles/azure-functions/functions-reference-node.md)

F# for Azure Functions est une solution conçue pour exécuter facilement de petits morceaux de code, ou « fonctions », dans le cloud. Les données circulent dans votre fonction F# par le biais d’arguments de fonction. Les noms d’argument sont spécifiés dans `function.json`, et il existe des noms prédéfinis pour accéder à des éléments tels que l’enregistreur de fonctions et les jetons d’annulation.

Cet article repose sur l’hypothèse que vous avez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md).

## Fonctionnement de .fsx

Un fichier `.fsx` est un script F#. Il peut être considéré comme un projet F# contenu dans un seul fichier. Ce fichier contient à la fois le code de votre programme (dans ce cas précis, votre fonction Azure) et des directives concernant la gestion des dépendances.

Lorsque vous utilisez un fichier `.fsx` pour une fonction Azure, les assemblys généralement requis sont automatiquement inclus à votre intention, ce qui vous permet de vous concentrer sur la fonction plutôt que sur le code « réutilisable ».

## Liaison aux arguments

Chaque liaison prend en charge un ensemble spécifique d’arguments, comme décrit en détail dans [Informations de référence pour les développeurs sur les déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md). Par exemple, l’une des liaisons d’argument prises en charge par un déclencheur d’objet blob est un objet CLR traditionnel (POCO), qui peut être exprimé à l’aide d’un enregistrement F#. Par exemple :

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Votre fonction Azure F# utilisera un ou plusieurs arguments. Lorsque nous parlons d’arguments Azure Functions, nous faisons référence à des arguments _d’entrée_ et à des arguments de _sortie_. Un argument d’entrée est exactement ce que son nom laisse entendre : une entrée pour votre fonction Azure F#. Un argument de _sortie_ correspond à des données mutables ou à un argument `byref<>` permettant de retransmettre des données _hors_ de votre fonction.

Dans l’exemple ci-dessus, `blob` est un argument d’entrée, tandis que `output` est un argument de sortie. Notez que nous avons utilisé le type `byref<>` pour l’argument `output` (il n’est pas nécessaire d’ajouter l’annotation `[<Out>]`). L’utilisation d’un type `byref<>` permet à votre fonction de modifier l’enregistrement ou l’objet auxquels l’argument fait référence.

Lorsqu’un enregistrement F# est utilisé comme type d’entrée, la définition d’enregistrement doit être indiquée par `[<CLIMutable>]` pour permettre à l’infrastructure Azure Functions de définir les champs de manière appropriée avant de transmettre l’enregistrement à votre fonction. En arrière-plan, `[<CLIMutable>]` génère des méthodes setter pour les propriétés d’enregistrement. Par exemple :

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Une classe F# est également utilisable pour les arguments d’entrée et de sortie. Pour une classe, les propriétés nécessitent généralement des méthodes getter et setter. Par exemple :

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## Journalisation

Pour consigner la sortie dans vos [journaux de diffusion en continu](../app-service-web/web-sites-streaming-logs-and-console.md) dans F#, votre fonction doit utiliser un argument de type `TraceWriter`. Par souci de cohérence, nous vous recommandons de nommer cet argument `log`. Par exemple :

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## Async

Il est possible d’utiliser le workflow `async`, mais le résultat doit renvoyer un élément `Task`. Pour ce faire, utilisez `Async.StartAsTask`. Par exemple :

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## Jeton d’annulation

Si votre fonction doit gérer les arrêts de manière appropriée, vous pouvez lui fournir un argument [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx), conjointement utilisable avec `async`. Par exemple :

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## Importation des espaces de noms

Les espaces de noms peuvent être ouverts de la manière habituelle :

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Les espaces de noms ci-après sont ouverts automatiquement :

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## Référencement des assemblys externes

De même, les références d’assembly d’infrastructure sont ajoutées avec la directive `#r "AssemblyName"`.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Les assemblys suivants sont ajoutés automatiquement par l’environnement hébergeant Azure Functions :

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

En outre, les assemblys ci-après ont une casse spécifique et peuvent être référencés par leur nom simple (par exemple, `#r "AssemblyName"`) :

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Si vous avez besoin de référencer un assembly privé, vous pouvez charger le fichier d’assembly dans un dossier `bin` relatif à votre fonction et le référencer à l’aide du nom de fichier (par exemple, `#r "MyAssembly.dll"`). Pour plus d’informations sur le téléchargement de fichiers vers votre conteneur de fonctions, consultez la section suivante sur la gestion des packages.

## Préambule destiné à l’éditeur

Un éditeur prenant en charge F# Compiler Services ne reconnaît pas les espaces de noms et les assemblys automatiquement inclus par Azure Functions. Il peut donc être utile d’insérer un préambule conçu pour permettre à l’éditeur de localiser les assemblys que vous utilisez et d’ouvrir explicitement les espaces de noms. Par exemple :

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Lorsque la solution Azure Functions exécute votre code, elle traite la source avec l’élément `COMPILED` défini, de sorte que le préambule destiné à l’éditeur sera ignoré.

## Gestion des packages

Pour utiliser des packages NuGet dans une fonction F#, ajoutez un fichier `project.json` au dossier de la fonction dans le système de fichiers de l’application de fonctions. Voici un exemple de fichier `project.json` ajoutant une référence de package NuGet à `Microsoft.ProjectOxford.Face` version 1.1.0 :

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Seul .NET Framework 4.6 est pris en charge. Par conséquent, assurez-vous que votre fichier `project.json` spécifie `net46` comme indiqué ici.

Lorsque vous chargez un fichier `project.json`, le runtime obtient les packages et ajoute automatiquement des références aux assemblys de packages. Vous n’avez pas besoin d’ajouter de directives `#r "AssemblyName"`. Il vous suffit d’ajouter les instructions `open` requises à votre fichier `.fsx`.

Si vous le souhaitez, vous pouvez placer automatiquement les assemblys de référence dans votre préambule destiné à l’éditeur, afin d’améliorer l’interaction de votre éditeur avec F# Compile Services.

### Ajout d’un fichier `project.json` à votre fonction Azure

1. Commencez par vous assurer que votre conteneur de fonctions est en cours d’exécution. Ce que vous pouvez faire en ouvrant votre fonction dans le portail Azure. Il donne également accès aux journaux de diffusion en continu où le résultat de l’installation du package s’affiche.

2. Pour charger un fichier `project.json`, utilisez l’une des méthodes décrites dans l’article [Comment mettre à jour les fichiers du conteneur de fonctions](functions-reference.md#fileupdate). Si vous utilisez l’article [Déploiement continu pour Azure Functions](functions-continuous-deployment.md), vous pouvez ajouter un fichier `project.json` à votre branche intermédiaire afin de le tester avant de l’ajouter à votre branche de déploiement.

3. Une fois le fichier `project.json` ajouté, une sortie semblable à l’exemple ci-après apparaîtra dans le journal de diffusion en continu de votre fonction :

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## Variables d’environnement

Pour obtenir une variable d’environnement ou une valeur de paramètre d’application, utilisez `System.Environment.GetEnvironmentVariable`. Par exemple :

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## Réutilisation du code .fsx

Vous pouvez utiliser le code d’autres fichiers `.fsx` au moyen d’une directive `#load`. Par exemple :

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Les chemins d’accès fournis à la directive `#load` sont relatifs à l’emplacement de votre fichier `.fsx`.

* `#load "logger.fsx"` charge un fichier situé dans le dossier de la fonction.

* `#load "package\logger.fsx"` charge un fichier situé dans le dossier `package` du dossier de la fonction.

* `#load "..\shared\mylogger.fsx"` charge un fichier situé dans le dossier `shared` au même niveau que le dossier de la fonction, c’est-à-dire, directement sous `wwwroot`.

La directive `#load` ne fonctionne qu’avec des fichiers `.fsx` (script F#), et non avec des fichiers `.fs`.

## Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [F# Guide](https://docs.microsoft.com/fr-FR/dotnet/articles/fsharp/index) (Guide F#)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Informations de référence pour les développeurs C# sur Azure Functions](functions-reference-csharp.md)
* [Azure Functions NodeJS developer reference (Référence pour les développeurs NodeJS Azure Functions)](functions-reference-node.md)
* [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md)
* [Test des fonctions Azure](functions-test-a-function.md)
* [Mise à l’échelle d’Azure Functions](functions-scale.md)

<!---HONumber=AcomDC_0921_2016-->