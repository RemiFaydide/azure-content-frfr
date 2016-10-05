<properties
	pageTitle="Quels sont les mots de passe d’application dans Azure MFA ?"
	description="Cette page permet aux utilisateurs de comprendre ce que sont les mots de passe d’application et leur utilisation avec Azure MFA."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>



# Que sont les mots de passe d’application dans Azure Multi-Factor Authentication ?

Actuellement, certaines applications sans navigateur telles que le client de messagerie native Apple qui utilise Exchange Active Sync ne prennent pas en charge l’authentification multifacteur. L’authentification multifacteur est activée par l’utilisateur. Cela signifie que si un utilisateur a été activé pour l’authentification multifacteur et qu’il tente d’utiliser les applications sans navigateur, il ne peut le faire. Un mot de passe d’application permet de remédier à ce problème.

>[AZURE.NOTE] Authentification moderne pour les clients Office 2013
>
> Les clients Office 2013 (y compris Outlook) prennent désormais en charge de nouveaux protocoles d’authentification et ils peuvent être activés pour prendre en charge Multi-Factor Authentication. Ainsi, une fois activés, les mots de passe d’application ne sont pas obligatoires avec les clients Office 2013. Pour plus d’informations, consultez la page [Version préliminaire publique de l’authentification moderne Office 2013 annoncée](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

## Utilisation de mots de passe d’application

Voici quelques points à retenir sur l’utilisation de mots de passe d’application.

- Le mot de passe est généré automatiquement et il n’est pas fourni par l’utilisateur. Le mot de passe automatiquement généré est en effet beaucoup plus difficile à pirater et bien mieux sécurisé.
- À l’heure actuelle, un utilisateur peut posséder jusqu’à 40 mots de passe. Si vous essayez d’en créer un une fois que vous avez atteint la limite, vous devrez supprimer l’un de vos mots de passe d’application existants pour en créer un autre.
- Il est recommandé de créer des mots de passe d’application par appareil et non par application. Par exemple, vous pouvez créer un mot de passe d’application pour votre ordinateur portable et utiliser ce mot de passe pour toutes vos applications sur cet ordinateur.
- Vous obtenez un mot de passe d’application quand vous vous connectez pour la première fois. Si vous avez besoin d’autres mots de passe, vous pouvez les créer.

![Paramétrage](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Une fois que vous disposez d’un mot de passe d’application, vous l’utilisez à la place de votre mot de passe d’origine avec ces applications sans navigateur. Par exemple, dans le cas de l’authentification multifacteur et du client de messagerie native Apple sur votre téléphone. Le mot de passe d’application permet à celle-ci de contourner l’authentification multifacteur et de continuer à fonctionner.

## Création et suppression des mots de passe d’application
À la première connexion, vous obtenez un mot de passe d’application que vous pouvez utiliser. En outre, vous pouvez également créer et supprimer des mots de passe d’application par la suite. La procédure à suivre dépend de l’utilisation de l’authentification multifacteur. Choisissez celle qui vous convient le mieux.

Mode d’utilisation de l’authentification multifacteur|Description
:------------- | :------------- | 
<a href="#avec">Je l’utilise avec Office 365</a>| Cela signifie que vous souhaitez créer des mots de passe d’application par le biais du portail Office 365.
<a href="#eee">Je ne sais pas</a>|Cela signifie que vous pouvez créer des mots de passe d’application par le biais de [https://myapps.microsoft.com](https://myapps.microsoft.com)
<a href="#Je">Je l’utilise avec Microsoft Azure</a>| Cela signifie que vous souhaitez créer des mots de passe d’application par le biais du portail Azure.

<h2 id="avec">Création et suppression des mots de passe d’application avec Office 365</h2> 

Si vous utilisez l’authentification multifacteur avec Office 365, vous pourrez créer et supprimer des mots de passe d’application par le biais du portail Office 365.

### Pour créer des mots de passe d'application dans le portail Office 365
--------------------------------------------------------------------------------

1. Connectez-vous au [portail Office 365](https://login.microsoftonline.com/).
2. Dans l’angle supérieur droit, sélectionnez le widget et choisissez les paramètres d’Office 365.
3. Cliquez sur Vérification de sécurité supplémentaire.
4. Sur la droite, cliquez sur le lien indiquant **Mettre à jour mes numéros de téléphone utilisés pour la sécurité du compte.** ![Paramétrage](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Cette opération vous amènera sur la page qui vous permet de modifier vos paramètres. ![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. En haut de l’écran, en regard de Vérification de sécurité supplémentaire, cliquez sur **mots de passe d’application.**
7. Cliquez sur **Créer**. ![Cloud](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Saisissez un nom pour le mot de passe d’application et cliquez sur **Suivant**. ![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application. ![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### Pour supprimer des mots de passe d’application avec le portail Office 365
--------------------------------------------------------------------------------


1. Connectez-vous au [portail Office 365](https://login.microsoftonline.com/).
2. Dans l’angle supérieur droit, sélectionnez le widget et choisissez les paramètres d’Office 365.
3. Cliquez sur Vérification de sécurité supplémentaire.
4. Sur la droite, cliquez sur le lien indiquant **Mettre à jour mes numéros de téléphone utilisés pour la sécurité du compte.** ![Paramétrage](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Cette opération vous amènera sur la page qui vous permet de modifier vos paramètres. ![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. En haut de l’écran, en regard de Vérification de sécurité supplémentaire, cliquez sur **mots de passe d’application.**
7. En regard du mot de passe d’application à supprimer, cliquez sur **Supprimer**. ![Supprimer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Confirmez la suppression en cliquant sur **oui**. ![Confirmer la suppression](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Une fois le mot de passe d’application supprimé, vous pouvez cliquer sur **Fermer**. ![Fermer](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


<h2 id="Je">Création et suppression des mots de passe d’application avec Office 365avec le portail Myapps</h2>.
Si vous n’êtes pas sûr de la façon d’utiliser l’authentification multifacteur, vous pouvez toujours créer et supprimer des mots de passe d’application via le portail myapps.

<h2 id="eee">Pour créer un mot de passe d’application à l’aide d’un portail Myapps</h2>

1. Ouvrez une session sur [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. En haut de la page, sélectionnez le profil.
3. Sélectionnez Vérification de sécurité supplémentaire. ![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Cette opération vous amènera sur la page qui vous permet de modifier vos paramètres. ![Paramétrage](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. En haut de l’écran, en regard de Vérification de sécurité supplémentaire, cliquez sur **mots de passe d’application.**
6. Cliquez sur **Créer**. ![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Saisissez un nom pour le mot de passe d’application et cliquez sur **Suivant**. ![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application. ![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### Pour supprimer un mot de passe d’application à l’aide d’un portail Myapps

1. Ouvrez une session sur [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. En haut de la page, sélectionnez le profil.
3. Sélectionnez Vérification de sécurité supplémentaire. ![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Cette opération vous amènera sur la page qui vous permet de modifier vos paramètres. ![Paramétrage](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. En haut de l’écran, en regard de Vérification de sécurité supplémentaire, cliquez sur **mots de passe d’application.**
6. En regard du mot de passe d’application à supprimer, cliquez sur **Supprimer**. ![Supprimer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Confirmez la suppression en cliquant sur **oui**. ![Confirmer la suppression](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Une fois le mot de passe d’application supprimé, vous pouvez cliquer sur **Fermer**. ![Fermer](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## Pour créer des mots de passe d’application dans le portail Azure

Si vous utilisez l’authentification multifacteur avec Azure, vous voulez peut-être créer des mots de passe d’application par le biais du portail Azure.

### Pour créer des mots de passe d’application dans le portail Azure

1. Connectez-vous au portail de gestion Azure.
2. En haut de la page, cliquez avec le bouton droit sur votre nom d’utilisateur et sélectionnez Vérification de sécurité supplémentaire.
3. En haut de la page de vérification, sélectionnez les mots de passe d'application
4. Cliquez sur **Create**.
5. Saisissez un nom pour le mot de passe d’application, puis cliquez sur **Suivant**
6. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application. ![Cloud](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### Pour supprimer des mots de passe d’application du portail Azure

1. Connectez-vous au portail de gestion Azure.
2. En haut de la page, cliquez avec le bouton droit sur votre nom d’utilisateur et sélectionnez Vérification de sécurité supplémentaire.
3. En haut de l’écran, en regard de Vérification de sécurité supplémentaire, cliquez sur **mots de passe d’application.**
4. En regard du mot de passe d’application à supprimer, sélectionnez **Supprimer**.
5. Confirmez la suppression en cliquant sur **oui**.
6. Une fois le mot de passe d’application supprimé, vous pouvez cliquer sur **Fermer**. ![Fermer](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

<!---HONumber=AcomDC_0921_2016-->