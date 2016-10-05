<properties 
   pageTitle="Appareil virtuel StorSimple Update 2 | Microsoft Azure"
   description="Apprenez à créer, déployer et gérer un appareil virtuel StorSimple dans un réseau virtuel Microsoft Azure. (S'applique à StorSimple Update 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2016"
   ms.author="alkohli" />

# Déployer et gérer un appareil virtuel StorSimple dans Azure


##Vue d'ensemble
L’appareil virtuel StorSimple série 8000 est une fonctionnalité supplémentaire fournie avec votre solution Microsoft Azure StorSimple. L’appareil virtuel StorSimple s’exécute sur une machine virtuelle dans un réseau virtuel Microsoft Azure. Vous pouvez l’utiliser pour sauvegarder et cloner les données à partir de vos ordinateurs hôtes. Ce didacticiel explique comment déployer et gérer un appareil virtuel dans Azure. Il s’applique à tous les appareils virtuels exécutant le logiciel version Update 2 et inférieure.


#### Comparaison des modèles d’appareils virtuels

L’appareil virtuel StorSimple est disponible en deux modèles, un 8010 Standard (anciennement 1100) et un 8020 Premium (dans Update 2). Vous trouverez ci-dessous une comparaison de ces deux modèles.


| Modèle de l'appareil | 8010<sup>1</sup> | 8020 |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Capacité maximale** | 30 To | 64 To |
| **Microsoft Azure** | Standard\_A3 (4 cœurs, 7 Go de mémoire) | Standard\_DS3 (4 cœurs, 14 Go de mémoire) |
| **Compatibilité des versions** | Les versions exécutant une version antérieure de la mise à jour préliminaire 2 ou version ultérieure | Les versions exécutant Update 2 ou version ultérieure |
| **Disponibilité des régions** | Toutes les régions Azure | Régions Azure qui prennent en charge Premium Storage<br></br>Pour obtenir la liste des régions, consultez [Régions prises en charge pour 8020](#supported-regions-for-8020). |
| **Type de stockage** | Utilise le stockage Azure Standard pour les disques locaux<br></br> Découvrez comment [créer un compte de stockage Standard]() | Utilise le stockage Azure Standard pour les disques locaux<sup>2</sup> <br></br>Découvrez comment [créer un compte de stockage Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk) |
| **Aide relative à la charge de travail** | Récupération au niveau des éléments des fichiers à partir de sauvegardes | Scénarios de développement et de test dans le cloud, faible latence, charges de travail plus performantes<br></br>Appareil secondaire pour la récupération d’urgence |
 
<sup>1</sup> *Anciennement, 1100*.

<sup>2</sup> *Le 8010 et la 8020 utilisent tous deux le stockage Azure Standard pour le niveau cloud. La différence existe uniquement au niveau local au sein de l’appareil*.

#### Régions prises en charge pour 8020

Les régions Premium Storage actuellement prises en charge pour 8020 sont indiquées dans le tableau ci-dessous. Cette liste est mise à jour chaque fois que Premium Storage est disponible dans une nouvelle région.

| No | Actuellement pris en charge dans les régions |
|---------------------------------------------------------|--------------------------------|
| 1 | Centre des États-Unis |
| 2 | Est des États-Unis |
| 3 | Est des États-Unis 2 |
| 4 | Ouest des États-Unis |
| 5 | Europe du Nord |
| 6 | Europe de l'Ouest |
| 7 | Asie du Sud-Est |
| 8 | Est du Japon |
| 9 | Ouest du Japon |
| 10 | Est de l’Australie |
| 11 | Sud-Est de l’Australie* |
| 12 | Asie de l’Est* |
| 13\. | Centre-Sud des États-Unis* |

*Premium Storage a été lancé récemment dans ces zones géographiques.

Cet article décrit le processus de déploiement pas à pas d'un appareil virtuel StorSimple dans Azure. À la fin de cet article, vous serez capable :

- de différencier l’appareil virtuel de l’appareil physique ;

- de créer et configurer l’appareil virtuel ;

- de vous connecter à l’appareil virtuel ;

- d’utiliser l’appareil virtuel.

Ce didacticiel s’applique à tous les appareils virtuels StorSimple exécutant Update 2 ou une version ultérieure.

## Différences entre l’appareil virtuel et l’appareil physique

L’appareil virtuel StorSimple est une version logicielle de StorSimple qui s’exécute sur un seul nœud dans une machine virtuelle Microsoft Azure. L’appareil virtuel prend en charge les scénarios de récupération d’urgence dans lesquels votre appareil physique n’est pas disponible. Il est approprié pour une utilisation dans les scénarios de récupération au niveau des éléments à partir de sauvegardes, de récupération d’urgence en local, et de développement et de test dans le cloud.

#### Différences par rapport à l’appareil physique

Le tableau ci-dessous présente quelques différences clés entre l’appareil virtuel StorSimple et l’appareil physique StorSimple.

| | Appareil physique | Appareil virtuel |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Emplacement** | Se trouve dans le centre de données. | S'exécute dans Azure. |
| **Interfaces réseau** | Comporte six interfaces réseau : de DATA 0 à DATA 5. | A une seule interface réseau : DATA 0. |
| **Inscription** | Inscrit durant l'étape de configuration. | L'inscription est une tâche distincte. |
| **Clé de chiffrement de données du service** | Régénérer la clé sur l’appareil physique, puis mettre à jour l’appareil virtuel avec la nouvelle clé. | Ne peut pas régénérer à partir de l'appareil virtuel. |


## Configuration requise pour l’appareil virtuel

Les sections suivantes décrivent les conditions préalables à la configuration de votre appareil virtuel StorSimple. Avant de déployer un appareil virtuel, passez en revue les [Considérations de sécurité relatives à l’utilisation d’un appareil virtuel](storsimple-security.md#storsimple-virtual-device-security).

#### Conditions requises pour Azure

Avant d’approvisionner l’appareil virtuel, vous devez effectuer les préparatifs suivants dans votre environnement Azure :

- Pour l’appareil virtuel, [configurez un réseau virtuel sur Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Si vous utilisez le stockage Premium, vous devez créer un réseau virtuel dans une région Azure qui prend en charge le stockage Premium. Plus d’informations sur les [régions actuellement prises en charge pour 8020](#supported-regions-for-8020).
- Il est recommandé d’utiliser le serveur DNS par défaut fourni par Azure au lieu de spécifier le nom de votre propre serveur DNS. Si le nom de votre serveur DNS n'est pas valide ou si le serveur DNS n'est pas en mesure de résoudre correctement les adresses IP, la création de l'appareil virtuel échoue.
- Les options de point à site et de site à site sont facultatives (non obligatoires). Si vous le souhaitez, vous pouvez configurer ces options pour des scénarios plus avancés.
- Vous pouvez créer des [Machines virtuelles Azure](../virtual-machines/virtual-machines-linux-about.md) (serveurs hôtes) dans le réseau virtuel qui peut utiliser les volumes exposés par l’appareil virtuel. Ces serveurs doivent répondre aux exigences suivantes :
	- Il doit s’agir de machines virtuelles Windows ou Linux sur lesquelles l’initiateur iSCSI est installé.
	- Ils doivent être en cours d’exécution dans le même réseau virtuel que l’appareil virtuel.
	- Ils doivent être en mesure de se connecter à la cible iSCSI de l’appareil virtuel via l’adresse IP interne de ce dernier.

- Vérifiez que vous avez configuré la prise en charge de iSCSI et du trafic cloud sur le même réseau virtuel.


#### Conditions requises pour StorSimple

Apportez les mises à jour suivantes à votre service Azure StorSimple avant de créer un appareil virtuel :


- Ajoutez des [enregistrements de contrôle d’accès](storsimple-manage-acrs.md) pour les machines virtuelles qui vont être les serveurs hôtes de votre appareil virtuel.

- Utilisez un [compte de stockage](storsimple-manage-storage-accounts.md#add-a-storage-account) dans la même région que l’appareil virtuel. Des comptes de stockage dans différentes régions peuvent entraîner une dégradation des performances. Vous pouvez utiliser un compte de stockage Standard ou Premium avec l’appareil virtuel. Plus d’informations sur la création d’un [compte de stockage Standard]() ou un [compte de stockage Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- Utilisez un compte de stockage différent pour la création de l’appareil virtuel de celui utilisé pour vos données. L’utilisation du même compte de stockage peut entraîner une dégradation des performances.

Assurez-vous de disposer des informations suivantes avant de commencer :

- Votre compte avec les informations d'identification d'accès au portail Azure Classic.

- Une copie de la clé de chiffrement de données de service de votre appareil physique.


## Création et configuration de l’appareil virtuel

Avant d’effectuer ces procédures, assurez-vous que vous avez rempli les [conditions requises pour l’appareil virtuel](#prerequisites-for-the-virtual-device).

Après avoir créé un réseau virtuel, configuré un service StorSimple Manager et inscrit votre appareil physique StorSimple auprès du service, vous pouvez suivre les étapes suivantes pour créer et configurer un appareil virtuel StorSimple.

### Étape 1 : création d'un appareil virtuel

Procédez comme suit pour créer l’appareil virtuel StorSimple.

[AZURE.INCLUDE [Création d'un appareil virtuel](../../includes/storsimple-create-virtual-device-u2.md)]

Si la création de l’appareil virtuel échoue au cours de cette étape, vous ne disposerez peut-être pas d’une connectivité à Internet. Pour plus d’informations, consultez la section [Résolution des problèmes de connectivité Internet](#troubleshoot-internet-connectivity-errors) lors de la création d’un appareil virtuel.


### Étape 2 : configuration et inscription de l’appareil virtuel

Avant de commencer cette procédure, assurez-vous que vous disposez d’une copie de la clé de chiffrement des données de service. Cette clé de chiffrement a été créée lorsque vous avez configuré votre premier appareil StorSimple et que vous avez été invité à l’enregistrer dans un emplacement sécurisé. Si vous n’avez pas de copie de la clé de chiffrement des données de service, vous devez contacter le support technique de Microsoft pour obtenir de l’aide.

Procédez comme suit pour configurer et inscrire l’appareil virtuel StorSimple.
[AZURE.INCLUDE [Configuration et inscription de l’appareil virtuel](../../includes/storsimple-configure-register-virtual-device.md)]

### Étape 3 : (facultatif) modification des paramètres de configuration de l’appareil

La section suivante décrit les paramètres de configuration d’appareil que vous devez configurer pour l’appareil virtuel StorSimple si vous envisagez d’utiliser CHAP, le Gestionnaire d'instantanés StorSimple ou de modifier le mot de passe d’administrateur de l’appareil.

#### Configuration de l’initiateur CHAP

Ce paramètre contient les informations d’identification que votre appareil virtuel (cible) attend des initiateurs (serveurs) qui tentent d’accéder aux volumes. Les initiateurs fournissent un nom d’utilisateur et un mot de passe CHAP pour s’identifier auprès de votre appareil au cours de cette authentification. Pour obtenir la procédure détaillée, consultez la rubrique [Configuration de CHAP pour votre appareil](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### Configuration de la cible CHAP

Ce paramètre contient les informations d’identification que votre appareil virtuel utilise lorsqu’un initiateur CHAP demande une authentification mutuelle ou bidirectionnelle. Votre appareil virtuel utilise un nom d’utilisateur et un mot de passe CHAP inversés pour s’identifier auprès de l’initiateur pendant le processus d’authentification. Notez que les paramètres CHAP cibles sont des paramètres globaux. Lorsqu’ils sont appliqués, tous les volumes connectés à l’appareil virtuel de stockage utilisent l’authentification CHAP. Pour obtenir la procédure détaillée, consultez la rubrique [Configuration de CHAP pour votre appareil](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### Configuration du mot de passe du gestionnaire d’instantanés StorSimple

Le Gestionnaire d’instantanés StorSimple réside sur l’ordinateur hôte Windows et permet aux administrateurs de gérer les sauvegardes de votre appareil StorSimple sous la forme d’instantanés cloud ou locaux.

>[AZURE.NOTE] Pour l’appareil virtuel, l’ordinateur hôte Windows est une machine virtuelle Azure.

Lorsque vous configurez un appareil dans le Gestionnaire d’instantanés StorSimple, vous devez fournir l’adresse IP et le mot de passe de l’appareil StorSimple pour authentifier votre appareil de stockage. Pour obtenir la procédure détaillée, consultez la rubrique [Configuration du mot de passe de gestionnaire d'instantanés StorSimple](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### Modification du mot de passe d’administrateur de l’appareil 

Lorsque vous utilisez l’interface Windows PowerShell pour accéder à l’appareil virtuel, vous devez entrer un mot de passe Administrateur d’appareil. Pour la sécurité de vos données, vous êtes obligé de changer ce mot de passe avant de pouvoir utiliser l’appareil virtuel. Pour obtenir la procédure détaillée, consultez la rubrique [Configuration du mot de passe d’administrateur de l’appareil](storsimple-change-passwords.md#change-the-device-administrator-password).

## Se connecter à distance à l’appareil virtuel
L’accès à distance à votre appareil virtuel via l’interface Windows PowerShell n’est pas activé par défaut. Vous devez tout d’abord activer la gestion à distance sur l’appareil virtuel, puis sur le client qui sera utilisé pour accéder à votre appareil virtuel.

La procédure en deux étapes qui permet de se connecter à distance est détaillée ci-dessous.

### Étape 1 : configuration de la gestion à distance

Procédez comme suit pour configurer la gestion à distance de l’appareil virtuel StorSimple.

[AZURE.INCLUDE [Configuration de la gestion à distance via le protocole HTTP pour l’appareil virtuel](../../includes/storsimple-configure-remote-management-http-device.md)]

### Étape 2 : accès à distance à l'appareil virtuel

Après avoir activé la gestion à distance dans la page de configuration de l’appareil StorSimple, vous pouvez utiliser l’accès distant Windows PowerShell pour vous connecter à l’appareil virtuel à partir d’une autre machine virtuelle située dans le même réseau virtuel. Par exemple, vous pouvez vous connecter à partir de la machine virtuelle que vous avez configurée et utilisée pour vous connecter à iSCSI. Dans la plupart des déploiements, vous avez déjà ouvert un point de terminaison public pour accéder à votre machine virtuelle hôte que vous pouvez utiliser pour accéder à l’appareil virtuel.

>[AZURE.WARNING] **Pour renforcer la sécurité, nous vous recommandons d’utiliser le protocole HTTPS lors de la connexion aux points de terminaison et de supprimer ces derniers à la fin de la session à distance PowerShell.**

Vous devez respecter les procédures de la rubrique [Connexion à distance à votre appareil StorSimple](storsimple-remote-connect.md) afin de configurer la communication à distance pour votre appareil virtuel.

## Se connecter directement à l’appareil virtuel

Vous pouvez également vous connecter directement à l’appareil virtuel. Si vous souhaitez vous connecter directement à l’appareil virtuel à partir d’un autre ordinateur en dehors du réseau virtuel ou de l’environnement Microsoft Azure, vous devez créer des points de terminaison supplémentaires comme décrit dans la procédure suivante.

Procédez comme suit pour créer un point de terminaison public sur l’appareil virtuel :

[AZURE.INCLUDE [Création de points de terminaison publics sur un appareil virtuel](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Nous vous recommandons de vous connecter à partir d’une autre machine virtuelle à l’intérieur du même réseau virtuel, car cela réduit le nombre de points de terminaison publics sur votre réseau virtuel. Lorsque vous utilisez cette méthode, vous vous connectez simplement à la machine virtuelle via une session Bureau à distance, puis vous configurez cette machine virtuelle pour utilisation, comme vous le feriez avec tout autre client Windows sur un réseau local. Il est inutile d’ajouter le numéro de port public, car le port est déjà connu.

## Utilisation de l’appareil virtuel StorSimple

Maintenant que vous avez créé et configuré l’appareil virtuel StorSimple, vous êtes prêt à commencer à l’utiliser. Vous pouvez utiliser des conteneurs de volumes, des volumes et des stratégies de sauvegarde sur un appareil virtuel comme vous le feriez sur un appareil physique StorSimple ; la seule différence est que vous devez vous assurer que vous sélectionnez l’appareil virtuel à partir de votre liste d’appareils. Reportez-vous à la rubrique [Utilisation du service StorSimple Manager pour gérer un appareil virtuel](storsimple-manager-service-administration.md) pour connaître les procédures pas à pas des différentes tâches de gestion pour l'appareil virtuel.

Les sections suivantes présentent les différences que vous rencontrez lorsque vous utilisez l’appareil virtuel.

### Maintenance d’un appareil virtuel StorSimple

Comme il s’agit d’un appareil logiciel uniquement, la maintenance de l’appareil virtuel est minime par rapport à la maintenance de l’appareil physique. Vous disposez des options suivantes :

- **Mises à jour logicielles** – Vous pouvez afficher la date de dernière mise à jour du logiciel, ainsi que les messages d’état de mise à jour. Vous pouvez utiliser le bouton **Rechercher les mises à jour** en bas de la page pour effectuer une recherche manuelle des nouvelles mises à jour. Si les mises à jour sont disponibles, cliquez sur **Installer les mises à jour**. Étant donné qu’il n’y a qu’une interface sur l’appareil virtuel, cela signifie qu’il y aura une brève interruption de service lors de l’application des mises à jour. L’appareil virtuel s’arrête et redémarre (si nécessaire) pour appliquer les mises à jour publiées. Pour une procédure pas à pas, consultez la rubrique [Mise à jour de votre appareil](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Package de prise en charge** – Vous pouvez créer et télécharger un package de prise en charge pour aider le support Microsoft à résoudre les problèmes que vous rencontrez avec votre appareil virtuel. Pour une procédure pas à pas, consultez la rubrique [Création et gestion d’un package de support](storsimple-create-manage-support-package.md).

### Comptes de stockage d’un appareil virtuel

Les comptes de stockage sont créés pour une utilisation par l’appareil physique, par le service StorSimple Manager et par l’appareil virtuel. Lorsque vous créez vos comptes de stockage, nous vous recommandons d’utiliser un identificateur de région avec un nom convivial pour garantir la cohérence entre les différents composants système. Pour un appareil virtuel, il est important que tous les composants soient situés dans la même région afin d’éviter les problèmes de performances.

Pour une procédure pas à pas, consultez la rubrique [Ajout d’un compte de stockage](storsimple-manage-storage-accounts.md#add-a-storage-account).

### Désactivation d’un appareil virtuel StorSimple

La désactivation d’un appareil virtuel supprime la machine virtuelle et les ressources créées lors de son approvisionnement. Une fois l’appareil virtuel désactivé, il ne peut pas être restauré vers son état précédent. Avant de désactiver l’appareil virtuel, arrêtez ou supprimez les clients et ordinateurs hôtes qui en dépendent.

La désactivation d’un appareil virtuel entraîne les actions suivantes :

- L’appareil virtuel est supprimé.

- Les disques de données et de système d’exploitation de l’appareil virtuel sont supprimés.

- Le service hébergé et le réseau virtuel créés lors de l’approvisionnement sont conservés. Si vous ne les utilisez pas, vous devez les supprimer manuellement.

- Les instantanés cloud créés pour l’appareil virtuel sont conservés.

Pour une procédure pas à pas, consultez [Désactiver et supprimer votre appareil StorSimple](storsimple-deactivate-and-delete-device.md).

Dès que l’appareil virtuel est indiqué comme désactivé dans la page du service StorSimple Manager, vous pouvez le supprimer de la liste des appareils de la page **Appareils**.


### Démarrage, arrêt et redémarrage d’un appareil virtuel
Contrairement à l’appareil physique StorSimple, aucun bouton d’alimentation ou de mise hors tension n’est présent sur un appareil virtuel StorSimple. Toutefois, il se peut que vous deviez arrêter et redémarrer l’appareil virtuel. Par exemple, certaines mises à jour peuvent nécessiter le redémarrage de la machine virtuelle pour terminer le processus de mise à jour. Le moyen le plus simple pour vous permettre de démarrer, d’arrêter et de redémarrer un appareil virtuel est d’utiliser la console de gestion des machines virtuelles.

Lorsque vous examinez la console de gestion, l’état de l’appareil virtuel est **En cours d’exécution**, car il est démarré par défaut après sa création. Vous pouvez démarrer, arrêter et redémarrer une machine virtuelle à tout moment.

[AZURE.INCLUDE [Arrêt et redémarrage d’un appareil virtuel](../../includes/storsimple-stop-restart-virtual-device.md)]

### Rétablir les paramètres d’usine par défaut

Si vous décidez de repartir de zéro avec votre appareil virtuel, désactivez-le, supprimez-le, puis créez-en un nouveau. Comme c’est le cas également lorsque votre appareil physique est réinitialisé, aucune mise à jour n’est installée sur votre nouvel appareil virtuel. Par conséquent, recherchez les éventuelles mises à jour avant de l’utiliser.


## Basculement vers l’appareil virtuel

La récupération d’urgence est un des scénarios clés pour lequel l’appareil virtuel StorSimple a été conçu. Dans ce scénario, l’appareil physique StorSimple ou un centre de données entier peut ne pas être disponible. Heureusement, vous pouvez utiliser un appareil virtuel pour restaurer les opérations dans un autre emplacement. Pendant la récupération d’urgence, la propriété des conteneurs de volumes de l’appareil source change et ces derniers sont transférés vers l’appareil virtuel. Les conditions requises pour la récupération d’urgence sont les suivantes : l’appareil virtuel a été créé et configuré, tous les volumes du conteneur de volumes ont été mis hors connexion et le conteneur de volumes est associé à un instantané cloud.

>[AZURE.NOTE] 
>
> - Lorsque vous utilisez un appareil virtuel en tant qu’appareil secondaire pour la récupération d'urgence, n'oubliez pas que le modèle 8 010 dispose de 30 To de stockage Standard et que le modèle 8 020 dispose de 64 To de stockage Premium. La capacité de l’appareil virtuel 8 020, qui est plus importante, peut être plus adaptée à un scénario de récupération d'urgence.
> - Vous ne pouvez pas effectuer un basculement ou un clonage à partir d’un appareil exécutant Update 2 sur un appareil exécutant un logiciel de la mise à jour préliminaire 1. Vous pouvez toutefois basculer un appareil exécutant Update 2 vers un appareil exécutant Update 1 (1.1 ou 1.2)

Pour une procédure pas à pas, consultez la rubrique [Basculement vers un appareil virtuel](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## Arrêt ou suppression de l’appareil virtuel

Si vous avez précédemment configuré et utilisé un appareil virtuel StorSimple, mais que vous ne voulez plus cumuler de frais de calcul pour son utilisation, vous pouvez l’arrêter. L’arrêt de l’appareil virtuel ne supprime pas son système d’exploitation ni les disques de données du stockage. Cette opération arrête le cumul de frais sur votre abonnement, mais les frais de stockage pour les disques de système d’exploitation et de données continuent d’être facturés.

Si vous supprimez ou arrêtez l’appareil virtuel, il apparaît comme **Hors connexion** dans la page Appareils du service StorSimple Manager. Vous pouvez choisir de désactiver ou supprimer l’appareil si vous souhaitez également supprimer les sauvegardes créées par l’appareil virtuel. Pour plus d’informations, consultez [Désactiver et supprimer un appareil StorSimple](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Arrêt d’un appareil virtuel](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Suppression d’un appareil virtuel](../../includes/storsimple-delete-virtual-device.md)]

   
## Résolution des problèmes de connectivité Internet 

La création d’un appareil virtuel échoue si vous ne disposez d’aucune connectivité à Internet. Pour déterminer si le problème est lié à la connectivité Internet, procédez comme suit dans le portail Azure Classic :

1. Créez une machine virtuelle Windows Server 2012 dans Azure. Cette machine virtuelle doit utiliser le même compte de stockage, le même réseau virtuel et le même sous-réseau que ceux utilisés par votre appareil virtuel. Si vous disposez déjà d’un hôte Windows Server dans Azure en utilisant le même compte de stockage, le même réseau virtuel et le même sous-réseau, vous pouvez également l’utiliser pour résoudre vos problèmes de connectivité Internet.
2. Connectez-vous à distance à la machine virtuelle créée à l’étape précédente.
3. Ouvrez une fenêtre de commande à l’intérieur de la machine virtuelle (Win + R, puis tapez `cmd`).
4. À l’invite, entrez la commande suivante :

	`nslookup windows.net`

5. Si `nslookup` échoue, l’échec de la connectivité Internet empêche l’appareil virtuel de s’inscrire au service StorSimple Manager.
6. Apportez les modifications requises à votre réseau virtuel pour vous assurer que l’appareil virtuel est en mesure d’accéder aux sites Azure tels que « windows.net ».

## Étapes suivantes

- Découvrez comment [utiliser le service StorSimple Manager pour gérer un appareil virtuel](storsimple-manager-service-administration.md).
 
- Découvrez comment [restaurer un volume StorSimple à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md).

<!---HONumber=AcomDC_0928_2016-->