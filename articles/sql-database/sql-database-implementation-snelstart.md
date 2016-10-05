<properties
   pageTitle="Étude de cas Azure SQL Database - SnelStart | Microsoft Azure"
   description="Découvrez comment SnelStart s’est appuyé sur SQL Database pour développer rapidement ses services d’entreprise au rythme de 1 000 nouvelles bases de données Azure SQL Database par mois"
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

# Avec Azure, SnelStart a rapidement développé ses services d’entreprise au rythme de 1 000 nouvelles bases de données Azure SQL Database par mois

![Logo](./media/sql-database-case-study-snelstart/snelstartlogo.png)

SnelStart crée des logiciels populaires de gestion financière et commerciale pour les petites et moyennes entreprises (PME) aux Pays-Bas. Une équipe de 110 employés, dont 35 dans le service informatique, est au service de ses 55 000 clients. En passant des logiciels de bureau à une offre SaaS (Logiciel-as-a-Service) basée sur Azure, SnelStart a tiré le meilleur parti des services intégrés, en automatisant la gestion grâce à un environnement familier en C# et en optimisant les performances et l’évolutivité grâce à un approvisionnement parfaitement calibré à l’aide de pools de base de données élastiques. L’utilisation d’Azure fournit à SnelStart la fluidité nécessaire pour déplacer les clients mobiles en local et sur le cloud.

> [AZURE.VIDEO azure-sql-database-case-study-snelstart]

##Pourquoi SnelStart a étendu ses services de l’informatique de bureau vers le cloud

> « Avec Azure, nous pouvons livrer plus vite les logiciels, réagir rapidement aux demandes des clients et mettre à l’échelle les solutions lorsque la demande augmente. »

> — Henry Been, architecte logiciel

SnelStart gère avec succès des activités logicielles depuis des années, selon un modèle de développement traditionnel : codage, empaquetage, livraison et ainsi de suite. Au fil du temps, les changements se sont accélérés. Les réglementations ont été fréquemment modifiées, et les clients avaient besoin de moyens plus simples de traiter les enregistrements financiers et de collaborer avec leurs comptables et avec le gouvernement pour suivre le rythme de ces modifications.

« La livraison des logiciels aux clients était coûteuse et contraignante, selon Henry Been, architecte logiciel. Les coûts de production, d’empaquetage et de livraison limitaient la fréquence à laquelle nous pouvions sortir des logiciels. Nous devions empaqueter des mises à jour en vue d’expéditions régulières, mais de ce fait il était difficile de répondre à l’évolution des besoins de nos clients. Nous n’avions jamais la garantie que nos clients passaient à la dernière version du produit. Par conséquent, nous devions assurer le support de plusieurs versions, ce qui en faisait un travail très difficile à mener à bien. »

Been ajoute : « Nous souhaitions trouver un moyen de programmer et de sortir des mises à jour à un rythme plus rapide, afin d’innover plus rapidement et de créer de nouveaux services pour nos clients. Nous voulions également avoir la possibilité d’automatiser davantage de processus afin de simplifier les besoins d’administration de nos clients. »

Pour SnelStart, la solution consistait à étendre ses services en devenant un fournisseur SaaS dans le cloud. La plateforme Azure SQL Database a aidé SnelStart à y parvenir sans subir la surcharge informatique majeure qu’aurait nécessitée une solution IaaS (Infrastructure-as-a-Service).

Le modèle cloud permet également à SnelStart de corriger les bogues et d’offrir de nouvelles fonctionnalités rapidement, sans que les clients aient besoin de télécharger et de mettre à niveau les logiciels. Selon Been, « L’utilisation des services cloud d’Azure nous permet de réagir rapidement à l’évolution des exigences externes. Au lieu de devoir livrer une nouvelle version à des milliers de clients, nous pouvons adapter les informations envoyées à partir de notre application de bureau aux nouveaux formats requis par les tiers. »

##Une société moderne aux racines traditionnelles

SnelStart est une entreprise de haute technologie moderne et agile aux racines modestes remontant à 1964, date à laquelle les fondateurs ont créé la société en tant que fabricant de pièces d’instruments de musique. Le cœur de l’activité logicielle de SnelStart s’est réellement mis à battre dans les années 1980, lors de la prolifération des ordinateurs personnels. La société avait besoin d’une meilleure solution que les logiciels de comptabilité disponibles à l’époque ; elle a donc pris les choses en main. En 1982, la société a posé la base de ce qui deviendrait finalement le logiciel de comptabilité SnelStart. Dès le début, le logiciel a été admiré pour sa simplicité et sa vitesse, tant et si bien que l’entreprise a fini par changer ses priorités et par se redéfinir comme éditeur de logiciels.

En 1995, SnelStart a publié sa première application de comptabilité pour Windows. L’application, conçue sur Microsoft Visual Basic 1.0 et les bases de données Microsoft Access, a permis à la clientèle d’atteindre plus de 5 000 utilisateurs.

Aujourd’hui, SnelStart est l’un des principaux fournisseurs d’applications métier (LOB) et d’administration destinées aux petites et moyennes entreprises et entrepreneurs indépendants néerlandais. Comme le dit Carlo Kuip, architecte informatique, « Notre objectif est de fournir l’automatisation de 100 % des services d’administration des entreprises à nos clients. »

##Optimisation des performances et des coûts avec les pools élastiques

SnelStart a été un précurseur de l’adoption des pools de base de données élastiques à grande échelle. Les pools élastiques aident l’entreprise à limiter les coûts et à gérer plus efficacement les exigences de performances. Selon Been, « En utilisant des pools de base de données élastiques, nous pouvons optimiser les performances en fonction des besoins de nos clients, sans approvisionnement excessif. Si nous devions effectuer l’approvisionnement en fonction des pics de charge, ce serait très coûteux. Au lieu de cela, la possibilité de partager les ressources entre plusieurs bases de données à faible utilisation nous permet de créer une solution efficace et économique. »

##Les bases de données Azure SQL Database aident à conteneuriser les données dans un objectif d’isolement et de sécurité 

Azure SQL Database permet à SnelStart de déplacer facilement et en toute transparence les données locales d’administration des entreprises vers Azure. C’est un conteneur pratique qui fournit des fonctions d’isolement, de limite d’authentification, d’autorisation et de sauvegarde et restauration faciles. Les bases de données représentent un modèle conceptuel bien adapté à l’administration des entreprises. Selon Carlo Kuip, architecte informatique, « Les éléments à l’intérieur de cette limite de conteneur contiennent des données sensibles et essentielles pour une entreprise, et le stockage de ces éléments dans une base de données isolée assure leur protection. Nous pouvons gérer l’autorisation au niveau de la base de données, et même automatiser la gestion et la montée en charge de ces bases de données sans avoir besoin d’administrateurs de bases de données au sein du personnel. »

Azure SQL Data Warehouse joue également un rôle dans l’histoire de la sécurité et de la gestion de SnelStart en permettant à l’entreprise de collecter des données de télémétrie, comme la détection d’intrusion, la journalisation des activités des utilisateurs et la connectivité.

##Azure élimine la surcharge de sorte que les développeurs puissent passer plus de temps à apporter une valeur ajoutée 

Le modèle de la plateforme Azure a supprimé la surcharge de l’infrastructure et permis à SnelStart d’automatiser les déploiements à l’aide des bibliothèques de gestion C#. Comme Kuip le déclare, « Nous avons pu développer nos opérations en cours avec une très petite équipe tout en augmentant les options de récupération d’urgence, de vitesse et d’évolutivité pour nos clients. Le passage au développement de services a libéré des ressources pour permettre de se concentrer sur de nouveaux services et fonctionnalités, au lieu de simplement mettre à jour le code existant pour faire face aux nouvelles réglementations ou aux nouveaux codes fiscaux. » Il ajoute : « En automatisant la gestion et en utilisant l’offre SaaS, nous pouvons apporter plus de valeur ajoutée à nos clients sans avoir à effectuer des investissements massifs au sein du personnel chargé des opérations. » Par exemple, avec Azure et les pools de base de données élastiques, SnelStart a pu ajouter un grand nombre de nouvelles fonctionnalités, notamment l’intégration plus robuste des données clients avec les banques, de nouveaux services de facturation, la vérification des antécédents des petites entreprises et des services de messagerie.

> « Rien qu’au cours des premiers mois de 2016, nous avons étendu nos déploiements Azure SQL Database d’environ 5 500 à plus de 12 000, et nous ajoutons actuellement à peu près 1 000 bases de données par mois. »

> — Henry Been, architecte logiciel

La gestion des bases de données est davantage automatisée grâce à la fonctionnalité de tâche élastique. Comme Kuip l’indique, « Nous valorisons énormément la détection automatique des bases de données sur une instance [serveur] de SQL DB ». Cela permet à SnelStart d’exécuter des opérations de gestion sur l’ensemble de sa base clients, en croissance dynamique, sans surcharge.

SnelStart développe également une API qui fonctionne comme un répartiteur entre les données des clients et les applications créées par les partenaires logiciels tiers. Comme Kuip l’affirme, « Cette API permettra aux autres fournisseurs d’ajouter des fonctionnalités à nos logiciels, comme l’élimination de la saisie de données pour les factures et d’autres documents. » Les clients n’auront plus à taper manuellement les factures dans leur logiciel de comptabilité pour petites entreprises ; le logiciel SnelStart échangera directement les informations nécessaires. Cela permet aux clients de relier leurs tâches d’administration à l’écosystème informationnel qui émerge de la transformation numérique du secteur.

##Comment les services Azure donnent à SnelStart l’accès au SaaS

Grâce à Azure, SnelStart peut offrir un service plus cohérent à ses clients et leurs comptables sur le cloud. Par exemple, les clients et les comptables peuvent partager des informations en accédant directement aux API clientes de SnelStart, hébergées sur Azure. Kuip indique : « Ces services réutilisables sont accessibles à nos applications web destinées aux clients, et fournissent une infrastructure et des fonctions communes pour donner aux clients l’accès à l’administration des entreprises, ainsi qu’aux logiciels tiers utilisés par nos clients. Ces fonctionnalités comprennent notamment la configuration des produits, la gestion des règles de pare-feu et la gestion des processus longs, comme les sauvegardes. »

> « Notre objectif est de fournir l’automatisation de 100 % des services d’administration des entreprises à nos clients. »

> — Carlo Kuip, architecte informatique

En outre, les services web SnelStart permettent à la fois aux clients et aux comptables d’accéder facilement aux données des pools élastiques Azure SQL Database. Ce modèle SaaS, associé à l’élasticité des bases de données et à Azure Resource Manager, offre à SnelStart des fonctionnalités d’évolutivité qui complètent chaque déploiement Azure. L’implémentation est entièrement automatisée grâce aux bibliothèques de gestion C#.

![La figure 1](./media/sql-database-case-study-snelstart/figure1.png)

Figure 1 : En juin 2016, SnelStart avait plus de 11 000 bases de données et plus de 50 pools de base de données élastiques
 
##Simplicité grâce au cloud

Depuis le passage à une solution cloud Azure, SnelStart a pu soutenir la croissance rapide des clients tout en offrant des services et fonctionnalités innovants. Selon Been, « Avec Azure, nous pouvons fournir des mises à jour à nos clients presque en continu, sans développer notre personnel des opérations. Et nous profitons de toutes les autres fonctionnalités importantes d’Azure, comme l’évolutivité et la récupération d’urgence, qui sont directement intégrées. »

> « Quand nous étions à Redmond... j’ai reçu un appel d’un développeur aux Pays-Bas, au sujet d’un problème spécifique. Nous avons pu faire en sorte que Microsoft apporte une modification à l’environnement de production dans les 48 heures afin de résoudre notre problème. »

> — Henry Been, architecte logiciel

SnelStart apprécie également l’étroit partenariat développé avec l’équipe Microsoft Azure SQL DB. Comme l’indique Kuip, « Nous discutons des fonctionnalités et de l’utilisation des technologies, ce que tout le monde apprécie. » L’objectif immédiat de SnelStart est de continuer à faire croître sa base de clients satisfaits. Comme l’affirme Been, « Sans les restrictions techniques et les limites de ressources que nous avions en tant qu’éditeur de logiciels indépendant, nos possibilités de développement ne connaissent plus de barrières. »


## Plus d’informations

- Pour plus d’informations sur les pools de base de données élastiques Azure, consultez [Pools de base de données élastiques](sql-database-elastic-pool.md).

- Pour plus d’informations sur les rôles Web et de travail, consultez [Rôles de travail](../fundamentals-introduction-to-azure.md#compute).

- Pour plus d’informations sur Azure SQL Data Warehouse, consultez [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)

- Pour plus d’informations sur SnelStart, consultez [SnelStart](http://www.snelstart.nl).

<!---HONumber=AcomDC_0921_2016-->