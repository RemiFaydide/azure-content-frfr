<properties
	pageTitle="Schémas pour les modèles Resource Manager | Microsoft Azure"
	description="Afficher les modèles de conception pour les modèles Azure Resource Manager"
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="tomfitz"/>

# Schémas de conception des modèles Azure Resource Manager

Dans notre collaboration avec les entreprises, les intégrateurs de système, les fournisseurs de services cloud et les équipes de projet de logiciel open source, il est souvent nécessaire de déployer rapidement des environnements, des charges de travail ou des unités d’échelle. Ces déploiements doivent être pris en charge, suivre des pratiques éprouvées et respecter des stratégies identifiées. À l’aide d’une approche souple, basée sur des modèles Azure Resource Manager, vous pouvez déployer des topologies complexes rapidement et de manière homogène. Vous pouvez adapter facilement ces déploiements selon l’évolution des offres principales. Vous pouvez également les adapter aux variables des scénarios ou clients hors-norme.

Cette rubrique fait partie d’un livre blanc plus volumineux. Pour lire tout le document, téléchargez World Class Azure Resource Manager Templates Considerations and Proven Practices [Considérations et pratiques éprouvées pour concevoir des modèles Azure Resource Manager de premier ordre](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

Les modèles associent les avantages du gestionnaire Azure Resource Manager sous-jacent à l’adaptabilité et à la lisibilité de JSON (JavaScript Objet Notation). En utilisant des modèles, vous pouvez :

- déployer des topologies et leurs charges de travail de manière cohérente ;
- gérer l’ensemble de vos ressources dans une application à l’aide de groupes de ressources ;
- appliquer le contrôle d’accès en fonction du rôle (RBAC) pour accorder un accès approprié aux utilisateurs, groupes et services ;
- utiliser le marquage des associations pour rationaliser des tâches telles que la facturation des cumuls.

Cet article fournit des détails sur les scénarios de consommation, l’architecture et les modèles d’implémentation identifiés au cours de nos sessions de conception et pendant les implémentations de modèle dans le monde réel auprès des clients AzureCAT (Azure Customer Advisory Team). Ces approches reposent sur des pratiques éprouvées qui, loin d’être théoriques, sont documentées par le développement de modèles pour 12 des principales technologies OSS basées sur Linux, notamment : Apache Kafka, Apache Spark, Cloudera, Couchbase, Hortonworks HDP, DataStax Enterprise optimisé par Apache Cassandra, Elasticsearch, Jenkins, MongoDB, Nagios, PostgreSQL, Redis et Nagios. La plupart de ces modèles ont été développés avec un fournisseur reconnu d’une distribution donnée et influencés par les exigences des clients de Microsoft (entreprises et intégrateurs de systèmes) au cours de projets récents.

Cet article partage ces pratiques éprouvées pour vous aider à concevoir des modèles Azure Resource Manager de premier ordre.

Au cours de notre collaboration avec les clients, nous avons identifié plusieurs expériences de consommation de modèles Resource Manager dans les entreprises, chez les intégrateurs de systèmes et les fournisseurs de services cloud. Les sections suivantes fournissent une vue d’ensemble des scénarios courants et des modèles pour différents types de client.

## Entreprises et intégrateurs de systèmes

Dans les grandes entreprises, il existe généralement deux utilisateurs de modèles Resource Manager : les équipes de développement logiciel internes et les services informatiques. Nous avons observé que les scénarios des intégrateurs de systèmes correspondent à ceux des entreprises. Par conséquent, les mêmes considérations s’appliquent.

### Équipes de développement logiciel internes

Si votre équipe développe des logiciels pour soutenir votre activité, les modèles constituent un moyen simple de déployer rapidement des technologies à utiliser dans des solutions propres à l’activité. Vous pouvez également utiliser ces modèles pour créer rapidement des environnements de formation qui permettent aux membres des équipes d’acquérir les compétences nécessaires.

Vous pouvez utiliser les modèles en l’état, les étendre ou les modifier en fonction de vos besoins. En utilisant un marquage dans les modèles, vous pouvez fournir un récapitulatif de facturation avec différentes vues, par exemple équipe, projet, individu et formation.

Les entreprises veulent souvent que les équipes de développement logiciel créent un modèle pour assurer le déploiement cohérent d’une solution tout en offrant des contraintes afin que certains éléments au sein de cet environnement restent fixes et ne puissent pas être remplacés. Par exemple, une banque peut exiger qu’un modèle inclue le contrôle RBAC afin qu’aucun programmeur ne puisse modifier de solution bancaire pour envoyer des données à un compte de stockage personnel.

### Services informatiques

Les entreprises disposant de services informatiques utilisent généralement des modèles pour fournir une capacité de cloud et des fonctionnalités hébergées dans le cloud.

#### Capacité de cloud

Les groupes de services informatiques fournissent généralement une capacité de cloud aux équipes dans des tailles standard : petite, moyenne et grande. Les offres de taille standard peuvent combiner différents types et quantités de ressources tout en fournissant un niveau de normalisation qui permet d’utiliser les modèles. Les modèles proposent des capacités d’une manière cohérente qui appliquent des stratégies d’entreprise et utilisent le marquage pour fournir la rétrofacturation aux entreprises utilisatrices.

Par exemple, vous devez peut-être fournir des environnements de développement, de test ou de production dans lesquels les équipes de développement logiciel peuvent déployer leurs solutions. L’environnement dispose d’une topologie de réseau prédéfinie et d’éléments que les équipes de développement logiciel ne peuvent pas modifier, par exemple des règles régissant l’accès à l’Internet public et l’inspection des paquets. Vous pouvez également disposer de rôles propres à l’entreprise pour ces environnements avec des droits d’accès distincts pour l’environnement.

#### Fonctionnalités hébergées dans le cloud

Vous pouvez utiliser des modèles pour prendre en charge des fonctionnalités hébergées dans le cloud, notamment des packages logiciels individuels ou des offres composites qui sont proposés aux services internes. Exemple d’une offre composite : analyse proposée en tant que service (analyse, visualisation et autres technologies), transmise dans une configuration optimisée et connectée dans une topologie de réseau prédéfinie.

Les fonctionnalités hébergées dans le cloud sont influencées par les considérations relatives à la sécurité et aux rôles établies par l’offre de capacité de cloud sur laquelle elles sont basées, comme décrit ci-dessus. Ces fonctionnalités sont proposées telles quelles ou en tant que service administré. Pour ce dernier, des rôles avec contrainte d’accès sont requis pour permettre l’accès à l’environnement à des fins de gestion.

## Fournisseurs de services cloud

Après nous être entretenus avec de nombreux fournisseurs de services cloud, nous avons identifié plusieurs approches qui vous permettent de déployer des services pour vos clients ainsi que les obligations associées.

### Offre hébergée par un fournisseur de services cloud

Si vous hébergez votre offre dans votre abonnement Azure, deux approches d’hébergement sont courantes : un déploiement distinct pour chaque client ou le déploiement d’unités d’échelle qui étayent l’infrastructure partagée utilisée pour tous les clients.

- **Déploiements distincts pour chaque client.** Les déploiements distincts par client nécessitent des topologies fixes de différentes configurations connues. Ces déploiements peuvent avoir différentes tailles de machine virtuelle, différents nombres de nœuds et différentes quantités de stockage associé. Le marquage des déploiements est utilisé pour la facturation des cumuls de chaque client. Un contrôle RBAC peut être activé pour permettre aux clients d’accéder aux aspects de leur environnement cloud.
- **Unités d’échelle dans des environnements partagés mutualisés.** Un modèle peut représenter une unité d’échelle pour des environnements mutualisés. Dans ce cas, une même infrastructure est utilisée pour prendre en charge tous les clients. Les déploiements représentent un groupe de ressources qui offrent un niveau de capacité pour l’offre hébergée, comme le nombre d’utilisateurs et le nombre de transactions. Ces unités d’échelle sont augmentées ou réduites en fonction des besoins de la demande.

### Offre des fournisseurs de services cloud injectée dans l’abonnement client

Il se peut que vous souhaitiez déployer vos logiciels dans les abonnements possédés par les clients finaux. Vous pouvez utiliser des modèles pour effectuer des déploiements distincts dans le compte Azure d’un client.

Ces déploiements utilisent le contrôle RBAC afin que vous puissiez mettre à jour et gérer le déploiement dans le compte du client.

### Azure Marketplace

Si vous souhaitez promouvoir et vendre vos offres par le biais d’un Marketplace, par exemple la Place de marché Microsoft Azure, vous pouvez développer des modèles pour fournir des types distincts de déploiement exécutés dans le compte Azure d’un client. Ces déploiements distincts sont décrits en général d’après leur taille (petite, moyenne, grande), le type de produit/public (communauté, développeur, entreprise) ou le type de fonctionnalité (de base, haute disponibilité). Dans certains cas, ces types vous permettent de spécifier certains attributs du déploiement, tels que le type de machine virtuelle ou le nombre de disques.

## Projets OSS

Dans les projets open source, les modèles Resource Manager permettent à une communauté de déployer rapidement une solution à l’aide des pratiques éprouvées. Vous pouvez stocker des modèles dans un référentiel GitHub afin que la communauté puisse les réviser au fil du temps. Les utilisateurs déploient ces modèles dans leur propre abonnement Azure.

Les sections suivantes identifient les éléments à prendre en compte avant de concevoir votre solution.

## Identification des éléments externes et internes d’une machine virtuelle

Lorsque vous concevez votre modèle, il est utile d’examiner les exigences en termes d’éléments externes et internes des machines virtuelles :

- Les éléments extérieurs correspondent aux machines virtuelles et aux autres ressources de votre déploiement, par exemple la topologie de réseau, le marquage, les références aux certificats/secrets et le contrôle d’accès basé sur les rôles. Toutes ces ressources font partie de votre modèle.
- Les éléments intérieurs correspondent aux logiciels installés et à la configuration d’état souhaité globale. Les autres mécanismes, tels que les extensions de machine virtuelle ou les scripts sont utilisés en tout ou partie. Ces mécanismes peuvent être identifiés et exécutés par le modèle, mais ils n’en font pas partie.

Exemples courants des activités à faire « en interne » :

- Installer ou supprimer des fonctionnalités et des rôles de serveur
- Installer et configurer des logiciels au niveau du nœud ou du cluster
- Déployer des sites web sur un serveur web
- Déployer des schémas de base de données
- Gérer le Registre ou d’autres types de paramètre de configuration
- Gérer les fichiers et répertoires
- Démarrer, arrêter et gérer les processus et services
- Gérer les groupes locaux et comptes d’utilisateur
- Installer et gérer les packages (.msi, .exe, yum, etc.)
- Gérer les variables d’environnement
- Exécuter les scripts natifs (Windows PowerShell, bash, etc.)

### Configuration d’état souhaité (DSC)

Réfléchissez à l’état interne de vos machines virtuelles au-delà du déploiement ; vous voulez vous assurer que ce déploiement ne « dérive » pas de la configuration que vous avez définie et archivée dans le contrôle de code source. Cette approche vous assure que vos développeurs ou que votre personnel des opérations n’apportent pas de modifications ad hoc à un environnement, qui ne sont pas approuvées, testées ou enregistrées dans le contrôle de code source. Ce contrôle est important, car les modifications manuelles ne figurent pas dans le contrôle de code source ; elles ne font pas non plus partie du déploiement standard et ont un impact sur les futurs déploiements automatisés du logiciel.

Outre pour vos employés internes, la configuration d’état souhaité est également importante en matière de sécurité. Les pirates informatiques essaient régulièrement de compromettre et d’exploiter les systèmes logiciels. En cas de succès, il est courant d’installer des fichiers et de modifier l’état d’un système compromis. À l’aide de la configuration d’état souhaité, vous pouvez identifier des deltas entre l’état souhaité et l’état réel, et restaurer une configuration connue.

Il existe des extensions de ressource pour les mécanismes les plus populaires de DSC : DSC PowerShell, Chef et Puppet. Chacune de ces extensions peut déployer l’état initial de votre machine virtuelle et peut également être utilisée pour vérifier que l’état souhaité est conservé.

## Étendues courantes de modèle

Nous avons vu que trois étendues clés de modèle de solution émergent. Ces trois étendues (de capacité, de fonctionnalité et de solution de bout en bout) sont décrites plus en détail dans les sections suivantes.

### Étendue de capacité

Une étendue de capacité fournit un ensemble de ressources dans une topologie standard qui est préconfigurée pour respecter les réglementations et les stratégies. L’exemple le plus courant consiste à déployer un environnement de développement standard dans un scénario d’informatique d’entreprise ou d’intégrateur de système.

### Étendue de fonctionnalité

Une étendue de fonctionnalité est axée sur le déploiement et la configuration d’une topologie pour une technologie donnée. Les scénarios courants incluent les technologies SQL Server, Cassandra et Hadoop.

### Étendue de solution de bout en bout

Une étendue de solution de bout en bout est ciblée au-delà d’une seule et même fonctionnalité et est plutôt axée sur la fourniture d’une solution de bout en bout composée de plusieurs fonctionnalités.

Un modèle avec étendue de solution se manifeste comme un ensemble d’un ou plusieurs modèles à étendue de fonctionnalité avec des ressources, une logique et un état souhaité propres à la solution. Un modèle avec étendue de solution est par exemple un modèle de solution de pipeline de données de bout en bout. Le modèle peut combiner une topologie et un état propres à une solution avec plusieurs modèles de solution avec étendue de fonctionnalité comme Kafka, Storm et Hadoop.

## Choix de configurations ouvertes et connues

Initialement, vous pouvez penser qu’un modèle doit donner aux clients la flexibilité maximale, mais de nombreuses considérations affectent le choix quant à l’utilisation de configurations ouvertes ou connues. Cette section identifie les principales exigences des clients et les considérations techniques qui ont façonné l’approche partagée dans ce document.

### Configurations ouvertes

De prime abord, les configurations ouvertes semblent idéales. Elles vous permettent de sélectionner un type de machine virtuelle et de fournir un nombre arbitraire de nœuds et de disques attachés pour ces nœuds, et ce, en tant que paramètres pour un modèle. Toutefois, cette approche n’est pas idéale pour certains scénarios.

L’article [Sizes for virtual machines](./virtual-machines/virtual-machines-windows-sizes.md) présente les différents types et tailles de machines virtuelles disponibles, ainsi que le nombre de disques (2, 4, 8, 16 ou 32) pouvant être attachés. Chaque disque attaché fournit 500 E/S par seconde, et plusieurs disques peuvent être regroupés pour obtenir un multiplicateur de ce nombre d’E/S par seconde. Par exemple, 16 disques peuvent être regroupés pour fournir 8 000 E/S par seconde. Le regroupement est effectué avec la configuration dans le système d’exploitation, à l’aide des espaces de stockage Microsoft Windows ou des disques RAID (Redundant Array of Inexpensive Disk) dans Linux.

Une configuration ouverte permet de sélectionner un nombre d’instances de machine virtuelle, un nombre de différents types de machine virtuelle et les tailles de ces instances, un nombre de disques qui peut varier en fonction du type de machine virtuelle et un ou plusieurs scripts pour configurer le contenu de la machine virtuelle.

Il est courant qu’un déploiement comprenne plusieurs types de nœud, tels que les nœuds principaux et de données. Cette flexibilité est donc souvent proposée pour tous les types de nœud.

Lorsque vous commencez à déployer des clusters de toutes tailles, vous en utilisez des multiples. Si vous déployez un cluster Hadoop, par exemple, comprenant 8 nœuds principaux et 200 nœuds de données, puis regroupez 4 disques attachés sur chaque nœud principal et 16 disques attachés par nœud de données, vous disposez de 208 machines virtuelles et de 3 232 disques à gérer.

Un compte de stockage limite les demandes supérieures à sa limite identifiée de 20 000 transactions par seconde. Vous devez donc examiner le partitionnement de comptes de stockage et effectuer des calculs pour déterminer le nombre approprié de comptes de stockage pour s’adapter à cette topologie. Étant donné la multitude des combinaisons prises en charge par l’approche ouverte, des calculs dynamiques sont requis pour déterminer le partitionnement approprié. Actuellement, le langage du modèle Azure Resource Manager ne fournit pas de fonctions mathématiques. Vous devez donc effectuer ces calculs dans le code, en générant un modèle unique, codé en dur avec les détails appropriés.

Dans les scénarios d’informatique d’entreprise ou d’intégrateur de système, une personne doit mettre à jour les modèles et assurer le support des topologies déployées pour une ou plusieurs entreprises. Cette surcharge supplémentaire, à savoir différentes configurations et différents modèles pour chaque client, est loin d’être souhaitable.

Vous pouvez utiliser ces modèles pour déployer des environnements dans l’abonnement Azure de vos clients, mais les équipes informatiques d’entreprise et les fournisseurs de services cloud les déploient généralement dans leur propre abonnement, en utilisant une fonction de rétrofacturation pour facturer leurs clients. Dans ces scénarios, l’objectif est de déployer une capacité pour plusieurs clients au sein d’un pool d’abonnements et de conserver des déploiements denses dans les abonnements pour réduire leur prolifération, qui implique un plus grand nombre d’abonnements à gérer. Avec des tailles de déploiement réellement dynamiques, atteindre ce type de densité nécessite une planification attentive et le développement supplémentaire de travail de génération de modèles automatique pour le compte de l’entreprise.

En outre, vous ne pouvez pas créer d’abonnements via un appel d’API, mais devez le faire manuellement via le portail. À mesure que le nombre d’abonnements augmente, la prolifération d’abonnements nécessite une intervention humaine, car elle ne peut pas être automatisée. Avec des tailles de déploiements aussi variables, vous seriez tenu de préconfigurer manuellement un certain nombre d’abonnements pour garantir la disponibilité des abonnements.

Compte tenu de tous ces facteurs, une configuration réellement ouverte est moins intéressante que de prime abord.

### Configurations connues : l’approche des tailles standard

Au lieu de proposer un modèle qui fournit une flexibilité totale et des variations innombrables, dans notre expérience, un modèle courant consiste à permettre de sélectionner des configurations connues, c’est-à-dire des tailles standard (bac à sable, petite, moyenne et grande). Les autres exemples de taille standard sont des offres de produits, telles que l’édition Community ou Enterprise. Dans d’autres cas, il peut s’agir de configurations d’une technologie propres à une charge de travail, par exemple MapReduce ou sans SQL.

Nombre d’entreprises informatiques, de fournisseurs OSS et d’intégrateurs de systèmes proposent leurs offres aujourd’hui de cette façon dans des environnements locaux, virtualisés (entreprises) ou en tant qu’offres SaaS (fournisseurs de services cloud et de systèmes d’exploitation).

Cette approche fournit des configurations correctes, connues de différentes tailles qui sont préconfigurées pour les clients. Sans configurations connues, les clients finaux doivent eux-mêmes déterminer la taille de cluster, tenir compte des contraintes des ressources de plateforme et effectuer des opérations mathématiques pour identifier le partitionnement résultant des comptes de stockage et des autres ressources (en raison des contraintes de taille de cluster et de ressource). Les configurations connues permettent aux clients de sélectionner facilement la bonne taille, c’est-à-dire un déploiement donné. Outre l’amélioration de l’expérience du client qu’il procure, un petit nombre de configurations connues est plus facile à prendre en charge et peut vous aider à fournir un haut niveau de densité.

Une approche de configuration connue axée sur des tailles standard peut également proposer un nombre de nœuds variable par taille. Par exemple, une petite taille peut comprendre 3 à 10 nœuds. La taille standard est conçue pour prendre en charge jusqu’à 10 nœuds et permettre au client d’effectuer des sélections libres allant jusqu’à la taille maximale identifiée.

Une taille standard basée sur le type de charge de travail peut être plus libre en termes de nombre de nœuds pouvant être déployés, mais aura une taille de nœud distincte de la charge de travail et une configuration des logiciels sur le nœud.

Les tailles standard basées sur les offres de produits, telles que Community ou Enterprise, peuvent avoir des types de ressource et un nombre maximal de nœuds distincts pouvant être déployés. Cela tient généralement aux licences ou à la disponibilité des fonctionnalités entre les différentes offres.

Vous pouvez également satisfaire des clients avec des variantes uniques utilisant des modèles basés sur JSON. Lorsque vous faites face à des valeurs hors-norme, vous pouvez incorporer la planification et les considérations appropriées pour le développement, le support et l’évaluation des coûts.

En fonction des scénarios de consommation de modèle par les clients, des besoins identifiés au début de ce document et de notre expérience pratique de création de nombreux modèles, nous avons identifié un modèle de décomposition de modèle.

## Modèles de solution avec étendue de capacité et de fonctionnalité

La décomposition fournit une approche modulaire au développement de modèles, qui prend en charge la réutilisation, l’extensibilité, le test et les outils. Cette section fournit des détails sur la façon dont une approche de décomposition peut être appliquée à des modèles avec une étendue de capacité ou de fonctionnalité.

Dans cette approche, un modèle principal reçoit des valeurs de paramètre d’un utilisateur de modèle, puis établit un lien vers plusieurs types de modèle et de script en aval, comme indiqué ci-dessous. Les paramètres, les variables statiques et les variables générées permettent de fournir des valeurs aux modèles liés.

![Paramètres de modèle](./media/best-practices-resource-manager-design-templates/template-parameters.png)

**Les paramètres sont transmis à un modèle principal, puis aux modèles liés**

Les sections suivantes sont consacrées aux types de modèle et de script composant un modèle. Elles présentent également les approches adoptées pour transférer les informations d’état entre les modèles. Chaque modèle ainsi que les types de script dans l’image sont décrits avec des exemples. Pour obtenir un exemple contextuel, consultez la rubrique « Synthèse générale : Exemple d’implémentation » plus loin dans ce document.

### Métadonnées de modèle

Les métadonnées de modèle (fichier metadata.json) contiennent des paires clé/valeur, qui décrivent un modèle dans JSON, lequel peut être lu par les utilisateurs et les systèmes logiciels.

![Métadonnées de modèle](./media/best-practices-resource-manager-design-templates/template-metadata.png)

**Les métadonnées de modèle sont décrites dans le fichier metadata.json**

Les agents de logiciel peuvent récupérer le fichier metadata.json et publier les informations et un lien vers le modèle dans une page ou un répertoire web. Les éléments incluent *itemDisplayName*, *description*, *summary*, *githubUsername* et *dateUpdated*.

Un exemple de fichier est présenté ci-dessous dans son intégralité.

    {
        "itemDisplayName": "PostgreSQL 9.3 on Ubuntu VMs",
        "description": "This template creates a PostgreSQL streaming-replication between a master and one or more slave servers each with 2 striped data disks. The database servers are deployed into a private-only subnet with one publicly accessible jumpbox VM in a DMZ subnet with public IP.",
        "summary": "PostgreSQL stream-replication with multiple slave servers and a publicly accessible jumpbox VM",
        "githubUsername": "arsenvlad",
        "dateUpdated": "2015-04-24"
    }

### Modèle principal

Le modèle principal reçoit les paramètres d’un utilisateur, utilise ces informations pour renseigner des variables objets complexes et exécute les modèles liés.

![Modèle principal](./media/best-practices-resource-manager-design-templates/main-template.png)

**Le modèle principal reçoit les paramètres d’un utilisateur**

Un paramètre fourni est un type de configuration connue également désigné sous le nom de paramètre de taille standard en raison de ses valeurs normalisées : petit, moyen ou grand. Dans la pratique, vous pouvez utiliser ce paramètre de plusieurs façons. Pour plus d’informations, consultez la rubrique « Modèles de ressource de configuration connue », plus loin dans ce document.

Certaines ressources sont déployées indépendamment de la configuration connue spécifiée par un paramètre utilisateur. Ces ressources sont approvisionnées à l’aide d’un modèle de ressource partagé unique et sont partagées par les autres modèles, le modèle de ressource partagé étant exécuté en premier.

Certaines ressources sont déployées éventuellement, indépendamment de la configuration connue spécifiée.

### Modèle de ressource partagé

Ce modèle propose des ressources communes à toutes les configurations connues. Il contient le réseau virtuel, des groupes à haute disponibilité et les autres ressources qui sont requises, quel que soit le modèle de configuration connue déployé.

![Ressources de modèle](./media/best-practices-resource-manager-design-templates/template-resources.png)

**Modèle de ressource partagé**

Les noms de ressource, comme le nom de réseau virtuel, sont basés sur le modèle principal. Vous pouvez les spécifier en tant que variable dans ce modèle ou les recevoir de l’utilisateur en tant que paramètre, comme requis par votre entreprise.

### Modèle de ressource facultatif

Le modèle de ressource facultatif contient des ressources qui sont déployées par programme en fonction de la valeur d’une variable ou d’un paramètre.

![Ressources facultatives](./media/best-practices-resource-manager-design-templates/optional-resources.png)

**Modèle de ressource facultatif**

Par exemple, vous pouvez utiliser un modèle de ressource facultatif pour configurer une jumpbox, qui permet d’accéder indirectement à un environnement déployé à partir de l’Internet public. Vous devez utiliser un paramètre ou une variable pour déterminer si la jumpbox doit être activée, et pour identifier la fonction *concat* permettant de générer le nom cible du modèle, par exemple *jumpbox\_enabled.json*. La liaison de modèles doit utiliser la variable obtenue pour installer la jumpbox.

Vous pouvez lier le modèle de ressource facultatif à partir de plusieurs emplacements :

-	S’il est applicable à tous les déploiements, créez un lien contrôlé par les paramètres à partir du modèle de ressource partagé.
-	S’il est applicable à certaines configurations connues, par exemple, uniquement sur les déploiements à grande échelle, créez un lien contrôlé par les paramètres ou contrôlé par les variables à partir du modèle de configuration connue.

Si une ressource donnée est facultative, il se peut qu’elle soit contrôlée par le fournisseur du modèle plutôt que par son utilisateur. Par exemple, vous devrez peut-être répondre à une spécification de produit particulière ou à un module complémentaire de produit (commun aux fournisseurs de services cloud), ou appliquer des stratégies (communes aux groupes d’intégrateurs de systèmes et de services informatiques). Dans ce cas, vous pouvez utiliser une variable pour déterminer si la ressource doit être déployée.

### Modèle de ressource de configuration connue

Dans le modèle principal, un paramètre peut être exposé pour permettre à l’utilisateur de spécifier la configuration connue souhaitée à déployer. Souvent, cette configuration connue utilise une approche de taille standard, avec un ensemble de tailles de configuration fixes, telles que bac à sable, petite, moyenne et grande.

![Ressources de configuration connue](./media/best-practices-resource-manager-design-templates/known-config.png)

**Modèle de ressource de configuration connue**

L’approche des tailles standard est couramment utilisée, mais les paramètres peuvent représenter n’importe quel ensemble de configurations connues. Par exemple, vous pouvez spécifier un ensemble d’environnements pour une application d’entreprise comme Développement, Test et Production. Vous pouvez également l’utiliser pour un service cloud afin de représenter différentes unités d’échelle, versions de produit ou configurations de produit comme Community, Developer ou Enterprise.

Comme pour le modèle de ressource partagé, les variables sont transmises au modèle de configuration connue par :

-	Un utilisateur final : autrement dit, les paramètres envoyés au modèle principal.
-	Une entreprise : autrement dit, les variables du modèle principal qui représentent les exigences ou les stratégies internes.

### Modèle de ressource de membre

Dans une configuration connue, un ou plusieurs types de nœud de membre sont souvent inclus. Par exemple, avec Hadoop, vous disposez de nœuds principaux et de nœuds de données. Si vous installez MongoDB, vous disposez de nœuds de données et d’un arbitre. Si vous déployez DataStax, vous disposez de nœuds de données et d’une machine virtuelle avec OpsCenter installé.

![Ressources de membres](./media/best-practices-resource-manager-design-templates/member-resources.png)

**Modèle de ressource de membre**

Chaque type de nœud peut avoir différentes tailles de machines virtuelles, différents nombres de disques attachés, des scripts pour installer et configurer les nœuds, des configurations de port pour les machines virtuelles, plusieurs instances, etc. Ainsi, chaque type de nœud obtient son propre modèle de ressource de membre qui contient les détails du déploiement et de la configuration d’une infrastructure, ainsi que les détails relatifs à l’exécution de scripts pour déployer et configurer des logiciels sur la machine virtuelle.

En général, pour les machines virtuelles, deux types de script sont utilisés : les scripts réutilisables à grande échelle et les scripts personnalisés.

### Scripts réutilisables à grande échelle

Les scripts réutilisables à grande échelle peuvent être utilisés dans plusieurs types de modèle. L’un des meilleurs exemples de ces scripts réutilisables à grande échelle configure RAID sur Linux afin de regrouper les disques pour augmenter le nombre d’E/S par seconde. Quels que soient les logiciels installés sur la machine virtuelle, ce script permet de réutiliser les pratiques éprouvées pour les scénarios courants.

![Scripts réutilisables](./media/best-practices-resource-manager-design-templates/reusable-scripts.png)

**Les modèles de ressource de membre peuvent appeler des scripts réutilisables à grande échelle**

### Scripts personnalisés

Les modèles appellent généralement un ou plusieurs scripts qui installent et configurent des logiciels sur les machines virtuelles. Cette approche est couramment rencontrée dans les topologies de grande taille dans lesquelles plusieurs instances d’un ou plusieurs types de membre sont déployées. Un script d’installation pouvant être exécuté en parallèle est lancé pour chaque machine virtuelle, suivi d’un script de configuration qui est appelé à l’issue du déploiement de toutes les machines virtuelles (ou de toutes les machines virtuelles d’un type de membre donné).

![Scripts personnalisés](./media/best-practices-resource-manager-design-templates/custom-scripts.png)

**Les modèles de ressource de membre peuvent appeler des scripts pour un objectif spécifique, par exemple la configuration d’une machine virtuelle**

## Exemple de modèle de solution avec étendue de capacité : Redis

Pour illustrer le fonctionnement d’une implémentation, examinons un exemple pratique de création d’un modèle qui facilite le déploiement et la configuration de Redis dans des tailles standard.

Pour le déploiement, il existe un ensemble de ressources partagées (réseau virtuel, compte de stockage, groupes à haute disponibilité) et une ressource facultative (jumpbox). Il existe plusieurs configurations connues représentées par des tailles standard (petite, moyenne, grande), mais chacune dispose d’un type de nœud unique. Il existe également deux scripts propres aux besoins (installation et configuration).

### Création de fichiers de modèle

Vous allez créer un modèle principal nommé azuredeploy.json.

Vous allez créer un modèle de ressource partagé, nommé shared-resources.json.

Vous allez créer un modèle de ressource facultatif pour permettre le déploiement d’une jumpbox, nommée jumpbox\_enabled.json.

Comme Redis utilise simplement un type de nœud unique, vous allez créer un modèle de ressource de membre unique, nommé node-resources.json.

Avec Redis, vous allez installer chaque nœud, puis configurer le cluster. Vous disposez de scripts répondant aux besoins de l’installation et de la configuration : redis-cluster-install.sh et redis-cluster-setup.sh.

### Liaison des modèles

À l’aide d’une liaison de modèles, le modèle principal est lié au modèle de ressource partagé, qui établit le réseau virtuel.

Une logique est ajoutée dans le modèle principal pour permettre aux utilisateurs du modèle de spécifier si une jumpbox doit être déployée. La valeur *enabled* du paramètre *EnableJumpbox* indique que le client souhaite en déployer une. Si cette valeur est indiquée, le modèle concatène *\_enabled* comme suffixe d’un nom de modèle de base pour la fonctionnalité jumpbox.

Le modèle principal applique la valeur de paramètre *large* comme suffixe d’un nom de modèle de base pour les tailles standard, puis utilise cette valeur dans un lien du modèle vers *technology\_on\_os\_large.json*.

La topologie doit ressembler à cette illustration.

![Modèle Redis](./media/best-practices-resource-manager-design-templates/redis-template.png)

**Structure d’un modèle Redis**

### Configuration d’un état

Pour les nœuds du cluster, la configuration de l’état s’effectue en deux étapes, représentées par les scripts propres aux besoins : « redis-cluster install.sh » installe Redis et « redis-cluster-setup.sh » configure le cluster.

### Prise en charge des déploiements de taille différente

Au sein des variables, le modèle de taille standard spécifie le nombre de nœuds de chaque type à déployer pour la taille spécifiée (*large*). Il déploie ensuite ce nombre d’instances de machine virtuelle à l’aide de boucles de ressources, en fournissant des noms uniques aux ressources en ajoutant un nom de nœud avec un numéro de séquence numérique à partir de *copyIndex()*. Il applique ces étapes pour les deux machines virtuelles de la zone sensible, comme défini dans le modèle de nom standard.

## Modèles de décomposition et avec étendue de solution de bout en bout

Un modèle de solution avec étendue de solution de bout en bout est destiné à fournir une solution de bout en bout. Cette approche repose généralement sur la composition de plusieurs modèles avec étendue de fonctionnalité avec des ressources supplémentaires, une logique et un état.

Comme illustré dans l’image ci-dessous, un même modèle utilisé pour les modèles avec étendue de fonctionnalité est étendu pour les modèles avec étendue de solution de bout en bout.

Un modèle de ressource partagé et des modèles de ressource facultatifs remplissent la même fonction que dans les approches de modèle avec étendue de capacité et de fonctionnalité, mais ils sont destinés à la solution de bout en bout.

Comme les modèles avec étendue de solution de bout en bout peuvent avoir également des tailles standard, le modèle de ressource de configuration connue reflète ce qui est requis pour une configuration connue donnée de la solution.

Le modèle de ressource de configuration connue établit un lien vers un ou plusieurs modèles de solution avec étendue de fonctionnalité, qui sont pertinents pour la solution de bout en bout, ainsi que vers les modèles de ressource de membre qui sont requis pour la solution de bout en bout.

Comme la taille standard de la solution peut être différente de celle du modèle individuel avec étendue de fonctionnalité, les variables au sein du modèle de ressource de configuration connue permettent de fournir les valeurs appropriées des modèles de solution avec étendue de fonctionnalité en aval pour déployer la taille standard appropriée.

![Bout en bout](./media/best-practices-resource-manager-design-templates/end-to-end.png)

**Le modèle utilisé pour les modèles de solution avec étendue de capacité ou de fonctionnalité peut être facilement étendu en modèle avec étendue de solution de bout en bout**

## Préparation des modèles pour le Marketplace

L’approche décrite plus haut répond facilement aux scénarios dans lesquels les entreprises, les intégrateurs de systèmes et les fournisseurs de services cloud souhaitent déployer eux-mêmes les modèles ou permettre à leurs clients de le faire.

Un autre scénario souhaité consiste à déployer un modèle via le Marketplace. Cette approche de décomposition fonctionne également pour le Marketplace, avec quelques changements mineurs.

Comme indiqué plus haut, les modèles permettent d’offrir des types de déploiement distinct pour la vente dans le Marketplace. Les types de déploiement distinct peuvent être des tailles standard (petite, moyenne, grande), le type de produit/public (communauté, développeur, entreprise) ou le type de fonctionnalité (de base, haute disponibilité).

Comme illustré ci-dessous, la solution de bout en bout existante ou les modèles avec étendue de fonctionnalité peuvent être facilement utilisés pour répertorier les différentes configurations connues dans le Marketplace.

Les paramètres du modèle principal sont tout d’abord modifiés afin de supprimer le paramètre entrant nommé tshirtSize.

Bien que les types de déploiement distinct soient mappés au modèle de ressource de configuration connue, ils ont également besoin des ressources communes et de la configuration du modèle de ressource partagé et, potentiellement, de celles du modèle de ressource facultatif.

Pour publier votre modèle dans le Marketplace, vous devez établir simplement des copies distinctes de votre modèle principal, qui remplace le paramètre entrant tshirtSize auparavant disponible par une variable intégrée au modèle.

![Marketplace](./media/best-practices-resource-manager-design-templates/marketplace.png)

**Adaptation d’un modèle avec étendue de solution pour le Marketplace**

## Étapes suivantes

- Pour obtenir des recommandations sur la façon de gérer la sécurité dans Azure Resource Manager, consultez [Considérations de sécurité pour Azure Resource Manager](best-practices-resource-manager-security.md).
- Pour en savoir plus sur le partage d’état dans les modèles, consultez [Partage d’état dans les modèles Azure Resource Manager](best-practices-resource-manager-state.md).

<!---HONumber=AcomDC_0914_2016-->