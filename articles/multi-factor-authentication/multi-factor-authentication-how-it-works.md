<properties 
	pageTitle="Azure Multi-Factor Authentication : fonctionnement"
	description="Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple. Il fournit une sécurité supplémentaire en exigeant un deuxième formulaire d'authentification et fournit une authentification renforcée via un éventail d'options de vérification simples."
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

#Azure Multi-Factor Authentication : fonctionnement

La sécurité de l'authentification multi-facteur repose sur son approche en couche. Compromettre plusieurs facteurs d'authentification présente un défi de taille pour les attaquants. Même si un attaquant réussit à connaître le mot de passe de l'utilisateur, ce dernier est inutile sans posséder l'appareil de confiance. Si l'utilisateur perd l'appareil, la personne qui l'a trouvé ne pourra pas l'utiliser sans connaître le mot de passe de l'utilisateur.

![Vérification](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple. Il fournit une sécurité supplémentaire en exigeant un deuxième formulaire d'authentification et fournit une authentification renforcée via un éventail d'options de vérification simples :

- appel téléphonique
- SMS
- notification sur l’application mobile, ce qui permet aux utilisateurs de choisir leur méthode préférée
- code de vérification sur l’application mobile
- jetons OATH tiers

Pour plus d'informations sur le fonctionnement, regardez la vidéo suivante.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##Méthodes disponibles pour l'authentification multifacteur
Lorsqu'un utilisateur se connecte, une vérification supplémentaire lui est envoyée. Voici la liste des méthodes qui peuvent être utilisées pour cette seconde vérification.

Méthode de vérification | Description
------------- | ------------- |
Appel téléphonique | Un appel est passé sur le smartphone de l’utilisateur demandant de vérifier qu'il se connecte en appuyant sur le symbole #. Cela va finaliser le processus de vérification. Cette option est configurable et peut être remplacée par un code que vous spécifiez.
SMS | Un SMS sera envoyé sur le smartphone de l’utilisateur avec un code à 6 chiffres. Entrez ce code pour finaliser le processus de vérification.
Notification sur l’application mobile | Une demande de vérification sera envoyée sur le smartphone de l'utilisateur lui indiquant de terminer la vérification en sélectionnant Vérifier depuis l'application mobile. Cela se produit si vous avez sélectionné la notification sur application comme méthode de vérification principale. Si l’utilisateur reçoit cette notification lorsqu'il ne se connecte pas, il peut choisir de la signaler comme fraude.
Code de vérification avec l’application mobile | Un code de vérification sera envoyé sur l'application mobile qui s'exécute sur le smartphone de l’utilisateur. Cela se produit si vous avez sélectionné un code de vérification comme méthode de vérification principale.


##Versions disponibles d’Azure Multi-Factor Authentication
Azure Multi-Factor Authentication est disponible en trois versions. Le tableau ci-dessous décrit chacune de ces versions en détail.

Version | Description
------------- | ------------- |
Authentification multi-facteur pour Office 365 | Cette version fonctionne exclusivement avec les applications Office 365 et est gérée à partir du portail Office 365. De ce fait, les administrateurs peuvent désormais sécuriser leurs ressources Office 365 avec une authentification multifacteur. Cette version est fournie avec un abonnement à Office 365.
Authentification multi-facteur pour administrateurs Azure | Le même sous-ensemble de fonctionnalités d'authentification multi-facteur pour Office 365 est disponible sans frais à tous les administrateurs Azure. Tout compte administratif possédant un abonnement Azure peut désormais bénéficier d'une protection supplémentaire en activant cette fonctionnalité principale d'authentification multi-facteur. Ainsi, un administrateur qui souhaite accéder au portail Azure afin de créer une machine virtuelle, un site Web, gérer le stockage, les services mobiles ou tout autre service Azure peut ajouter une authentification multi-facteur à son compte d'administrateur.
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication offre un riche éventail de fonctionnalités. <br><br>Il fournit des options de configuration supplémentaires via le portail de gestion Azure, des fonctions de rapports avancées et la prise en charge d’une sélection d’applications locales et dans le cloud. Azure Multi-Factor Authentication est disponible à l’achat dans le cadre d’une licence autonome. Il est aussi regroupé avec Azure Active Directory Premium et Enterprise Mobility Suite. <br><br>Vous pouvez également l’acheter dans le cadre d’un modèle basé sur la consommation en créant un fournisseur Azure Multi-Factor Authentication dans un abonnement Azure.
##Comparaison des fonctionnalités suivant les versions
Le tableau suivant fournit la liste des fonctionnalités qui sont disponibles dans les différentes versions d’Azure Multi-Factor Authentication.


Fonctionnalité | Multi-Factor Authentication pour Office 365 (fourni dans les SKU Office 365)|Multi-Factor Authentication pour les administrateurs Azure (fourni avec un abonnement Azure) | Azure Multi-Factor Authentication (fourni dans Azure AD Premium et Enterprise Mobility Suite)
------------- | :-------------: |:-------------: |:-------------: |
Les administrateurs peuvent protéger les comptes avec MFA| * | * (Disponible uniquement pour les comptes d'administrateur Azure)|*
Application mobile comme second facteur|* | * | *
Appel téléphonique comme second facteur|* | * | *
SMS comme second facteur|* | * | *
Mots de passe d'application pour les clients qui ne prennent pas en charge MFA|* | * | *
Contrôle d'administration sur les méthodes d'authentification| *| *| *
Mode du code PIN| | | *
Alerte de fraude| | | *
Rapports MFA| | | *
Contournement à usage unique| | | *
Messages de bienvenue personnalisés pour les appels téléphoniques| | | *
Personnalisation de l'ID d'appelant pour les appels téléphoniques| | | *
Confirmation d’événement| | | *
Adresses IP approuvées| | | *
Suspendre MFA pour les appareils mémorisés (aperçu public)| | | *
SDK MFA| | | *
MFA pour des applications locales utilisant le serveur MFA| | | *


##Comment obtenir Azure Multi-Factor Authentication ?

Si vous souhaitez tirer parti de l’ensemble des fonctionnalités offertes par Azure Multi-Factor Authentication, et non vous limiter à celles fournies aux utilisateurs d’Office 365 et administrateurs Azure, plusieurs options s’offrent à vous :

1.	Achetez des licences Azure Multi-Factor Authentication et attribuez-les à vos utilisateurs.
2.	Achetez des licences regroupant Azure Multi-Factor Authentication (comme Azure Active Directory Premium ou Enterprise Mobility Suite) et attribuez-les à vos utilisateurs.
3.	Créez un fournisseur Azure Multi-Factor Authentication dans un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez vous inscrire pour souscrire à un abonnement Azure d’évaluation. Les abonnements d’évaluation doivent être convertis en abonnements standard avant leur date d’expiration.

Quand vous utilisez un fournisseur Azure Multi-Factor Authentication, vous avez le choix entre deux modèles d’utilisation qui sont facturés dans le cadre de votre abonnement Azure :


- **Par utilisateur**. Généralement, pour les entreprises qui veulent activer l'authentification multifacteur pour un nombre fixe d'employés qui s'authentifient régulièrement.
- **Par authentification**. Généralement, pour les entreprises qui veulent activer l'authentification multifacteur pour un nombre important d’utilisateurs externes qui s'authentifient ponctuellement.

Pour plus d’informations sur la tarification, consultez la [tarification d’Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Choisissez le modèle qui convient le mieux à votre organisation : celui basé sur le nombre d’utilisateurs ou celui basé sur la consommation. Puis, pour la prise en main, reportez-vous à [Mise en route](multi-factor-authentication-get-started.md)

<!---HONumber=AcomDC_0921_2016-->