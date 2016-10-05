<properties
   pageTitle="Azure AD Connect : Mettre à niveau depuis DirSync | Microsoft Azure"
   description="Découvrez comment mettre à niveau la synchronisation d’annuaires vers Azure AD Connect. Cet article décrit les étapes de mise à niveau à partir de DirSync vers Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="08/19/2016"
   ms.author="andkjell;shoatman;billmath"/>

# Azure AD Connect : Mise à niveau à partir de DirSync
Azure AD Connect est le successeur de DirSync. Cette rubrique explique les différentes façons de procéder à une mise à niveau à partir de DirSync. Ces étapes ne fonctionnent pas pour la mise à niveau à partir d’une autre version d’Azure AD Connect ou d’Azure AD Sync.

Avant de commencer l’installation d’Azure AD Connect, veillez à [télécharger Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) et effectuer les étapes préalables décrites dans [Azure AD Connect : matériel et conditions préalables](active-directory-aadconnect-prerequisites.md). En particulier, il est recommandé de se renseigner sur les éléments suivants, dans la mesure où ces zones sont différentes de celles de DirSync :

- La version requise de .Net et de PowerShell. Les versions plus récentes doivent être sur le serveur dont DirSync a besoin.
- La configuration du serveur proxy. Si vous utilisez un serveur proxy pour accéder à internet, ce paramètre doit être configuré avant d’effectuer la mise à niveau. DirSync a toujours utilisé le serveur proxy configuré pour l’utilisateur qui effectue l’installation, alors qu’Azure AD Connect utilise les paramètres de l’ordinateur.
- Les URL doivent être ouvertes dans le serveur proxy. Pour les scénarios de base, qui sont également pris en charge par DirSync, les exigences sont les mêmes. Si vous souhaitez utiliser une des nouvelles fonctionnalités d’Azure AD Connect, certaines nouvelles URL doivent être ouvertes.

Si vous n’effectuez pas une mise à niveau à partir de DirSync, consultez la section [Documentation connexe](#related-documentation) pour d’autres scénarios.

## Effectuer une mise à niveau à partir de DirSync
Selon votre déploiement DirSync actuel, il existe différentes options pour la mise à niveau. Si la durée de mise à niveau attendue est inférieure à 3 heures, nous vous recommandons de l’effectuer sur place. Si elle est supérieure à 3 heures, nous vous recommandons d’effectuer un déploiement en parallèle sur un autre serveur. Nos estimations montrent que, si vous avez plus de 50 000 objets, la mise à niveau prendra plus de 3 heures.

Scénario |  
---- | ----
[Mise à niveau sur place](#in-place-upgrade) | Option recommandée si la mise à niveau doit prendre moins de 3 heures.
[Déploiement en parallèle](#parallel-deployment) | Option recommandée si la mise à niveau doit prendre plus de 3 heures.

>[AZURE.NOTE] Lorsque vous envisagez de mettre à niveau à partir de DirSync vers Azure AD Connect, ne désinstallez pas DirSync vous-même avant la mise à niveau. Azure AD Connect lit et migre la configuration de DirSync et procède à la désinstallation après l'inspection du serveur.

**Mise à niveau sur place** L’Assistant affiche la durée estimée de l’opération. Cette estimation est basée sur l’hypothèse qu’il faut 3 heures pour mettre à niveau une base de données contenant 50 000 objets (utilisateurs, contacts et groupes). Azure AD Connect recommande une mise à niveau sur place si votre base de données contient moins de 50 000 objets. Si vous décidez de continuer, vos paramètres actuels sont appliqués automatiquement au cours de la mise à niveau et votre serveur rétablit automatiquement la synchronisation active.

Si vous souhaitez effectuer une migration de la configuration et un déploiement en parallèle, vous pouvez ne pas effectuer la mise à niveau sur place. Vous pouvez, par exemple, prendre l'opportunité d'actualiser le matériel et le système d'exploitation. Consultez la section sur le [déploiement en parallèle](#parallel-deployment) pour plus d'informations.

**Déploiement en parallèle** Un déploiement en parallèle est recommandé si vous avez plus de 50 000 objets. Il permet d’éviter les retards opérationnels rencontrés par vos utilisateurs. L’installation d’Azure AD Connect tente d’estimer le temps d’arrêt associé à la mise à niveau, mais si vous avez déjà mis à niveau DirSync, votre propre expérience est sans doute plus fiable.

### Configurations de DirSync prises en charge à mettre à niveau
Les modifications de configuration suivantes sont prises en charge dans DirSync lors de la mise à niveau :

- Filtrage domaine et unité organisationnelle
- Autre ID (UPN)
- Synchronisation de mot de passe et paramètres hybrides d'Exchange
- Vos paramètres domaine/forêt et Azure AD
- Filtrage basé sur les attributs de l'utilisateur

La modification suivante ne peut pas être mise à niveau. Si vous avez cette configuration, la mise à niveau est bloquée :

- Modifications de DirSync non prises en charge, par exemple : attributs supprimés et utilisation d’une DLL d’extension personnalisée

![Blocage de la mise à niveau](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

Dans ces cas, il est recommandé d’installer un nouveau serveur Azure AD Connect en [mode de préproduction](active-directory-aadconnectsync-operations.md#staging-mode) et de vérifier l’ancienne configuration de DirSync et la nouvelle configuration d’Azure AD Connect. Appliquez de nouveau les éventuelles modifications à l’aide de la configuration personnalisée, comme décrit dans [Azure AD Connect Sync : configuration personnalisée](active-directory-aadconnectsync-whatis.md).

Les mots de passe utilisés par DirSync pour les comptes de service ne peuvent pas être récupérés et ne sont pas migrés. Ces mots de passe sont réinitialisés lors de la mise à niveau.

### Étapes générales pour la mise à niveau à partir de DirSync vers Azure AD Connect

1. Bienvenue dans Azure AD Connect
2. Analyse de la configuration DirSync actuelle
3. Collecte du mot de passe administrateur global Azure AD
4. Collecte des informations d’identification pour le compte d’administrateur entreprise (utilisé uniquement lors de l’installation d’Azure AD Connect)
5. Installation d'Azure AD Connect
    * Désinstaller DirSync (ou le désactiver temporairement)
	* Installer Azure AD Connect
	* Synchronisation, si nécessaire

Des étapes supplémentaires sont nécessaires dans les cas suivants :

* Vous utilisez actuellement un serveur SQL complet, local ou distant
* Vous avez plus de 50 000 objets à synchroniser

## Mise à niveau sur place

1. Lancez le programme d'installation d'Azure AD Connect (MSI).
2. Lisez et acceptez les termes du contrat de licence et la déclaration de confidentialité. ![Bienvenue dans Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. Cliquez sur le bouton Suivant pour analyser votre installation DirSync existante. ![Analyse de l’installation existante de la synchronisation d’annuaires](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. Une fois l’analyse terminée, nous proposons des recommandations sur la marche à suivre.
    - Si vous utilisez SQL Server Express et que vous avez moins de 50 000 objets, l'écran suivant s'affiche : ![Analyse terminée, mise à niveau à partir de la synchronisation d’annuaires prête](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
    - Si vous utilisez un serveur SQL complet pour DirSync, la page suivante s’affiche : ![Analyse terminée, mise à niveau à partir de la synchronisation d’annuaires prête](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png) Les informations concernant le serveur de base de données SQL Server utilisé par DirSync s’affichent. Apportez des modifications, si nécessaire. Cliquez sur **Suivant** pour continuer l'installation.
    - Si vous avez plus de 50 000 objets, l’écran suivant s’affiche : ![Analyse terminée, mise à niveau à partir de la synchronisation d’annuaires prête](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png) Pour procéder à une mise à niveau sur place, cochez la case en regard du message : **Continuez à mettre à niveau DirSync sur cet ordinateur.** Pour effectuer un [déploiement en parallèle](#parallel-deployment), exportez les paramètres de configuration de DirSync et copiez-les sur le nouveau serveur.
5. Indiquez le mot de passe du compte que vous utilisez actuellement pour vous connecter à Azure AD. Il doit s’agir du compte actuellement utilisé par DirSync. ![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png) Si vous recevez une erreur et que vous avez des problèmes de connectivité, consultez [Résoudre les problèmes de connectivité liés à Azure AD Connect](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Indiquez un compte d'administrateur d'entreprise pour Active Directory. ![Entrez vos informations d'identification ADDS.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Vous êtes prêt à passer à la configuration. Lorsque vous cliquez sur **Mettre à niveau**, DirSync est désinstallé, puis Azure AD Connect est configuré et lance la synchronisation. ![Prêt à configurer](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Une fois l’installation terminée, déconnectez-vous puis reconnectez-vous à Windows avant d’utiliser le gestionnaire Synchronization Service Manager, l’éditeur de règles de synchronisation ou de tenter d’apporter d’autres modifications à la configuration.

## Déploiement en parallèle

### Exportation de la configuration de DirSync
**Déploiement en parallèle avec plus de 50 000 objets**

Si vous avez plus de 50 000 objets, l’installation d’Azure AD Connect recommande un déploiement en parallèle.

Un écran semblable à celui-ci s’affiche :

![Analyse terminée](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Pour effectuer un déploiement en parallèle, vous devez effectuer les opérations suivantes :

- Cliquez sur le bouton **Paramètres d'exportation**. Lorsque vous installez Azure AD Connect sur un autre serveur, ces paramètres sont migrés de votre installation DirSync vers votre nouvelle installation Azure AD Connect.

Une fois vos paramètres exportés, vous pouvez quitter l’Assistant Azure AD Connect sur le serveur de synchronisation d’annuaires. Passez à l'étape suivante pour [installer Azure AD Connect sur un serveur distinct](#installation-of-azure-ad-connect-on-separate-server)

**Déploiement en parallèle avec moins de 50 000 objets**

Si vous avez moins de 50 000 objets mais que vous souhaitez effectuer un déploiement en parallèle, procédez comme suit :

1. Lancez le programme d’installation d’Azure AD Connect (MSI).
2. Lorsque l'écran **Bienvenue à Azure AD Connect** s'affiche, quittez l'Assistant d'installation en cliquant sur le X en haut à droite de la fenêtre.
3. Ouvrez une invite de commandes.
4. À partir de l'emplacement d'installation d'Azure AD Connect (par défaut C:\\Program Files\\Microsoft Azure Active Directory Connect), exécutez la commande suivante : `AzureADConnect.exe /ForceExport`.
5. Cliquez sur le bouton **Paramètres d'exportation**. Lorsque vous installez Azure AD Connect sur un autre serveur, ces paramètres sont migrés de votre installation DirSync vers votre nouvelle installation Azure AD Connect.

![Analyse terminée](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

Une fois vos paramètres exportés, vous pouvez quitter l’Assistant Azure AD Connect sur le serveur de synchronisation d’annuaires. Passez à l'étape suivante pour [installer Azure AD Connect sur un serveur distinct](#installation-of-azure-ad-connect-on-separate-server)

### Installation d'Azure AD Connect sur un serveur distinct

Lorsque vous installez Azure AD Connect sur un nouveau serveur, le système présuppose que vous voulez effectuer une nouvelle installation d’Azure AD Connect. Comme vous souhaitez utiliser la configuration de DirSync, quelques étapes supplémentaires sont nécessaires :

1. Lancez le programme d’installation d’Azure AD Connect (MSI).
2. Lorsque l'écran **Bienvenue à Azure AD Connect** s'affiche, quittez l'Assistant d'installation en cliquant sur le X en haut à droite de la fenêtre.
3. Ouvrez une invite de commandes.
4. À partir de l'emplacement d'installation d'Azure AD Connect (par défaut C:\\Program Files\\Microsoft Azure Active Directory Connect), exécutez la commande suivante : `AzureADConnect.exe /migrate`. L'Assistant d'installation d'Azure AD Connect démarre et affiche l'écran suivant : ![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. Sélectionnez le fichier de paramètres exportés à partir de votre installation de la synchronisation d’annuaires.
6. Configurez les options avancées :
    - Un emplacement d'installation personnalisé pour Azure AD Connect.
	- Une instance de SQL Server (par défaut, Azure AD Connect installe SQL Server 2012 Express). N'utilisez pas la même instance de base de données que celle de votre serveur DirSync.
	- Un compte de service utilisé pour la connexion à SQL Server (si votre base de données SQL Server est distante, ce compte doit être un compte de service du domaine). Ces options s'affichent dans cet écran : ![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. Cliquez sur **Suivant**.
8. Sur la page **Prêt pour la configuration**, laissez la case **Démarrez le processus de synchronisation dès que la configuration est terminée** cochée. Le serveur étant maintenant en [mode de préproduction](active-directory-aadconnectsync-operations.md#staging-mode), les modifications ne sont pas exportées vers Azure AD.
9. Cliquez sur **Installer**.
10. Une fois l’installation terminée, déconnectez-vous puis reconnectez-vous à Windows avant d’utiliser le gestionnaire Synchronization Service Manager, l’éditeur de règles de synchronisation ou de tenter d’apporter d’autres modifications à la configuration.

>[AZURE.NOTE] La synchronisation entre Windows Server Active Directory et Azure Active Directory commence, mais aucune modification n’est exportée vers Azure AD. Les modifications ne peuvent être exportées activement que par un seul outil de synchronisation à la fois. Cet état est appelé [mode de préproduction](active-directory-aadconnectsync-operations.md#staging-mode).

### Vérifiez qu'Azure AD Connect est prêt à commencer la synchronisation

Pour vérifier qu’Azure AD Connect est prêt à remplacer DirSync, vous devrez ouvrir le **Synchronization Service Manager** dans le groupe **Azure AD Connect** à l’aide du menu Démarrer.

Dans l’application, cliquez sur l’onglet **Operations**. Dans cet onglet, vérifiez que les opérations suivantes ont abouti :

- Importation sur le connecteur Active Directory
- Importation sur le connecteur Azure Active Directory
- Synchronisation complète sur le connecteur Active Directory
- Synchronisation complète sur le connecteur Azure Active Directory

![Importation et synchronisation terminées](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

Examinez le résultat de ces opérations et vérifiez qu'il n'y a aucune erreur.

Si vous souhaitez afficher et examiner les modifications qui sont sur le point d’être exportées vers Azure AD, lisez comment vérifier la configuration en [mode de préproduction](active-directory-aadconnectsync-operations.md#staging-mode). Apportez les modifications de la configuration requises jusqu'à ce que tout soit correct.

Vous êtes prêt à passer de DirSync à Azure AD lorsque vous avez effectué ces étapes et que vous êtes satisfait du résultat.

### Désinstallation de DirSync (ancien serveur)

- Dans **Programmes et fonctionnalités**, recherchez **Outil de synchronisation Windows Azure Active Directory**.
- Désinstallez **Outil de synchronisation Windows Azure Active Directory**.
- La désinstallation peut prendre jusqu’à 15 minutes.

Si vous préférez désinstaller DirSync ultérieurement, vous pouvez également arrêter le serveur ou désactiver le service. Si une erreur survient, cette méthode vous permet de le réactiver. Toutefois, comme il est peu probable que l’étape suivante échoue, cette opération ne devrait pas être nécessaire.

Une fois DirSync désinstallé ou désactivé, aucun serveur actif n’effectue d’exportations vers Azure AD. L’étape suivante consistant à activer Azure AD Connect, doit aboutir pour que les modifications dans votre Active Directory local restent synchronisées avec Azure AD.

### Activation d'Azure AD Connect (nouveau serveur)
Après l’installation, lorsque vous rouvrez Azure AD Connect, vous aurez la possibilité d’apporter des modifications supplémentaires à la configuration. Démarrez **Azure AD Connect** depuis le menu Démarrer ou à partir du raccourci sur le bureau. Assurez-vous que vous n'essayez pas d'exécuter à nouveau le programme MSI d'installation.

Les éléments suivants doivent s’afficher :

![Tâches supplémentaires](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

- Sélectionnez **Configuration du mode de préproduction**.
- Désactivez le mode de préproduction en désactivant la case à cocher **Mode de préproduction activé**.

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

- Cliquez sur le bouton **Suivant**.
- Dans la page Confirmation, cliquez sur le bouton **Installer**.

Azure AD Connect est maintenant votre serveur actif.

## Étapes suivantes
Azure AD Connect étant installé, vous pouvez passer à [Vérification de l’installation et affectation des licences](active-directory-aadconnect-whats-next.md).

Pour en savoir plus sur ces nouvelles fonctionnalités, qui sont activées lors de l’installation, consultez les pages [Azure AD Connect : Mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md), [Azure AD Connect Sync : Prévention des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) et [Utilisation d’Azure AD Connect Health pour la synchronisation](active-directory-aadconnect-health-sync.md).

Pour en savoir plus sur ces sujets courants, consultez l’article [Planificateur Azure AD Connect Sync](active-directory-aadconnectsync-feature-scheduler.md).

En savoir plus sur l'[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

## Documentation connexe

Rubrique |  
--------- | ---------
Présentation d’Azure AD Connect | [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
Mise à niveau depuis une version antérieure d’Azure AD Connect | [Mise à niveau depuis une version antérieure d’Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md)
Installation à l’aide de la configuration rapide | [Installation rapide pour Azure AD Connect](active-directory-aadconnect-get-started-express.md)
Installation à l’aide des paramètres personnalisés | [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Comptes utilisés pour l’installation | [Informations supplémentaires sur les autorisations et les comptes Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)

<!---HONumber=AcomDC_0928_2016-->