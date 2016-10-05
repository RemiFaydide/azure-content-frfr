<properties
	pageTitle="Utiliser une application mobile comme méthode de contact avec Azure Multi-Factor Authentication | Microsoft Azure"
	description="Cet article vous montre comment utiliser une application mobile comme méthode de contact principale pour Azure Multi-Factor Authentication."
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
	ms.date="08/30/2016"
	ms.author="kgremban"/>

# Utiliser une application mobile comme méthode de contact avec Azure Multi-Factor Authentication

Si vous souhaitez utiliser l’application Microsoft Authenticator comme méthode de contact principale, vous pouvez vous référer à cet article. Il vous guide dans la configuration d’Azure Multi-Factor Authentication pour utiliser votre application mobile comme méthode de contact principale.

L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Utiliser Microsoft Authenticator comme méthode de contact


1. Sur l’écran **Vérification de sécurité supplémentaire**, sélectionnez **Application mobile** dans la liste déroulante.
2. Sélectionnez **Notification** ou **Mot de passe à usage unique**, puis **Configurer**.

	![Écran de vérification de sécurité supplémentaire](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. Sur le téléphone où est installée l’application Microsoft Authenticator, ouvrez l’application et sélectionnez **+** pour ajouter un compte.
4. Spécifiez que vous souhaitez ajouter un compte professionnel ou scolaire. Le scanneur de code QR s’ouvre. Si votre appareil photo ne fonctionne pas correctement, vous pouvez choisir d’entrer manuellement les informations de votre société. Pour plus d’informations, consultez [Ajouter manuellement un compte](#add-an-account-manually).

	![Écran de sélection d’un compte](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

	![Écran de choix entre la numérisation d’un code QR et la saisie manuelle d’un compte](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

5. Numérisez l’image du code QR apparaissant à l’écran de configuration de l’application mobile. Sélectionnez **Terminer** pour fermer l’écran de code QR.

	![Écran de code QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

	Si vous ne parvenez pas à numériser le code QR, vous pouvez entrer les informations manuellement.

	![Écran de saisie manuelle des informations](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

6. Une fois l’activation terminée sur le téléphone, sélectionnez **Me contacter**. Cette étape envoie une notification ou un code de vérification sur votre téléphone. Sélectionnez **Vérifier**.

	![Écran permettant de sélectionner Vérifier pour se connecter](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

7. Si votre entreprise requiert un code confidentiel pour approuver la vérification de l’identification, entrez-le.

	![Zone permettant d’entrer un code confidentiel](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. Une fois le code confidentiel entré, sélectionnez **Fermer**. À ce stade, la vérification doit réussir.
9. Nous vous recommandons d’entrer votre numéro de téléphone mobile en cas de perte d’accès à votre application mobile. Spécifiez votre pays dans la liste déroulante et entrez votre numéro de téléphone portable dans la zone située en regard du nom de pays. Sélectionnez **Suivant**.
10. À ce stade, vous avez configuré votre méthode de contact. Il est maintenant temps de configurer les mots de passe des applications hors navigateur, comme Outlook 2010 ou les versions antérieures. Si vous n’utilisez pas ces applications, sélectionnez **Terminé**. Sinon, passez à l’étape suivante.

	![Écran de création d’un mot de passe d’application](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

11. Si vous utilisez des applications hors navigateur, copiez le mot de passe d’application fourni et collez-le dans vos applications. Pour connaître les étapes relatives à des applications données, comme Outlook et Lync, consultez Modification du mot de passe dans votre messagerie électronique par le mot de passe d’application et Modification du mot de passe dans votre application par le mot de passe d’application.
12. Sélectionnez **Terminé**.


## Ajouter un compte manuellement
Si vous souhaitez ajouter un compte manuellement :

1. Sélectionnez le bouton **Entrer un compte manuellement**.

	![Écran de saisie du code et de l’URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

	![Écran de saisie du code et de l’URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

2. Si vous disposez d’un compte qui a déjà Azure Multi-Factor Authentication, entrez le code et l’URL fournis sur la même page que celle illustrant le code-barres. Ces informations sont destinées aux zones **Code** et **URL** de l’application mobile.

	![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. Une fois l’activation terminée, sélectionnez **Me contacter**. Cette étape envoie une notification ou un code de vérification sur votre téléphone. Sélectionnez **Vérifier**.

<!---HONumber=AcomDC_0921_2016-->