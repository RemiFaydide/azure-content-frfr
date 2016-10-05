<properties 
	pageTitle="Microsoft Azure Multi-Factor Authentication - État utilisateur"
	description="En savoir plus sur l’état utilisateur dans Azure MFA."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# User States in Azure Multi-Factor Authentication (États des utilisateurs dans Azure Multi-Factor Authentication)

Les comptes d'utilisateur dans Azure Multi-Factor Authentication peuvent présenter les trois états suivants :

State | Description |Applications affectées (autres que des navigateurs)| Remarques
:-------------: | :-------------: |:-------------: |:-------------: |
Désactivé | État par défaut d’un nouvel utilisateur non inscrit dans l’authentification multifacteur.|Non|L’utilisateur n’utilise pas l’authentification multifacteur actuellement.
Activé |L'utilisateur a été inscrit dans l'authentification multifacteur.|Non. Ils continuent de fonctionner jusqu’à ce que le processus d’inscription soit terminé.|L'utilisateur est activé mais n'a pas terminé le processus d'inscription. Il seront invités à terminer le processus lors de la prochaine connexion.
Appliquée|L'utilisateur a été inscrit et a terminé le processus d'inscription pour utiliser l'authentification multifacteur.|Oui. Les applications requièrent des mots de passe d'application. | L'utilisateur peut ou non avoir terminé l'inscription. S’ils ont terminé le processus d’inscription, ils utilisent l’authentification multifacteur. Autrement, l’utilisateur est invité à terminer le processus lors de la prochaine connexion.

## Modification de l’état utilisateur
Un état utilisateur est modifié selon qu’il a été configuré pour l’authentification multi-facteur ou non et si l’utilisateur a terminé le processus. Lorsque vous activez l’authentification multi-facteur pour un utilisateur, l’état utilisateur passe de « désactivé » à « activé ». Une fois que l’utilisateur dont l’état est passé à « activé » se connecte et termine le processus, son état passe à « appliqué ».

### Pour afficher l’état utilisateur
--------------------------------------------------------------------------------
1.  Connectez-vous en tant qu’administrateur sur le **portail Azure Classic**.
2.  Cliquez à gauche sur **Active Directory**.
3.  Sous **Répertoire**, cliquez sur le répertoire de l’utilisateur que vous souhaitez activer. ![Cliquez sur Répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En haut de la page, cliquez sur **Utilisateurs**.
5.  En bas de la page, cliquez sur **Gérer Multi-Factor Auth**. ![Cliquez sur Répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Un nouvel onglet s’ouvre dans votre navigateur. Vous pourrez afficher l’état utilisateur. ![Cliquez sur Répertoire](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###Pour passer l’état de désactivé à activé
1.  Connectez-vous en tant qu’administrateur sur le **portail Azure Classic**.
2.  Cliquez à gauche sur **Active Directory**.
3.  Sous **Répertoire**, cliquez sur le répertoire de l’utilisateur que vous souhaitez activer. ![Cliquez sur Répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En haut de la page, cliquez sur **Utilisateurs**.
5.  En bas de la page, cliquez sur **Gérer Multi-Factor Auth**. ![Cliquez sur Répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Un nouvel onglet s’ouvre dans votre navigateur. Recherchez l'utilisateur que vous souhaitez activer pour l'authentification multifacteur. Vous devrez peut-être modifier l'affichage en haut de la page. Assurez-vous que l’état est **désactivé.** ![Activer un utilisateur](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  **Cochez** la case en regard du nom des utilisateurs.
7.  Sur la droite, cliquez sur **Activer**. ![Activer un utilisateur](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Cliquez sur **Activer l’authentification multifacteur**. ![Activer un utilisateur](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Vous remarquerez que l’état est passé de **désactivé** à **activé**. ![Activer des utilisateurs](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Après que vous avez activé les utilisateurs, il est recommandé de les avertir par e-mail. Il doit également les informer de la façon dont ils peuvent utiliser leurs applications autres qu’un explorateur pour éviter le verrouillage.

### Pour passer l’état d’activé/appliqué à désactivé
1.  Connectez-vous en tant qu’administrateur sur le **portail Azure Classic**.
2.  Cliquez à gauche sur **Active Directory**.
3.  Sous **Répertoire**, cliquez sur le répertoire de l’utilisateur que vous souhaitez activer. ![Cliquez sur Répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En haut de la page, cliquez sur **Utilisateurs**.
5.  En bas de la page, cliquez sur **Gérer Multi-Factor Auth**. ![Cliquez sur Répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Un nouvel onglet s’ouvre dans votre navigateur. Recherchez l’utilisateur que vous souhaitez désactiver. Vous devrez peut-être modifier l'affichage en haut de la page. Assurez-vous que l’état est soit **activé**, soit **appliqué.**
7.  **Cochez** la case en regard du nom des utilisateurs.
7.  Sur la droite, cliquez sur **Désactiver**. ![Désactiver l’utilisateur](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Un message apparaît, vous invitant à confirmer votre décision. Cliquez sur **Oui**. ![Désactiver l’utilisateur](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  Vous verrez si l’opération s’est terminée correctement. Cliquez sur **Fermer** ![Désactiver l’utilisateur](./media/multi-factor-authentication-get-started-user-states/userstate4.png).

<!---HONumber=AcomDC_0921_2016-->