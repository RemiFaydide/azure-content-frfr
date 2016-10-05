<properties
	pageTitle="Rubriques d’aide du portail d’inscription des applications | Microsoft Azure"
	description="Description de différentes fonctionnalités dans le portail d’inscription des applications Microsoft."
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

# Informations de référence sur l’inscription des applications
Ce document fournit le contexte et les descriptions de différentes fonctionnalités figurant dans le portail d’inscription des applications Microsoft [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com).

## Mes applications
Cette liste contient toutes les applications inscrites pour une utilisation avec le point de terminaison Azure AD v2.0. Ces applications permettent de connecter des utilisateurs avec à la fois des comptes personnels Microsoft et des comptes professionnels/scolaires Azure Active Directory. Pour en savoir plus sur le point de terminaison Azure AD v2.0, consultez notre [présentation de v2.0](active-directory-appmodel-v2-overview.md). Ces applications peuvent également servir à l’intégration au point de terminaison d’authentification de compte Microsoft, `https://login.live.com`.

## Applications du Kit de développement logiciel (SDK) Live
Cette liste contient toutes les applications inscrites pour une utilisation uniquement avec un compte Microsoft. Elles ne peuvent pas être utilisées avec Azure Active Directory de quelque manière que ce soit. Il s’agit de l’emplacement où vous trouverez toutes les applications précédemment inscrites dans le portail des développeurs MSA sur `https://account.live.com/developers/applications`. Toutes les fonctions précédemment effectuées sur `https://account.live.com/developers/applications` peuvent maintenant être exécutées dans ce nouveau portail, `https://apps.dev.microsoft.com`. Si vous avez d’autres questions concernant les applications de votre compte Microsoft, veuillez nous contacter.

## Secrets de l’application
Les secrets de l’application sont des informations d’identification qui permettent à votre application d’effectuer une [authentification du client](http://tools.ietf.org/html/rfc6749#section-2.3) fiable avec Azure AD. Dans OAuth et OpenID Connect, les secrets d’une application sont généralement désignés sous le nom de `client_secret`. Dans le protocole v2.0, toute application qui reçoit un jeton de sécurité à un emplacement adressable web (à l’aide d’un schéma `https`) doit utiliser un secret d’application pour s’identifier auprès d’Azure AD lors de l’échange de ce jeton de sécurité. En outre, il sera interdit à tout client natif qui reçoit les jetons sur un appareil d’utiliser un secret d’application pour effectuer l’authentification du client, afin d’empêcher le stockage de secrets dans des environnements non sécurisés.

Chaque application peut contenir deux secrets d’application valides à un moment donné dans le temps. En gérant deux secrets, vous avez la possibilité d’effectuer une substitution périodique de clé dans l’ensemble de l’environnement de votre application. Une fois que vous avez migré l’intégralité de votre application vers un nouveau secret, vous pouvez supprimer l’ancien et en configurer un nouveau.

Actuellement, seuls deux types de secrets d’application sont autorisés dans le portail d’inscription des applications. L’option **Générer un nouveau mot de passe** permet de générer et stocker un secret partagé dans le magasin de données respectif, que vous pouvez utiliser dans votre application. L’option **Générer une nouvelle paire de clés** permet de créer une paire de clés publique/privée qui peut être téléchargée et utilisée pour l’authentification du client vers Azure AD.

## Profil
La section de profil du portail d’inscription des applications peut être utilisée pour personnaliser la page de connexion de votre application. Actuellement, vous pouvez modifier le logo de l’application de la page de connexion, les conditions d’utilisation de l’URL du service et la déclaration de confidentialité. Le logo doit être une image transparente de 48 x 48 ou 50 x 50 pixels dans un fichier GIF, PNG ou JPEG de 15 Ko au maximum. Essayez de modifier les valeurs et d’afficher la page de connexion obtenue !

## Support du Kit de développement logiciel (SDK) Live
Quand vous activez le support du Kit de développement logiciel (SDK) Live, les secrets d’application créés sont configurés à la fois dans les magasins de données Azure AD et de compte Microsoft. Cela permet à votre application d’être directement intégrée au service de compte Microsoft (login.live.com). Si vous voulez créer une application directement à l’aide d’un compte Microsoft (au lieu d’utiliser le point de terminaison Azure AD v2.0), vous devez vérifier que le support du Kit de développement logiciel (SDK) Live est activé.

La désactivation du support du Kit de développement logiciel (SDK) Live garantit que le secret d’application n’est écrit que dans le magasin de données Azure AD. Le magasin de données Azure AD incorpore des réglementations de qualité professionnelle qui lui permettent de répondre à certaines normes, telles que la conformité FISMA. Si vous activez le support du Kit de développement logiciel (SDK) Live, il est possible que votre application ne soit pas conforme à certaines de ces normes.

Si vous avez jamais envisagé d’utiliser le point de terminaison Azure AD v2.0, vous pouvez désactiver en toute sécurité le support du Kit de développement logiciel (SDK) Live.

<!---HONumber=AcomDC_0921_2016-->