<properties
	pageTitle="Préparer un disque dur virtuel Windows à charger sur Azure | Microsoft Azure"
	description="Pratiques recommandées pour préparer un disque dur virtuel Windows avant de le charger sur Azure"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="genlin"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/18/2016"
	ms.author="glimoli;genli"/>

# Préparer un disque dur virtuel Windows à charger sur Azure
Pour charger une machine virtuelle Windows locale sur Azure, vous devez préparer correctement le disque dur virtuel (VHD). Avant de charger un disque dur virtuel sur Azure, vous devez suivre une série d’étapes recommandées. Cet article vous indique comment préparer un disque dur virtuel Windows à charger sur Microsoft Azure, ainsi que [quand et comment utiliser Sysprep](#step23).

## Préparer le disque virtuel

>[AZURE.NOTE] Azure ne prend pas en charge le nouveau format VHDX. Le disque dur virtuel doit être de taille fixe, pas dynamique. Si nécessaire, les instructions ci-dessous décrivent la conversion de disques VHDX ou dynamiques. La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go.

Vérifiez que le disque dur virtuel Windows fonctionne correctement sur le serveur local. Résolvez toute erreur dans la machine virtuelle elle-même avant d’effectuer une conversion ou un chargement sur Azure.

Pour convertir votre disque virtuel au format exigé par Azure, utilisez une des méthodes décrites dans les sections suivantes. Sauvegardez la machine virtuelle avant d’exécuter tout processus de conversion de disque virtuel ou Sysprep.

### Convertir à l’aide du Gestionnaire Hyper-V
- Ouvrez le Gestionnaire Hyper-V et sélectionnez votre ordinateur local sur la gauche. Dans le menu du haut, cliquez sur **Action** > **Modifier le disque**.
	- Dans l’écran **Rechercher un disque dur virtuel**, recherchez et sélectionnez le disque virtuel.
	- Sélectionnez **Convertir** dans l’écran suivant.
		- Pour convertir un disque VHDX, sélectionnez **VHD**, puis cliquez sur **Suivant**.
		- Pour convertir un disque dynamique, sélectionnez **Taille fixe**, puis cliquez sur **Suivant**.

	- Recherchez et sélectionnez le **chemin du nouveau fichier de disque dur virtuel**.
	- Cliquez sur **Terminer** pour fermer la fenêtre.

### Convertir à l’aide de PowerShell
Vous pouvez convertir un disque virtuel à l’aide de [l’applet de commande PowerShell Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Dans l’exemple suivant, nous convertissons un disque VHDX au format VHD et un disque dynamique en disque de taille fixe :

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### Convertir à partir du format de disque VMDK VMware
Si vous disposez d’une image de machine virtuelle Windows au [format de fichier VMDK](https://en.wikipedia.org/wiki/VMDK), convertissez-la au format VHD à l’aide de [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Consultez le blog [How to Convert a VMWare VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (en anglais) pour plus d’informations.

## Préparer la configuration de Windows pour le chargement

> [AZURE.NOTE] Exécutez toutes les commandes suivantes avec des [privilèges d’administrateur](https://technet.microsoft.com/library/cc947813.aspx).

1. Supprimez tout itinéraire statique persistant de la table de routage :

	- Pour afficher la table de routage, exécutez `route print`.
	- Vérifiez les sections **Persistence Routes**. S’il existe un itinéraire persistant, utilisez [route delete](https://technet.microsoft.com/library/cc739598.apx) pour le supprimer.

2. Supprimez le proxy WinHTTP :

	```
	netsh winhttp reset proxy
	```

3. Définissez la stratégie SAN pour les disques sur [onlineall](https://technet.microsoft.com/library/gg252636.aspx) :

	```
	diskpart san policy=onlineall
	```

4. Utilisez l’heure UTC (Temps universel coordonné) pour Windows et définissez le type de démarrage du service de Temps Windows (w32time) sur **automatique** :

	```
	REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
	sc config w32time start= auto
	```


## Configurer les services Windows
5. Vérifiez que chacun des services Windows suivants est défini sur les **valeurs par défaut Windows**. Ils sont configurés avec les paramètres de démarrage répertoriés dans la liste suivante. Vous pouvez exécuter ces commandes pour réinitialiser les paramètres de démarrage :

	```
	sc config bfe start= auto

	sc config dcomlaunch start= auto

	sc config dhcp start= auto

	sc config dnscache start= auto

	sc config IKEEXT start= auto

	sc config iphlpsvc start= auto

	sc config PolicyAgent start= demand

	sc config LSM start= auto

	sc config netlogon start= demand

	sc config netman start= demand

	sc config NcaSvc start= demand

	sc config netprofm start= demand

	sc config NlaSvc start= auto

	sc config nsi start= auto

	sc config RpcSs start= auto

	sc config RpcEptMapper start= auto

	sc config termService start= demand

	sc config MpsSvc start= auto

	sc config WinHttpAutoProxySvc start= demand

	sc config LanmanWorkstation start= auto

	sc config RemoteRegistry start= auto
	```


## Paramétrer la configuration de Bureau à distance
6. Si des certificats auto-signés sont liés à l’écouteur RDP (Remote Desktop Protocol), supprimez-les :

	```
	REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
	```

	Pour plus d’informations sur la configuration des certificats pour l’écouteur RDP, consultez [Listener Certificate Configurations in Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/) (Configurations de certificats d’écouteur dans Windows Server).

7. Configurez les valeurs [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) pour le service RDP :

	```
	REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
	```

8. Configurez le mode d’authentification pour le service RDP :

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
	```

9. Activez le service RDP en ajoutant les sous-clés suivantes au Registre :

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
	```


## Configurer les règles du Pare-feu Windows
10. Autorisez WinRM sur les trois profils de pare-feu (domaine, privé et public) et activez le service à distance PowerShell :

	```
	Enable-PSRemoting -force
	```

11. Vérifiez que les règles de pare-feu du système d’exploitation invité suivantes sont en place :

	- Trafic entrant

	```
	netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
	```

	- Trafic entrant et sortant

	```
	netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

	netsh advfirewall firewall set rule group="Core Networking" new enable=yes
	```

	- Trafic sortant

	```
	netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
	```


## Étapes de configuration Windows supplémentaires
12. Exécutez `winmgmt /verifyrepository` pour confirmer que le référentiel Windows Management Instrumentation (WMI) est cohérent. Si le référentiel est endommagé, consultez [ce billet de blog](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Vérifiez que les paramètres de données de configuration de démarrage (BCD) correspondent à ce qui suit :

	```
	bcdedit /set {bootmgr} integrityservices enable

	bcdedit /set {default} device partition=C:

	bcdedit /set {default} integrityservices enable

	bcdedit /set {default} recoveryenabled Off

	bcdedit /set {default} osdevice partition=C:

	bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
	```

14. Supprimez tous les filtres de TDI (Transport Driver Interface) supplémentaires, tels que les logiciels qui analysent les paquets TCP.
15. Pour vérifier que le disque est sain et cohérent, exécutez la commande `CHKDSK /f`.
16.	Désinstallez tous les autres logiciels et pilotes tiers liés aux composants physiques ou toute autre technologie de virtualisation.
17. Vérifiez qu’aucune application tierce n’utilise le port 3389. Ce port est utilisé pour le service RDP dans Azure. Vous pouvez utiliser la commande `netstat -anob` pour vérifier les ports utilisés par les applications.
18.	Si le disque dur virtuel Windows à charger est un contrôleur de domaine, suivez [ces étapes supplémentaires](https://support.microsoft.com/kb/2904015) pour le préparer.
19.	Redémarrez la machine virtuelle pour vous assurer que Windows est toujours sain et qu’il est accessible par le biais de la connexion RDP.
20.	Réinitialisez le mot de passe administrateur local actuel et vérifiez que vous pouvez utiliser ce compte pour vous connecter à Windows par le biais de la connexion RDP. Cette autorisation d’accès est contrôlée par l’objet de stratégie « Autoriser l’ouverture de session par les services Bureau à distance ». Cet objet se trouve à l’emplacement « Computer Configuration\\Windows Settings\\Security Settings\\Local Policies\\User Rights Assignment ».


## Installer les mises à jour Windows
22. Installez les dernières mises à jour pour Windows. Si ce n’est pas possible, vérifiez que les mises à jour suivantes sont installées :

	- [KB3137061](https://support.microsoft.com/kb/3137061) Les machines virtuelles Microsoft Azure ne récupèrent pas d’une panne de réseau et des problèmes de corruption de données se produisent

	- [KB3115224](https://support.microsoft.com/kb/3115224) Améliorations de la fiabilité des machines virtuelles qui s’exécutent sur un hôte Windows Server 2012 R2 ou Windows Server 2012

	- [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031 : Mise à jour de sécurité pour Microsoft Windows afin de résoudre un problème d’élévation de privilèges : 8 mars 2016

	- [KB3063075](https://support.microsoft.com/kb/3063075) De nombreux événements ID 129 sont enregistrés quand vous exécutez une machine virtuelle Windows Server 2012 R2 dans Microsoft Azure

	- [KB3137061](https://support.microsoft.com/kb/3137061) Les machines virtuelles Microsoft Azure ne récupèrent pas d’une panne de réseau et des problèmes de corruption de données se produisent

	- [KB3114025](https://support.microsoft.com/kb/3114025) Ralentissement des performances quand vous accédez au stockage de fichiers Azure depuis Windows 8.1 ou Server 2012 R2

	- [KB3033930](https://support.microsoft.com/kb/3033930) Le correctif augmente la limite de 64 Ko dans les mémoires tampons RIO par processus pour le service Microsoft Azure

	- [KB3004545](https://support.microsoft.com/kb/3004545) Impossible d’accéder aux machines virtuelles hébergées sur des services d’hébergement Azure par le biais d’une connexion VPN dans Windows

	- [KB3082343](https://support.microsoft.com/kb/3082343) La connectivité VPN intersite est perdue quand les tunnels VPN site à site Azure utilisent Windows Server 2012 R2 RRAS

	- [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031 : Mise à jour de sécurité pour Microsoft Windows afin de résoudre un problème d’élévation de privilèges : 8 mars 2016

	- [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048 : Description de la mise à jour de sécurité pour CSRSS : 12 avril 2016
	- [KB2904100](https://support.microsoft.com/kb/2904100) Le système se fige pendant des opérations d’E/S sur disque dans Windows <a id="step23"></a>
23. Pour créer une image permettant de déployer plusieurs ordinateurs, vous devez généraliser l’image en exécutant `sysprep` avant de charger le disque dur virtuel sur Azure. Vous n’avez pas besoin exécuter `sysprep` pour utiliser un disque dur virtuel spécialisé. Pour plus d’informations sur la création d’une image généralisée, consultez les articles suivants :

	- [Create a VM image from an existing Azure VM using the Resource Manager deployment model (Créer une image de machine virtuelle à partir d’une machine virtuelle Azure existante en utilisant le modèle de déploiement Resource Manager)](virtual-machines-windows-capture-image.md)
	- [Create a VM image from an existing Azure VM using the Classic deployment model (Créer une image de machine virtuelle à partir d’une machine virtuelle Azure existante en utilisant le modèle de déploiement Classic)](virtual-machines-windows-classic-capture-image.md)
	- [Prise en charge de Sysprep pour les rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## Configurations supplémentaires suggérées

Les paramètres suivants n’affectent pas le chargement du disque dur virtuel. Toutefois, nous vous recommandons vivement de les configurer.

- Installez [l’agent Machines virtuelles Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Après avoir installé l’agent, vous pouvez activer des extensions de machine virtuelle. Les extensions de machine virtuelle mettent en œuvre la plupart des fonctionnalités stratégiques à utiliser avec vos machines virtuelles, telles que la réinitialisation des mots de passe, la configuration de RDP, et bien d’autres encore.

- Le journal de vidage peut être utile pour résoudre les problèmes de blocage de Windows. Activez la collecte des journaux de vidage :

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

	REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

	REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

	REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

	sc config wer start= auto
	```

- Une fois la machine virtuelle créée dans Azure, configurez le fichier d’échange de taille définie par le système sur le lecteur D :

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
	```


## Étapes suivantes

- [Télécharger une image de machine virtuelle Windows dans Azure pour des déploiements Resource Manager](virtual-machines-windows-upload-image.md)

<!---HONumber=AcomDC_0921_2016-->