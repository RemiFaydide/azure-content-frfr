<properties
	pageTitle="Créer un pool élastique avec le portail Azure | Microsoft Azure"
	description="Comment ajouter un pool de base de données élastique évolutive à votre configuration de base de données SQL pour faciliter l’administration et le partage des ressources entre plusieurs bases de données."
	keywords="base de données évolutive, configuration de la base de données"
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/20/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Créer un pool de base de données élastique avec le portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)

Cet article vous montre comment créer un [pool de base de données élastique](sql-database-elastic-pool.md) évolutif avec le [portail Azure](https://portal.azure.com/). Vous pouvez créer un pool de deux façons. Vous pouvez le faire à partir de zéro si vous connaissez la configuration de pool souhaitée ou si vous commencez par une recommandation issue du service. La base de données SQL est dotée d’une intelligence intégrée qui recommande une configuration de pool si cela semble plus économique pour vous en fonction de la dernière télémétrie d’utilisation de vos bases de données.

Vous pouvez ajouter plusieurs pools à un serveur, mais il est impossible d’ajouter des bases de données de différents serveurs dans le même pool. Pour créer un pool, vous devez disposer au moins d’une base de données sur un serveur V12. Si vous en êtes dépourvu, consultez l’article [Créer votre première base de données SQL Azure](sql-database-get-started.md). Vous pouvez créer un pool avec une seule base de données, mais les pools ne sont rentables que s’ils comportent plusieurs bases de données. Consultez l’article [Considérations sur les prix et performances pour un pool de bases de données élastique](sql-database-elastic-pool-guidance.md).

> [AZURE.NOTE] Les pools élastiques sont mis à la disposition générale dans toutes les régions Azure, à l’exception de l’Inde de l’Ouest, où ils sont actuellement en préversion. Les pools élastiques seront en disposition générale dès que possible dans cette région.

## Étape 1 : créer un nouveau pool

Cet article explique comment créer un pool à partir d’un panneau **Serveur** existant dans le portail. C’est le moyen le plus simple pour déplacer des bases de données existantes dans un pool.

> [AZURE.NOTE] Que vous disposiez d’un serveur ou non, vous pouvez également créer un pool à partir du panneau **Pools élastiques SQL** (sous la liste du côté gauche du portail, cliquez sur **Parcourir** **>** **Pools élastiques SQL**). Cliquez sur **+ Ajouter** sur le panneau **Pools élastiques SQL** pour créer un serveur pendant le workflow d’approvisionnement du pool.

1. Dans le [portail Azure](http://portal.azure.com/), sous la liste du côté gauche, cliquez sur **Parcourir** **>** **Serveurs SQL**, puis sur le serveur qui contient les bases de données que vous souhaitez ajouter à un pool.
2. Cliquez sur **Nouveau pool**.

    ![Ajouter un pool à un serveur](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **OU**

    Il se peut qu’un message indiquant que des pools de base de données élastiques recommandés sont disponibles pour le serveur (V12 uniquement) s’affiche. Cliquez sur le message pour afficher les pools recommandés en fonction de la télémétrie de l’historique d’utilisation de base de données, puis cliquez sur le niveau pour afficher plus de détails et personnaliser le pool. Consultez la section [Comprendre les recommandations relatives au pool](#understand-pool-recommendations) plus loin dans cette rubrique pour savoir sur quoi repose la recommandation.

    ![pool recommandé](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    Le panneau **Pool de base de données élastique** s’affiche. Il correspond à l’emplacement où vous allez configurer le pool. Si vous avez cliqué sur **Nouveau pool** à l’étape précédente, le portail choisit un **Pool standard** sous **Niveau tarifaire**, ainsi qu’un **Nom** unique et une configuration par défaut pour le pool. Si vous avez choisi un pool recommandé, le niveau et la configuration recommandés du pool sont déjà sélectionnés, mais vous pouvez toujours les modifier.

    ![Configurer un pool élastique](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. Entrez un nom pour le pool élastique, ou conservez la valeur par défaut.

## Étape 2 : sélectionner un niveau tarifaire

Le niveau de tarification du pool détermine les fonctionnalités disponibles pour les bases de données élastiques dans le pool, ainsi que le nombre maximal d'eDTU (eDTU MAX) et le stockage (Go) disponibles pour chaque base de données. Pour plus d’informations, voir Niveaux de service.

Pour modifier le niveau tarifaire du pool, cliquez sur **Niveau tarifaire**, sur le niveau tarifaire de votre choix, puis sur **Sélectionner**.

> [AZURE.IMPORTANT] Une fois que vous avez choisi le niveau tarifaire et validé vos modifications en cliquant sur **OK** à la dernière étape, vous ne pouvez plus modifier le niveau tarifaire du pool. Pour modifier le niveau de tarification d’un pool élastique existant, créez un nouveau pool élastique dans le niveau de tarification souhaité et migrez les bases de données élastiques vers ce nouveau pool.

![Sélectionner un niveau de tarification](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

## Étape 3 : configurer le pool

Après avoir défini le niveau de tarification, cliquez sur Configurer le pool à l’endroit où vous ajoutez des bases de données, définissez la quantité d’eDTU et de stockage (Go de pool), et définissez les nombres minimum et maximum d’eDTU des bases de données élastiques dans le pool.

1. Cliquez sur **Configurer le pool**
2. Sélectionnez les bases de données que vous souhaitez ajouter au pool. Cette étape est facultative lors de la création du pool. Les bases de données peuvent être ajoutées après la création du pool. Pour ajouter des bases de données, cliquez successivement sur **Ajouter une base de données**, les bases de données que vous souhaitez ajouter, et le bouton **Sélectionner**.

    ![Ajouter des bases de données](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Si les bases de données que vous utilisez disposent de données de télémétrie d’historique d’utilisation suffisantes, le graphique **Utilisation estimée des eDTU et des Go** et le graphique à barres **Utilisation effective des eDTU ** sont mis à jour pour vous aider à prendre des décisions en termes de configuration. Le service peut également vous envoyer un message de recommandation pour vous aider à rectifier la taille du pool. Voir [Recommandations dynamiques](#dynamic-recommendations).

3. Utilisez les contrôles de la page **Configurer le pool** pour explorer les paramètres et configurer votre pool. Pour plus d’informations sur les limites de chaque niveau de service, reportez-vous aux [limites de pools élastiques](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases). Pour obtenir des conseils détaillés sur la définition d’une taille de pool sur mesure, consultez les [considérations sur les prix et performances pour un pool de base de données élastique](sql-database-elastic-pool-guidance.md). Pour plus d’informations sur les paramètres du pool, consultez [Propriétés du pool de bases de données élastiques](sql-database-elastic-pool.md#elastic-database-pool-properties).

	![Configurer un pool élastique](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. Cliquez sur **Sélectionner** dans le panneau **Configurer le pool** après avoir modifié des paramètres.
5. Cliquez sur **OK** pour créer le pool.


## Comprendre les recommandations relatives au pool

Le service SQL Database évalue l’historique d’utilisation et recommande un ou plusieurs pools lorsque cela est plus rentable que d’utiliser des bases de données uniques. Chaque recommandation est configurée avec un sous-ensemble unique de bases de données du serveur qui correspond le mieux au pool.

![pool recommandé](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

La recommandation relative au pool comprend les éléments suivants :

- Niveau de tarification du pool (De base, Standard ou Premium)
- Valeur **eDTU du pool** appropriée (également appelée eDTU max par pool)
- Paramètres **eDTU max** et **eDTU min** par base de données
- Liste des bases de données recommandées pour le pool

Le service prend en compte les 30 derniers jours de télémétrie lors de la recommandation de pools. Pour qu’une base de données soit considérée comme candidate à un pool élastique de données, elle doit exister depuis au moins 7 jours. Les bases de données qui se trouvent déjà dans un pool élastique de bases de données ne sont pas considérées comme candidates pour les recommandations de pool élastique de bases de données.

Le service évalue les besoins en ressources et la rentabilité du déplacement des bases de données uniques dans chaque niveau de service vers des pools du même niveau. Par exemple, toutes les bases de données Standard d’un serveur sont évaluées pour leur compatibilité avec un pool élastique Standard. Cela signifie que le service n'effectue aucune recommandation multiniveau telle que le déplacement d'une base de données Standard dans un pool Premium.

### Recommandations dynamiques

Après avoir ajouté des bases de données au pool, des recommandations seront générées de façon dynamique en fonction de l’historique d’utilisation des bases de données que vous avez sélectionnées. Ces recommandations seront affichées dans le graphique d’utilisation d’eDTU et de Go ainsi que dans la bannière de recommandation en haut du panneau **Configurer le pool**. Ces recommandations sont destinées à vous aider à créer un pool optimisé pour vos bases de données spécifiques.

![recommandations dynamiques](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## Ressources supplémentaires

- [Gérer un pool élastique de base de données SQL avec le portail](sql-database-elastic-pool-manage-portal.md)
- [Gérer un pool élastique de base de données SQL avec PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [Gérer un pool élastique de base de données SQL en C#](sql-database-elastic-pool-manage-csharp.md)
- [Montée en charge avec Base de données SQL Azure](sql-database-elastic-scale-introduction.md)

<!---HONumber=AcomDC_0928_2016-->