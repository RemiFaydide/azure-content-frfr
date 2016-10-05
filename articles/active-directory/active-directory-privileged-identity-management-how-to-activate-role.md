<properties
   pageTitle="Comment activer ou désactiver un rôle | Microsoft Azure"
   description="Découvrez comment activer des rôles pour des identités privilégiées avec l’application Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="kgremban"/>

# Comment activer ou désactiver des rôles dans Azure AD Privileged Identity Management

Le service Azure Active Directory (AD) Privileged Identity Management simplifie la gestion par les entreprises de l’accès privilégié aux ressources dans Azure AD et d’autres services en ligne Microsoft tels qu’Office 365 ou que Microsoft Intune.

Si vous êtes éligible à un rôle d'administrateur, vous pouvez activer ce rôle lorsque vous devez effectuer une tâche qui nécessite ce rôle. Par exemple, si vous gérez occasionnellement des fonctionnalités d’Office 365, les administrateurs de rôle privilégié de votre organisation peuvent ne pas vous attribuer le rôle d’administrateur général permanent, étant donné que ce rôle affecte également les autres services. Au lieu de cela, ils peuvent vous attribuer des rôles Azure AD tels qu’administrateur Exchange Online. Vous pouvez demander une attribution temporaire de ce rôle lorsque vous avez besoin de ces privilèges, et par conséquent, vous aurez le contrôle d’administration pendant une période prédéterminée.

Cet article est destiné aux administrateurs qui doivent activer leur rôle dans Azure AD Privileged Identity Management (PIM). Il vous guidera à travers les étapes pour activer un rôle lorsque vous avez besoin des autorisations et pour désactiver le rôle lorsque vous avez terminé.


## Ajout de l’application Privileged Identity Management

Utilisez l’application Azure AD Privileged Identity Management dans le [portail Azure](https://portal.azure.com/) pour demander une activation de rôle, même si vous allez utiliser un autre portail ou PowerShell. Si vous n'avez pas l'application Azure AD Privileged Identity Management sur votre portail Azure, procédez comme suit pour commencer.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez votre nom d’utilisateur dans le coin supérieur droit du portail Azure, puis choisissez le répertoire que vous allez utiliser.
3. Sélectionnez **Plus de services** et utilisez la zone de texte Filtre pour rechercher **Azure AD Privileged Identity Management**.
4. Cochez **Épingler au tableau de bord**, puis cliquez sur **Créer**. L’application Privileged Identity Management s’ouvre.

## Activer un rôle

Lorsque vous avez besoin prendre un rôle, vous pouvez demander une activation avec le bouton **Activer mes rôles** dans l'application Azure AD Privileged Identity Management.


1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez la mosaïque Azure AD Privileged Identity Management.
2. Sélectionnez **Activer mes rôles**. La liste des rôles qui vous ont été attribués s’affiche.
3. Sélectionnez le rôle que vous souhaitez activer.
4. Sélectionnez **Activer**. Le panneau **Demande d’activation du rôle** s’affiche.
5. Certains rôles requièrent l’authentification multifacteur (MFA) avant de pouvoir activer le rôle. Vous ne devez vous authentifier qu’une seule fois par session.

    ![Vérification multifacteur avant activation du rôle : capture d’écran][2]

6. Entrez le motif de la demande d’activation dans le champ de texte. Certains rôles vous obligent à fournir un numéro de ticket d’incident.
7. Sélectionnez **OK**. Le rôle est maintenant activé et la modification de rôle est visible dans Microsoft Online Services.

## Désactiver un rôle

Une fois qu’un rôle a été activé, il se désactive automatiquement lorsque sa limite de temps est atteinte.

Si vous avez terminé tôt, vous pouvez également désactiver un rôle manuellement dans l'application Azure AD Privileged Identity Management. Sélectionnez **Activer mes rôles**, choisissez le rôle que vous venez de terminer, puis sélectionnez **Désactiver**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Si vous souhaitez en savoir plus sur Azure AD Privileged Identity Management, les liens suivants contiennent d'autres informations.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png

<!---HONumber=AcomDC_0921_2016-->