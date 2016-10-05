<properties
	pageTitle="Première connexion Azure Multi-Factor Authentication"
	description="Cette page décrit l’expérience de l’utilisateur quand il se connecte pour la première fois."
	services="multi-factor-authentication"
	keywords="utilisation du répertoire azure, active directory dans le cloud, didacticiel sur active directory"
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
	ms.date="08/22/2016"
	ms.author="kgremban"/>
# Configuration de l’expérience Azure Multi-Factor Authentication

 Les paramètres de vérification de sécurité supplémentaires sont utilisés quand un administrateur a configuré votre compte pour que votre mot de passe et une réponse de votre téléphone soient obligatoires pour vérifier votre identité. Si un administrateur a configuré votre compte pour exiger la vérification de sécurité supplémentaire, **vous ne pouvez pas vous connecter tant que vous n’avez pas terminé le processus d’inscription automatique**.

## Détermination de l’utilisation de l’authentification multifacteur

 La première fois que vous vous connectez après que votre compte a été configuré, vous devez commencer le processus d’inscription automatique. Vous pouvez commencer ce processus en cliquant sur **Configurer maintenant.**

![Paramétrage](./media/multi-factor-authentication-end-user-first-time/first.png)

La procédure d’inscription vous permet de spécifier votre méthode préférée de vérification. Il peut s’agir de l’une des opérations suivantes dans le tableau ci-dessous. Pour obtenir plus d’informations, notamment une procédure, cliquez simplement sur l’une des méthodes.

Méthode|Description
:------------- | :------------- |
[Appel téléphonique](multi-factor-authentication-end-user-first-time-mobile-phone.md)| Effectue un appel vocal automatisé vers le téléphone d’authentification. L’utilisateur répond à l’appel et appuie sur la touche # du clavier du téléphone pour s’authentifier. Ce numéro de téléphone n’est pas synchronisé avec Active Directory local.
[SMS sur téléphone mobile](multi-factor-authentication-end-user-first-time-mobile-phone.md)|Envoie un message texte contenant un code de vérification à l’utilisateur. L’utilisateur est invité à répondre au SMS avec le code de vérification ou à entrer le code de vérification dans l’interface de connexion.
[Téléphone de bureau](multi-factor-authentication-end-user-first-time-office-phone.md)|Passe un appel vocal automatisé à l’utilisateur. L’utilisateur répond à l’appel et appuie sur la touche # du clavier du téléphone pour s’authentifier.
[Application mobile](multi-factor-authentication-end-user-first-time-mobile-app.md)|Envoie une notification à l’application Microsoft Authenticator sur le smartphone ou la tablette de l’utilisateur. L’utilisateur appuie sur « Vérifier » dans l’application pour s’authentifier. Ou bien, l’application peut également servir de jeton OTP pour l’authentification en mode hors connexion. L’utilisateur saisit le jeton dans l’écran de connexion pour s’authentifier.<br><p> L’application Microsoft Authenticator peut fonctionner dans 2 modes différents pour fournir la sécurité supplémentaire qu’un service d’authentification multifacteur peut offrir. Il s’agit des modes des suivants :<li> **Notification** - Dans ce mode, l’application Microsoft Authenticator empêche l’accès non autorisé aux comptes et arrête les transactions frauduleuses. Cette opération est effectuée à l’aide d’une notification Push sur votre téléphone ou votre appareil inscrit. Affichez simplement la notification et si elle est légitime, sélectionnez Authentifier. Dans le cas contraire, vous pouvez choisir de refuser ou de refuser et signaler la notification frauduleuse. Pour plus d’informations sur le signalement des notifications frauduleuses, consultez la rubrique sur l’utilisation de la fonctionnalité de refus et de signalement d’une fraude pour Multi-Factor Authentication. </li><p><li>**Mot de passe unique** - Dans ce mode, l’application Microsoft Authenticator peut être utilisée comme jeton logiciel pour générer un code de vérification OATH. Ce code de vérification peut ensuite être entré avec le nom d’utilisateur et un mot de passe pour fournir la deuxième forme d’authentification.</li><br><p> L'application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

<!---HONumber=AcomDC_0921_2016-->