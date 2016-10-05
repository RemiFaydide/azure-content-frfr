<properties
	pageTitle="Étape 3 : Création d’une expérience Machine Learning | Microsoft Azure"
	description="Étape 3 du guide pas à pas du développement d'une solution prédictive : Création d'une expérience d'apprentissage dans Azure Machine Learning Studio."
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
	ms.author="garye"/>


# Étape 3 de la procédure pas à pas : création d’une expérience Azure Machine Learning

Voici la troisième étape de la procédure pas à pas [Développement d’une solution d’analyse prédictive avec Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Créer un espace de travail Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.	**Créer une expérience**
4.	[Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

----------

L’étape suivante de cette procédure pas à pas consiste à créer une expérience dans Machine Learning Studio qui utilise le jeu de données que nous avons chargé.

1.	Dans Studio, cliquez sur **+NOUVEAU** en bas de la fenêtre.
2.	Sélectionnez **EXPÉRIENCE**, puis sélectionnez « Expérience vide ». Sélectionnez le nom d’expérience par défaut, situé en haut de la zone de dessin, et remplacez-le par un nom significatif.

	> [AZURE.TIP] Il est conseillé de compléter le **Résumé** et la **Description** relatifs à l'expérience dans le panneau **Propriétés**. Ces propriétés vous donnent la possibilité de documenter l'expérience afin que toute personne la consultant ultérieurement comprenne vos objectifs et votre méthodologie.

3.	Dans la palette des modules à gauche du canevas d'expérience, développez **Jeux de données enregistrés**.
4.	Recherchez le jeu de données que vous avez créé sous **My Datasets** (Mes jeux de données) et faites-le glisser sur la zone de dessin. Vous pouvez également le rechercher en entrant son nom dans la zone **Rechercher** au-dessus de la palette.

## Préparation des données

Vous pouvez voir les 100 premières lignes de données et quelques informations statistiques concernant tout le jeu de données en cliquant sur le port de sortie du jeu de données (le petit cercle en bas) et en sélectionnant **Visualiser**.

Le fichier de données étant dépourvu d’en-têtes de colonne, Studio a fourni des en-têtes génériques (Col1, Col2, *etc.*). Des en-têtes explicites ne sont pas essentiels pour créer un modèle, mais ils facilitent l’utilisation des données dans l’expérience. En outre, lors de la publication de ce modèle dans un service web, les en-têtes permettent à l'utilisateur du service d'identifier les colonnes.

Nous pouvons ajouter des en-têtes de colonne en utilisant le module [Modifier les métadonnées][edit-metadata]. Vous utilisez le module [Modifier les métadonnées][edit-metadata] pour modifier des métadonnées associées à un jeu de données. Dans ce cas, il peut fournir plus de noms conviviaux pour les en-têtes de colonne.

Pour utiliser [Modifier les métadonnées][edit-metadata], vous spécifiez les colonnes à modifier (dans ce cas, toutes les colonnes), puis l’action à effectuer sur les colonnes (en l’occurrence, modifier les en-têtes de colonne).

1.	Dans la palette des modules, tapez « métadonnées » dans la zone **Rechercher**. [Modifier les métadonnées][edit-metadata] apparaît dans la liste des modules.
2.	Cliquez sur le module [Modifier les métadonnées][edit-metadata] et faites-le glisser sur le canevas avant de le déposer sous le jeu de données que nous avons ajouté précédemment.
3.	Connectez le jeu de données au module [Modifier les métadonnées][edit-metadata] \: cliquez sur le port de sortie du jeu de données (le petit cercle en bas du jeu de données), faites glisser vers le port d’entrée de [Modifier les métadonnées][edit-metadata] \(le petit cercle en haut du module), puis relâchez le bouton de la souris. Le jeu de données et le module restent connectés même si vous opérez des déplacements sur le canevas.

    L'expérience doit ressembler à ceci :

    ![Ajout de Modifier les métadonnées][2]
    
    Le point d’exclamation rouge indique que nous n’avons pas encore défini les propriétés de ce module. Ce sera notre prochaine tâche.
    
    > [AZURE.TIP] Vous pouvez ajouter un commentaire dans un module en double-cliquant sur ce module, puis en saisissant du texte. Ceci peut vous aider à voir d'un seul coup d'œil ce que fait chaque module dans votre expérience. Dans ce cas, double-cliquez sur le module [Modifier les métadonnées][edit-metadata] et tapez le commentaire « Ajouter des en-têtes de colonne ». Cliquez n’importe où sur le canevas pour fermer la zone de texte. Cliquez sur la flèche vers le bas sur le module pour afficher le commentaire.

4.	Sélectionnez [Modifier les métadonnées][edit-metadata] puis, dans le panneau **Propriétés** à droite du canevas, cliquez sur **Lancer le sélecteur de colonne**.
5.	Dans la boîte de dialogue **Sélectionner les colonnes**, sélectionnez toutes les lignes des **colonnes disponibles** puis cliquez sur > pour les déplacer vers les **colonnes sélectionnées**. La boîte de dialogue doit ressembler à ceci : ![Sélecteur de colonnes avec toutes les colonnes sélectionnées][4]
7.	Cliquez sur la coche **OK**.
8.	Dans le panneau **Propriétés**, recherchez le paramètre **Nouveaux noms de colonne**. Dans ce champ, entrez une liste de noms pour les 21 colonnes du jeu de données, séparés par des virgules et dans l’ordre de la colonne. Vous pouvez obtenir le nom des colonnes dans la documentation du jeu de données sur le site web UCI ou, par commodité, vous pouvez copier et coller la liste suivante :

		  Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    Le volet Propriétés ressemble à ceci :

    ![Propriétés de Modifier les métadonnées][1]

> [AZURE.TIP] Si vous souhaitez vérifier les en-têtes de colonne, exécutez l’expérience (cliquez sur **EXÉCUTER** sous le canevas d’expérience). À la fin de l’exécution (une coche verte s’affiche sur [Modifier les métadonnées][edit-metadata]), cliquez sur le port de sortie du module [Modifier les métadonnées][edit-metadata] et sélectionnez **Visualiser**. Vous pouvez voir la sortie de tous les modules en procédant de même pour afficher la progression des données dans l'expérience.

## Création de jeux de données d'apprentissage et de test

L’étape suivante de l’expérience consiste à générer des jeux de données séparés dont nous nous servirons pour l’apprentissage et le test de notre modèle.

Pour ce faire, nous utilisons le module [Fractionner les données][split].

1.	Recherchez le module [Fractionner les données][split], faites-le glisser sur le canevas et connectez-le au dernier module [Modifier les métadonnées][edit-metadata].
2.	Par défaut, le rapport de division est 0,5 et le paramètre **Fractionnement aléatoire** est défini. Cela signifie qu’une moitié aléatoire des données est sortie par un port du module [Fractionner les données][split], et l’autre moitié par l’autre port. Vous pouvez ajuster ces paramètres, de même que le paramètre **Valeur de départ aléatoire**, pour changer la répartition entre les données d’apprentissage et de test. Pour cet exemple, nous ne changeons rien.
	> [AZURE.TIP] La propriété **Fraction de lignes dans le premier jeu de données de sortie** détermine la quantité de données qui est sortie par le port de sortie gauche. Par exemple, si vous définissez le rapport sur 0,7, 70 % des données sont sorties par le port gauche et 30 % par le port droit.
3. Double-cliquez sur le module [Fractionner les données][split] et entrez le commentaire « Fractionnement des données de formation/test de 50 % ».

Nous pouvons utiliser les sorties du module [Fractionner les données][split] à notre gré, mais choisissons la sortie gauche pour les données d’apprentissage et la sortie droite pour les données de test.

Comme indiqué sur le site web UCI, le coût d'une erreur consistant à classer un crédit à risque élevé comme étant à faible risque est 5 fois plus élevé que celui de l'erreur consistant à classer un crédit à faible risque comme étant à risque élevé. Pour tenir compte de cela, nous générons un nouveau jeu de données qui reflète cette fonction de coût. Dans le nouveau jeu de données, chaque exemple à haut risque est répliqué 5 fois, alors que les exemples à faible risque ne sont pas répliqués.

Nous pouvons procéder à la réplication en utilisant le code R :

1.	Recherchez et faites glisser le module [Exécuter un script R][execute-r-script] vers le canevas de l’expérience et connectez le port de sortie gauche du module [Fractionner les données][split] au premier port d’entrée (« Dataset1 ») du module [Exécuter un script R][execute-r-script].
2. Double-cliquez sur le module [Exécuter un script R][execute-r-script] et entrez le commentaire « Définir les ajustement des coûts ».
2.	Dans le panneau **Propriétés**, supprimez le texte par défaut du paramètre **Script R** et entrez le script suivant :

		  dataset1 <- maml.mapInputPort(1)
		  data.set<-dataset1[dataset1[,21]==1,]
		  pos<-dataset1[dataset1[,21]==2,]
		  for (i in 1:5) data.set<-rbind(data.set,pos)
		  maml.mapOutputPort("data.set")


Nous devons répéter cette opération de réplication pour chaque sortie du module [Fractionner les données][split] pour que les données d’apprentissage et de test aient le même ajustement des coûts.

1.	Cliquez avec le bouton droit sur le module [Exécuter un script R][execute-r-script] et sélectionnez **Copier**.
2.	Cliquez avec le bouton droit sur le canevas d'expérience et sélectionnez **Coller**.
3.	Connectez le premier port d’entrée de ce module [Exécuter un script R][execute-r-script] au port de sortie droit du module [Fractionner les données][split].

> [AZURE.TIP] La copie du Module d’exécution de script R contient le même script que le module d’origine. Lorsque vous copiez-collez un module sur le canevas, la copie conserve toutes les propriétés de l'original.

À ce stade, notre expérience ressemble à cela :

![Adding Split module and R scripts][3]

Pour plus d’informations sur l'utilisation de scripts R dans vos expériences, consultez la page [Prolonger votre expérience avec R](machine-learning-extend-your-experiment-with-r.md).

**Suite : [Formation et évaluation des modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0921_2016-->