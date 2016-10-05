<properties
   pageTitle="Échec de sauvegarde des machines virtuelles Azure : impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané ; la sous-tâche de capture de la machine virtuelle a expiré | Microsoft Azure"
   description="Causes des symptômes et solutions possibles pour les échecs de sauvegarde de machines virtuelles Azure liés à l’impossibilité de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané. Erreur de délai d’expiration de sous-tâche de capture instantanée de machine virtuelle"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jwhit"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/14/2016"
    ms.author="jimpark; markgal;genli"/>

# Échec de sauvegarde des machines virtuelles Azure : impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané ; la sous-tâche de capture instantanée de la machine virtuelle a expiré.

## Résumé

Après l’inscription et la planification d’une machine virtuelle pour Azure Backup, le service Azure Backup lance le travail de sauvegarde à l’heure planifiée en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Certaines conditions peuvent empêcher la capture d’un instantané, ce qui entraîne un échec de la sauvegarde. Cet article détaille les étapes nécessaires au dépannage des problèmes liés aux échecs de sauvegarde des machines virtuelles Azure relatifs à une erreur de délai d’expiration d’un instantané.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Symptôme

Microsoft Azure Backup pour machine virtuelle utilisant l’infrastructure IaaS échoue et renvoie le message d’erreur suivant dans les détails de l’erreur de la tâche dans le Portail Azure.

**Impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané ; la sous-tâche de capture instantanée de la machine virtuelle a expiré.**

## Cause :
Quatre causes courantes peuvent être à l’origine de cette erreur :

- La machine virtuelle n’a pas accès à Internet.
- L’agent de machine virtuelle Microsoft Azure installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux).
- L’extension de sauvegarde ne peut être mise à jour ou chargée.
- Impossible de récupérer l’état des instantanés ou de capturer les instantanés.

## Cause 1 : la machine virtuelle n’a pas accès à Internet.
Selon la configuration requise pour le déploiement, la machine virtuelle n’a pas accès à Internet, ou a des restrictions en place qui empêchent l’accès à l’infrastructure Azure.

L’extension de sauvegarde nécessite le fonctionnement correct de la connectivité vers les adresses IP publiques Azure. Cela est dû à l’envoi de commandes vers un point de terminaison Azure Storage (HTTP URL) pour gérer les instantanés de la machine virtuelle. Si l’extension n’a pas accès à Internet public, la sauvegarde échoue.

### Solution
Dans ce scénario, utilisez l’une des méthodes suivantes pour résoudre le problème :

- Mettez sur liste approuvée les plages IP du centre de données Azure.
- Créez un chemin d'accès pour le trafic HTTP

### Mettre sur liste blanche les plages IP du centre de données Azure.

1. Obtenez la liste des [adresses IP de centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) à mettre sur liste blanche.
2. Débloquez les adresses IP à l’aide de l’applet de commande New-NetRoute. Exécutez cette applet de commande dans la machine virtuelle Azure, dans une fenêtre PowerShell avec élévation de privilèges (exécution en tant qu’administrateur).
3. Ajoutez des règles à un groupe de sécurité réseau (le cas échéant) pour autoriser l’accès aux adresses IP.

### Créer un chemin d’accès pour le trafic HTTP

1. Si des restrictions réseau ont été mises en place (un groupe de sécurité réseau, par exemple), déployez un serveur proxy HTTP pour acheminer le trafic.
2. Ajoutez des règles à un groupe de sécurité réseau (le cas échéant) pour autoriser l’accès à Internet à partir du proxy HTTP.

Apprenez l’[utilisation d’un proxy HTTP pour les sauvegardes de machine virtuelle](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## Cause 2 : l’agent de machine virtuelle Microsoft Azure installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux).

### Solution
La plupart des échecs des machines virtuelles Linux liés aux agents ou aux extensions sont causés par des problèmes qui affectent un ancien agent de machine virtuelle. En règle générale, les premières étapes vers la résolution de ce problème sont les suivantes :

1. [Installez le dernier agent de machine virtuelle Azure](https://github.com/Azure/WALinuxAgent).
2. Assurez-vous que l’agent Azure s’exécute sur la machine virtuelle. Pour ce faire, exécutez la commande suivante : ```ps -e```

    Si ce n’est pas le cas, utilisez les commandes suivantes pour le redémarrer.

    Pour Ubuntu : ```service walinuxagent start```

    Pour les autres distributions : ```service waagent start
```

3. [Configurez l’agent de redémarrage automatique](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. Exécutez une nouvelle sauvegarde de test. Si les problèmes persistent, veuillez collecter des journaux dans les dossiers suivants pour un débogage supplémentaire.

    Nous demandons les journaux suivants de la machine virtuelle du client :

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

Si nous exigeons une journalisation détaillée pour waagent, procédez comme suit :

1. Dans le fichier /etc/waagent.conf, recherchez la ligne suivante :

    Activer la journalisation documentée (O|N)

2. Passez la valeur **Logs.Verbose** de N à O.
3. Enregistrez cette modification, puis redémarrez waagent en suivant les étapes précédentes de cette section.

## Cause 3 : l’extension de sauvegarde ne peut être mise à jour ou chargée.
Si les extensions ne peuvent pas être chargées, la sauvegarde échoue en raison de l’impossibilité de capturer un instantané.

### Solution
Pour les invités Windows :

1. Vérifiez que le service iaasvmprovider est activé et qu’il a un type de démarrage automatique.
2. Si ce n’est pas le cas, activez le service pour déterminer si la sauvegarde suivante réussit.

Pour les hôtes Linux :

La dernière version de VMSnapshot Linux (extension utilisée par la sauvegarde) est 1.0.91.0

Si l’extension de sauvegarde rencontre toujours un échec lors de la mise à jour ou du chargement, vous pouvez forcer le rechargement de l’extension VMSnapshot en désinstallant l’extension. La prochaine tentative de sauvegarde rechargera l’extension.

### Pour désinstaller l’extension

1. Accédez au [portail Azure](https://portal.azure.com/).
2. Localisez la machine virtuelle qui rencontre des problèmes de sauvegarde.
3. Cliquez sur **Paramètres**.
4. Cliquez sur **Extensions**.
5. Cliquez sur **Extension Vmsnapshot**.
6. Cliquez sur **Désinstaller**.

Cela réinstalle l’extension lors de la sauvegarde suivante.

## Cause 4 : impossible de récupérer l’état des instantanés ou de capturer les instantanés.
La sauvegarde de machine virtuelle émet une commande de capture instantanée à destination du stockage sous-jacent. La sauvegarde peut échouer en raison de l’absence d’accès au stockage ou du retard de l’exécution d’une tâche de capture instantanée.

### Solution
Voici les causes possibles de l’échec d’une tâche de capture instantanée :

| Cause : | Solution |
| ----- | ----- |
| Machines virtuelles dont la sauvegarde Microsoft SQL Server a été configurée. Par défaut, la sauvegarde de machine virtuelle exécute une sauvegarde complète VSS sur les machines virtuelles Windows. Sur les machines virtuelles exécutant des serveurs basés sur SQL Server et sur lesquelles la sauvegarde SQL Server est configurée, des retards d’exécution des captures instantanées peuvent survenir. | Configurez la clé de Registre suivante si vous constatez des échecs de sauvegarde en raison de problèmes de capture instantanée.<br><br>[HKEY\_LOCAL\_MACHINE\\SOFTWARE\\MICROSOFT\\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| L’état de la machine virtuelle est rapporté de manière incorrecte en raison de l’arrêt de la machine virtuelle dans RDP. Si vous avez arrêté la machine virtuelle dans RDP, retournez sur le portail pour vérifier que l’état de la machine virtuelle est correct. | Si ce n’est pas le cas, arrêtez la machine virtuelle dans le portail à l’aide de l’option « Arrêter » dans le tableau de bord de la machine virtuelle. |
| Plusieurs machines virtuelles du même service cloud sont configurées pour exécuter la sauvegarde en même temps. | Il est recommandé d’étaler les planifications de sauvegarde entre les différentes machines virtuelles d’un même service cloud. |
| Forte sollicitation du processeur ou de la mémoire sur la machine virtuelle. | Si la machine virtuelle sollicite fortement le processeur (plus de 90 pour cent) ou la mémoire, la tâche de capture instantanée est mise en file d’attente et retardée, puis finit par expirer. En pareille situation, essayez de procéder à des sauvegardes à la demande. |
|La machine virtuelle ne peut pas obtenir une adresse d’hôte/de structure du protocole DHCP.|Le protocole DHCP doit être activé dans l’invité pour que la sauvegarde de la machine virtuelle IaaS fonctionne. Si la machine virtuelle ne peut pas bénéficier d’adresses d’hôte/de structure de la réponse DHCP 245, elle ne peut ni télécharger, ni exécuter d’extension. Si vous avez besoin d’une adresse IP privée statique, vous devez la configurer via la plateforme. L’option DHCP à l’intérieur de la machine virtuelle doit être laissée désactivée. En savoir plus sur la [définition d’une adresse IP privée interne statique](../virtual-network/virtual-networks-reserved-private-ip.md).|

<!---HONumber=AcomDC_0921_2016-->