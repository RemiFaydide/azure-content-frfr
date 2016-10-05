<properties 
	pageTitle="Meilleures pratiques pour l’utilisation d’Azure MFA"
	description="Ce document propose de meilleures pratiques à l’aide de l’authentification Multifacteur Azure avec des comptes Azure"
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

# Meilleures pratiques pour l’utilisation de l’authentification multifacteur Azure avec des comptes Azure AD

Lorsqu’il s’agit d’améliorer votre processus d’authentification, l’authentification multifacteur est le choix privilégié par la plupart des organisations. Azure multi-Factor Authentication (MFA) permet aux entreprises de satisfaire les exigences des sociétés en matière de sécurité et de conformité tout en fournissant une expérience de connexion facile pour leurs utilisateurs. Cet article décrit certaines meilleures pratiques que vous devez prendre en compte au moment de planifier l’adoption de l’authentification MFA.

## Meilleures pratiques pour Azure Multi-Factor Authentication dans le cloud
Pour fournir à tous vos utilisateurs des fonctions d’authentification multifacteur et tirer parti des fonctionnalités étendues qu’offre l’authentification multifacteur Azure, vous devez activer l’authentification multifacteur Azure sur tous vos utilisateurs. Pour ce faire, utilisez l’une des méthodes suivantes :

- Azure AD Premium ou la suite Enterprise Mobility Suite
- Fournisseur d’authentification multi facteurs

### Azure AD Premium/Enterprise Mobility Suite

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

La première étape recommandée pour l’adoption d’Azure MFA dans le cloud en utilisant Azure AD Premium ou Enterprise Mobility Suite consiste à attribuer des licences à vos utilisateurs. L’authentification multifacteur Azure fait partie de ces suites, et par conséquent votre organisation n’a pas besoin d’étendre la fonctionnalité d’authentification multifacteur à tous les utilisateurs.

Lors de la configuration de l’authentification multifacteur, prenez en compte les éléments suivants :

- Vous n’avez pas à créer de fournisseur d’authentification multifacteur. Azure Multi-Factor Authentication Azure AD Premium et Enterprise Mobility Suite sont intégrés à l’authentification multifacteurs Azure. Si vous créez un fournisseur d’authentification, vous pouvez être facturé deux fois.
- Azure AD Connect est une exigence uniquement si vous synchronisez votre environnement Active Directory local avec un répertoire Azure AD. Si vous utilisez uniquement un répertoire Azure AD non synchronisé avec une instance locale d’Active Directory, vous n’avez pas besoin d’Azure AD Connect.


### Fournisseur d’authentification multi facteurs

![Fournisseur d’authentification multi facteurs](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Si vous ne disposez pas d’Azure AD Premium ou d’Enterprise Mobility Suite la première étape recommandée pour adopter l’authentification Multifacteur Azure dans le cloud consiste à créer un multifacteur. Bien que l’authentification multifacteur soit disponible par défaut pour les administrateurs généraux disposant d’Azure Active Directory, lorsque vous déployez l’authentification Multifacteur pour votre organisation, vous devez l’étendre à tous les utilisateurs et pour ce faire, vous avez besoin d’un fournisseur d’authentification multifacteur. Lorsque vous sélectionnez le fournisseur d’authentification, vous devez sélectionner un répertoire et de prendre en compte les éléments suivants :

- Vous n'avez pas besoin d’un répertoire Azure AD pour créer un fournisseur Multi-Factor Auth.
- Vous devez associer le fournisseur d’authentification multifacteur à avec le répertoire AD si vous souhaitez que vos administrateurs généraux puissent tirer le meilleur parti de certaines fonctionnalités telles que le portail de gestion, les messages de bienvenue personnalisés et les rapports.
- DirSync ou AAD Sync sont nécessaires uniquement si vous synchronisez votre environnement Active Directory local avec un répertoire Azure AD. Si vous utilisez uniquement un répertoire Azure AD qui n'est pas synchronisé avec une instance locale d'Active Directory, DirSync ou AAD Sync est inutile.
- Si vous avez Azure AD Premium ou Enterprise Mobility Suite, il est inutile de créer un fournisseur Multi-Factor Auth. Il vous suffit d’attribuer une licence à un utilisateur, puis vous pouvez commencer à activer MFA pour les utilisateurs.
- Veillez à choisir le modèle d’utilisation convenant à votre entreprise (par authentification ou par utilisateur activé). Une fois que vous aurez sélectionné le modèle d’utilisation, vous ne pourrez plus en changer.

### Compte d’utilisateur
Lorsque vous activez l’authentification Multifacteur pour vos utilisateurs, les comptes d’utilisateurs peuvent être dans un des trois états principaux : désactivé, activé ou appliqué. Utilisez les instructions ci-dessous afin de vous assurer que vous utilisez bien l’option le mieux adaptée à votre déploiement :

- Lorsque l’état est défini sur désactivé, Un utilisateur dont l’état est défini sur désactivé n’utilise pas l’authentification multifacteur. Il s’agit de la valeur par défaut.
- Lorsque l’état est défini sur activé, cela signifie que l’utilisateur est activé mais que la procédure d’inscription n’est pas terminée. Il seront invités à terminer le processus lors de la prochaine connexion. Ce paramètre n’affecte pas les applications ne servant pas à la navigation. Toutes les applications continuent de fonctionner jusqu’à la fin du processus d’inscription.
- Lorsque l’état est défini sur appliqué, cela ne signifie pas forcément que l’utilisateur a achevé son enregistrement. S'ils ont terminé le processus d'inscription, ils utilisent donc l'authentification multifacteur. Sinon, l’utilisateur est invité à terminer la procédure lors de la prochaine connexion Ce paramètre n’affecte pas les applications autres que les navigateurs. Les applications ne fonctionnent pas tant que les mots de passe d’application ne sont ni créés ni utilisés.
- Utilisez le modèle de Notification utilisateur disponible dans l’article [Prise en main de l’authentification multifacteur Azure dans le cloud ](multi-factor-authentication-get-started-cloud.md) pour envoyer un courrier électronique au sujet de l’adoption de l’authentification Multifacteur à vos utilisateurs.

### Prise en charge

Dans la mesure où la grande majorité des utilisateurs sont habitués à utiliser uniquement les mots de passe pour s’authentifier, il est important que votre société sensibilise l’ensemble des utilisateurs à ce processus. Le fait de connaître le processus peut réduire la probabilité que les utilisateurs appellent votre support technique pour des problèmes mineurs liés à l’authentification Multifacteur. Toutefois, pour certains scénarios, il est nécessaire de désactiver provisoirement l’authentification Multifacteur. Suivez les indications ci-dessous pour comprendre comment gérer ces scénarios :

- Assurez-vous que le personnel de support technique est formé à la gestion de scénarios dans lesquels l’application mobile ou le téléphone ne reçoivent de notification ou ne passe pas d’appel téléphonique, et pour cette raison, l’utilisateur se trouve dans l’impossibilité de se connecter. Ils peuvent activer une option de contournement à usage unique qui permet à un utilisateur de s’authentifier une seule fois en « contournant » l’authentification multifacteur. Le contournement est temporaire et expire après le nombre de secondes spécifié.
- Si nécessaire, vous pouvez utiliser la fonctionnalité de fournisseurs d’identité approuvés dans Azure MFA. Cette fonction permet aux administrateurs d’un client géré ou fédéré de contourner l’authentification multifacteur des utilisateurs qui se connectent depuis l’intranet local de l’entreprise. Les fonctionnalités sont disponibles pour les clients Azure AD titulaires d’une licence Azure AD Premium, Enterprise Mobility Suite ou Azure Multi-Factor Authentication.


## Meilleures pratiques pour l’authentification multifacteur Azure locale
Si votre entreprise a décidé de se servir de sa propre infrastructure pour activer l’authentification Multifacteur, il sera nécessaire de déployer un serveur d’authentification multifacteur Azure localement. Les composants serveur de l’authentification Multifacteur figurent dans le diagramme ci-dessous :

![Fournisseur d’authentification multi facteurs](./media/multi-factor-authentication-security-best-practices/server.png) *N’est pas installé par défaut **installé, mais non activé par défaut


Le serveur d’authentification multifacteur Azure permet de sécuriser les ressources de cloud et des ressources locales qui sont accessibles via des comptes Azure AD. Toutefois, cette opération peut uniquement être effectuée à l’aide de fédération. En d’autres termes, vous devez disposer d’AD FS et le fédérer avec votre locataire Azure AD. Lors de la configuration de serveur d’authentification multifacteur, tenez compte de ce qui suit :

- Si vous sécurisez des ressources Azure AD à avec services de fédération Active Directory, le 1er facteur d’authentification est effectué sur site avec AD FS et le 2e, effectué localement en répondant à la demande.
- Le serveur Azure Multi-Factor Authentication ne doit pas nécessairement être installé sur votre serveur de fédération AD FS, toutefois, l'adaptateur d'authentification multifacteur pour AD FS doit être installé sur un serveur Windows Server 2012 R2 exécutant AD FS. Vous pouvez installer le serveur sur un autre ordinateur, tant qu'il s'agit d'une version prise en charge et installer l'adaptateur AD FS séparément sur votre serveur de fédération AD FS. Consultez la procédure ci-dessous pour obtenir des instructions sur l'installation séparée de l’adaptateur.
- L'assistant d’installation de l'adaptateur d’authentification multifacteur AD FS crée un groupe de sécurité appelé PhoneFactor Admins dans Active Directory, puis ajoute le compte de service AD FS de votre service de fédération à ce groupe. Il est recommandé de vérifier sur votre contrôleur de domaine que le groupe PhoneFactor Admins a bien créé et que le compte de service AD FS est membre de ce groupe. Si nécessaire, ajoutez manuellement le compte de service AD FS au groupe PhoneFactor Admins sur votre contrôleur de domaine.

### Portail de l'utilisateur
Ce portail s’exécute sur un site web Internet Information Server (IIS), qui autorise les fonctionnalités en libre-service et fournit un ensemble complet de fonctionnalités d’administration des utilisateurs. Utilisez les instructions ci-dessous pour configurer ce composant :

- IIS 6 ou version ultérieure est requis
- V2.0.507207 ASP.NET doit être installé et inscrit
- Ce serveur peut être déployé dans un réseau de périmètre.



### Mots de passe d'application
Si votre organisation est fédérée (SSO) avec Azure AD et que vous vous apprêtez à utiliser l’Authentication multifacteur Azure, voici ce que vous devez absolument savoir à propos de l’utilisation des mots de passe d’application.

- Le mot de passe est vérifié par Azure AD et il contourne ainsi la fédération. La fédération n’est utilisée activement que lorsque vous configurez le mot de passe d’application.
- Les mots de passe sont stockés dans l’id d’organisation. Si l'utilisateur quitte l'entreprise, ces informations doivent être stockées en temps réel dans l'id d'organisation à l'aide de DirSync. La désactivation/suppression de compte peut mettre jusqu'à 3 heures à se synchroniser, ce qui peut retarder la désactivation/suppression du mot de passe dans Azure AD.
- Les paramètres de contrôle d'accès client locaux ne sont pas honorés par Mot de passe d’application
- Aucune authentification locale de journalisation/fonctionnalité d’audit n'est disponible pour Mot de passe
- Pour utiliser le client Microsoft Lync 2013, l’utilisateur final doit être très qualifié.
- Certaines conceptions architecturales avancées peuvent nécessiter l’utilisation d'une combinaison de noms d'utilisateur, de mots de passe et de mots de passe d'application, lors de l'utilisation de l'authentification multifacteur avec les clients, selon l'emplacement de cette dernière. Pour les clients qui s’authentifient auprès d’une infrastructure locale, vous devez utiliser le nom d’utilisateur et le mot de passe d’une organisation. Pour les clients qui s'authentifient auprès d'Azure AD, vous utiliseriez le mot de passe d’application.
- Par défaut, les utilisateurs ne peuvent pas créer de mots de passe d’application. Si votre entreprise requiert que, ou si vous devez utiliser des utilisateurs à créer un mot de passe d’application dans certains scénarios, assurez-vous que l’option Autoriser des utilisateurs pour créer des mots de passe d’application pour vous connecter à des applications sans navigateur est sélectionné.

## Considérations supplémentaires
Utilisez la liste ci-dessous pour connaître certaines considérations supplémentaires, et les meilleures pratiques pour chaque composant qui sera déployé sur site :

Méthode|Description
:------------- | :------------- |
[Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md)|Informations sur la configuration d'Azure Multi-Factor Authentication avec AD FS.
[Authentification RADIUS](multi-factor-authentication-get-started-server-radius.md)| Informations sur l'installation et la configuration du serveur Azure MFA avec RADIUS.
[Authentification IIS](multi-factor-authentication-get-started-server-iis.md)|Informations sur l'installation et la configuration du serveur Azure MFA avec IIS.
[Authentification Windows](multi-factor-authentication-get-started-server-windows.md)| Informations sur l'installation et la configuration du serveur Azure MFA avec l’authentification Windows.
[Authentification LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informations sur l'installation et la configuration du serveur Azure MFA avec l’authentification LDAP.
[Passerelle des services Bureau à distance et serveur Azure Multi-Factor Authentication avec RADIUS](multi-factor-authentication-get-started-server-rdg.md)| Informations sur l'installation et la configuration du serveur Azure MFA avec Passerelle des services Bureau à distance et RADIUS.
[Synchronisation avec Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)|Informations sur l’installation et la configuration de la synchronisation entre Active Directory et le serveur Azure MFA.
[Déploiement du service Web de l’application mobile du serveur Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md)|Informations sur l'installation et la configuration du service Web du serveur Azure MFA.
[Configuration VPN avancée avec l’authentification multifacteurs Azure](multi-factor-authentication-advanced-vpn-configurations.md)|Informations sur la configuration d’appareils itrix Netscaler et Juniper/Pulse Secure VPN.


## Ressources supplémentaires
Bien que cet article mette en évidence quelques-unes des meilleures pratiques de l’authentification Multifacteur Azure, il existe d’autres ressources que vous pouvez également utiliser au moment de la planification de votre déploiement de l’authentification Multifacteur. La liste ci-dessous présente certains articles clés qui peuvent vous aider lors de ce processus<:

- [Rapports dans Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
- [Configuration de l’expérience Azure Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md)
- [Forum Aux Questions d’Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)

<!---HONumber=AcomDC_0921_2016-->