<properties
	pageTitle="Identité hybride : Comparaison des outils d’intégration d’annuaire | Microsoft Azure"
	description="Cette page contient un tableau comparatif complet des différents outils d’intégration de répertoire pouvant être utilisés pour l’intégration de répertoire."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/08/2016"
	ms.author="billmath"/>

# Identité hybride : Comparaison des outils d’intégration d’annuaire

Au fil des années, les outils d’intégration de répertoire ont évolué et se sont améliorés. Ce document procure une vue d’ensemble consolidée de ces outils et compare les fonctions disponibles dans chacun d’entre eux.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Azure AD Connect comprend les composants et les fonctionnalités précédemment publiés sous le nom de synchronisation d’annuaires et AAD Sync. Ces outils ne sont plus commercialisés individuellement et toutes les améliorations à venir figureront dans les mises à jour d’Azure AD Connect, afin que vous sachiez toujours où obtenir les dernières fonctionnalités.
>
>DirSync et Azure AD Sync sont déconseillés. Des informations supplémentaires sont disponibles [ici](active-directory-aadconnect-dirsync-deprecated.md).


Le code suivant s’applique aux tableaux ci-dessous.

● = Disponible maintenant VU = Version ultérieure PP = Version préliminaire publique

## Synchronisation des ressources locales avec le cloud

| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory (AAD Sync) | Outil de synchronisation Azure Active Directory (DirSync)| Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Connexion à une forêt AD locale | ● | ● | ● | ● |● |
| Connexion à plusieurs forêts AD locales |● | ● | | ● |● |
| Connexion à plusieurs organisations Exchange locales | ● | | | | |
| Connexion à un répertoire LDAP local | FR | | | ● |● |
| Connexion à plusieurs répertoires LDAP |FR | | | ● |● |
| Connexion à des répertoires locaux AD et LDAP |FR | | | ● |● |
| Connexion à des systèmes personnalisés (comme SQL, Oracle, MySQL, etc.) | FR | | | ● |● |
| Synchronisation des attributs définis par le client (extensions de répertoire) | ● | | | | |
|Connexion à un système de ressources humaines local (par exemple, SAP, Oracle eBusiness, PeopleSoft)| FR | | | ● |● |
|Prise en charge des connecteurs et des règles de synchronisation FIM pour le déploiement sur des systèmes locaux.| | | | ● |● |

## Synchronisation du cloud avec les ressources locales

| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Écriture différée des appareils | ● | | ● | ||
| Écriture différée des attributs (pour un déploiement hybride Exchange) | ● | ● | ● | ● |● |
| Écriture différée des objets utilisateurs et groupes | ●| | | ||
| Écriture différée des mots de passe (à partir de la réinitialisation de mot de passe libre-service et de la modification de mot de passe) | ● | ● | | ||



## Prise en charge des fonctionnalités d’authentification

| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Synchronisation de mot de passe pour une forêt AD locale | ● | ● | ● | ||
| Synchronisation de mot de passe pour plusieurs forêts AD locales | ●| ● | | ||
| Authentification unique à l’aide de la fédération | ● | ● | ● | ● |● |
| Écriture différée des mots de passe (à partir de la réinitialisation de mot de passe libre-service et de la modification de mot de passe) |● | ● | | ||



## Configuration et installation

| Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Microsoft Identity Manager 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Installation des supports sur un contrôleur de domaine | ● | ● | ● | |
| Installation des supports à l’aide de SQL Express | ● | ● | ● | |
| Mise à niveau facile à partir de DirSync |● | | | |
| Localisation de l’expérience utilisateur admin en langues Windows Server | ● | ● | ● | |
|Localisation de l’expérience utilisateur final en langues Windows Server| | | |● |
| Prise en charge pour Windows Server 2008 et Windows Server 2008 R2 | ● pour la synchronisation, pas pour la fédération| ● | ● | ● |
| Prise en charge pour Windows Server 2012 et Windows Server 2012 R2 | ● | ● | ● | ● |

## Filtrage et configuration

Fonctionnalité | Azure Active Directory Connect | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM)| Microsoft Identity Manager 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Filtrage sur les domaines et les unités organisationnelles | ● | ● | ● | ● | ●
Filtrage sur les valeurs d’attributs des objets | ● | ● | ● | ●| ●
Autorisation d’un ensemble minimal d’attributs à synchroniser (MinSync) | ● | ● | ||
Autorisation de différents modèles de service à appliquer pour les flux d’attributs |● | ● | ||
Autorisation du blocage des attributs circulant d’AD vers Azure AD | ● | ● | | |
Autorisation de la personnalisation avancée pour les flux d’attributs | ● | ● | | ● | ●

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0928_2016-->