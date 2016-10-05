<properties
   pageTitle="Mise à niveau d’applications Service Fabric à l’aide de PowerShell | Microsoft Azure"
   description="Cet article vous présente l'expérience de déploiement d'une application Service Fabric, la modification du code et le déploiement d'une mise à niveau à l’aide de PowerShell."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>




# Mise à niveau d’applications Service Fabric à l’aide de PowerShell

L'approche de la mise à niveau la plus fréquemment utilisée et recommandée est la mise à niveau propagée surveillée. Azure Service Fabric surveille l’intégrité de l’application en cours de mise à niveau sur la base d’un ensemble de stratégies de contrôle d’intégrité. Une fois qu’un domaine de mise à jour a été mis à niveau, Service Fabric évalue l’intégrité de l’application et passe au domaine de mise à jour suivant ou fait échouer la mise à niveau, en fonction des stratégies de contrôle d’intégrité.

Une mise à niveau surveillée des applications peut être effectuée à l'aide des API managées ou natives, PowerShell ou REST. Pour obtenir des instructions sur l’exécution d’une mise à niveau à l’aide de Visual Studio, consultez [Mise à niveau de votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md).

Grâce à la mise à niveau propagée surveillée de Service Fabric, l’administrateur d’une application peut configurer la stratégie d’évaluation d’intégrité que Service Fabric utilise pour déterminer si l’application est saine. En outre, l’administrateur peut configurer l’action à entreprendre en cas d’échec de l’évaluation d’intégrité (par exemple, effectuer une restauration automatique). Cette section présente une procédure pas à pas de mise à niveau surveillée pour l’un des exemples du Kit de développement logiciel (SDK) à l’aide de PowerShell.

## Étape 1 : Créer et déployer l'exemple Visual Objects


Générez et publiez l’application en cliquant avec le bouton droit sur le projet d’application, **VisualObjectsApplication** et en sélectionnant la commande **Publier**. Pour plus d’informations, consultez le [didacticiel sur la mise à niveau d’une application Service Fabric](service-fabric-application-upgrade-tutorial.md). Vous pouvez également utiliser PowerShell pour déployer votre application.

> [AZURE.NOTE] Avant d’utiliser les commandes Service Fabric dans PowerShell, vous devez vous connecter au cluster en utilisant l’applet de commande `Connect-ServiceFabricCluster`. De même, cela suppose que le cluster a déjà été configuré sur votre ordinateur local. Consultez l'article sur la [configuration de votre environnement de développement Service Fabric](service-fabric-get-started.md).

Après avoir généré le projet dans Visual Studio, vous pouvez utiliser la commande PowerShell **Copy-ServiceFabricApplicationPackage** pour copier le package d’application dans le magasin d’images. L’étape suivante consiste à inscrire l’application au runtime Service Fabric à l’aide de l’applet de commande **Register-ServiceFabricApplicationPackage**. La dernière étape consiste à démarrer une instance de l’application à l’aide de l’applet de commande **New-ServiceFabricApplication**. Ces trois étapes sont analogues à l’utilisation de l’élément de menu **Déployer** dans Visual Studio.

À présent, vous pouvez utiliser l'[Explorateur de Service Fabric pour afficher le cluster et l'application](service-fabric-visualizing-your-cluster.md). L'application possède un service web accessible dans Internet Explorer en tapant [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) dans la barre d'adresse. Vous devez normalement voir des objets visuels flottants en rotation à l'écran. En outre, vous pouvez utiliser **Get-ServiceFabricApplication** pour vérifier l’état de l’application.

## Étape 2 : Mettre à jour l'exemple Visual Objects

Vous pouvez remarquer qu'avec la version qui a été déployée à l'étape 1, les objets visuels ne sont pas en rotation. Mettons à niveau cette application vers une application dans laquelle les objets visuels sont en rotation.

Sélectionnez le projet VisualObjects.ActorService dans la solution VisualObjects et ouvrez le fichier StatefulVisualObjectActor.cs. Dans ce fichier, accédez à la méthode `MoveObject`, placez en commentaire `this.State.Move()` et effacez le commentaire de `this.State.Move(true)`. Cette modification fait pivoter les objets lorsque le service a été mis à niveau.

Vous devez également mettre à jour le fichier *ServiceManifest.xml* (sous PackageRoot) du projet **VisualObjects.ActorService**. Mettez à jour *CodePackage*, la version du service vers 2.0 et les lignes correspondantes dans le fichier *ServiceManifest.xml* Vous pouvez utiliser l’option *Edit Manifest Files* (Modifier les fichiers du manifeste) de Visual Studio après avoir cliqué avec le bouton droit sur la solution pour modifier le fichier du manifeste.


Une fois les modifications effectuées, le manifeste doit se présenter comme suit (les parties en surbrillance présentent les modifications) :

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

À présent, le fichier *ApplicationManifest.xml* (situé sous le projet **VisualObjects**, sous la solution **VisualObjects**) est mis à niveau sur la version 2.0 du projet **VisualObjects.ActorService**. En outre, la version de l’application est mise à jour de 1.0.0.0 vers 2.0.0.0. Le fichier *ApplicationManifest.xml* doit ressembler à l’extrait de code suivant :

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


À présent, générez le projet en sélectionnant uniquement le projet **ActorService**, puis en effectuant un clic droit et en sélectionnant l’option **Générer** dans Visual Studio. Si vous sélectionnez **Régénérer tout**, vous devez mettre à jour les versions pour tous les projets, étant donné que le code aura été modifié. Nous allons maintenant empaqueter l’application mise à jour en cliquant avec le bouton droit sur ***VisualObjectsApplication***, en sélectionnant le menu Service Fabric et en choisissant **Package**. Cette action crée un package d’application qui peut être déployé. Votre application mise à jour est prête à être déployée.


## Étape 3 : Décider des stratégies de contrôle d'intégrité et des paramètres de mise à niveau

Familiarisez-vous avec les [paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md) et le [processus de mise à niveau](service-fabric-application-upgrade.md) pour bien comprendre les différents paramètres de mise à niveau, délais d’attente et critères d’intégrité appliqués. Pour cette procédure pas à pas, le critère d’évaluation d’intégrité du service est défini sur la valeur par défaut (recommandé), ce qui signifie que tous les services et instances doivent être _sains_ après la mise à niveau.

Toutefois, nous allons augmenter le paramètre *HealthCheckStableDuration* pour spécifier 60 secondes (afin que les services soient sains pendant au moins 20 secondes avant que la mise à niveau passe au domaine de mise à jour suivant). Nous allons également définir *UpgradeDomainTimeout* sur 1200 secondes et *UpgradeTimeout* sur 3000 secondes.

Enfin, nous allons également définir *UpgradeFailureAction* sur la restauration. Cette option nécessite que Service Fabric restaure l’application sur la version précédente en cas de problèmes lors de la mise à niveau. Par conséquent, lors du démarrage de la mise à niveau (à l’étape 4), les paramètres suivants sont spécifiés :

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## Étape 4 : Préparer l'application pour la mise à niveau

À présent, l’application est créée et prête à être mise à niveau. Si vous ouvrez une fenêtre PowerShell en tant qu’administrateur et tapez **Get-ServiceFabricApplication**, la commande doit vous informer que le type d’application 1.0.0.0 de **VisualObjects** est en cours de déploiement.

Le package d'application est stocké sous le chemin d'accès relatif suivant où vous avez décompressé le Kit de développement logiciel (SDK) Fabric Service : *Samples\\Services\\Stateful\\VisualObjects\\VisualObjects\\obj\\x64\\Debug*. Un dossier « Package » doit figurer dans ce répertoire ; il contient le package d’application. Vérifiez les horodatages pour vous assurer qu’il s’agit de la dernière version (vous devrez peut-être également modifier les chemins d’accès en conséquence).

À présent, nous allons copier le package d'application mis à jour dans le magasin d'images de Service Fabric (où les packages d'application sont stockés par Service Fabric). Le paramètre *ApplicationPackagePathInImageStore* indique à Service Fabric où il peut trouver le package d'application. Nous avons placé l’application mise à jour dans « VisualObjects\_V2 » à l’aide de la commande suivante (vous devrez peut-être modifier de nouveau les chemins d’accès en conséquence).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

L'étape suivante consiste à inscrire l'application auprès de Service Fabric, ce qui peut être effectué à l'aide de la commande suivante :

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Si la commande ci-dessus ne réussit pas, vous avez probablement besoin d’une régénération de tous les services. Comme cela est mentionné à l'étape 2, vous devrez peut-être mettre à jour également votre version de WebService.

## Étape 5 : Démarrer la mise à niveau de l'application

À présent, nous sommes prêts à démarrer la mise à niveau de l’application en utilisant la commande suivante :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Le nom de l’application est tel qu’il a été décrit dans le fichier *ApplicationManifest.xml*. Service Fabric utilise ce nom pour identifier l'application à mettre à niveau. Si vous définissez des délais d’attente trop courts, vous pouvez recevoir un message d’échec stipulant le problème. Reportez-vous à la section de résolution des problèmes ou augmentez les délais d’attente.

Vous pouvez désormais surveiller la progression de la mise à niveau de l’application à l’aide de Service Fabric Explorer ou à l’aide de la commande PowerShell suivante : **Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects**.

Après quelques minutes, l’état issu de la commande PowerShell ci-dessus doit indiquer que tous les domaines de mise à jour ont été mis à niveau (terminés). De plus, vous pouvez normalement noter que les objets visuels sont désormais en rotation dans la fenêtre de votre navigateur.

Vous pouvez essayer de mettre à niveau de la version 2 à la version 3, ou de la version 2 à la version 1 en guise d’exercice. Le passage de la version 2 à la version 1 est également considéré comme une mise à niveau. Amusez-vous avec les délais d'attente et les stratégies de contrôle d'intégrité pour vous familiariser avec ceux-ci. Lorsque vous déployez sur un cluster Azure, les paramètres doivent être définis correctement. Nous vous recommandons de définir les délais d’attente de manière plus restrictive.


## Étapes suivantes

La [mise à niveau de votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l’application à l’aide de Visual Studio.

Contrôlez les mises à niveau de votre application à l’aide des [paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Rendez les mises à niveau de votre application compatibles en apprenant à utiliser la [sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en consultant les [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résolvez les problèmes courants de mise à niveau de l’application en vous reportant aux étapes de [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md).

<!---HONumber=AcomDC_0921_2016-->