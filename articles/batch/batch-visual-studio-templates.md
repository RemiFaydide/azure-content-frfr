<properties
	pageTitle="Modèles Visual Studio pour Azure Batch | Microsoft Azure"
	description="Découvrez comment ces modèles de projet Visual Studio peuvent vous aider à implémenter et à exécuter vos charges de travail nécessitant beaucoup de ressources sur Azure Batch"
	services="batch"
	documentationCenter=".net"
	authors="fayora"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="09/07/2016"
	ms.author="marsma" />

# Modèles de projet Visual Studio pour Azure Batch

Les **modèles Visual Studio du gestionnaire de travaux** et du **processeur de tâches** pour Batch fournissent le code vous permettant d’implémenter et d’exécuter sans effort vos charges de travail nécessitant beaucoup de ressources sur Batch. Ce document décrit ces modèles et fournit des conseils pour leur utilisation.

>[AZURE.IMPORTANT] Cet article traite uniquement les informations relatives à ces deux modèles et suppose que vous maîtrisez le service Batch ainsi que les concepts clés qui y sont liés : pools, nœuds de calcul, travaux et tâches, tâches du gestionnaire de travaux, variables d’environnement et autres informations pertinentes. Pour plus d’informations, consultez [Notions de base d’Azure Batch](batch-technical-overview.md), [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md), et [Prise en main de la bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md).

## Vue d’ensemble globale

Les modèles du gestionnaire de travaux et du processeur de tâches peuvent être utilisés pour créer deux composants utiles :

* Une tâche du gestionnaire de travaux qui permet de fractionner un travail afin de le découper en plusieurs tâches pouvant être exécutées indépendamment, en parallèle.

* Un processeur de tâches qui peut être utilisé pour effectuer le prétraitement et le post-traitement d’une ligne de commande d’application.

Par exemple, dans un scénario de rendu vidéo, l’outil de fractionnement du travail diviserait le travail vidéo en plusieurs centaines voire milliers de tâches distinctes qui traiteraient chaque image séparément. En conséquence, le processeur de tâches appellerait l’application de rendu ainsi que tous les processus dépendants nécessaires au rendu de chaque image, et effectuerait des actions supplémentaires (par exemple, la copie de l’image rendue sur un emplacement de stockage).

>[AZURE.NOTE] Les modèles du gestionnaire de travaux et du processeur de tâches étant indépendants l’un de l’autre, vous pouvez choisir d’utiliser les deux ou un seul, en fonction des besoins de votre travail de calcul et de vos préférences.

Comme indiqué dans le diagramme ci-dessous, un travail de calcul qui utilise ces modèles passe par trois étapes :

1. Le code client (par exemple, application, service web, etc.) soumet un travail au service Batch sur Azure, spécifiant le programme du gestionnaire de travaux en tant que tâche de son gestionnaire de travaux.

2. Le service Batch exécute la tâche du gestionnaire de travaux sur un nœud de calcul, et l’outil de fractionnement du travail lance le nombre spécifié de tâches du processeur de tâches sur autant de nœuds de calcul que nécessaire, en fonction des paramètres et des spécifications indiqués dans son code.

3. Les tâches du processeur de tâches sont exécutées de manière indépendante, en parallèle, pour traiter les données d’entrée et générer les données de sortie.

![Diagramme montrant comment le code client interagit avec le service Batch][diagram01]

## Composants requis

Voici les composants requis pour utiliser les modèles Batch :

* Un ordinateur disposant de Visual Studio 2015 ou d’une version plus récente.

* Les modèles Batch, qui sont disponibles dans la [galerie Visual Studio][vs_gallery] en tant qu’extensions Visual Studio. Il existe deux façons de se procurer les modèles :

  * Les installer à l’aide de la boîte de dialogue **Extensions et mises à jour** dans Visual Studio (pour plus d’informations, consultez [Recherche et utilisation des extensions Visual Studio][vs_find_use_ext]). Dans la boîte de dialogue **Extensions et mises à jour**, recherchez et téléchargez les deux extensions suivantes :

    * Le gestionnaire de travaux Azure Batch avec l’outil de fractionnement du travail
    * Le processeur de tâches Azure Batch

  * Les télécharger à partir de la galerie en ligne pour Visual Studio : [Modèles de projet Microsoft Azure Batch][vs_gallery_templates]

* Si vous prévoyez d’utiliser la fonctionnalité [Packages d’applications](batch-application-packages.md) pour déployer le gestionnaire de travaux et le processeur de tâches sur les nœuds de calcul Batch, vous devez lier un compte de stockage à votre compte Batch.

## Préparation

Nous vous recommandons de créer une solution pouvant contenir à la fois votre gestionnaire de travaux et votre processeur de tâches, afin de faciliter le partage du code entre leurs programmes. Pour créer cette solution, procédez comme suit :

1. Ouvrez Visual Studio 2015 et sélectionnez **Fichier** > **Nouveau** > **Projet**.

2. Sous **Modèles**, développez **Autres Types de projets**, cliquez sur **Solutions Visual Studio**, puis sélectionnez **Nouvelle Solution**.

3. Entrez un nom décrivant votre application et l’objectif de cette solution (par exemple, « ProgrammesTâchesBatchLitware »).

4. Cliquez sur **OK** pour créer la solution.

## Modèle du gestionnaire de travaux

Le modèle du gestionnaire de travaux vous permet d’implémenter une tâche du gestionnaire de travaux pouvant effectuer les actions suivantes :

* Fractionner un travail en plusieurs tâches.
* Soumettre ces tâches pour les exécuter dans Batch.

>[AZURE.NOTE] Pour plus d’informations sur le gestionnaire de travaux, consultez [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md#job-manager-task).

### Créer un gestionnaire de travaux à l’aide du modèle

Pour ajouter un gestionnaire de travaux à la solution que vous avez créée précédemment, procédez comme suit :

1. Ouvrez votre solution existante dans Visual Studio 2015.

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sur **Ajouter** > **Nouveau projet**.

3. Sous **Visual C#**, cliquez sur **Cloud**, puis sur **Azure Batch Job Manager with Job Splitter** (Gestionnaire de travaux Azure Batch avec outil de fractionnement du travail).

4. Entrez un nom décrivant votre application et identifiant ce projet en tant que le gestionnaire de travaux (par exemple, « GestionnaireTravauxLitware »).

5. Cliquez sur **OK** pour créer le projet.

6. Pour finir, générez le projet pour forcer Visual Studio à charger tous les packages NuGet référencés et vérifier que le projet est valide avant de commencer à le modifier.

### Les fichiers du modèle du gestionnaire de travaux et leur objectif

Lorsque vous créez un projet à l’aide du modèle du gestionnaire de travaux, il génère trois groupes de fichiers de code :

* Le fichier de programme principal (Program.cs). Il contient le point d’entrée du programme et la gestion des exceptions de niveau supérieur. Vous n’avez normalement pas besoin de modifier cette configuration.

* Le répertoire Framework. Il contient les fichiers responsables du travail « boilerplate » (réutilisable) effectué par le programme du gestionnaire de travaux – décompression de paramètres, ajout de tâches au travail Batch, etc. Vous n’avez normalement pas besoin de modifier ces fichiers.

* Le fichier de fractionnement du travail (JobSplitter.cs). C’est là que vous allez placer la logique spécifique à votre application pour le fractionnement d’un travail en tâches.

Bien sûr, vous pouvez si besoin ajouter des fichiers supplémentaires pour prendre en charge le code de fractionnement de votre travail, en fonction de la complexité de la logique de fractionnement du travail.

Le modèle génère également des fichiers de projet .NET standard comme des fichiers .csproj, app.config, packages.config, etc.

Le reste de cette section décrit les différents fichiers et leur structure de code, et explique ce que fait chaque classe.

![Explorateur de solutions Visual Studio affichant la solution du modèle du gestionnaire de travaux][solution_explorer01]

**Fichiers Framework**

* `Configuration.cs` : Encapsule le chargement des données de configuration du travail, telles que les détails du compte Batch, les informations d’identification du compte de stockage lié, les informations relatives aux travaux et aux tâches, et les paramètres du travail. Il donne également accès aux variables d’environnement définies par Batch (voir les paramètres d’environnement des tâches, dans la documentation Batch) via la classe Configuration.EnvironmentVariable.

* `IConfiguration.cs` : Résume l’implémentation de la classe de configuration, afin que vous puissiez soumettre votre outil de fractionnement du travail à un test unitaire, à l’aide d’un objet de configuration fictif ou simulé.

* `JobManager.cs` : Orchestre les composants du programme de gestionnaire de travaux. Il est responsable de l’initialisation et de l’appel de l’outil de fractionnement du travail, et de la distribution des tâches retournées par l’outil de fractionnement du travail à l’émetteur de la tâche.

* `JobManagerException.cs` : Représente une erreur nécessitant l’arrêt du gestionnaire de travaux. L’exception JobManagerException est utilisée pour encapsuler des erreurs « attendues » lorsque des informations de diagnostic spécifiques peuvent être fournies dans le cadre de l’arrêt.

* `TaskSubmitter.cs` : Cette classe est chargée d’ajouter au travail Batch les tâches retournées par l’outil de fractionnement du travail. La classe JobManager agrège la séquence de tâches en lots pour les ajouter au travail de manière efficace et opportune, puis appelle TaskSubmitter.SubmitTasks sur un thread d’arrière-plan pour chaque lot.

**Outil de fractionnement du travail**

`JobSplitter.cs` : Cette classe contient la logique spécifique à l’application pour fractionner le travail en tâches. L’infrastructure appelle la méthode JobSplitter.Split pour obtenir une séquence de tâches, qu’elle ajoute au travail à mesure que la méthode les retourne. C’est la classe où vous injecterez la logique de votre travail. Implémentez la méthode Split pour retourner une séquence d’instances CloudTask représentant les tâches dans lesquelles vous souhaitez partitionner le travail.

**Fichiers de projet de ligne de commande .NET standard**

* `App.config` : Fichier de configuration d’application .NET standard.

* `Packages.config`: Fichier de dépendance de package NuGet standard.

* `Program.cs` : Contient le point d’entrée du programme et la gestion des exceptions de niveau supérieur.

### Implémenter l’outil de fractionnement du travail

Lorsque vous ouvrez le projet de modèle du gestionnaire de travaux, il ouvre le fichier JobSplitter.cs par défaut. Vous pouvez implémenter la logique de fractionnement pour les tâches de votre charge de travail à l’aide de la méthode Split() présentée ci-dessous :

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

>[AZURE.NOTE] La section annotée dans la méthode `Split()` est la seule section de code du modèle du gestionnaire de travaux que vous êtes censé modifier en ajoutant la logique de fractionnement de vos travaux en différentes tâches. Si vous souhaitez modifier une autre section du modèle, assurez-vous de maîtriser le fonctionnement de Batch et essayez quelques-uns des [exemples de code Batch][github_samples].

Votre implémentation Split() a accès :

* Aux paramètres du travail, via le champ `_parameters`.
* À l’objet CloudJob représentant le travail, via le champ `_job`.
* À l’objet CloudTask représentant la tâche du gestionnaire de travaux, via le champ `_jobManagerTask`.

Il n’est pas nécessaire que votre implémentation `Split()` ajoute directement des tâches au travail. Au lieu de cela, votre code doit retourner une séquence d’objets CloudTask, qui seront automatiquement ajoutés au travail par les classes de l’infrastructure appelant l’outil de fractionnement du travail. Il est courant d’utiliser la fonctionnalité de l’itérateur C# (`yield return`) pour implémenter des outils de fractionnement du travail, car elle permet de lancer les tâches dès que possible plutôt que d’attendre qu’elles soient toutes calculées.

**Échec du fractionnement du travail**

Si votre outil de fractionnement du travail rencontre une erreur, il doit soit :

* Terminer la séquence à l’aide de l’instruction C# `yield break`, auquel cas le gestionnaire de travaux sera considéré comme ayant réussi ; ou

* Lever une exception, auquel cas le gestionnaire de travaux sera traité comme s’il avait échoué et pourra être relancé, selon la façon dont le client l’a configuré.

Dans les deux cas, toutes les tâches déjà retournées par l’outil de fractionnement du travail et ajoutées au travail Batch seront autorisées à être exécutées. Si vous souhaitez éviter cette situation, vous pouvez :

* Arrêter le travail avant qu’il ne soit retourné par l’outil de fractionnement du travail

* Formuler toute la collection de tâches avant de la retourner (autrement dit, retourner `ICollection<CloudTask>` ou `IList<CloudTask>` au lieu d’implémenter votre outil de fractionnement du travail à l’aide d’un itérateur C#)

* Utiliser les dépendances entre tâches pour rendre toutes les tâches dépendantes de la réussite du gestionnaire de travaux

**Nouvelles tentatives du gestionnaire de travaux**

Si le gestionnaire de travaux échoue, il peut être relancé par le service Batch en fonction des paramètres de nouvelles tentatives du client. En général, il n’y a aucun risque, car lorsque l’infrastructure ajoute des tâches au travail, elle ignore toutes les tâches qui existent déjà. Toutefois, si le calcul des tâches est coûteux, vous pouvez choisir de ne pas encourir les frais de recalcul des tâches qui ont déjà été ajoutées au projet. À l’inverse, s’il n’est pas garanti que la réexécution génère les mêmes ID de tâches, le comportement « ignorer les doublons » n’intervient pas. Dans ce cas, vous devez concevoir votre outil de fractionnement du travail de manière à ce qu’il détecte le travail déjà effectué et ne le répète pas, par exemple en effectuant CloudJob.ListTasks avant de commencer à générer des tâches.

### Codes de sortie et exceptions dans le modèle du gestionnaire de travaux

Les codes de sortie et les exceptions fournissent un mécanisme permettant de déterminer le résultat de l’exécution d’un programme, et peuvent aider à identifier les problèmes rencontrés lors de l’exécution du programme. Le modèle du gestionnaire de travaux implémente les codes de sortie et les exceptions décrites dans cette section.

Une tâche du gestionnaire de travaux implémentée avec le modèle du gestionnaire de travaux peut retourner trois codes de sortie possibles :

| Code | Description |
|------|-------------|
| 0 | Le gestionnaire de travaux s’est terminé avec succès. Le code de votre outil de fractionnement du travail a été exécuté entièrement, et toutes les tâches ont été ajoutées au travail. |
| 1 | La tâche du gestionnaire de travaux a échoué avec une exception rencontrée dans une section « attendue » du programme. L’exception a été convertie en une JobManagerException avec des informations de diagnostic et, si possible, des suggestions pour résoudre l’échec. |
| 2 | La tâche du gestionnaire de travaux a échoué avec une exception « inattendue ». L’exception a été enregistrée dans la sortie standard, mais le gestionnaire de travaux n’a pas pu ajouter d’informations de diagnostic ou de correction supplémentaires. |

En cas d’échec de la tâche du gestionnaire de travaux, il est possible que certaines tâches aient tout de même été ajoutées au service avant que l’erreur ne se soit produite. Ces tâches s’exécutent normalement. Pour plus d’informations sur ce chemin de code, consultez la rubrique « Échec du fractionnement du travail » ci-dessus.

Toutes les informations retournées par des exceptions sont écrites dans des fichiers stdout.txt et stderr.txt. Pour plus d’informations, consultez la rubrique [Gestion des erreurs](batch-api-basics.md#error-handling).

### Considérations du client

Cette section présente certaines exigences d’implémentation du client lors de l’appel d’un gestionnaire de travaux basé sur ce modèle. Pour plus d’informations, consultez la section expliquant [comment transmettre des paramètres et des paramètres d’environnement à partir du code client](#pass-environment-settings).

**Informations d’identification obligatoires**

Pour ajouter des tâches au travail Azure Batch, la tâche du gestionnaire de travaux requiert la clé et l’URL de votre compte Azure Batch. Vous devez les transmettre en tant que variables d’environnement appelées YOUR\_BATCH\_URL et YOUR\_BATCH\_KEY. Vous pouvez les définir dans les paramètres d’environnement de tâche du gestionnaire de travaux. Par exemple, dans un client C# :

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Informations d’identification de stockage**

En règle générale, le client n’a pas besoin de fournir les informations d’identification du compte de stockage lié à la tâche du gestionnaire de travaux, car (a) la plupart des gestionnaires de travaux n’ont pas besoin d’accéder explicitement au compte de stockage lié et (b) le compte de stockage lié est souvent fourni pour toutes les tâches en tant que paramètre d’environnement commun du travail. Si vous ne fournissez pas le compte de stockage lié via les paramètres d’environnement commun, et si le gestionnaire de travaux a besoin d’accéder au compte de stockage lié, vous devez fournir les informations d’identification de stockage comme suit :

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Paramètres de tâche du gestionnaire de travaux**

Le client doit définir l’indicateur *killJobOnCompletion* du gestionnaire de travaux sur **false**.

Généralement, le client peut définir *runExclusive* sur **false** en toute sécurité.

Le client doit utiliser la collection *resourceFiles* ou *applicationPackageReferences* pour que l’exécutable du gestionnaire de travaux (et ses fichiers DLL requis) soit déployé sur le nœud de calcul.

Par défaut, le gestionnaire de travaux ne sera pas relancé en cas d’échec. Selon la logique de votre gestionnaire de travaux, il est possible que le client souhaite autoriser les nouvelles tentatives via *constraints*/*maxTaskRetryCount*.

**Paramètres du travail**

Si l’outil de fractionnement du travail émet des tâches avec des dépendances, le client doit définir la propriété usesTaskDependencies du travail sur true.

Dans le modèle de fractionnement du travail, il est rare que les clients souhaitent ajouter à des travaux davantage de tâches que celles que crée l’outil de fractionnement du travail. Le client doit donc définir normalement la propriété *onAllTasksComplete* du travail sur **terminatejob**.

## Modèle du processeur de tâches

Le modèle du processeur de tâches vous permet d’implémenter une tâche du processeur de tâches pouvant effectuer les actions suivantes :

* Définir les informations requises par chaque tâche Batch à exécuter.
* Exécuter toutes les actions requises par chaque tâche Batch.
* Enregistrer les sorties de tâche dans un emplacement de stockage permanent.

Bien qu’un processeur de tâches ne soit pas nécessaire pour exécuter des tâches dans Batch, le principal avantage à utiliser un processeur de tâches est qu’il fournit un wrapper pour implémenter toutes les actions d’exécution de tâches à un seul emplacement. Par exemple, si vous devez exécuter plusieurs applications dans le contexte de chaque tâche, ou si vous devez copier des données vers un emplacement de stockage permanent après la fin de chaque tâche.

Les actions effectuées par le processeur de tâches peuvent être simples ou complexes, nombreuses ou peu nombreuses, en fonction des besoins de votre charge de travail. En outre, en mettant en œuvre toutes les actions de tâches dans un processeur de tâches, vous pouvez facilement mettre à jour ou ajouter des actions en fonction des modifications apportées aux besoins des applications ou des charges de travail. Toutefois, dans certains cas, le processeur de tâches n’est pas forcément la solution optimale pour votre implémentation puisqu’il peut engendrer une complexité inutile, par exemple si vous exécutez des travaux pouvant être démarrés rapidement à l’aide d’une simple ligne de commande.

### Créer un processeur de tâches à l’aide du modèle

Pour ajouter un processeur de tâches à la solution que vous avez créée précédemment, procédez comme suit :

1. Ouvrez votre solution existante dans Visual Studio 2015.

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, cliquez sur **Ajouter**, puis sur **Nouveau projet**.

3. Sous **Visual C#**, cliquez sur **Cloud**, puis sur **Azure Batch Task Processor** (Processeur de tâches Azure Batch).

4. Entrez un nom décrivant votre application et identifiant ce projet en tant que le processeur de tâches (par exemple, « ProcesseurLitwareTask »).

5. Cliquez sur **OK** pour créer le projet.

6. Pour finir, générez le projet pour forcer Visual Studio à charger tous les packages NuGet référencés et vérifier que le projet est valide avant de commencer à le modifier.

### Les fichiers du modèle du processeur de tâches et leur objectif

Lorsque vous créez un projet à l’aide du modèle du processeur de tâches, il génère trois groupes de fichiers de code :

* Le fichier de programme principal (Program.cs). Il contient le point d’entrée du programme et la gestion des exceptions de niveau supérieur. Vous n’avez normalement pas besoin de modifier cette configuration.

* Le répertoire Framework. Il contient les fichiers responsables du travail « boilerplate » (réutilisable) effectué par le programme du gestionnaire de travaux – décompression de paramètres, ajout de tâches au travail Batch, etc. Vous n’avez normalement pas besoin de modifier ces fichiers.

* Le fichier du processeur de tâches (TaskProcessor.cs). C’est là que vous allez placer la logique spécifique à votre application pour l’exécution d’une tâche (généralement en appelant un exécutable existant). C’est aussi là que se trouve le code de pré et de post-traitement, tel que le téléchargement de données supplémentaires ou de fichiers de résultats.

Bien sûr, vous pouvez si besoin ajouter des fichiers supplémentaires pour prendre en charge le code de votre processeur de tâches, en fonction de la complexité de la logique de fractionnement du travail.

Le modèle génère également des fichiers de projet .NET standard comme des fichiers .csproj, app.config, packages.config, etc.

Le reste de cette section décrit les différents fichiers et leur structure de code, et explique ce que fait chaque classe.

![Explorateur de solutions Visual Studio affichant la solution du modèle du processeur de tâches][solution_explorer02]

**Fichiers Framework**

* `Configuration.cs` : Encapsule le chargement des données de configuration du travail, telles que les détails du compte Batch, les informations d’identification du compte de stockage lié, les informations relatives aux travaux et aux tâches, et les paramètres du travail. Il donne également accès aux variables d’environnement définies par Batch (voir les paramètres d’environnement des tâches, dans la documentation Batch) via la classe Configuration.EnvironmentVariable.

* `IConfiguration.cs` : Résume l’implémentation de la classe de configuration, afin que vous puissiez soumettre votre outil de fractionnement du travail à un test unitaire, à l’aide d’un objet de configuration fictif ou simulé.

* `TaskProcessorException.cs` : Représente une erreur nécessitant l’arrêt du gestionnaire de travaux. TaskProcessorException est utilisé pour encapsuler des erreurs « attendues » lorsque des informations de diagnostic spécifiques peuvent être fournies dans le cadre de l’arrêt.

**Processeur de tâches**

* `TaskProcessor.cs`: Exécute la tâche. L’infrastructure appelle la méthode TaskProcessor.Run. C’est la classe où vous injecterez la logique spécifique à l’application de votre tâche. Implémentez la méthode Run pour :
  * Analyser et valider les paramètres de n’importe quelle tâche
  * Composer la ligne de commande pour tout programme externe que vous souhaitez appeler
  * Enregistrer des informations de diagnostic qui peuvent vous être utiles pour le débogage
  * Démarrer un processus à l’aide de cette ligne de commande
  * Attendre que le processus s’arrête
  * Capturer le code de sortie du processus pour déterminer s’il a réussi ou échoué
  * Enregistrer tous les fichiers de sortie que vous souhaitez conserver dans un emplacement de stockage permanent

**Fichiers de projet de ligne de commande .NET standard**

* `App.config` : Fichier de configuration d’application .NET standard.
* `Packages.config`: Fichier de dépendance de package NuGet standard.
* `Program.cs` : Contient le point d’entrée du programme et la gestion des exceptions de niveau supérieur.

## Implémenter le processeur de tâches

Lorsque vous ouvrez le projet de modèle du processeur de tâches, il ouvre le fichier TaskProcessor.cs par défaut. Vous pouvez implémenter la logique d’exécution pour les tâches de votre charge de travail à l’aide de la méthode Run() présentée ci-dessous :

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
>[AZURE.NOTE] La section annotée dans la méthode Run() est la seule section de code du modèle du processeur de tâches que vous êtes censé modifier en ajoutant la logique d’exécution des tâches de votre charge de travail. Si vous souhaitez modifier une autre section du modèle, familiarisez-vous d’abord avec le fonctionnement de Batch en consultant la documentation Batch et en essayant quelques-uns des exemples de code Batch.

La méthode Run() est chargée de lancer de la ligne de commande, de démarrer un ou plusieurs processus, d’attendre que tous les processus se terminent, d’enregistrer les résultats, et de retourner un code de sortie. C’est dans la méthode Run() que vous implémentez la logique de traitement de vos tâches. C’est l’infrastructure du processeur de tâches qui appelle la méthode Run() ; il est inutile de l’appeler vous-même.

Votre implémentation Run() a accès :

* Aux paramètres de la tâche, via le champ `_parameters`.
* Aux ID du travail et de la tâche, via les champs `_jobId` et `_taskId`.
* À la configuration de la tâche, via le champ `_configuration`.

**Échec de la tâche**

En cas d’échec, vous pouvez quitter la méthode Run() en levant une exception, mais le contrôle du code de sortie de la tâche est alors confié au gestionnaire d’exceptions de niveau supérieur. Si vous avez besoin de contrôler le code de sortie afin de pouvoir distinguer les différents types d’échec, par exemple à des fins de diagnostic ou parce que certains modes d’échec doivent arrêter le travail et d’autres non, vous devez quitter la méthode Run() en retournant un code de sortie différent de zéro. Ce code devient le code de sortie de la tâche.

### Codes de sortie et exceptions dans le modèle du processeur de tâches

Les codes de sortie et les exceptions fournissent un mécanisme permettant de déterminer le résultat de l’exécution d’un programme, et peuvent aider à identifier les problèmes rencontrés lors de l’exécution du programme. Le modèle du processeur de tâches implémente les codes de sortie et les exceptions décrites dans cette section.

Une tâche du processeur de tâches implémentée avec le modèle du processeur de tâches peut retourner trois codes de sortie possibles :

| Code | Description |
|------|-------------|
| [Process.ExitCode][process_exitcode] | Le processeur de tâches s’est terminé. Notez que cela ne signifie pas que le programme que vous avez appelé a réussi, uniquement que le processeur de tâches l’a appelé avec succès et a exécuté le post-traitement sans exceptions. La signification du code de sortie dépend du programme appelé – le code de sortie 0 signifie généralement que le programme a réussi et tous les autres codes de sortie signifient qu’il a échoué. |
| 1 | La tâche du processeur de tâches a échoué avec une exception rencontrée dans une section « attendue » du programme. L’exception a été convertie en une `TaskProcessorException` avec des informations de diagnostic et, si possible, des suggestions pour résoudre l’échec. |
| 2 | Le processeur de tâches a échoué avec une exception « inattendue ». L’exception a été enregistrée dans la sortie standard, mais le processeur de tâches n’a pas pu ajouter d’informations de diagnostic ou de correction supplémentaires. |

>[AZURE.NOTE] Si le programme que vous appelez utilise des codes de sortie 1 et 2 pour désigner des modes d’échec spécifiques, il serait ambigu d’utiliser ces mêmes codes de sortie pour les erreurs du processeur de tâches. Vous pouvez remplacer ces codes d’erreur du processeur de tâches par d’autres codes de sortie en modifiant les cas d’exceptions dans le fichier Program.cs.

Toutes les informations retournées par des exceptions sont écrites dans des fichiers stdout.txt et stderr.txt. Pour plus d’informations, consultez la rubrique Gestion des erreurs de la documentation Batch.

### Considérations du client

**Informations d’identification de stockage**

Si votre processeur de tâches utilise le stockage blob Azure pour conserver les sorties, par exemple à l’aide de la bibliothèque d’assistance de conventions de fichier, il a besoin d’accéder *soit* aux informations d’identification du compte de stockage cloud *ou* à une URL de conteneur d’objets blob incluant une signature d’accès partagé (SAP). Le modèle inclut une prise en charge pour fournir des informations d’identification via des variables d’environnement commun. Votre client peut transmettre les informations d’identification de stockage comme suit :

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Le compte de stockage est ensuite disponible dans la classe TaskProcessor via la propriété `_configuration.StorageAccount`.

Si vous préférez utiliser une URL de conteneur avec SAP, vous pouvez également la transmettre via un paramètre d’environnement commun de travail, mais le modèle de processeur de tâches n’inclut pas la prise en charge intégrée de cette solution pour l’instant.

**Paramétrage du stockage**

Il est recommandé que le client ou la tâche du gestionnaire de travaux crée tous les conteneurs requis par les tâches avant de les ajouter au travail. Cette étape est obligatoire si vous utilisez une URL de conteneur avec SAP, puisque ce type d’URL n’inclut pas l’autorisation de créer le conteneur. Elle est recommandée même si vous transmettez des informations d’identification de compte de stockage, car elle enregistre toutes les tâches devant appeler CloudBlobContainer.CreateIfNotExistsAsync sur le conteneur.

## Transmettre des paramètres et des variables d’environnement

### Transmettre des paramètres d’environnement

Un client peut transmettre des informations à la tâche du gestionnaire de travaux sous la forme de paramètres d’environnement. Ces informations peuvent ensuite être utilisées par la tâche du gestionnaire de travaux lors de la génération des tâches du processeur de tâches qui seront exécutées dans le cadre de travail de calcul. Les informations que vous pouvez transmettre en tant que paramètres d’environnement sont les suivantes :

* Clés d’accès et nom du compte de stockage
* URL du compte Batch
* Clé du compte Batch

Le service Batch dispose d’un mécanisme simple pour transmettre des paramètres d’environnement à un gestionnaire de travaux, à l’aide de la propriété `EnvironmentSettings` [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Par exemple, pour obtenir l’instance `BatchClient` pour un compte Batch, vous pouvez transmettre les variables d’environnement à partir de l’URL du code client ainsi que les informations d’identification de la clé partagée du compte Batch. De même, pour accéder au compte de stockage lié au compte Batch, vous pouvez transmettre le nom et la clé du compte de stockage en tant que variables d’environnement.

### Transmettre des paramètres au modèle du gestionnaire de travaux

Dans de nombreux cas, il est utile de transmettre à la tâche du gestionnaire de travaux les paramètres de chaque travail, soit pour contrôler le processus de fractionnement du travail ou pour configurer les tâches du travail. Vous pouvez le faire en téléchargeant un fichier JSON nommé parameters.json en tant fichier de ressources pour la tâche du gestionnaire de travaux. Les paramètres sont ensuite accessibles dans le champ `JobSplitter._parameters` du modèle du gestionnaire de travaux.

>[AZURE.NOTE] Le gestionnaire de paramètres intégré prend en charge uniquement les dictionnaires string-to-string (de chaîne en chaîne). Si vous souhaitez transmettre des valeurs JSON complexes en tant que valeurs de paramètre, vous devez les transmettre sous forme de chaînes et les analyser dans l’outil de fractionnement du travail ou modifier la méthode `Configuration.GetJobParameters` de l’infrastructure.

### Transmettre des paramètres au modèle du processeur de tâches

Vous pouvez également transmettre des paramètres à des tâches individuelles implémentées à l’aide du modèle du processeur de tâches. Tout comme le modèle du gestionnaire de travaux, celui du processeur de tâches recherche un fichier de ressources nommé

parameters.json et, s’il le trouve, le charge en tant que dictionnaire de paramètres. Il existe deux options pour transmettre des paramètres aux tâches du processeur de tâches :

* Réutiliser le fichier JSON des paramètres du travail. Cette option est efficace si les paramètres concernent l’ensemble du travail (par exemple, un rendu hauteur et largeur). Pour ce faire, lorsque vous créez un objet CloudTask dans l’outil de fractionnement du travail, ajoutez une référence à l’objet de fichier de ressources parameters.json depuis la collection ResourceFiles de la tâche du gestionnaire de travaux (`JobSplitter._jobManagerTask.ResourceFiles`) vers la collection ResourceFiles de l’objet CloudTask.

* Générer et télécharger un document parameters.json spécifique à une tâche dans le cadre de l’exécution de l’outil de fractionnement du travail, et référencer cet objet blob dans la collection de fichiers de ressources de la tâche. Cela est nécessaire si des tâches différentes ont des paramètres différents. Par exemple, un scénario de rendu 3D dans lequel l’index de l’image est transmis à la tâche en tant que paramètre.

>[AZURE.NOTE] Le gestionnaire de paramètres intégré prend en charge uniquement les dictionnaires string-to-string (de chaîne en chaîne). Si vous souhaitez transmettre des valeurs JSON complexes en tant que valeurs de paramètre, vous devez les transmettre sous forme de chaînes et les analyser dans le processeur de tâches ou modifier la méthode `Configuration.GetTaskParameters` de l’infrastructure.

## Étapes suivantes

### Conserver la sortie d’un travail et d’une tâche dans le stockage Azure

Les [conventions de fichiers Azure Batch][nuget_package] sont également utiles au développement de solutions Batch. Utilisez cette bibliothèque de classes .NET (actuellement en version préliminaire) dans vos applications .NET Batch pour stocker et récupérer facilement les sorties des tâches vers et depuis le stockage Azure. L’article [Persist Azure Batch job and task output](batch-task-output.md) (Conserver une sortie de tâche et de travail Azure Batch) fait une description complète de la bibliothèque et de son utilisation.

### Forum Azure Batch

Le [Forum Azure Batch][forum] sur MSDN est l’endroit idéal pour discuter de Batch et poser des questions sur le service. Consultez le forum pour obtenir des publications « permanentes » utiles et publiez les questions que vous vous posez pendant la création de vos solutions Batch.

[forum]: https://social.msdn.microsoft.com/forums/azure/fr-FR/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png

<!---HONumber=AcomDC_0921_2016-->