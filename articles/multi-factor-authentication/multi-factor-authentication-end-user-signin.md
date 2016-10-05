<properties
	pageTitle="Expérience de connexion Azure MFA avec Azure Multi-Factor Authentication"
	description="Cette page vous fournit des conseils pour consulter les différentes méthodes de connexion disponibles avec Azure MFA."
	keywords="authentification de l'utilisateur, expérience de connexion, connexion avec un téléphone mobile, connexion avec le téléphone de bureau"
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
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Expérience de connexion avec Azure Multi-Factor Authentication
> [AZURE.NOTE]  La documentation suivante fournie sur cette page montre une expérience de connexion classique. Pour obtenir de l'aide concernant la connexion, référez-vous à [Problèmes avec Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md)



## Quelle sera votre expérience de connexion ?
Selon la méthode de connexion et l'authentification multifacteur utilisées, le processus est différent. Dans cette section, nous fournissons des informations sur le processus de connexion. Choisissez ce qui décrit le mieux votre méthode de connexion :


Que faites-vous ?|Description
:------------- | :------------- | 
<a href="#1">Connexion avec un téléphone mobile ou de bureau</a> | Voici le processus de connexion habituel lors d’une connexion à l'aide de votre téléphone mobile ou de bureau.
<a href="#2">Connexion avec l’application mobile à l’aide de la notification</a> | Voici le processus de connexion habituel lors d’une connexion à l'aide de l’application mobile avec des notifications.
<a href="#3">Connexion avec l'application mobile à l'aide du code de vérification</a>|Voici le processus de connexion habituel lors d’une connexion à l'aide de l’application mobile avec un code de vérification.
<a href="#4">Connexion avec une autre méthode</a>|Ceci vous présente le processus de connexion habituel si vous souhaitez utiliser une autre méthode.

<h2 id="1">Connexion avec un téléphone mobile ou de bureau</h2>

Les informations suivantes décrivent l'utilisation de l'authentification multifacteur avec votre téléphone de bureau ou mobile.

### Connexion avec un appel sur votre téléphone de bureau ou mobile

- Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.
- Microsoft vous appelle.

![Microsoft appelle](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Répondez au téléphone et appuyez sur la touche #.

![Réponse](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- Vous devez maintenant être connecté.</li>

<h2 id="2">Connexion avec l’application mobile à l’aide de la notification</h2>

Les informations suivantes décrivent l'utilisation de l'authentification multifacteur avec l’application Microsoft Authenticator lorsqu'une notification vous est envoyée.

### Pour vous connecter avec des notifications envoyées par l’application Microsoft Authenticator

- Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.
- Microsoft vous envoie une notification.

![Microsoft envoie une notification](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Répondez au téléphone et appuyez sur la touche de vérification. Si votre entreprise requiert un code PIN, il vous sera demandé ici.

![Verify](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- Vous devez maintenant être connecté.


<h2 id="3">Connexion avec l'application mobile à l'aide du code de vérification</h2>

Les informations suivantes décrivent l'utilisation de l'authentification multifacteur avec l’application Microsoft Authenticator lorsque vous utilisez un code de vérification.

### Pour vous connecter à l’application Microsoft Authenticator à l’aide d’un code de vérification

- Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.
- Microsoft vous demande un code de vérification.

![Entrer le code de vérification](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Ouvrez l’application Microsoft Authenticator sur votre téléphone et entrez le code dans la zone où vous vous connectez.

![Obtenir le code](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- Vous devez maintenant être connecté.


<h2 id="4">Connexion avec une autre méthode</h2>


La section suivante vous montre comment vous connecter avec une autre méthode quand votre méthode principale n’est pas disponible.

### Pour vous connecter avec une autre méthode

- Connectez-vous à une application ou un service comme Office 365 à l’aide de votre nom d’utilisateur et votre mot de passe.
- Sélectionnez Utiliser une autre option de vérification. Un choix de différentes options s’affiche. Le nombre qui s’affiche dépend du nombre que vous avez défini.

![Utiliser une autre méthode](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Choisissez une autre méthode et connectez-vous.

<!---HONumber=AcomDC_0921_2016-->