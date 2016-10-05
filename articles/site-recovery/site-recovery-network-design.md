<properties
	pageTitle="Conception de votre infrastructure réseau pour la récupération d’urgence | Microsoft Azure"
	description="Cet article aborde les considérations de conception de réseau pour Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="prateek9us"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/19/2016"
	ms.author="pratshar"/>

#  Conception de votre infrastructure réseau pour la récupération d’urgence

Cet article s’adresse aux professionnels de l’informatique, chargés de l’architecture, de l’implémentation et de la prise en charge de l’infrastructure BCDR (continuité des activités et récupération d’urgence), et qui souhaitent tirer parti de Microsoft Azure Site Recovery (ASR) pour prendre en charge et améliorer leurs services BCDR. Ce document traite de considérations pratiques pour le déploiement de serveur System Center Virtual Machine Manager, et des avantages et inconvénients des sous-réseaux étirés et du basculement de sous-réseau. Il indique également comment structurer la récupération d’urgence sur les sites virtuels dans Microsoft Azure.

## Vue d'ensemble

[Azure Site Recovery (ASR)](https://azure.microsoft.com/services/site-recovery/) est un service Microsoft Azure qui organise la protection et la récupération de vos applications virtualisées pour les besoins de scénarios BCDR. Ce document est conçu pour guider le lecteur dans le processus de conception des réseaux, en mettant l’accent sur l’architecture des plages d’adresses IP et des sous-réseaux sur le site de récupération d’urgence, lors de la réplication des machines virtuelles à l’aide de Site Recovery.

En outre, cet article montre comment Site Recovery permet de créer et d’implémenter un centre de données virtuel multisite pour prendre en charge les services BCDR lors d’un test ou d’une urgence.

Dans un monde où chacun compte sur une connectivité 24 heures sur 24, 7 jours sur 7, il est plus important que jamais de maintenir votre infrastructure et vos applications opérationnelles. L’objectif de BCDR est de restaurer les composants en échec afin que l’organisation puisse reprendre rapidement son fonctionnement normal. Il est très difficile de développer des stratégies de récupération d’urgence pour gérer les problèmes et les événements dévastateurs. Cela est dû à la difficulté inhérente à la prédiction, en particulier en matière d’événements improbables, et aux coûts élevés des mesures de protection appropriées contre les catastrophes importantes.

Essentiels pour la planification de BCDR, un objectif de délai de récupération (RTO) et un objectif de point de récupération (RPO) doivent être définis dans le cadre d’un plan de récupération d’urgence. Si un sinistre frappe le centre de données d’un client, en utilisant Azure Site Recovery, il peut rapidement (RTO faible) mettre en ligne ses machines virtuelles répliquées situées dans le centre de données secondaire ou dans Microsoft Azure avec un minimum de perte de données (RPO faible).

Le basculement est rendu possible par ASR, qui copie initialement les machines virtuelles désignées du centre de données principal vers le centre de données secondaire ou vers Azure (en fonction du scénario), et qui actualise ensuite régulièrement les réplicas. Lors de la planification de l’infrastructure, la conception réseau doit être considérée comme un goulot d’étranglement potentiel, qui peut vous empêcher de remplir les objectifs RTO et RPO de la société.

Si un administrateur envisage de déployer une solution de récupération d’urgence, l’une des questions clés qu’il doit se poser concerne l’accessibilité de la machine virtuelle à l’issue du basculement. La récupération automatique du système (ASR) permet à l’administrateur de choisir le réseau auquel une machine virtuelle doit être connectée après basculement. Si le site principal est géré par un serveur VMM, cela est effectué à l’aide d’un mappage réseau. Pour plus de détails, voir [Préparer le mappage réseau](site-recovery-network-mapping.md).

Lors de la conception du réseau pour le site de récupération, l’administrateur a deux possibilités :

- Utiliser une autre plage d’adresses IP pour le réseau sur le site de récupération. Dans ce scénario, à l’issue du basculement, la machine virtuelle obtient une nouvelle adresse IP, et l’administrateur doit effectuer une mise à jour DNS. Pour en savoir plus sur la procédure de mise à jour de DNS, cliquez [ici](site-recovery-vmm-to-vmm.md#test-your-deployment).
- Utiliser la même plage d’adresses IP pour le réseau sur le site de récupération. Dans certains scénarios, les administrateurs préféreront conserver les adresses IP dont ils disposent sur le site principal, même après le basculement. Dans un scénario normal, un administrateur devrait mettre à jour les itinéraires pour indiquer le nouvel emplacement des adresses IP, mais dans le scénario où un réseau local virtuel étiré est déployé entre le site principal et les sites de récupération, conserver les adresses IP des machines virtuelles est alors une option intéressante. Conserver les mêmes adresses IP permet de simplifier le processus de récupération en supprimant les étapes post-basculement associées au réseau.


Si un administrateur envisage de déployer une solution de récupération d’urgence, l’une des questions clés qu’il doit se poser concerne l’accessibilité des applications à l’issue du basculement. Dans une certaine mesure, les applications modernes sont presque toujours dépendantes de la mise en réseau. Par conséquent, le déplacement physique d’un service d’un site à un autre représente un défi pour la mise en réseau. Deux méthodes principales permettent de traiter ce problème dans les solutions de récupération d’urgence. La première approche consiste à conserver des adresses IP fixes. Malgré les déplacements de services et les serveurs d'hébergement qui se trouvent dans différents emplacements physiques, les applications prennent avec elles la configuration de l'adresse IP dans le nouvel emplacement. La seconde approche implique de changer complètement l’adresse IP lors de la transition vers le site récupéré. Chaque approche présente plusieurs variantes d’implémentation qui sont résumées ci-dessous.

Lors de la conception du réseau pour le site de récupération, l’administrateur a deux possibilités :

## Option 1 : Conserver les adresses IP 

Si l’on considère le processus de récupération d’urgence, l’utilisation d’adresses IP fixes semble être la méthode la plus simple à implémenter, mais elle présente plusieurs difficultés potentielles qui la rendent moins populaire dans la pratique. Azure Site Recovery offre la possibilité de conserver les adresses IP dans tous les scénarios. Avant de décider de conserver les adresses IP, il convient de réfléchir aux contraintes que cette méthode impose sur les fonctionnalités de basculement. Examinons les facteurs qui peuvent vous aider à prendre une décision pour conserver ou non les adresses IP. Cela est possible de deux manières, en utilisant un sous-réseau étiré ou en procédant à un basculement de sous-réseau complet.

### Sous-réseau étiré

Ici, le sous-réseau est disponible simultanément sur le site principal et sur le site de récupération d’urgence. En termes simples, cela signifie que vous pouvez déplacer un serveur et sa configuration IP (couche 3) sur le site secondaire, et que le réseau acheminera automatiquement le trafic vers ce nouveau site. Cela est très facile à gérer du point de vue du serveur mais présente un certain nombre de défis :

- Dans la perspective de la couche 2 (couche de liaison de données), un équipement de mise en réseau capable de gérer un VLAN étiré est nécessaire, mais cela n’est plus un problème, car ce type d’équipement est désormais largement disponible. Le deuxième problème, plus difficile, est qu’en étirant le VLAN, le domaine d’erreur potentielle est étendu aux deux sites, et devient essentiellement un point de défaillance unique. Bien que cela soit peu probable, il est arrivé qu’une tempête de diffusion commence, sans pouvoir être isolée. Sur ce dernier problème, les avis sont partagés. Nombre d’implémentations ont été réussies, tandis que des organisations ne souhaitent pas du tout entendre parler de cette technologie.
- La méthode utilisant un sous-réseau étiré n’est pas possible si vous utilisez Microsoft Azure comme site de récupération d’urgence.


### Basculement de sous-réseau

Il est possible d’implémenter le basculement de sous-réseau pour bénéficier des avantages de la solution de sous-réseau étiré, décrite ci-dessus sans étirer le sous-réseau sur plusieurs sites. Dans ce cas, n’importe quel sous-réseau donné serait présent sur le site 1 ou le site 2, mais jamais simultanément sur les deux sites. Pour conserver l’espace d’adressage IP en cas de basculement, il est possible de mettre en place un programme pour que l’infrastructure du routeur déplace les sous-réseaux d’un site à un autre. Dans un scénario de basculement, les sous-réseaux se déplacent avec les machines virtuelles protégées associées. Le principal inconvénient de cette approche est que vous devez déplacer l’ensemble du sous-réseau en cas de défaillance, ce qui peut ne pas poser de problèmes, mais peut affecter les considérations relatives à la granularité du basculement.

Examinons comment l’entreprise fictive nommée Contoso est en mesure de répliquer ses machines virtuelles vers un emplacement de récupération, tout en basculant l’ensemble du sous-réseau. Nous allons commencer par examiner comment Contoso peut gérer ses sous-réseaux tout en répliquant des machines virtuelles entre deux emplacements locaux, puis nous allons voir le fonctionnement du basculement de sous-réseau quand [Azure est utilisé en tant que site de récupération d’urgence](#failover-to-azure).

#### Basculement vers un site secondaire local

Examinons un scénario dans lequel nous souhaitons conserver l’adresse IP de chacune des machines virtuelles, et basculer simultanément tout le sous-réseau. Le site principal possède des applications qui s'exécutent dans le sous-réseau 192.168.1.0/24. Lorsque le basculement se produit, toutes les machines virtuelles qui font partie de ce sous-réseau basculent vers le site de récupération et conservent leur adresse IP. Les itinéraires doivent être modifiés en conséquence afin de refléter le fait que toutes les machines virtuelles appartenant au sous-réseau 192.168.1.0/24 ont été déplacées vers le site de récupération.

Dans l’illustration suivante, les itinéraires existant entre le site principal et le site de récupération, le troisième site et le site principal, et entre le troisième site et le site de récupération doivent être modifiés comme il convient.

Les images ci-dessous montrent les sous-réseaux avant le basculement. Le sous-réseau 192.168.0.1/24 est actif sur le site principal avant le basculement, puis devient actif sur le site de récupération après le basculement

![Avant le basculement](./media/site-recovery-network-design/network-design2.png)

Avant le basculement


L’illustration ci-dessous montre les réseaux et sous-réseaux après le basculement.
	
![Après le basculement](./media/site-recovery-network-design/network-design3.png)

Après le basculement

Si votre site secondaire est local et si vous utilisez un serveur VMM pour le gérer, lorsque vous activez la protection d’une machine virtuelle spécifique, la récupération automatique du système alloue des ressources réseau en suivant le flux de travail suivant :

- ASR alloue une adresse IP pour chaque interface réseau sur la machine virtuelle à partir du pool d’adresses IP statiques défini sur le réseau approprié pour chaque instance SCVMM.
- Si l’administrateur définit le même pool d’adresses IP pour le réseau du site de récupération que celui utilisé pour le réseau du site principal tout en allouant l’adresse IP à la machine virtuelle de réplication, ASR alloue la même adresse IP que celle de la machine virtuelle principale. L’adresse IP est réservée dans VMM, mais elle n’est pas définie comme adresse IP de basculement. L’adresse IP de basculement est définie juste avant le basculement.

![Conserver l’adresse IP](./media/site-recovery-network-design/network-design4.png)
	
Figure 5

La figure 5 montre les paramètres TCP/IP de basculement de la machine virtuelle de réplication (sur la console Hyper-V). Ces paramètres sont renseignés juste avant le démarrage de la machine virtuelle après un basculement.

Si cette même adresse IP n’est pas disponible, ASR alloue une autre adresse IP disponible à partir du pool d’adresses IP défini.

Une fois la machine virtuelle activée pour la protection, vous pouvez utiliser l’exemple de script suivant pour vérifier l’adresse IP qui lui a été allouée. La même adresse IP est définie comme adresse IP de basculement et affectée à la machine virtuelle au moment du basculement :

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

>[AZURE.NOTE] Dans le cas où les machines virtuelles utilisent DHCP, la gestion des adresses IP se trouve complètement hors du contrôle d’ASR. Un administrateur doit s’assurer que le serveur DHCP qui fournit les adresses IP sur le site de récupération peut fournir des adresses provenant de la même plage que celle du site principal.

#### Basculement vers Azure

ASR permet d’utiliser Microsoft Azure comme site de récupération d’urgence pour vos machines virtuelles. Dans ce cas, vous devez gérer une contrainte supplémentaire.

Examinons un scénario dans lequel la société fictive Woodgrove Bank dispose d’une infrastructure locale, qui héberge ses applications métier, et d’Azure qui héberge ses applications mobiles. La connectivité entre les machines virtuelles de la Woodgrove Bank dans Azure et les serveurs locaux est assurée par un réseau privé virtuel de site à site. Ce réseau privé virtuel de site à site permet au réseau virtuel dans Azure de la Woodgrove Bank d’être considéré comme une extension du réseau local de la Woodgrove Bank. Cette communication est permise par le réseau privé virtuel de site à site entre le périmètre de la Woodgrove Bank et le réseau virtuel Azure. À présent, la Woodgrove Bank souhaite utiliser ASR pour répliquer ses charges de travail s’exécutant dans son centre de données vers Azure. Cette option répond aux besoins de la Woodgrove Bank, qui veut une option de récupération d’urgence économique et qui souhaite pouvoir stocker des données dans les environnements de cloud public. La Woodgrove Bank doit gérer des applications et des configurations qui dépendent des adresses IP codées en dur. La société demande donc à conserver les adresses IP de ses applications après le basculement vers Azure.

La Woodgrove Bank décide d’affecter des adresses IP de la plage 172.16.1.0/24 à 172.16.2.0/24 à ses ressources exécutées dans Azure.

Pour que la Woodgrove Bank puisse répliquer ses machines virtuelles vers Azure tout en conservant les adresses IP, un réseau virtuel Azure doit être créé. Ce doit être une extension du réseau local pour que les applications puissent basculer du site local vers Azure en toute transparence. Azure vous permet d’ajouter une connectivité de type réseau privé virtuel de site à site et de point à site aux réseaux virtuels créés dans Azure. Lors de la configuration de votre connexion de site à site, un réseau Azure vous permet d’acheminer le trafic vers l’emplacement local (qu’Azure appelle « réseau local ») uniquement si la plage d’adresses IP est différente de la plage d’adresses IP locale, car Azure ne prend pas en charge les sous-réseaux étirés. Cela signifie que si vous avez un sous-réseau local 192.168.1.0/24, vous ne pouvez pas ajouter un réseau local 192.168.1.0/24 dans le réseau Azure. Ceci est dû au fait que Azure ne sait pas qu'aucune machine virtuelle n’est active dans le sous-réseau et que le sous-réseau est créé uniquement à des fins de récupération d'urgence. Pour pouvoir acheminer correctement le trafic réseau à partir d’un réseau Azure, les sous-réseaux du réseau et le réseau local ne doivent pas entrer en conflit.

![Avant le basculement de sous-réseau](./media/site-recovery-network-design/network-design7.png)

Avant le basculement

Pour aider la Woodgrove Bank à satisfaire à ses exigences professionnelles, nous devons implémenter les flux de travail suivants :

- Créez un autre réseau, que nous allons appeler réseau de récupération, où les machines virtuelles ayant basculé seraient créées.
- Pour vous assurer que l’adresse IP d’une machine virtuelle est conservée après un basculement, accédez à l’onglet Configurer, dans les propriétés de la machine virtuelle, spécifiez la même adresse IP que celle utilisée par la machine virtuelle en local, puis cliquez sur Enregistrer. Au basculement de la machine virtuelle, Azure Site Recovery affecte l’adresse IP fournie à la machine virtuelle.

![Propriétés du réseau](./media/site-recovery-network-design/network-design8.png)

Une fois le basculement déclenché et les machines virtuelles créées dans le réseau de récupération avec l’adresse IP de votre choix, la connectivité avec ce réseau peut être établie à l’aide d’une [connexion de réseau virtuel à réseau virtuel](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Si nécessaire, cette action peut faire l’objet d’un script. Comme expliqué dans la section précédente sur le basculement de sous-réseau, même en cas de basculement vers Azure, les itinéraires doivent être modifiés comme il convient pour montrer que l’adresse IP 192.168.1.0/24 a été déplacée vers Azure.

![Après le basculement de sous-réseau](./media/site-recovery-network-design/network-design9.png)

Après le basculement

Si vous n’avez pas de réseau Azure comme illustré dans l’image ci-dessus. Vous pouvez créer une connexion vpn de site à site entre votre Site principal et votre Réseau de récupération après le basculement.


## Option 2 : modification des adresses IP

Nous avons constaté que cette approche semble être la plus répandue. Cette approche consiste à changer l’adresse IP de toutes les machines virtuelles impliquées dans le basculement. L’inconvénient de cette approche est qu’elle implique que le réseau entrant « sache » que l’application qui était à l’adresse IPx est désormais à l’adresse IPy. Même si les adresses IPx et IPy sont des noms logiques, les entrées DNS doivent généralement être modifiées ou vidées dans le réseau, et les entrées mises en cache dans les tables de réseaux doivent être mises à jour ou vidées. Il est donc possible qu’un temps d’arrêt ait lieu en fonction de la configuration de l’infrastructure DNS. Ces problèmes peuvent être atténués en utilisant des valeurs TTL faibles dans le cas d’applications intranet, et en utilisant [Azure Traffic Manager avec ASR](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) pour les applications basées sur Internet.

### Modification des adresses IP - Illustration

Examinons le scénario dans lequel vous prévoyez d’utiliser différentes adresses IP sur le site principal et le site de récupération. Dans l’exemple suivant, nous disposons également d’un troisième site à partir duquel les applications hébergées sur le site principal ou le site de récupération sont accessibles.

![Autre adresse IP - avant le basculement](./media/site-recovery-network-design/network-design10.png)

Figure 11

Figure 11, certaines applications sont hébergées dans le sous-réseau 192.168.1.0/24 sur le site principal, et elles sont configurées pour arriver sur le site de récupération dans le sous-réseau 172.16.1.0/24 après un basculement. Les itinéraires du réseau/des connexions VPN ont été correctement configurés pour que les trois sites puissent accéder l’un à l'autre.
 
Comme illustré figure 12, après leur basculement, les applications sont restaurées dans le sous-réseau de récupération. Dans ce cas, nous ne sommes pas contraints de basculer tout le sous-réseau en même temps. Aucune modification n’est nécessaire pour reconfigurer les itinéraires du réseau ou du réseau privé virtuel. Un basculement et certaines mises à jour DNS garantissent le maintien de l’accessibilité des applications. Si le système DNS est configuré pour autoriser des mises à jour dynamiques, les machines virtuelles peuvent alors s’inscrire à l’aide de la nouvelle adresse IP une fois qu’elles démarrent après un basculement.

![Autre adresse IP - après le basculement](./media/site-recovery-network-design/network-design11.png)

Figure 12

Après le basculement, il est possible que la machine virtuelle de réplication possède une adresse IP différente de celle de la machine virtuelle principale. Les machines virtuelles mettront à jour le serveur DNS qu'elles utilisent après leur démarrage. Les entrées DNS doivent généralement être modifiées ou supprimées du réseau, et es entrées mises en cache dans les tables de réseau doivent être mises à jour ou supprimées. Il n’est donc pas rare d’avoir des temps d'arrêt pendant que ces modifications ont lieu. Ce problème peut être atténué par :

- L’utilisation de valeurs TTL faibles pour les applications intranet.
- L’utilisation d’Azure Traffic Manager avec ASR pour les applications basées sur Internet.
- L’utilisation du script suivant dans votre plan de récupération pour mettre à jour le serveur DNS afin de garantir une mise à jour en temps voulu (le script n'est pas requis si l'inscription DNS dynamique est configurée)

		string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


### Modification des adresses IP : Récupération d’urgence dans Azure

Le billet de blog [Networking Infrastructure Setup for Microsoft Azure as a Disaster Recovery Site](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) (Configuration de l’infrastructure de réseau de Microsoft Azure comme site de récupération d’urgence) explique comment configurer l’infrastructure de réseau d’Azure sans que la conservation des adresses IP soit nécessaire. Il commence par décrire l’application, examine la configuration de la mise en réseau localement et sur Azure, puis conclut par la procédure à suivre pour exécuter un test de basculement et un basculement planifié.



## Étapes suivantes

[Découvrir ](site-recovery-network-mapping.md) comment Site Recovery mappe les réseaux source et cible quand un serveur VMM est utilisé pour gérer le site principal.

<!---HONumber=AcomDC_0921_2016-->