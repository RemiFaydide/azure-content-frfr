<properties
	pageTitle="Forum Aux Questions d’Azure Multi-Factor Authentication"
	description="Fournit une liste de questions fréquentes et les réponses relatives à Azure Multi-Factor Authentication. Multi-Factor Authentication est une méthode permettant de vérifier l’identité d’un utilisateur qui requiert d’autres méthodes que le nom d’utilisateur et le mot de passe. Ce service fournit une couche de sécurité supplémentaire pour la connexion et les transactions de l’utilisateur."
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
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Forum Aux Questions d’Azure Multi-Factor Authentication


Ce forum aux questions répond aux questions courantes sur Azure Multi-Factor Authentication et sur l’utilisation du service Multi-Factor Authentication, y compris aux questions sur le modèle de facturation et l’ergonomie.

## Généralités

**Q : Comment puis-je obtenir de l'aide pour Azure Multi-Factor Authentication ?**

- [Recherche dans la Base de connaissances du Support Microsoft](https://www.microsoft.com/fr-FR/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)

  Recherchez des solutions aux problèmes techniques courants dans la base de connaissances.

- [Forums Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

  Parcourez les forums et recherchez des questions et des réponses techniques de la communauté ou posez votre question sur les [forums Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

- [Réinitialisation de mot de passe](mailto:phonefactorsupport@microsoft.com)

  Si vous êtes un client hérité de PhoneFactor et que vous avez des questions ou besoin d’aide pour réinitialiser un mot de passe, utilisez le lien [Réinitialisation de mot de passe](mailto:phonefactorsupport@microsoft.com) pour ouvrir une demande de support.

- [Support du serveur Azure Multi-Factor Authentication (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947)

  Utilisez ce lien pour contacter un professionnel du support Microsoft. Il vous posera quelques questions pour vous aider à déterminer vos options de support, par exemple par courrier électronique, par soumission en ligne ou par téléphone.



**Q : Comment les données utilisateur sont-elles gérées par le serveur Azure Multi-Factor Authentication ?**

Lorsque vous utilisez le serveur Multi-Factor Authentication, les données utilisateur sont stockées uniquement sur les serveurs locaux. Aucune donnée utilisateur persistante n'est stockée dans le cloud. Lorsque l’utilisateur effectue une authentification à deux facteurs, Multi-Factor Authentication envoie les données au service cloud Azure Multi-Factor Authentication à des fins d’authentification. Lorsque les demandes d’authentification sont envoyées au service cloud, les données des champs de la liste suivante sont envoyées dans la demande et les journaux afin qu’ils soient disponibles dans les rapports d’utilisation et d’authentification du client. Certains champs sont facultatifs, et vous pouvez les configurer dans le serveur Multi-Factor Authentication. La communication entre le serveur Multi-Factor Authentication et le service cloud Multi-Factor Authentication utilise le protocole SSL (Secure Sockets Layer) ou TLS (Transport Layer Security) sur le port 443 sortant. Les champs de données inclus dans les journaux d’authentification à deux facteurs sont les suivants :

- **ID unique** (soit le nom d’utilisateur soit l’ID de serveur Multi-Factor Authentication local)
- **Prénom et nom** (facultatif)
- **Adresse de messagerie** (facultatif)
- **Numéro de téléphone** (en cas d’utilisation de l’authentification par appel vocal ou SMS)
- **Jeton de l’appareil** (en cas d’utilisation de l’authentification par application mobile)
- **Mode d'authentification**
- **Résultat de l'authentification**
- **Nom du serveur Multi-Factor Authentication**
- **Adresse IP du serveur Multi-Factor Authentication**
- **Adresse IP du client** (si disponible)



En plus de ces champs, le résultat de l’authentification (réussite ou échec) et le motif de refus sont également stockés avec les données d’authentification et sont disponibles dans les rapports d’utilisation et d’authentification.




## Facturation

**Q : Mon organisation devra-t-elle payer les appels téléphoniques ou les SMS utilisés pour authentifier mes utilisateurs ?**

Tous les frais sont regroupés dans le coût du service par utilisateur ou par authentification. Les appels émis et les SMS envoyés aux utilisateurs avec Azure Multi-Factor Authentication ne sont pas facturés aux organisations. Les propriétaires de téléphones peuvent avoir à supporter des frais liés à l’itinérance ou d’autres coûts des opérateurs de services téléphoniques pour les appels téléphoniques ou les messages reçus.

**Q : Comment le service Azure Multi-Factor Authentication est-il facturé à l’organisation ?**

Azure Multi-Factor Authentication est disponible en tant que service autonome avec des options de facturation par utilisateur et par authentification, ou regroupé avec Azure Active Directory Premium, Enterprise Mobility Suite ou Enterprise Cloud Suite. Le service autonome est disponible sur une base de consommation qui est facturée chaque mois en fonction de l’engagement monétaire Azure d’une organisation, ou sur une base de licence annuelle par utilisateur disponible par le biais du Contrat Entreprise Microsoft, du programme de licence ouvert Microsoft, du programme des fournisseurs de solutions Microsoft Cloud (CSP) ou de façon directe.

>[AZURE.IMPORTANT]
Les régions en Australie sont disponibles aux clients ayant une présence commerciale en Australie et en Nouvelle-Zélande.

Modèle de facturation | Prix
------------- | ------------- |
Consommation par utilisateur (engagement monétaire Azure)| 1,40 $ par mois (authentifications illimitées)
Consommation par authentification (engagement monétaire Azure)|1,40 $ pour 10 authentifications
Licence annuelle par utilisateur (direct)|1,40 $ par mois (authentifications illimitées)
Licence annuelle par utilisateur (licence en volume)|Contactez votre [représentant Contrat Entreprise](https://www.microsoft.com/fr-FR/licensing/licensing-programs/enterprise.aspx)

**Q : Le modèle de facturation « par utilisateur » dépend-il du nombre d’utilisateurs configurés pour utiliser Multi-Factor Authentication ou du nombre d’utilisateurs qui effectuent les vérifications ?**

La facturation repose sur le nombre d’utilisateurs configurés pour utiliser Multi-Factor Authentication.

**Q : Comment fonctionne la facturation Multi-Factor Authentication ?**

Lorsqu’une organisation utilise le modèle d’utilisation et de facturation de la consommation par authentification et par utilisateur, l’administrateur de l’organisation choisit le type d’utilisation lorsqu’il crée un fournisseur Multi-Factor Authentication dans le portail Azure Classic. Cette ressource est facturée à l’abonnement Azure de l’organisation, comme c’est le cas pour les machines virtuelles, les sites web, etc. Lorsque vous utilisez le modèle basé sur licence, des licences Azure Multi-Factor Authentication sont achetées, puis affectées à des utilisateurs, comme pour Office 365 et d’autres produits soumis à abonnement.

**Q : Existe-t-il une version gratuite d’Azure Multi-Factor Authentication pour les administrateurs ?**

Un sous-ensemble des fonctionnalités d’Azure Multi-Factor Authentication, nommé Multi-Factor Authentication pour administrateurs Azure, est offert gratuitement aux membres du groupe d’administrateurs généraux Azure lorsqu’un fournisseur Azure Multi-Factor Authentication basé sur la consommation n’a pas été lié à l’instance correspondante d’Azure Active Directory. L’utilisation d’un fournisseur Multi-Factor Authentication met à niveau tous les administrateurs et utilisateurs du répertoire qui sont configurés pour utiliser Multi-Factor Authentication vers la version complète d’Azure Multi-Factor Authentication.

**Q : Existe-t-il une version gratuite d’Azure Multi-Factor Authentication pour les utilisateurs d’Office 365 ?**

Un sous-ensemble des fonctionnalités d’Azure Multi-Factor Authentication, nommé Multi-Factor Authentication pour Office 365, est offert gratuitement aux utilisateurs auxquels est assignée une licence Office 365 lorsqu’un fournisseur Azure Multi-Factor Authentication basé sur la consommation n’a pas été lié à l’instance correspondante d’Azure Active Directory. L’utilisation du fournisseur Multi-Factor Authentication met à niveau tous les administrateurs et utilisateurs du répertoire qui sont configurés pour utiliser Multi-Factor Authentication vers la version complète d’Azure Multi-Factor Authentication.

**Q : Mon organisation peut-elle passer de la facturation de la consommation par utilisateur à la facturation par authentification, et inversement, à tout instant ?**

Votre organisation choisit un modèle de facturation lorsqu’elle crée une ressource. Vous ne pouvez pas modifier le modèle de facturation une fois la ressource configurée. Vous pouvez, cependant, créer une nouvelle ressource Multi-Factor Authentication pour remplacer la ressource originale. Notez que les paramètres utilisateur et les options de configuration ne peuvent pas être transférés vers la nouvelle ressource.

**Q : Mon organisation peut-elle passer du modèle de facturation à la consommation au modèle basé sur licence, et inversement, à tout instant ?**

Votre organisation peut acheter des licences Azure Multi-Factor Authentication, Azure Active Directory Premium, Enterprise Mobility Suite et Enterprise Cloud Suite à tout moment. Lorsque des licences sont ajoutées à un répertoire qui dispose déjà d’un fournisseur Azure Multi-Factor Authentication Provider par utilisateur, la facturation basée sur la consommation est décrémentée en fonction du nombre de licences détenues. Si tous les utilisateurs configurés pour utiliser Multi-Factor Authentication ont une licence, l’administrateur peut supprimer le fournisseur Azure Multi-Factor Authentication. Une organisation ne peut pas mélanger la facturation de la consommation par authentification avec un modèle basé sur licence. Lorsqu’un fournisseur Multi-Factor Authentication par authentification est lié à un répertoire, l’organisation est facturée pour toutes les demandes de vérification Multi-Factor Authentication, indépendamment des licences détenues.

**Q : Mon organisation doit-elle utiliser et synchroniser les identités pour utiliser Azure Multi-Factor Authentication ?**

Azure Active Directory n’est pas requis en cas d’utilisation des modèles de facturation basés sur la consommation par l’organisation. La liaison d'un fournisseur Multi-Factor Authentication à un répertoire est facultative. Si votre organisation n’est pas liée à un répertoire, elle peut déployer le serveur Azure multi-Factor Authentication ou le SDK Azure Multi-Factor Authentication local. Azure Active Directory est requis pour le modèle basé sur licence car les licences sont ajoutées au répertoire lorsque vous les achetez et que vous les affectez à des utilisateurs dans le répertoire.


## Facilité d'utilisation

**Q : Que fait un utilisateur s’il ne reçoit pas de réponse sur son téléphone, ou si le téléphone n’est pas disponible pour l’utilisateur ?**

Si l’utilisateur a auparavant configuré un téléphone de secours, il doit réessayer et en sélectionner ce téléphone à l’invite sur la page de connexion. Si l’utilisateur n’a pas configuré d’autre méthode, il doit contacter l’administrateur de l’organisation et demander à l’administrateur de mettre à jour le numéro affecté au téléphone principal de l’utilisateur, mobile ou professionnel.


**Q : Que fait l’administrateur si un utilisateur le contacte au sujet d’un compte auquel l’utilisateur ne peut plus accéder ?**

L’administrateur peut réinitialiser le compte de l’utilisateur en lui demandant d’effectuer à nouveau le processus d’inscription. Pour en savoir plus, consultez [Gestion des paramètres utilisateur et des appareils avec Azure Multi-Factor Authentication dans le cloud](multi-factor-authentication-manage-users-and-devices.md).

**Q : Que fait un administrateur si le téléphone d’un utilisateur qui utilise des mots de passe d’applications est perdu ou volé ?**

L’administrateur peut supprimer tous les mots de passe d’applications de l’utilisateur afin d’empêcher tout accès non autorisé. Une fois que l’utilisateur dispose d’un appareil de remplacement, il peut recréer les mots de passe. Pour en savoir plus, consultez [Gestion des paramètres utilisateur et des appareils avec Azure Multi-Factor Authentication dans le cloud](multi-factor-authentication-manage-users-and-devices.md).

**Q : Que se passe-t-il si l’utilisateur ne peut pas se connecter à des applications sans navigateur ?**

- Un utilisateur qui est configuré pour utiliser Multi-Factor Authentication a besoin d’un mot de passe d’applications pour se connecter à certaines applications sans navigateur.
- L’utilisateur doit effacer (supprimer) les informations de connexion, redémarrer l’application et se connecter à l’aide de son nom d’utilisateur et du mot de passe de l’application.

Pour en savoir plus sur la création de mots de passe d’applications notamment, consultez [l’aide relative aux mots de passe d’applications](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Authentification moderne pour les clients Office 2013
>
> Les clients Office 2013 (y compris Outlook) prennent en charge de nouveaux protocoles d’authentification. Vous pouvez configurer Office 2013 pour prendre en charge Multi-Factor Authentication. Une fois Office 2013 configuré, les mots de passe d’applications ne sont pas requis pour les clients Office 2013. Pour plus d’informations, consultez [l’annonce de la version préliminaire publique de l’authentification moderne Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**Q : Que fait un utilisateur s’il ne reçoit pas un message texte ou s’il répond à un message texte bidirectionnel mais que la vérification expire ?**

Le service Azure Multi-Factor Authentication envoie des messages via des agrégateurs de SMS. De nombreux facteurs peuvent affecter la fiabilité de remise et de réception des messages, notamment l’agrégation utilisée, le pays de destination, l’opérateur de téléphonie mobile de l’utilisateur et la puissance du signal. Par conséquent, la remise de messages texte et la réception des SMS de réponse en cas de SMS bidirectionnel ne sont pas garanties. Nous recommandons d’utiliser des SMS unidirectionnels plutôt que bidirectionnels dans la mesure du possible. Le SMS unidirectionnel est plus fiable et évite aux utilisateurs des frais de SMS internationaux en cas de réponse à un message envoyé à partir d’un autre pays.

Les vérifications de SMS sont également plus fiables dans certains pays ou régions comme les États-Unis et le Canada. Nous encourageons les utilisateurs qui rencontrent des difficultés pour recevoir des messages avec fiabilité lors de l’utilisation d’Azure Multi-Factor Authentication à sélectionner la méthode d’application mobile ou d’appel téléphonique à la place. La méthode d’authentification par application mobile est intéressante car l’utilisateur peut recevoir des notifications d’applications mobiles sur des connexions à la fois cellulaires et Wi-Fi. En outre, le code secret de l’application mobile s’affiche même si l’appareil n’a aucun signal. L'application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

**Q : Puis-je utiliser des jetons matériels avec le serveur Azure Multi-Factor Authentication ?**

Si vous utilisez le serveur Azure Multi-Factor Authentication, vous pouvez importer des jetons de mots de passe à usage unique et durée définie (TOTP) d’authentification ouverte tierce (OATH), puis les utiliser pour Multi-Factor Authentication. Nous prenons en charge l’importation de jetons OATH TOTP tiers dans un format PSKC (Portable Symmetric Key Container) plus ancien que Gemalto peut produire pour ses jetons, et l’importation de jetons au format CSV. Si vous importez des jetons au format CSV, le fichier CSV doit contenir un numéro de série, une clé secrète au format Base32 et un intervalle de temps (généralement 30 secondes).

Vous pouvez utiliser des jetons ActiveIdentity, qui sont des jetons OATH TOTP, si vous pouvez placer le fichier de clé secrète dans un fichier CSV que vous pouvez importer sur le serveur Azure Multi-Factor Authentication. Vous pouvez utiliser des jetons OATH avec les services de fédération Active Directory (AD FS) ; le protocole RADIUS (Remote Authentication Dial-In User Service), lorsque le système client peut traiter les réponses aux demandes d’accès ; et l’authentification par formulaires Internet Information Server (IIS).

**Q : Puis-je utiliser le serveur Azure Multi-Factor Authentication pour sécuriser les Services Terminal Server ?**

Oui, mais si vous utilisez Windows Server 2012 R2 ou une version ultérieure, vous pouvez le faire uniquement avec la Passerelle des services Bureau à distance (passerelle RD).

Les modifications de sécurité dans Windows Server 2012 R2 ont changé la façon dont le serveur Azure Multi-Factor Authentication se connecte au package de sécurité de l’autorité de sécurité locale (LSA) dans Windows Server 2012 et les versions antérieures. Pour les versions de Terminal Services sous Windows 2012 ou une version antérieure, vous pouvez tout simplement [sécuriser une application avec l’authentification Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Si vous utilisez Windows Server 2012 R2, vous devrez utiliser une passerelle RD.

**Q : Pourquoi un utilisateur recevrait-il un appel Multi-Factor Authentication d’un appelant anonyme après avoir configuré l’ID de l’appelant ?**

Lorsque des appels Multi-Factor Authentication sont transmis sur le réseau téléphonique public, ils sont parfois acheminés par le biais d’un opérateur qui ne prend pas en charge les ID d’appelant. Pour cette raison, l’ID de l’appelant n’est pas garanti, même si le système Multi-Factor Authentication l’envoie toujours.


## Erreurs

**Q : Que fait un utilisateur s’il voit un message d’erreur « La demande d’authentification ne concerne pas un compte activé » lorsqu’il s’authentifie avec les notifications d’applications mobiles ?**

Suivez cette procédure :

1. Accédez à [votre profil du portail Azure](https://account.activedirectory.windowsazure.com/profile/) et connectez-vous avec votre compte professionnel.
2. Si nécessaire, cliquez sur **Autres options de vérification**, puis cliquez sur une autre option pour effectuer la vérification de votre compte.
3. Cliquez sur **Vérification de sécurité supplémentaire**.
4. Supprimez le compte existant de l’application mobile.
5. Cliquez sur **Configurer** et suivez les instructions pour configurer à nouveau l’application mobile.




**Q : Que fait un utilisateur s’il voit un message d’erreur 0x800434D4L lorsqu’il tente de se connecter à l’aide d’une application sans navigateur ?**

Actuellement, l’utilisateur ne peut utiliser la vérification de sécurité supplémentaire qu’avec les applications et les services auxquels il peut accéder avec son navigateur. Les applications sans navigateur (également appelées *applications clientes riches*) qui sont installées sur un ordinateur local, comme Windows PowerShell, ne fonctionnent pas avec les comptes qui requièrent la vérification de sécurité supplémentaire. Dans ce cas, l’utilisateur peut constater que l’application génère une erreur 0x800434D4L.

La solution consiste à disposer de comptes d’utilisateur distincts pour les opérations d’administration et les autres. Vous pouvez ultérieurement lier les boîtes de réception de votre compte d’administrateur et d’un compte non administrateur pour vous connecter à Outlook à l’aide de votre compte non administrateur. Pour plus d’informations, consultez [Permettre à un administrateur d’ouvrir et d’afficher le contenu de la boîte aux lettres d’un utilisateur](http://help.outlook.com/141/gg709759.aspx?sl=1).

<!---HONumber=AcomDC_0921_2016-->