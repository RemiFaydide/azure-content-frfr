
<properties
    pageTitle="Modifier le client Azure Active Directory dans Azure RemoteApp | Microsoft Azure"
    description="Apprenez à modifier le client Azure Active Directory associé à Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# Modification du client Azure Active Directory dans Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).

Azure RemoteApp utilise Azure Active Directory (Azure AD) pour autoriser l’accès utilisateur. Le seul locataire Azure AD que vous pouvez utiliser dans Azure RemoteApp est celui qui est associé à l'abonnement Azure. Vous pouvez afficher l'abonnement associé dans la page **Paramètres** du portail. Examinez la colonne **Annuaire** dans l'onglet **Abonnements**.

> [AZURE.NOTE] Pour que cette modification réussisse, commencez par supprimer tous les utilisateurs dans le locataire Azure Active Directory existant à partir de toutes les collections Azure RemoteApp. Pour cela, accédez au portail Azure, puis à l'onglet **Azure RemoteApp** et ouvrez chaque collection Azure RemoteApp. Accédez à l’onglet **Utilisateurs** et supprimez les utilisateurs qui appartiennent à votre client Azure Active Directory actuel. Répétez l’opération pour toutes les collections Azure RemoteApp. Sans cela, vous ne serez pas en mesure de créer des collections ni de les corriger.

Si vous souhaitez utiliser un autre locataire, suivez ces étapes pour modifier l’association avec votre abonnement :

1. Dans le portail, supprimez les utilisateurs Azure AD auxquels vous avez donné accès aux collections Azure RemoteApp. (Voir la remarque précédente pour les étapes de cette procédure).


2. Définissez un compte Microsoft (anciennement appelé Live ID) comme administrateur du service. (Ne savez pas si vous êtes déjà l'administrateur de service ? Vous pouvez vous en assurer en cliquant sur **Paramètres -> administrateurs**.) À présent, voici comment effectuer les modifications
	1. Cliquez sur l'utilisateur actuellement connecté dans le coin supérieur droit, puis cliquez sur **Afficher ma facture**.
	2. Cliquez sur l'abonnement. Ensuite, sur la nouvelle page, faites défiler vers le bas et cliquez sur **Modifiez les données d'abonnement** à droite. (Triez du milieu en bas à droite, si cela peut vous aider.)
	3. Saisissez le compte Microsoft pour l'utilisateur qui doit être l'administrateur du service.

3. Déconnectez-vous maintenant du portail, puis reconnectez-vous avec le compte Microsoft que vous avez spécifié à l'étape précédente.


4. Cliquez sur **New -> App Services -> Active Directory -> Annuaire -> Créer personnalisée**.
5. Sous **Annuaire**, choisissez **Utiliser un annuaire existant**. Vous allez devoir vous déconnecter du portail maintenant. Appuyez sur **Je suis prêt à me déconnecter**.
6. Reconnectez-vous au portail en tant qu'un administrateur général de l'annuaire que vous souhaitez ajouter. (Si vous n'étiez pas déjà un administrateur général, vous le serez après vous être reconnecté et déconnecté.)
7. Lors de votre connexion, il vous sera demandé si vous souhaitez utiliser votre locataire Active Directory existant avec votre abonnement. Cliquez sur **Continuer**, puis sur **Se déconnecter maintenant**.
5. Connectez-vous à nouveau et revenez à **Paramètres -> Abonnements**. Sélectionnez votre abonnement, puis cliquez sur **Modifier l'annuaire**. Sélectionnez le locataire Azure AD à utiliser.



Vous pouvez à présent utiliser le client Azure AD pour contrôler l’accès à l’abonnement Azure et configurer l’accès utilisateur dans Azure RemoteApp.

<!---HONumber=AcomDC_0817_2016-->