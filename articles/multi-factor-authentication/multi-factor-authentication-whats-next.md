<properties 
	pageTitle="Azure Multi-Factor Authentication - Étapes suivantes"
	description="Voici la page Multi-Factor Authentication qui vous indique ce qu’il faut faire avec MFA. Cela inclut les rapports, l’alerte de fraude, le contournement à usage unique, les messages vocaux personnalisés, la mise en cache, les adresses IP approuvées et les mots de passe d’application."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="kgremban"/>

# Configuration d’Azure Multi-Factor Authentication

L'article suivant vous aidera à gérer Azure Multi-Factor Authentication, maintenant que vous êtes opérationnel. Il aborde divers sujets qui vous permettent de tirer le meilleur parti d'Azure Multi-Factor Authentication. N'oubliez pas que toutes ces fonctionnalités sont disponibles dans chaque version d’Azure Multi-Factor Authentication.

Vous trouverez la configuration de certaines des fonctionnalités ci-dessous dans le portail de gestion Azure Multi-Factor Authentication. Vous pouvez accéder à ce portail de deux façons différentes. Les deux s'effectuent par le biais du portail Azure. La première consiste à gérer un fournisseur Multi-Factor Auth si vous utilisez le modèle MFA basé sur la consommation. La seconde s'effectue par le biais des paramètres du service MFA. La seconde option requiert un fournisseur Multi-Factor Auth ou une licence Azure MFA, Azure AD Premium ou Enterprise Mobility Suite.

Pour accéder au portail de gestion MFA par le biais d'un fournisseur Azure Multi-Factor Auth, connectez-vous au portail Azure en tant qu'administrateur, puis sélectionnez l'option Active Directory. Cliquez sur l'onglet **Fournisseurs d'authentification multifacteur**, puis sélectionnez votre répertoire et cliquez sur le bouton **Gérer** en bas.

Pour accéder au portail de gestion MFA par le biais de la page des paramètres du service MFA, connectez-vous au portail Azure en tant qu'administrateur, puis sélectionnez l'option Active Directory. Cliquez sur votre répertoire, puis sur l'onglet **Configurer**. Dans la section Authentification multifacteur, sélectionnez **Gérer les paramètres du service**. En bas de la page Paramètres du service MFA, cliquez sur le lien **Accéder au portail**.


Fonctionnalité| Description| Éléments
:------------- | :------------- | :------------- |
[Alerte de fraude](#fraud-alert)|Alerte de fraude peut être configuré et installé de manière à ce que vos utilisateurs puissent signaler les tentatives frauduleuses d’accès à leurs ressources.|Installation, configuration et signalement d’une fraude
[Contournement à usage unique](#one-time-bypass) |Un contournement à usage unique permet à un utilisateur de s'authentifier une seule fois en « contournant » l'authentification multifacteur.|Installation et configuration d’un contournement à usage unique
[Messages vocaux personnalisés](#custom-voice-messages) |Les messages vocaux personnalisés vous permettent d'utiliser vos propres enregistrements ou messages d’accueil avec l'authentification multifacteur. |Installation et configuration des messages et messages d’accueil personnalisés
[Mise en cache](#caching-in-azure-multi-factor-authentication)|La mise en cache vous permet de définir une période spécifique pour que les tentatives d'authentification suivantes aboutissent automatiquement. |Installation et configuration de la mise en cache de l’authentification.
[Adresses IP approuvées](#trusted-ips)|Adresses IP approuvées est une fonctionnalité d'authentification multifacteur qui permet aux administrateurs d'un client géré ou fédéré de contourner l'authentification multifacteur des utilisateurs qui se connectent à partir de l'intranet local de l'entreprise.|Configurer et définir les adresses IP qui ne sont pas soumis à l'authentification multifacteur
[Mots de passe d'application](#app-passwords)|Mots de passe d'application permet à une application, qui ne prend pas en charge MFA, de contourner l'authentification multifacteur et de continuer à fonctionner.|Informations sur les mots de passe d'application.
[Mémoriser Multi-Factor Authentication pour les appareils et les navigateurs mémorisés](#remember-multi-factor-authentication-for-devices-users-trust)|Vous permet de mémoriser des appareils pour un nombre défini de jours après qu’un utilisateur soit parvenu à se connecter à l’aide de MFA.|Informations sur l'activation de cette fonctionnalité et la configuration du nombre de jours.
[Méthodes de vérification sélectionnables](#selectable-verification-methods)|Vous permet de choisir les méthodes d'authentification disponibles pour les utilisateurs.|Informations sur l'activation ou la désactivation des méthodes d'authentification spécifiques telles que les messages d'appel ou de texte.



## Alerte de fraude
Alerte de fraude peut être configuré et installé de manière à ce que vos utilisateurs puissent signaler les tentatives frauduleuses d’accès à leurs ressources. Les utilisateurs peuvent signaler une fraude à l’aide de l'application mobile ou de leur téléphone.

### Pour installer et configurer l'alerte de fraude

1.	Connectez-vous à http://azure.microsoft.com.
2.	Accédez au portail de gestion MFA avec les instructions indiquées en haut de cette page.
3.	Dans le portail de gestion Azure Multi-Factor Authentication, cliquez sur Paramètres dans la section Configurer.
4.	Sous la section Alerte de fraude de la page Paramètres, cochez la case Permettre aux utilisateurs d'envoyer des alertes de fraude.
5.	Si vous souhaitez que les utilisateurs soient bloqués lorsqu'une fraude est signalée, cochez l’option Bloquer l'utilisateur lorsqu'une fraude est signalée.
6.	Dans la zone de texte **Code permettant de signaler une fraude durant le message d'accueil initial**, saisissez un code qui peut être utilisé lors de la vérification de l'appel. Si un utilisateur saisit ce code et le signe #, à la place du signe # seul, une alerte de fraude est alors signalée.
7.	Cliquez sur Enregistrer au bas de la page.

>[AZURE.NOTE]
Le message d'accueil de Microsoft par défaut demande aux utilisateurs d'appuyer sur 0# pour envoyer une alerte de fraude. Si vous utilisez un code autre que 0, vous devrez enregistrer et charger vos propres messages d'accueil vocaux personnalisés avec les instructions appropriées.


![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)

### Pour signaler une alerte de fraude
Une alerte de fraude peut être déclarée de deux façons. À l’aide de l'application mobile ou de votre téléphone.

### Pour signaler une alerte de fraude à l’aide de l'application mobile



1. Lorsqu'une vérification est envoyée sur votre téléphone, cliquez dessus pour lancer l'application Microsoft Authenticator.
2. Pour signaler une fraude, cliquez sur Annuler et Signaler une fraude. Cela fera apparaître une zone de texte qui vous indiquera que le personnel du service informatique de votre organisation va être averti d'une éventuelle fraude.
3. Cliquez sur Signaler une fraude.
4. Dans l'application, cliquez sur Fermer.

![Cloud](./media/multi-factor-authentication-whats-next/report1.png)


![Cloud](./media/multi-factor-authentication-whats-next/fraud2.png)

### Pour signaler une alerte de fraude à l’aide de votre téléphone

1. Lorsque vous recevrez un appel de vérification sur votre téléphone, répondez-y.</li>
2. Pour signaler une fraude, saisissez le code qui a été configuré pour correspondre au signalement de fraude via votre téléphone, à la place du signe #. Vous serez averti qu'une alerte de fraude a été soumise.
3. Terminez l'appel.

### Pour afficher le rapport de fraude

1. Ouvrez une session sur [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Sélectionnez à gauche Active Directory.
3. En haut de la page, sélectionnez Fournisseurs d'authentification multifacteur. Cela fera apparaître une liste de vos fournisseurs d'authentification multifacteur.
4. Si vous avez plusieurs fournisseurs d'authentification multifacteur, sélectionnez celui pour lequel vous souhaitez afficher le rapport d’alertes de fraude et cliquez sur Gérer en bas de la page. Si vous n’en avez qu’un, cliquez sur Gérer. Cette opération ouvre le portail de gestion Azure Multi-Factor Authentication.
5. Dans le portail de gestion Azure Multi-Factor Authentication, sur la gauche, sous Afficher un rapport, cliquez sur Alerte de fraude.
6. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d'utilisateur spécifiques, les numéros de téléphone et l'état des utilisateurs.
7. Cliquez sur Exécuter. Cela fera apparaître un rapport similaire à celui mentionné ci-dessous. Vous pouvez également cliquer sur Exporter au format CSV si vous souhaitez exporter le rapport.

## Contournement à usage unique

Un contournement à usage unique permet à un utilisateur de s'authentifier une seule fois en « contournant » l'authentification multifacteur. Le contournement est temporaire et expire après le nombre de secondes spécifié. Par conséquent, lorsque l'application mobile ou le téléphone ne reçoit pas de notification ou d’appel téléphonique, vous pouvez activer un contournement à usage unique afin que l'utilisateur puisse accéder à la ressource souhaitée.

### Pour créer un contournement à usage unique

1.	Connectez-vous à http://azure.microsoft.com.
2.	Accédez au portail de gestion MFA avec les instructions indiquées en haut de cette page.
3.	Dans le portail de gestion Azure Multi-Factor Authentication, si le nom de votre client ou fournisseur Azure MFA s'affiche à gauche avec le signe +, cliquez sur le signe + pour afficher les différents groupes de réplication du serveur MFA et le groupe Azure par défaut. Cliquez sur le groupe approprié.
4.	Sous Administration des utilisateurs, cliquez sur **Contournement à usage unique**. ![Cloud](./media/multi-factor-authentication-whats-next/create1.png)
5.	Dans la page Contournement à usage unique, cliquez sur **Nouveau contournement à usage unique**.
6.	Saisissez le nom d'utilisateur, le nombre de secondes pendant lesquelles le contournement se produira, la raison du contournement, puis cliquez sur **Contourner**. ![Cloud](./media/multi-factor-authentication-whats-next/create2.png)
7.	À ce stade, l'utilisateur doit se connecter avant l'expiration du contournement à usage unique.



### Pour afficher le rapport de contournement à usage unique

1. Ouvrez une session sur [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Sélectionnez à gauche Active Directory.
3. En haut de la page, sélectionnez Fournisseurs d'authentification multifacteur. Cela fera apparaître une liste de vos fournisseurs d'authentification multifacteur.
4. Si vous avez plusieurs fournisseurs d'authentification multifacteur, sélectionnez celui pour lequel vous souhaitez afficher le rapport d’alertes de fraude et cliquez sur Gérer en bas de la page. Si vous n’en avez qu’un, cliquez sur Gérer. Cette opération ouvre le portail de gestion Azure Multi-Factor Authentication.
5. Dans le portail de gestion Azure Multi-Factor Authentication, sur la gauche, sous Afficher un rapport, cliquez sur Contournement à usage unique.
6. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d'utilisateur spécifiques, les numéros de téléphone et l'état des utilisateurs.
7. Cliquez sur Exécuter. Cela fera apparaître un rapport similaire à celui mentionné ci-dessous. Vous pouvez également cliquer sur Exporter au format CSV si vous souhaitez exporter le rapport.

<center>![Cloud](./media/multi-factor-authentication-whats-next/report.png)</center>

## Messages vocaux personnalisés

Les messages vocaux personnalisés vous permettent d'utiliser vos propres enregistrements ou messages d’accueil avec l'authentification multifacteur. Ils peuvent être utilisés pour compléter ou remplacer les enregistrements Microsoft.

Avant de commencer, tenez compte des informations suivantes :

- Les formats de fichiers pris en charge sont .wav et .mp3.
- La taille limite des fichiers est de 5 Mo.
- La durée des messages d’authentification ne doit pas dépasser les 20 secondes. Toute durée supérieure pourrait entraîner l’échec de la vérification, car l'utilisateur ne répondra peut-être pas avant que le message se termine et que la vérification expire.



### Pour configurer les messages vocaux personnalisés dans Azure Multi-Factor Authentication
1.	Créer un message vocal personnalisé à l'aide d'un des formats de fichiers pris en charge.
2.	Connectez-vous à http://azure.microsoft.com.
3.	Accédez au portail de gestion MFA avec les instructions indiquées en haut de cette page.
4.	Dans le portail de gestion Azure Multi-Factor Authentication, cliquez sur Messages vocaux dans la section Configurer.
5.	Sous la section Messages vocaux, cliquez sur **Nouveau message vocal**. ![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)
6.	Dans la page Configurer : nouveaux messages vocaux, cliquez sur **Gérer les fichiers audio**. ![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)
7.	Dans la page Configurer : fichiers audio, cliquez sur **Télécharger un fichier audio**. ![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)
8.	Dans la page Configurer : télécharger un fichier audio, cliquez sur **Parcourir**, accédez à votre message vocal, puis cliquez sur **Ouvrir**. ![Cloud](./media/multi-factor-authentication-whats-next/custom4.png)
9.	Ajoutez une description et cliquez sur Télécharger.
10.	Une fois cette opération terminée, vous verrez un message indiquant que vous avez téléchargé le fichier.
11.	Sur la gauche, cliquez sur Messages vocaux.
12.	Sous la section Messages vocaux, cliquez sur Nouveau message vocal.
13.	Dans la liste déroulante des langues, sélectionnez une langue.
14.	Si ce message concerne une application spécifique, spécifiez-la dans la zone de l'application.
15.	Dans le type de message, sélectionnez le type de message qui sera remplacé par notre nouveau message personnalisé.
16.	Dans le menu déroulant du fichier audio, sélectionnez le fichier audio.
17.	Cliquez sur **Create**. Vous verrez un message indiquant que vous avez créé un message vocal. ![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## Mise en cache dans Azure Multi-Factor Authentication

La mise en cache vous permet de définir une période spécifique pour que les tentatives d'authentification suivantes aboutissent automatiquement. Cette fonctionnalité est principalement utilisée lorsque les systèmes locaux, comme un VPN, envoient plusieurs demandes de vérification alors que la première demande est toujours en cours. Ceci permet aux demandes suivantes de réussir automatiquement après que l'utilisateur a réussi la vérification en cours. Notez que la mise en cache n’est pas destinée à être utilisée pour les connexions à Azure AD.


### Pour configurer la mise en cache dans Azure Multi-Factor Authentication

1.	Connectez-vous à http://azure.microsoft.com.
2.	Accédez au portail de gestion MFA avec les instructions indiquées en haut de cette page.
3.	Dans le portail de gestion Azure Multi-Factor Authentication, cliquez sur Mise en cache dans la section Configurer.
4.	Dans la page Configurer la mise en cache, cliquez sur Nouveau Cache
5.	Sélectionnez le type de cache et les secondes du cache. Cliquez sur Créer.

<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## Adresses IP approuvées

Adresses IP approuvées est une fonctionnalité d'authentification multifacteur qui permet aux administrateurs d'un client géré ou fédéré de contourner l'authentification multifacteur des utilisateurs qui se connectent à partir de l'intranet local de l'entreprise. Les fonctionnalités sont disponibles pour les clients Azure AD titulaires d’une licence Azure AD Premium, Enterprise Mobility Suite ou Azure Multi-Factor Authentication.


Type de client Azure AD| Options d’Adresses IP approuvées disponibles
:------------- | :------------- |
Adresses IP gérées|Plages d'adresses IP spécifiques : les administrateurs peuvent spécifier une plage d'adresses IP pouvant contourner l'authentification multifacteur des utilisateurs qui se connectent à partir de l'intranet de l’entreprise.
Adresses IP fédérées|<li>Tous les utilisateurs fédérés : tous les utilisateurs fédérés, qui se connectent au sein de l'organisation, contourneront l'authentification multifacteur à l'aide d'une revendication émise par AD FS.</li><li>Plages d'adresses IP spécifiques : les administrateurs peuvent spécifier une plage d'adresses IP pouvant contourner l'authentification multifacteur des utilisateurs qui se connectent à partir de l'intranet de l'entreprise.

Ce contournement ne fonctionne qu’à partir de l'intranet d'une entreprise. Par exemple, si vous n'avez sélectionné que les utilisateurs fédérés, et qu’un utilisateur se connecte en dehors de l'intranet de l'entreprise, cet utilisateur devra s'authentifier à l'aide de l'authentification multifacteur, même si celui-ci présente une revendication AD FS. Le tableau suivant vous indique lorsque l’authentification multifacteur et les mots de passe d’application sont requis au sein de votre réseau d'entreprise et en dehors de votre réseau d'entreprise lorsque Adresses IP approuvées est activé.


|Adresses IP approuvées activé| Adresses IP approuvées désactivé
:------------- | :------------- | :------------- |
Au sein du réseau d'entreprise|Pour les flux de navigateur, l’authentification multifacteur n’est PAS obligatoire.|Pour les flux de navigateur, l’authentification multifacteur est obligatoire.
|Pour les applications clientes riches, les mots de passe traditionnels fonctionnent si l'utilisateur n'a créé aucun mot de passe d'application. Une fois qu'un mot de passe d’application a été créé, les mots de passe d'application sont obligatoires.|Pour les applications clientes riches, les mots de passe d'application sont obligatoires
En dehors du réseau d'entreprise|Pour les flux de navigateur, l'authentification multifacteur est obligatoire.|Pour les flux de navigateur, l'authentification multifacteur est obligatoire.
|Pour les applications clientes riches, les mots de passe d'application sont obligatoires.|Pour les applications clientes riches, les mots de passe d'application sont obligatoires.

### Pour activer Adresses IP approuvées

1. Connectez-vous au portail Azure Classic.
2. Cliquez à gauche sur Active Directory.
3. Sous Directory, cliquez sur le répertoire sur lequel vous souhaitez configurer Adresses IP approuvées.
4. Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.
5. Dans la section Authentification multifacteur, cliquez sur Gérer les paramètres de service.
6. Dans la page Paramètres du service, sous Adresses IP approuvées, sélectionnez l'une des options suivantes :

	- Pour les demandes effectuées par des utilisateurs fédérés provenant de mon intranet : tous les utilisateurs fédérés qui se connectent à partir du réseau d'entreprise contourneront l'authentification multifacteur à l'aide d'une revendication émise par AD FS.
	- Pour les demandes provenant d'une plage spécifique d'adresses IP publiques, saisissez les adresses IP dans les zones de texte à l'aide de la notation CIDR. Par exemple : xxx.xxx.xxx.0/24 pour les adresses IP dans la plage xxx.xxx.xxx.1 – xxx.xxx.xxx.254, ou xxx.xxx.xxx.xxx/32 pour une adresse IP unique. Vous pouvez saisir jusqu'à 50 plages d'adresses IP.

7. Cliquez sur Enregistrer.
8. Une fois les mises à jour appliquées, cliquez sur Fermer.



![Adresses IP approuvées](./media/multi-factor-authentication-whats-next/trustedips3.png)




## Mots de passe d'application

Dans certaines applications, telles qu’Office 2010 (ou version antérieure) et Apple Mail, vous ne pouvez pas utiliser l’authentification multifacteur. Pour utiliser ces applications, vous devrez utiliser des « mots de passe d’application » à la place de votre mot de passe traditionnel. Le mot de passe d'application permet à l’application de contourner l'authentification multifacteur et de continuer à fonctionner.

>[AZURE.NOTE] Authentification moderne pour les clients Office 2013
>
> Les clients Office 2013 (y compris Outlook) prennent désormais en charge de nouveaux protocoles d’authentification et ils peuvent être activés pour prendre en charge Multi-Factor Authentication. Ainsi, une fois activés, les mots de passe d’application ne sont pas obligatoires avec les clients Office 2013. Pour plus d'informations, consultez la rubrique [Version préliminaire publique de l'authentification moderne Office 2013 annoncée](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).



### Informations à connaître sur les mots de passe d’application

Voici ce qu’il faut absolument savoir sur les mots de passe d'application.

Expérience d'authentification|Pour les applications avec navigateur|Pour les applications sans navigateur
:------------- | :------------- | :-------------
|<ul><li>Le premier facteur d'authentification est exécuté localement</li><li>Le second facteur est une méthode effectuée par téléphone via l'identité de Cloud.</li>|<ul><li>Les administrateurs et utilisateurs peuvent utiliser des mots de passe d'application pour se connecter.

- Les utilisateurs peuvent avoir plusieurs mots de passe d’application, ce qui augmente leur surface d'exposition au vol. Puisque les mots de passe d'application sont difficiles à mémoriser, cela peut encourager les utilisateurs à les noter. Ceci n'est pas recommandé et doit être proscrit, car pour se connecter, l’utilisateur n’a besoin que d’un mot de passe d’application.
- Les applications qui mettent en cache des mots de passe et les utilisent dans des scénarios locaux, peuvent échouer, car le mot de passe d'application ne sera pas connu en dehors de l'id d'organisation. Des courriers électroniques Exchange sont, par exemple, stockés localement, mais la messagerie archivée se trouve dans le cloud. Le même mot de passe ne fonctionnera pas.
- Le mot de passe est généré automatiquement et il n'est pas fourni par l'utilisateur. Le mot de passe automatiquement généré est en effet beaucoup plus difficile à pirater et bien mieux sécurisé.
- À l’heure actuelle, un utilisateur peut posséder jusqu’à 40 mots de passe. Il vous faudra supprimer l’un de vos mots de passe d’application existants pour pouvoir en créer un nouveau.
- Une fois l’authentification multifacteur activée sur un compte d’utilisateur, les mots de passe d’application peuvent être utilisés avec la plupart des clients sans navigateur tels qu’Outlook et Lync, mais les actions d’administration ne peuvent pas être effectuées à l’aide de mots de passe d’application via des applications sans navigateur telles que Windows PowerShell, même si l’utilisateur dispose d’un compte d’administrateur. Vérifiez que vous créez un compte de service avec un mot de passe fort pour exécuter des scripts PowerShell et que vous n’activez pas ce compte pour l’authentification multifacteur.

>[AZURE.WARNING]  Les mots de passe d’application ne fonctionnent pas dans les environnements hybrides où les clients communiquent avec les points de terminaison locaux et les points de terminaison à découverte automatique de cloud.
>
>N’oubliez pas que les mots de passe d’application ne fonctionneront pas dans les environnements hybrides où les clients communiquent avec à la fois avec les points de terminaison locaux et de découverte automatique, car les mots de passe de domaine sont tenus de s’authentifier sur le site et les mots de passe d’application doivent s’authentifier avec le cloud.


### Recommandations en matière d'affectation de noms pour les mots de passe d'application
Les noms des mots de passe d'application doivent refléter l’appareil sur lequel ils seront utilisés. Si vous disposez, par exemple, d’un ordinateur portable qui contient des applications sans navigateur, telles qu’Outlook, Word et Excel, vous ne devez créer qu’un seul mot de passe d’application intitulé Ordinateur portable et l'utiliser dans toutes ces applications. Vous avez la possibilité de créer des mots de passe distincts pour toutes ces applications, mais ceci n'est pas conseillé. Il est recommandé de n'utiliser qu’un seul mot de passe par appareil.


<center>![Cloud](./media/multi-factor-authentication-whats-next/naming.png)</center>


### Mots de passe d'application fédérés (SSO)
Azure AD prend en charge la fédération avec les services de domaine Windows Server Active Directory (AD DS) locaux. Si votre organisation est fédérée (SSO) avec Azure AD et que vous vous apprêtez à utiliser Azure Multi-Factor Authentication, voici ce que vous devez absolument savoir à propos de l'utilisation des mots de passe d'application. Ceci s'applique uniquement aux clients fédérés (SSO).

- Le mot de passe est vérifié par Azure AD et il contourne ainsi la fédération. La fédération n'est utilisée activement que lorsque vous configurez le mot de passe d’application.
- Pour les utilisateurs fédérés (SSO), nous n’accéderons jamais au fournisseur d'identité (IdP), contrairement au flux passif. Les mots de passe seront stockés dans l'id d'organisation. Si l'utilisateur quitte l'entreprise, ces informations doivent être stockées en temps réel dans l'id d'organisation à l'aide de DirSync. La désactivation/suppression de compte peut mettre jusqu'à 3 heures à se synchroniser, ce qui peut retarder la désactivation/suppression du mot de passe dans Azure AD.
- Les paramètres de contrôle d'accès client locaux ne sont pas honorés par Mot de passe d’application
- Aucune authentification locale de journalisation/fonctionnalité d’audit n'est disponible pour Mot de passe
- Pour utiliser le client Microsoft Lync 2013, l’utilisateur final doit être très qualifié. Pour découvrir les étapes obligatoires, consultez la rubrique Modification du mot de passe de votre courrier électronique en mot de passe d’application.
- Certaines conceptions architecturales avancées peuvent nécessiter l’utilisation d'une combinaison de noms d'utilisateur, de mots de passe et de mots de passe d'application, lors de l'utilisation de l'authentification multifacteur avec les clients, selon l'emplacement de cette dernière. Pour les clients qui s'authentifient auprès d'une infrastructure locale, vous utiliseriez le nom d'utilisateur et le mot de passe d’une organisation. Pour les clients qui s'authentifient auprès d'Azure AD, vous utiliseriez le mot de passe d’application.

Supposons, par exemple, que vous disposez d'une architecture qui se compose des éléments suivants :

- Vous fédérez votre instance locale d'Active Directory avec Azure AD
- Vous utilisez Exchange Online
- Vous utilisez Lync qui est spécifiquement local
- Vous utilisez Azure Multi-Factor Authentication


![Vérification](./media/multi-factor-authentication-whats-next/federated.png)

 Dans ce cas, vous devez procéder comme suit :

- Lorsque vous vous connectez à Lync, utilisez le nom d'utilisateur et le mot de passe de votre organisation.
- Lorsque vous tentez d'accéder au carnet d'adresses via un client Outlook qui se connecte à Exchange Online, utilisez un mot de passe d’application.

### Autorisation de création de mots de passe d'application
Par défaut, les utilisateurs ne peuvent pas créer des mots de passe d'application. Cette fonctionnalité doit être activée. Pour permettre aux utilisateurs de créer des mots de passe d’application, procédez comme suit.

#### Pour permettre aux utilisateurs de créer des mots de passe d'application



1. Connectez-vous au portail Azure Classic.
2. Cliquez à gauche sur Active Directory.
3. Sous Directory, cliquez sur le répertoire de l'utilisateur que vous souhaitez activer.
4. En haut de la page, cliquez sur Utilisateurs.
5. En bas de la page, cliquez sur Gérer Multi-Factor Auth. Cette action ouvre la page relative à l’authentification multifacteur.
6. En haut de la section Authentification multifacteur, cliquez sur Paramètres de service.
7. Vérifiez que l’option, située à côté de Permettre aux utilisateurs de créer des mots de passe d'application pour se connecter à des applications sans navigateur, est bien sélectionnée.


![Création de mots de passe d'application](./media/multi-factor-authentication-whats-next/trustedips3.png)

### Création de mots de passe d'application
Les utilisateurs peuvent créer des mots de passe d'application lors de leur inscription initiale. Ils ont la possibilité de les créer à la fin du processus d'inscription.

Les utilisateurs pourront également créer des mots de passe d’application par la suite, en modifiant leurs paramètres dans le portail Azure, le portail Office 365 ou via

### Pour créer des mots de passe d'application dans le portail Office 365
--------------------------------------------------------------------------------


1. Connectez-vous au portail Office 365.
2. Dans l’angle supérieur droit, sélectionnez le widget de paramètres.
3. Sur la gauche, sélectionnez Vérification de sécurité supplémentaire
4. Sur la droite, sélectionnez **Mettre à jour mes numéros de téléphone utilisés pour la sécurité du compte**
5. En haut de la page de vérification, sélectionnez les mots de passe d'application
6. Cliquez sur **Créer**
7. Saisissez un nom pour le mot de passe d’application, puis cliquez sur **Suivant**
8. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### Pour créer des mots de passe d'application dans le portail Azure
--------------------------------------------------------------------------------
1. Connectez-vous au portail Azure Classic.
3. En haut de la page, cliquez avec le bouton droit sur votre nom d’utilisateur et sélectionnez Vérification de sécurité supplémentaire.
5. En haut de la page de vérification, sélectionnez les mots de passe d'application
6. Cliquez sur **Créer**
7. Saisissez un nom pour le mot de passe d’application, puis cliquez sur **Suivant**
8. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.


![Mots de passe d'application](./media/multi-factor-authentication-whats-next/app2.png)

### Pour créer des mots de passe d’application si vous ne possédez pas d’abonnement Office 365 ou Azure
--------------------------------------------------------------------------------
1. Ouvrez une session sur [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. En haut de la page, sélectionnez le profil.
3. Cliquez avec le bouton droit sur votre nom d'utilisateur et sélectionnez Vérification de sécurité supplémentaire.
5. En haut de la page de vérification, sélectionnez les mots de passe d'application
6. Cliquez sur **Créer**
7. Saisissez un nom pour le mot de passe d’application, puis cliquez sur **Suivant**
8. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.

![Mots de passe d'application](./media/multi-factor-authentication-whats-next/myapp.png)

## Mémoriser Multi-Factor Authentication pour les appareils utilisateur de confiance

La mémorisation Multi-Factor Authentication pour les appareils et les navigateurs de confiance est une fonctionnalité disponible gratuitement pour tous les utilisateurs MFA. Elle vous permet de donner aux utilisateurs la possibilité de contourner MFA pour un nombre défini de jours après une connexion réussie à l’aide de l’authentification multifacteur. Ceci permet d’améliorer la facilité d’utilisation pour vos utilisateurs.

Toutefois, étant donné que les utilisateurs sont autorisés à mémoriser MFA pour les appareils de confiance, cette fonctionnalité peut réduire la sécurité du compte. Pour garantir la sécurité du compte, vous devez restaurer Multi-Factor Authentication sur leurs appareils pour l’un des scénarios suivants :

- Si leur compte d'entreprise est devenu compromis
- Si un appareil mémorisé a été perdu ou volé

> [AZURE.NOTE] Cette fonctionnalité est implémentée comme un cache de cookie du navigateur. Elle ne fonctionnera pas si les cookies de votre navigateur ne sont pas activés.

### Activation et désactivation de Mémoriser MFA

1. Connectez-vous au portail Azure Classic.
2. Cliquez à gauche sur Active Directory.
3. Dans Active Directory, cliquez sur le répertoire pour lequel vous souhaitez configurer Mémoriser Multi-Factor Authentication pour les appareils.
4. Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.
5. Dans la section Authentification multifacteur, cliquez sur Gérer les paramètres de service.
6. Dans la page Paramètres de service, sous Gérer les paramètres des appareils de l’utilisateur, sélectionnez/désélectionnez l’option **Permettre aux utilisateurs de mémoriser l’authentification multifacteur pour les appareils de confiance**. ![Mémoriser des appareils](./media/multi-factor-authentication-whats-next/remember.png)
8. Définissez le nombre de jours pendant lesquels vous souhaitez autoriser l’interruption. La valeur par défaut est de 14 jours.
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.


## Méthodes de vérification sélectionnables
Il est désormais possible de choisir les méthodes d'authentification disponibles pour vos utilisateurs lors de l'utilisation d'Azure Multi-Factor Authentication. Cette fonctionnalité était auparavant uniquement disponible dans la version serveur locale. Le tableau ci-dessous fournit une vue d'ensemble des différentes méthodes d'authentification qui peuvent être activées ou désactivées pour vos utilisateurs.

Méthode|Description
:------------- | :------------- |
[Appel vers le téléphone](multi-factor-authentication-end-user-first-time-mobile-phone.md)| Effectue un appel vocal automatisé vers le téléphone d’authentification. L’utilisateur répond à l’appel et appuie sur la touche # du clavier du téléphone pour s’authentifier. Ce numéro de téléphone n’est pas synchronisé avec Active Directory local.
[Message texte vers le téléphone](multi-factor-authentication-end-user-first-time-mobile-phone.md)|Envoie un message texte contenant un code de vérification à l’utilisateur. L’utilisateur est invité à répondre au SMS avec le code de vérification ou à entrer le code de vérification dans l’interface de connexion.
[Notification via une application mobile](multi-factor-authentication-end-user-first-time-mobile-app.md)|Dans ce mode, l’application Microsoft Authenticator empêche l’accès non autorisé à des comptes et arrête les transactions frauduleuses. Cette opération est effectuée à l’aide d’une notification Push sur votre téléphone ou votre appareil inscrit. Affichez simplement la notification et si elle est légitime, sélectionnez Vérifier. Dans le cas contraire, vous pouvez choisir de refuser ou de refuser et signaler la notification frauduleuse. Pour plus d’informations sur le signalement des notifications frauduleuses, consultez la rubrique Utilisation de la fonctionnalité de refus et signalement d’une fraude pour Multi-Factor Authentication.</br></br>L'application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).|
[Code de vérification de l’application mobile](multi-factor-authentication-end-user-first-time-mobile-app.md)|Dans ce mode, l'application Microsoft Authenticator peut être utilisée comme jeton logiciel pour générer un code de vérification OATH. Ce code de vérification peut ensuite être entré avec le nom d’utilisateur et un mot de passe pour fournir la deuxième forme d’authentification.</li><br><p> L'application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

### Comment activer/désactiver les méthodes d'authentification

1. Connectez-vous au portail Azure Classic.
2. Cliquez à gauche sur Active Directory.
3. Sous Active Directory, cliquez sur le répertoire pour lequel vous souhaitez activer ou désactiver les méthodes d'authentification.
4. Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.
5. Dans la section Authentification multifacteur, cliquez sur Gérer les paramètres de service.
6. Dans les options de vérification de la page Paramètres de service, sélectionnez/désélectionnez les options que vous souhaitez utiliser.</br></br> ![Options de vérification](./media/multi-factor-authentication-whats-next/authmethods.png)
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.

<!---HONumber=AcomDC_0921_2016-->