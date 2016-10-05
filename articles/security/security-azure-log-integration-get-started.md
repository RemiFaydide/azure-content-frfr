<properties
   pageTitle="Prise en main de l’intégration des journaux Azure | Microsoft Azure"
   description="Découvrez comment installer le service d’intégration des journaux Azure et intégrer les journaux d’audit Azure et les alertes de l’Azure Security Center."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# Prise en main de l’intégration des journaux Azure (version préliminaire)

L’intégration des journaux Azure permet d’intégrer des journaux bruts de vos ressources Azure dans vos systèmes SIEM (Security Information and Event Management) locaux. Cette intégration offre un tableau de bord unifié pour toutes vos ressources, en local ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité associés à vos applications.

Ce didacticiel vous guide dans la procédure d’installation de l’intégration des journaux Azure et d’intégration des journaux d’audit Azure et des alertes de l’Azure Security Center. La durée estimée pour effectuer ce didacticiel est d’une heure.

## Conditions préalables

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Un ordinateur (local ou dans le cloud) sur lequel installer le service d’intégration Azure journal. Cet ordinateur doit exécuter un système d’exploitation Windows 64 bits avec .net 4.5.1 installé. Cet ordinateur est appelé **l’intégrateur Azlog**.
- Abonnement Azure. Si vous n’en possédez pas, vous pouvez vous inscrire pour créer dès aujourd’hui un [compte gratuit](https://azure.microsoft.com/free/).
- Les diagnostics Azure sont activés pour vos machines virtuelles Azure. Pour activer les diagnostics pour les Cloud Services, consultez [Activation des diagnostics Azure dans Azure Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md). Pour activer les diagnostics pour une machine virtuelle exécutant Windows, consultez la rubrique [Utiliser PowerShell pour activer Azure Diagnostics sur une machine virtuelle exécutant Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md).
- La connectivité depuis l’intégrateur Azlog vers le stockage Azure pour authentifier et autoriser un abonnement Azure.
- Pour les journaux des machines virtuelles Azure, l’agent SIEM (par exemple, Splunk Universal Forwarder, HP ArcSight Windows Event Collector agent ou IBM QRadar WinCollect) doit être installé sur l’intégrateur Azlog.

## Points à prendre en considération pour le déploiement

Vous pouvez exécuter plusieurs instances de l’intégrateur Azlog si le volume d’événements est élevé. L’équilibrage de charge des comptes de stockage Diagnostics Microsoft Azure pour Windows *(WAD)* et le nombre d’abonnements à fournir aux instances doivent être basés sur la capacité.

Sur un ordinateur 8 processeurs (cœurs), une seule instance de l’intégrateur Azlog peut traiter environ 24 millions d’événements par jour (environ 1 million/heure).

Sur un ordinateur 4 processeurs (cœurs), une seule instance de l’intégrateur Azlog peut traiter environ 1,5 million d’événements par jour (environ 62 500 millions/heure).

## Installer l’intégration des journaux Azure

Téléchargez [Intégration des journaux Azure](https://www.microsoft.com/download/details.aspx?id=53324).

Le service d’intégration des journaux Azure collecte les données de télémétrie à partir de l’ordinateur sur lequel il est installé. Les données de télémétrie recueillies sont les suivantes :

- Les exceptions qui se produisent pendant l’exécution de l’intégration des journaux Azure
- Des métriques concernant le nombre de requêtes et d’événements traités
- Des statistiques sur les options de ligne de commande Azlog.exe utilisées

> [AZURE.NOTE] Vous pouvez désactiver la collecte des données de télémétrie en décochant cette option.

## Intégration de journaux des machines virtuelles Azure à partir de vos comptes de stockage Azure Diagnostics

1. Vérifiez la configuration requise indiquée ci-dessus pour vous assurer que votre compte de stockage WAD collecte les journaux avant de poursuivre l’intégration de votre journal Azure. N’effectuez pas les étapes suivantes si votre compte de stockage WAD ne collecte pas les journaux.

2. Ouvrez l’invite de commandes et **cd** dans **c:\\Program Files\\Microsoft Azure Log Integration**.

3. Exécutez la commande.

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      Remplacez StorageAccountName par le nom du compte de stockage Azure configuré pour recevoir des événements de diagnostics de votre machine virtuelle.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      Si vous souhaitez que l’ID d’abonnement s’affiche dans l’XML de l’événement, ajoutez l’ID d’abonnement au nom convivial :

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. Patientez 30 à 60 minutes (l’opération peut prendre jusqu'à une heure), puis affichez les événements qui sont extraits à partir du compte de stockage. Pour afficher, ouvrez **l’Observateur d’événements > Journaux Windows > Événements transférés** dans l’intégrateur Azlog.

5. Assurez-vous que votre connecteur SIEM standard installé sur l’ordinateur est configuré pour choisir les événements à partir de la **événements transmis** dossier et transférez-les dans votre instance SIEM. Passez en revue la configuration spécifique SIEM pour configurer et afficher l’intégration des journaux.

## Que se passe-t-il si les données ne s’affichent pas dans le dossier Événements transférés ?

Si les données ne s’affichent pas dans le dossier **Événements transférés** après une heure, procédez comme suit :

1. Vérifiez que l’ordinateur peut accéder à Azure. Pour tester la connectivité, essayez d’ouvrir le [portail Azure](http://portal.azure.com) à partir du navigateur.

2. Assurez-vous que le compte d’utilisateur **azlog** a l’autorisation d’écriture sur le dossier **utilisateurs\\azlog**.

3. Assurez-vous que le compte de stockage ajouté à la commande **Source azlog / Ajouter** est répertoriée lorsque vous exécutez la commande **Source azlog / Liste**.
4. Accédez à **l’Observateur d’événements > journaux Windows > Application** pour voir si des erreurs sont signalées par l’intégration des journaux Azure.

Si vous ne voyez toujours les événements, procédez comme suit :

1. Téléchargez [l’Explorateur de stockage Microsoft Azure](http://storageexplorer.com/).

2. Connectez-vous au compte de stockage ajouté à la commande **Source azlog / Ajouter**.

3. Dans l’Explorateur de stockage Microsoft Azure, accédez à la table **WADWindowsEventLogsTable** pour voir il s'existe des données. Si ce n’est pas le cas, les diagnostics dans la machine virtuelle ne sont pas configurés correctement.

## Intégration de journaux d’audit Azure et d’alertes du Security Center

1. Ouvrez l’invite de commandes et **cd** dans **c:\\Program Files\\Microsoft Azure Log Integration**.

2. Exécutez la commande.

        azlog createazureid

      Cette commande vous invite à entrer votre nom d’utilisateur Azure. La commande crée ensuite un [principal du Service Azure Active Directory](../active-directory/active-directory-application-objects.md) dans les locataires Azure AD qui hébergent les abonnements Azure dans lesquels l’utilisateur connecté est un administrateur, un coadministrateur ou un propriétaire. La commande échouera si l’utilisateur connecté est seulement un utilisateur invité dans le locataire Azure AD. L’authentification à Azure s’effectue par le biais d’Azure Azure Active Directory (AD). La création d’un principal du service pour l’intégration d’Azlog crée l’identité Azure AD qui aura un accès en lecture aux abonnements Azure.

3. Exécutez la commande.

        azlog authorize <SubscriptionID>

      Elle affecte les accès en lecture de l’abonnement au service principal créé à l’étape 2. Si vous ne spécifiez pas de SubscriptionID, elle tente d’affecter le rôle de lecteur du principal du service à tous les abonnements auxquels vous avez accès.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] Des avertissements peuvent s’afficher si vous exécutez la commande **authorize** immédiatement après la commande **createazureid**. Il existe un temps de latence entre la création du compte Azure AD et la disponibilité du compte pour une utilisation. Si vous patientez environ 10 secondes après l’exécution de la commande **createazureid** avant d’exécuter la commande **authorize**, vous ne devriez pas voir ces avertissements.

4. Vérifiez que les fichiers JSON de journaux d’audit sont présents dans les dossiers suivants :

  - **c:\\Users\\azlog\\AzureResourceManagerJson**
  - **c:\\Users\\azlog\\AzureResourceManagerJsonLD**

5. Vérifiez que les alertes Security Center existent dans les dossiers suivants :

  - **c:\\Users\\azlog\\ AzureSecurityCenterJson**
  - **c:\\Users\\azlog\\AzureSecurityCenterJsonLD**

6. Pointez le connecteur de transfert de fichier SIEM standard vers le dossier approprié pour diriger les données vers l’instance SIEM. Vous aurez peut-être besoin de certains mappages de champ en fonction du produit SIEM que vous utilisez.

Si vous avez des questions sur l’intégration des journaux Azure, envoyez un e-mail à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## Étapes suivantes

Dans ce didacticiel, vous avez appris à installer l’intégration des journaux Azure et d’intégrer des journaux à partir du stockage Azure. Pour en savoir plus, consultez les articles suivants :

- [Intégration de journaux Microsoft Azure pour les journaux Azure (Version préliminaire)](https://www.microsoft.com/download/details.aspx?id=53324) : centre de téléchargement pour plus d’informations, la configuration système requise et les instructions d’installation sur l’intégration de journaux Azure.
- [Introduction à l’intégration de journaux Azure](security-azure-log-integration-overview.md) : ce document présente l’intégration des journaux Azure, ses principales fonctionnalités et son fonctionnement.
- [Étapes de configuration de partenaires](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : ce billet de blog vous montre comment configurer l’intégration des journaux Azure pour travailler avec des solutions de partenaires Splunk, HP ArcSight et IBM QRadar.
- [FAQ de l’intégration des journaux Azure](security-azure-log-integration-faq.md) : ce forum aux questions répond aux questions sur l’intégration des journaux Azure.
- [Intégration des alertes du Security Center avec les journaux Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) : ce document montre comment synchroniser les alertes du Security Center, ainsi que les événements de sécurité des machines virtuelles collectés par Azure Diagnostics et les journaux d’audit Azure dans leur solution SIEM ou Log Analytics.
- [Nouvelles fonctionnalités des diagnostics Azure et des journaux d’Audit Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : ce billet de blog présente les journaux d’Audit Azure et autres fonctionnalités pour vous permettre de mieux connaître les opérations de vos ressources Azure.

<!---HONumber=AcomDC_0921_2016-->