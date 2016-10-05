<properties 
   pageTitle="Configuration de MPIO sur l’hôte Linux StorSimple| Microsoft Azure"
   description="Configuration de MPIO sur StorSimple connecté à un hôte Linux exécutant CentOS 6.6"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# Configuration de MPIO sur un hôte StorSimple exécutant CentOS

Cet article explique les étapes requises pour configurer la MPIO (gestion multivoie d’E/S) sur votre serveur hôte CentOS 6.6. Le serveur hôte est connecté à votre appareil Microsoft Azure StorSimple pour la haute disponibilité via les initiateurs iSCSI. Il décrit en détail la détection automatique des appareils multivoies et l’installation spécifique uniquement pour les volumes StorSimple.

Cette procédure s’applique à tous les modèles d’appareils StorSimple série 8000.

>[AZURE.NOTE] Cette procédure ne peut pas être utilisée pour un appareil virtuel StorSimple. Pour plus d’informations, consultez Configuration des serveurs hôtes de votre appareil virtuel.

## À propos de la gestion multivoie 

La fonctionnalité de gestion multivoie vous permet de configurer plusieurs chemins d’accès d’E/S entre un serveur hôte et un appareil de stockage. Ces chemins d’accès d’E/S sont des connexions SAN physiques pouvant inclure des câbles distincts, des commutateurs, des interfaces réseau et des contrôleurs. La gestion multivoie agrège les chemins d’accès d’E/S pour configurer un nouvel appareil qui est associé à tous les chemins d’accès agrégés.

L’objectif de la gestion multivoie est double :

- **Haute disponibilité** : elle fournit un autre chemin en cas d’échec de tout élément du chemin d’accès d’E/S (p. ex., câble, commutateur, interface réseau ou contrôleur).

- **Équilibrage de charge** : selon la configuration de votre appareil de stockage, elle peut améliorer les performances en détectant des charges sur les chemins d’accès d’E/S et en les rééquilibrant dynamiquement.


### À propos des composants de la gestion multivoie 

Sous Linux, la gestion multivoie comprend des composants du noyau et des composants de l’espace utilisateur comme présenté ci-dessous.

- **Noyau** : le composant principal est le *mappeur d’appareils* qui redirige les E/S et prend en charge le basculement pour les chemins d’accès et les groupes de chemin d’accès.

1. **Espace utilisateur** : il s’agit des *outils de la gestion multivoie* permettant de gérer les appareils à chemins d’accès multiples en indiquant la marche à suivre au module multivoie du mappeur d’appareils. Les outils comprennent les éléments suivants :

	- **Multipath** : répertorie et configure les appareils à chemins d’accès multiples.
		
	- **Multipathd** : démon qui exécute la gestion multivoie et surveille les chemins d’accès.
	
	- **Devmap-name** : fournit un nom d’appareil explicite vers udev pour devmaps.
 
	- **Kpartx** : mappe les éléments devmaps linéaires aux partitions d’appareils pour effectuer des mappages multivoies configurables en partition.
	
	- **Multipath.conf** : fichier de configuration pour démon multivoie, utilisé pour remplacer la table de configuration intégrée.

### À propos du fichier de configuration multipath.conf

Le fichier de configuration `/etc/multipath.conf` rend un grand nombre de fonctionnalités de gestion multivoie configurables par l’utilisateur. La commande `multipath` et le démon de noyau `multipathd` utilisent les informations figurant dans ce fichier. Le fichier est consulté uniquement lors de la configuration des appareils multivoies. Assurez-vous d’avoir effectué toutes les modifications avant d’exécuter la commande `multipath`. Si vous modifiez le fichier par la suite, vous devrez arrêter, puis redémarrer multipathd pour que les modifications prennent effet.

Le fichier multipath.conf comporte cinq sections :

- **Valeurs par défaut au niveau système** *(defaults)* : vous pouvez remplacer les valeurs par défaut au niveau système.

1. **Appareils sur liste noire** *(blacklist)* : vous pouvez spécifier la liste des appareils qui ne doivent pas être contrôlés par le mappeur d’appareils.

1. **Exceptions de la liste noire** *(blacklist\_exceptions)* : vous pouvez identifier des appareils spécifiques devant être traités en tant qu’appareils multivoies même s’ils sont répertoriés dans la liste noire.

1. **Paramètres spécifiques au contrôleur de stockage** *(devices)* : vous pouvez spécifier des paramètres de configuration qui seront appliqués aux appareils contenant des informations de produit et de fournisseur.

1. **Paramètres spécifiques aux appareils** *(multipaths)* : vous pouvez utiliser cette section pour ajuster les paramètres de configuration des numéros d’unité logique individuels.

## Configuration de la gestion multivoie sur StorSimple connecté à l’hôte Linux

Un appareil StorSimple connecté à un hôte Linux peut être configuré pour la haute disponibilité et l’équilibrage de charge. Par exemple, si l’hôte Linux possède deux interfaces connectées au réseau SAN et que l’appareil possède deux interfaces connectées au réseau SAN de telle sorte qu’elles sont sur le même sous-réseau, 4 chemins d’accès sont disponibles. Cependant, si les interfaces DATA sur l’appareil et l’interface de l’hôte sont sur un autre sous-réseau IP (non routable), alors seuls 2 chemins d’accès sont disponibles. Vous pouvez configurer la gestion multivoie pour détecter automatiquement tous les chemins d’accès disponibles, choisir un algorithme d’équilibrage de charge pour ces chemins d’accès, appliquer des paramètres de configuration spécifiques pour les volumes StorSimple uniquement, puis activer et vérifier la gestion multivoie.

La procédure suivante décrit comment configurer la gestion multivoie lorsqu’un appareil StorSimple possédant deux interfaces réseau est connecté à un hôte avec deux interfaces réseau.

## Composants requis

Cette section détaille la configuration requise pour le serveur CentOS et votre appareil StorSimple.

### Sur l’hôte CentOS



1. Assurez-vous que votre hôte CentOS possède 2 interfaces réseau activées. Entrez :

	`ifconfig`

	L’exemple suivant montre la sortie lorsque deux interfaces réseau (`eth0` et `eth1`) sont présentes sur l’hôte.

    	[root@centosSS ~]# ifconfig
    	eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
      	inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
      	inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
      	inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
      	UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
     	RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:1000 
      	RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
    
    	eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
      	inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
      	inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
      	inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
      	UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
      	RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:1000 
      	RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
    
    	loLink encap:Local Loopback  
      	inet addr:127.0.0.1  Mask:255.0.0.0
      	inet6 addr: ::1/128 Scope:Host
      	UP LOOPBACK RUNNING  MTU:65536  Metric:1
      	RX packets:12 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:0 
      	RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)


1. Installez *iSCSI-initiator-utils* sur votre serveur CentOS. Effectuez les opérations suivantes pour installer *iSCSI-initiator-utils*.

	1. Connectez-vous en tant que `root` à votre hôte CentOS.

	1. Installez *iSCSI-initiator-utils*. Entrez :
		
		`yum install iscsi-initiator-utils`


	1. Après avoir correctement installé *iSCSI-Initiator-utils*, démarrez le service iSCSI. Entrez :

		`service iscsid start`

		Parfois, `iscsid` peut ne pas démarrer et l’option `--force` peut être nécessaire.

	1. Pour vous assurer que votre initiateur iSCSI est activé à l’heure de démarrage, utilisez la commande `chkconfig` pour activer le service.

		`chkconfig iscsi on`


	1. Pour vérifier que l’installation s’est correctement effectuée, exécutez la commande :
	
		`chkconfig --list | grep iscsi`
	
		Voici un exemple de sortie obtenue.

			iscsi   0:off   1:off   2:on3:on4:on5:on6:off
			iscsid  0:off   1:off   2:on3:on4:on5:on6:off

		Dans l’exemple ci-dessus, vous pouvez voir que votre environnement iSCSI s’exécutera à l’heure de démarrage sur les niveaux d’exécution 2, 3, 4 et 5.


1. Installez *device-mapper-multipath*. Entrez :

	`yum install device-mapper-multipath`

	L’installation démarre. Tapez **Y** pour continuer lorsque vous êtes invité à confirmer l’opération.



### Sur l’appareil StorSimple

Votre appareil StorSimple doit disposer des éléments suivants :

- Deux interfaces activées pour iSCSI minimum. Pour vérifier que deux interfaces sont compatibles iSCSI sur votre appareil StorSimple, effectuez les opérations suivantes dans le portail Azure Classic de votre appareil StorSimple :

	1. Connectez-vous au portail Classic de votre appareil StorSimple.

	1. Sélectionnez votre service StorSimple Manager, cliquez sur **Appareils** et choisissez l’appareil StorSimple concerné. Cliquez sur **Configurer** et vérifiez les paramètres d’interface réseau. Une capture d’écran avec deux interfaces réseau compatibles iSCSI est affichée ci-dessous. Ici, pour DATA 2 et DATA 3, les deux interfaces 10 Gigabit Ethernet sont activées pour iSCSI.
	
		![Configuration de MPIO StorSimple DATA 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
	
		![Configuration de MPIO StorSimple DATA 3](./media/storsimple-configure-mpio-on-linux/IC761348.png)

		Sur la page **Configurer**

		1. Assurez-vous que les deux interfaces réseau sont compatibles iSCSI. Le champ **Compatible iSCSI** doit être défini sur **Oui**.
		2. Vérifiez que les interfaces réseau ont la même vitesse, les deux devant être définies sur 1 Gigabit Ethernet ou 10 Gigabit Ethernet.
		3. Notez les adresses IPv4 des interfaces compatibles iSCSI et conservez-les pour une utilisation ultérieure sur l’hôte.


- Les interfaces iSCSI sur votre appareil StorSimple doivent être accessibles à partir du serveur CentOS.

	Pour vérifier ceci, vous devez fournir les adresses IP de vos interfaces réseau compatibles iSCSI StorSimple sur votre serveur hôte. Les commandes utilisées et la sortie correspondante avec DATA2 (10.126.162.25) et DATA3 (10.126.162.26) sont présentées ci-dessous :

    	[root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    	10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    	10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target


### Configuration matérielle

Nous vous recommandons de connecter les deux interfaces réseau iSCSI sur des chemins d’accès distincts pour la redondance. La figure ci-dessous illustre la configuration matérielle recommandée pour la gestion multivoie haute disponibilité et équilibrage de charge pour votre serveur CentOS et votre appareil StorSimple.

![Configuration matérielle de MPIO pour StorSimple sur l’hôte Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Comme indiqué dans la figure précédente :

- Votre appareil StorSimple est une configuration active/passive avec deux contrôleurs.

- Deux commutateurs SAN sont connectés à vos contrôleurs d’appareils.
 
- Deux initiateurs iSCSI sont activés sur votre appareil StorSimple.
 
- Deux interfaces réseau sont activées sur votre hôte CentOS.

La configuration ci-dessus produira 4 chemins d’accès distincts entre votre appareil et l’hôte si les interfaces de l’hôte et des données sont routables.

>[AZURE.IMPORTANT] 
>
>- Nous vous recommandons de ne pas mélanger les interfaces réseau 1 Gigabit Ethernet et 10 Gigabit Ethernet pour la gestion multivoie. Lorsque vous utilisez deux interfaces réseau, elles doivent être du même type.
>- Sur votre appareil StorSimple, DATA0, DATA1, DATA4 et DATA5 sont des interfaces 1 Gigabit Ethernet, alors que DATA2 et DATA3 sont des interfaces réseau 10 Gigabit Ethernet.|

## Configuration

Les étapes de configuration pour la gestion multivoie impliquent la configuration des chemins d’accès disponibles pour la détection automatique, la spécification de l’algorithme d’équilibrage de charge à utiliser, l’activation de la gestion multivoie et enfin la vérification de la configuration. Chacune de ces étapes est abordée en détail dans les sections suivantes.

### Étape 1 : Configurer la gestion multivoie pour la détection automatique

Les appareils pris en charge par la gestion multivoie peuvent être automatiquement détectés et configurés.

1. Initialisez le fichier `/etc/multipath.conf`. Entrez :

	 `Copy mpathconf --enable`
	
	La commande ci-dessus crée un fichier `sample/etc/multipath.conf`.


1. Démarrez le service de gestion multivoie. Entrez :

    ``Copy service multipathd start``
	
	La sortie suivante s’affiche :

	`Starting multipathd daemon:`

1. Activez la détection automatique des chemins d’accès multiples. Entrez :

	`mpathconf --find_multipaths y`

	Cela modifiera la section defaults de votre `multipath.conf` comme indiqué ci-dessous :

		defaults {
		find_multipaths yes
		user_friendly_names yes
		path_grouping_policy multibus
		}

### Étape 2 : Configurer la gestion multivoie pour les volumes StorSimple

Par défaut, tous les appareils sont sur liste noire dans le fichier multipath.conf et seront contournés. Vous devrez créer des exceptions de la liste noire pour autoriser la gestion multivoie pour les volumes à partir des appareils StorSimple.

1. Modifiez le fichier `/etc/mulitpath.conf`. Entrez :

	`vi /etc/multipath.conf`

1. Recherchez la section blacklist\_exceptions dans le fichier multipath.conf. Votre appareil StorSimple doit être répertorié comme une exception de la liste noire dans cette section. Vous pouvez supprimer les marques de commentaire des lignes appropriées dans ce fichier pour le modifier comme indiqué ci-dessous (utilisez uniquement le modèle spécifique de l’appareil que vous utilisez) :

    	blacklist_exceptions {
    	    device {
    	               vendor  "MSFT"
    	               product "STORSIMPLE 8100*"
    	    }
    	    device {
    	               vendor  "MSFT"
    	               product "STORSIMPLE 8600*"
    	    }
    	   }

### Étape 3 : Configurer la gestion multivoie de tourniquet (round robin)

Cet algorithme d’équilibrage de charge utilise tous les chemins d’accès multiples disponibles pour le contrôleur actif de manière équilibrée, en tourniquet (round robin).

1. Modifiez le fichier `/etc/multipath.conf`. Entrez :

	`vi /etc/multipath.conf`

1. Sous la section `defaults`, définissez `path_grouping_policy` sur `multibus`. `path_grouping_policy` spécifie la stratégie de regroupement de chemins d’accès par défaut à appliquer aux chemins d’accès multiples non spécifiés. La section defaults ressemblera à ce qui est présenté ci-dessous.

	    defaults {
	            user_friendly_names yes
	            path_grouping_policy multibus
	    }



> [AZURE.NOTE] 
Les valeurs les plus courantes de `path_grouping_policy` incluent :
	
> - basculement = 1 chemin d’accès par groupe de priorité
> - multibus = tous les chemins d’accès valides dans 1 groupe de priorité

### Étape 4 : Activer la gestion multivoie

1. Redémarrez le démon `multipathd`. Tapez :

    `service multipathd restart`

1. La sortie sera comme indiqué ci-dessous :

    	[root@centosSS ~]# service multipathd start
    	Starting multipathd daemon:  [OK]




### Étape 5 : Vérifier la gestion multivoie

1. Tout d’abord, assurez-vous que la connexion iSCSI est établie avec l’appareil StorSimple comme suit :


	1. Détectez votre appareil StorSimple. Entrez :
		
		`iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`

		La sortie lorsque l’adresse IP pour DATA0 est 10.126.162.25 et que le port 3260 est ouvert sur l’appareil StorSimple pour le trafic iSCSI sortant est comme indiqué ci-dessous :

		    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
		    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target


		Copiez le nom qualifié de votre appareil StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, à partir de la sortie précédente.



	1. Connectez-vous à l’appareil à l’aide du nom qualifié cible. L’appareil StorSimple est la cible iSCSI ici. Entrez :

		`iscsiadm -m node --login -T <IQN of iSCSI target>`

		L’exemple suivant illustre la sortie avec un nom qualifié cible de `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. La sortie indique que vous vous êtes correctement connecté aux deux interfaces réseau compatibles iSCSI sur votre appareil.

		    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
	    	Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
	    	Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
	    	Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
	    	Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
	    	Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
	    	Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
	    		Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.


		Si vous ne voyez qu’une seule interface hôte et deux chemins d’accès ici, vous devez activer les deux interfaces sur l’hôte pour iSCSI. Vous pouvez suivre les [instructions détaillées dans la documentation Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

	
	1. Un volume est exposé au serveur CentOS à partir de l’appareil StorSimple. Pour plus d’informations, consultez [Étape 6 : Créer un volume](storsimple-deployment-walkthrough.md#step-6-create-a-volume) via le portail Azure Classic sur votre appareil StorSimple.

	1. Vérifiez les chemins d’accès disponibles. Entrez :

		`multipath –l`

		L’exemple suivant montre la sortie de deux interfaces réseau sur un appareil StorSimple connecté à une interface réseau de l’hôte unique avec deux chemins d’accès disponibles.

		    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    		size=100G features='0' hwhandler='0' wp=rw
    		`-+- policy='round-robin 0' prio=0 status=active
    		  |- 7:0:0:1 sdc 8:32 active undef running
    		  `- 6:0:0:1 sdd 8:48 active undef running

		L’exemple suivant montre la sortie de deux interfaces réseau sur un appareil StorSimple connecté à deux interfaces réseau de l’hôte avec quatre chemins d’accès disponibles.
		
		    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    		size=100G features='0' hwhandler='0' wp=rw
    		`-+- policy='round-robin 0' prio=0 status=active
    		  |- 17:0:0:0 sdb 8:16 active undef running
    		  |- 15:0:0:0 sdd 8:48 active undef running
    		  |- 14:0:0:0 sdc 8:32 active undef running
    		  `- 16:0:0:0 sde 8:64 active undef running

		Après avoir configuré les chemins d’accès, reportez-vous aux instructions spécifiques de votre système d’exploitation hôte (CentOS 6.6) pour monter et formater ce volume.


## Résolution des problèmes de la gestion multivoie

Cette section fournit des conseils utiles si vous rencontrez des problèmes lors de la configuration de la gestion multivoie.

Q : Je ne vois pas les modifications apportées au fichier `multipath.conf` s’appliquer.

A. Si vous avez apporté des modifications au fichier `multipath.conf`, vous devez redémarrer le service de gestion multivoie. Tapez la commande suivante :
    
    service multipathd restart

Q : J’ai activé deux interfaces réseau sur l’appareil StorSimple et deux interfaces réseau sur l’hôte. Lorsque je répertorie les chemins d’accès disponibles, je ne vois que deux chemins d’accès. Je m’attendais à voir quatre chemins d’accès disponibles.

A. Vérifiez que les deux chemins d’accès sont sur le même sous-réseau et routables. Si les interfaces réseau se trouvent sur des réseaux locaux virtuels différents et non routables, vous ne verrez que deux chemins d’accès. L’un des moyens de vérifier cela consiste à vous assurer que vous pouvez atteindre les deux interfaces hôte à partir d’une interface réseau sur l’appareil StorSimple. Vous devrez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) car cette vérification ne peut être effectuée qu’au moyen d’une session de support.

Q : Lorsque je répertorie les chemins d’accès disponibles, je ne vois aucune sortie.

A. En règle générale, le non-affichage de chemins d’accès multiples suggère un problème avec le démon de gestion multivoie et il est très probable que le problème éventuel réside dans le fichier `multipath.conf`.

Il convient également de vérifier que vous pouvez voir certains disques après la connexion à la cible, car l’absence de réponse des listes multivoies peut signifier que vous n’avez aucun disque.

- Pour relancer l’analyse du bus SCSI, utilisez la commande suivante :
 
	`$ rescan-scsi-bus.sh `(partie du package sg3\_utils)
 
- Tapez les commandes suivantes :

	`$ dmesg | grep sd*`
 
- Ou

	`$ fdisk –l`
 
	Celles-ci renverront les détails des disques récemment ajoutés.
  
- Pour déterminer s’il s’agit d’un disque StorSimple, utilisez les commandes suivantes :
 
	`cat /sys/block/<DISK>/device/model`
 
	Cela renvoie une chaîne qui déterminera s’il s’agit d’un disque StorSimple.

Cause moins probable mais possible, la présence d’un PID iSCSI obsolète. Utilisez la commande suivante pour vous déconnecter des sessions iSCSI :

    iscsiadm -m node --logout -p <Target_IP>

Répétez cette commande pour toutes les interfaces réseau connectées sur la cible iSCSI, qui est votre appareil StorSimple. Une fois que vous vous êtes déconnecté de toutes les sessions iSCSI, utilisez le nom qualifié cible iSCSI pour rétablir la session iSCSI. Tapez la commande suivante :

    iscsiadm -m node --login -T <TARGET_IQN>


Q : Je ne sais pas si mon appareil figure dans la liste approuvée.

A. Pour vérifier que votre appareil figure dans la liste approuvée, utilisez la commande interactive de résolution des problèmes suivante :

	multipathd –k
	multipathd> show devices
	available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Pour plus d’informations, accédez à [Utilisation de la commande interactive de résolution des problèmes pour la gestion multivoie](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## Liste des commandes utiles

|Type|Commande|Description|
|---|---|---|
|**iSCSI**|`service iscsid start`|Démarrer le service iSCSI|
||`service iscsid stop`|Arrêter le service iSCSI|
||`service iscsid restart`|Redémarrer le service iSCSI|
||`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>`|Détecter les cibles disponibles à l'adresse spécifiée|
||`iscsiadm -m node --login -T <TARGET_IQN>`|Se connecter à la cible iSCSI|
||`iscsiadm -m node --logout -p <Target_IP>`|Se déconnecter de la cible iSCSI|
||`cat /etc/iscsi/initiatorname.iscsi`|Imprimer le nom de l'initiateur iSCSI|
||`iscsiadm –m session –s <sessionid> -P 3`|Vérifier l'état de la session iSCSI et le volume détecté sur l’hôte|
||`iscsi –m session`|Affiche toutes les sessions iSCSI établies entre l'hôte et l'appareil StorSimple|
| | | |
|**Gestion multivoie**|`service multipathd start`|Démarrer le démon multivoie|
||`service multipathd stop`|Arrêter le démon multivoie|
||`service multipathd restart`|Redémarrer le démon multivoie|
||`chkconfig multipathd on` </br> OU </br> `mpathconf –with_chkconfig y`|Activer le lancement du démon multivoie au démarrage|
||`multipathd –k`|Démarrer la console interactive pour le dépannage|
||`multipath –l`|Lister les connexions et les périphériques multivoie|
||`mpathconf --enable`|Créer un exemple de fichier mulitpath.conf dans `/etc/mulitpath.conf`|
||||

## Étapes suivantes

Étant donné que vous configurez MPIO sur un hôte Linux, vous pouvez avoir besoin de consulter les documents CentOS 6.6 suivants :

- [Configuration de MPIO sur CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
- [Guide de formation Linux](http://linux-training.be/files/books/LinuxAdm.pdf)

<!---HONumber=AcomDC_0921_2016-->