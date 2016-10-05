<properties
	pageTitle="Développer et exécuter Azure Functions en local | Microsoft Azure"
	description="Apprenez à coder et tester des fonctions Azure dans Visual Studio avant de les exécuter dans Azure App Service."
	services="functions"
	documentationCenter="na"
	authors="tdykstra"
	manager="erikre"
	editor=""/>

<tags
	ms.service="functions"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="glenga"/>

# Procédure de codage et de test d’Azure Functions dans Visual Studio

## Vue d'ensemble

Cet article explique comment exécuter [Azure Functions](functions-overview.md) en local en téléchargeant le référentiel GitHub [WebJobs.Script](https://github.com/Azure/azure-webjobs-sdk-script/) et en exécutant la solution Visual Studio qu’il contient.

Le runtime d’Azure Functions est une implémentation du projet open source WebJobs.Script. Ce projet est à son tour basé sur le [Kit de développement logiciel (SDK) Azure WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) et les deux infrastructures peuvent s’exécuter localement. Toutefois, vous devez vous connecter à un compte de stockage Azure, étant donné que le SDK WebJobs utilise les fonctionnalités de compte de stockage non prises en charge par l’émulateur de stockage.

Les fonctions sont faciles à coder et tester dans le portail Azure, mais il est parfois utile de les utiliser localement avant l’exécution dans Azure. Par exemple, il est plus facile d’écrire du code pour certains des langages pris en charge par Azure Functions dans Visual Studio, en raison de la fourniture de la fonctionnalité [IntelliSense](https://msdn.microsoft.com/library/hcw1s69b.aspx). Or, même si vous pouvez déboguer une fonction à distance, il est plus facile et plus rapide de la déboguer en local. Lorsque vous exécutez en local, vous pouvez déboguer et définir des points d’arrêt dans le code de la fonction ainsi que dans le code hôte WebJobs.Script.

>[AZURE.NOTE] Azure Functions est actuellement disponible en version préliminaire. L’expérience globale incluant les outils est toujours en cours de développement rapide. Les procédures décrites dans cet article ne reflètent pas l'expérience de développement local finale, et nous aimerions que vous [nous fassiez part de vos commentaires](https://feedback.azure.com/forums/355860-azure-functions).

## Composants requis

### Un compte Azure avec un conteneur de fonctions

Cet article suppose que vous avez travaillé avec [Azure Functions](functions-overview.md) dans le portail et que vous connaissez déjà des concepts Azure Functions comme [les déclencheurs, les liaisons et JobHost](functions-reference.md).

Lorsque vous exécutez des fonctions localement, vous obtenez des sorties dans la fenêtre de console. Toutefois, vous pourrez également utiliser le tableau de bord hébergé par un conteneur de fonctions dynamique pour afficher des journaux et des appels de fonction.

### Visual Studio 2015 avec le dernier Kit de développement logiciel (SDK) Azure pour .NET

Si vous n’avez pas Visual Studio 2015 ou si vous n’avez pas le Kit de développement logiciel (SDK) Azure en cours, [téléchargez le SDK Azure pour Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio 2015 est automatiquement installé avec le SDK si vous n’en disposez pas déjà.

### Conditions préalables conditionnelles

Certaines ressources et installations de logiciels Azure sont requises uniquement si vous projetez d’exécuter des fonctions qui les utilisent, par exemple :

* Ressources Azure
	* Service Bus
	* Tables faciles
	* Base de données de documents
	* Event Hubs
	* Notification Hubs

* Compilateurs et moteurs de script
	* F#
	* BASH
	* Python
	* PHP

Pour plus d’informations sur ces exigences, y compris les variables d’environnement que vous devez définir pour celles-ci, consultez les [pages wiki du référentiel WebJobs.Script](https://github.com/Azure/azure-webjobs-sdk-script/wiki/home)

Si votre objectif est de contribuer au projet WebJobs.SDK, vous devez satisfaire toutes les conditions préalables conditionnelles pour exécuter des tests complets.

## Pour exécuter en local

1. [Clonez](https://github.com/Azure/azure-webjobs-sdk-script/) ou [téléchargez](https://github.com/Azure/azure-webjobs-sdk-script/archive/master.zip) le référentielWebjobs.Script.

2. Définissez les variables d’environnement pour les chaînes de connexion de stockage.

	* AzureWebJobsStorage
	* AzureWebJobsDashboard

	Vous pouvez obtenir ces valeurs à partir du panneau du portail **Paramètres de l’application** d’App Service pour un conteneur de fonctions.

	a. Sur le panneau **Conteneur de fonctions**, cliquez sur **Paramètres du conteneur de fonctions**.

	![Cliquez sur Paramètres du conteneur d’applications](./media/functions-run-local/clickfuncappsettings.png)
 
	b. Dans le panneau **Paramètres du conteneur de fonctions**, cliquez sur **Accéder aux paramètres App Service**.

	![Cliquez sur Paramètres App Service](./media/functions-run-local/clickappsvcsettings.png)
 
	c. Dans le panneau **Paramètres**, cliquez sur **Paramètres de l’application**.

	![Cliquez sur Paramètres de l’application](./media/functions-run-local/clickappsettings.png)
 
	d. Dans le panneau **Paramètres de l’application**, faites défiler l’écran jusqu’à la section **Paramètres de l’application** et trouvez les paramètres du SDK WebJobs.

	![Paramètres WebJobs](./media/functions-run-local/wjsettings.png)

	e. Définissez une variable d’environnement portant les mêmes nom et valeur que le paramètre d’application `AzureWebJobsStorage`.

	f. Faites-en de même pour le paramètre d’application `AzureWebJobsDashboard`.

2. Créez une variable d’environnement nommée AzureWebJobsServiceBus et affectez-lui votre chaîne de connexion Service Bus.

	Cette variable d’environnement est requise pour les liaisons Service Bus. Nous vous recommandons de la définir même si vous n’utilisez pas les liaisons Service Bus. Dans certains scénarios, vous pouvez voir des exceptions si la chaîne de connexion Service Bus n’est pas définie, quelles que soient les liaisons en cours d’utilisation.

3. Assurez-vous que les autres variables d’environnement dont vous avez besoin sont définies. (Consultez la section précédente [Conditions préalables conditionnelles](#conditional-prerequisites)).

4. Démarrez Visual Studio et ouvrez la solution WebJobs.Script.

6. Définissez le projet de démarrage. Si vous souhaitez exécuter les fonctions qui utilisent des déclencheurs HTTP ou WebHook, choisissez **WebJobs.Script.WebHost** ; sinon, choisissez **WebJobs.Script.Host**.

4. Si votre projet de démarrage est WebJobs.Script.Host :

	a. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet WebJobs.Script.Host, puis cliquez sur **Propriétés**.

	b. Dans l’onglet **Debug** de la fenêtre **Propriétés du projet**, définissez **Arguments de la ligne de commande** sur `..\..\..\..\sample`.

	![Arguments de ligne de commande](./media/functions-run-local/cmdlineargs.png)

	Il s’agit d’un chemin d’accès relatif au dossier *sample* dans le référentiel. Le dossier *sample* contient un fichier *host.json* contenant des paramètres globaux et un dossier pour chaque fonction exemple.

	Pour démarrer, le plus simple est d’utiliser le dossier *sample* fourni. Vous avez ensuite la possibilité d’ajouter vos propres fonctions au dossier *sample* ou d’utiliser un dossier contenant un *host.json* et des dossiers de fonctions.

5. Si votre projet de démarrage est WebJobs.Script.WebHost :

	a. Définissez une variable d’environnement AzureWebJobsScriptRoot sur le chemin d’accès complet au dossier `sample`.

	b. Redémarrez Visual Studio pour récupérer la nouvelle valeur de variable d’environnement.

	Reportez-vous à la section [Clés API](#api-keys) pour plus d’informations sur l’exécution des fonctions de déclencheur HTTP.

5. Ouvrez le fichier *sample\\host.json* et ajoutez une propriété `functions` pour spécifier les fonctions que vous souhaitez exécuter.

	Par exemple, avec le JSON suivant, le JobHost du SDK WebJobs recherche uniquement deux fonctions.

		{
		  "functions": [ "TimerTrigger-CSharp", "QueueTrigger-CSharp"],
		  "id": "5a709861cab44e68bfed5d2c2fe7fc0c"
		}

	Lorsque vous utilisez votre propre dossier à la place du dossier *sample*, incluez-y uniquement les fonctions que vous voulez exécuter. Vous pouvez omettre la propriété `functions` dans *host.json*.
 
6. Créez et exécutez la solution.

	La fenêtre de console indique que le JobHost recherche uniquement les fonctions spécifiées dans le fichier `host.json`.

		Found the following functions:
		Host.Functions.QueueTrigger-CSharp
		Host.Functions.TimerTrigger-CSharp
		Job host started

	Quand vous démarrez le projet WebHost, vous obtenez une page de navigateur vide, car l’URL de base du projet ne comprend aucun contenu. Reportez-vous à la section [Clés API](#apikeys) pour plus d’informations sur les URL à utiliser pour les fonctions de déclencheur HTTP.

## Affichage de la sortie de fonction

Accédez au tableau de bord de votre conteneur de fonctions pour voir les appels de fonction et les sorties de journaux correspondants.

Le tableau de bord est à l’adresse URL suivante :

	https://{function app name}.scm.azurewebsites.net/azurejobs/#/functions

La page **Fonctions** affiche une liste des fonctions qui ont été exécutées et une liste des appels de fonctions.

![Détails des appels](./media/functions-run-local/invocationdetail.png)

Cliquez sur un appel pour voir la page **Détails des appels**, qui indique quand la fonction a été déclenchée, l’heure d’exécution approximative et sa réussite. Cliquez sur le bouton **Basculer la sortie** pour afficher les journaux générés par le code de la fonction.

![Détails des appels](./media/functions-run-local/invocationdetail.png)

## <a id="apikeys"></a> Clés API pour les déclencheurs HTTP

Pour exécuter une fonction HTTP ou WebHook, vous aurez besoin d’une clé API, sauf si vous incluez `"authLevel": "anonymous"` dans le fichier *function.json*.

Par exemple, si la clé API est `12345`, vous pouvez déclencher la fonction *HttpTrigger* avec l’URL suivante, lorsque le projet WebJobs.Script.WebHost est en cours d’exécution.

	http://localhost:28549/api/httptrigger?code=12345

(Sinon, vous pouvez placer la clé API dans l’en-tête HTTP `x-functions-key`.)

Les clés API sont stockées dans les fichiers `.json` du dossier [App\_Data/secrets](https://github.com/Azure/azure-webjobs-sdk-script/tree/master/src/WebJobs.Script.WebHost/App_Data/secrets) du projet WebJobs.Script.WebHost.

### Clés API qui s’appliquent à toutes les fonctions HTTP et WebHook

Le fichier *host.json* dans le dossier *App\_Data/secrets* comporte deux clés :

```json
{
  "masterKey": "hyexydhln844f2mb7hyexydhln844f2mb7",
  "functionKey": "7hyexydhn844f2mb7hyexydhln844f2mb7"
}
```

La propriété `functionKey` stocke une clé qui peut être utilisée pour n’importe quelle fonction HTTP ou WebHook si aucun remplacement de cette fonction donnée n’est défini. Cette fonctionnalité élimine la nécessité de toujours définir de nouvelles clés API pour chaque fonction que vous créez.

La propriété `masterKey` stocke une clé qui est utile dans certains scénarios de test :

* Si vous appelez une fonction WebHook avec une clé principale, le SDK WebJobs ignore la validation de la signature du fournisseur WebHook.

* Si vous appelez une fonction HTTP ou WebHook avec une clé principale, la fonction est déclenchée même si elle est désactivée dans le fichier *function.json*. Ainsi, dans le portail Azure, le bouton **Exécuter** fonctionne même pour les fonctions désactivées.
 
### Clés API qui s’appliquent à des fonctions individuelles

Les fichiers nommés *{nom\_fonction}.json* contiennent la clé API d’une fonction donnée. Par exemple, l’exemple de contenu JSON suivant dans *App\_Data/secrets/HttpTrigger.json* définit la clé API pour la fonction `HttpTrigger`.

```json
{
  "key": "844f2mdhn844f2mb7hyexydhln844f2mb7"
}
```

## Utilisation des références du package NuGet dans les fonctions  

En raison de la façon dont les références NuGet sont actuellement traitées, assurez-vous de « toucher » le fichier *project.json* pendant que l’ordinateur hôte est en cours d’exécution. L’hôte surveille les modifications de fichier et lance une restauration quand il détecte des modifications. En outre, *NuGet.exe* (version 3.3.0 recommandée) doit être compris dans le chemin. Sinon, vous devez définir une variable d’environnement nommée AzureWebJobs\_NuGetPath, dont le chemin est défini sur *NuGet.exe*.

## Résolution de problèmes

Les modifications de variable d’environnement effectuées pendant l’exécution de Visual Studio ne sont pas récupérées automatiquement. Si vous avez ajouté ou modifié une variable d’environnement après le démarrage de Visual Studio, arrêtez Visual Studio et redémarrez-le pour vous assurer qu’il récupère les valeurs actuelles.

Lorsque vous effectuez un débogage, vous pouvez obtenir plus d’informations sur les exceptions en sélectionnant **Exceptions Common Language Runtime** dans la fenêtre **Paramètres d’exception** (CTRL-ALT-E pour ouvrir la fenêtre).

Pour obtenir plus d’informations sur les exceptions pendant le débogage, vous pouvez aussi définir un point d’arrêt dans le bloc `catch` de la boucle principale de l’hôte de script. Vous trouverez cela dans le projet WebJobs.Script, dans *Host/ScriptHostManager.cs*, au sein de la méthode `RunAndBlock`.

## Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Informations de référence pour les développeurs C# sur Azure Functions](functions-reference-csharp.md)
* [Informations de référence pour les développeurs F# sur Azure Functions](functions-reference-fsharp.md)
* [Azure Functions NodeJS developer reference (Référence pour les développeurs NodeJS Azure Functions)](functions-reference-node.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0921_2016-->