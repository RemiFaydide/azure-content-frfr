<properties
   pageTitle="Guide de démarrage rapide pour l’installation manuelle de SAP HANA sur des machines virtuelles Azure | Microsoft Azure"
   description="Guide de démarrage rapide pour l’installation manuelle de SAP HANA sur des machines virtuelles Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# Guide de démarrage rapide pour l’installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure

## Introduction

Ce guide de démarrage rapide vous aide à configurer un système de prototype/démonstration SAP HANA à instance unique sur des machines virtuelles Azure via une installation manuelle de SAP NetWeaver 7.5 et SAP HANA SP12. Ce guide suppose que le lecteur est familiarisé avec les fondamentaux d’Azure IaaS comme le déploiement de machines virtuelles ou de réseaux virtuels à l’aide du Portail Azure ou de Powershell/CLI et la possibilité d’utiliser des modèles JSON. Le lecteur doit également être familiarisé avec SAP HANA, SAP NetWeaver et leur mode d’installation en local.

Le lecteur doit avoir connaissance de la documentation SAP-Azure générale mentionnée dans la section des informations générales à la fin de l’article.

Dans la mesure où il se limite aux systèmes hors production, ce guide ne traite pas certains sujets comme la haute disponibilité, la sauvegarde, la récupération d’urgence ou la haute performance et n’aborde pas de considération de sécurité particulière.

Dans le cadre de l’exemple d’installation, deux machines virtuelles ont été utilisées pour réaliser une installation SAP NetWeaver distribuée à l’aide du modèle Azure Resource Manager. En effet, SAP-Linux-Azure est pris en charge par Azure Resource Manager uniquement et non par le modèle classique. Vous trouverez des liens vers davantage d’informations sur Azure Resource Manager dans la section des informations générales à la fin de cet article.

Voici les deux machines virtuelles de test utilisées pour l’exemple d’installation :

* hana-appsrv (type DS3) pour héberger l’instance NW 7.5 ASCS + PAS.
* hana-dbsrv (type GS4) pour héberger HANA SP12.
* Les deux machines virtuelles appartenaient à un réseau virtuel Azure (azure-hana-test-vnet).
* Dans les deux cas, le système d’exploitation était SLES 12 SP1.


Cependant, sachez que depuis juillet 2016, SAP HANA bénéficie d’une prise en charge totale uniquement pour les systèmes de production OLAP (BW) sur machine virtuelle Azure type GS5. Pour la réalisation de tests, où une prise en charge officielle de SAP n’est pas envisagée, un type inférieur, comme GS4, peut être utilisé. Pour SAP HANA sur Azure, le Stockage Premium Azure doit toujours être utilisé pour les fichiers de données et journaux HANA. Consultez la section « Configuration des disques » plus bas. Pour plus d’informations sur les produits SAP pris en charge sur Azure, consultez la section des informations générales à la fin de cet article.


Le guide décrit deux manières d’installer manuellement SAP HANA sur des machines virtuelles Azure :

* Installation de SAP HANA à l’aide du gestionnaire de déploiement de logiciels (SWPM, Software Provisioning Manager) SAP, dans le cadre d’une installation NetWeaver distribuée, à l’étape « instance de base de données »
* Installation de SAP HANA à l’aide de l’outil HANA Life Cycle Manager (hdblcm), puis de NetWeaver

Il est également possible d’utiliser SWPM et d’installer tous les composants (SAP HANA, serveur d’applications SAP, instance ASCS, interface utilisateur graphique SAP) sur une seule machine virtuelle. Cette option n’est pas décrite dans cet article, mais les éléments à prendre en considération sont les mêmes.

Avant de commencer l’installation, lisez la section située après les listes de contrôle ci-dessous. Elle concerne la configuration des machines virtuelles de test Azure et vise à éviter plusieurs erreurs de base qui se produiront si vous utilisez uniquement une configuration de machine virtuelle Azure par défaut.


## Liste de contrôle pour l’installation de SAP HANA avec SAP SWPM

Il s’agit d’une simple liste de contrôle couvrant les éléments clés liés à l’installation manuelle d’un système SAP HANA à instance unique à des fins de démonstration ou de prototypage, dans le cadre d’une installation distribuée de SAP NW 7.5 avec SAP SWPM. Chaque élément est expliqué plus en détail et présenté sous forme de captures d’écran dans l’article :

* Créer un réseau virtuel Azure qui inclut les deux machines virtuelles de test.
* Déployer deux machines virtuelles Azure avec le système d’exploitation SLES 12 SP1 à l’aide du modèle Azure Resource Manager.
* Attacher deux disques de stockage standard à la machine virtuelle du serveur d’applications (par exemple, 75 Go et 500 Go).
* Attacher quatre disques à la machine virtuelle du serveur de base de données HANA : 2 disques de stockage standard, comme pour la machine virtuelle du serveur d’applications et 2 disques de stockage Premium (par exemple, 2 disques de 512 Go).
* Selon les spécifications de taille et/ou de débit, attacher plusieurs disques et créer des volumes agrégés par bandes avec lvm ou mdadm au niveau du système d’exploitation dans la machine virtuelle.
* Créer des systèmes de fichiers XFS sur les disques attachés/volumes logiques.
* Monter les nouveaux systèmes de fichiers XFS au niveau du système d’exploitation. Utiliser un système de fichiers pour stocker tous les logiciels SAP et l’autre, par exemple, pour le répertoire sapmnt et éventuellement les sauvegardes. Sur le serveur de base de données SAP HANA, monter les systèmes de fichiers XFS sur les disques de stockage Premium en tant que /hana et /usr/sap. Cette opération est nécessaire pour éviter la saturation du système de fichiers racine, peu volumineux sur les machines virtuelles Linux Azure.
* Entrer les adresses IP locales des machines virtuelles de test dans /etc/hosts.
* Entrer le paramètre nofail dans /etc/fstab.
* Définir les paramètres de noyau en fonction de la note SAP HANA-SLES-12 (pour obtenir des informations détaillées, voir la section sur les paramètres de noyau plus bas).
* Ajouter un espace d’échange.
* Le cas échéant, installer un bureau graphique sur les machines virtuelles de test. Sinon, utiliser une installation sapinst distante.
* Télécharger les logiciels SAP sur le SAP Service Marketplace.
* Installer l’instance SAP ASCS sur la machine virtuelle du serveur d’applications.
* Partager le répertoire sapmnt par l’intermédiaire de NFS entre les machines virtuelles de test (la machine virtuelle du serveur d’applications est le serveur NFS).
* Avec l’outil SWPM, installer l’instance de base de données incluant HANA sur la machine virtuelle du serveur de base de données.
* Installer le PAS sur la machine virtuelle du serveur d’applications.
* Démarrer la console de gestion SAP et se connecter par le biais de l’interface utilisateur graphique SAP/HANA Studio, par exemple.



## Liste de contrôle pour l’installation de SAP HANA avec hdblcm

Il s’agit d’une simple liste de contrôle couvrant les éléments clés liés à l’installation manuelle d’un système SAP HANA à instance unique à des fins de démonstration ou de prototypage, dans le cadre d’une installation distribuée de SAP NW 7.5 avec SAP SWPM. Chaque élément est expliqué plus en détail et présenté sous forme de captures d’écran dans l’article :

* Créer un réseau virtuel Azure qui inclut les deux machines virtuelles de test.
* Déployer deux machines virtuelles Azure avec le système d’exploitation SLES 12 SP1 à l’aide du modèle Azure Resource Manager.
* Attacher deux disques de stockage standard à la machine virtuelle du serveur d’applications (par exemple, 75 Go et 500 Go).
* Attacher quatre disques à la machine virtuelle du serveur de base de données HANA : 2 disques de stockage standard, comme pour la machine virtuelle du serveur d’applications et 2 disques de stockage Premium (par exemple, 2 disques de 512 Go).
* Selon les spécifications de taille et/ou de débit, attacher plusieurs disques et créer des volumes agrégés par bandes avec lvm ou mdadm au niveau du système d’exploitation dans la machine virtuelle.
* Créer des systèmes de fichiers XFS sur les disques attachés/volumes logiques.
* Monter les nouveaux systèmes de fichiers XFS au niveau du système d’exploitation. Utiliser un système de fichiers pour stocker tous les logiciels SAP et l’autre, par exemple, pour le répertoire sapmnt et éventuellement les sauvegardes. Sur le serveur de base de données SAP HANA, monter les systèmes de fichiers XFS sur les disques de stockage Premium en tant que /hana et /usr/sap. Cette opération est nécessaire pour éviter la saturation du système de fichiers racine, peu volumineux sur les machines virtuelles Linux Azure.
* Entrer les adresses IP locales des machines virtuelles de test dans /etc/hosts.
* Entrer le paramètre nofail dans /etc/fstab.
* Définir les paramètres de noyau en fonction de la note SAP HANA-SLES-12 (pour obtenir des informations détaillées, voir la section sur les paramètres de noyau plus bas).
* Ajouter un espace d’échange.
* Le cas échéant, installer un bureau graphique sur les machines virtuelles de test. Sinon, utiliser une installation sapinst distante.
* Télécharger les logiciels SAP sur le SAP Service Marketplace.
* Créer un groupe « sapsys » avec l’ID de groupe 1001 sur la machine virtuelle du serveur de base de données HANA
* Installer SAP HANA sur la machine virtuelle du serveur de base de données à l’aide de hdblcm
* Installer l’instance SAP ASCS sur la machine virtuelle du serveur d’applications.
* Partager le répertoire sapmnt par l’intermédiaire de NFS entre les machines virtuelles de test (la machine virtuelle du serveur d’applications est le serveur NFS).
* Avec l’outil SWPM, installer l’instance de base de données incluant HANA sur la machine virtuelle du serveur de base de données.
* Installer le PAS sur la machine virtuelle du serveur d’applications.
* Démarrer la console de gestion SAP et se connecter par le biais de l’interface utilisateur graphique SAP/HANA Studio, par exemple.




## Préparation des machines virtuelles Azure pour l’installation manuelle de SAP HANA

Ce chapitre sur la préparation des machines virtuelles Azure pour l’installation manuelle de SAP HANA se compose de cinq sections, qui couvrent les sujets suivants :

* Configuration des disques
* Paramètres de noyau
* Systèmes de fichiers
* /etc/hosts
* /etc/fstab


### Configuration des disques

La taille du système de fichiers racine dans une machine virtuelle Linux sur Azure est limitée. Par conséquent, il est nécessaire d’attacher un espace disque supplémentaire à une machine virtuelle pour l’exécution de SAP. Dans le cas d’une machine virtuelle de serveur d’applications SAP utilisée dans un environnement de prototype/démonstration pur, des disques de stockage Azure standard peuvent être utilisés. En revanche, pour les fichiers de données et journaux de base de données SAP HANA, des disques de stockage Premium Azure doivent être utilisés, même dans un environnement hors production.

Pour obtenir des informations détaillées sur la façon d’attacher des disques à une machine virtuelle Linux, cliquez [ici](virtual-machines-linux-add-disk.md).

Pour les disques qui seront utilisés pour stocker les journaux de transactions HANA, il ne faut définir aucune mise en cache du disque Azure. Pour les fichiers de données HANA, il est possible d’utiliser la mise en cache en lecture. Comme HANA est une base de données en mémoire, la mesure dans laquelle le cache de lecture au niveau du disque Azure améliore les performances dépend du modèle d’utilisation générale (par exemple, démarrage de HANA et lecture des données du disque vers la mémoire).

Pour obtenir des informations détaillées sur le stockage Premium Azure, cliquez [ici](../storage/storage-premium-storage.md).

Pour accéder à des exemples de modèles JSON pour créer des machines virtuelles, cliquez [ici](https://github.com/Azure/azure-quickstart-templates). « 101-vm-simple-linux » montre comment se présente un modèle de base et inclut la section de stockage qui ajoute un disque de données de 100 Go.

[Cet article](virtual-machines-linux-sap-on-suse-quickstart.md) offre des informations sur la recherche d’une image SUSE par le biais de Powershell ou de l’interface CLI et sur l’importance d’attacher un disque à l’aide d’un UUID.


Selon la taille du système et les spécifications de débit, il peut être nécessaire d’attacher plusieurs disques plutôt qu’un seul et de créer par la suite un agrégat par bandes sur ces disques au niveau du système d’exploitation. Voici les deux aspects motivant la création d’un agrégat par bandes sur plusieurs disques Azure :

* Augmentation du débit
* Besoin d’un système de fichiers unique supérieur à 1 To, dans la mesure où la taille actuelle du disque Azure est limitée à 1 To (depuis juillet 2016)


Pour plus d’informations sur les deux principaux outils permettant de configurer l’agrégation par bandes, reportez-vous aux articles suivants :

[Article sur l’utilisation de mdadm pour configurer un RAID logiciel Linux sur une machine virtuelle Azure](virtual-machines-linux-configure-raid.md)

[Article sur la configuration du Gestionnaire de volumes logiques sur une machine virtuelle Linux Azure](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

Dans l’environnement de test, deux disques de stockage Azure standard ont été attachés à la machine virtuelle du serveur applications SAP. L’un d’eux était utilisé pour stocker tous les logiciels SAP pour l’installation (par exemple, NetWeaver 7.5, l’interface utilisateur graphique SAP, SAP HANA, etc.) et l’autre pour fournir l’espace nécessaire aux autres éléments requis (par exemple, sauvegarde et données de test) et pour le répertoire sapmnt (par exemple, les profils SAP) à partager entre toutes les machines virtuelles appartenant au même paysage SAP.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

Contrairement au cas de la machine virtuelle du serveur d’applications, quatre disques ont été attachés à la machine virtuelle du serveur SAP HANA. Comme précédemment, deux disques étaient utilisés pour stocker les logiciels SAP (il était également possible de partager le disque des logiciels SAP par l’intermédiaire de NFS) et pour fournir l’espace nécessaire, notamment pour la sauvegarde. Les deux disques supplémentaires étaient des disques de stockage Premium Azure utilisés pour stocker les fichiers journaux et de données SAP HANA, ainsi que le répertoire /usr/sap.


### Paramètres de noyau


SAP HANA nécessite des paramètres de noyau Linux spécifiques, qui ne font pas partie des images de galerie Azure standard et doivent être définis manuellement. Il existe une note SAP spécifique qui décrit les paramètres.


Note SAP SAP HANA DB : Recommended OS settings for SLES 12 / SLE for SAP Applications 12 (Paramètres de système d’exploitation recommandés pour SLES 12 / SLE for SAP Applications 12) : [ Note SAP 2205917](https://launchpad.support.sap.com/#/notes/2205917)

Vous trouverez également une autre rubrique sur le cache de page, dans le cadre de l’exécution de SAP HANA sur SLES, en cliquant [ici](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache) : chapitre 6.1 Kernel: Page-Cache Limit (Noyau : limite de cache de page).

Il existe également une Note SAP concernant la limite du cache de page : [Note SAP 1557506](https://service.sap.com/sap/support/notes/1557506).

SLES 12 offre un nouvel outil qui remplace l’ancien utilitaire sapconf : « tuned-adm ». Un profil SAP HANA spécial est disponible. Des informations supplémentaires sur cet outil sont disponibles en suivant les deux liens ci-dessous.

Vous trouverez [ici](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) la documentation SLES sur le profil tuned-adm sap-hana.

Vous trouverez [ici](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) la documentation SLES sur le profil tuned-adm sap-hana : chapitre 6.2 Tuning Systems for SAP Workloads with tuned-adm (Systèmes de paramétrage pour les charges de travail SAP avec tuned-adm).


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Vous pouvez voir ici comment « tuned-adm » a modifié les valeurs transparent\_hugepage et numa\_balancing, selon les paramètres SAP HANA requis.


Pour conserver les paramètres de noyau SAP HANA, il convient d’utiliser grub2 sur SLES 12. Pour plus d’informations sur grub2, cliquez [ici](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html).


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Cette capture d’écran montre comment les paramètres de noyau ont été modifiés dans le fichier de configuration, puis « compilés » via grub2-mkconfig.


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

Vous pouvez également modifier les paramètres par le biais de Yast et des paramètres de configuration de noyau du chargeur de démarrage.


### Systèmes de fichiers 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Vous pouvez voir ici les deux systèmes de fichiers qui ont été créés sur la machine virtuelle du serveur d’applications SAP, sur les deux disques de stockage Azure standard. Les deux systèmes de fichiers sont de type XFS et montés sur /sapdata et /sapsoftware.

Il n’est pas obligatoire de procéder de cette façon. Il existe différentes façons de structurer l’espace disque. L’aspect le plus important est d’éviter que le système de fichiers racine manque d’espace.


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

En ce qui concerne la machine virtuelle de base de données SAP HANA, il est important de savoir que lors d’une installation de base de données avec sapinst (swpm), si vous utilisez simplement l’option d’installation « typical » (par défaut), les éléments sont installés par défaut sous /hana et /usr/sap. Le paramètre par défaut pour la sauvegarde de journal SAP HANA peut se trouver sous /usr/sap. Comme précédemment, cet aspect est essentiel pour éviter que le système de fichiers racine ne manque d’espace. Par conséquent, vous devez vous assurer que l’espace libre sous /hana et /usr/sap est suffisant avant d’installer SAP HANA via swpm.

[Cet article](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) SAP décrit la disposition du système de fichiers standard de SAP HANA.


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

Lors de l’installation de SAP NetWeaver sur une image de galerie Azure SLES 12 standard, un message indique l’absence d’espace d’échange. Pour supprimer ce message, vous pouvez, par exemple, ajouter manuellement un fichier d’échange comme décrit dans ce document, avec dd, mkswap et swapon. Recherchez simplement « Adding a Swap File Manually » (Ajout manuel d’un fichier d’échange) dans [cet article](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html).

Vous pouvez également configurer l’espace d’échange par le biais de l’agent de machine virtuelle Linux. Des informations supplémentaires sont disponibles [ici](virtual-machines-linux-agent-user-guide.md).


### /etc/hosts

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

Un autre aspect clé doit être pris en considération avant de commencer l’installation de SAP : vous devez inclure les noms d’hôtes et les adresses IP des machines virtuelles SAP dans le fichier /etc/hosts. Vous devez déployer toutes les machines virtuelles SAP dans un même réseau virtuel Azure, puis utiliser les adresses IP internes.

### /etc/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

Pendant la phase de test, il s’est avéré judicieux d’ajouter le paramètre nofail à fstab. En cas de problème avec les disques, la machine virtuelle s’affichera et ne restera pas bloquée dans le processus de démarrage. Dans ce cas, cependant, il faut savoir que l’espace disque supplémentaire ne sera peut-être pas disponible et que les processus peuvent saturer le système de fichiers racine. Par ailleurs, si /hana est manquant, SAP HANA ne démarrera pas.


## Installation du bureau graphique Gnome sur SLES 12

Ce chapitre se compose de deux sections qui couvrent les sujets suivants :

* Installation du bureau Gnome et de xrdp sur SLES 12
* Exécution de la console de gestion SAP basée sur Java avec Firefox sur SLES 12

Vous pouvez également utiliser d’autres solutions, comme Xterminal et VNC. Cependant, à compter de septembre 2016, cet article décrit uniquement xrdp.

### Installation du bureau Gnome et de xrdp sur SLES 12

Pour ceux qui utilisent Microsoft Windows en arrière-plan et voudraient utiliser un bureau graphique directement dans les machines virtuelles SAP Linux pour exécuter Firefox, Sapinst, l’interface utilisateur graphique SAP, la console de gestion SAP ou HANA Studio et éventuellement se connecter à la machine virtuelle avec RDP à partir d’un ordinateur Microsoft Windows, il existe un moyen simple pour y parvenir. Si cette procédure n’est peut-être pas appropriée dans certains cas, par exemple pour un serveur de base de données de production, elle est acceptable pour un environnement de prototype/démonstration pur. Voici les étapes à suivre pour installer le bureau Gnome sur une machine virtuelle Azure SLES 12 :

Installez le bureau Gnome à l’aide de la commande suivante (que vous pouvez entrer dans une fenêtre PuTTY, par exemple) :

   zypper in -t pattern gnome-basic

Puis, installez xrdp pour autoriser la connexion à la machine virtuelle avec RDP :

   zypper in xrdp

Modifiez /etc/sysconfig/windowmanager et définissez Gnome comme Gestionnaire de fenêtrage par défaut :

   DEFAULT\_WM="gnome"

Exécutez la commande chkconfig pour vous assurer que xrdp démarre automatiquement après un redémarrage :

  chkconfig -level 3 xrdp on

En cas de problème avec la connexion RDP, essayez de redémarrer (éventuellement à l’aide d’une fenêtre PuTTY) :

  /etc/xrdp/xrdp.sh restart

Si le redémarrage de xrdp, tel que décrit ci-dessus, ne fonctionne pas, vérifiez la présence d’un fichier .pid et supprimez-le, le cas échéant :

  Vérifiez /var/run et recherchez xrdp.pid. Supprimez-le, puis essayez à nouveau de redémarrer.



### Console de gestion SAP


Lorsque vous démarrez la console de gestion SAP graphique basée sur Java à partir de Firefox sur une machine virtuelle Azure SLES 12 après avoir installé le bureau Gnome comme décrit dans la section précédente, vous obtenez une erreur due à l’absence de plug-in de navigateur Java.

L’URL de démarrage de la console de gestion SAP est <serveur>: 5< numéro\_instance >13

Pour plus d’informations, cliquez [ici](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

Sur la capture d’écran ci-dessus, vous pouvez voir comment le message d’erreur se présente lorsque le plug-in de navigateur Java est manquant.

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Vous pouvez résoudre le problème simplement en installant le plug-in manquant avec Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

Si vous entrez à nouveau l’URL de la console de gestion SAP, une petite boîte de dialogue s’affiche la première fois, vous invitant à activer le plug-in.


Un autre message d’erreur peut s’afficher, indiquant l’absence du fichier javafx.properties. Ce problème est généralement lié à l’installation de Java 1.8, nécessaire pour l’interface utilisateur graphique SAP 7.4.

La version d’IBM Java visible avec Yast n’inclut pas ce fichier. La solution consiste à télécharger Java à partir d’Oracle. Vous trouverez [ici](https://scn.sap.com/thread/3908306) un article abordant ce problème.



## Installation manuelle de SAP HANA avec SWPM dans le cadre d’une installation de NetWeaver 7.5


La suite de captures d’écran suivante illustre les principales étapes d’installation de SAP NetWeaver 7.5 et SAP HANA SP12 avec SWPM (sapinst). Lors de l’installation de NW 7.5, SWPM offre la possibilité d’installer également la base de données HANA en tant qu’instance unique.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

Pour l’environnement de test, un seul serveur d’applications ABAP a été installé. L’option « Distributed System » (Système distribué) a été utilisée pour installer l’instance ASCS et l’instance de serveur d’applications principal sur une machine virtuelle Azure et SAP HANA en tant que système de base de données sur une autre machine virtuelle Azure.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

Une fois que l’instance ASCS est installée sur la machine virtuelle du serveur d’applications et définie en vert dans la console de gestion SAP, le répertoire sapmnt, qui inclut notamment le répertoire de profil SAP, doit être partagé avec la machine virtuelle du serveur de base de données SAP HANA. L’étape d’installation de la base de données requiert l’accès à ces informations. Le meilleur moyen de procéder consiste à utiliser NFS, qui peut être configuré à l’aide de Yast.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

Sur la machine virtuelle du serveur d’applications, le répertoire sapmnt doit être partagé par l’intermédiaire de NFS à l’aide des options « rw » et « no\_root\_squash ». Les options par défaut « ro » et « root\_squash » peuvent entraîner des problèmes lors de l’installation de l’instance de base de données.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

Sur la machine virtuelle du serveur de base de données SAP HANA, le partage sapmnt à partir de la machine virtuelle du serveur d’applications doit être configuré par le biais du « client NFS » (par exemple, à l’aide de Yast).


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

Vous devez ensuite vous connecter à la machine virtuelle du serveur de base de données SAP HANA et démarrer SWPM pour accomplir l’étape suivante d’une installation distribuée de NW 7.5 « Instance de base de données ».


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

L’installation de SAP HANA implique peu de saisie d’informations une fois l’installation « typical » (par défaut) sélectionnée. Outre le chemin d’accès au support d’installation, vous devez entrer un SID de base de données, le nom d’hôte, le numéro d’instance et le mot de passe d’administrateur système de base de données.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

L’étape suivante consiste à entrer le mot de passe pour le schéma DBACOCKPIT.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Puis, le mot de passe du schéma SAPABAP1 est demandé.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

À la fin de la procédure, seules des coches vertes doivent figurer en regard de chaque phase du processus d’installation de base de données, et la petite boîte de dialogue qui apparaît doit indiquer que l’instance de base de données a été exécutée.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

Après l’installation, la console de gestion SAP doit également indiquer l’instance de base de données en vert, ainsi que la liste complète des processus SAP HANA (par exemple, hdbindexserver et hdbcompileserver).


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Cette capture d’écran montre des parties de la structure de fichiers sous /hana/shared, créée par SWPM lors de l’installation de SAP HANA. Il n’y avait aucun moyen de spécifier un autre chemin d’accès. Par conséquent, il est important de monter un espace disque supplémentaire sous /hana avant l’installation de SAP HANA avec SWPM pour éviter que le système de fichiers racine manque d’espace libre.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

Vous pouvez voir ici la même chose que ce que nous avons décrit précédemment pour le répertoire /usr/sap.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

La dernière étape de l’installation distribuée ABAP concerne l’instance de serveur d’applications principal.


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Une fois le PAS et l’interface utilisateur graphique SAP installés, vous pouvez vérifier si l’installation de SAP HANA semble correctement effectuée à l’aide de la transaction « dbacockpit ».

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Pour terminer, vous pouvez installer SAP HANA Studio sur la machine virtuelle du serveur d’applications SAP et vous connecter à une instance SAP HANA en cours d’exécution sur la machine virtuelle du serveur de base de données.




## Installation manuelle de SAP HANA avec l’outil HANA Life Cycle Manager (hdblcm)


Outre l’installation de SAP HANA dans le cadre d’une installation distribuée avec SWPM, il est également possible de commencer par installer HANA de façon autonome à l’aide de l’outil hdblcm, puis, par exemple, d’installer SAP NetWeaver 7.5. La série de captures d’écran ci-dessous illustre cette procédure.

Voici trois sources d’informations sur l’outil HANA hdblcm :

[Choosing the Correct SAP HANA HDBLCM for Your Task (Choisir l’outil SAP HANA HDBLCM adapté à votre tâche)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[SAP HANA Lifecycle Management Tools (Outils de gestion du cycle de vie SAP HANA)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA Server Installation and Update Guide (Guide d’installation et de mise à jour du serveur SAP HANA)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

Pour éviter de rencontrer des problèmes plus tard avec un paramètre d’ID de groupe par défaut pour <SID HANA>adm user (créé par l’outil hdblcm), vous devez définir un nouveau groupe appelé « sapsys » avec l’ID de groupe 1001 avant d’installer SAP HANA à l’aide de l’outil hdblcm.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

Lorsque vous démarrez l’outil hdblcm pour la première fois, vous accédez à un menu de démarrage simple dans lequel vous devez sélectionner l’élément 1 « Install new System » (Installer un nouveau système).



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

Sur cette capture d’écran, vous pouvez voir toutes les options clés entrées précédemment. Important : les répertoires qui ont été nommés pour les volumes de données et de journaux HANA, ainsi que le chemin d’installation (/hana/shared dans cet exemple) et /usr/sap ne doivent pas faire partie du système de fichiers racine. Ils doivent appartenir aux disques de données Azure qui ont été attachés à la machine virtuelle, comme décrit dans la section sur la configuration des machines virtuelles Azure. Ceci permettra d’éviter que le système de fichiers racine manque d’espace. Vous pouvez également voir que l’ID de l’utilisateur administrateur HANA est 1005 et que cet utilisateur fait partie du groupe sapsys (ID 1001) qui a été défini avant l’installation.



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

Vous pouvez consulter les informations d’utilisateur HANA <SID HANA>adm (azdadm dans cet exemple) dans /etc/passwd.



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Après avoir installé SAP HANA à l’aide de l’outil hdblcm, vous pouvez le voir dans SAP HANA Studio. Le schéma SAPABAP1, qui peut inclure, par exemple, toutes les tables SAP NetWeaver n’est pas encore disponible.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Après avoir installé SAP HANA, vous pouvez installer SAP NetWeaver par-dessus. Dans cet exemple, cette tâche a été effectuée à l’aide d’une « installation distribuée » avec SWPM, comme décrit dans la section correspondante plus haut. Lors de l’installation de l’instance de base de données avec SWPM, vous pouvez simplement entrer les mêmes données que celles entrées précédemment avec l’outil hdblcm (par exemple, nom d’hôte, SID HANA et numéro d’instance). SWPM utilisera alors l’installation HANA existante et ajoutera d’autres schémas.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Cette image illustre l’étape d’installation avec SWPM, au cours de laquelle vous devez entrer des données relatives au schéma DBACOCKPIT.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Ensuite, SWPM attend une saisie de données sur le schéma SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Une fois terminée l’installation de l’instance de base de données avec SWPM, vous pouvez voir le schéma SAPABAP1 dans HANA Studio.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

Enfin, après l’installation du serveur d’applications SAP et de l’interface utilisateur graphique SAP, vous devriez être en mesure de vérifier l’instance de base de données HANA avec la transaction « dbacockpit ».




## Informations générales relatives aux certifications SAP Azure, à l’exécution de SAP HANA sur Azure et au téléchargement de logiciels SAP

* Documentation SAP Azure générale sur l’exécution de SAP sur Azure avec le système d’exploitation Windows en mode classique : [Utilisation de SAP sur des machines virtuelles Windows dans Azure](virtual-machines-windows-classic-sap-get-started.md)

* Informations sur les modèles SAP existants pour une utilisation par les clients : [Azure Quickstart Templates for SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/) (Modèles de démarrage rapide Azure pour SAP)

* Documentation SAP Azure générale sur l’exécution de SAP sur Azure avec le système d’exploitation Linux dans le modèle Azure Resource Manager : [Utilisation de SAP sur des machines virtuelles Linux](virtual-machines-linux-sap-get-started.md)

* Répertoire de matériel SAP HANA certifié indiquant les types de machines virtuelles Azure prises en charge pour la production : [Certified SAP HANA® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) (Répertoire de matériel SAP HANA® certifié)

* Informations sur les tailles des machines virtuelles, en particulier pour les charges de travail Linux : [Tailles des machines virtuelles dans Azure](virtual-machines-linux-sizes.md)

* Note SAP répertoriant tous les produits SAP pris en charge sur Azure et types de machines virtuelles Azure pris en charge pour SAP : [Note SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)

* Note SAP sur la « surveillance améliorée » avec des machines virtuelles Linux sur Azure : [Note SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)

* Offre SAP HANA sur les « grandes instances » Azure. Il est important de comprendre qu’il ne s’agit pas ici d’exécuter SAP HANA sur des machines virtuelles Azure, mais dans un environnement hybride où les serveurs d’applications SAP s’exécutent dans des machines virtuelles Azure et où SAP HANA s’exécute sur des serveurs nus : [Note SAP 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)

* Note SAP contenant des informations relatives à SAPOSCOL sur Linux : [Note SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)

* Métriques de surveillance clés pour SAP sur Microsoft Azure : [Note SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Informations sur Azure Resource Manager : [Présentation d’Azure Resource Manager](../resource-group-overview.md)

* Informations sur le déploiement de machines virtuelles Linux via des modèles : [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md)

* Comparaison de modèles de déploiement entre Azure Resource Manager et un déploiement classique : [Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources](../resource-manager-deployment-model.md)

* Téléchargement de NetWeaver 7.5 pour Linux/HANA sur le SAP Service Marketplace : ![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* Téléchargement de la plateforme HANA SP12 sur le SAP Service Marketplace : ![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

<!---HONumber=AcomDC_0921_2016-->