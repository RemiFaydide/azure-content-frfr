<properties
   pageTitle="Étude de cas Azure SQL Database - Daxko/CSI | Microsoft Azure"
   description="Découvrez comment Daxko/CSI utilise SQL Database pour accélérer son cycle de développement et améliorer ses performances et ses services clients"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>
   
# Daxko/CSI a utilisé Azure pour accélérer son cycle de développement et améliorer ses performances et ses services clients

![Logo](./media/sql-database-case-study-daxko/csidaxkologo25.png)

Daxko/CSI Software était confronté à un défi : sa clientèle des centres de fitness et de loisirs augmentait rapidement, grâce au succès de sa solution complète de logiciels d’entreprise, mais le personnel informatique de l’entreprise rencontrait des difficultés pour faire face aux besoins d’infrastructure informatique liés au développement de la clientèle. La société était de plus en plus gênée par l’augmentation de la charge opérationnelle, en particulier pour la gestion de ses bases de données. Pire encore, cette surcharge rognait les ressources de développement de nouvelles initiatives, notamment les nouvelles fonctionnalités de mobilité des logiciels de la société.

Selon David Molina, directeur du développement produits à Daxko/CSI, Azure a fourni à CSI Software le modèle PaaS (Platform-as-a-Service) dont elle avait besoin pour simplifier la gestion des bases de données, améliorer l’évolutivité et libérer des ressources pour se concentrer sur les logiciels et non sur les opérations. « Azure SQL Database a représenté une solution très intéressante pour nous. Il était idéal de ne plus avoir à nous soucier de maintenir un serveur SQL Server, un cluster de basculement et tous les autres besoins d’infrastructure. »

Depuis la migration vers Azure, CSI Software n’a besoin que de deux employés chargés des opérations pour gérer plus de 600 bases de données clients. La société utilise les pools élastiques Azure SQL Database pour déplacer les bases de données clients en fonction de la taille et du besoin.

Molina ajoute : « Nos clients se sont immédiatement rendu compte du changement. Avant les pools élastiques, ils subissaient parfois des délais d’attente et d’autres problèmes pendant les périodes de pic de charge. Avec les pools élastiques Azure, ils peuvent utiliser le logiciel sans problème même pendant ces périodes. »

Outre l’amélioration des performances pour les clients, les pools de base de données élastiques Azure ont libéré des ressources de CSI Software pour lui permettre de se concentrer sur le développement de nouveaux services et fonctionnalités, au lieu de s’occuper des opérations et de la gestion. Ces ressources informatiques ont aidé CSI Software à améliorer son offre de logiciels d’entreprise, SpectrumNG, pour attirer des membres, améliorer l’efficacité du personnel et leur offrir à tous un accès mobile à des tâches interactives et des notifications en temps réel.

Azure a également aidé CSI Software à accélérer et améliorer le cycle de développement et d’assurance qualité (AQ) en offrant des possibilités d’automatisation. Avec l’implémentation Azure de l’entreprise, les responsables de build peuvent créer des packages de composants d’un seul clic. Comme le décrit Molina, « Dans le cadre du cycle de lancement, l’AQ est désormais en mesure d’effectuer des déploiements dans un environnement de test Azure, qui reproduit fidèlement notre pile de production. Nous pouvons déployer des builds immédiatement sur notre environnement de développement pour valider les modifications. C’est une grande avancée pour nous, car nous n’avions pas de parité de test auparavant. »

## Déchargement sur le cloud

Avant de passer au cloud, CSI Software a créé avec succès sa propre infrastructure multi-locataire dans un centre de données local à Houston. Au fil de son développement, la société a fait face à des difficultés grandissantes d’achat, d’approvisionnement et de maintenance de tous les équipements et logiciels nécessaires pour répondre aux demandes de ses clients. Le personnel chargé des opérations informatiques est devenu un autre goulet d’étranglement qui a conduit à un ralentissement de l’approvisionnement de nouvelles ressources et du déploiement de nouveaux services auprès des clients.

CSI Software a examiné les options offertes par le cloud pour éliminer cette surcharge et ainsi lui permettre de se concentrer sur le code et non sur les questions opérationnelles. L’entreprise a découvert que, parmi les principaux fournisseurs de cloud, la plupart n’offraient que des solutions IaaS (Infrastructure-as-a-Service) qui nécessitaient quand même un personnel informatique conséquent pour gérer la pile IaaS. En fin de compte, CSI Software a déterminé que la solution PaaS d’Azure était celle qui correspondait le mieux à ses besoins. Molina explique : « Azure élimine les logiciels système et le matériel, ce qui nous permet de nous concentrer sur nos offres logicielles, tout en réduisant notre charge de gestion informatique. »

## La transition vers Azure

Après avoir sélectionné la solution PaaS d’Azure, CSI Software a commencé à migrer son infrastructure principale et ses bases de données vers le cloud. Avant Azure, les clients SpectrumNG devaient installer une application cliente qui communiquait avec un service Windows Communication Foundation (WCF) sur le serveur principal. Selon Molina, « Même si certains clients hébergeaient tout dans leur propre centre de données, nous avons conçu le produit de façon multilocataire. Nous avons tout hébergé dans un centre de données à Houston, avec SQL Server comme magasin de données. »

« Notre offre de produits comprenait également un portail web d’interaction avec les membres (écrit en ASP.NET), conçu en marque blanche pour correspondre à la présence sur le web du client, et une API SOAP prenant en charge les pages en ligne et toutes les intégrations tierces. »

La migration vers le cloud n’a pas pris longtemps pour l’architecture. Selon Molina, « Le plus difficile a été de changer la façon dont nous lisions les informations du fichier de configuration, de modifier la chaîne de connexion centralisée et d’automatiser l’empaquetage, le chargement et le déploiement de nos versions. »

Afin de développer l’automatisation des builds, les ingénieurs de CSI Software ont utilisé Azure PowerShell et les API REST pour créer des packages et les charger dans un environnement intermédiaire en vue d’un lancement toutes les nuits. La transition générale vers un déploiement cloud Azure a été rapide et facile pour l’équipe informatique de CSI Software. Molina explique : « En tout, nous avons obtenu un environnement bêta dans le cloud trois à quatre semaines après avoir entrepris le projet. Une telle réussite nous a surpris. »

Après avoir configuré et testé l’environnement, CSI Software a commencé à migrer les clients. Pour les clients qui utilisaient déjà l’hébergement CSI Software, la transition s’est faite de façon presque transparente. Pour les clients qui migraient à partir d’un déploiement local, la migration vers le cloud a nécessité un peu plus de temps, mais elle a été essentiellement indolore pour les clients et CSI Software.

En ce qui concerne les nouveaux clients, le personnel informatique de CSI Software utilise le processus suivant pour les intégrer à Azure :

1.	Des scripts Azure PowerShell sont utilisés pour créer une base de données pour le client ; tous les clients démarrent sur un niveau Premium pour garantir un débit initial suffisant pour la transition.
2.	Quand c’est possible, CSI Software utilise l’Assistant Migration SQL Azure pour déplacer les données existantes vers une instance Azure SQL Database.
3.	Enfin, les services Microsoft SQL Server Integration Services (SSIS) sont utilisés pour résoudre les divergences de données ou effectuer les éventuels nettoyages de données nécessaires.

Aujourd’hui, environ 99 % des clients de CSI Software sont hébergés dans Azure, sur quatre centres de données régionaux (Nord-Centre, Sud, Est et Ouest). Étant donné que les centres de données se trouvent dans la région géographique de chacun des clients, la latence est réduite au minimum.

## Les pools de base de données élastiques Azure libèrent des ressources informatiques

Plusieurs fonctionnalités d’Azure ont aidé CSI Software à se concentrer sur les fonctions et le développement, et non plus sur l’infrastructure et les opérations. Ce sont peut-être les pools de base de données élastiques qui ont apporté les améliorations les plus importantes.

CSI Software fournit actuellement environ 550 bases de données aux clients. Avant les pools élastiques, il était difficile de gérer autant de bases de données dans une structure hiérarchisée. Les responsables des opérations devaient attribuer différents niveaux de performances selon les besoins de croissance des clients, ce qui nécessitait la mobilisation de nombreuses ressources informatiques. Avec les pools de base de données élastiques, ils peuvent affecter aux locataires un pool Standard ou Premium selon les cas, puis déplacer les clients en fonction de la taille et du besoin. Les clients ont presque immédiatement ressenti les effets des pools de base de données élastiques ; avant, ils subissaient des délais d’attente et d’autres problèmes pendant les périodes de pic d’utilisation mais, avec les pools élastiques, ils peuvent au besoin manifester des pics d’activité et continuer à utiliser SpectrumNG sans problème.

## La géo-réplication active Azure accélère la création de rapports

Plusieurs clients CSI Software tirent également parti de la géo-réplication active Azure. Avec la géo-réplication active, jusqu’à quatre bases de données secondaires accessibles en lecture peuvent être configurées dans des régions de centres de données identiques ou différentes. CSI Software utilise la géo-réplication active de deux manières : tout d’abord, les bases de données secondaires sont disponibles en cas de panne du centre de données ou d’incapacité à se connecter à la base de données primaire ; ensuite, elles sont accessibles en lecture et peuvent être utilisées pour décharger les charges de travail en lecture seule, comme les travaux de création de rapports. Certains clients de CSI Software utilisent cet avantage pour accélérer les flux de travail de création de rapports.

## Architecture et logique des applications de CSI Software

SpectrumNG utilise des rôles Web. L’application étant multilocataire, un service WCF est utilisé pour gérer la demande de connexion initiale des clients. Comme Molina l’indique, « La requête identifie chaque client, ce qui nous permet ensuite de créer une chaîne de connexion à leur base de données afin qu’ils puissent mener à bien leurs travaux. »

Pour le niveau web de son service, CSI Software tire parti de la montée en charge Azure, en fonction de la date et de l’heure. Les ressources disponibles sont automatiquement augmentées pour gérer une utilisation plus importante pendant les heures de travail, selon le fuseau horaire des différents centres de données régionaux. Les ressources sont également programmées pour descendre en puissance le week-end, lorsque les besoins des clients sont plus limités.

     
![La figure 1](./media/sql-database-case-study-daxko/figure1.png)

Figure 1 : Un rôle de travail des services cloud tire des données structurées d’Azure SQL Database et des données semi-structurées du Stockage Table. Les utilisateurs de SpectrumNG interagissent avec ces données par le biais du rôle web des services cloud.

## Utilisation d’applications web et d’un niveau Plan web pour les applications mobiles

Azure SQL Database a permis à CSI Software de libérer des ressources pour pouvoir lancer de nouvelles initiatives, notamment une plateforme mobile complète basée sur une API personnalisée hébergée dans Azure Web Apps. La plateforme permet aux membres et au personnel des salles de sport d’utiliser des appareils mobiles pour vérifier les calendriers, réserver des cours et recevoir des messages.

La plateforme utilise une architecture orientée services (SOA) pour déplacer à la volée un composant unique, comme un système de point de vente (PDV) ou de vente, sur un autre plan web, puis créer un service pour le prendre en charge, tout en laissant tout le reste sur le plan web d’origine. Cette possibilité offre à CSI Software une flexibilité considérable et lui permet de réduire les coûts.

## Azure permet aux développeurs de CSI Software de se concentrer sur les applications et services

Azure SQL Database n’est pas une aubaine que pour les clients SpectrumNG, qui bénéficient d’un service rapide et fiable : c’est également une grande avancée pour le personnel informatique et les développeurs de CSI Software. En déchargeant les opérations sur Azure dans le cloud, CSI Software a réduit la surcharge des ressources et de l’infrastructure, a grandement accéléré ses cycles de développement et n’a plus besoin de microgérer les bases de données pour optimiser les performances de ses locataires.

## Plus d’informations

- Pour plus d’informations sur les pools de base de données élastiques Azure, consultez [Pools de base de données élastiques](sql-database-elastic-pool.md).

- Pour plus d’informations sur les outils de bases de données et la mise à l’échelle élastique, consultez [Outils de bases de données élastique et mise à l’échelle élastique](sql-database-elastic-scale-get-started.md).

- Pour plus d’informations sur la migration d’une base de données SQL Server, consultez [l’Assistant Migration SQL Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md).

- Pour plus d’informations sur la géo-réplication active, consultez [Géo-réplication active](sql-database-geo-replication-overview.md).

- Pour plus d’informations sur les rôles Web et de travail, consultez [Rôles de travail](../fundamentals-introduction-to-azure.md#compute).

- Pour plus d’informations sur Azure Service Bus, consultez [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).

- Pour plus d’informations sur la mise à l’échelle automatique, consultez [Mise à l’échelle des services cloud](../cloud-services/cloud-services-how-to-scale.md).

<!---HONumber=AcomDC_0921_2016-->