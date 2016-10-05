<properties
	pageTitle="Comprendre la réplication Hyper-V avec Azure Site Recovery | Microsoft Azure" 
	description="Utilisez cet article pour comprendre les concepts techniques qui vous permettent d’installer, de configurer et de gérer avec succès Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/12/2016" 
	ms.author="anbacker"/>
 

# Comprendre la réplication Hyper-V avec Azure Site Recovery

Cet article décrit les concepts techniques qui vous permettent de configurer et de gérer avec succès la protection entre un site Hyper-V ou un site VMM et Azure à l’aide d’Azure Site Recovery.

## Vue d’ensemble des composants

### Déploiement de site Hyper-V ou de site VMM pour la réplication entre des sites locaux et Azure.
 
Dans le cadre de la configuration de la récupération d’urgence entre des sites locaux et Azure ; le fournisseur Azure Site Recovery doit être téléchargé et installé sur le serveur VMM avec l’agent Azure Recovery Services qui doit être installé sur chaque hôte Hyper-V.

![Déploiement du site VMM pour la réplication entre des sites locaux et Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Le déploiement de site Hyper-V est similaire au déploiement VMM. La seule différence étant que le fournisseur et l’agent sont installés sur l’hôte Hyper-V lui-même.

## Vue d’ensemble des flux de travail

### Activer la protection
Une fois que vous protégez une machine virtuelle du portail ou en local, une tâche de récupération automatique du système nommée *Activer la protection* est lancée et peut être surveillée sous l’onglet Tâches.

![Résolution des problèmes Hyper-V locaux](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

La tâche *Activer la protection* vérifie la configuration requise avant d’appeler [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) qui crée la réplication vers Azure à l’aide d’entrées configurées au cours de la protection. La tâche *Activer la Protection* démarre la réplication initiale en local en appelant [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) qui envoie les disques virtuels de la machine virtuelle dans Azure.

![Résolution des problèmes Hyper-V locaux](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### Finaliser la protection
Un [instantané de machine virtuelle Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) est effectué lorsque la réplication initiale est déclenchée. Les disques durs virtuels sont traités un par un jusqu’à ce que tous les disques soient chargés dans Azure. Cette procédure prend généralement un certain temps selon la taille du disque et la bande passante. Consultez la page [Gestion de l’utilisation de la bande passante réseau de protection d’un serveur local vers Azure](https://support.microsoft.com/kb/3056159) pour optimiser votre utilisation du réseau. Une fois la réplication initiale terminée, la tâche *Finaliser la protection sur la machine virtuelle* configure les paramètres réseau et post-réplication. Tandis que la réplication initiale est en cours d’exécution, toutes les modifications des disques sont suivies comme indiqué dans la section Réplication delta ci-dessous. Le stockage sur disque supplémentaire sera consommé pour l’instantané et les fichiers HRL tandis que la réplication initiale est en cours. Une fois la réplication initiale terminée, l’instantané de la machine virtuelle Hyper-V est supprimé, ce qui entraîne la fusion des modifications de données post-réplication initiale sur le disque parent.

![Résolution des problèmes Hyper-V locaux](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### Réplication delta
Le dispositif de suivi de réplication des réplicas Hyper-V, qui fait partie du moteur de réplication des réplicas Hyper-V, suit les modifications apportées à un disque dur virtuel sous la forme de fichiers .hrl (Hyper-V Replication Logs, journaux de réplication Hyper-V). Les fichiers HRL se trouveront dans le même répertoire que les disques associés. Chaque disque configuré pour la réplication aura un fichier HRL associé. Ce ou ces journaux sont envoyés au compte de stockage du client après la fin de la réplication initiale. Lorsqu’un journal est en transit vers Azure, les modifications dans le fichier principal sont suivies dans un autre fichier journal du même répertoire.

L’intégrité de la réplication de la machine virtuelle au cours de la réplication initiale ou de la réplication delta peut être surveillée dans la vue Machine virtuelle comme mentionné sous [Surveillance de l’intégrité de la réplication de la machine virtuelle](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).

### Resynchronisation 
Une machine virtuelle est marquée pour la resynchronisation lorsque la réplication delta échoue et que la réplication initiale complète est coûteuse en termes de bande passante réseau ou de temps nécessaire pour effectuer une réplication initiale complète. Par exemple, lorsque la taille du fichier HRL représente jusqu’à 50 % de la taille totale du disque, la machine virtuelle est marquée pour la resynchronisation. La resynchronisation réduit la quantité de données envoyées sur le réseau par le calcul de sommes de contrôle des disques de machine virtuelle source et cible et l’envoi du différentiel uniquement.

Une fois la resynchronisation terminée, la réplication delta normale doit reprendre. La resynchronisation peut être reprise en cas de panne (par exemple, panne du réseau, incident VMM, etc.).

Par défaut, l’option *Resynchronisation planifiée automatiquement* est configurée pour s’exécuter en dehors des heures de travail. Si la machine virtuelle doit être resynchronisée manuellement, sélectionnez la machine virtuelle à partir du portail et cliquez sur RESYNCHRONISER.

![Résolution des problèmes Hyper-V locaux](media/site-recovery-understanding-site-to-azure-protection/image04.png)

La resynchronisation utilise un algorithme de segmentation de bloc fixe où les fichiers source et cible sont divisés en segments fixes ; les sommes de contrôle pour chaque segment sont générées et sont ensuite comparées afin de déterminer quels blocs de la source doivent être appliqués à la cible.

### Logique de nouvelle tentative
Il existe une logique de nouvelle tentative intégrée lorsque des erreurs de réplication se produisent. On peut classer les erreurs en deux catégories, comme indiqué ci-dessous.

| Catégorie | Scénarios |
|---------------------------|----------------------------------------------|
| Erreur non récupérable | Aucune nouvelle tentative ne va être effectuée. L’état de réplication de la machine virtuelle sera affiché comme critique et une intervention de l’administrateur sera requise. Voici quelques exemples : <ul><li>Chaîne rompue de disque dur virtuel</li><li>État non valide de la machine virtuelle de réplica</li><li>Erreur d’authentification réseau</li><li>Erreur d’autorisation</li><li>Machine virtuelle non trouvée dans le cas d’un serveur Hyper-V autonome</li></ul>|
| Erreur récupérable | De nouvelles tentatives se produisent à chaque intervalle de réplication à l’aide d’une interruption exponentielle qui augmente l’intervalle de nouvelle tentative dès le début de la première tentative (1, 2, 4, 8, 10 minutes). Si une erreur persiste, effectuez une nouvelle tentative toutes les 30 minutes. Voici quelques exemples : <ul><li>Erreur réseau</li><li>Espace disque faible</li><li>Mémoire insuffisante</li></ul>|

## Vue d’ensemble du cycle de vie de protection et de récupération de la machine virtuelle Hyper-V

![Comprendre le cycle de vie de protection et de récupération de la machine virtuelle Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## Autres références

- [Surveiller et résoudre les problèmes de protection pour les sites VMware, VMM, Hyper-V et physiques](./site-recovery-monitoring-and-troubleshooting.md)
- [Contacter le support Microsoft](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Les erreurs ASR courantes et leur résolution](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

<!---HONumber=AcomDC_0921_2016-->