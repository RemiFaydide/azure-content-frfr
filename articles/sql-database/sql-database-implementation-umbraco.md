<properties
   pageTitle="Étude de cas Azure SQL Database - Umbraco | Microsoft Azure"
   description="Découvrez comment Umbraco utilise SQL Database afin d’approvisionner rapidement des services et de les mettre à l’échelle pour des milliers de locataires dans le cloud"
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

# Umbraco utilise Azure SQL Database afin d’approvisionner rapidement des services et de les mettre à l’échelle pour des milliers de locataires dans le cloud

![Logo Umbraco](./media/sql-database-case-study-umbraco/umbracologo.png)

Umbraco est un système de gestion de contenu (CMS) open source populaire capable de tout gérer, des sites de petites campagnes ou brochures à des applications complexes pour les entreprises du Fortune 500 et des sites multimédias internationaux.

> « Nous avons une très grande communauté de développeurs qui utilisent le système : plus de 100 000 développeurs sur nos forums et plus de 350 000 sites actifs exécutant Umbraco. »

> — Morten Christensen, responsable technique, Umbraco

> [AZURE.VIDEO azure-sql-database-case-study-umbraco]

Pour simplifier les déploiements clients, Umbraco a ajouté Umbraco-as-a-Service (UaaS) : une offre SaaS (Software-as-a-Service) qui élimine la nécessité de déploiements locaux, fournit une évolutivité intégrée et supprime la surcharge de gestion en permettant aux développeurs de se concentrer sur l’innovation produits plutôt que sur la gestion des solutions. Umbraco est en mesure d’offrir tous ces avantages en s’appuyant sur le modèle PaaS (Platform-as-a-Service) flexible proposé par Microsoft Azure.

L’UaaS permet aux clients SaaS d’utiliser les fonctions de CMS d’Umbraco qui étaient auparavant inaccessibles. Ces clients sont approvisionnés avec un environnement de CMS fonctionnel qui inclut une base de données de production. Les clients peuvent ajouter jusqu’à deux bases de données supplémentaires aux environnements intermédiaire et de développement, en fonction de leurs besoins. Lorsqu’un nouvel environnement est demandé, un processus automatisé affecte automatiquement une base de données à ce client. La nouvelle base de données est prête en quelques secondes, car elle a déjà été pré-approvisionnée par Umbraco à partir d’un pool élastique Azure de bases de données disponibles (voir la figure 1).


![La figure 1](./media/sql-database-case-study-umbraco/figure1.png)

Figure 1 : Approvisionnement du cycle de vie pour Umbraco-as-a-Service (UaaS)
 
##Les pools élastiques Azure et l’automatisation simplifient les déploiements

Avec Azure SQL Database et d’autres services Azure, les clients Umbraco peuvent approvisionner eux-mêmes leurs environnements, et Umbraco peut facilement surveiller et gérer les bases de données selon un flux de travail intuitif :

1.	Mise en service

    Umbraco maintient une capacité de 200 bases de données pré-approvisionnées et disponibles dans des pools élastiques. Lorsqu’un nouveau client s’inscrit à l’UaaS, Umbraco lui fournit un nouvel environnement de CMS quasiment en temps réel en lui affectant une base de données du pool de disponibilité.

    Lorsqu’un pool de disponibilité atteint son seuil, un nouveau pool élastique est créé, et de nouvelles bases de données sont pré-approvisionnées pour être affectées aux clients en fonction des besoins.

    L’implémentation est entièrement automatisée grâce aux bibliothèques de gestion C# et aux files d’attente Azure Service Bus.

2.	Utilisation

    Les clients utilisent entre un et trois environnements (de production, intermédiaire ou de développement), chacun ayant sa propre base de données. Les bases de données clients se trouvent dans des pools de base de données élastiques, ce qui permet à Umbraco de fournir une mise à l’échelle efficace sans approvisionnement excessif.

    ![Figure 2](./media/sql-database-case-study-umbraco/figure2.png)

    ![Figure 3](./media/sql-database-case-study-umbraco/figure3.png)

    Figure 2 : Site web client Umbraco-as-a-Service (UaaS), montrant les détails et la vue d’ensemble du projet

    Azure SQL Database utilise des DTU (Database Transaction Unit) pour représenter la puissance relative requise pour les transactions réelles des bases de données. Pour les clients UaaS, les bases de données fonctionnent généralement à environ 10 DTU, mais chacune a l’élasticité nécessaire pour évoluer à la demande. Cela signifie que l’UaaS peut faire en sorte que les clients aient toujours les ressources nécessaires, même pendant les heures d’activité maximale. Par exemple, lors d’un événement sportif qui a eu lieu récemment, un dimanche soir, un client UaaS a constaté des pics d’utilisation des bases de données jusqu’à 100 DTU pendant toute la durée du match. Les pools élastiques Azure ont permis à Umbraco de gérer cette demande élevée sans dégradation des performances.

3.	Surveiller

    Umbraco surveille l’activité des bases de données à l’aide de tableaux de bord sur le Portail Azure, ainsi que d’alertes e-mail personnalisées.

4.	Récupération d'urgence

    Azure propose deux options de récupération d’urgence : la géo-réplication active et la géo-restauration. Une entreprise doit sélectionner l’option de récupération d’urgence en fonction de ses [objectifs de continuité d’activité](sql-database-business-continuity.md).

    La géo-réplication active fournit le niveau de réponse le plus rapide en cas de temps d’arrêt. À l’aide de la géo-réplication active, vous pouvez créer jusqu’à quatre bases de données secondaires accessibles en lecture sur les serveurs de différentes régions, et ensuite lancer le basculement vers les bases de données secondaires en cas de défaillance.

    Umbraco ne requiert pas la géo-réplication, mais utilise la géo-restauration Azure pour garantir des temps d’arrêt minimum en cas d’interruption. La géo-restauration s’appuie sur les sauvegardes des bases de données dans le stockage Azure géo-redondant. Cela permet aux utilisateurs d’effectuer la restauration à partir d’une copie de sauvegarde en cas d’interruption dans la région primaire.

5.	Annuler l’approvisionnement

    Lorsqu’un environnement de projet est supprimé, les bases de données (de développement, intermédiaires ou en ligne) associées sont supprimées au cours du nettoyage de la file d’attente Azure Service Bus. Ce processus automatisé restaure les bases de données inutilisées dans le pool de disponibilité des bases de données élastiques d’Umbraco, ce qui les rend disponibles pour un approvisionnement futur tout en maintenant une utilisation maximale.

##Les pools élastiques permettent la mise à l’échelle de l’UaaS en toute simplicité

En tirant parti des pools de base de données élastiques Azure, Umbraco peut optimiser les performances de ses clients sans approvisionnement excessif ou insuffisant. Umbraco dispose actuellement de près de 3 000 bases de données sur 19 pools de base de données élastiques, avec la possibilité d’évoluer en fonction des besoins pour prendre en charge ses 325 000 clients existants ou les nouveaux clients prêts à déployer un CMS dans le cloud.

En fait, selon Morten Christensen, responsable technique à Umbraco : « L’UaaS connaît maintenant une croissance d’environ 30 nouveaux clients par jour. Nos clients sont ravis d’avoir la possibilité d’approvisionner de nouveaux projets en quelques secondes, de publier instantanément des mises à jour sur leurs sites en ligne à partir d’un environnement de développement grâce au « déploiement en un clic » et d’apporter des modifications tout aussi rapidement s’ils trouvent des erreurs. »

Si un client n’a plus besoin du deuxième ou du troisième environnement, il peut simplement les supprimer. Cela libère des ressources qui peuvent être utilisées pour d’autres clients dans le cadre du pool de disponibilité des bases de données élastiques d’Umbraco.

![Figure 4](./media/sql-database-case-study-umbraco/figure4.png)

Figure 3. Architecture de déploiement UaaS sur Microsoft Azure

##Le passage du centre de données au cloud

Lorsqu’à l’origine les développeurs Umbraco ont pris la décision de passer à un modèle SaaS, ils savaient qu’ils auraient besoin d’un moyen économique et évolutif de développer le service.

> « Les pools de base de données élastiques sont parfaitement adaptés à notre offre SaaS, car nous pouvons accroître ou diminuer la capacité en fonction des besoins. L’approvisionnement est facile et, avec notre configuration, nous pouvons maintenir une utilisation maximale. »

> — Morten Christensen, responsable technique, Umbraco

« Nous souhaitions consacrer nos efforts à la résolution des problèmes de nos clients, et non à la gestion des infrastructures. Nous voulions que nos clients puissent obtenir la meilleure valeur ajoutée en toute simplicité, dit Niels Hartvig, fondateur d’Umbraco. À l’origine, nous avions envisagé d’héberger nous-mêmes les serveurs, mais la planification de la capacité aurait été un cauchemar. » Par coïncidence, Umbraco n’emploie pas d’administrateurs de bases de données, ce qui met en évidence une proposition de valeur essentielle pour l’utilisation de l’UaaS.

L’un des objectifs importants des développeurs Umbraco était de fournir un moyen aux clients UaaS d’approvisionner des environnements rapidement et sans limite de capacité. Mais un service hébergé dédié dans les centres de données Umbraco aurait demandé une énorme capacité excédentaire pour gérer les pics de traitement. Cela aurait signifié ajouter une infrastructure de calcul considérable qui aurait été régulièrement sous-utilisée.

En outre, l’équipe de développement d’Umbraco souhaitait une solution qui leur permettrait de réutiliser autant que possible leur code. Mikkel Hansen, développeur d’Umbraco, déclare : « Nous étions satisfaits des outils de développement de Microsoft que nous connaissions déjà, comme Microsoft SQL Server, Microsoft Azure SQL Database, ASP.NET et Internet Information Services (IIS). Avant d’investir dans une solution cloud IaaS ou PaaS, nous souhaitions nous assurer qu’elle prendrait en charge nos outils et plateformes Microsoft, afin de ne pas avoir à apporter des modifications importantes à notre base de code. »

Pour répondre à tous ses critères, Umbraco a recherché un partenaire cloud possédant les qualifications suivantes :

-	Capacité et fiabilité suffisantes
-	Prise en charge des outils de développement Microsoft, afin que les ingénieurs Umbraco ne soient pas obligés de réinventer complètement leur environnement de développement
-	Présence dans tous les marchés géographiques de l’UaaS (les entreprises doivent s’assurer qu’elles peuvent accéder à leurs données rapidement et que leurs données sont stockées dans un emplacement qui répond à leurs exigences réglementaires régionales)

##Pourquoi Umbraco a choisi Azure pour l’UaaS

Selon Morten Christensen, « Après avoir étudié tous nos options, nous avons sélectionné Azure, car la solution remplissait tous nos critères de facilité de gestion, d’évolutivité, d’expérience familière et de rentabilité. Nous configurons les environnements sur les machines virtuelles Azure, et chaque environnement possède sa propre instance Azure SQL Database, toutes les instances se trouvant dans des pools de base de données élastiques. En séparant les bases de données entre les environnements de développement, intermédiaire et en ligne, nous pouvons offrir à nos clients évolutivité et isolation robuste des performances, ce qui représente un énorme avantage. »

Morten poursuit : « Avant, il nous fallait approvisionner manuellement les serveurs de bases de données web. À présent, nous n’avons plus à nous en soucier. Tout est automatisé, de l’approvisionnement au nettoyage. »

Morten est également satisfait des capacités de mise à l’échelle offertes par Azure. « Les pools de base de données élastiques sont parfaitement adaptés à notre offre SaaS, car nous pouvons accroître ou diminuer la capacité en fonction des besoins. L’approvisionnement est facile et, avec notre configuration, nous pouvons maintenir une utilisation maximale. » Morten affirme : « La simplicité des pools élastiques, ainsi que l’assurance des DTU basées sur la couche de service, nous donnent la possibilité d’approvisionner de nouveaux pools de ressources à la demande. Récemment, un de nos gros clients a connu un pic à 100 DTU dans son environnement en ligne. À l’aide d’Azure, nos pools élastiques ont fourni en temps réel les ressources nécessaires aux bases de données du client, sans avoir à prévoir les exigences en DTU. En bref, nos clients obtiennent les temps de réponse qu’ils attendent, et nous pouvons remplir nos contrats de niveau de service de performances. »

Mikkel Hansen résume : « Nous avons adopté le puissant algorithme Azure qui connecte un scénario SaaS courant (intégration de nouveaux clients en temps réel et à grande échelle) à notre modèle d’application (pré-approvisionnement de bases de données de développement et en ligne) au-dessus de la technologie sous-jacente (à l’aide des files d’attente Azure Service Bus, conjointement avec Azure SQL Database). »

##Avec Azure, l’UaaS dépasse les attentes des clients

Depuis le choix d’Azure comme partenaire cloud, Umbraco a pu fournir aux clients UaaS une optimisation des performances de gestion du contenu, sans l’investissement en ressources informatiques requis pour une solution hébergée par ses propres moyens. Comme le dit Morten, « Nous aimons la facilité de développement et l’évolutivité d’Azure, et nos clients sont très enthousiasmés par ses fonctionnalités et sa fiabilité. En somme, c’est un grand pas en avant pour nous ! »
 
## Plus d’informations

- Pour plus d’informations sur les pools de base de données élastiques Azure, consultez [Pools de base de données élastiques](sql-database-elastic-pool.md).

- Pour plus d’informations sur Azure Service Bus, consultez [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).

- Pour plus d’informations sur les rôles Web et de travail, consultez [Rôles de travail](../fundamentals-introduction-to-azure.md#compute).

- Pour plus d’informations sur les réseaux virtuels, consultez [Réseaux virtuels](https://azure.microsoft.com/documentation/services/virtual-network/).

- Pour plus d’informations sur la sauvegarde et la récupération, consultez [Continuité d’activité](sql-database-business-continuity.md).

- Pour plus d’informations sur l’analyse des pools, consultez [Analyse des pools](sql-database-elastic-pool-manage-portal.md).

- Pour plus d’informations sur Umbraco-as-a-Service, consultez [Umbraco](https://umbraco.com/cloud).

<!---HONumber=AcomDC_0921_2016-->