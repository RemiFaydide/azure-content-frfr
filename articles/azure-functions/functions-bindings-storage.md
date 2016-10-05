<properties
	pageTitle="Déclencheurs et liaisons Azure Functions pour Azure Storage| Microsoft Azure"
	description="Découvrez comment utiliser des déclencheurs et des liaisons Azure Storage dans Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande"/>

# Déclencheurs et liaisons Azure Functions pour Azure Storage

[AZURE.INCLUDE [functions-selector-bindings (liaisons de sélecteur de fonctions)](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des déclencheurs et des liaisons Azure Storage dans Azure Functions.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="storagequeuetrigger"></a> Déclencheur de file d’attente Azure Storage

#### Fichier function.json pour un déclencheur de file d’attente Azure Storage

Le fichier *function.json* spécifie les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour la file d’attente ou le message de file d’attente.
- `queueName` : nom de la file d’attente à interroger. Pour plus d’informations sur les règles de nommage des files d’attente, consultez [Affectation de noms pour les files d’attente et les métadonnées](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection` : nom d’un paramètre d’application contenant une chaîne de connexion de stockage. Si vous laissez `connection` vide, le déclencheur fonctionnera avec la chaîne de connexion de stockage par défaut pour le conteneur de fonctions, qui est spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type` : propriété devant être définie sur *queueTrigger*.
- `direction` : doit être défini sur *in*.

Exemple *function.json* pour un déclencheur de file d’attente de stockage :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### Types pris en charge pour le déclencheur de file d’attente

Le message de file d’attente peut être désérialisé vers l’un des types suivants :

* Objet (de JSON)
* String
* Tableau d’octets
* `CloudQueueMessage` (C#)

#### Métadonnées de déclencheur de file d’attente

Vous pouvez obtenir les métadonnées de file d’attente dans votre fonction en utilisant les noms de variable suivants :

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (autre méthode pour récupérer le texte du message de file d’attente sous la forme d’une chaîne)

Cet exemple de code C# récupère et enregistre les métadonnées de file d’attente :

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### Gestion des messages de file d’attente incohérents

Les messages dont le contenu provoque l'échec d'une fonction sont appelés *messages incohérents*. Lorsque la fonction échoue, le message en file d’attente n’est pas supprimé, mais finalement récupéré à nouveau, provoquant la répétition du cycle. Le Kit de développement logiciel (SDK) peut interrompre automatiquement le cycle après un nombre limité d’itérations, ou vous pouvez le faire manuellement.

Le Kit de développement logiciel (SDK) appelle une fonction jusqu’à cinq fois pour traiter un message de file d’attente. Si la cinquième tentative échoue, le message est déplacé vers une file d’attente de messages incohérents.

La file d'attente de messages incohérents se nomme *{nom\_file\_d'attente\_d'origine}*-poison. Vous pouvez écrire une fonction pour traiter les messages de la file d’attente de messages incohérents en les consignant dans un journal ou en envoyant une notification signalant qu’une attention manuelle est nécessaire.

Si vous voulez gérer les messages incohérents manuellement, vous pouvez obtenir le nombre de fois où un message a été récupéré pour traitement en consultant `dequeueCount`.

## <a id="storagequeueoutput"></a> Liaison de sortie de file d’attente Azure Storage

#### Fichier function.json pour une liaison de sortie de file d’attente de stockage

Le fichier *function.json* spécifie les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour la file d’attente ou le message de file d’attente.
- `queueName` : nom de la file d’attente. Pour plus d’informations sur les règles de nommage des files d’attente, consultez [Affectation de noms pour les files d’attente et les métadonnées](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection` : nom d’un paramètre d’application contenant une chaîne de connexion de stockage. Si vous laissez `connection` vide, le déclencheur fonctionnera avec la chaîne de connexion de stockage par défaut pour le conteneur de fonctions, qui est spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type` : doit être défini sur *queue*.
- `direction` : doit être défini sur *out*.

Exemple *function.json* pour une liaison de sortie de file d’attente de stockage qui utilise un déclencheur de file d’attente et écrit un message de file d’attente :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Types pris en charge pour la liaison de sortie de file d’attente

La liaison `queue` peut sérialiser les types ci-après vers un message de file d’attente :

* Objet (`out T` en C#, crée un message avec un objet Null si le paramètre présente la valeur Null lorsque la fonction se termine)
* Chaîne (`out string` en C#, crée le message en file d’attente si la valeur du paramètre n’est pas Null lorsque la fonction se termine)
* Tableau d’octets (`out byte[]` en C#, fonctionne comme le type string)
* `out CloudQueueMessage` (C#, fonctionne comme le type string)

En C#, vous pouvez également lier à `ICollector<T>` ou `IAsyncCollector<T>`, où `T` est un des types pris en charge.

#### Exemples de code de liaison de sortie de file d’attente

Cet exemple de code C# écrit un message de file d’attente de sortie spécifique pour chaque message de file d’attente d’entrée.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Cet exemple de code C# écrit plusieurs messages en utilisant `ICollector<T>` (spécifiez `IAsyncCollector<T>` dans une fonction asynchrone) :

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a> Déclencheur d’objet blob Azure Storage

#### Fichier function.json pour un déclencheur d’objets blob de stockage Azure

Le fichier *function.json* spécifie les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour l’objet blob.
- `path` : chemin qui spécifie le conteneur à surveiller, et éventuellement un modèle de nom d’objet blob.
- `connection` : nom d’un paramètre d’application contenant une chaîne de connexion de stockage. Si vous laissez `connection` vide, le déclencheur fonctionnera avec la chaîne de connexion de stockage par défaut pour le conteneur de fonctions, qui est spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type` : doit être défini sur *blobTrigger*.
- `direction` : doit être défini sur *in*.

Exemple *function.json* pour un déclencheur d’objets blob de stockage qui surveille les objets blob qui sont ajoutés au conteneur samples-workitems :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### Types pris en charge pour le déclencheur d’objet blob

Le blob peut être désérialisé sur n’importe lequel des types suivants dans les fonctions C # ou de Node :

* Objet (de JSON)
* String

Dans les fonctions C#, vous pouvez également lier les types suivants :

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Autres types de désérialisation par [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb)

#### Exemple de code C# de déclencheur d’objet blob

Cet exemple de code C# enregistre le contenu de chaque objet blob qui est ajouté au conteneur surveillé.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### Modèles de nom de déclencheur d’objet blob

Vous pouvez spécifier un modèle de nom d’objet blob dans la propriété `path`. Par exemple :

```json
"path": "input/original-{name}",
```

Ce chemin d’accès trouverait un objet blob nommé *original-Blob1.txt* dans le conteneur *input*, et la variable `name` du code de fonction présenterait la valeur `Blob1`.

Autre exemple :

```json
"path": "input/{blobname}.{blobextension}",
```

Ce chemin d’accès trouverait également un objet blob nommé *original-Blob1.txt*, et les variables `blobname` et `blobextension` du code de fonction présenteraient respectivement les valeurs *original-Blob1* et *txt*.

Vous pouvez restreindre les types d’objets blob qui déclenchent la fonction en spécifiant un modèle avec une valeur fixe pour l’extension de fichier. Si vous définissez la propriété `path` sur *samples/{nom}.png*, seuls les objets blob *.png* du conteneur *samples* déclencheront la fonction.

Si vous devez spécifier un modèle de nom pour les noms d’objet blob qui présentent des accolades, doublez ces dernières. Par exemple, si vous souhaitez rechercher des objets blob, dans le conteneur *images*, qui présentent des noms comme suit :

		{20140101}-soundfile.mp3

Utilisez la valeur ci-après pour la propriété `path` :

		images/{{20140101}}-{name}

Dans cet exemple, la variable `name` présenterait la valeur *soundfile.mp3*.

#### Reçus d’objets blob

Le runtime Azure Functions vérifie qu’aucune fonction de déclencheur d’objet blob n’est appelée plusieurs fois pour un même objet blob, nouveau ou mis à jour. Pour ce faire, il tient à jour les *reçus d’objets blob* afin de déterminer si la version d’un objet blob donné a été traitée.

Les reçus d’objets blob sont stockés dans un conteneur appelé *azure-webjobs-hosts* associé au compte de stockage Microsoft Azure indiqué par la chaîne de connexion AzureWebJobsStorage. Un reçu d’objet blob contient les informations suivantes :

* Fonction appelée pour l’objet blob ("*{nom du conteneur de fonctions}*.Functions.*{nom de la fonction}*", par exemple : "functionsf74b96f7.Functions.CopyBlob")
* Nom du conteneur
* Type d’objet blob (« BlockBlob » ou « PageBlob »)
* Nom de l’objet blob
* ETag (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

Si vous souhaitez forcer le retraitement d’un objet blob, vous pouvez supprimer manuellement le reçu de l’objet blob à partir du conteneur *azure-webjobs-hosts*.

#### Gestion des objets blob incohérents

Lorsqu’une fonction de déclencheur d’objet blob échoue, le Kit de développement logiciel (SDK) l’appelle de nouveau, au cas où l’échec aurait été provoqué par une erreur temporaire. Si le problème est occasionné par le contenu de l’objet blob, la fonction échoue chaque fois qu’elle tente de traiter cet objet. Par défaut, le Kit de développement logiciel (SDK) appelle une fonction jusqu’à 5 fois pour un objet blob donné. En cas d’échec après la cinquième tentative, le Kit de développement logiciel (SDK) ajoute un message à la file d’attente nommée *webjobs-blobtrigger-poison*.

Le message en file d’attente associé aux objets blob incohérents correspond à un objet JSON, qui contient les propriétés suivantes :

* FunctionId (au format *{nom du conteneur de fonctions}*.Functions.*{nom de la fonction}*)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

#### Interrogation de blob pour les grands conteneurs

Si le conteneur d’objets blob que le déclencheur surveille contient plus de 10 000 objets blob, le runtime Functions analyse les fichiers journaux pour effectuer le suivi des objets blob nouveaux ou modifiés. Ce processus ne se déroule pas en temps réel ; il se peut qu’une fonction ne se déclenche que quelques minutes ou plus après la création de l’objet blob. En outre, des [journaux de stockage sont créés sur la base du « meilleur effort »](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; il n’est pas garanti que tous les événements soient capturés. Dans certaines conditions, des journaux peuvent être omis. Si les limitations relatives à la vitesse et à la fiabilité des déclencheurs d’objet blob pour les conteneurs volumineux ne sont pas acceptables pour votre application, il est recommandé de créer un message de file d’attente lorsque vous créez l’objet blob, et d’utiliser un déclencheur de file d’attente plutôt qu’un déclencheur d’objet blob pour traiter cet objet blob.
 
## <a id="storageblobbindings"></a> Liaisons d’entrée et de sortie d’objet blob Azure Storage

#### Fichier function.json pour une liaison d’entrée ou de sortie d’objets blob de stockage

Le fichier *function.json* spécifie les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour l’objet blob.
- `path` : chemin qui spécifie le conteneur dans lequel lire ou écrire l’objet blob, et éventuellement un modèle de nom d’objet blob.
- `connection` : nom d’un paramètre d’application contenant une chaîne de connexion de stockage. Si vous laissez `connection` vide, la liaison fonctionnera avec la chaîne de connexion de stockage par défaut pour le conteneur de fonctions, qui est spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type` : doit être défini sur *blob*.
- `direction` : défini sur *in* ou *out*.

Exemple de *function.json* pour une liaison d’entrée ou de sortie d’objet blob de stockage utilisant un déclencheur de file d’attente pour copier un objet blob :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Types pris en charge pour l’entrée et la sortie d’objet BLOB

La liaison `blob` peut sérialiser ou désérialiser les types suivants dans les fonctions Node.js ou C# :

* Objet (`out T` en C# pour l’objet blob de sortie : crée un objet blob en tant qu’objet Null si le paramètre présente la valeur Null lorsque la fonction se termine)
* Chaîne (`out string` en C# pour l’objet blob de sortie : crée un objet blob uniquement si le paramètre de chaîne ne présente pas la valeur Null lorsque la fonction renvoie les résultats)

Dans les fonctions C#, vous pouvez également lier les types suivants :

* `TextReader` (entrée uniquement)
* `TextWriter` (sortie uniquement)
* `Stream`
* `CloudBlobStream` (sortie uniquement)
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`

#### Exemple de code C# de sortie d’objet blob

Cet exemple de code C# copie un objet blob dont le nom est reçu dans un message de file d’attente.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a> Liaisons d’entrée et de sortie de tables Azure Storage

#### Fichier function.json pour les tables de stockage

Le fichier *function.json* spécifie les propriétés suivantes :

- `name` : nom de variable utilisé dans le code de fonction pour la liaison de table.
- `tableName` : nom de la table.
- `partitionKey` et `rowKey` : propriétés utilisées conjointement pour lire une entité unique dans une fonction C# ou Node, ou pour écrire une entité unique dans une fonction Node.
- `take` : nombre maximal de lignes à lire pour l’entrée de table dans une fonction Node.
- `filter` : expression de filtre OData pour l’entrée de table dans une fonction Node.
- `connection` : nom d’un paramètre d’application contenant une chaîne de connexion de stockage. Si vous laissez `connection` vide, la liaison fonctionnera avec la chaîne de connexion de stockage par défaut pour le conteneur de fonctions, qui est spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type` : doit être défini sur *table*.
- `direction` : défini sur *in* ou *out*.

L’exemple *function.json* suivant utilise un déclencheur de file d’attente pour lire une seule ligne du tableau. Le fichier JSON fournit une valeur de clé de partition codée en dur et spécifie que la clé de ligne provient du message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Types pris en charge pour l’entrée et la sortie des tables de stockage

La liaison `table` peut sérialiser ou désérialiser les objets dans les fonctions Node.js ou C#. Les objets auront des propriétés RowKey et PartitionKey.

Dans les fonctions C#, vous pouvez également lier les types suivants :

* `T` où `T` implémente `ITableEntity`
* `IQueryable<T>` (entrée uniquement)
* `ICollector<T>` (sortie uniquement)
* `IAsyncCollector<T>` (sortie uniquement)

#### Scénarios de liaison des tables de stockage

La liaison de tables prend en charge les scénarios suivants :

* Lecture d’une seule ligne dans une fonction C# ou Node.

	Définissez `partitionKey` et `rowKey`. Les propriétés `filter` et `take` ne sont pas utilisées dans ce scénario.

* Lecture de plusieurs lignes dans une fonction C#.

	Le runtime Functions fournit un objet `IQueryable<T>` lié à la table. Le type `T` doit dériver de `TableEntity` ou implémenter `ITableEntity`. Les propriétés `partitionKey`, `rowKey`, `filter` et `take` ne sont pas utilisées dans ce scénario ; s’il y a lieu, vous pouvez utiliser l’objet `IQueryable` pour effectuer le filtrage éventuellement requis.

* Lecture de plusieurs lignes dans une fonction Node.

	Définissez les propriétés `filter` et `take`. Ne définissez pas `partitionKey` ni `rowKey`.

* Écriture d’une ou de plusieurs lignes dans une fonction C#.

	Le runtime Functions fournit un objet `ICollector<T>` ou `IAsyncCollector<T>` lié à la table, où `T` spécifie le schéma des entités à ajouter. En général, le type `T` dérive de `TableEntity` ou implémente `ITableEntity`, mais ce n’est pas obligatoire. Les propriétés `partitionKey`, `rowKey`, `filter` et `take` ne sont pas utilisées dans ce scénario.

#### Exemple de tables de stockage : lire une entité de table unique en C# ou en Node

L’exemple de code C# ci-après fonctionne avec le fichier *function.json* précédent pour lire une entité de table unique. Le message de file d’attente comporte la valeur de clé de ligne, et l’entité de table est lue dans un type défini dans le fichier *run.csx*. Le type inclut les propriétés `PartitionKey` et `RowKey`, et ne dérive pas de `TableEntity`.

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

L’exemple de code F# ci-après fonctionne également avec le fichier *function.json* précédent pour lire une entité de table unique.

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

L’exemple de code Node ci-après fonctionne également avec le fichier *function.json* précédent pour lire une entité de table unique.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### Exemple de tables de stockage : lire plusieurs entités de table en C# 

L’exemple de fichier *function.json* et de code C# ci-après lit les entités relatives à une clé de partition spécifiée dans le message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Le code C# ajoute une référence au SDK Azure Storage afin que le type d’entité puisse dériver de `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### Exemple de tables de stockage : créer des entités de table en C# 

Les exemples de fichiers *function.json* et *run.csx* ci-après indiquent comment écrire des entités de table en C#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### Exemple de tables de stockage : créer des entités de table en F#

Les exemples de fichiers *function.json* et *run.csx* ci-après montrent comment écrire des entités de table en F#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### Exemple de tables de stockage : créer une entité de table en Node

Les exemples de fichiers *function.json* et *run.csx* ci-après indiquent comment écrire une entité de table en Node.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Étapes suivantes

[AZURE.INCLUDE [Étapes suivantes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->