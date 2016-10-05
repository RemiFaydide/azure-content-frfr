<properties 
	pageTitle="Azure Multi-Factor Authentication - Prise en main"
	description="Choisissez la solution de sécurité d'authentification multifacteur la plus appropriée pour vous en définissant les éléments à protéger et l’emplacement des utilisateurs. Choisissez ensuite le cloud, le serveur MFA ou AD FS."
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
	ms.topic="get-started-article"
	ms.date="08/15/2016"
	ms.author="kgremban"/>

#Choix de la solution de sécurité multifacteur la plus appropriée pour vous

Étant donné qu’il existe plusieurs types d'authentification multifacteur Azure, nous devons déterminer plusieurs éléments pour connaître la version utiliser. Ces éléments sont :

-	<a href="#1">Les éléments à protéger</a>
-	<a href="#2">l’emplacement des utilisateurs</a>

Les sections suivantes fournissent des conseils sur la détermination de ces éléments.

<h2 id="1">Les éléments à protéger</h2>

Afin de déterminer la solution d'authentification multifacteur appropriée, nous devons tout d'abord connaître les éléments à protéger avec une seconde méthode d'authentification. S’agit-il d’une application dans Azure ? Ou un système d'accès à distance par exemple ? En déterminant les éléments à protéger, nous allons répondre à la question qui est de savoir où l’authentification multifacteur doit être activée.


Les éléments que vous souhaitez protéger| Multi-Factor Authentication dans le cloud|Serveur Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Applications Microsoft internes|* |* |
Applications SaaS dans la galerie d'applications|* |* |
Applications IIS publiées via le proxy d'application Azure AD|* |* |
Applications IIS non publiées via le proxy d'application Azure AD | |* |
Accès à distance comme VPN, RDG| |* |



<h2 id="2">l’emplacement des utilisateurs</h2>

Ensuite, selon l’emplacement de nos utilisateurs, nous pouvons déterminer la solution appropriée à utiliser, que ce soit l’authentification multifacteur dans le cloud ou locale avec le serveur MFA.



Emplacement de l'utilisateur| Solution
------------- | :------------- |
Azure Active Directory| Multi-Factor Authentication dans le cloud|
Azure AD et AD local à l'aide de la fédération avec AD FS| MFA dans le cloud et sur serveur sont les deux options disponibles
Azure AD et AD local à l'aide de DirSync, Azure AD Sync, Azure Connect AD : aucune synchronisation de mot de passe|MFA dans le cloud et sur serveur sont les deux options disponibles
Azure AD et AD local à l'aide de DirSync, Azure AD Sync, Azure Connect AD : avec synchronisation de mot de passe|Multi-Factor Authentication dans le cloud
Active Directory local|Serveur Multi-Factor Authentication

Le tableau suivant présente une comparaison des fonctionnalités de Multi-Factor Authentication dans le cloud et du serveur Multi-Factor Authentication.

 | Multi-Factor Authentication dans le cloud | Serveur Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Notification d'application mobile comme second facteur | ● | ● |
Code de vérification d'application mobile comme second facteur | ● | ●
Appel téléphonique comme second facteur | ● | ●
SMS unidirectionnel comme second facteur | ● | ●
SMS bidirectionnel comme second facteur | | ●
Jetons matériels comme second facteur | | ●
Mots de passe d'application pour les clients qui ne prennent pas en charge MFA | ● |  
Contrôle d'administration sur les méthodes d'authentification | ● | ●
Mode du code PIN | | ●
Alerte de fraude | ● | ●
Rapports MFA | ● | ●
Contournement à usage unique | | ●
Messages de bienvenue personnalisés pour les appels téléphoniques | ● | ●
ID d'appelant personnalisable pour les appels téléphoniques | ● | ●
Adresses IP approuvées | ● | ●
Mémoriser MFA pour les appareils fiables | ● |  
Accès conditionnel | ● | ●
Cache | | ●

Maintenant que nous avons déterminé s'il faut utiliser l'authentification multifacteur de cloud ou le serveur MFA local, nous pouvons commencer à configurer et à utiliser Azure Multi-Factor Authentication. **Sélectionnez l'icône qui représente votre scénario !**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

<!---HONumber=AcomDC_0928_2016-->