<properties
	pageTitle="Utiliser le client Office 365 avec un abonnement Azure | Microsoft Azure"
	description="Découvrez comment ajouter un annuaire (client) Office 365 à un abonnement Azure afin d’établir l’association."
	services=""
	documentationCenter=""
	authors="jiangchen79"
	manager="mbaldwin"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="cjiang"/>

# Associer un client Office 365 à un abonnement Azure
Scénario : vous avez obtenu les deux abonnements Azure et Office 365 séparément et vous souhaitez désormais accéder au client Office 365 à partir de l’abonnement Azure. Cet article vous montre que vous pouvez y parvenir facilement.

> [AZURE.NOTE] Cet article ne s’applique pas aux clients du Contrat Entreprise (EA).

## Conseils rapides
Si vous souhaitez associer votre client Office 365 à l’abonnement Azure, utilisez votre compte Azure pour ajouter votre client Office 365, puis associez votre abonnement Azure à ce dernier. Consultez la procédure détaillée.

## Procédure détaillée
Dans ce scénario, Kelley Wall est un utilisateur qui possède un abonnement à Azure sous le compte kelly.wall@outlook.com. Kelley possède également un abonnement Office 365 sous le compte kelley.wall@contoso.onmicrosoft.com. Kelley souhaite désormais accéder au client Office 365 avec l’abonnement Azure.

![msa-aad-status](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![office-365-user](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

**Configuration requise :**

- Vous avez besoin des informations d’identification de l’administrateur de service de l’abonnement Azure. Les coadministrateurs ne peuvent pas exécuter un sous-ensemble des étapes.
- Vous avez besoin des informations d’identification d’un administrateur général du client Office 365.
- L’adresse électronique de l’administrateur de service ne doit pas figurer dans le client Office 365.
- L’adresse électronique de l’administrateur de service ne doit pas correspondre à celle de l’administrateur général du client Office 365.
- Si vous utilisez actuellement une adresse électronique qui est à la fois un compte Microsoft et un compte professionnel, nous vous recommandons de modifier temporairement l’administrateur de service de votre abonnement Azure afin d’utiliser un autre compte Microsoft. Cela restreint les limitations connues de la procédure. Vous pouvez créer un compte Microsoft à la [page d’inscription de compte Microsoft](https://signup.live.com/).

	Pour changer votre administrateur de service, procédez comme suit :

	1. Connectez-vous au [portail de gestion des comptes Azure](https://account.windowsazure.com/subscriptions).
	2. Sélectionnez l’abonnement que vous souhaitez modifier.
	3. Sur le côté droit, cliquez sur **Modifier les détails de l’abonnement**.

		![azure-edit-subscription-details](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

	4. Dans la zone **ADMINISTRATEUR DE SERVICES**, entrez l’adresse de messagerie du nouvel administrateur de service.

		![change-subscription-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

Pour associer le client Office 365 à l’abonnement Azure, procédez comme suit :

1. 	Connectez-vous au [portail de gestion des comptes](https://account.windowsazure.com/subscriptions) à l’aide des informations d’identification d’administrateur de service.
2.	Dans le volet gauche, cliquez sur **ACTIVE DIRECTORY**.

	![classic-portal-active-directory-entry](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

	> [AZURE.NOTE] Vous ne devriez pas voir le client Office 365. Si vous le voyez, ignorez l’étape suivante.

	![aad-tenant-default](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Ajoutez le client Office 365 à votre abonnement Azure.
	1. Cliquez sur **NOUVEAU** > **ANNUAIRE** > **CRÉATION PERSONNALISÉE**.

		![aad-custom-create](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

	2. Dans la page **Ajouter un annuaire**, sélectionnez **Utiliser un annuaire existant** sous **ANNUAIRE**, puis cliquez sur **Je suis prêt à me déconnecter** et **Terminé** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

		![add-directory-use-existing](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

	3. Une fois que vous êtes déconnecté, connectez-vous à l’aide des informations d’identification d’administrateur global de votre client Office 365.

		![sign-in-global-admin-office-365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

	4. Cliquez sur **Continuer**.

		![use-contoso-directory-azure-verify](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

	5. Cliquez sur **Se déconnecter maintenant**.

		![use-contoso-directory-azure-confirm-and-sign-out](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

	6. Connectez-vous au [portail de gestion des comptes](https://account.windowsazure.com/subscriptions) à l’aide des informations d’identification d’administrateur de service.

		![azure-sign-in-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

	7. Votre client Office 365 doit s’afficher dans le tableau de bord.

		![office-365-tenant-appear-in-azure](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Commencez par modifier l’annuaire associé à votre abonnement Azure.

	1. Cliquez sur **Paramètres**.

		![azure-classic-portal-settings-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

	2. Sélectionnez votre abonnement, puis cliquez sur **MODIFIER L’ANNUAIRE**.

		![azure-subscription-edit-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

	3. Cliquez sur **Suivant** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

		![azure-change-associated-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

		> [AZURE.WARNING] Vous recevrez un avertissement vous informant que tous les coadministrateurs seront supprimés.

		![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

		>[AZURE.WARNING] En outre, tous les utilisateurs disposant du [contrôle d’accès en fonction du rôle (RBAC)](./active-directory/role-based-access-control-configure.md) avec accès affecté dans les groupes de ressources existants seront également supprimés. Toutefois, l’avertissement mentionne uniquement la suppression des coadministrateurs.

		![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

	4. Cliquez sur **Terminé** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Vous pouvez désormais ajouter vos comptes professionnels Office 365 en tant que coadministrateurs du client AAD.

	1. Cliquez sur l’onglet **ADMINISTRATEURS**, puis sur **AJOUTER**.

		![azure-classic-portal-settings-administrators](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

	2. Entrez un compte professionnel de votre client Office 365, sélectionnez l’abonnement Azure, puis cliquez sur **Terminé** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

		![azure-add-co-administrator](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

	3. Revenez à l’onglet **ADMINISTRATEURS** ; le compte professionnel doit être affiché en tant que coadministrateur.

		![azure-co-administrator-added](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. Vous pouvez ensuite tester l’accès avec le coadministrateur.

	1. Déconnectez-vous du portail de gestion des comptes.
	2. Ouvrez le [portail de gestion des comptes](https://account.windowsazure.com/subscriptions) ou le [portail Azure](https://portal.azure.com/).
	3. Si la page de connexion Azure inclut un lien **Sign in with your organization account** (Se connecter à l’aide d’un compte professionnel), cliquez sur ce dernier. Sinon, ignorez cette étape.

		![Azure-sign-in-with-orgid-link](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

	4. Entrez les informations d’identification du coadministrateur, puis cliquez sur **Se connecter**.

		![Azure-sign-in-with-co-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## Étapes suivantes
Dans un scénario connexe, vous disposez déjà d’un abonnement à Office 365 et vous êtes prêt à prendre un abonnement à Azure, mais vous souhaitez utiliser le ou les comptes d’utilisateur Office 365 existants pour votre abonnement Azure. Ou bien, vous êtes un abonné Azure et vous souhaitez obtenir un abonnement à Office 365 pour les utilisateurs dans votre Azure Active Directory existant. Pour découvrir comment effectuer ces tâches, voir [Utiliser votre compte Office 365 existant avec votre abonnement Azure ou vice versa](billing-use-existing-office-365-account-azure-subscription.md).

<!---HONumber=AcomDC_0921_2016-->