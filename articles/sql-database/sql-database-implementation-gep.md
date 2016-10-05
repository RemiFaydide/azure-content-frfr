<properties
   pageTitle="Étude de cas Azure SQL Database - GEP | Microsoft Azure"
   description="Découvrez comment GEP utilise SQL Database pour atteindre plus de clients à l’international et gagner en efficacité"
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

# Azure donne à GEP une portée mondiale et une plus grande efficacité

![Logo GEP](./media/sql-database-case-study-gep/geplogo.png)

GEP propose des logiciels et des services qui permettent aux leaders de l’approvisionnement dans le monde entier d’optimiser leur impact sur les opérations, les stratégies et les performances financières de leur entreprise. En complément des services de conseils et des services gérés, la société propose SMART by GEP®, une plateforme cloud de logiciels d’approvisionnement complète. Toutefois, elle s’est heurtée à des limites pour prendre en charge des solutions comme SMART by GEP avec ses propres centres de données locaux : les investissements requis étaient exorbitants, et les exigences réglementaires dans d’autres pays les auraient rendus plus colossaux encore. En passant au cloud, GEP a libéré des ressources informatiques, ce qui lui a permis de moins se soucier des opérations informatiques et de se concentrer davantage sur le développement de nouvelles sources de valeur ajoutée pour ses clients dans le monde entier.

## Développement de services et croissance avec Azure

Les clients de SMART by GEP apprécient les fonctionnalités de la plateforme et sa facilité d’utilisation ; ils peuvent gérer leurs processus partout, à tout moment et sur n’importe quel appareil (ordinateur portable, tablette ou téléphone). En passant à Microsoft Azure, GEP a pu gérer sa croissance rapide et son potentiel de développement sur de nouveaux marchés. Selon Dhananjay Nagalkar, vice-président à la technologie de GEP, « Microsoft Azure a joué un rôle clé dans la réussite de GEP en nous permettant de faire évoluer les services rapidement et avec agilité, et en fournissant des centres de données régionaux qui nous aident à répondre aux besoins de nos clients à travers le monde en matière de réglementation. »

## Les limites d’un centre de données maison

En 2013, les dirigeants de GEP ont reconnu qu’il leur fallait un moyen de s’assurer performances et évolutivité au fil de l’augmentation de la clientèle. Nagalkar explique : « Pour répondre à cette demande avec nos centres de données de l’époque, nous aurions dû considérablement développer notre infrastructure et nos ressources informatiques. L’investissement et le délai d’exécution nécessaires auraient été énormes. » Les machines physiques et virtuelles locales nécessitent une configuration, une gestion, une mise à l’échelle, des sauvegardes et des mises à jour correctives complètes à un rythme dont le coût aurait été prohibitif pour GEP. Les solutions cloud offrent, quant à elles, une simplicité et un confort d’utilisation qui ont permis à GEP de se concentrer davantage sur le développement et moins sur la gestion d’opérations informatiques colossales et toujours grandissantes. Nagalkar savait que GEP pourrait réduire sa charge d’achat, de configuration et de gestion de l’infrastructure en migrant vers le cloud.

GEP avait également besoin de surmonter les barrières réglementaires qui l’empêchaient d’entrer sur certains marchés internationaux. Pour de nombreux clients européens potentiels de GEP, la conformité aux réglementations passait par le stockage des données dans leur propre zone géographique. Mais il n’aurait pas été pratique pour GEP de mettre en place plusieurs centres de données. « L’extension des investissements d’infrastructure et des coûts de main-d’œuvre informatique aurait un impact significatif sur les marges, selon Nagalkar. Par conséquent, nous étions véritablement forcés de refuser les clients potentiels qui exigeaient que les données soient stockées localement. »

Nagalkar savait qu’une solution cloud pouvait apporter une solution à ce dilemme. En devenant un fournisseur cloud présent à l’international, GEP pourrait mieux répondre aux besoins de ses clients en matière de réglementations et de performances en stockant les données plus près des emplacements physiques des clients.

## Un ciel bleu derrière les nuages

Nagalkar et son équipe ont exploré différentes options de cloud, mais la plupart étaient des solutions IaaS (Infrastructure-as-a-Service) qui auraient demandé à GEP des investissements massifs en ressources informatiques pour configurer et gérer le service. La solution PaaS (Platform-as-a-Service) d’Azure s’est révélée beaucoup plus adaptée.

« Grâce à Azure, GEP n’a pas besoin de s’occuper de la gestion des bases de données, de la configuration des machines virtuelles, des mises à jour correctives ou d’autres tâches de gestion de l’infrastructure, indique Nagalkar. Nous pouvons plutôt consacrer nos ressources à ce que nous faisons le mieux : tirer parti de notre expertise en approvisionnement afin de développer pour nos clients des logiciels qui donnent vraiment des résultats. »

En fait, la migration vers Azure a permis à GEP de réduire son personnel opérationnel informatique tout en augmentant simultanément les fonctionnalités offertes aux clients.

En tirant parti des centres de données Azure dans le monde entier, GEP peut facilement étendre sa portée en Europe et en Asie. Ces centres de données internationaux permettent à GEP d’évoluer avec souplesse et de répondre aux besoins des clients en matière de stockage local des données, réduisant la latence et satisfaisant aux exigences réglementaires.

## L’architecture SMART by GEP

GEP a conçu SMART by GEP dès le départ dans Azure. Parmi les principales motivations de GEP figuraient l’évolutivité supérieure, les temps d’arrêt réduits et les coûts de maintenance moindres dont la société bénéficiait avec Azure SQL Database, par rapport à ce qu’elle pouvait atteindre en local. Toutefois, une fois passée au cloud, elle a découvert de nouvelles opportunités de développement dans le cloud, notamment le prototypage rapide et l’ingénierie lean, pour mieux répondre aux besoins des clients. Le développement dans Azure a permis à GEP de se débarrasser du casse-tête des licences logicielles que ses développeurs rencontraient parfois en local. Azure SQL Database est le cœur de SMART by GEP, bien que GEP utilise de nombreux autres services Azure pour continuer à améliorer facilement et rapidement SMART by GEP.

![La figure 1](./media/sql-database-case-study-gep/figure1.png) Figure 1. L’architecture SMART by GEP

## Données structurées

Au cœur de l’application SMART by GEP se trouvent les instances d’Azure SQL Database, qui soutiennent cette solution professionnelle de gestion de l’approvisionnement. Lorsqu’elle a conçu SMART by GEP, la société considérait Azure SQL Database comme la solution idéale pour son architecture, qui lui permettrait d’atteindre le niveau le plus élevé de protection des données et de satisfaire à ses exigences réglementaires. GEP utilise les diverses couches de protection des données qu’offre Azure SQL Database, notamment :

- Le chiffrement des données au repos avec Transparent Data Encryption.
- La sécurisation de l’authentification par l’intégration d’Azure SQL Database avec Azure Active Directory.
- La limitation de l’accès à un sous-ensemble approprié de données avec la sécurité au niveau des lignes.
- Le masquage des données en temps réel grâce à des stratégies.
- Le suivi des événements des bases de données grâce à l’audit Azure SQL Database.

> « Nous pouvons utiliser toutes ces options sans effectuer d’importantes modifications de code et avec un impact minimal sur les performances », affirme Nagalkar.

En utilisant Azure SQL Database, GEP dispose automatiquement de plus de fonctionnalités de récupération d’urgence qu’il ne lui aurait été possible d’en concevoir en local à bas coût, en raison des fonctionnalités de tolérance aux pannes intégrées à Azure SQL Database. GEP utilise la fonctionnalité de géo-réplication active d’Azure SQL Database, couplée avec plusieurs réplicas secondaires actifs, accessibles en lecture et en ligne (groupes de disponibilité AlwaysOn) dans différentes régions géographiques, pour former des paires à haute disponibilité. La réplication des données de SMART by GEP sur plusieurs régions signifie que, en cas de sinistre sur l’ensemble de la région, GEP peut facilement récupérer les données des clients avec un objectif de point de récupération (RPO) et un objectif de délai de récupération (RTO) minimaux.

Chaque client de SMART by GEP a deux instances Azure SQL Database : l’une pour le traitement transactionnel en ligne (OLTP) et l’autre pour l’analyse (par exemple, l’analyse des rapports et des dépenses des clients). Les pools de base de données élastiques Azure SQL Database permettent à GEP de gérer facilement des milliers de bases de données à travers le monde pour traiter des demandes imprévisibles de ressources de bases de données. Les pools élastiques fournissent à GEP un moyen de veiller à ce que les bases de données clients soient capables d’évoluer selon les besoins, sans approvisionnement excessif ou insuffisant, tout en lui permettant de contrôler les coûts. En outre, étant donné qu’il s’agit d’un service PaaS, la société obtient toutes les nouvelles fonctionnalités d’Azure SQL Database grâce à des mises à niveau automatiques.

## Données non structurées et semi-structurées

Toutefois, certaines données clients SMART by GEP requièrent un stockage structuré de façon moins rigide. Pour ce type de données, GEP utilise le Stockage Blob Azure, le Stockage Table Azure et le Cache Redis Azure. Le Stockage Blob Azure héberge toutes les pièces jointes que les utilisateurs de SMART by GEP chargent dans l’application. C’est également là que SMART by GEP stocke le contenu statique, notamment les feuilles de style en cascade (CSS) et les fichiers JavaScript.

GEP stocke les données non visibles par les clients, comme les données de journal SMART by GEP, dans le Stockage Table Azure, qui lui offre un stockage véritablement illimité et économique et des temps de récupération rapides, sans configuration d’un schéma pour les données. L’entreprise utilise le Cache Redis Azure comme cache principal.

## Authentification et routage

Azure Access Control Service (ACS) fournit aux utilisateurs de SMART by GEP un large éventail d’options de connexion au logiciel. Azure ACS peut se fédérer avec tous les fournisseurs d’identité qui échangent des données d’authentification avec Security Assertion Markup Language (SAML), notamment Active Directory Domain Services, Ping Identity, OneLogin et SiteMinder. Cela permet à GEP d’implémenter l’authentification unique (SSO) pour les clients sans avoir à stocker les informations d’identification des utilisateurs et à maintenir des stratégies de mot de passe des clients.

Une fois connectés, les clients ont accès aux ressources d’entreprise adaptées dans SMART by GEP. GEP utilise Azure Traffic Manager pour rediriger et équilibrer les demandes en provenance des appareils mobiles et des sessions de navigation des clients.

## Autres services Azure

GEP utilise d’autres services Azure pour adapter SMART by GEP aux attentes des clients. La société utilise les services cloud Azure (rôles Web et de travail) pour héberger la présentation des applications et les services sécurisés de logique métier. Les services cloud permettent aux développeurs de gérer l’infrastructure en tant que code (IAC) et de déployer de nouvelles applications SMART by GEP en bien moins de temps qu’avec les centres de données locaux, le tout sans l’intervention du service informatique. Les développeurs de GEP peuvent utiliser l’environnement intermédiaire des services cloud pour tester les nouvelles versions sans impact sur le déploiement en production. Une fois les tests effectués, GEP utilise les fonctionnalités d’échange d’adresses IP virtuelles des services cloud Azure pour déplacer le code intermédiaire vers l’emplacement de production en une minute, ce qui réduit le temps d’arrêt du déploiement.

Pour réduire la latence des applications, GEP utilise le réseau de distribution de contenu (CDN) Azure pour mettre le contenu statique stocké dans le Stockage Blob Azure (par exemple, les fichiers CSS et JavaScript) sur les serveurs Edge proches des utilisateurs. La société utilise Azure Service Bus pour prendre en charge les modèles d’architecture des applications allant de l’architecture publication-abonnement à l’architecture partiellement CQRS (ségrégation de la responsabilité des commandes et des requêtes) en passant par l’architecture hiérarchisée avec un couplage lâche et une communication asynchrone. Elle utilise Azure Media Services pour améliorer son service clientèle. Elle a constaté qu’elle pouvait publier facilement des vidéos d’assistance aux utilisateurs sur Azure Media Services. Ces vidéos répondent aux questions courantes des utilisateurs, ce qui permet d’améliorer la satisfaction des utilisateurs de SMART by GEP tout en allégeant en partie la charge de travail d’assistance du personnel de service clientèle de GEP.

Pour envoyer les milliers d’e-mails transactionnels générés quotidiennement par SMART by GEP, l’entreprise utilise l’API .NET SendGrid, qui s’intègre à Azure. Pour les développeurs de GEP, c’est très simple : le module complémentaire SendGrid pour Azure est disponible directement dans Azure Marketplace. Les développeurs de GEP peuvent configurer SMART by GEP à l’aide du package NuGet SendGrid directement dans Microsoft Visual Studio ; le service informatique de GEP peut surveiller le trafic de messagerie SendGrid du logiciel au sein d’Azure.

Enfin, SMART by GEP utilise les Machines virtuelles Azure (le service IaaS Azure) pour héberger les applications et les services qu’il n’était pas pertinent, au moment de la conception, de remplacer par des solutions SaaS (Software-as-a-Service) ou PaaS. Par exemple, GEP héberge les services API d’intégration dans des machines virtuelles pour l’intégration interentreprises (B2B) aux systèmes locaux de planification des ressources de l’entreprise (ERP) des clients comme SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP et Lawson, et aux solutions SaaS pour échanger efficacement des documents d’approvisionnement, par exemple des factures.

> « Le fait de concevoir SMART by GEP dans le cloud Microsoft Azure a supprimé complètement le besoin d’une informatique locale, non seulement pour GEP, mais également pour les opérations d’approvisionnement de nos clients. »

> Dhananjay Nagalkar, vice-président aux solutions technologiques

## Développer la satisfaction des clients sans développer les ressources informatiques

Depuis la migration des centres de données locaux vers Azure et la création intégrale de SMART by GEP sur la plateforme Azure, GEP a gagné en évolutivité et en flexibilité sans avoir à développer son infrastructure ou son personnel informatique. En fait, la société n’a pas ajouté de ressources informatiques depuis plus de quatre ans. Très pratique, le modèle PaaS d’Azure a permis à GEP de réduire ses dépenses de gestion des opérations et de support des fournisseurs. Par conséquent, GEP a pu concentrer ses ressources sur le développement de logiciels ; et le développement dans le cloud permet aux développeurs de GEP de tester rapidement de nouvelles idées sans devoir passer du temps à se coordonner avec le service informatique ou se préoccuper des licences locales requises. Azure SQL Database aide GEP à mieux veiller à ce que ses clients bénéficient toujours de performances et d’une qualité de service exceptionnelles.
 
## Plus d’informations

- Page d’accueil de GEP : [GEP](http://www.gep.com)
- SMART by GEP : [SMART by GEP](http://www.smartbygep.com)

##Contributeurs de GEP

- Huzaifa Matawala, directeur associé-architecte, GEP
- Sathyan Narasingh, responsable de l’ingénierie, GEP
- Deepa Velukutty, architecte de base de données, GEP

![Logo SMART](./media/sql-database-case-study-gep/smartbygeplogo.png)

![Logo GEP](./media/sql-database-case-study-gep/geplogo.png)

<!---HONumber=AcomDC_0921_2016-->