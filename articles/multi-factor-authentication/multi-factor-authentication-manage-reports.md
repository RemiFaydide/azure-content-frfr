<properties 
	pageTitle="Rapports Azure Multi-Factor Authentication"
	description="Cette section décrit comment utiliser la fonctionnalité de rapports d’Azure Multi-Factor Authentication."
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

# Rapports dans Azure Multi-Factor Authentication

Azure Multi-Factor Authentication fournit plusieurs rapports qui peuvent être utilisés par vous et votre organisation. Ces rapports sont accessibles par le biais du portail de gestion Multi-Factor Authentication. Celui-ci exige un fournisseur Azure MFA ou une licence Azure MFA, Azure AD Premium ou Enterprise Mobility Suite. Voici la liste des rapports disponibles.

Vous pouvez accéder à ces rapports via le portail de gestion d’Azure.

Name| Description
:------------- | :------------- |
Utilisation | Les rapports d'utilisation affichent des informations sur l'utilisation générale, des récapitulatifs par utilisateurs et des détails sur les utilisateurs.
État du serveur|Ce rapport affiche l'état des serveurs Multi-Factor Authentication associés à votre compte.
Historique de l'utilisateur bloqué|Ces rapports présentent l'historique des demandes de blocage ou déblocage des utilisateurs.
Historique de l'utilisateur contourné|Affiche l'historique des demandes de contournement de Multi-Factor Authentication pour le numéro de téléphone d'un utilisateur.
Alerte de fraude|Affiche un historique des alertes de fraude soumises au cours de la plage de dates spécifiée.
Mis en file d'attente.|Répertorie les rapports en file d'attente de traitement et leur état. Un lien pour télécharger ou afficher le rapport est fourni lorsque ce dernier est terminé.

## Pour afficher des rapports

1.	Connectez-vous à http://azure.microsoft.com.
2.	Sélectionnez à gauche Active Directory.
3.	Sélectionnez l’une des options suivantes :
	- **Option 1** : cliquez sur l’onglet Fournisseurs d’authentification multifacteur. Sélectionnez votre fournisseur MFA et cliquez sur le bouton Gérer dans la partie inférieure.
	- **Option 2** : sélectionnez votre annuaire et cliquez sur l’onglet Configurer. Dans la section Authentification multifacteur, sélectionnez Gérer les paramètres du service. En bas de la page Paramètres du service MFA, cliquez sur le lien Accéder au portail.
4.	Dans le portail de gestion Azure Multi-Factor Authentication, la section Afficher un rapport apparaît dans le volet de navigation gauche. Ici, vous pouvez sélectionner les rapports décrits ci-dessus.

<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Ressources supplémentaires**

* [Pour les utilisateurs](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication sur MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)

<!---HONumber=AcomDC_0921_2016-->