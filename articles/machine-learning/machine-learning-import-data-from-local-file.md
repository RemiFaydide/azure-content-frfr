<properties
	pageTitle="Importer des données dans Machine Learning Studio à partir d’un fichier local | Microsoft Azure"
	description="Comment importer vos données d’apprentissage Azure Machine Learning Studio à partir d’un fichier local"
	keywords="importer des données, format de données, types de données, sources de données, données d'apprentissage"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="garye;bradsev" />


# Importation de vos données d'apprentissage dans Azure Machine Learning Studio à partir d'un fichier local

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Pour utiliser vos propres données dans Machine Learning Studio, vous pouvez télécharger un fichier de données à l’avance depuis votre disque dur local pour créer un module de jeu de données dans votre espace de travail.


## Importation des données à partir d’un fichier local

Vous pouvez télécharger des données à partir d’un disque dur local en procédant comme suit :

1. Cliquez sur **+NOUVEAU** en bas de la fenêtre de Machine Learning Studio.
2. Sélectionnez **JEU DE DONNÉES** et **DEPUIS UN FICHIER LOCAL**.
3. Dans la boîte de dialogue **Télécharger un nouveau jeu de données**, recherchez le fichier que vous souhaitez télécharger
4. Saisissez un nom, identifiez le type de données puis saisissez éventuellement une description. Une description est recommandée : elle vous permet d'enregistrer des caractéristiques relatives aux données que vous souhaitez mémoriser pour une utilisation future.
5. La case à cocher **Il s'agit de la nouvelle version d'un jeu de donnée existant** vous permet de mettre à jour un jeu de données existant avec de nouvelles données. Cliquez simplement sur cette case à cocher, puis saisissez le nom d'un jeu de données existant.

Pendant le téléchargement, vous verrez un message indiquant que votre fichier est en cours de téléchargement. Le temps de téléchargement dépend de la taille de vos données et de la vitesse de votre connexion au service. Si vous savez que le fichier prendra du temps, vous pouvez faire autre chose dans Machine Learning Studio en attendant. Cependant, la fermeture du navigateur entraîne l’échec du téléchargement.

Une fois que vos données sont téléchargées, elles sont stockées dans un module de jeu de données et sont disponibles pour n'importe quelle expérience dans votre espace de travail. Lorsque vous éditez une expérience, les jeux de données que vous avez créés apparaissent sous **Mes jeux de données** dans la liste **Jeux de données enregistrés** de la palette des modules. Vous pouvez glisser-déplacer le jeu de données dans le canevas de l’expérience en vue d’affiner une analyse et l’apprentissage automatique.

<!---HONumber=AcomDC_0921_2016-->