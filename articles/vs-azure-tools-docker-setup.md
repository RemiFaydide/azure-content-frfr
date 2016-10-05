<properties
   pageTitle="Configurer un hôte Docker avec VirtualBox | Microsoft Azure"
   description="Instructions pas à pas pour configurer une instance Docker par défaut à l'aide de Docker Machine et de VirtualBox."
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
   ms.author="mlearned" />

# Configurer un hôte Docker avec VirtualBox

## Vue d'ensemble
Cet article vous guide tout au long de la configuration d'une instance Docker par défaut à l'aide de Docker Machine et de VirtualBox. Si vous utilisez la [version bêta de Docker pour Windows](http://beta.docker.com/), cette configuration n'est pas nécessaire.

## Composants requis
Les outils suivants doivent être installés.

- [Boîte à outils Docker](https://www.docker.com/products/overview#/docker_toolbox)

## Configuration du client Docker avec Windows PowerShell

Pour configurer un client Docker, ouvrez Windows PowerShell et procédez comme suit :

1. Créez une instance hôte docker par défaut.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Vérifiez que l'instance par défaut est configurée et en cours d'exécution. Vous devriez voir une instance nommée « par défaut » en cours d’exécution.

    ```PowerShell
	docker-machine ls 
    ```
		
	![Sortie docker-machine ls][0]
 
1. Choisissez par défaut l'hôte actuel et configurez votre interpréteur de commandes.

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. Affichez les conteneurs Docker actifs. La liste doit être vide.

    ```PowerShell
	docker ps
    ```

	![Sortie docker ps][1]
 
> [AZURE.NOTE] Chaque fois que vous redémarrez votre machine de développement, vous devrez également redémarrer votre hôte Docker local. Pour ce faire, exécutez la commande suivante à l’invite de commandes : `docker-machine start default`

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png

<!---HONumber=AcomDC_0921_2016-->