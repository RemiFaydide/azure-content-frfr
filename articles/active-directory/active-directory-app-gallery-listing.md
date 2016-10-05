<properties
   pageTitle="Affichage de votre application dans la galerie d’applications Azure AD"
   description="Comment répertorier une application qui prend en charge l'authentification unique dans la galerie Azure Active Directory | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>


# Affichage de votre application dans la galerie d’applications Azure AD

Pour répertorier une application qui prend en charge l'authentification unique avec Azure Active Directory dans la [galerie Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), l'application doit tout d'abord mettre en œuvre l'un des modes d'intégration suivants :

* **OpenID Connect** : intégration directe dans Azure AD à l'aide d'OpenID Connect pour l'authentification et l'API de consentement Azure AD pour la configuration. Si vous débutez une intégration et que votre application ne prend pas en charge SAML, il s'agit du mode de recommandé.

* **SAML** : votre application a déjà la possibilité de configurer des fournisseurs d'identité tiers utilisant le protocole SAML.

Les exigences pour chaque mode sont indiquées ci-dessous.

##Intégration d'OpenID Connect

Pour intégrer votre application dans Azure AD, suivez les [instructions pour développeurs](active-directory-authentication-scenarios.md). Puis répondez aux questions ci-dessous et adressez vos réponses à waadpartners@microsoft.com.

* Fournissez des informations d'identification pour votre application pour un locataire ou un compte de test pouvant être utilisées par l'équipe Azure AD pour tester l'intégration.

* Fournissez des instructions sur la manière dont l'équipe Azure AD peut se connecter et connecter une instance d'Azure AD à votre application à l'aide de l'[infrastructure de consentement d'Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework).

* Fournissez toute instruction supplémentaire nécessaire pour permettre à l'équipe Azure AD de tester l'authentification unique avec votre application.

* Fournissez les informations ci-dessous :

> Nom de l’entreprise :
> 
> Site web de l’entreprise :
> 
> Nom de l’application :
> 
> Description de l’application (256 caractères maximum) :
> 
> Site web de l’application (pour informatif) :
> 
> Site web du support technique de l’application ou les informations de contact :
> 
> ID de client de l’application, comme indiqué dans les détails de l’application à https://manage.windowsazure.com :
> 
> URL d’inscription d’application à laquelle les clients accèdent pour s’inscrire et/ou acheter l’application :
> 
> Sélectionnez jusqu’à trois catégories répertoriées pour votre application (pour connaître les catégories disponibles, consultez l'Azure Active Directory Marketplace) :
> 
> Attacher une petite icône d’application (fichier PNG, 45 px par 45 px, couleur d’arrière-plan unie) :
> 
> Attacher une grande icône d’application (fichier PNG, 215 px par 215 px, couleur d’arrière-plan unie) :
> 
> Attacher un grand logo d’application (fichier PNG, 150 px par 122 px, couleur d’arrière-plan unie) :

##Intégration de SAML

Toute application prenant en charge SAML 2.0 peut être intégrée directement dans un locataire Azure AD à l'aide de [ces instructions pour ajouter une application personnalisée](active-directory-saas-custom-apps.md). Une fois que vous avez testé que l'intégration de votre application fonctionne avec Azure AD, envoyez les informations suivantes à l'adresse <waadpartners@microsoft.com>.

* Fournissez des informations d'identification pour votre application pour un locataire ou un compte de test pouvant être utilisées par l'équipe Azure AD pour tester l'intégration.

* Fournissez l'URL de connexion SAML, l'URL de l'émetteur (ID d'entité) et l'URL de réponse (Assertion Consumer Service) pour votre application, comme indiqué [ici](active-directory-saas-custom-apps.md). Si vous fournissez généralement ces valeurs dans un fichier de métadonnées SAML, envoyez également ce dernier.

* Fournissez une brève description de la configuration d'Azure AD comme fournisseur d'identité dans votre application à l'aide de SAML 2.0. Si votre application prend en charge la configuration d'Azure AD comme fournisseur d'identité via un portail d'administration en libre-service, assurez-vous que les informations d'identification fournies vous le permettent.

* Fournissez les informations ci-dessous :

> Nom de l’entreprise :
> 
> Site web de l’entreprise :
> 
> Nom de l’application :
> 
> Description de l’application (256 caractères maximum) :
> 
> Site web de l’application (pour informatif) :
> 
> Site web du support technique de l’application ou les informations de contact :
> 
> URL d’inscription d’application à laquelle les clients accèdent pour s’inscrire et/ou acheter l’application :
> 
> Sélectionnez jusqu'à trois catégories à répertorier pour votre application sous (pour connaître les catégories disponibles, consultez le site [Marketplace Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/)) :
> 
> Attacher une petite icône d’application (fichier PNG, 45 px par 45 px, couleur d’arrière-plan unie) :
> 
> Attacher une grande icône d’application (fichier PNG, 215 px par 215 px, couleur d’arrière-plan unie) :
> 
> Attacher un grand logo d’application (fichier PNG, 150 px par 122 px, couleur d’arrière-plan unie) :

<!---HONumber=AcomDC_0921_2016-->