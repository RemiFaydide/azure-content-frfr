<properties
	pageTitle="Inscription d’application v2.0 | Microsoft Azure"
	description="Inscription d’une application avec Microsoft pour l’activation de l’authentification et l’accès aux services Microsoft à l’aide du point de terminaison v2.0"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# Inscription d’une application avec le point de terminaison v2.0

Pour générer une application prenant en charge à la fois les connexions à MSA et Azure AD, vous devez d’abord l’inscrire auprès de Microsoft. Pour le moment, vous ne pouvez pas utiliser les applications existantes avec Azure AD ou MSA. Vous devez en créer une.

> [AZURE.NOTE]
	Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).

## Visiter le portail d’inscription des applications Microsoft
Commencez tout d’abord par accéder à [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com). Il s’agit d’un nouveau portail d’inscription des applications où vous pouvez gérer vos applications Microsoft.

Connectez-vous à l’aide d’un compte Microsoft personnel, professionnel ou scolaire. Si vous en êtes dépourvu, inscrivez-vous pour obtenir un nouveau compte personnel. Cela ne sera pas long. Nous patientons ici.

Vous avez terminé ? À présent, consultez votre liste d’applications Microsoft, qui est probablement vide. Nous allons y remédier.

Cliquez sur **Ajouter une application**, et attribuez-lui un nom. Le portail attribue à votre application un ID d’application global unique que vous utiliserez ultérieurement dans votre code. Si votre application inclut un composant côté serveur, qui nécessite des jetons d’accès pour appeler des API (à savoir Office, Azure ou votre propre API Web), créez également ici un **secret d’application**.
<!-- TODO: Link for app secrets -->

Ensuite, ajoutez les plateformes que votre application utilisera.

- Pour les applications basées sur le Web, fournissez une **URI de redirection** où les messages de connexion peuvent être envoyés.
- Pour les applications mobiles, copiez la valeur par défaut de l’URI de redirection créée automatiquement pour vous.

Si vous le souhaitez, vous pouvez personnaliser l’apparence de votre page de connexion dans la section Profil. Avant de continuer, veillez à cliquer sur **Enregistrer**.

> [AZURE.NOTE] Lorsque vous créez une application à l'aide de [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com), l’application est enregistrée dans le client de base du compte que vous utilisez pour vous connecter au portail. Cela signifie que vous ne pouvez pas inscrire une application dans votre client Azure AD à l’aide d’un compte Microsoft personnel. Si vous souhaitez réellement inscrire une application dans un client particulier, connectez-vous avec un compte créé à l’origine dans ce client.

## Générer une application de démarrage rapide
Maintenant que vous disposez d’une application Microsoft, vous pouvez suivre l’un de nos didacticiels de démarrage rapide de v2.0. Voici quelques recommandations :

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=AcomDC_0921_2016-->