<properties 
	pageTitle="Utiliser le Portail Azure pour déployer des ressources Azure | Microsoft Azure" 
	description="Utilisez le Portail Azure et Azure Resource Manager pour déployer vos ressources." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="tomfitz"/>

# Déployer des ressources à l’aide de modèles Resource Manager et du Portail Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Interface de ligne de commande Azure](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Cette rubrique montre comment utiliser le [Portail Azure](https://portal.azure.com) avec [Azure Resource Manager](resource-group-overview.md) pour déployer vos ressources Azure. Pour en savoir plus sur la gestion de vos ressources, consultez [Gérer des ressources Azure avec le portail](./azure-portal/resource-group-portal.md).

Actuellement, certains services ne prennent pas en charge le portail ou Resource Manager. Pour ces services, vous devez utiliser le [Portail Azure Classic](https://manage.windowsazure.com). Pour connaître l’état de chaque service, voir [Graphique de la disponibilité du portail Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## Créer un groupe de ressources

1. Pour créer un groupe de ressources vide, sélectionnez **Nouveau** > **Gestion** > **Groupe de ressources**.

    ![créer un groupe de ressources vide](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. Spécifiez un nom et un emplacement, puis, si nécessaire, sélectionnez un abonnement. Vous devez fournir un emplacement pour le groupe de ressources, car celui-ci stocke des métadonnées sur les ressources. Pour des raisons de conformité, vous souhaiterez peut-être indiquer où sont stockées métadonnées. En règle générale, nous vous recommandons de spécifier l’emplacement où réside la plupart de vos ressources. L’utilisation du même emplacement permet de simplifier votre modèle.

    ![définir les valeurs d’un groupe](./media/resource-group-template-deploy-portal/set-group-properties.png)

## Déployer des ressources à partir de Marketplace

Une fois votre groupe de ressources créé, vous pouvez y déployer des ressources à partir de Marketplace. Marketplace fournit des solutions prédéfinies pour les scénarios courants.

1. Pour commencer un déploiement, sélectionnez **Nouveau** et choisissez le type de ressource que vous souhaitez déployer. Ensuite, recherchez la version de la ressource que vous souhaitez déployer.

    ![déployer des ressources](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. Si vous ne voyez pas la solution que vous souhaitez déployer, vous pouvez lancer une recherche sur le Marketplace.

    ![effectuer une recherche sur le marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

3. Selon le type de ressource que vous avez sélectionné, vous devrez définir une collection de propriétés pertinentes avant le déploiement. Ces options ne sont pas présentées ici, car elles varient selon le type de ressource. Pour tous les types, vous devez sélectionner un groupe de ressources de destination. L’illustration suivante montre comment créer une application web et la déployer dans le groupe de ressources que vous avez créé.

    ![Créer un groupe de ressources](./media/resource-group-template-deploy-portal/select-existing-group.png)

    Vous pouvez également décider de créer un groupe de ressources lors du déploiement de vos ressources. Sélectionnez **Créer** et donnez un nom au groupe de ressources.

    ![créer un groupe de ressources](./media/resource-group-template-deploy-portal/select-new-group.png)

4. Votre déploiement se met en route. Ce déploiement peut prendre quelques minutes. Vous recevez une notification une fois le déploiement terminé.

    ![afficher une notification](./media/resource-group-template-deploy-portal/view-notification.png)

5. Après avoir déployé vos ressources, vous pouvez ajouter d’autres ressources au groupe de ressources au moyen de la commande **Ajouter** sur le panneau du groupe de ressources.

    ![ajouter une ressource](./media/resource-group-template-deploy-portal/add-resource.png)

## Déployer des ressources à partir d’un modèle personnalisé

Si vous souhaitez effectuer un déploiement sans utiliser l’un des modèles de Marketplace, vous pouvez créer un modèle personnalisé qui définit l’infrastructure de votre solution. Pour en savoir plus sur la création de modèles, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

1. Pour déployer un modèle personnalisé à travers le portail, sélectionnez **Nouveau**, puis recherchez le **Déploiement de modèle** jusqu’à ce que vous puissiez le sélectionner dans les options.

    ![rechercher un déploiement de modèle](./media/resource-group-template-deploy-portal/search-template.png)

2. Sélectionnez **Déploiement de modèle** dans les ressources disponibles.

    ![sélectionner un déploiement de modèle](./media/resource-group-template-deploy-portal/select-template.png)

3. Après avoir lancé le déploiement du modèle, ouvrez le modèle vide qui peut être personnalisé.

    ![créer un modèle](./media/resource-group-template-deploy-portal/show-custom-template.png)

    Dans l’éditeur, ajoutez la syntaxe JSON qui définit les ressources que vous souhaitez déployer. Sélectionnez **Enregistrer** lorsque vous avez terminé. Pour obtenir des conseils sur l’écriture de la syntaxe JSON, consultez [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md).

    ![modifier un modèle](./media/resource-group-template-deploy-portal/edit-template.png)

4. Vous pouvez également sélectionner un modèle existant à partir des [modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/). Ces modèles sont fournis par la communauté. Ils couvrent de nombreux scénarios courants, et il est donc possible que quelqu’un ait ajouté un modèle semblable à celui que vous tentez de déployer. Vous pouvez rechercher parmi les modèles pour trouver un élément qui correspond à votre scénario.

    ![sélectionner un modèle de démarrage rapide](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    Vous pouvez afficher le modèle sélectionné dans l’éditeur.

5. Après avoir entré toutes les autres valeurs, sélectionnez **Créer** pour déployer le modèle.

    ![déployer un modèle](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## Déployer des ressources à partir d’un modèle enregistré dans votre compte

Le portail vous permet d’enregistrer un modèle dans votre compte Azure et de le redéployer ultérieurement. Pour plus d’informations sur l’utilisation de ces modèles enregistrés, accédez à [Prise en main des modèles privés sur le Portail Azure](./marketplace-consumer/mytemplates-getstarted.md).

1. Pour rechercher vos modèles enregistrés, sélectionnez **Parcourir** > **Modèles**.

    ![parcourir les modèles](./media/resource-group-template-deploy-portal/browse-templates.png)

2. Dans la liste des modèles enregistrés dans votre compte, sélectionnez celui sur lequel vous souhaitez travailler.

    ![modèles enregistrés](./media/resource-group-template-deploy-portal/saved-templates.png)

3. Sélectionnez **Déployer** pour redéployer ce modèle enregistré.

    ![déployer un modèle enregistré](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## Étapes suivantes

- Pour visualiser les journaux d’audit, voir [Opérations d’audit avec Resource Manager](resource-group-audit.md).
- Pour résoudre les erreurs de déploiement, voir [Résolution des problèmes liés aux déploiements de groupes de ressources avec le Portail Azure](resource-manager-troubleshoot-deployments-portal.md).
- Pour récupérer un modèle à partir d’un déploiement ou d’un groupe de ressources, consultez [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md).

<!---HONumber=AcomDC_0921_2016-->