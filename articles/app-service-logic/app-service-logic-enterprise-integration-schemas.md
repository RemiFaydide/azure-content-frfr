<properties 
	pageTitle="Vue d’ensemble des schémas et d’Enterprise Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
	description="Découvrez comment utiliser les schémas avec Enterprise Integration Pack et vos applications logiques" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2016" 
	ms.author="deonhe"/>

# En savoir plus sur les schémas et Enterprise Integration Pack  

## Pourquoi utiliser un schéma ?
Vous utilisez des schémas pour confirmer que les documents XML que vous recevez sont valides, ce qui signifie que les documents contiennent les données attendues dans un format prédéfini.

## Ajout d’un schéma
À partir du portail Azure :

1. Sélectionnez **Plus de services** ![](./media/app-service-logic-enterprise-integration-overview/overview-11.png)
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats ![](./media/app-service-logic-enterprise-integration-overview/overview-21.png)
3. Sélectionnez le **compte d’intégration** auquel ajouter le schéma. ![](./media/app-service-logic-enterprise-integration-overview/overview-31.png)
4. Sélectionnez la mosaïque **Schémas**![](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)

#### Pour ajouter un fichier de schéma d’une taille inférieure à 2 Mo  

5. Sélectionnez le bouton **Ajouter** dans le panneau Schémas qui s’affiche ![](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)
6. Entrez un **nom** pour votre schéma, puis sélectionnez l’icône de dossier située à droite de la zone de texte **Schéma** pour télécharger le fichier de schéma. Une fois le processus de téléchargement terminé, sélectionnez le bouton **OK**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)

#### Pour ajouter un fichier de schéma d’une taille supérieure à 2 Mo (maximum de 8 Mo)  

7. Si le niveau de sécurité de l’accès aux objets blob est défini sur **Public** ![](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)

  * Téléchargez le schéma sur le stockage, puis copiez l’URI ![](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)

  * Sélectionnez Fichier volumineux dans Ajouter un schéma et indiquez l’URI dans URI du contenu. ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)

8. Si le niveau de sécurité de l’accès aux objets blob est défini sur **No anonymous access** (Pas d’accès anonyme) ![](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)

  * Chargez le schéma dans le stockage. ![](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

  * Générez un URI de signature d’accès partagé pour le schéma. ![](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

  * Sélectionnez Fichier volumineux dans Ajouter un schéma et indiquez l’URI de signature d’accès partagé dans URI du contenu. ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)

9. Vous devez voir le schéma qui vient d’être ajouté. ![](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)

## Utilisation des schémas
- Les schémas sont utilisés pour valider les messages échangés dans un scénario d’entreprise à entreprise (B2B).

## Modification de schémas
1. Sélectionnez la mosaïque **Schémas**
2. Sélectionnez le schéma que vous souhaitez modifier dans le panneau Schémas qui s’affiche
3. Sélectionnez le lien **Télécharger** dans le panneau Schémas ![](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)
4. Sélectionnez le fichier de schéma que vous souhaitez télécharger à l’aide de la boîte de dialogue de sélection de fichier qui s’ouvre.
5. Sélectionnez **Ouvrir** dans le sélecteur de fichiers ![](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)
6. Vous recevez une notification indiquant que le téléchargement a réussi.

## Suppression de schémas
1. Sélectionnez la mosaïque **Schémas**
2. Sélectionnez le schéma que vous souhaitez supprimer dans le panneau Schémas qui s’affiche
3. Sélectionnez le lien **Supprimer** dans la barre de menu du panneau Schémas. ![](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)
4. Si vous voulez vraiment supprimer le schéma sélectionné, choisissez **Oui** dans la boîte de dialogue Supprimer un schéma pour confirmer votre choix ![](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)
5. Enfin, notez que la liste des schémas dans le panneau Schémas s’actualise et que le schéma que vous avez supprimé n’est plus répertorié ![](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)

## Étapes suivantes

- [En savoir plus sur Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")

<!---HONumber=AcomDC_0921_2016-->