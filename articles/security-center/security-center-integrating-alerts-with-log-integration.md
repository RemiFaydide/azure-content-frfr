<properties
   pageTitle="Intégration des alertes du Centre de sécurité Azure avec les journaux Azure (version préliminaire) | Microsoft Azure"
   description="Cet article vous aidera à vous familiariser avec l’intégration des alertes du Centre de sécurité avec les journaux Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# Intégration des alertes du Centre de sécurité avec les journaux Azure (version préliminaire)

De nombreuses équipes de sécurité et d’intervention utilisent une solution SIEM (Security Information and Event Management) comme point de départ pour le triage et l’examen des alertes de sécurité. Grâce à l’intégration des journaux Azure, les clients peuvent synchroniser quasiment en temps réel les alertes d’Azure Security Center, ainsi que les événements de sécurité des machines virtuelles collectés par les journaux de diagnostics et d’audit Azure, avec leur solution SIEM ou Log Analytics.

L’intégration des journaux Azure fonctionne avec HP ArcSight, Splunk, IBM QRadar, entre autres.

## Quels journaux puis-je intégrer ?

Azure génère une journalisation complète pour chaque service. Ces journaux sont classés de la façon suivante :

- **Journaux de contrôle/gestion**, qui vous offrent une visibilité sur les opérations CREATE, UPDATE et DELETE d’Azure Resource Manager.
- **Journaux des plans de données**, qui vous offrent une visibilité sur les événements déclenchés lors de l’utilisation d’une ressource Azure. Il s’agit par exemple du journal des événements Windows : les journaux de sécurité et des applications dans une machine virtuelle.

L’intégration des journaux Azure prend actuellement en charge l’intégration de :

- Journaux des machines virtuelles Azure
- Journaux d’audit Azure
- Alertes Azure Security Center

## Installer l’intégration des journaux Azure

Téléchargez [Intégration des journaux Azure](https://www.microsoft.com/download/details.aspx?id=53324).

Le service d’intégration des journaux Azure collecte les données de télémétrie à partir de l’ordinateur sur lequel il est installé. Les données de télémétrie recueillies sont les suivantes :

- Les exceptions qui se produisent pendant l’exécution de l’intégration des journaux Azure
- Des métriques concernant le nombre de requêtes et d’événements traités
- Des statistiques sur les options de ligne de commande Azlog.exe utilisées

> [AZURE.NOTE] Vous pouvez désactiver la collecte des données de télémétrie en décochant cette option.

## Intégrer les alertes des journaux d’audit Azure et de Security Center

1. Ouvrez l’invite de commandes et **cd** dans **c:\\Program Files\\Microsoft Azure Log Integration**.

2. Exécutez la commande **azlog createazureid** pour créer un [Principal du service Azure Active Directory](../active-directory/active-directory-application-objects.md) dans les locataires Azure Active Directory (AD) qui hébergent les abonnements Azure.

    Vous serez invité à entrer vos identifiants de connexion Azure.

    > [AZURE.NOTE] Vous devez être le propriétaire ou un coadministrateur de l’abonnement.

    L’authentification à Azure s’effectue avec Azure AD. La création d’un principal du service pour l’intégration des journaux Azure créera l’identité Azure AD qui aura un accès en lecture aux abonnements Azure.

3. Exécutez la commande **azlog authorize <SubscriptionID>** pour affecter un accès en lecture à l’abonnement au principal du service créé à l’étape 2. Si vous ne spécifiez pas de **SubscriptionID**, le principal du service se verra affecté le rôle Lecteur pour tous les abonnements auxquels vous avez accès.

    > [AZURE.NOTE] Des avertissements peuvent s’afficher si vous exécutez la commande **authorize** immédiatement après la commande **createazureid** car il y a un temps de latence entre la création du compte Azure AD et sa disponibilité à l’utilisation. Si vous patientez environ 10 secondes après l’exécution de la commande **createazureid** avant d’exécuter la commande **authorize**, vous ne devriez pas voir ces avertissements.

4. Vérifiez que les fichiers JSON de journaux d’audit sont présents dans les dossiers suivants :

  - **c:\\Users\\azlog\\AzureResourceManagerJson**
  - **c:\\Users\\azlog\\AzureResourceManagerJsonLD**

5. Vérifiez que les alertes Security Center existent dans les dossiers suivants :

  - **c:\\Users\\azlog\\ AzureSecurityCenterJson**
  - **c:\\Users\\azlog\\AzureSecurityCenterJsonLD**

6. Pointez le connecteur de transfert de fichier SIEM standard vers le dossier approprié pour diriger les données vers l’instance SIEM. Reportez-vous aux [configurations SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) sur votre configuration SIEM.

Si vous avez des questions sur l’intégration des journaux Azure, envoyez un e-mail à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## Étapes suivantes

Pour plus d’informations sur les journaux d’audit Azure et les définitions de propriétés, consultez :

- [Opérations d’audit avec Resource Manager](../resource-group-audit.md)
- [Liste des événements de gestion dans un abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx) : pour récupérer les événements du journal d’audit.

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!---HONumber=AcomDC_0921_2016-->