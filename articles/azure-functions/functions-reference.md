<properties
	pageTitle="Information de référence pour les développeurs sur Azure Functions | Microsoft Azure"
	description="Découvrez les concepts et les composants Azure Functions communs à tous les langages et liaisons."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Informations de référence pour les développeurs sur Azure Functions

Les fonctions Azure Functions partagent quelques concepts techniques et composants de base, quels que soient le langage et la liaison que vous utilisez. Avant de passer à l'apprentissage des détails propres à un langage ou une liaison donnés, veillez à lire cette présentation qui s'applique à l’ensemble d’entre eux.

Cet article suppose que vous avez déjà lu la [Vue d'ensemble d'Azure Functions](functions-overview.md) et que vous connaissez les [concepts du Kit de développement logiciel (SDK) WebJobs, notamment les déclencheurs, les liaisons et le runtime JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Azure Functions est fondé sur le SDK WebJobs.


## Code de fonction

La *fonction* est le principal concept d'Azure Functions. Vous écrivez du code pour une fonction dans le langage de votre choix et enregistrez le(s) fichier(s) de code et un fichier de configuration dans le même dossier. La configuration est au format JSON, et le fichier est nommé `function.json`. Plusieurs langages sont pris en charge, et chacun d’entre eux offre une expérience légèrement différente, optimisée pour fonctionner idéalement pour ce langage.

Le fichier `function.json` contient la configuration propre à une fonction, y compris ses liaisons. Le runtime lit ce fichier pour déterminer les événements à déclencher, les données à inclure lors de l'appel de la fonction et l'emplacement où envoyer les données transmises à partir de la fonction elle-même.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Vous pouvez empêcher le runtime d’exécuter la fonction en définissant la propriété `disabled` sur `true`.

La propriété `bindings` vous permet de configurer les liaisons et les déclencheurs. Chaque liaison partage quelques paramètres communs et des paramètres propres à un type de liaison donné. Chaque liaison requiert les paramètres suivants :

|Propriété|Valeurs/types|Commentaires|
|---|-----|------|
|`type`|string|Type de liaison. Par exemple, `queueTrigger`.
|`direction`|'in', 'out'| Indique si la liaison sert à recevoir des données dans la fonction ou à envoyer des données à partir de la fonction.
| `name` | string | Le nom qui sera utilisé pour les données liées dans la fonction. Pour C#, ce sera un nom d'argument ; pour JavaScript, ce sera la clé dans une liste de clés/valeurs.

## Conteneur de fonctions

Un conteneur de fonctions est constitué d’une ou de plusieurs des fonctions individuelles qui sont gérées ensemble par Azure App Service. Toutes les fonctions d’un conteneur de fonctions partagent le même plan de tarification, le même déploiement continu et la même version du runtime. Les fonctions écrites dans plusieurs langages peuvent partager le même conteneur de fonctions. Considérez un conteneur de fonctions comme un moyen d’organiser et de gérer collectivement vos fonctions.

## Runtime (hôte de script et hôte web)

Le runtime, également appelé hôte de script, est l’hôte du Kit de développement logiciel (SDK) WebJobs sous-jacent qui écoute les événements, collecte et envoie les données et, enfin, exécute votre code.

Pour faciliter les déclencheurs HTTP, il existe également un hôte web conçu pour précéder l'hôte de script dans les scénarios de production. Cela permet d'isoler l'hôte de script du trafic frontal géré par l'hôte web.

## Structure des dossiers

[AZURE.INCLUDE [structure de dossier de fonctions](../../includes/functions-folder-structure.md)]

Lorsque vous configurez un projet pour déployer des fonctions dans un conteneur de fonctions dans Azure App Service, vous pouvez traiter cette structure de dossiers comme le code de votre site. Vous pouvez utiliser des outils existants, comme le déploiement et l'intégration continus, ou des scripts de déploiement personnalisés pour effectuer l'installation du package ou la transpilation de code au moment du déploiement.

## <a id="fileupdate"></a> Comment mettre à jour les fichiers du conteneur de fonctions

L’éditeur de fonctions intégré au portail Azure vous permet de mettre à jour le fichier *function.json* et le fichier de code pour une fonction. Pour télécharger ou mettre à jour d’autres fichiers comme *package.json* ou *project.json* ou les dépendances, vous devez utiliser d’autres méthodes de déploiement.

Les conteneurs de fonctions sont créés sur App Service, de sorte que toutes les [options de déploiement disponibles sur les applications web standard](../app-service-web/web-sites-deploy.md) le sont également sur les conteneurs de fonctions. Voici des méthodes que vous pouvez utiliser pour télécharger ou mettre à jour les fichiers du conteneur de fonctions.

#### Pour utiliser l’Éditeur App Service

1. Dans le portail Azure Functions, cliquez sur **Paramètres du conteneur de fonctions**.

2. Dans la section **Paramètres avancés** cliquez sur **Accéder aux paramètres App Service**.

3. Cliquez sur **Éditeur App Service** dans App Menu Nav sous **OUTILS DE DÉVELOPPEMENT**.

4.  Cliquez sur **Atteindre**.

	Une fois l’Éditeur App Service chargé, le fichier *host.json* est les dossiers de fonctions s’affichent sous *wwwroot*.

5. Ouvrez des fichiers pour les modifier, ou téléchargez des fichiers par glisser-déplacer depuis votre machine de développement.

#### Pour utiliser la fonction de système d'extrémité SCM (Kudu) de l’application

1. Accédez à `https://<function_app_name>.scm.azurewebsites.net`.

2. Cliquez sur **Console de débogage > CMD**.

3. Accédez à `D:\home\site\wwwroot` pour mettre à jour *host.json* ou `D:\home\site\wwwroot<function_name>` pour mettre à jour les fichiers d’une fonction.

4. Glissez-déplacez un fichier à télécharger dans le dossier approprié dans la grille de fichiers. La grille de fichiers offre deux zones dans lesquelles vous pouvez déposer un fichier. Pour les fichiers *.zip*, une zone s’affiche avec le libellé « Faites glisser ici pour charger et décompresser ». Pour les autres types de fichier, déposez le fichier dans la grille de fichiers, mais en dehors de cette zone.

#### Pour utiliser le FTP

1. Suivez les instructions [ici](../app-service-web/web-sites-deploy.md#ftp) pour configurer le FTP.

2. Lorsque vous êtes connecté au site du conteneur de fonctions, copiez le fichier *host.json* mis à jour vers `/site/wwwroot` ou copiez les fichiers de fonction vers `/site/wwwroot/<function_name>`.

#### Pour utiliser le déploiement continu

Suivez les instructions de la rubrique [Déploiement continu pour Azure Functions](functions-continuous-deployment.md).

## Exécution en parallèle

Lorsque plusieurs événements de déclenchement se produisent plus rapidement qu'un runtime de fonction monothread ne peut les traiter, le runtime peut appeler la fonction plusieurs fois en parallèle. Si un conteneur de fonctions utilise le [plan de service dynamique](functions-scale.md#dynamic-service-plan), il peut monter automatiquement augmenter la taille des instances. Que l’application s’exécute sur le plan de service dynamique ou sur un [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) standard, chaque instance de l’application de fonction peut traiter en parallèle des appels de fonction simultanés en utilisant plusieurs threads. Le nombre maximal d'appels de fonction simultanés dans chaque instance d'application de fonction varie en fonction de la taille de la mémoire de l'application de fonction.

## Azure Functions Pulse  

Pulse est un flux d'événements en direct qui indique la fréquence d'exécution de votre fonction, ainsi que les réussites et les échecs. Vous pouvez également surveiller le temps moyen d'exécution. Nous allons y ajouter plus de fonctionnalités et d’éléments de personnalisation au fil du temps. Vous pouvez accéder à la page **Pulse** à partir de l’onglet **Analyse**.

## Référentiels

Le code pour Azure Fonctions est open source et stocké dans des référentiels GitHub :

* [Runtime Azure Functions](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portail Azure Functions](https://github.com/projectkudu/AzureFunctionsPortal)
* [Modèles Azure Functions](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Kit de développement logiciel Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensions du kit de développement logiciel Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## Liaisons

Voici un tableau de toutes les liaisons prises en charge.

[AZURE.INCLUDE [calcul dynamique](../../includes/functions-bindings.md)]

## Problèmes liés aux rapports

[AZURE.INCLUDE [Problèmes liés aux rapports](../../includes/functions-reporting-issues.md)]

## Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Informations de référence pour les développeurs C# sur Azure Functions](functions-reference-csharp.md)
* [Informations de référence pour les développeurs F# sur Azure Functions](functions-reference-fsharp.md)
* [Azure Functions NodeJS developer reference (Référence pour les développeurs NodeJS Azure Functions)](functions-reference-node.md)
* [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md)
* [Azure Functions : The Journey](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) (Découverte d’Azure Functions) sur le blog d’Azure App Service. Un historique sur le développement d’Azure Functions.

<!---HONumber=AcomDC_0921_2016-->