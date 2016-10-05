
<properties
   pageTitle="Scénarios d’authentification pour Azure AD | Microsoft Azure"
   description="Vue d’ensemble des cinq scénarios d’authentification les plus courants pour Azure Active Directory (AAD)"
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

# Scénarios d’authentification pour Azure AD

Azure Active Directory (Azure AD) simplifie l’authentification pour les développeurs en fournissant l’identité en tant que service, avec la prise en charge des protocoles standard tels que OAuth 2.0 et OpenID Connect, ainsi que des bibliothèques open source pour différentes plateformes afin de vous permettre de commencer à coder rapidement. Ce document a pour but de vous aider à comprendre les différents scénarios pris en charge par Azure AD et vous montre comment prendre l’application en main. Il comprend les sections suivantes :

- [Principes fondamentaux de l’authentification dans Azure AD](#basics-of-authentication-in-azure-ad)

- [Revendications des jetons de sécurité Azure AD](#claims-in-azure-ad-security-tokens)

- [Principes fondamentaux de l’inscription d’une application dans Azure AD](#basics-of-registering-an-application-in-azure-ad)

- [Types d’application et scénarios](#application-types-and-scenarios)

  - [Navigateur web vers application web](#web-browser-to-web-application)

  - [Application à page unique (SPA)](#single-page-application-spa)

  - [Application native vers API web](#native-application-to-web-api)

  - [Application web vers API web](#web-application-to-web-api)

  - [Application démon ou serveur vers API web](#daemon-or-server-application-to-web-api)



## Principes fondamentaux de l’authentification dans Azure AD

Si vous ne connaissez pas les concepts de base de l’authentification dans Azure AD, lisez cette section. Sinon, vous pouvez l’ignorer et passer directement à la section [Types d’application et scénarios](#application-types-and-scenarios).

Prenons l’exemple du scénario le plus élémentaire dans lequel une identité est requise : un utilisateur doit s’authentifier auprès d’une application web dans un navigateur web. Ce scénario est décrit plus en détail dans la section [Navigateur web vers application web](#web-browser-to-web-application), mais c’est un bon point de départ pour illustrer les fonctionnalités d’Azure AD et conceptualiser le fonctionnement du scénario. Considérez le diagramme suivant pour ce scénario :

![Vue d’ensemble de l’authentification des applications web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Voici ce que vous devez savoir sur les divers composants du diagramme ci-dessus :

- Azure AD est le fournisseur d’identité responsable de la vérification de l’identité des utilisateurs et applications de l’annuaire d’une organisation et, en fin de compte, de l’émission des jetons de sécurité après l’authentification correcte de ces utilisateurs et applications.


- Pour pouvoir externaliser l’authentification en la confiant à Azure AD, une application doit être inscrite dans Azure AD, qui l’inscrit et l’identifie de manière unique dans l’annuaire.


- Les développeurs peuvent utiliser les bibliothèques d’authentification open source d’Azure AD pour simplifier l’authentification en gérant les détails du protocole pour vous. Pour plus d’informations, consultez la rubrique [Bibliothèques d’authentification d’Azure Active Directory](active-directory-authentication-libraries.md).


• Une fois qu’un utilisateur a été authentifié, l’application doit valider son jeton de sécurité pour s’assurer que l’authentification a réussi pour les parties concernées. Les développeurs peuvent utiliser les bibliothèques d’authentification fournies pour gérer la validation d’un jeton d’Azure AD, y compris les jetons Web JSON (JWT) ou SAML 2.0. Pour effectuer la validation manuellement, consultez la documentation sur le [gestionnaire de jeton JWT](https://msdn.microsoft.com/library/dn205065.aspx).


> [AZURE.IMPORTANT] Azure AD utilise le chiffrement à clé publique pour signer les jetons et vérifier leur validité. Consultez la rubrique [Informations importantes sur la substitution des clés de signature dans Azure AD](active-directory-signing-key-rollover.md) pour plus d’informations sur la logique dont vous devez disposer dans votre application pour vous assurer qu’elle est systématiquement mise à jour avec les clés les plus récentes.


• Le flux de demandes et réponses du processus d’authentification est déterminé par le protocole d’authentification utilisé, par exemple OAuth 2.0, OpenID Connect, WS-Federation ou SAML 2.0. Ces protocoles sont présentés plus en détail dans la rubrique [Protocoles d’authentification d’Azure Active Directory](active-directory-authentication-protocols.md) et dans les sections ci-dessous.

> [AZURE.NOTE] Azure AD prend en charge les normes OAuth 2.0 et OpenID Connect, qui utilisent massivement les jetons porteurs, y compris des jetons porteurs représentés sous forme de JWT. Un jeton porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le « porteur » désigne toute partie qui peut présenter le jeton. Une partie doit certes d’abord s’authentifier auprès d’Azure AD pour recevoir le jeton porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons porteurs n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton porteur est transmis en clair, une partie malveillante peut utiliser une attaque d’intercepteur afin de s’approprier le jeton et de l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée. Pour en savoir plus sur les aspects de sécurité des jetons porteurs, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).


Maintenant que vous avez une vue d’ensemble des principes fondamentaux, lisez les sections ci-dessous pour comprendre comment l’approvisionnement fonctionne dans Azure AD et les scénarios courants qu’Azure AD prend en charge.


## Revendications des jetons de sécurité Azure AD

Les jetons de sécurité émis par Azure AD contiennent des revendications, ou assertions d’informations, sur le sujet qui a été authentifié. Ces revendications peuvent être utilisées par l’application pour différentes tâches. Par exemple, elles peuvent servir à valider le jeton, identifier le client du domaine du sujet, afficher les informations des utilisateurs, déterminer l’autorisation du sujet, etc. Les revendications présentes dans un jeton de sécurité dépendent du type de jeton, du type d’informations d’identification utilisées pour authentifier l’utilisateur et de la configuration de l’application. Une brève description de chaque type de revendication émise par Azure AD est fournie dans le tableau ci-dessous. Pour plus d’informations, consultez la rubrique [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md).


| Revendication | Description |
|-------|-------------|
| ID de l'application | Identifie l’application qui utilise le jeton.
| Public ciblé | Identifie la ressource de destination du jeton. |
| Référence de classe du contexte d’authentification de l’application | Indique comment le client a été authentifié (client public par opposition à client confidentiel). |
| Moment d’authentification | Enregistre la date et l’heure de l’authentification. |
| Méthode d’authentification | Indique comment le sujet du jeton a été authentifié (mot de passe, certificat, etc.). |
| Prénom | Fournit le prénom de l’utilisateur tel qu’il est défini dans Azure AD. |
| Groupes | Contient les ID objets des groupes Azure AD dont l’utilisateur est membre. |
| Fournisseur d’identité | Enregistre le fournisseur d’identité qui a authentifié le sujet du jeton. |
| Émis à | Enregistre l’heure à laquelle le jeton a été émis, souvent utilisée pour l’actualisation du jeton. |
| Émetteur | Identifie le service d’émission de jeton de sécurité qui a émis le jeton, ainsi que le client Azure AD. |
| Nom | Fournit le nom de l’utilisateur tel qu’il est défini dans Azure AD. |
| Nom | Fournit une valeur contrôlable de visu qui identifie le sujet du jeton. |
| ID objet | Contient un identificateur unique non modifiable du sujet dans Azure AD. |
| contrôleur | Contient les noms conviviaux des rôles d’application Azure AD qui ont été affectés à l’utilisateur. |
| Scope | Indique les autorisations accordées à l’application cliente. |
| Objet | Indique le principal sur lequel portent les assertions d’informations du jeton. |
| ID client | Contient un identificateur unique non modifiable du client de l’annuaire qui a émis le jeton. |
| Durée de vie du jeton | Définit l’intervalle de temps pendant lequel un jeton est valide. |
| Nom d’utilisateur principal | Contient le nom d’utilisateur principal du sujet. |
| Version | Contient le numéro de version du jeton. |


## Principes fondamentaux de l’inscription d’une application dans Azure AD

Toute application qui externalise l’authentification pour la confier à Azure AD doit être inscrite dans un annuaire. Cette étape consiste à donner des informations sur votre application à Azure AD, y compris son URL, l’URL à laquelle les réponses doivent être envoyées après l’authentification, l’URI permettant d’identifier votre application, etc. Ces informations sont obligatoires pour les raisons principales suivantes :

- Azure AD a besoin de coordonnées pour communiquer avec l’application lors de l’authentification ou de l’échange de jetons. Elles comprennent notamment les éléments suivants :

  - URI ID d’application : identificateur d’une application. Cette valeur est envoyée à Azure AD lors de l’authentification afin d’indiquer pour quelle application l’appelant souhaite un jeton. Cette valeur est également incluse dans le jeton pour que l’application sache qu’il s’agit de la cible prévue.


  - URL de réponse et URI de redirection : pour une API web ou une application web, l’URL de réponse correspond à l’emplacement auquel Azure AD envoie la réponse d’authentification, avec un jeton si l’authentification a réussi. Pour une application native, l’URI de redirection est un identificateur unique vers lequel Azure AD redirige l’agent utilisateur dans une requête OAuth 2.0.


  - ID client : ID d’une application généré par Azure AD au moment de l’inscription de l’application. Lors d’une demande de code d’autorisation ou de jeton, l’ID client et la clé sont envoyés à Azure AD lors de l’authentification.


  - Clé : clé envoyée avec un ID client lors de l’authentification auprès d’Azure AD en vue d’appeler une API web.


- Azure AD doit assurer que l’application dispose des autorisations nécessaires pour accéder à vos données d’annuaire, à d’autres applications de votre organisation, etc.

L’approvisionnement devient plus clair lorsque vous comprenez qu’il existe deux catégories d’applications que vous pouvez développer et intégrer avec Azure AD :

- Application à client unique : une application à client unique est prévue pour une utilisation dans une organisation. Il s’agit généralement d’applications métiers écrites par un développeur d’entreprise. Une application à client unique doit être accessible uniquement aux utilisateurs d’un annuaire et, en conséquence, ne doit être approvisionnée que dans un seul annuaire. Ces applications sont généralement inscrites par un développeur de l’organisation.


- Application mutualisée : une application mutualisée est prévue pour une utilisation dans plusieurs organisations, pas une seule. Il s’agit généralement d’applications SaaS (software-as-a-service) écrites par un éditeur de logiciels indépendant. Les applications mutualisées doivent être approvisionnées dans chaque annuaire dans lequel elles sont utilisées, ce qui suppose le consentement d’un utilisateur ou d’un administrateur pour les inscrire. Ce processus de consentement démarre quand une application a été enregistrée dans l’annuaire et accède à l’API Graph ou à une autre API web. Lorsqu’un utilisateur ou un administrateur d’une autre organisation s’inscrit pour utiliser l’application, une boîte de dialogue contenant les autorisations que l’application requiert s’affiche. L’utilisateur ou l’administrateur peut alors donner son consentement à l’application, ce qui permet à cette dernière d’accéder aux données indiquées et inscrit l’application dans l’annuaire de l’utilisateur ou de l’administrateur. Pour plus d’informations, consultez la page [Vue d’ensemble de l’infrastructure de consentement](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Vous devez tenir compte d’autres éléments lorsque vous choisissez de développer une application mutualisée plutôt qu’une application à client unique. Par exemple, si vous mettez votre application à la disposition des utilisateurs dans plusieurs annuaires, vous devez disposer d’un mécanisme permettant de déterminer dans quel client ils se trouvent. Il suffit à une application à client unique de rechercher l’utilisateur dans son propre annuaire, alors qu’une application mutualisée doit tenir compte de tous les annuaires d’Azure AD pour identifier un utilisateur particulier. À cet effet, Azure AD fournit un point de terminaison d’authentification commun vers lequel une application mutualisée peut diriger les demandes de connexion, plutôt que vers un point de terminaison spécifique au client. Ce point de terminaison est https://login.microsoftonline.com/common pour tous les annuaires Azure AD, tandis qu’un point de terminaison propre à un client peut être https://login.microsoftonline.com/contoso.onmicrosoft.com. Lorsque vous développez votre application, il est particulièrement important de tenir compte du point de terminaison commun, car vous aurez besoin de la logique nécessaire à la gestion de plusieurs clients lors de la connexion, de la déconnexion et de la validation des jetons.

Si vous développez actuellement une application à client unique, mais que vous souhaitez la mettre à disposition de plusieurs organisations, vous pouvez facilement apporter des modifications à l’application et à sa configuration dans Azure AD pour la rendre compatible avec la mutualisation. De plus, Azure AD utilise la même clé de signature pour tous les jetons de tous les annuaires, que vous fournissiez l’authentification dans une application à client unique ou mutualisée.

Chaque scénario répertorié dans ce document inclut une sous-section décrivant les exigences d’approvisionnement associées. Pour obtenir des informations plus détaillées sur l’approvisionnement d’une application dans Azure AD et sur les différences entre les applications à client unique et les applications mutualisées, consultez [Intégration d’applications dans Azure Active Directory](active-directory-integrating-applications.md). Poursuivez votre lecture pour comprendre les scénarios d’application courants dans Azure AD.

## Types d’application et scénarios

Chacun des scénarios décrits dans ce document peut être développé à l'aide de différents langages et plates-formes. Ils sont tous soutenus par des exemples de code complets disponibles dans notre [guide d’exemples de code](active-directory-code-samples.md), ou directement à partir des [référentiels d'exemples Github](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory) correspondants. De plus, si votre application nécessite un élément ou segment spécifique d’un scénario de bout en bout, vous pouvez ajouter cette fonctionnalité séparément dans la plupart des cas. Par exemple, si vous avez une application native qui appelle une API web, vous pouvez facilement ajouter une application web qui appelle elle-aussi l’API web. Le diagramme suivant illustre ces scénarios et types d’application, ainsi que la manière dont vous pouvez ajouter les différents composants :

![Types d’application et scénarios](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Voici les cinq principaux scénarios d’application pris en charge par Azure AD :

- [Navigateur web vers application web](#web-browser-to-web-application) : un utilisateur doit se connecter à une application web sécurisée par Azure AD.

- [Application à page unique](#single-page-application-spa) : un utilisateur doit se connecter à une application à page unique sécurisée par Azure AD.

- [Application native vers API web](#native-application-to-web-api) : une application native qui s’exécute sur un téléphone, une tablette ou un PC doit authentifier un utilisateur pour obtenir des ressources d’une API web sécurisée par Azure AD.

- [Application web vers API web](#web-application-to-web-api) : une application web doit obtenir des ressources d’une API web sécurisée par Azure AD.

- [Application démon ou serveur vers API web](#daemon-or-server-application-to-web-api) : une application démon ou une application serveur sans interface utilisateur web doit obtenir des ressources d’une API web sécurisée par Azure AD.

### Navigateur web vers application web

Cette section décrit une application authentifiant un utilisateur d’un navigateur web auprès d’une application web. Dans ce scénario, l’application web dirige le navigateur de l’utilisateur pour connecter ce dernier à Azure AD. Azure AD renvoie une réponse de connexion via le navigateur de l’utilisateur, celle-ci contenant des revendications relatives à l’utilisateur dans un jeton de sécurité. Ce scénario prend en charge l’authentification à l’aide des protocoles WS-Federation, SAML 2.0 et OpenID Connect.


#### Diagramme
![Flux d’authentification pour le scénario du type navigateur vers application web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### Description du flux du protocole


1. Quand un utilisateur accède à l’application et doit se connecter, il est redirigé via une demande de connexion au point de terminaison d’authentification d’Azure AD.


2. L’utilisateur se connecte sur la page de connexion.


3. Si l’authentification réussit, Azure AD crée un jeton d’authentification et renvoie une réponse de connexion à l’URL de réponse de l’application qui a été configurée dans le portail de gestion Azure. Pour une application de production, cette URL de réponse doit être au format HTTPS. Le jeton renvoyé inclut des revendications sur l’utilisateur et Azure AD dont l’application a besoin pour valider le jeton.


4. L’application valide le jeton à l’aide d’une clé de signature publique et des informations sur l’émetteur disponibles dans le document de métadonnées de fédération pour Azure AD. Une fois que l’application a validé le jeton, Azure AD démarre une nouvelle session avec l’utilisateur. Cette session permet à l’utilisateur d’accéder à l’application jusqu’à expiration de la session.


#### Exemples de code


Consultez les exemples de code pour les scénarios du type navigateur web vers application web. Et consultez cette page régulièrement : nous ajoutons de nouveaux exemples en permanence. [Navigateur web vers application web](active-directory-code-samples.md#web-browser-to-web-application).


#### Inscription


- Application à client unique : si vous créez une application uniquement pour votre organisation, vous devez l’inscrire dans l’annuaire de votre entreprise à l’aide du portail de gestion Azure.


- Application mutualisée : si vous créez une application qui peut être utilisée par des utilisateurs externes, vous devez l’inscrire dans l’annuaire de votre entreprise, mais également dans celui de chaque organisation qui utilisera l’application. Afin de mettre votre application à disposition dans ces annuaires, vous pouvez inclure pour vos clients un processus d’inscription qui leur permet de donner leur consentement à votre application. Quand ils s’inscrivent auprès de votre application, une boîte de dialogue contenant les autorisations requises par l’application s’affiche, et ils ont ensuite la possibilité de donner leur consentement. Selon les autorisations requises, il est possible qu’un administrateur de l’autre organisation doive donner le consentement. Une fois le consentement donné par l’utilisateur ou l’administrateur, l’application est inscrite dans l’annuaire de l’organisation de l’utilisateur ou de l’administrateur. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](active-directory-integrating-applications.md).


#### Expiration du jeton

La session utilisateur expire lorsque la durée de vie du jeton émis par Azure AD arrive à expiration. Votre application peut raccourcir cette durée au besoin, par exemple en déconnectant les utilisateurs suite à une période d’inactivité. Lorsque la session arrive à expiration, l’utilisateur est invité à se connecter à nouveau.





### Application à page unique (SPA)

Cette section décrit l’authentification pour une application à page unique utilisant Azure AD et l’octroi d’autorisation implicite OAuth 2.0 afin de sécuriser les composants principaux de son API web. Les applications à page unique sont généralement structurées comme une couche présentation (frontale) JavaScript qui s’exécute dans le navigateur et comme les composants principaux d’une API web qui s’exécute sur un serveur et implémente la logique métier de l’application. Pour plus d’informations sur l’octroi d’autorisation implicite et pour vous aider à décider si cette méthode est adaptée à votre scénario d’application, consultez [Comprendre le flux d’octroi implicite OAuth2 dans Azure Active Directory (AD)](active-directory-dev-understanding-oauth2-implicit-grant.md).

Dans ce scénario, quand l’utilisateur se connecte, le JavaScript frontal utilise [Active Directory Authentication Library pour JavaScript (ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) et l’octroi d’autorisation implicite pour obtenir un jeton d’ID (id\_token) d’Azure AD. Le jeton est mis en cache, et le client l’attache à la demande en tant que jeton porteur lors de l’appel des composants principaux de son API web, qui sont sécurisés à l’aide de l’intergiciel OWIN.
#### Diagramme

![Diagramme Application à page unique (SPA)](./media/active-directory-authentication-scenarios/single_page_app.png)

#### Description du flux du protocole

1. L’utilisateur accède à l’application web.


2. L’application renvoie le JavaScript frontal (couche présentation) au navigateur.


3. L’utilisateur établit la connexion, par exemple en cliquant sur un lien de connexion. Le navigateur envoie une commande GET au point de terminaison d’autorisation Azure AD pour demander un jeton d’ID. Les paramètres de requête de cette demande comprennent l’ID client et l’URL de réponse.


4. Azure AD valide l’URL de réponse par rapport à celle enregistrée et configurée dans le portail de gestion Azure.


5. L’utilisateur se connecte sur la page de connexion.


6. Si l’authentification réussit, Azure AD crée un jeton d’ID et le renvoie à l’URL de réponse de l’application sous forme de fragment d’URL (#). Pour une application de production, cette URL de réponse doit être au format HTTPS. Le jeton renvoyé inclut des revendications sur l’utilisateur et Azure AD dont l’application a besoin pour valider le jeton.


7. Le code client JavaScript exécuté dans le navigateur extrait le jeton de la réponse afin de l’utiliser pour la sécurisation des appels vers les composants principaux de l’API web de l’application.


8. Le navigateur appelle les composants principaux de l’API web de l’application, avec le jeton d’accès dans l’en-tête d’autorisation.

#### Exemples de code


Consultez les exemples de code pour les scénarios du type application à page unique (SPA). Veillez à consulter cette page régulièrement : nous ajoutons de nouveaux exemples en permanence. [Application à page unique (SPA)](active-directory-code-samples.md#single-page-application-spa).


#### Inscription


- Application à client unique : si vous créez une application uniquement pour votre organisation, vous devez l’inscrire dans l’annuaire de votre entreprise à l’aide du portail de gestion Azure.


- Application mutualisée : si vous créez une application qui peut être utilisée par des utilisateurs externes, vous devez l’inscrire dans l’annuaire de votre entreprise, mais également dans celui de chaque organisation qui utilisera l’application. Afin de mettre votre application à disposition dans ces annuaires, vous pouvez inclure pour vos clients un processus d’inscription qui leur permet de donner leur consentement à votre application. Quand ils s’inscrivent auprès de votre application, une boîte de dialogue contenant les autorisations requises par l’application s’affiche, et ils ont ensuite la possibilité de donner leur consentement. Selon les autorisations requises, il est possible qu’un administrateur de l’autre organisation doive donner le consentement. Une fois le consentement donné par l’utilisateur ou l’administrateur, l’application est inscrite dans l’annuaire de l’organisation de l’utilisateur ou de l’administrateur. Pour plus d'informations, consultez [Intégration d'applications dans Azure Active Directory](active-directory-integrating-applications.md).

Après avoir inscrit l’application, vous devez la configurer pour qu’elle utilise le protocole d’octroi implicite OAuth 2.0. Par défaut, ce protocole est désactivé pour les applications. Pour activer le protocole d’octroi implicite OAuth2 pour votre application, téléchargez son manifeste d’application à partir du portail de gestion Azure, définissez la valeur « oauth2AllowImplicitFlow » sur True, puis téléchargez le manifeste à nouveau sur le portail. Pour obtenir des instructions détaillées, consultez la rubrique [Activation de l’octroi implicite OAuth 2.0 pour les applications à page unique](active-directory-integrating-applications.md).


#### Expiration du jeton

Lorsque vous utilisez ADAL.js pour gérer l’authentification avec Azure AD, vous bénéficiez de plusieurs fonctionnalités qui facilitent l’actualisation d’un jeton arrivé à expiration, ainsi que l’obtention de jetons pour les ressources API web supplémentaires que l’application peut appeler. Lorsque l’utilisateur s’authentifie avec succès auprès d’Azure AD, une session sécurisée par un cookie est établie pour l’utilisateur entre le navigateur et Azure AD. Il est important de noter que la session existe entre l’utilisateur et Azure AD et non entre l’utilisateur et l’application web exécutée sur le serveur. Lorsqu’un jeton arrive à expiration, ADAL.js utilise cette session pour obtenir un autre jeton en mode silencieux. Pour cela, il utilise un iFrame masqué pour l’envoi et la réception de la demande à l’aide du protocole d’octroi implicite OAuth. ADAL.js peut également utiliser ce mécanisme afin d’obtenir des jetons d’accès d’Azure AD en mode silencieux pour les autres ressources de l’API web que l’application appelle, à condition que ces ressources prennent en charge le partage des ressources cross-origin (CORS) et soient inscrites dans l’annuaire de l’utilisateur, et que l’utilisateur ait donné le consentement requis au moment de la connexion.


### Application native vers API web


Cette section décrit l’appel d’une API web par une application native au nom d’un utilisateur. Ce scénario repose sur le type d’octroi de code d’autorisation OAuth 2.0 avec un client public, comme décrit à la section 4.1 de la [spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749). L’application native obtient un jeton d’accès pour l’utilisateur à l’aide du protocole OAuth 2.0. Ce jeton d’accès est ensuite envoyé via la demande à l’API web, qui autorise l’utilisateur et renvoie la ressource souhaitée.

#### Diagramme

![Diagramme Application native vers API web](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### Flux d’authentification pour le scénario du type application native vers API web

#### Description du flux du protocole


Si vous utilisez les bibliothèques d’authentification AD, la plupart des détails de protocole décrits ci-dessous sont gérés pour vous, notamment les fenêtres contextuelles du navigateur, la mise en cache des jetons et la gestion des jetons d’actualisation.

1. À l’aide d’une fenêtre contextuelle du navigateur, l’application native envoie une demande au point de terminaison d’autorisation d’Azure AD. Cette demande comprend l’ID client et l’URI de redirection de l’application native, tels qu’ils figurent dans le portail de gestion, et l’URI ID d’application de l’API web. Si l’utilisateur ne s’est pas déjà connecté, il est invité à nouveau à se connecter.


2. Azure AD authentifie l’utilisateur. S’il s’agit d’une application mutualisée et si un consentement est requis pour utiliser l’application, l’utilisateur doit donner son consentement, s’il ne l’a pas déjà fait. Une fois le consentement donné et l’authentification réussie, Azure AD renvoie une réponse de code d’autorisation à l’URI de redirection de l’application cliente.


3. Quand Azure AD renvoie une réponse de code d’autorisation à l’URI de redirection, l’application cliente arrête l’interaction du navigateur et extrait le code d’autorisation de la réponse. À l’aide de ce code d’autorisation, l’application cliente envoie une demande au point de terminaison de jeton d’Azure AD. Celle-ci comprend le code d’autorisation, des détails sur l’application cliente (ID client et URI de redirection) et la ressource souhaitée (URI ID d’application de l’API web).


4. Le code d’autorisation et les informations sur l’application cliente et l’API web sont validés par Azure AD. Si la validation réussit, Azure AD renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT. Azure AD renvoie également des informations de base sur l’utilisateur, telles que son nom d’affichage et son ID client.


5. Sur HTTPS, l’application cliente utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec la mention « porteur » dans l’en-tête d’autorisation de la demande adressée à l’API web. L’API web valide ensuite le jeton JWT et, si la validation réussit, renvoie la ressource souhaitée.


6. Quand le jeton d’accès arrive à expiration, l’application cliente reçoit une erreur indiquant que l’utilisateur doit s’authentifier à nouveau. Si l’application dispose d’un jeton d’actualisation valide, celui-ci peut être utilisé pour obtenir un nouveau jeton d’accès sans demander à l’utilisateur de se connecter à nouveau. Si le jeton d’actualisation arrive à expiration, l’application doit interactivement authentifier l’utilisateur une nouvelle fois.


> [AZURE.NOTE] Le jeton d’actualisation émis par Azure AD peut être utilisé pour accéder à plusieurs ressources. Par exemple, si une application cliente est autorisée à appeler deux API web, le jeton d’actualisation peut être utilisé pour obtenir un jeton d’accès à l’autre API web également.


#### Exemples de code


Consultez les exemples de code pour les scénarios du type application native vers API web. Et consultez cette page régulièrement : nous ajoutons de nouveaux exemples en permanence. [Application native vers API web](active-directory-code-samples.md#native-application-to-web-api).


#### Inscription


- Application à client unique : l’application native et l’API web doivent être inscrites dans le même annuaire dans Azure AD. L’API web peut être configurée pour exposer un ensemble d’autorisations utilisées pour limiter l’accès de l’application native à ses ressources. L’application cliente sélectionne ensuite les autorisations souhaitées dans le menu déroulant « Autorisations pour d’autres applications » du portail de gestion Azure.


- Application mutualisée : premièrement, l’application native est toujours inscrite dans l’annuaire du développeur ou de l’éditeur. Deuxièmement, l’application native est configurée pour indiquer les autorisations dont elle a besoin pour fonctionner. Cette liste d’autorisations requises s’affiche dans une boîte de dialogue quand un utilisateur ou un administrateur de l’annuaire de destination donne son consentement à l’application, ce qui la met à disposition de son organisation. Certaines applications nécessitent uniquement des autorisations utilisateur, pour lesquelles tous les utilisateurs de l’organisation peuvent donner leur consentement. D’autres nécessitent des autorisations administrateur, pour lesquelles un utilisateur de l’organisation ne peut pas donner son consentement. Seul un administrateur d’annuaires peut donner son consentement aux applications qui requièrent des autorisations de ce niveau. Quand un utilisateur ou un administrateur donne son consentement, seule l’API web est inscrite dans son annuaire. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](active-directory-integrating-applications.md).


#### Expiration du jeton


Quand l’application native utilise son code d’autorisation pour obtenir un jeton d’accès JWT, elle reçoit également un jeton d’actualisation JWT. À l’expiration du jeton d’accès , le jeton d’actualisation peut être utilisé pour authentifier l’utilisateur à nouveau sans lui demander de se connecter une nouvelle fois. Ce jeton d’actualisation est ensuite utilisé pour authentifier l’utilisateur, ce qui donne lieu à la création d’un nouveau jeton d’accès et d’un nouveau jeton d’actualisation.





### Application web vers API web


Cette section décrit une application web ayant besoin d’obtenir des ressources d’une API web. Dans ce scénario, il existe deux types d’identité que l’application web peut utiliser pour authentifier et appeler l’API web : une identité d’application ou une identité d’utilisateur délégué.

*Identité d’application* : ce scénario utilise l’octroi d’informations d’identification client OAuth 2.0 pour l’authentification en tant qu’application et l’accès à l’API web. Avec une identité d’application, l’API web peut détecter uniquement que l’application web l’appelle, car elle ne reçoit aucune information sur l’utilisateur. Si l’application reçoit des informations sur l’utilisateur, celles-ci sont envoyées via le protocole d’application et ne sont pas signées par Azure AD. L’API web suppose que l’application web a authentifié l’utilisateur. C’est pour cette raison que ce modèle est appelé « sous-système approuvé ».

*Identité d’utilisateur délégué* : ce scénario peut être obtenu de deux façons : OpenID Connect et octroi de code d’autorisation OAuth 2.0 avec un client confidentiel. L’application web obtient un jeton d’accès pour l’utilisateur, ce qui prouve à l’API web que l’utilisateur s’est correctement authentifié auprès de l’application web et que l’application web a pu obtenir une identité d’utilisateur délégué pour appeler l’API web. Ce jeton d’accès est envoyé via la demande à l’API web, qui autorise l’utilisateur et renvoie la ressource souhaitée.

#### Diagramme

![Diagramme Application web vers API web](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### Description du flux du protocole

L’identité d’application et l’identité d’utilisateur délégué sont décrites dans le flux ci-après. La différence principale entre ces deux types d’identité est que l’identité d’utilisateur délégué doit obtenir un code d’autorisation avant que l’utilisateur ne puisse se connecter et accéder à l’API web.

##### Identité d’application avec octroi d’informations d’identification client OAuth 2.0

1. Un utilisateur est connecté à Azure AD dans l’application web (voir la section [Navigateur web vers application web](#web-browser-to-web-application) ci-dessus).


2. L’application web doit obtenir un jeton d’accès pour pouvoir s’authentifier auprès de l’API web et extraire la ressource souhaitée. Elle envoie une demande au point de terminaison de jeton d’Azure AD, avec les informations d’identification, l’ID client et l’URI ID d’application de l’API web.


3. Azure AD authentifie l’application et renvoie un jeton d’accès JWT, qui est utilisé pour appeler l’API web.


4. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec la mention « porteur » dans l’en-tête d’autorisation de la demande adressée à l’API web. L’API web valide ensuite le jeton JWT et, si la validation réussit, renvoie la ressource souhaitée.

##### Identité d’utilisateur délégué avec OpenID Connect

1. Un utilisateur est connecté à une application web via Azure AD (voir la section [Navigateur web vers application web](#web-browser-to-web-application) ci-dessus). Si l’utilisateur de l’application web n’a pas encore consenti à autoriser l’application web à appeler l’API web en son nom, il doit donner son consentement. L’application affiche les autorisations nécessaires et, si certaines d’entre elles sont des autorisations administrateur, un utilisateur normal de l’annuaire ne peut pas donner le consentement. Ce processus de consentement s’applique uniquement aux applications mutualisées, pas aux applications à client unique, car l’application a déjà les autorisations nécessaires. Quand l’utilisateur s’est connecté, l’application web a reçu un jeton d’ID avec les informations sur l’utilisateur, ainsi qu’un code d’autorisation.


2. À l’aide du code d’autorisation émis par Azure AD, l’application web envoie une demande au point de terminaison de jeton d’Azure AD. Celle-ci comprend le code d’autorisation, des détails sur l’application cliente (ID client et URI de redirection) et la ressource souhaitée (URI ID d’application de l’API web).


3. Le code d’autorisation et les informations sur l’application web et l’API web sont validés par Azure AD. Si la validation réussit, Azure AD renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT.


4. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec la mention « porteur » dans l’en-tête d’autorisation de la demande adressée à l’API web. L’API web valide ensuite le jeton JWT et, si la validation réussit, renvoie la ressource souhaitée.

##### Identité d’utilisateur délégué avec octroi du code d’autorisation OAuth 2.0

1. Un utilisateur est déjà connecté à une application web, dont le mécanisme d’authentification est indépendant d’Azure AD.


2. L’application web a besoin d’un code d’autorisation pour obtenir un jeton d’accès et adresse donc une demande au point de terminaison d’autorisation d’Azure AD via le navigateur, en indiquant l’ID client et l’URI de redirection de l’application web une fois l’authentification réussie. L’utilisateur se connecte à Azure AD.


3. Si l’utilisateur de l’application web n’a pas encore consenti à autoriser l’application web à appeler l’API web en son nom, il doit donner son consentement. L’application affiche les autorisations nécessaires et, si certaines d’entre elles sont des autorisations administrateur, un utilisateur normal de l’annuaire ne peut pas donner le consentement. Ce processus de consentement s’applique uniquement aux applications mutualisées, pas aux applications à client unique, car l’application a déjà les autorisations nécessaires.


4. Une fois que l’utilisateur a donné son consentement, l’application web reçoit le code d’autorisation dont elle a besoin pour obtenir un jeton d’accès.


5. À l’aide du code d’autorisation émis par Azure AD, l’application web envoie une demande au point de terminaison de jeton d’Azure AD. Celle-ci comprend le code d’autorisation, des détails sur l’application cliente (ID client et URI de redirection) et la ressource souhaitée (URI ID d’application de l’API web).


6. Le code d’autorisation et les informations sur l’application web et l’API web sont validés par Azure AD. Si la validation réussit, Azure AD renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT.


7. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec la mention « porteur » dans l’en-tête d’autorisation de la demande adressée à l’API web. L’API web valide ensuite le jeton JWT et, si la validation réussit, renvoie la ressource souhaitée.

#### Exemples de code

Consultez les exemples de code pour les scénarios du type application web vers API web. Et consultez cette page régulièrement : nous ajoutons de nouveaux exemples en permanence. [Application web vers API web](active-directory-code-samples.md#web-application-to-web-api).


#### Inscription

- Application à client unique : pour les identités d’application et les identités d’utilisateur délégué, l’application web et l’API web doivent être inscrites dans le même annuaire dans Azure AD. L’API web peut être configurée pour exposer un ensemble d’autorisations utilisées pour limiter l’accès de l’application web à ses ressources. Si une identité d’utilisateur délégué est utilisée, l’application web doit sélectionner les autorisations souhaitées dans le menu déroulant « Autorisations pour d’autres applications » du portail de gestion Azure. Cette étape n’est pas requise si une identité d’application est utilisée.


- Application mutualisée : tout d’abord, l’application web est configurée pour indiquer les autorisations dont elle a besoin pour fonctionner. Cette liste d’autorisations requises s’affiche dans une boîte de dialogue quand un utilisateur ou un administrateur de l’annuaire de destination donne son consentement à l’application, ce qui la met à disposition de son organisation. Certaines applications nécessitent uniquement des autorisations utilisateur, pour lesquelles tous les utilisateurs de l’organisation peuvent donner leur consentement. D’autres nécessitent des autorisations administrateur, pour lesquelles un utilisateur de l’organisation ne peut pas donner son consentement. Seul un administrateur d’annuaires peut donner son consentement aux applications qui requièrent des autorisations de ce niveau. Une fois le consentement donné par l’utilisateur ou l’administrateur, l’application web et l’API web sont inscrites dans l’annuaire de l’organisation de l’utilisateur ou de l’administrateur.

#### Expiration du jeton

Quand l’application web utilise son code d’autorisation pour obtenir un jeton d’accès JWT, elle reçoit également un jeton d’actualisation JWT. À l’expiration du jeton d’accès , le jeton d’actualisation peut être utilisé pour authentifier l’utilisateur à nouveau sans lui demander de se connecter une nouvelle fois. Ce jeton d’actualisation est ensuite utilisé pour authentifier l’utilisateur, ce qui donne lieu à la création d’un nouveau jeton d’accès et d’un nouveau jeton d’actualisation.


### Application démon ou serveur vers API web


Cette section décrit une application démon ou serveur ayant besoin d’obtenir des ressources d’une API web. Deux scénarios secondaires s’appliquent à cette section : un démon doit appeler une API web, scénario basé sur l’octroi d’informations d’identification client OAuth 2.0 ; et une application serveur (par exemple, une API web) doit appeler une API web, scénario basé sur la spécification préliminaire « Au nom de » OAuth 2.0.

Pour le scénario dans lequel une application démon doit appeler une API web, il est important de comprendre un certain nombre de choses. Tout d’abord, l’intervention de l’utilisateur n’est pas possible avec une application démon, qui requiert que l’application ait sa propre identité. Le traitement par lots et un service de système d’exploitation exécuté en arrière-plan sont des exemples d’application démon. Ce type d’application demande un jeton d’accès en utilisant son identité d’application et en fournissant à Azure AD son ID client, ses informations d’identification (mot de passe ou certificat) et l’URI ID d’application. Une fois l’authentification réussie, le démon reçoit un jeton d’accès d’Azure AD. Celui-ci est ensuite utilisé pour appeler l’API web.

Pour le scénario dans lequel une application serveur doit appeler une API web, nous allons prendre un exemple afin de faciliter la compréhension. Imaginez qu’un utilisateur se soit authentifié auprès d’une application native et que celle-ci doive appeler une API web. Azure AD émet un jeton d’accès JWT pour appeler l’API web. Si l’API web doit appeler une autre API web en aval, elle peut utiliser le flux « au nom de » pour déléguer l’identité de l’utilisateur et s’authentifier auprès de l’API web de deuxième niveau.

#### Diagramme

![Diagramme Application démon ou serveur vers API web](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### Description du flux du protocole

##### Identité d’application avec octroi d’informations d’identification client OAuth 2.0

1. Tout d’abord, l’application serveur doit s’authentifier auprès d’Azure AD avec sa propre identité, sans intervention humaine par le biais d’une boîte de dialogue interactive d’ouverture de session par exemple. Elle envoie une demande au point de terminaison de jeton d’Azure AD, avec les informations d’identification, l’ID client et l’URI ID d’application.


2. Azure AD authentifie l’application et renvoie un jeton d’accès JWT, qui est utilisé pour appeler l’API web.


3. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec la mention « porteur » dans l’en-tête d’autorisation de la demande adressée à l’API web. L’API web valide ensuite le jeton JWT et, si la validation réussit, renvoie la ressource souhaitée.


##### Identité d’utilisateur délégué avec spécification préliminaire « Au nom de » OAuth 2.0

Le flux présenté ci-après part du principe qu’un utilisateur a été authentifié auprès d’une autre application (telle qu’une application native) et que son identité d’utilisateur a été utilisée pour obtenir un jeton d’accès à l’API web de premier niveau.

1. L’application native envoie le jeton d’accès à l’API web de premier niveau.


2. L’API web de premier niveau envoie une demande au point de terminaison de jeton d’Azure AD, en fournissant son ID client et ses informations d’identification, ainsi que le jeton d’accès de l’utilisateur. La demande est également envoyée avec un paramètre on\_behalf\_of, qui indique que l’API web demande de nouveaux jetons pour appeler une API web en aval au nom de l’utilisateur d’origine.


3. Azure AD vérifie que l’API web de premier niveau est autorisée à accéder à l’API web de deuxième niveau et valide la demande en renvoyant un jeton d’accès JWT et un jeton d’actualisation JWT à l’API web de premier niveau.


4. Sur HTTPS, l’API web de premier niveau appelle ensuite l’API web de deuxième niveau en ajoutant la chaîne de jeton dans l’en-tête d’autorisation de la demande. L’API web de premier niveau peut continuer à appeler l’API web de deuxième niveau tant que le jeton d’accès et les jetons d’actualisation sont valides.

#### Exemples de code

Consultez les exemples de code pour les scénarios du type application démon ou serveur vers API web. Et consultez cette page régulièrement : nous ajoutons de nouveaux exemples en permanence. [Application serveur ou démon vers API web](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### Inscription

- Application à client unique : pour les identités d’application et les identités d’utilisateur délégué, l’application démon ou serveur doit être inscrite dans le même annuaire dans Azure AD. L’API web peut être configurée pour exposer un ensemble d’autorisations utilisées pour limiter l’accès de l’application démon ou serveur à ses ressources. Si une identité d’utilisateur délégué est utilisée, l’application serveur doit sélectionner les autorisations souhaitées dans le menu déroulant « Autorisations pour d’autres applications » du portail de gestion Azure. Cette étape n’est pas requise si une identité d’application est utilisée.


- Application mutualisée : tout d’abord, l’application démon ou serveur est configurée pour indiquer les autorisations dont elle a besoin pour fonctionner. Cette liste d’autorisations requises s’affiche dans une boîte de dialogue quand un utilisateur ou un administrateur de l’annuaire de destination donne son consentement à l’application, ce qui la met à disposition de son organisation. Certaines applications nécessitent uniquement des autorisations utilisateur, pour lesquelles tous les utilisateurs de l’organisation peuvent donner leur consentement. D’autres nécessitent des autorisations administrateur, pour lesquelles un utilisateur de l’organisation ne peut pas donner son consentement. Seul un administrateur d’annuaires peut donner son consentement aux applications qui requièrent des autorisations de ce niveau. Quand un utilisateur ou un administrateur donne son consentement, les deux API web sont inscrites dans son annuaire.

#### Expiration du jeton

Quand la première application utilise son code d’autorisation pour obtenir un jeton d’accès JWT, elle reçoit également un jeton d’actualisation JWT. À l’expiration du jeton d’accès , le jeton d’actualisation peut être utilisé pour authentifier l’utilisateur à nouveau sans lui demander ses informations d’identification. Ce jeton d’actualisation est ensuite utilisé pour authentifier l’utilisateur, ce qui donne lieu à la création d’un nouveau jeton d’accès et d’un nouveau jeton d’actualisation.

## Voir aussi

[Guide du développeur Azure Active Directory](active-directory-developers-guide.md)

[Exemples de code Azure Active Directory](active-directory-code-samples.md)

[Informations importantes sur la substitution des clés de signature dans Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)

<!---HONumber=AcomDC_0921_2016-->