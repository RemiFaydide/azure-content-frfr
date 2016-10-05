<properties
	pageTitle="Introduction à Microsoft Azure | Microsoft Azure"
	description="Vous débutez avec Microsoft Azure ? Consultez une présentation générale des services fournis, ainsi que des exemples démontrant leur efficacité."
	services=" "
	documentationCenter=".net"
	authors="rboucher"
	manager=""
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"  
	ms.author="robb"/>

# Présentation de Microsoft Azure

Microsoft Azure est la plateforme d'applications de Microsoft pour le cloud public. L'objectif de cet article est de vous donner les bases pour comprendre les principes essentiels d'Azure, même si vous ne disposez d'absolument aucune connaissance en matière de cloud computing.

**Lecture de cet article**

Azure est en évolution constante, il est donc facile de perdre le fil. Démarrez avec les services de base, qui sont répertoriés en premier dans cet article, avant de poursuivre avec les services supplémentaires. Si vous le souhaitez, vous pouvez évidemment utiliser les services supplémentaires, mais sachez qu'une application exécutée dans Azure repose sur les services de base.

**Envoyer des commentaires**

Vos commentaires sont très importants pour nous. Cet article a été rédigé pour vous offrir un aperçu complet d'Azure. Si vous n'êtes pas satisfait de son contenu, n'hésitez pas à nous en informer via la section des commentaires située en bas de page. Faites-nous part de ce que vous attendez, ainsi que des améliorations que nous pouvons apporter à cet article.


## Composants d’Azure

Azure regroupe les services en catégories sur le portail de gestion et sur différentes aides visuelles telles que cette [infographie sur Microsoft Azure](https://azure.microsoft.com/documentation/infographics/azure/). Le portail de gestion vous permet de gérer la plupart des services dans Azure (mais pas tous).

Cet article repose sur une **organisation différente** afin de présenter les services basés sur des fonctionnalités similaires et d’accéder aux sous-services importants intégrés à des services de plus grande taille.

![Composants d’Azure](./media/fundamentals-introduction-to-azure/AzureComponentsIntroNew780.png) *Figure : Azure fournit des services d’application accessibles via Internet, exécutés depuis les centres de données Azure.*

## Portail de gestion
Le [portail de gestion](http://manage.windowsazure.com) Azure est une interface web qui permet aux administrateurs d'accéder à la plupart des fonctionnalités Azure (mais pas à toutes). Microsoft publie généralement une version Bêta du nouveau portail d'interface utilisateur avant de supprimer l'ancien portail. Le nouveau portail est appelé [« portail Azure en version préliminaire »](https://portal.azure.com/).

Lorsque les deux portails sont activés, leurs fonctionnalités se chevauchent en grande partie. Les services de base sont présents dans les deux portails. Il est cependant possible que les fonctionnalités ne soient pas toutes disponibles dans les deux portails. Les nouveaux services peuvent apparaître en premier dans le portail le plus récent. De même, il est possible que les fonctionnalités et services plus anciens ne soient présents que dans le portail le moins récent. Conclusion : si vous ne trouvez pas ce que vous cherchez dans le portail plus ancien, passez au plus récent et inversement.



## Calcul

La mission de base d’une plateforme de cloud est d’exécuter des applications. Chacun des modèles de calcul d’Azure a son propre rôle à jouer.

Vous pouvez utiliser ces technologies de façon séparée ou les combiner en fonction de vos besoins pour créer les meilleures bases pour votre application. Votre approche dépend des problèmes que vous cherchez à résoudre.


### Azure Virtual Machines

![Azure Virtual Machines ROBBCSIART\_TEST](./media/fundamentals-introduction-to-azure/mscsiart_VirtualMachinesIntroNew_12345.png) *Figure : Azure Virtual Machines offre un contrôle total des instances de machine virtuelle dans le cloud.*

Il peut s’avérer très utile de créer une machine virtuelle à la demande, à partir d’une image standard comme depuis une image que vous fournissez. Cette approche, communément appelée « Infrastructure as a Service » (IaaS) est celle adoptée par les machines virtuelles Azure. La figure 2 présente le fonctionnement d'une machine virtuelle ainsi que la création d'une machine virtuelle depuis un disque dur virtuel.

Pour créer une machine virtuelle, indiquez le disque dur virtuel à utiliser et la taille de la machine virtuelle. Vous payez ensuite selon la durée d’utilisation de la machine virtuelle. Vous payez à la minute et uniquement lorsque la machine virtuelle est utilisée. Cela dit, des frais de stockage minimum s'appliquent pour avoir accès au disque dur virtuel. Azure propose une galerie de disques durs virtuels de stockage (appelés « images ») qui contiennent un système d'exploitation amorçable. Ceux-ci comprennent des options Microsoft ainsi que des options partenaires, comme Windows Server et Linux, SQL Server, Oracle et bien d'autres. Vous pouvez créer des disques durs virtuels et des images, puis les télécharger sur le serveur. Vous pouvez même télécharger des disques durs virtuels contenant uniquement des données, puis y accéder depuis vos machines virtuelles en cours d'exécution.

Quelle que soit la provenance du disque dur virtuel, vous pouvez y stocker toutes vos modifications de façon permanente pendant que la machine virtuelle est en cours d’exécution. Lors de la prochaine création d’une machine virtuelle à partir de ce disque dur virtuel, tout reprend là où vous vous étiez arrêté. Les disques durs virtuels associés aux machines virtuelles sont stockés dans des objets blob Azure Storage, sur lesquels nous reviendrons ultérieurement. Vous disposez ainsi de la redondance nécessaire pour empêcher vos machines virtuelles de disparaître en cas de pannes matérielles ou de défaillances des disques. Il est également possible de copier le disque dur virtuel en dehors d’Azure, puis de l’exécuter localement.

Selon la façon dont vous créez votre application, celle-ci peut être exécutée sur une ou plusieurs machines virtuelles.

Cette approche générique du cloud computing peut être utilisée pour gérer de nombreux problèmes.

**Scénarios relatifs à Azure Virtual Machines**

1.	**Développement/Test** - Vous pouvez les utiliser pour créer une plateforme économique de développement et de test, que vous pouvez fermer une fois votre utilisation terminée. Vous pouvez également choisir de créer et d’exécuter des applications utilisant le langage et la bibliothèque que vous préférez. Ces applications peuvent utiliser toutes les options de gestion proposées par Azure. Vous pouvez également exécuter SQL Server ou un autre système de gestion de bases de données sur une ou plusieurs machines virtuelles.
2.	**Déplacement d'applications vers Azure (Soulever et déplacer)** - L'opération « Soulever et déplacer » consiste à déplacer votre application comme si vous déplaciez un gros objet avec un chariot-élévateur. Vous « soulevez » le disque dur virtuel de votre centre de données local et le « déplacez » vers Azure pour l'y exécuter. Vous devrez généralement supprimer les dépendances sur les autres systèmes. Si ces dépendances sont trop nombreuses, vous pouvez opter pour l'option 3.
3.	**Extension de votre centre de données** - Une autre possibilité est d'utiliser les machines virtuelles Azure comme extension de votre centre de données local pour exécuter SharePoint ou d'autres applications. Pour cette opération, il est possible de créer des domaines Windows dans le cloud en exécutant Active Directory sur les machines virtuelles Azure. Vous pouvez utiliser Azure Virtual Network (mentionné ultérieurement) pour relier votre réseau local et votre réseau Azure.



### Web Apps

![Azure Web Apps ROBBCSIART\_TEST](./media/fundamentals-introduction-to-azure/mscsiart_AzureWebsitesIntroNew_12345.png) *Figure : Azure Web Apps exécute une application de site web dans le cloud sans gestion du serveur web sous-jacent.*

Dans bien des cas, le cloud est utilisé pour exécuter les sites et applications web. Azure Virtual Machines permet ceci, mais vous conservez la charge d'administrer une ou plusieurs machines virtuelles et les systèmes d'exploitation sous-jacents. Les rôles web Cloud Services peuvent s'en charger, mais leur déploiement et leur gestion nécessitent malgré tout des tâches d'administration. Pourquoi ne pas simplement opter pour un site web où vous n'avez pas à vous soucier des tâches d'administration ?

C’est exactement ce que fournit Web Apps. Ce modèle de calcul offre un environnement web géré et utilise le portail de gestion Azure ainsi que des API. Vous pouvez transférer une application de site web existante vers Web Apps sans la modifier ou en créer une directement dans le cloud. Une fois que le site web fonctionne, vous pouvez ajouter ou supprimer des instances de façon dynamique sur Azure Web Apps pour équilibrer les demandes. Azure Apps propose une option partagée, grâce à laquelle votre site est exécuté sur une machine virtuelle avec d’autres sites, et une option standard qui permet au site de fonctionner sur sa propre machine virtuelle. L’option standard vous permet également d’augmenter la taille (la puissance informatique) de vos instances si nécessaire.

Pour le développement, Web Apps prend en charge .NET, PHP, Node.js, Java et Python, de même que la base de données SQL et MySQL (de ClearDB, un partenaire Microsoft) pour le stockage relationnel. La prise en charge de plusieurs applications populaires, notamment WordPress, Joomla et Drupal est également intégrée. L'objectif est de fournir une plateforme économique, évolutive et utile au plus grand nombre pour la création de sites et d'applications web dans le cloud public.


**Scénarios Web Apps**

Azure Web Apps a été conçu pour répondre aux besoins des entreprises, des développeurs et des agences de design web. Les entreprises bénéficient d'une solution simple à gérer, évolutive, hautement sécurisée et très disponible pour leurs sites web. Pour configurer un site web, nous vous recommandons de commencer avec Azure Web Apps, puis de passer à Cloud Services lorsque vous avez besoin d’une fonctionnalité qui n’est pas disponible. Reportez-vous à la section « Compute » pour accéder à différents liens qui vous aideront à faire votre choix parmi les options disponibles.

### Services cloud
![Azure Cloud Services](./media/fundamentals-introduction-to-azure/CloudServicesIntroNew.png) *Figure : Azure Cloud Services permet d’exécuter un code personnalisé hautement évolutif dans un environnement PaaS (Platform as a Service).*

Supposons que vous souhaitez générer une application cloud pouvant prendre en charge un grand nombre d’utilisateurs simultanés, en évitant au maximum les tâches d’administration tout en assurant une disponibilité permanente. Que vous soyez un fournisseur de logiciels déjà bien établi et qui, par exemple, décide de se lancer dans les solutions SaaS (software as a service) en transposant une version d’une de vos applications sur le cloud, ou que vous soyez une start-up créatrice d’une application au potentiel prometteur, si vous travaillez dans Azure, quel modèle d’exécution devez-vous choisir ?

Azure Web Apps vous permet de créer ce type d’applications web, mais avec certaines contraintes. Par exemple, vous ne bénéficiez pas d’accès administrateur, ce qui signifie que vous ne pouvez pas installer de logiciel arbitrairement. Les machines virtuelles Azure vous offrent une grande flexibilité, notamment en termes d’accès administrateur. Vous pouvez donc vous en servir pour mettre en place une application hautement évolutive, mais vous devez vous-même gérer de nombreux aspects relatifs à la fiabilité et à l’administration. La solution idéale serait de pouvoir garder le contrôle dont vous avez besoin sans avoir à vous soucier de la plupart des tâches d’administration ou de fiabilité.

C’est exactement ce que proposent les services cloud Azure. Cette technologie est précisément conçue pour prendre en charge les applications évolutives, fiables et à faible niveau d’administration. On parle communément de « Platform as a Service » (PaaS). Pour l’utiliser, créez une application avec la technologie de votre choix (C#, Java, PHP, Python, Node.js ou autre). Votre code est ensuite exécuté dans des machines virtuelles (ou instances) fonctionnant avec une version de Windows Server.

Mais ces machines virtuelles sont différentes de celles que vous créez avec les machines virtuelles Azure. Il faut savoir qu’Azure les gère lui-même (par exemple, pour l’installation de correctifs du système d’exploitation, le déploiement automatique de nouvelles images avec correctifs, etc. Par conséquent, votre application ne doit pas conserver d'état dans les instances de rôles web ou de travail. Elle doit rester dans le cadre d'une des options de gestion de données Azure présentées dans la section suivante. Azure surveille également ces machines virtuelles et les redémarre en cas d'échec. Selon la demande, vous pouvez configurer les services cloud de manière à créer automatiquement plus ou moins d'instances. Vous pouvez ainsi répondre à une utilisation accrue, puis revenir en arrière afin de payer moins cher lorsque l'utilisation diminue.

Vous pouvez choisir entre deux rôles, tous deux basés sur Windows Server, lors de la création d’une instance. La différence principale entre ces deux options est qu’une instance de rôle Web exécute IIS alors que ce n’est pas le cas d’une instance de travail. Les deux sont toutefois gérées de façon identique et il est commun pour une application d’utiliser les deux. Par exemple, une instance de rôle Web peut accepter les requêtes d’utilisateurs, puis les transmettre à une instance de rôle de travail pour traitement. Pour faire évoluer votre application, vous pouvez demander à Azure de créer plus d’instances de chaque rôle ou d’arrêter des instances existantes. Et, de la même façon qu’avec les machines virtuelles Azure, la tarification se base uniquement sur la durée d’exécution de chaque instance Web ou de travail.

**Scénarios relatifs à Azure Cloud Services**

Azure Cloud Services est idéal pour gérer une montée en charge conséquente et bénéficier d’un meilleur contrôle sur la plateforme qu’avec Azure Web Apps, sans toutefois contrôler le système d’exploitation sous-jacent.

#### Choix d'un modèle de calcul
La page [Comparaison entre Azure Web Apps, Azure Cloud Services et Azure Virtual Machines](./app-service-web/choose-web-site-cloud-service-vm.md) contient des informations détaillées qui vous aideront à choisir un modèle de calcul.



## Gestion des données

Les applications ont besoin de données, et chaque type d’application a besoin de données différentes. C’est pour cela qu’Azure propose différentes façons de stocker et de gérer les données. Azure propose de nombreuses options de stockage, toutes conçues pour un stockage de très longue durée. Chacune de ces options vous permet de conserver trois copies synchronisées de vos données dans un centre de données Azure (six si vous autorisez Azure à utiliser la géo-redondance pour la sauvegarde dans un autre centre de données éloigné d'au moins 500 kilomètres).


### Machines virtuelles
Comme mentionné précédemment, vous pouvez exécuter SQL Server ou un autre système de gestion de bases de données (SGBD) sur une machine virtuelle créée avec Azure Virtual Machines. Sachez que cette option n'est pas limitée aux systèmes relationnels. Vous pouvez également exécuter des technologies NoSQL telles que MongoDB et Cassandra. Il est facile d’utiliser votre propre système de base de données, ce qui vous permet d’évoluer dans un environnement similaire à celui de votre propre centre de données. Toutefois, cela implique de gérer l’administration de ce SGBD. D'autres options vous permettent de déléguer à Azure tout ou partie des tâches d'administration.

Ici encore, l'état de la machine virtuelle et des éventuels disques de données supplémentaires que vous créez ou téléchargez repose sur le stockage d'objets blob (dont nous reparlerons ultérieurement).


### Azure SQL Database
![Azure Storage - SQL Database](./media/fundamentals-introduction-to-azure/StorageAzureSQLDatabaseIntroNew.png)

*Figure : Azure SQL Database fournit un service de base de données relationnelle géré dans le cloud.*

Pour le stockage relationnel, Azure fournit la fonctionnalité SQL Database. Ne vous méprenez pas sur son nom. Elle n’a rien à voir avec la base de données SQL fournie par SQL Server et exécutée sur l’infrastructure Windows Server.

Azure SQL Database (anciennement SQL Azure) propose toutes les fonctionnalités essentielles d'un système de gestion de base de données relationnelle, comme les transactions atomiques, l'accès simultané aux données par plusieurs utilisateurs tout en maintenant l'intégrité des données, les requêtes SQL ANSI et un modèle de programmation familier. Tout comme SQL Server, SQL Database est accessible via Entity Framework, ADO.NET, JDBC et les autres principales technologies d’accès aux données. Elle prend également en charge la majorité du langage T-SQL, avec les outils SQL Server tels que SQL Server Management Studio. Pour toute personne familière avec SQL Server (ou toute autre base de données relationnelle), l’utilisation de la base de données SQL ne devrait poser aucun problème.

Mais SQL Database n’est pas un simple système SGBD sur le cloud : c’est un service PaaS. Vous continuez de contrôler vos données et les personnes pouvant y accéder, mais SQL Database prend en charge tout le travail administratif de base (gestion de l’infrastructure matérielle, mise à jour automatique de la base de données et du système d’exploitation, etc.). SQL Database offre également une haute disponibilité, des sauvegardes automatiques, des fonctionnalités de récupération jusqu'à une date et une heure, et peut répliquer des copies d'une zone géographique vers une autre.


**Scénarios relatifs à SQL Database**

Si vous créez une application Azure (à l'aide d'un des modèles de calcul disponibles) nécessitant un stockage relationnel, SQL Database peut être une bonne solution. Les applications fonctionnant hors du cloud peuvent également utiliser ce service, même s’il existe de nombreuses autres possibilités. Par exemple, les données stockées dans SQL Database sont accessibles à partir de différents systèmes client, dont les ordinateurs de bureau, les ordinateurs portables, les tablettes et les téléphones. L’utilisation de SQL Database permet de réduire les temps d’arrêt grâce à la réplication qui assure une haute disponibilité intégrée.


### Tables
![Azure Storage - Tables](./media/fundamentals-introduction-to-azure/StorageTablesIntroNew.png)

*Figure : les tables Azure Tables permettent de stocker des données via la technologie NoSQL.*

Cette fonctionnalité porte parfois d'autres noms, car elle fait partie d'une fonctionnalité plus large appelée « Azure Storage ». Les termes « tables », « tables Azure » et « tables de stockage » désignent tous la même chose.

De même, ne vous inquiétez pas du mot utilisé, cette technologie n'offre pas de stockage relationnel. Il s'agit en fait d'un exemple d'approche NoSQL appelé « stockage clé/valeur ». Les tables Azure permettent à une application de stocker différents types de propriétés, comme des chaînes, des nombres entiers et des dates. Une application peut ensuite récupérer un groupe de propriétés en fournissant une clé unique pour ce groupe. Même si les opérations complexes telles que les jonctions ne sont pas prises en charge, les tables permettent surtout un accès rapide aux données saisies. Elles sont également hautement évolutives : une seule table peut contenir jusqu’à un téraoctet de données. Comme elles sont plus simples, les tables sont également plus économiques à l’utilisation que le stockage relationnel de SQL Database.

**Scénarios relatifs aux tables Azure**

Supposons que vous souhaitiez créer une application Azure bénéficiant d’un accès rapide aux données saisies, en volumes importants, sans avoir besoin d’exécuter des requêtes SQL complexes sur ces données. Imaginons, par exemple, que vous créez une application cliente chargée de stocker les informations de profil pour chaque utilisateur. Votre application va être très populaire, vous devez donc avoir une grande capacité de données, mais vous allez vous contenter de les stocker, puis de les récupérer de façon simple. C’est précisément pour ce type d’utilisation qu’ont été conçues les tables Azure.


### Objets blob
![Azure Storage - Objets blob](./media/fundamentals-introduction-to-azure/StorageBlobsIntroNew.png) *Figure : les objets blob Azure fournissent des données binaires non structurées.*

Les objets blob Azure (pour rappel, les termes « stockage d'objets blob » et « objet blob de stockage » désignent la même chose) sont conçus pour stocker des données binaires non structurées. Tout comme les tables, les objets blob offrent un stockage économique, et la taille d'un objet blob peut atteindre 1 To (un téraoctet). Les applications Azure peuvent également utiliser les lecteurs Azure, qui permettent aux objets blob de proposer un stockage permanent pour un système de fichiers Windows monté sur une instance Azure. L’application voit des fichiers Windows normaux, mais le contenu est en fait stocké dans un objet blob.

Le stockage d'objets blob est utilisé par beaucoup d'autres fonctionnalités Azure (y compris par Virtual Machines), ce qui permet également de gérer vos charges de travail.

**Scénarios relatifs aux objets blob**

Une application stockant des vidéos, des fichiers volumineux ou d'autres informations binaires peut utiliser les objets blob pour assurer un stockage simple et peu onéreux. Souvent, les objets blob sont aussi utilisés en association avec d'autres services, comme le réseau de distribution de contenu (CDN) sur lequel nous nous pencherons ultérieurement.

### Import/Export
![Azure Import Export Service](./media/fundamentals-introduction-to-azure/ImportExportIntroNew.png)

*Figure : le service Azure Import/Export permet de transférer un disque dur physique vers ou depuis Azure pour accélérer de manière économique l’importation ou l’exportation des données en bloc.*

Vous serez peut-être amené à transférer de gros volumes de données dans Azure. Cette opération peut prendre beaucoup de temps, plusieurs jours parfois, et utiliser beaucoup de bande passante. Dans ce cas, vous pouvez utiliser le service Azure Import/Export, pour transférer directement des disques durs SATA 3.5" chiffrés avec Bitlocker vers des centres de données Azure et Microsoft transférera les données correspondantes vers un stockage d'objets blob. Une fois le téléchargement effectué, Microsoft vous renverra les disques durs. Vous pouvez également demander à ce que les gros volumes de données du stockage d'objets blob soient exportés sur des disques durs et qu'ils vous soient envoyés par la poste.

**Scénarios relatifs à Azure Import/Export**

- **Migration de gros volumes de données** - Pour télécharger de gros volumes de données (plusieurs téraoctets) vers Azure, le service Import/Export est généralement plus rapide et parfois moins coûteux qu'un transfert via Internet. Lorsque les données se trouvent dans des objets blob, vous pouvez les traiter sous d'autres formes, comme un stockage de tables ou une base de données SQL.

- **Récupération des données archivées** - Vous pouvez utiliser le service Import/Export pour transférer de gros volumes de données stockées dans un stockage d'objets blob Azure vers un périphérique de stockage préalablement fourni à Microsoft et vous faire livrer celui-ci à l'adresse de votre choix. Dans la mesure où ce transfert prend du temps, cette solution ne convient pas à la récupération d'urgence. Elle convient davantage aux données archivées auxquelles vous n'avez pas besoin d'accéder rapidement.


### Service de fichiers
![Azure File Services](./media/fundamentals-introduction-to-azure/FileServiceIntroNew.png) *Figure : Azure File Services fournit des chemins SMB \\\serveur\\partage aux applications exécutées dans le cloud.*

Localement, les gros volumes de stockage de fichiers sont généralement accessibles grâce au protocole Server Message Block (SMB) via un format \\\Serveur\\partage. Azure propose désormais un service qui vous permet d'utiliser ce protocole dans le cloud. Les applications exécutées sous Azure peuvent utiliser ce service pour partager des fichiers entre les machines virtuelles à l'aide d'API de systèmes de fichiers connus, comme ReadFile et WriteFile. En outre, les fichiers sont accessibles simultanément via une interface REST, ce qui vous permet d'accéder aux partages en local lorsque vous configurez un réseau virtuel. Azure Files se trouve au sommet du service d'objets blob, ce qui lui permet d'hériter des mêmes caractéristiques de disponibilité, de durabilité, d'extensibilité et de géo-redondance qu'Azure Storage.

**Scénarios relatifs à Azure Files Services**

- **Migration d'applications existantes dans le cloud** - Il est plus facile de transférer vers le cloud des applications locales qui utilisent des partages de fichiers afin de partager les données entre différentes parties de l'application. Chaque machine virtuelle se connecte au partage de fichiers, après quoi elle peut lire et écrire dans les fichiers comme sur un partage de fichiers local.

- **Paramètres d'applications partagés** - Pour les applications distribuées, les fichiers de configuration sont souvent centralisés à un emplacement accessible par différentes machines virtuelles. Ces fichiers de configuration peuvent être stockés dans un partage Azure Files, puis lus par toutes les instances de l'application. Les paramètres peuvent également être gérés via l'interface REST, ce qui offre un accès mondial aux fichiers de configuration.

- **Partage de diagnostic** - Vous pouvez enregistrer et partager des fichiers de diagnostic comme des journaux, des indicateurs de performances et des vidages sur incident. Le fait que ces fichiers soient à la fois disponibles via SMB et l'interface REST permet aux applications d'utiliser différents outils d'analyse pour traiter et analyser les données de diagnostic.

- **Développement/Test/Débogage** - Lorsque les développeurs ou les administrateurs travaillent sur des machines virtuelles situées dans le cloud, ils ont souvent besoin de différents outils ou utilitaires. L'installation et la distribution de ces utilitaires sur chaque machine virtuelle prennent du temps. Avec Azure Files Services, un développeur ou administrateur peut stocker ses outils préférés sur un partage de fichiers et les relier à n'importe quelle machine virtuelle.



## Mise en réseau

Azure est aujourd'hui exécuté dans de nombreux centres de données à travers le monde. Lorsque vous exécutez une application ou que vous stockez des données, vous pouvez choisir d’utiliser un ou plusieurs de ces centres de données. Vous pouvez également vous y connecter de différentes façons grâce aux services suivants :


### Réseau virtuel
![VirtualNetwork](./media/fundamentals-introduction-to-azure/VirtualNetworkIntroNew.png)

*Figure : le service Virtual Network fournit un réseau privé dans le cloud qui permet à différents services de communiquer les uns avec les autres, ou avec des ressources locales si vous configurez une connexion VPN intersite.*


Une approche utile pour utiliser un cloud public est de le considérer comme une extension de votre propre centre de données.

Comme vous pouvez créer des machines virtuelles à la demande, puis les supprimer (et ainsi arrêter la facturation) lorsque vous n’en avez plus besoin, vous disposez de toute la puissance informatique, juste quand vous en avez besoin. Et dans la mesure où Azure Virtual Machines vous permet de créer des machines virtuelles exécutant SharePoint, Active Directory et d'autres logiciels courants utilisés en local, cette approche fonctionne avec les applications dont vous disposez déjà.

Pour que ceci soit réellement utile, vos utilisateurs doivent pouvoir traiter ces applications comme si elles étaient exécutées sur votre propre centre de données. C’est exactement ce que propose le réseau virtuel Azure. Avec une passerelle VPN, un administrateur peut configurer un réseau privé virtuel (VPN) entre votre réseau local et les machines virtuelles déployées sur un réseau virtuel Azure. Comme vous attribuez vos propres adresses IP v4 aux machines virtuelles du cloud, elles semblent se trouver sur votre réseau. Les utilisateurs de votre entreprise peuvent accéder aux applications contenues sur ces machines virtuelles de la même façon que si elles étaient exécutées en local.

Pour plus d'informations sur la planification et la création d'un réseau virtuel, consultez la rubrique [Virtual Network](./virtual-network/virtual-networks-overview.md).

### ExpressRoute

![ExpressRoute](./media/fundamentals-introduction-to-azure/ExpressRouteIntroNew.png)

*Figure : le service ExpressRoute utilise une instance d’Azure Virtual Network, mais au lieu de passer par le réseau Internet public, il achemine les connexions à l’aide de lignes dédiées plus rapides.*

Si vous avez besoin de bande passante supplémentaire ou si vous souhaitez bénéficier d'une sécurité supérieure à celle que peut fournir une connexion Azure Virtual Network, vous pouvez opter pour ExpressRoute. Dans certains cas, ExpressRoute peut également vous faire économiser de l'argent. Vous aurez toujours besoin d'un réseau virtuel Azure, mais la liaison entre Azure et votre site se fera par une connexion dédiée qui ne passera pas par le réseau Internet public. Pour utiliser ce service, vous devrez passer un accord avec un fournisseur de services de réseau ou un fournisseur Exchange.

La configuration d'une connexion ExpressRoute nécessite du temps et une certaine planification, aussi est-il préférable de commencer avec un VPN de site à site avant de migrer vers une connexion ExpressRoute.

Pour plus d'informations sur ExpressRoute, consultez la rubrique [ExpressRoute - Aperçu technique](./expressroute/expressroute-introduction.md).

### Traffic Manager

![TrafficManager](./media/fundamentals-introduction-to-azure/TrafficManagerIntroNew.png)

*Figure : Azure Traffic Manager vous permet d’acheminer le trafic global vers votre service sur la base de règles intelligentes.*

Si votre application Azure est exécutée dans plusieurs centres de données, vous pouvez utiliser Azure Traffic Manager pour orienter intelligemment les requêtes des utilisateurs sur plusieurs instances de l’application. Vous pouvez également acheminer le trafic vers des services non exécutés sous Azure à condition qu'ils soient accessibles depuis Internet.

Une application Azure dont les utilisateurs sont regroupés au sein d'une même zone géographique peut fonctionner dans un centre de données Azure unique. Au contraire, une application avec des utilisateurs répartis dans le monde entier utilisera probablement plusieurs centres de données, voire tous. Dans ce deuxième cas, vous êtes confronté à un problème : comment diriger de façon intelligente les utilisateurs vers des instances de l’application ? Dans la plupart des cas, vous souhaitez que chaque utilisateur accède au centre de données le plus proche de son emplacement, ce qui implique généralement une réduction du délai de réponse. Mais que se passe-t-il si l’instance de l’application est surchargée ou indisponible ? Dans ce cas, la solution idéale consiste à diriger la requête automatiquement vers un autre centre de données. C’est exactement le rôle d’Azure Traffic Manager.

Le propriétaire d’une application définit des règles qui spécifient comment les requêtes des utilisateurs doivent être dirigées vers les centres de données. Azure Traffic Manager se charge ensuite de les appliquer. Par exemple, les utilisateurs peuvent être dirigés par défaut vers le centre de données Azure le plus proche, mais réacheminés vers un autre centre de données lorsque le délai de réponse du premier centre dépasse celui d’autres centres de données. Pour les applications à distribution globale, avec de nombreux utilisateurs, il est utile de disposer d’un service intégré pour gérer de tels problèmes.

Traffic Manager utilise le système DNS (Directory Name Service) pour diriger les utilisateurs vers les points de terminaison de service mais, une fois cette connexion établie, le trafic ne passe plus par Traffic Manager. Traffic Manager ne se transforme ainsi pas en goulot d'étranglement susceptible de ralentir vos communications de service.


## Services de développement
Azure offre aux développeurs et aux professionnels de l'informatique un certain nombre d'outils qui leur permettent de créer et de gérer des applications dans le cloud.

### Kit de développement logiciel (SDK) Azure
En 2008, la toute première version préliminaire d’Azure prenait en charge le développement uniquement en .NET. Aujourd’hui, vous pouvez créer des applications Azure dans la quasi-totalité des langages. Actuellement, Microsoft fournit des Kits de développement logiciel (SDK) spécifiques pour .NET, Java, PHP, Node.js, Ruby et Python. Il existe aussi un Kit de développement logiciel (SDK) Azure général qui assure une prise en charge de base pour tous les langages, tels que C++.

Ces Kits de développement logiciel (SDK) vous aident à créer, à déployer et à gérer vos applications Azure. Ils sont disponibles sur [www.microsoftazure.com](https://azure.microsoft.com/downloads/) ou GitHub et peuvent être utilisés avec Visual Studio et Eclipse. Azure propose également des outils en ligne de commande que les développeurs peuvent utiliser avec tous les éditeurs et les environnements de développement. Cela comprend les outils pour déployer les applications sur Azure depuis des systèmes Linux et Macintosh.

En plus de vous aider à concevoir des applications Azure, ces Kits de développement logiciel (SDK) fournissent des bibliothèques clientes qui vous permettent de créer des logiciels utilisant les services Azure. Par exemple, vous pouvez créer une application qui lit et écrit des objets blob Azure ou concevoir un outil qui déploie des applications Azure via l'interface de gestion d'Azure.

### Visual Studio Team Services

Visual Studio Team Services est une appellation marketing englobant un certain nombre de services qui permettent de développer des applications sous Azure.

Pour éviter toute confusion, aucune version hébergée ou version web de Visual Studio n'est fournie. Une copie locale en cours d'exécution de Visual Studio est requise. Mais vous avez accès à des outils supplémentaires qui peuvent se révéler très utiles.

Le système de contrôle de code source hébergé Team Foundation Service est également fourni pour le contrôle de version et le suivi des éléments de travail. Pour le contrôle de version, vous pouvez également utiliser Git. Et, selon le projet, vous pouvez utiliser le système de contrôle de code source de votre choix. Vous pouvez créer des projets d'équipe privés illimités accessibles n'importe où dans le monde.

Visual Studio Team Services fournit un service de test de charge. Vous pouvez exécuter des tests de charge créés dans Visual Studio sur des machines virtuelles du cloud. Il vous suffit de spécifier le nombre total d’utilisateurs que vous souhaitez soumettre à un test de charge et Visual Studio Team Services détermine automatiquement le nombre d’agents nécessaires, lance les machines virtuelles requises et exécute vos tests de charge. Si vous êtes abonné à MSDN, vous bénéficiez chaque mois de milliers de minutes gratuites pour les tests de charge.

Visual Studio Team Services prend également en charge le développement agile avec des fonctionnalités comme les builds d’intégration continue, les tableaux Kanban et les salles d’équipe virtuelles.

**Scénarios Visual Studio Team Services**

Visual Studio Team Services est une solution particulièrement intéressante pour les entreprises qui doivent collaborer dans le monde entier et ne disposent pas encore de l’infrastructure nécessaire pour le faire. Vous pouvez procéder à la configuration en quelques minutes, choisir un système de contrôle du code source et commencer à créer le code et la build le jour même. Les outils d'équipe offrent une place de choix à la coordination et à la collaboration, et les outils supplémentaires fournissent l'analyse nécessaire pour tester et paramétrer rapidement votre application.

Les entreprises qui disposent déjà d’un système local peuvent, quant à elles, tester de nouveaux projets sur Visual Studio Team Services pour évaluer son efficacité.

### Application Insights

![Application Insights](./media/fundamentals-introduction-to-azure/ApplicationInsights.png)

*Figure : Application Insights surveille les performances et l’utilisation de votre site web en ligne ou de votre application.*

Quand vous avez publié votre application, qu’elle s’exécute sur des appareils mobiles, des ordinateurs de bureau ou des navigateurs web, Application Insights vous indique comment elle fonctionne et ce que les utilisateurs font avec elle. Il tient le compte du nombre d’incidents et des temps de réponse lents, vous alerte si les chiffres dépassent des seuils inacceptables et vous aide à diagnostiquer les problèmes.

Quand vous développez une nouvelle fonctionnalité, prévoyez de mesurer sa popularité auprès des utilisateurs. En analysant les modèles d’utilisation, vous comprenez ce qui convient le mieux à vos clients et vous améliorez votre application lors de chaque cycle de développement.

Bien qu’il soit hébergé dans Azure, Application Insights fonctionne pour une large gamme d’applications, à la fois sur Azure et en dehors. Les applications web J2EE et ASP.NET sont couvertes, ainsi que les applications iOS, Android, OSX et Windows. Les données de télémétrie sont envoyées depuis un Kit de développement logiciel (SDK) intégré à l’application pour être analysées et affichées dans le service Application Insights d’Azure.

Si vous voulez des analyses plus spécialisées, exportez le flux de données de télémétrie vers une base de données, Power BI ou d’autres outils.

**Scénarios Application Insights**

Vous développez une application. Il peut s’agir d’une application web ou d’une application pour appareil, ou d’une application d’appareil avec un serveur web principal.

* Optimisez les performances de votre application après sa publication, ou pendant son test de charge. Application Insights agrège les données de télémétrie de toutes les instances installées et vous présente des graphiques des temps de réponse, des nombres de demandes et d’exceptions, des temps de réponse des dépendances et d’autres indicateurs de performances. Ceux-ci vous aident à optimiser les performances de votre application. Vous pouvez insérer du code pour produire des données plus spécifiques si vous en avez besoin.
* Détectez et diagnostiquez les problèmes dans votre application en direct. Vous pouvez recevoir des alertes par e-mail si les indicateurs de performances dépassent les seuils acceptables. Vous pouvez examiner des sessions utilisateurs spécifiques, par exemple pour voir la demande qui a provoqué une exception.
* Faites le suivi de l’utilisation pour évaluer la popularité de chaque fonctionnalité. Quand vous concevez un nouveau récit utilisateur, prévoyez de mesurer son utilisation et si les utilisateurs atteignent leurs objectifs attendus. Application Insights vous fournit des données d’utilisation de base, comme les vues de page web, et vous pouvez insérer du code pour faire le suivi de l’expérience utilisateur plus en détail.

### Automatisation
Personne n'aime perdre son temps en répétant continuellement les mêmes processus manuels. Azure Automation vous permet de créer, de surveiller, de gérer et de déployer des ressources dans votre environnement Azure.

Azure Automation utilise des « runbooks » qui ont recours aux workflows Windows PowerShell (et non à la version ordinaire de PowerShell). Les runbooks sont conçus pour s'exécuter sans intervention de l'utilisateur. Les workflows PowerShell permettent d'enregistrer l'état d'un script à certains points de contrôle. Ainsi, en cas de défaillance, vous n'êtes pas contraint de recommencer un script depuis le début. Vous pouvez le reprendre à partir du dernier point de contrôle. Cela permet de gagner beaucoup de temps.

**Scénarios relatifs à Azure Automation**

Azure Automation est une solution idéale pour automatiser les tâches répétitives, manuelles, de longue durée et susceptibles d'engendrer des erreurs dans Azure.


### Gestion des API

La création et la publication d'API (Application Programmer Interfaces) sur Internet est une méthode communément utilisée pour fournir des services à des applications. Si ces services sont destinés à la revente (données météo, par exemple), l'entreprise peut autoriser des tiers à y accéder moyennant paiement. Lorsque le nombre de partenaires augmente, vous devez généralement optimiser et contrôler l'accès. Certains partenaires peuvent également avoir besoin des données sous un autre format.

Le service Gestion des API Azure permet aux entreprises de publier facilement des API pour les partenaires, employés et développeurs tiers et ce, de manière sécurisée et à l'échelle. Il fournit un système d'extrémité d’API différent et agit comme proxy pour appeler le système d'extrémité réel lors de services comme la mise en cache, la transformation, la limitation, le contrôle des accès et l’agrégation d’analyses.

**Scénarios relatifs au service Gestion des API**

Supposons que votre entreprise possède un ensemble de terminaux qui doivent tous accéder à un service central pour obtenir des données ; par exemple, une entreprise de transport dont tous les camions en circulation sont dotés d'un terminal. L'entreprise souhaitera probablement créer un système pour suivre ses camions afin de pouvoir prévoir et actualiser de manière fiable les délais de livraison. Elle pourra connaître le nombre de camions dont elle dispose et effectuer des prévisions appropriées. Chaque camion aura besoin d'un terminal qui transmettra à un site central ses données de positionnement et de vitesse, voire plus.

Un client de l'entreprise de transport pourra également tirer profit de ces données de positionnement. Il pourra les utiliser pour connaître la distance à parcourir par les produits, le lieu où ils sont bloqués, le prix de certaines routes (si associé aux frais de transport). Si l'entreprise de transport agrège déjà ses données, beaucoup de clients seront prêts à payer pour les obtenir. Mais l'entreprise de transport devra alors trouver un moyen de fournir ces données à ses clients. Et une fois les données mises à la disposition des clients, elle n'aura pas forcément le contrôle sur la fréquence à laquelle celles-ci sont interrogées. Elle devra définir des règles pour déterminer qui a accès à telles ou telles données. Toutes ces règles devront être intégrées à son API externe. C'est là que Gestion des API Azure peut s'avérer utile.


## Identité et accès

La plupart des applications fonctionnent avec des identités. En connaissant l'identité d'un utilisateur, l'application sait comment elle doit interagir avec lui. Azure fournit des services permettant d'assurer le suivi des identités et d'intégrer celles-ci aux magasins d'identités que vous utilisez peut-être.


### Active Directory

Comme pour la plupart des services d’annuaire, Azure Active Directory stocke des informations à propos des utilisateurs et de leur entreprise. Le service permet aux utilisateurs de se connecter, puis leur attribue un jeton qui sera présenté aux applications pour prouver leur identité. Il permet de synchroniser les informations utilisateur avec Windows Server Active Directory exécuté sur votre réseau local. Même si les mécanismes et les formats de données utilisés par Azure Active Directory diffèrent de ceux utilisés dans Windows Server Active Directory, les fonctions assurées par les deux services sont relativement identiques.

Il est important de comprendre qu’Azure Active Directory est conçu en priorité pour les applications cloud. Il peut être utilisé par les applications exécutées sur Azure ou sur d’autres plateformes de cloud. Il est également utilisé par les applications en nuage de Microsoft, telles que celles dans Office 365. Si vous souhaitez étendre votre centre de données dans le cloud à l'aide de Machines virtuelles Azure et Azure Virtual Network, cependant, Azure Active Directory n'est pas le bon choix. Il est préférable d'exécuter Windows Server Active Directory sur des machines virtuelles.

Pour permettre aux applications d’accéder aux informations qu’il renferme, Azure Active Directory propose une API RESTful nommée Azure Active Directory Graph. Cette API permet aux applications exécutées sur tous types de plateformes d’accéder aux objets d’annuaire et aux relations qui les unissent. Par exemple, une application autorisée peut utiliser cette API pour obtenir des informations sur un utilisateur, le groupe auquel il appartient ou d’autres informations. Les applications peuvent également détecter les relations entre utilisateurs (leur schéma social) pour travailler de façon plus intelligente en prenant en compte les connexions entre les personnes.

Une autre fonction de ce service, le contrôle d’accès Azure Active Directory, permet à une application d’accepter plus facilement les informations d’identité provenant de Facebook, Twitter, Google, Windows Live ID et d’autres fournisseurs d’identité répandus. Plutôt que de faire reconnaître par l’application les différents formats et protocoles utilisés par chacun de ces fournisseurs, le contrôle d’accès les convertit tous dans un format commun unique. Il permet aussi à l’application d’accepter des identifiants provenant d’un ou de plusieurs domaines Active Directory. Par exemple, un fournisseur proposant une application SaaS peut utiliser le contrôle d’accès Azure Active Directory pour permettre une authentification unique (SSO) sur l’application à ses clients.

Les services d’annuaire sont une composante essentielle de l’informatique en local. Il n’est pas étonnant qu’ils le soient également sur le cloud.

### Azure Multi-Factor Authentication
![Azure Multi-Factor Authentication](./media/fundamentals-introduction-to-azure/MFAIntroNew.png)

*Figure : Multi-Factor Authentication permet à votre application de vérifier plusieurs formes d’identification.*

La sécurité est un élément toujours important. L'authentification multi-facteur (MFA - Multi-factor authentication) permet de veiller à ce que seuls les utilisateurs aient accès à leurs comptes. Pour l'authentification multi-facteur (ou authentification à deux facteurs), les utilisateurs doivent fournir deux de ces trois méthodes de vérification de l'identité pour les connexions et transactions des utilisateurs.

- Un élément que vous connaissez (généralement un mot de passe)
- Un élément que vous possédez (un appareil de confiance qui n'est pas facilement dupliqué, comme un téléphone)
- Un élément vous concernant particulièrement (biométrie)

Ainsi, lorsqu'un utilisateur se connecte, vous pouvez également vérifier son identité via une application mobile, un appel téléphonique ou un message texte en association avec son mot de passe. Par défaut, Azure Active Directory prend en charge l'utilisation de mots de passe comme unique méthode d'authentification pour les connexions des utilisateurs. Vous pouvez utiliser l'authentification multi-facteur en association avec Azure AD ou des applications et annuaires personnalisés à l'aide du Kit de développement logiciel (SDK) MFA. Vous pouvez également l'utiliser avec des applications locales à l'aide du serveur Azure Multi-Factor Authentication.

**Scénarios relatifs à MFA**

Protection des identifiants de connexion aux comptes sensibles comme les comptes bancaires et l'accès au code source pour lesquels un accès non autorisé peut avoir un coût important sur le plan financier ou sur le plan de la propriété intellectuelle.






## Mobile

Si vous créez une application pour un appareil mobile, Azure peut stocker les données dans le cloud, authentifier les utilisateurs et envoyer des notifications push sans que vous ayez à créer un code personnalisé très élaboré.

Même si vous êtes en mesure de concevoir l’infrastructure d’une application mobile à l’aide de Virtual Machines, Cloud Services ou Web Apps, vous gagnerez un temps précieux en utilisant les services d’Azure, car vous n’aurez pas à créer les composants de service sous-jacents.


### Mobile Apps

![Mobile Apps](./media/fundamentals-introduction-to-azure/MobileServicesIntroNew.png)

*Figure : Mobile Apps fournit la fonctionnalité généralement requise par les applications qui font office d’interface avec les appareils mobiles.*

Azure Mobile Apps contient de nombreuses fonctionnalités utiles qui peuvent vous faire gagner un temps précieux lors de la conception de l’infrastructure d’une application mobile. Azure Mobile Services permet une gestion et un approvisionnement simples des données stockées dans une base de données SQL. Avec un code côté serveur, vous pouvez facilement utiliser des options de stockage supplémentaires, telles que le stockage d'objets blob ou MongoDB. Mobile Apps prend les notifications en charge. Cela dit, dans certains cas, vous pouvez utiliser Notification Hubs, comme décrit plus loin. Le service comprend également une API REST à laquelle votre application mobile peut accéder pour effectuer le travail. Mobile Apps permet aussi l’authentification des utilisateurs via Microsoft, Active Directory et d’autres fournisseurs d’identités connus tels que Facebook, Twitter et Google.


Vous pouvez utiliser d'autres services Azure, comme Service Bus ou les rôles de travail, et établir une connexion avec des systèmes locaux. Vous pouvez même utiliser des modules tiers de l'Azure Store (comme SendGrid pour les courriers électroniques) afin de bénéficier de fonctionnalités supplémentaires.


Les bibliothèques clientes natives pour Android, iOS, HTML/JavaScript, Windows Phone et Windows Store facilitent le développement d'applications sur les principales plateformes pour mobiles. Une API REST vous permet d'utiliser les fonctionnalités de données et d'authentification d'Azure Mobile Services avec des applications basées sur différentes plateformes. Un service mobile unique peut être utilisé pour plusieurs applications clientes. Vous pouvez ainsi offrir une expérience utilisateur cohérente sur différents appareils.

Dans la mesure où Azure prend déjà en charge une extensibilité importante, vous pouvez gérer le trafic en fonction de l'évolution de la popularité de votre application. La surveillance et la journalisation sont prises en charge pour résoudre les problèmes et gérer les performances.


### Notification Hubs

![NotificationHubs](./media/fundamentals-introduction-to-azure/NotificationHubsIntroNew.png)

*Figure : Notification Hubs fournit la fonctionnalité généralement requise par les applications qui font office d’interface avec les appareils mobiles.*

Azure Mobile Apps vous permet de rédiger du code pour les notifications tandis que Notification Hubs est optimisé pour diffuser en quelques minutes des millions de notifications push ultra-personnalisées. Vous n'avez pas à vous soucier des détails tels que l'opérateur de téléphonie mobile ou le fabricant de l'appareil. Vous pouvez cibler des utilisateurs individuels ou des millions d'utilisateurs avec un seul appel d'API.

Notification Hubs a été conçu pour fonctionner avec n'importe quelle infrastructure. Vous pouvez utiliser Azure Mobile Apps avec une infrastructure personnalisée située dans le cloud et exécutée auprès de n’importe quel fournisseur, ou avec une infrastructure locale.

**Scénarios relatifs à Notification Hubs** Si vous concevez un jeu mobile dans lequel les joueurs jouent à tour de rôle, vous pouvez être amené à notifier le joueur 2 que la joueuse 1 a terminé son tour. Si vous n’avez rien d’autre à faire, vous pouvez vous contenter d’utiliser Mobile Apps. Mais si 100 000 utilisateurs jouent à votre jeu et que vous souhaitez envoyer une offre ponctuelle à chacun d’eux, mieux vaut utiliser Notification Hubs.

Vous pouvez envoyer des notifications contenant des actualités, des informations sur des événements sportifs et des annonces relatives à un produit à des millions d'utilisateurs avec une faible latence. Les entreprises peuvent envoyer à leurs employés des notifications relatives à des communications ponctuelles afin que ceux-ci n'aient pas à consulter constamment la messagerie ou d'autres applications pour se tenir informés. Vous pouvez également envoyer les mots de passe à usage unique requis pour l'authentification multi-facteur.




## Sauvegarde
Toutes les entreprises doivent sauvegarder et restaurer des données. Vous pouvez utiliser Azure pour sauvegarder et restaurer votre application, qu'elle soit dans le cloud ou locale. En fonction du type de sauvegarde, Azure vous propose différentes options.

### Site Recovery

Azure Site Recovery (anciennement appelé Hyper-V Recovery Manager) vous permet de protéger des applications importantes en coordonnant la réplication et la récupération entre les sites. Site Recovery, qui fournit une fonctionnalité de protection basée sur Hyper-V, VMWare ou SAN sur votre propre site secondaire, sur le site d’un hébergeur ou sur Azure, vous permet d’éviter les frais et la complexité liés au développement et à la gestion de votre propre emplacement secondaire. Azure chiffre les données et communications, et le chiffrement peut également s'appliquer aux données stockées.

Azure surveille en continu l'état de vos services et automatise la récupération organisée des services en cas de panne de site dans le centre de données principal. Les machines virtuelles peuvent être intégrées de manière orchestrée pour restaurer rapidement le service, même pour les charges de travail multiniveau complexes.

Azure Site Recovery utilise des technologies existantes telles que Réplica Hyper-V, System Center et SQL Server Always On. Pour plus de détails, consultez la [vue d’ensemble Azure Site Recovery](site-recovery/site-recovery-overview.md).

### Azure Backup
![Azure Backup](./media/fundamentals-introduction-to-azure/AzureBackupIntroNew.png)

*Figure : Azure Backup sauvegarde les données des serveurs Windows locaux dans le cloud.*

Azure Backup sauvegarde les données des serveurs Windows Server locaux dans le cloud. Vous pouvez gérer vos sauvegardes à l'aide des outils de sauvegarde de Windows Server 2012, Windows Server 2012 Essentials ou System Center 2012 - Data Protection Manager. Vous pouvez également utiliser un agent de sauvegarde spécialisé.

Les données sont mieux sécurisées, car les sauvegardes sont chiffrées avant la transmission, stockées et chiffrées dans Azure et protégées par un certificat que vous téléchargez. Le service utilise la protection de données redondante et hautement disponible d'Azure Storage. Vous pouvez sauvegarder les fichiers et dossiers à intervalles réguliers ou immédiatement, et exécuter des sauvegardes complètes ou incrémentielles. Une fois les données sauvegardées dans le cloud, les utilisateurs autorisés peuvent facilement restaurer les sauvegardes sur un serveur. Des stratégies de rétention des données configurables ainsi que des fonctionnalités de compression de données et de limitation de transfert de données sont également disponibles pour vous permettre de gérer les coûts de stockage et de transfert des données.

**Scénarios relatifs à Azure Backup**

Si vous utilisez déjà Windows Server ou System Center, Azure Backup est une solution idéale pour sauvegarder le système de fichiers de votre serveur, vos machines virtuelles et vos bases de données SQL Server. Il fonctionne avec des fichiers chiffrés, partiellement alloués et compressés. Dans la mesure où il existe certaines limitations, vous devez d'abord [consulter les conditions préalables à l'utilisation d'Azure Backup](http://technet.microsoft.com/library/dn296608.aspx).



## Messagerie et intégration

Quelle que soit son utilité, tout morceau de code a régulièrement besoin d’interagir avec d’autres morceaux de code. Dans certaines situations, une simple messagerie de base en file d’attente suffit. Dans d’autres, des interactions plus complexes sont nécessaires. Azure propose différentes façons de résoudre ces problèmes. La figure 5 vous présente les différentes possibilités.

### Files d’attente
![Azure Service Bus Relay](./media/fundamentals-introduction-to-azure/QueuesIntroNew.png)

*Figure : les files d’attente permettent d’associer des éléments d’une application et facilitent la mise à l’échelle.*

La mise en file d’attente est une idée simple : une application place un message dans une file d’attente afin qu’il soit ensuite lu par une autre application. Si votre application requiert juste ce service simple, les files d’attente Azure peuvent se révéler être le meilleur choix.

Compte tenu de la croissance qu'a connue Azure, les files d'attente Azure Storage et Service Bus fournissent des services de mise en file d'attente similaires. Les raisons qui peuvent vous inciter à utiliser l’une plutôt que l’autre sont présentées dans le document technique [<LINK>](http://msdn.microsoft.com/library/azure/hh767287.aspx "Files d’attente Azure et files d’attente Service Bus : comparaison et différences"). Dans de nombreux scénarios, celles-ci peuvent être utilisées indifféremment.

**Scénarios relatifs aux files d’attente**

Aujourd'hui, les files d'attente sont généralement utilisées pour permettre à une instance de rôle web de communiquer avec une instance de rôle de travail au sein d'une même application de service cloud.

Par exemple, supposons que vous créez une application de partage vidéo avec Azure. Celle-ci consiste en un code PHP exécuté dans un rôle Web qui permet à l’utilisateur de télécharger et de visionner des vidéos, associé à un rôle de travail implémenté en C# qui convertit les vidéos téléchargées en différents formats.

Lorsqu’une instance de rôle Web reçoit une nouvelle vidéo d’un utilisateur, elle peut stocker la vidéo dans un objet blob, puis envoyer un message à un rôle de travail via une file d’attente pour indiquer où trouver la nouvelle vidéo. Une instance de rôle de travail, peu importe laquelle, consulte alors le message dans la file d’attente et effectue les conversions vidéo en arrière-plan.

Structurer une application ainsi permet un traitement asynchrone et simplifie la mise à l’échelle, puisqu’il est possible de faire varier le nombre d’instances de rôles Web et de travail de façon indépendante. Vous pouvez aussi utiliser la taille de la file d'attente comme déclencheur pour faire évoluer le nombre de rôles de travail. Lorsque cette taille augmente, vous pouvez ajouter de nouveaux rôles. Lorsqu'elle diminue, vous pouvez réduire le nombre de rôles pour économiser de l'argent.

Vous pouvez utiliser ce même modèle entre de nombreux éléments différents de votre application, même s'ils n'utilisent pas de rôles de travail/web. Cela vous permet de mettre à l'échelle les éléments de chaque côté de la file d'attente en fonction de la demande et des délais de traitement.


### Service Bus
Qu’elles soient exécutées sur le cloud, dans votre centre de données, sur un appareil mobile ou autre, les applications doivent interagir. L’objectif d’Azure Service Bus est de laisser les applications effectuer à peu près partout les échanges de données.

En plus des files d'attente (un à un) décrites précédemment, Service Bus propose d'autres méthodes de communication.

#### Service Bus Relay
![Azure Service Bus Relay](./media/fundamentals-introduction-to-azure/ServiceBusRelayIntroNew.png)

*Figure : Service Bus Relay permet la communication entre des applications situées de chaque côté d’un pare-feu.*

Service Bus permet la communication directe via son service de relais, offrant ainsi une méthode sécurisée d'interaction à travers les pare-feu. Les relais Service Bus permettent aux applications de communiquer en échangeant des messages via un point de terminaison hébergé dans le cloud, plutôt que localement.

**Scénarios relatifs à Service Bus Relay**

Les applications qui communiquent via Service Bus peuvent être des applications Azure ou des logiciels exécutés sur une autre plateforme cloud. Il peut également s’agir d’applications exécutées hors cloud. On peut prendre l’exemple d’une compagnie aérienne qui implémente ses services de réservation sur des ordinateurs au sein de son propre centre de données. La compagnie aérienne doit rendre ces services accessibles à de nombreux clients (guichets d’enregistrement dans les aéroports, terminaux d’agences de réservation, voire téléphones des clients). Pour ce faire, il est possible d’utiliser Service Bus en créant des interactions souples entre les différentes applications.

#### Rubriques et abonnements Service Bus
![Azure Service Bus - Rubriques](./media/fundamentals-introduction-to-azure/ServiceBusTopicsSubsIntroNew.png) *Figure : les rubriques d’Azure Service Bus permettent à différentes applications de poster des messages et à d’autres applications de s’abonner pour recevoir des messages répondant à des critères spécifiques.*

Service Bus contient un mécanisme de publication et d'abonnement appelé Rubriques et abonnements. Avec le système de publication/d’abonnement, une application peut envoyer des messages vers une rubrique, tandis que d’autres applications peuvent s’abonner à cette dernière. Ceci permet de communiquer vers plusieurs applications depuis une source unique. Un même message est lu par plusieurs destinataires.

**Scénarios pour les rubriques et abonnements Service Bus**

Lorsque vous procédez à une configuration en présence de nombreux messages importants, mais que différents systèmes situés en aval ont seulement besoin d'écouter certains sous-ensembles de ces communications, les Rubriques et abonnements Service Bus constituent une solution intéressante.


### BizTalk Services
![BizTalk Services](./media/fundamentals-introduction-to-azure/BizTalkServicesIntroNew.png) *Figure : BizTalk Services offre la possibilité de transformer les formats de messages XML dans le cloud.*

Il est parfois nécessaire de relier des systèmes qui communiquent à l'aide de différents formats de messagerie. Les schémas de base de données et les formats de messagerie XML sont souvent différents, même lorsqu'une norme commune est disponible. Plutôt que de créer un code personnalisé interminable, vous pouvez utiliser BizTalk Server localement pour intégrer différents systèmes. Azure BizTalk Services fournit le même type de service, mais dans le cloud. Vous payez uniquement ce que vous utilisez sans vous soucier de l'extensibilité, comme en local.


**Scénarios relatifs à BizTalk Services**

Les interactions interentreprises requièrent généralement ce type de traduction. Par exemple, un constructeur aéronautique doit commander des pièces à ses différents fournisseurs. Il aura plusieurs fournisseurs de pièces. Ces commandes doivent être automatisées pour aller directement des systèmes du constructeur aéronautique vers les systèmes de ses fournisseurs. Aucune de ces entreprises ne souhaite modifier ses systèmes centraux et les formats de ses messages, et il est fort peu probable que ces formats soient les mêmes. BizTalk Services peut traduire ces messages dans les deux sens. Le constructeur aéronautique ou ses différents fournisseurs peuvent se charger de la traduction, selon le niveau de contrôle souhaité par chacun et le volume de traduction nécessaire.


## Assistance au calcul
Azure fournit une assistance pour les services non exécutés en permanence.

### Scheduler

![Azure Scheduler](./media/fundamentals-introduction-to-azure/SchedulerIntroNew.png) *Figure : Azure Scheduler permet de planifier les tâches à un moment précis pour une durée déterminée.*

Certaines applications ne sont exécutées que ponctuellement. Pour ce type d'applications, Azure peut vous permettre d'économiser de l'argent en évitant qu'elles fonctionnent 24 heures sur 24 et 7 jours sur 7 dans l'attente de données à traiter. Azure Scheduler vous permet de planifier le moment où une application doit être exécutée, selon un intervalle ou un calendrier donné. Ce service particulièrement fiable vérifie qu'un processus est en cours d'exécution, même en cas de défaillance du réseau, de l'ordinateur et du centre de données. Vous pouvez utiliser l'API REST d'Azure Scheduler pour gérer ces opérations.

Lorsqu’une alarme planifiée se déclenche, Azure Scheduler envoie des messages HTTP ou HTTPS à un point de terminaison spécifique ou peut placer un message dans une file d’attente de stockage. Votre application doit donc disposer d'un point de terminaison accessible ou surveiller une file d'attente de stockage. Lorsqu'elle reçoit le message, elle peut alors effectuer l'opération pour laquelle elle est programmée.

**Scénarios relatifs à Scheduler**

- Actions d’application récurrentes : à titre d'exemple, un service peut régulièrement obtenir des données de Twitter et les rassembler dans un flux régulier.
- Maintenance quotidienne : nettoyage ou traitement des journaux en exécutant des sauvegardes et autres tâches de planification intermittentes.
- Tâches exécutées de nuit.
- Tâches relatives aux applications web, comme les nettoyages quotidiens des journaux en exécutant des sauvegardes et autres tâches de maintenance. Un administrateur peut choisir de sauvegarder sa base de données toutes les nuits à 1 h 00 pendant neuf mois, par exemple.

L'API Scheduler vous permet de créer, de mettre à jour, de supprimer, d'afficher et de gérer des ensembles de tâches et des tâches planifiées par programme.





## Performances

Les performances sont toujours déterminantes pour une application. Les applications tendent à accéder toujours aux mêmes données. Une des façons d’améliorer les performances est de conserver une copie de ces données à proximité de l’application, minimisant ainsi le délai de récupération. Pour ce faire, Azure propose différents services :


### Azure Caching

![Azure Caching](./media/fundamentals-introduction-to-azure/AzureCacheIntroNew.png) **Figure : une application Azure peut mettre des données en cache dans la mémoire, voire les fractionner entre différents rôles de travail.**

L’accès aux données stockées dans un des services de gestion de données Azure (base de données SQL, tables ou objets blob) est relativement rapide. Mais accéder à des données stockées en mémoire l’est encore plus. C’est pour cela que conserver une copie en mémoire des données fréquemment utilisées peut améliorer les performances des applications. La mise en cache en mémoire proposée par Azure vous permet cela.


Une application de services cloud peut stocker des données dans ce cache, puis les récupérer directement sans avoir besoin d’accéder au stockage permanent. Le cache peut être conservé dans les machines virtuelles de votre application ou fourni par des machines virtuelles exclusivement dédiées à la mise en cache. Dans les deux cas, le cache peut être réparti, avec les données contenues diffusées sur plusieurs machines virtuelles dans un centre de données Azure.

Azure comporte un certain nombre de technologies de mise en cache qui ont évolué au fil du temps. Par ordre d'introduction, ces technologies sont les suivantes : cache partagé, In-Role Cache, cache géré et cache Redis. La mise en cache partagée est une technologie ancienne que vous ne devez pas utiliser pour créer de nouvelles implémentations. Le cache géré présente les mêmes fonctionnalités que In-Role Cache, mais en tant que service géré en dehors du portail de gestion Azure. Le cache Redis en est actuellement au stade préliminaire. L'implémentation Redis comporte le plus grand nombre de fonctionnalités et est recommandée pour créer un nouveau code de mise en cache.


**Scénarios relatifs à la mise en cache Azure**

Par exemple, une application qui lit de façon répétée un catalogue produits peut tirer un avantage de ce type de mise en cache puisque les données nécessaires sont disponibles plus rapidement. Cette technologie prend également en charge le verrouillage, permettant une utilisation des données en lecture seule ou en lecture/écriture. Les applications ASP.NET peuvent également utiliser le service pour stocker des données de session en apportant une simple modification de configuration.

### Réseau de distribution de contenu
![Azure CDN](./media/fundamentals-introduction-to-azure/CDNIntroNew.png) **Figure : les copies d’un objet blob peuvent être mises en cache sur des sites du monde entier.**

Supposons que vous devez mettre à disposition d’utilisateurs du monde entier des données sous forme d’objets blob. Il peut s’agir d’une vidéo du dernier match de la Coupe du monde comme d’une mise à jour de pilote ou d’un livre électronique populaire. Stocker une copie des données dans plusieurs centres de données Azure va vous aider, mais ce ne sera probablement pas suffisant si de nombreux utilisateurs sont impliqués. Pour renforcer les performances, vous pouvez utiliser Azure CDN.

Le CDN comporte des dizaines de sites répartis dans le monde entier, chacun étant capable de stocker des données d’objets blob Azure. La première fois qu’un utilisateur accède à un objet blob donné, les informations contenues dans celui-ci sont copiées du centre de données Azure vers le stockage CDN de la zone géographique de l’utilisateur. Par la suite, c’est cette copie en cache dans le CDN de l’objet blob qui est utilisée lorsqu’un utilisateur de cette partie du monde souhaite accéder aux données. Nul besoin d’accéder au centre de données Azure le plus proche. Il en résulte un accès plus rapide aux données les plus consultées par les utilisateurs dans le monde entier.

**Scénarios relatifs à CDN**

CDN (réseau de distribution de contenu) est souvent utilisé avec Media Services pour diffuser des vidéos dans le monde entier. Les vidéos sont généralement volumineuses et nécessitent beaucoup de bande passante. Media Services est abordé plus loin sur cette page.



## Big Compute et données volumineuses

### HDInsight (Hadoop)
![HDInsight](./media/fundamentals-introduction-to-azure/HDInsightIntroNew.png) **Figure : HDInsight permet le traitement en bloc de gros volumes de données.**

Depuis plusieurs années, le gros des analyses de données est réalisé sur des données relationnelles stockées dans des entrepôts de données formés avec un système SGBD relationnel. Ce type d’analyse décisionnelle demeure important et le restera encore longtemps. Mais que faire si les données que vous souhaitez analyser sont tellement volumineuses qu’elles ne peuvent pas être gérées par des bases de données relationnelles ? Et que se passe-t-il s’il ne s’agit pas de données relationnelles ? Il peut s’agir, entre autres exemples, de journaux de serveur dans un centre de données ou de données historiques d’événements enregistrés par des capteurs Dans ce cas, données importantes est synonyme de problèmes importants. Il vous faut trouver une autre approche.

La technologie dominante aujourd’hui pour l’analyse des données volumineuses est Hadoop. Il s’agit d’une technologie open source Apache qui stocke les données à l’aide du système HDFS (Hadoop Distributed File System), puis permet aux développeurs de créer des tâches MapReduce pour analyser les données. HDFS répartit les données sur plusieurs serveurs, puis exécute des parties de la tâche MapReduce sur chaque serveur, permettant un traitement parallèle des données.

HDInsight est le nom du service Azure basé sur Apache Hadoop. HDInsight permet à HDFS de stocker des données sur le cluster et de les distribuer sur plusieurs machines virtuelles. Il diffuse également la logique d’une tâche MapReduce sur ces machines virtuelles. Tout comme avec une solution Hadoop locale, les données sont traitées localement. La logique et les données traitées résident dans la même machine virtuelle, avec une exploitation en parallèle pour renforcer les performances. HDInsight peut également stocker des données dans Azure Storage Vault qui utilise des objets blob. Utiliser ASV vous permet de réaliser des économies, car vous pouvez supprimer votre cluster HDInsight lorsque celui-ci n’est pas utilisé tout en conservant vos données dans le cloud.

HDInsight prend en charge d’autres composants de l’écosystème Hadoop, notamment Hive et Pig. Microsoft a également créé des composants qui facilitent le travail sur les données produites par HDInsight à l’aide d’outils répandus d’analyse décisionnelle, tels que l’adaptateur HiveOBC et Data Explorer, qui fonctionnent avec Excel.

### Calculs complexes (Big Compute)

Une des façons les plus avantageuses d'utiliser une plateforme cloud consiste à exécuter des applications de calculs complexes et autres applications « Big Compute ». Il peut par exemple s'agir d'applications d'ingénierie spécialisées conçues pour utiliser l'interface de transfert de messages MPI standard ainsi que d'applications parallèles, comme des modèles de risques financiers.

Par essence, le Big Compute consiste à exécuter du code sur de nombreuses machines à la fois. Sur Azure, cela se traduit par l’utilisation simultanée de nombreuses machines virtuelles, fonctionnant toutes en parallèle pour résoudre un problème. Un accès aux ressources et aux applications de planification est alors requis, pour répartir les tâches sur les différentes instances par exemple. La solution HPC Pack gratuite de Microsoft et d'autres solutions de cluster de calcul peuvent être utilisées dans Azure, en tirant profit des services de calcul et d'infrastructure Azure pour ajouter de la capacité à la demande à un cluster de calcul local ou exécuter les applications Big Compute entièrement dans le cloud.

Azure propose des machines virtuelles de toutes tailles avec différentes configurations en termes de cœurs d'UC, de mémoire, de capacité de disque et autres caractéristiques afin de satisfaire les exigences des différentes applications. Les instances A8 et A9 récemment introduites fonctionnent très bien pour de nombreuses charges de travail nécessitant beaucoup de calculs, en particulier pour les applications MPI parallèles car elles disposent d'une UC multicœur haute vitesse et d'une mémoire très importante. Dans certaines configurations, les instances bénéficient d'un réseau d'applications à faible latence et débit élevé situé dans le cloud qui inclut la technologie RDMA (Accès direct à la mémoire à distance) pour une efficacité maximum des applications MPI parallèles.

Azure offre également aux développeurs d'applications Big Compute et aux partenaires un ensemble complet de fonctionnalités de calcul, de services, de choix d'architectures et d'outils de développement. Azure prend en charge les workflows Big Compute personnalisés impliquant des workflows de données spécialisées et des modèles de planification des travaux et des tâches qui peuvent évoluer vers des milliers de noyaux de calcul.



## Médias

![Azure Media Services](./media/fundamentals-introduction-to-azure/MediaServicesIntroNew.png) **Figure : Media Services est une plateforme pour les applications fournissant des vidéos ou d’autres médias à des clients du monde entier.**

La vidéo prend une part importante du trafic Internet aujourd’hui et cette proportion va continuer à croître. Mais fournir de la vidéo sur Internet n’est toutefois pas chose simple. Il existe de nombreuses variables, telles que l’algorithme d’encodage et la résolution d’affichage sur l’écran de l’utilisateur. Les demandes de vidéos sont irrégulières. Par exemple, un samedi soir, de nombreuses personnes peuvent décider de regarder un film en ligne.

En réponse à cette popularité, on peut logiquement penser que de plus en plus de nouvelles applications utiliseront la vidéo. Mais elles devront toutes résoudre des problèmes communs. Laisser chaque développeur trouver les réponses de son côté n’a aucun sens. La meilleure approche est de créer une plateforme qui propose des solutions communes utilisables par de nombreuses applications. Mettre en place cette plateforme sur le cloud présente certains avantages réels. Elle est alors hautement disponible, sur une base de paiement à l’utilisation, et permet de gérer les variations de demande auxquelles doivent souvent faire face les applications vidéo.

Azure Media Services a été développé pour répondre à ces problèmes. Il fournit un ensemble de composants de cloud pour simplifier le travail des personnes chargées de créer et d’exécuter des applications utilisant de la vidéo ou d’autres médias.

Comme le montre la figure, Media Services propose un ensemble de composants à destination des applications qui fonctionnent avec de la vidéo ou d’autres médias. Par exemple, un service d’ingestion de médias permet d’envoyer des vidéos vers Media Services (avec stockage sur des objets blob Azure), un composant d’encodage prenant en charge divers formats vidéo et audio, un dispositif de protection de contenu qui permet de gérer les droits numériques, un service permettant d’insérer des publicités dans un flux vidéo, des solutions de diffusion en continu, etc. Les partenaires Microsoft peuvent également fournir des composants pour la plateforme. Microsoft assure ensuite la distribution de ces composants et se charge de la facturation au nom des partenaires.

Les applications qui utilisent cette plateforme peuvent être exécutées sur Azure ou autre. Par exemple, une application pour une maison de production vidéo peut permettre à ses utilisateurs d’envoyer du contenu vers Media Services, puis le traiter de différentes façons. Autre exemple, un service de gestion de contenu basé sur le cloud fonctionnant sur Azure peut s’appuyer sur Media Services pour traiter et distribuer du contenu vidéo. Quel que soit son emplacement et sa fonction, chaque application choisit les composants qui lui sont utiles et y accède via des interfaces RESTful.

Pour distribuer ce qu’elle produit, une application peut utiliser le CDN Azure, un autre CDN ou envoyer des portions directement aux utilisateurs. Quelle que soit l’origine, les vidéos créées à l’aide de Media Services peuvent être utilisées par divers systèmes clients, notamment Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash et Silverlight. L’objectif est de simplifier la création d’applications multimédia modernes.

**Informations de référence**

Pour un aperçu visuel du fonctionnement de Media Services, téléchargez le [poster Azure Media Services][Azure Media Services Poster].

## Commerce

La montée en puissance du SaaS (Software as a Service) transforme la façon dont les applications sont créées. Les modes de vente des applications évoluent également. Les applications SaaS résidant dans le cloud, il semble logique que les clients potentiels recherchent des solutions en ligne. Cette évolution concerne autant les données que les applications. Pourquoi les utilisateurs ne se tourneraient-ils pas vers le cloud pour des ensembles de données disponibles ? Microsoft répond à ces demandes avec [Azure Marketplace](https://azure.microsoft.com/marketplace/).

![Azure Commerce](./media/fundamentals-introduction-to-azure/CommerceIntroNew.png) **Figure : Azure Marketplace et Azure Store vous permettent de rechercher et d’acheter des applications et ensembles de données commerciales Azure afin de les utiliser avec vos applications Azure.**

La différence entre les deux est que le Marketplace se situe hors du portail de gestion Azure alors que le Store est accessible sur le portail. Les clients potentiels peuvent effectuer des recherches pour trouver les applications Azure correspondant à leurs besoins. Les clients peuvent rechercher des ensembles de données commerciales, comme des données démographiques, financières et géographiques. Lorsque l’utilisateur trouve un élément répondant à ses besoins, il peut y accéder via le fournisseur, directement sur le MarketPlace ou le Store sur le web ou encore, dans certains cas, dans le portail de gestion. Les applications peuvent également utiliser l’API de recherche Bing sur le Marketplace, pour accéder aux résultats des recherches Web.


**Scénarios relatifs à Azure Commerce**

SendGrid est une application de l'Azure Store qui vous permet d'envoyer des courriers électroniques. Cette application offre des fonctionnalités supplémentaires comme une remise fiable et des statistiques. Vous pouvez acheter cette application et les services associés plutôt que d'essayer de concevoir une telle infrastructure vous-même.


## Prise en main

Maintenant que vous connaissez les grandes lignes, la prochaine étape est de créer votre première application Azure. Choisissez votre langage, [téléchargez le Kit de développement logiciel (SDK) approprié](/downloads/) et vous n’avez plus qu’à vous lancer. Le cloud computing est la nouvelle norme. Adoptez-le dès maintenant.


[Azure Media Services Poster]: http://azure.microsoft.com/documentation/infographics/media-services/

<!---HONumber=AcomDC_0921_2016-->