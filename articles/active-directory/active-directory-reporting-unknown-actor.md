<properties
   pageTitle="Événement « Acteur inconnu»  de la création de rapports Azure Active Directory | Microsoft Azure"
   description="Description de l’événement « Acteur inconnu » des rapports Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="SSalahAhmed"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="saah"/>

# Événement « Acteur inconnu » lors de la création de rapports Azure Active Directory

*Cette documentation fait partie du guide [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Dans de rares occasions, les champs « Acteur » ou « Utilisateur » des rapports Azure AD peuvent contenir des valeurs inhabituelles. Ce comportement est normal. Il est dû à l’un des deux événements suivants :

## Un principal de service agit sur l’annuaire, sans contexte utilisateur

Dans ce cas, un principal de service (application) effectue les mises à jour sans réellement ouvrir de session en tant qu’utilisateur. L’ID du principal de service s’affiche en tant qu’acteur et non pas comme nom UPN. Voici un exemple :

![](./media/active-directory-reporting-unknown-actor/spd-actor.png)

Il s’agit d’un bogue connu et nous travaillons à sa résolution.

## Un utilisateur a été supprimé de l’annuaire avant que l'événement ait été traité.

Dans ce cas, un utilisateur a été supprimé de l’annuaire avant que nous ayons traité l’événement et l’ayons associé à un nom d’utilisateur. Voici un exemple :

![](./media/active-directory-reporting-unknown-actor/unknown-actor.png)

Il s’agit d’un bogue connu et nous travaillons à sa résolution.

<!-- ![](./media/active-directory-reporting-unknown-actor/uid-actor.png) -->

<!---HONumber=AcomDC_0921_2016-->