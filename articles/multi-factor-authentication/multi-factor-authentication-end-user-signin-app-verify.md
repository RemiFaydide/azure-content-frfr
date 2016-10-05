
<properties
	pageTitle="Connexion à l'application mobile par code de vérification avec Azure Multi-Factor Authentication"
	description="Cette page décrit comment un utilisateur peut se connecter à l'aide du code de vérification de l'application mobile avec Azure MFA."
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

# Connexion à l'application mobile par code de vérification avec Azure Multi-Factor Authentication


Les informations suivantes décrivent l'utilisation de l'authentification multifacteur avec votre application mobile, si vous utilisez un code de vérification.

## Connexion à l'aide d'un code de vérification avec votre application mobile

<ol>

<li>Connectez-vous à une application ou un service comme Office&#160;365 à l'aide de votre nom d'utilisateur et de votre mot de passe.</li>
<li>Microsoft vous demande un code de vérification.</li>


<center>![Setup](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)</center>

<li>Ouvrez l'application Azure Authenticator sur votre téléphone et entrez le code dans la zone où vous vous connectez.</li>

<center>![Setup](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)</center>


<li>Vous devez maintenant être connecté.</li>

<!---HONumber=AcomDC_0921_2016-->