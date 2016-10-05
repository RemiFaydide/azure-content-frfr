<properties
   pageTitle="Dépannage d’erreurs client Docker sur Windows avec Visual Studio | Microsoft Azure"
   description="Résolvez les problèmes d’utilisation de Visual Studio pour créer et déployer des applications web dans Docker sur Windows avec Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# Résolution des problèmes de développement avec Docker pour Visual Studio

Lorsque vous travaillez avec Visual Studio Tools pour Docker Preview, vous pourriez rencontrer des problèmes en raison de la nature de la version préliminaire. Voici quelques problèmes courants et leurs solutions.


## Impossible de valider le mappage de volume
Le mappage de volume est requis pour partager le code source et les fichiers binaires de votre application avec le dossier de l’application dans le conteneur. Les mappages de volume spécifique sont contenus dans les fichiers docker-compose.dev.debug.yml et docker-compose.dev.release.yml. Au fur et à mesure que les fichiers sont modifiés sur votre ordinateur hôte, les conteneurs reflètent ces modifications dans une structure de dossiers similaire.

Pour activer le mappage de volume, ouvrez **Paramètres...** à partir de l’icône moby de la barre d’état système Docker For Windows, puis sélectionnez l’onglet **Disques partagés**. Assurez-vous que la lettre du lecteur qui héberge votre projet, ainsi que la lettre du lecteur où se trouve %USERPROFILE% sont identiques, puis cliquez sur **Appliquer**.

Pour vérifier si le mappage de volume fonctionne, une fois les lecteurs partagés, effectuez la reconstruction et cliquez sur F5 dans Visual Studio ou essayez la procédure suivante à partir d’une invite de commandes :

*Dans une invite de commandes Windows*

*[Remarque : cela suppose que le dossier Utilisateurs se trouve sur le lecteur « C» et qu’il a été partagé. Mettez à jour si nécessaire si vous avez partagé un autre lecteur]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Dans le conteneur Linux*

```
/ # ls
```

Vous devriez voir une liste de répertoires dans le dossier Users/Public. Si aucun fichier ne s’affiche et que votre dossier /c/Users/Public n’est pas vide, le mappage de volumes n’est pas configuré correctement.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Passez au répertoire wormhole pour voir le contenu du répertoire `/c/Users/Public` :

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**Remarque :** *lorsque vous travaillez avec des machines virtuelles Linux, le système de fichiers du conteneur respecte la casse.*

##Build : échec inattendu de la tâche PrepareForBuild.

Microsoft.DotNet.Docker.CommandLine.ClientException : une erreur s’est produite lors de la tentative de connexion :

Vérifiez que l’hôte Docker par défaut est en cours d’exécution. Ouvrez une invite de commandes et exécutez :

```
docker info
```

Si elle renvoie une erreur, essayez de démarrer l’application de bureau **Docker pour Windows**. Si l’application de bureau est en cours d’exécution, l’icône **moby**de la barre d’état système doit être visible. Cliquez avec le bouton droit sur l’icône de barre d’état et ouvrez **Paramètres**. Cliquez sur l’onglet **Réinitialiser**, puis sur **Redémarrer Docker**.

##Mise à niveau manuelle de la version 0.31 à 0.40


1. Sauvegarde du projet
1. Supprimez les fichiers suivants dans le projet :

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Fermez la solution et supprimez les lignes suivantes du fichier .xproj :

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Ouvrez de nouveau la solution
1. Supprimez les lignes suivantes du fichier Properties\\launchSettings.json file :

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Supprimez les fichiers suivants associés à Docker du project.json dans publishOptions :

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Désinstallez la version précédente et installez Docker Tools 0.40, puis **Ajouter -> Prise en charge Docker** à partir du menu contextuel pour votre application web ASP.Net Core ou console. Cela ajoutera les nouveaux artefacts Docker requis à votre projet.

## Une boîte de dialogue d’erreur apparaît lorsque vous effectuez l’opération **Ajouter -> Prise en charge Docker** ou Débogage (F5) sur une application ASP.NET Core dans un conteneur

Après la désinstallation et l’installation des extensions, le cache MEF (Managed Extensibility Framework) de Visual Studio peut être endommagé. Dans ce cas, cela peut générer des boîtes de dialogue d’erreur lors de l’ajout de la prise en charge Docker et/ou de la tentative d’exécution ou du Débogage (F5) sur votre application ASP.NET Core. Pour contourner provisoirement le problème, exécutez les étapes suivantes pour supprimer et régénérer le cache MEF.

1. Fermez toutes les instances de Visual Studio
1. Ouvrez %USERPROFILE%\\AppData\\Local\\Microsoft\\VisualStudio\\14.0\\
1. Supprimez les dossiers suivants
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Ouvrez Visual Studio.
1. Réessayez le scénario

<!---HONumber=AcomDC_0921_2016-->