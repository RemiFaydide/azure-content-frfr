<properties
	pageTitle="Configuration d’Azure AD Join pour vos utilisateurs | Microsoft Azure"
	description="Explique comment les administrateurs peuvent configurer Azure AD Join pour l’inscription locale d’appareils et d’annuaires."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/27/2016"
	ms.author="femila"/>

# Configuration d’Azure AD Join dans votre organisation

Avant de configurer Azure Active Directory Join (Azure AD Join), vous devez synchroniser votre annuaire local d’utilisateurs avec le cloud ou créer manuellement les comptes gérés dans Azure AD.

Des instructions détaillées pour la synchronisation de vos utilisateurs locaux avec Azure AD sont disponibles dans [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).


Pour créer et gérer manuellement des utilisateurs dans Azure AD, reportez-vous à [Gestion des utilisateurs dans Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## Configuration de l’inscription des appareils
1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Dans le volet gauche, sélectionnez **Active Directory**.
3. Dans l’onglet **Annuaire**, sélectionnez votre annuaire.
4. Sélectionnez l'onglet **Configurer**.
5. Accédez à la section **Appareils**.
6. Dans l’onglet **Appareils**, définissez les éléments suivants :
   * **NOMBRE MAXIMAL D’APPAREILS PAR UTILISATEUR** : sélectionnez le nombre maximal d’appareils qu’un utilisateur peut avoir dans Azure AD. Si un utilisateur atteint ce quota, il ne sera pas en mesure d’ajouter des appareils tant qu’un ou plusieurs appareils existants n’auront pas été supprimés.
   * **EXIGER MULTI-FACTOR AUTH POUR JOINDRE DES APPAREILS** : indiquez si les utilisateurs doivent fournir un second facteur d’authentification pour joindre leurs appareils à Azure AD. Pour plus d’informations sur Azure Multi-Factor Authentication, consultez [Prise en main avec Azure Multi-Factor Authentication dans le cloud](..\multi-factor-authentication\multi-factor-authentication-get-started-cloud.md).
   * **LES UTILISATEURS PEUVENT JOINDRE DES APPAREILS À AZURE AD** : sélectionnez les utilisateurs et groupes autorisés à joindre des appareils à Azure AD.
   * **ADMINISTRATEURS SUPPLÉMENTAIRES SUR LES APPAREILS JOINTS À AZURE AD** : avec Azure AD Premium ou Enterprise Mobility Suite (EMS), vous pouvez choisir les utilisateurs qui bénéficient de droits d’administrateur local sur l’appareil. Les administrateurs globaux et les propriétaires des appareils bénéficient de droits d’administrateur local par défaut.

<center>![Configuration de l’inscription des appareils](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>

Après avoir configuré Azure AD Join pour vos utilisateurs, ces derniers peuvent se connecter à Azure AD via leurs appareils d’entreprise ou personnels.

Voici trois scénarios expliquant comment vous pouvez autoriser les utilisateurs à configurer Azure AD Join :

- Les utilisateurs joignent un appareil appartenant à l’entreprise directement à Azure AD.
- Les utilisateurs joignent au domaine un appareil appartenant à l’entreprise à l’annuaire Active Directory local et l’étendent à Azure AD.
- Les utilisateurs ajoutent des comptes professionnels ou scolaires à Windows sur un appareil personnel.

## Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->