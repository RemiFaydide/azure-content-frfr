<properties
	pageTitle="Installation de l'agent Azure AD Connect Health | Microsoft Azure"
	description="Il s'agit de la page Azure AD Connect Health qui décrit l'agent l'installation pour AD FS et la synchronisation."
	services="active-directory"
	documentationCenter=""
	authors="karavar"
	manager="femila"
	editor="karavar"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/05/2016"
	ms.author="vakarand"/>


# Installation de l'agent Azure AD Connect Health

Ce document vous guide dans l’installation et la configuration des agents Azure AD Connect Health. Vous pouvez télécharger les agents [ici](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent) :

## 	Configuration requise
Le tableau qui suit est une liste d’exigences d’utilisation d’Azure AD Connect Health.

| Prérequis | Description|
| ----------- | ---------- |
|Azure AD Premium| Azure AD Connect Health est une fonctionnalité d’Azure AD Premium qui nécessite Azure AD Premium. </br></br>Pour plus d’informations, consultez [Prise en main d’Azure AD Premium](active-directory-get-started-premium.md).</br>Pour démarrer une période d’évaluation gratuite de 30 jours, consultez [Démarrer l’essai gratuit.](https://azure.microsoft.com/trial/get-started-active-directory/)|
|Vous devez être administrateur général de votre instance Azure AD pour démarrer Azure AD Connect Health|Par défaut, seuls les administrateurs généraux peuvent installer et configurer les agents d’intégrité afin qu’ils puissent être démarrés, accéder au portail et exécuter des opérations au sein d’Azure AD Connect Health. Pour plus d’informations, consultez la section [Administration de votre annuaire Azure AD](active-directory-administer.md). <br><br> À l’aide du contrôle d’accès en fonction du rôle, vous pouvez accorder l’accès à Azure AD Connect Health à d’autres utilisateurs dans votre organisation. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle pour Azure AD Connect Health.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Important :** Le compte que vous utilisez lors de l’installation des agents doit être un compte professionnel ou scolaire. Il ne peut pas s’agir d’un compte Microsoft. Pour plus d’informations, consultez [Inscription à Azure en tant qu’organisation](sign-up-organization.md)
|L’agent Azure AD Connect Health est installé sur chaque serveur cible| Azure AD Connect Health requiert qu’un agent soit installé sur les serveurs cibles pour fournir les données affichées dans le portail. </br></br>Par exemple, pour obtenir des données sur votre infrastructure locale AD FS, l’agent doit être installé sur les serveurs AD FS, les serveurs proxy AD FS et les serveurs proxy d’application web. De même, pour obtenir des données sur votre infrastructure locale AD DS, l’agent doit être installé sur les contrôleurs de domaine. </br></br>**Important :** Le compte que vous utilisez lors de l’installation des agents doit être un compte professionnel ou scolaire. Il ne peut pas s’agir d’un compte Microsoft. Pour plus d’informations, consultez [Inscription à Azure en tant qu’organisation](sign-up-organization.md)|
|Connectivité sortante vers les points de terminaison de service Azure|Pendant l’installation et l’exécution, l’agent nécessite une connectivité vers les points de terminaison de service Azure AD Connect Health répertoriés ci-dessous. Si vous bloquez la connectivité sortante, assurez-vous d’ajouter les éléments suivants à la liste autorisée : </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.queue.core.windows.net</li><li>adhsprodwus.servicebus.windows.net - Port: 5671 </li><li>https://management.azure.com </li><li>https://s1.adhybridhealth.azure.com/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
|Ports du pare-feu sur le serveur qui exécute l’agent.| L’agent requiert que les ports de pare-feu suivants soient ouverts pour pouvoir communiquer avec les points de terminaison du service Azure AD Health.</br></br><li>Port TCP/UDP 443</li><li>Port TCP/UDP 5671</li>
|Autoriser les sites web suivants en cas d’activation de la sécurité renforcée d’IE|Les sites web suivants doivent être autorisés si la sécurité renforcée d’Internet Explorer est activée sur le serveur où l’agent sera installé.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>Le serveur de fédération pour votre organisation approuvé par Azure Active Directory. Par exemple : https://sts.contoso.com</li>




## Installation de l'agent Azure AD Connect Health pour AD FS
Pour démarrer l’installation de l’agent, double-cliquez sur le fichier .exe que vous avez téléchargé. Sur le premier écran, cliquez sur Installer.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Une fois l’installation terminée, cliquez sur Configurer maintenant.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Cette action lance une invite de commandes suivie par certains éléments PowerShell qui exécuteront Register-AzureADConnectHealthADFSAgent. Vous serez invité à vous connecter à Azure. Connectez-vous.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


Une fois que vous êtes connecté, PowerShell continue. À l’issue du processus, vous pouvez fermer PowerShell ; la configuration est terminée.

À ce stade, les services doivent être démarrés automatiquement. L’agent surveille et collecte désormais les données. Gardez à l’esprit que vous verrez des avertissements s’afficher dans la fenêtre PowerShell si vous n’avez pas satisfait la configuration requise décrite dans les sections précédentes. Assurez-vous de disposer de la configuration requise [ici](active-directory-aadconnect-health-agent-install.md#requirements) avant d’installer l’agent. La capture d’écran qui suit est un exemple de ces erreurs.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Pour vérifier que l’agent a été installé, ouvrez les services et procédez aux vérifications suivantes. Si vous avez terminé la configuration, ces services doivent s’exécuter. Dans le cas contraire, vous devez impérativement terminer la configuration pour exécuter les services.

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


### Installation de l’agent sur les serveurs Windows Server 2008 R2

Pour les serveurs Windows Server 2008 R2, procédez comme suit :

1. Assurez-vous que le serveur est exécuté au Service Pack 1 ou une version supérieure.
1. Désactivez la Configuration de sécurité renforcée pour l’installation de l’agent :
1. Installez Windows PowerShell 4.0 sur chacun des serveurs avant d’installer l’agent AD Health. Pour installer Windows PowerShell 4.0 :
 - Installez [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) en cliquant sur le lien suivant pour télécharger le programme d’installation hors connexion.
 - Installer PowerShell ISE (à partir des fonctionnalités de Windows)
 - Installez [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Installez Internet Explorer version 10 ou supérieure sur le serveur. Cette installation est requise par le service Health, afin de vous authentifier avec vos informations d’identification d’administrateur Azure.
1. Pour plus d’informations sur l’installation de Windows PowerShell 4.0 sur Windows Server 2008 R2, consultez l’article wiki [ici](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### Activer l’audit pour AD FS

Pour que la fonctionnalité d’analyse de l’utilisation puisse collecter et analyser les données, l’agent Azure AD Connect Health doit avoir les informations à disposition dans les journaux d’audit AD FS. Ces journaux ne sont pas activés par défaut. Cette règle s’applique uniquement aux serveurs de fédération AD FS. Il est inutile d’activer l’audit sur les serveurs proxy AD FS ou sur les serveurs proxy d’application web. Utilisez les procédures suivantes pour activer l’audit AD FS et localiser les journaux d’audit AD FS.

#### Pour activer l’audit pour AD FS 2.0

1. Cliquez sur **Démarrer**, pointez sur **Programmes**, pointez sur **Outils d’administration**, puis cliquez sur **Stratégie de sécurité locale**.
2. Accédez au dossier **Security Settings\\Local Policies\\User Rights Management**, puis double-cliquez sur Générer des audits de sécurité.
3. Sur l’onglet **Paramètre de sécurité locale**, vérifiez que le compte de service AD FS 2.0 est répertorié. S’il n’est pas présent, cliquez sur **Ajouter un utilisateur ou un groupe** et ajoutez-le à la liste, puis cliquez sur **OK**.
4. Ouvrez une invite de commandes avec des privilèges élevés et exécutez la commande suivante pour activer l’audit.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Fermez Stratégie de sécurité locale, puis ouvrez le composant logiciel enfichable Gestion. Pour ouvrir le composant logiciel enfichable Gestion, cliquez sur **Démarrer**, pointez sur **Programmes**, pointez sur **Outils d’administration**, puis cliquez sur Gestion AD FS 2.0.
6. Dans le volet Actions, cliquez sur Modifier les propriétés du service FS (Federation Service).
7. Dans la boîte de dialogue **Propriétés du service FS**, cliquez sur l’onglet **Événements**.
8. Cochez les cases **Audits des succès** et **Audits des échecs**.
9. Cliquez sur **OK**.

#### Pour activer l’audit pour AD FS sur Windows Server 2012 R2

1. Ouvrez **Stratégie de sécurité locale** en ouvrant **Gestionnaire de serveur** sur l’écran d’accueil, ou Gestionnaire de serveur dans la barre des tâches sur le bureau, puis cliquez sur **Outils/Stratégie de sécurité locale**.
2. Accédez au dossier **Security Settings\\Local Policies\\User Rights Assignment**, puis double-cliquez sur **Générer des audits de sécurité**.
3. Sous l’onglet **Paramètre de sécurité locale**, vérifiez que le compte de service AD FS est répertorié. S’il n’est pas présent, cliquez sur **Ajouter un utilisateur ou un groupe** et ajoutez-le à la liste, puis cliquez sur **OK**.
4. Ouvrez une invite de commandes avec des privilèges élevés et exécutez la commande suivante pour activer l’audit : <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Fermez **Stratégie de sécurité locale**, puis ouvrez le composant logiciel enfichable **Gestion AD FS** (dans Gestionnaire de serveur, cliquez sur Outils, puis sélectionnez Gestion AD FS).
6. Dans le volet Actions, cliquez sur **Modifier les propriétés du service de fédération**.
7. Dans la boîte de dialogue Propriétés du service de fédération, cliquez sur l’onglet **Événements**.
8. Sélectionnez les cases **Audits des succès et Audits des échecs**, puis cliquez sur **OK**.






#### Pour localiser les journaux d’audit AD FS


1. Ouvrez l’**Observateur d’événements**.
2. Accédez à Journaux Windows et sélectionnez **Sécurité**.
3. Sur la droite, cliquez sur **Filtrer les journaux actuels**.
4. Dans Source de l’événement, sélectionnez **Audit AD FS**.

![Journaux d’audit AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Si vous disposez d’une stratégie de groupe qui désactive l’audit AD FS, l’agent Azure AD Connect Health ne pourra pas collecter les informations. Assurez-vous de ne pas disposer de stratégie de groupe susceptible de désactiver l’audit.

[//]: # "Début de la section de configuration de l'agent proxy"

## Installation de l'agent Azure AD Connect Health pour la synchronisation
L'agent Azure AD Connect Health pour la synchronisation est installé automatiquement dans la dernière version d'Azure AD Connect. Pour utiliser Azure AD Connect pour la synchronisation, vous devez télécharger la dernière version d'Azure AD Connect et l'installer. Vous pouvez télécharger la dernière version [ici](http://www.microsoft.com/download/details.aspx?id=47594).

Pour vérifier que l’agent a été installé, ouvrez les services et procédez aux vérifications suivantes. Si vous avez terminé la configuration, ces services doivent s’exécuter. Dans le cas contraire, vous devez impérativement terminer la configuration pour exécuter les services.

- Azure AD Connect Health Sync Insights Service
- Azure AD Connect Health Sync Monitoring Service

![Vérifier Azure AD Connect Health pour la synchronisation](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] N'oubliez pas que l'utilisation d'Azure AD Connect Health requiert Azure AD Premium. Si vous ne disposez pas d'Azure AD Premium, vous ne serez pas en mesure d'effectuer la configuration dans le portail Azure. Vous trouverez plus d'informations sur la configuration requise [ici](active-directory-aadconnect-health-agent-install.md#requirements).


## Enregistrement manuel d’Azure AD Connect Health pour la synchronisation
Si l’enregistrement de l’agent Azure AD Connect Health pour la synchronisation échoue après avoir correctement installé Azure AD Connect, vous pouvez utiliser la commande PowerShell suivante pour enregistrer l’agent manuellement.

>[AZURE.IMPORTANT] Cette commande PowerShell est uniquement requise si l’enregistrement de l’agent échoue après l’installation d’Azure AD Connect.

La commande PowerShell ci-dessous est requise UNIQUEMENT lorsque l’enregistrement de l’Agent d’intégrité échoue, même après une installation et une configuration réussies d’Azure AD Connect. Dans ce cas, les services Azure AD Connect Health ne démarreront PAS tant que l’agent n’a pas été correctement enregistré.

Vous pouvez enregistrer manuellement l’agent Azure AD Connect Health pour la synchronisation à l’aide de la commande PowerShell suivante :

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

La commande prend les paramètres qui suivent :

- AttributeFiltering : $true (par défaut), si Azure AD Connect ne synchronise pas le jeu d’attributs par défaut et a été personnalisé pour utiliser un jeu d’attributs filtré. Dans le cas contraire, c’est le paramètre $false qui s’applique.
- StagingMode : $false (par défaut), si le serveur Azure AD Connect n’est PAS en mode de préproduction, $true s’applique si le serveur est configuré en mode de préproduction.

Lorsque vous êtes invité à vous authentifier, vous devez utiliser le compte d’administrateur général (tel que admin@domain.onmicrosoft.com) que vous avez utilisé pour la configuration d’Azure AD Connect.

## Installation de l’agent Azure AD Connect Health pour AD DS
Pour démarrer l’installation de l’agent, double-cliquez sur le fichier .exe que vous avez téléchargé. Sur le premier écran, cliquez sur Installer.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Une fois l’installation terminée, cliquez sur Configurer maintenant.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Cette action lance une invite de commandes suivie par certains éléments PowerShell qui exécuteront Register-AzureADConnectHealthADFSAgent. Vous serez invité à vous connecter à Azure. Connectez-vous.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Une fois que vous êtes connecté, PowerShell continue. À l’issue du processus, vous pouvez fermer PowerShell ; la configuration est terminée.

À ce stade, les services doivent être démarrés automatiquement. L’agent surveille et collecte désormais les données. La capture d’écran qui suit est un exemple de la sortie. Gardez à l’esprit que vous verrez des avertissements s’afficher dans la fenêtre PowerShell si vous n’avez pas satisfait la configuration requise décrite dans les sections précédentes. Assurez-vous de disposer de la configuration requise [ici](active-directory-aadconnect-health-agent-install.md#requirements) avant d’installer l’agent.

![Vérifier Azure AD Connect Health pour AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Pour vérifier que l’agent a été installé, ouvrez les services et procédez aux vérifications suivantes :

- Azure AD Connect Health AD DS Insights Service
- Azure AD Connect Health AD DS Monitoring Service

Ces deux services ne démarrent pas tant que la configuration n’est pas terminée.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## Installation de l’agent Azure AD Connect Health pour AD DS sur Server Core. 
Après avoir installé le fichier .exe, vous pouvez terminer le processus d’inscription à l’aide de la commande PowerShell suivante :

`Register-AzureADConnectHealthADDSAgent -Credentials $cred

## Configuration des agents Azure AD Connect Health pour utiliser le proxy HTTP
Vous pouvez configurer des agents Azure AD Connect Health pour utiliser un proxy HTTP.

>[AZURE.NOTE]
- L’utilisation de « Netsh WinHttp set ProxyServerAddress » ne fonctionnera pas, car l’agent utilise System.Net pour effectuer des requêtes web au lieu des Services HTTP Microsoft Windows.
- L’adresse de proxy HTTP configurée servira à transmettre des messages HTTPS chiffrés.
- Les serveurs proxy authentifiés (à l’aide de HTTPBasic) ne sont pas pris en charge.

### Modification de la configuration du proxy d’un agent Health
Vous disposez des options suivantes afin de configurer un agent Azure AD Connect Health pour utiliser un proxy HTTP.

>[AZURE.NOTE] Vous devez redémarrer tous les services de l’agent Azure AD Connect Health pour mettre à jour les paramètres du proxy. Exécutez la commande suivante :<br> Restart-Service AdHealth*

#### Importation des paramètres de proxy existants

##### Importation à partir d'Internet Explorer
Vous pouvez importer vos paramètres de proxy HTTP Internet Explorer et les utiliser pour les agents Azure AD Connect Health en exécutant la commande PowerShell suivante sur chaque serveur qui exécute l'agent Health.

	Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### Importation à partir de WinHTTP
Vous pouvez importer vos paramètres de proxy WinHTTP en exécutant la commande PowerShell suivante sur chaque serveur qui exécute l'agent Health.

	Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### Spécification manuelle des adresses du proxy
Vous pouvez spécifier manuellement un serveur proxy en exécutant la commande PowerShell suivante sur chaque serveur qui exécute l'agent Health.

	Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exemple : *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*

- « address » peut être un nom de serveur DNS pouvant être résolu ou une adresse IPv4
- "port" peut être omis. Dans ce cas, 443 est choisi comme port par défaut.

#### Effacement de la configuration de proxy existante
Vous pouvez effacer la configuration du proxy en exécutant la commande suivante.

	Set-AzureAdConnectHealthProxySettings -NoProxy


### Lecture des paramètres de proxy actuels
Vous pouvez utiliser la commande suivante pour lire les paramètres de proxy actuellement configurés.

	Get-AzureAdConnectHealthProxySettings


## Tester la connectivité au service Azure AD Connect Health
Des problèmes provoquant la perte de connectivité entre l’agent Azure AD Connect Health et le service Azure AD Connect Health peuvent survenir. Cela inclut, entre autres, des problèmes réseau et des problèmes d’autorisation.

Si l’agent ne parvient pas à envoyer des données au service Azure AD Connect Health pendant plus de 2 heures, vous verrez un message d’alerte indiquant « Les données du service de contrôle d’intégrité ne sont pas à jour. » Dans ce cas, vous pouvez tester si les agents Azure AD Connect Health sont en mesure de télécharger des données vers le service Azure AD Connect Health en exécutant la commande PowerShell suivante à partir de l’ordinateur dont l’agent rencontre ce problème.

    Test-AzureADConnectHealthConnectivity -Role Adfs

Le paramètre de rôle peut avoir les valeurs suivantes :

- Adfs
- Synchronisation
- AJOUTE

Vous pouvez utiliser l’indicateur - ShowResults dans la commande pour afficher des journaux détaillés. Consultez l’exemple qui suit :

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]Pour utiliser l’outil de connectivité, vous devez d’abord effectuer l’inscription de l’agent. Si vous n’êtes pas en mesure de terminer l’inscription de l’agent, assurez-vous d’avoir rempli toutes les [conditions requises](active-directory-aadconnect-health-agent-install.md#requirements) pour Azure AD Connect Health. Ce test de connectivité est effectué par défaut lors de l’inscription de l’agent.



## Liens connexes

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilisation d'Azure AD Connect Health pour la synchronisation (en Anglais)](active-directory-aadconnect-health-sync.md)
* [Utilisation d’Azure AD Connect Health avec AD DS](active-directory-aadconnect-health-adds.md)
* [Forum Aux Questions (FAQ) Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historique de publication des versions d’Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0928_2016-->