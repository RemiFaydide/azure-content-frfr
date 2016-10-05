<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure"
	description="Une rubrique qui explique ce qu’est Azure AD Privileged Identity Management et comment utiliser PIM pour renforcer la sécurité de votre cloud."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="kgremban"/>

# Azure AD Privileged Identity Management

Avec Azure Active Directory (AD) Privileged Identity Management, vous pouvez gérer, contrôler et surveiller l’accès au sein de votre organisation. Cela inclut l’accès aux ressources dans Azure AD et d’autres services en ligne Microsoft comme Office 365 ou Microsoft Intune.

> [AZURE.NOTE] Privileged Identity Management est disponible uniquement avec l’édition Premium P2 d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

Les organisations veulent limiter le nombre de personnes qui ont accès aux informations ou aux ressources sécurisées afin de réduire le risque qu’un utilisateur malveillant accède à ces données. Mais utilisateurs doivent pouvoir toujours effectuer des opérations privilégiées dans les applications Azure, Office 365 ou SaaS. Les organisations doivent offrir un accès privilégié aux utilisateurs dans Azure AD sans avoir à surveiller ce que font les utilisateurs avec leurs privilèges d’administrateur. Azure AD Privileged Identity Management contribue à minimiser ce risque.

Azure AD Privileged Identity Management vous aide à :

- Identifier les utilisateurs qui ont un rôle d’administrateur dans Azure AD
- Activer à la demande un accès administrateur « juste à temps » aux services Microsoft Online Services comme Office 365 et Intune
- Obtenir des rapports sur l'historique des accès administrateur et sur les modifications apportées aux affectations de l'administrateur
- Recevoir des alertes sur l'accès à un rôle privilégié

Azure AD Privileged Identity Management peut gérer les rôles d'organisation intégrés dans Azure AD, notamment :

- Administrateur général
- Administrateur de facturation
- Administrateur de services
- Administrateur d'utilisateurs
- Administrateur de mots de passe

## Administrateur des accès immédiats

Jusqu’ici, vous pouviez affecter un rôle d’administrateur à un utilisateur via le portail Azure Classic ou Windows PowerShell. Cet utilisateur devient ainsi un **administrateur permanent**, toujours actif dans le rôle qui lui a été affecté. Azure AD Privileged Identity Management introduit le concept d**’administrateur éligible**. Les administrateurs éligibles doivent être des utilisateurs qui nécessitent un accès privilégié de temps à autres, mais pas tous les jours. Ce rôle reste inactif jusqu’à ce l’utilisateur ait besoin d’un tel accès ; dans ce cas, il complète un processus d’activation et devient administrateur actif pour une durée prédéterminée.

## Activer Privileged Identity Management pour votre répertoire

Vous pouvez commencer à utiliser Azure AD Privileged Identity Management dans le [portail Azure](https://portal.azure.com/).

>[AZURE.NOTE] Vous devez être un administrateur général avec un compte d’organisation (par exemple, @votredomaine.com) et non avec un compte Microsoft (par exemple, @outlook.com), pour activer Azure AD Privileged Identity Management sur un répertoire.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre répertoire.
2. Si votre organisation possède plusieurs répertoires, sélectionnez votre nom d’utilisateur dans le coin supérieur droit du portail Azure. Sélectionnez le répertoire où vous allez utiliser Azure AD Privileged Identity Management.
3. Sélectionnez **Plus de services** et utilisez la zone de texte Filtre pour rechercher **Azure AD Privileged Identity Management**.
4. Cochez **Épingler au tableau de bord**, puis cliquez sur **Créer**. L’application Privileged Identity Management s’ouvre.

Si vous êtes la première personne à utiliser Azure AD Privileged Identity Management dans votre répertoire, l’[Assistant Sécurité](active-directory-privileged-identity-management-security-wizard.md) vous guide tout au long de la procédure d’attribution initiale. Vous devenez alors automatiquement le premier **administrateur de la sécurité** et le premier **administrateur de rôle privilégié** du répertoire.

Seul un administrateur de rôle privilégié peut gérer l’accès des autres administrateurs. Vous pouvez [donner aux autres utilisateurs la capacité à gérer dans PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Tableau de bord de gestion des identités privilégiées

Azure AD Privileged Identity Manager fournit un tableau de bord qui contient des informations importantes telles que :

- Alertes indiquant des possibilités d’amélioration de la sécurité
- Le nombre d'utilisateurs affectés à chaque rôle privilégié
- Le nombre d'administrateurs éligibles et permanents
- Révisions en continu de l’accès

![Capture d’écran du tableau de bord PIM][2]

## Gestion des rôles privilégiés

Avec Azure AD Privileged Identity Management, vous pouvez gérer les administrateurs en ajoutant ou en supprimant des administrateurs permanents ou éligibles pour chaque rôle.

![Capture d’écran d’ajout et de suppression d’administrateurs dans PIM][3]

## Configurer les paramètres d'activation de rôle

À l’aide des [paramètres de rôle](active-directory-privileged-identity-management-how-to-change-default-settings.md), vous pouvez configurer les propriétés d’activation d’un rôle éligible, notamment :

- La durée de la période d’activation d’un rôle
- La notification d'activation d’un rôle
- Les informations qu'un utilisateur doit fournir au cours du processus d'activation du rôle

![Capture d’écran de l’activation d’administrateur dans les paramètres PIM][4]

Notez que dans l’image, les boutons de **l’authentification multifacteur** sont désactivés. Avec certains rôles dotés de privilèges élevés, l’authentification multifacteur est requise pour garantir une protection renforcée.

## Activation d’un rôle  

Pour [activer un rôle](active-directory-privileged-identity-management-how-to-activate-role.md), un administrateur éligible demande une « activation » limitée dans le temps concernant ce rôle. L'activation peut être demandée à l'aide de l’option **Activate my role** dans Azure AD Privileged Identity Management.

Un administrateur qui souhaite activer un rôle doit initialiser Azure AD Privileged Identity Management sur le portail Azure.

L’activation de rôles est personnalisable. Dans les paramètres de PIM, vous pouvez définir la durée de l’activation, ainsi que les informations que l’administrateur doit fournir pour activer le rôle.

![Capture d’écran de demande d’activation de rôle d’administrateur dans PIM][5]

## Passer en revue les activités de rôle

Il existe deux méthodes pour suivre la façon dont vos employés et les administrateurs utilisent les rôles privilégiés. La première option consiste à utiliser [l’historique d’audit](active-directory-privileged-identity-management-how-to-use-audit-log.md). L’historique d’audit consigne le suivi des modifications dans les affectations de rôles privilégiés et l’historique de l’activation de rôle.

![Capture d’écran de l’historique d’activation dans PIM][6]

La deuxième option consiste à configurer des [révisions d’accès](active-directory-privileged-identity-management-how-to-start-security-review.md) régulières. Ces révisions d’accès peuvent être effectuées par un réviseur affecté (par exemple, un gestionnaire d’équipe) ou par les employés eux-mêmes. C’est la méthode la plus adaptée pour déterminer les utilisateurs qui nécessitent l’accès et ceux qui n’en ont plus besoin.


## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0921_2016-->