<properties
   pageTitle="Intégration à Azure Active Directory | Microsoft Azure"
   description="Un guide sur les avantages et les ressources de l'intégration avec Azure Active Directory."
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

# Intégration avec Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure Active Directory offre aux organisations la gestion des identités professionnelles pour les applications cloud. L’intégration d'Azure AD offre à vos utilisateurs une expérience de connexion rationalisée et permet à votre application d’être conforme à la stratégie informatique.

## Processus d’intégration

Il existe plusieurs façons d’intégrer votre application à Azure AD. Bénéficiez d’autant de ces scénarios nécessaires à votre application.

### Prise en charge d'Azure AD pour de se connecter à votre application

**Réduction des problèmes de connexion et réduction des coûts de prise en charge.** En utilisant Azure AD pour se connecter à votre application, vos utilisateurs n'auront pas un nom et un mot de passe supplémentaires à mémoriser. En tant que développeur, vous aurez un mot de passe en moins à stocker et à protéger. Ne pas avoir à gérer les réinitialisations de mots de passe oubliés peut être en soi une économie considérable. Azure AD alimente les connexions pour certaines des applications cloud les plus populaires au monde, y compris Office 365 et Microsoft Azure. Avec des centaines de millions d'utilisateurs dans des millions d'organisations, il est fort probable que votre utilisateur soit déjà connecté à Azure AD. En savoir plus sur [l’ajout de la prise en charge pour la connexion Azure AD](active-directory-authentication-scenarios.md).

**Simplifiez l’inscription à votre application.** Lors de l'inscription de votre application, Azure AD peut envoyer des informations essentielles sur un utilisateur pour vous permettre de remplir au préalable votre formulaire d'inscription ou de le supprimer complètement. Les utilisateurs peuvent s’inscrire à votre application à l'aide de leur compte Azure AD via une expérience de consentement courante, similaire à celles des réseaux sociaux et des applications mobiles. N'importe quel utilisateur peut s'inscrire et se connecter à une application qui est intégrée à Azure AD sans avoir besoin de l’intervention des services informatiques. En savoir plus sur l’[inscription de votre application pour la connexion d’un compte Azure AD](../mobile-services/mobile-services-how-to-register-active-directory-authentication.md).

### Recherche des utilisateurs, gestion de l’attribution de privilèges d’accès des utilisateurs et contrôle de l’accès à votre application

**Rechercher des utilisateurs dans le répertoire.** Utilisez l'API Graph pour la recherche d’utilisateurs et recherchez d'autres personnes dans leur organisation lorsque vous invitez d'autres personnes ou que vous leur permettez l’accès, au lieu de leur demander de taper leur adresse e-mail. Les utilisateurs peuvent effectuer leur recherche dans une interface similaire à un carnet d’adresses, notamment en affichant les détails de la hiérarchie organisationnelle. En savoir plus sur l’[API Graph](active-directory-graph-api.md).

**Réutilisation des groupes Active Directory et des listes de distribution déjà gérées par votre client.** Azure AD contient les groupes que votre client utilise déjà pour la distribution d’e-mail et la gestion des accès. À l'aide de l'API Graph, réutiliser ces groupes au lieu de demander à votre client de créer et de gérer un ensemble de groupes distinct dans votre application. Les informations sur les groupes peuvent également être envoyées à votre application dans les jetons de connexion. En savoir plus sur l’[API Graph](active-directory-graph-api.md).

**Utilisation d’Azure AD pour contrôler quels utilisateurs ont accès à votre application.** Les administrateurs et les propriétaires d’applications dans Azure AD peuvent attribuer l’accès aux applications à des utilisateurs et des groupes spécifiques. À l'aide de l'API Graph, vous pouvez lire cette liste et l'utiliser pour contrôler l’attribution de privilèges d’accès et la suppression de privilèges d’accès pour les ressources et l’accès à votre application.

**Utilisation d’Azure AD pour les rôles en fonction d’Access Control.** Les administrateurs et les propriétaires d'applications peuvent attribuer des utilisateurs et des groupes aux rôles que vous définissez lorsque vous enregistrez votre application dans Azure AD. Les informations sur les rôles sont envoyées à votre application dans des jetons de connexion et peuvent également être lues à l'aide de l'API Graph. En savoir plus sur l’[utilisation d'Azure AD pour les autorisations](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### Accéder au profil de l'utilisateur, au calendrier, aux e-mails, aux contacts, aux fichiers et plus encore

**Azure AD est le serveur d'autorisation pour Office 365 et d'autres services métiers de Microsoft.** Si vous prenez en charge Azure AD pour la connexion à votre application ou si vous prenez en charge la liaison de vos comptes d'utilisateur actuels aux comptes d'utilisateur Azure AD à l'aide d'OAuth 2.0, vous pouvez demander l'accès en lecture et en écriture à un profil utilisateur, un calendrier, des e-mails, des contacts, des fichiers et d’autres informations. Vous pouvez écrire des événements en toute transparence dans le calendrier de l'utilisateur et lire ou écrire des fichiers dans leur OneDrive. En savoir plus sur l’[accès aux API Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### Promotion de votre application dans les Marketplace d’Azure et d’Office 365

**Promouvez votre application aux millions d'organisations qui utilisent déjà Azure AD.** Les utilisateurs qui recherchent ces marketplace utilisent déjà un ou plusieurs services cloud, ce qui les définit en tant que clients de service cloud. En savoir plus sur la promotion de votre application dans [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Lorsque des utilisateurs s’inscrivent à votre application, cela apparaîtra dans leur panneau d'accès Azure AD et le lanceur d'applications Office 365.** Les utilisateurs pourront rapidement et facilement retourner à votre application plus tard, ce qui améliore ainsi l’engagement utilisateur. En savoir plus sur le [panneau d'accès Azure AD](active-directory-saas-access-panel-introduction.md).

### Sécurisation de la communication de périphérique à service et de service à service

**L’utilisation d'Azure AD pour la gestion des identités des services et des périphériques réduit le code que vous devez écrire et permet au service informatique de gérer les accès.** Les services et périphériques peuvent obtenir des jetons d'Azure AD à l'aide d'OAuth et utiliser ces jetons pour accéder aux API web. Grâce à Azure AD, vous pouvez éviter d'écrire du code d'authentification complexe. Étant donné que les identités des services et des périphériques sont stockées dans Azure AD, le service informatique peut gérer les clés et la révocation dans un même endroit plutôt que de faire cela séparément dans votre application.

## Avantages de l'intégration

L’intégration à Azure AD est fournie avec des avantages qui ne nécessitent pas d'écrire du code supplémentaire.

### Intégration avec la gestion des identités d'entreprise

**Aidez votre application à se conformer aux stratégies informatiques.** Les organisations intègrent leurs systèmes de gestion des identités d’entreprise à Azure AD, donc si une personne quitte une organisation, elle perdra automatiquement accès à votre application sans que le service informatique ait besoin de prendre des mesures supplémentaires. Le service informatique peut gérer quels utilisateurs peuvent accéder à votre application et déterminer quelles sont les stratégies d'accès requises (par exemple, une authentification multifacteur), réduisant ainsi la nécessité d'écrire du code pour se conformer aux stratégies d'entreprise complexes. Azure AD fournit aux administrateurs un journal d'audit détaillé des utilisateurs qui se sont connectés à votre application, ce qui permet au service informatique d’effectuer un suivi de l’utilisation.

**Azure AD étend Active Directory au cloud afin que votre application puisse être intégrée à Active Directory.** De nombreuses organisations dans le monde utilisent Active Directory comme leur principal système de gestion des identités et des connexions et ont besoin que leurs applications fonctionnent avec Active Directory. L’intégration avec Azure AD permet d’intégrer votre application à Active Directory.

### Fonctionnalités de sécurité avancées

**Authentification multifacteur** Azure AD fournit une authentification multifacteur native. Les administrateurs informatiques peuvent avoir besoin d'une authentification multifacteur pour accéder à votre application afin que vous n'ayez pas à écrire le code vous-même. En savoir plus sur l’[authentification multifacteur](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Détection de connexion anormale** Azure AD traite plus d'un milliard de connexions par jour, tout en utilisant des algorithmes d'apprentissage automatique pour détecter des activités suspectes et signaler tout problème éventuel aux administrateurs informatiques. En prenant en charge la connexion à Azure AD, votre application bénéficie de cette protection. En savoir plus sur l’[affichage des rapports d'accès Azure Active Directory](active-directory-view-access-usage-reports.md).

**Accès conditionnel.** Outre l'authentification multifacteur, les administrateurs peuvent exiger que les utilisateurs remplissent certaines conditions avant qu’ils puissent se connecter à votre application. Les conditions qui peuvent être définies incluent la plage d'adresses IP d’un périphérique client, l'appartenance à des groupes spécifiques et l'état du périphérique utilisé pour l'accès. En savoir plus sur l’[accès conditionnel d’Azure Active Directory](active-directory-conditional-access.md).

### Développement facile :

**Protocoles standard de l’industrie.** Microsoft s'engage à prendre en charge les normes industrielles. Azure AD prend en charge les protocoles d'authentification SAML 2.0, OpenID Connect 1.0, OAuth 2.0, et WS-Federation 1.2 L'API Graph est conforme à OData 4.0. Si votre application prend déjà en charge les protocoles SAML 2.0 ou OpenID Connect 1.0 pour la connexion fédérée, l’ajout de la prise en charge par Azure AD peut être facile. En savoir plus sur les [protocoles d'authentification pris en charge par Azure AD](active-directory-authentication-protocols.md).

**Bibliothèques open source.** Microsoft fournit les bibliothèques open source entièrement prises en charge pour les langages et plateformes courants afin d’accélérer le développement. Le code source est acquis sous licence sous Apache 2.0 et vous êtes libre de répliquer et de contribuer aux projets. En savoir plus sur les [bibliothèques d'authentification Azure AD](active-directory-authentication-libraries.md).

### Présence dans le monde entier et haute disponibilité

**Azure AD est déployé dans des centres de données dans le monde entier et est géré et surveillé en permanence.** Azure AD est le système de gestion des identités de Microsoft Azure et Office 365 et est déployé dans 28 centres de données dans le monde entier. Les données d’Active Directory sont garanties pour être répliquées sur au moins trois centres de données. Les équilibrages de charge globaux garantissent aux utilisateurs un accès à la copie la plus fidèle d'Azure AD contenant leurs données. Ils retransmettent automatiquement les demandes vers d’autres centres de données si un problème est détecté.

## Étapes suivantes

[Commencer à écrire du code](active-directory-developers-guide.md#getting-started).

[Connexion des utilisateurs à l'aide d'Azure AD](active-directory-authentication-scenarios.md)

<!---HONumber=AcomDC_0921_2016-->