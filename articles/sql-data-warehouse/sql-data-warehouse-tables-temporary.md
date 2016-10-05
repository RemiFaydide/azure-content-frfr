<properties
   pageTitle="Tables temporaires dans SQL Data Warehouse | Microsoft Azure"
   description="Prise en main des tables temporaires dans Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Tables temporaires dans SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d'ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Les tables temporaires sont très utiles lors du traitement des données, notamment lors d’une transformation, lorsque les résultats intermédiaires sont temporaires. Les tables temporaires se trouvent au niveau de la session dans SQL Data Warehouse. Elles sont uniquement visibles dans la session dans laquelle elles ont été créées et sont automatiquement supprimées lorsque cette session se déconnecte. Les tables temporaires offrent un gain de performances, car leurs résultats sont écrits en local et non dans un stockage distant. Dans Azure SQL Data Warehouse, les tables temporaires diffèrent légèrement par rapport à la base de données SQL Azure, car elles sont accessibles à partir de tout point à l’intérieur de la session, notamment à l’intérieur et à l’extérieur d’une procédure stockée.

Cet article contient des conseils de base pour l’utilisation des tables temporaires et met en évidence les principes des tables temporaires au niveau de la session. L’utilisation des informations de cet article peut vous aider à modulariser votre code, et à améliorer sa réutilisabilité et sa facilité de maintenance.

## Créer une table temporaire

Les tables temporaires sont créées en faisant simplement précéder le nom de votre table de `#`. Par exemple :

```sql
CREATE TABLE #stats_ddl
(
	[schema_name]		NVARCHAR(128) NOT NULL
,	[table_name]            NVARCHAR(128) NOT NULL
,	[stats_name]            NVARCHAR(128) NOT NULL
,	[stats_is_filtered]     BIT           NOT NULL
,	[seq_nmbr]              BIGINT        NOT NULL
,	[two_part_name]         NVARCHAR(260) NOT NULL
,	[three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
```

Vous pouvez également utiliser `CTAS` pour créer des tables temporaires à l’aide de la même approche :

```sql
CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS` est une commande très puissante et présente l’avantage d’être très efficace dans son utilisation de l’espace de journal des transactions.


## Suppression de tables temporaires

Lorsqu’une nouvelle session est créée, aucune table temporaire ne doit exister. Toutefois, si vous appelez la même procédure stockée, qui crée une table temporaire avec le même nom, pour vous assurer de la réussite de vos instructions `CREATE TABLE`, une simple vérification d’existence préalable avec `DROP` peut être utilisée comme dans l’exemple ci-dessous :

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END
```

Pour la cohérence de codage, il convient d’utiliser ce modèle pour les tables et les tables temporaires. Il est également judicieux d’utiliser `DROP TABLE` pour supprimer les tables temporaires lorsque vous avez terminé de les utiliser dans votre code. Dans le développement de procédure stockée, il est assez courant de voir les commandes de suppression regroupées ensemble à la fin d’une procédure pour s’assurer que ces objets sont nettoyés.

```sql
DROP TABLE #stats_ddl
```

## Modularisation du code

Étant donné que les tables temporaires peuvent être affichées depuis n’importe quel point d’une session utilisateur, cela peut vous aider à modulariser votre code d’application. Par exemple, la procédure stockée ci-dessous réunit les pratiques recommandées ci-dessus pour générer le DDL qui va mettre à jour toutes les statistiques dans la base de données par nom de statistique.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
	,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

À ce stade, la seule action qui s’est produite est la création d’une procédure stockée qui va simplement générer une table temporaire, #stats\_ddl, avec des instructions DDL. Cette procédure stockée va abandonner la table #stats\_ddl si elle existe déjà pour assurer l’absence d’échec en cas d’exécution multiple dans une session. Toutefois, étant donné l’absence de `DROP TABLE` à la fin de la procédure stockée, lorsque la procédure stockée se termine, elle quitte la table créée afin de pouvoir être lue en dehors de la procédure stockée. Dans SQL Data Warehouse, contrairement à d’autres bases de données SQL, il est possible d’utiliser la table temporaire en dehors de la procédure qui l’a créée. Les tables temporaires SQL Data Warehouse peuvent être utilisées à **n’importe quel point** de la session. Cela peut optimiser la facilité de gestion et la modularité du code comme dans l’exemple ci-dessous :

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## Limitations relatives aux tables temporaires

SQL Data Warehouse impose quelques restrictions lors de l’implémentation de tables temporaires. Actuellement, seules les tables temporaires de la session sont prises en charge. Les tables temporaires globales ne sont pas prises en charge. En outre, vous ne pouvez pas créer de vues sur des tables temporaires.

## Étapes suivantes

Pour plus d’informations, consultez les articles [Table Overview][Overview] \(Vue d’ensemble des tables), [Table Data Types][Data Types] \(Types de données de table), [Distributing a Table][Distribute] \(Distribution d’une table), [Indexing a Table][Index] \(Indexation d’une table), [Partitioning a Table][Partition] \(Partitionnement d’une table) et [Maintaining Table Statistics][Statistics] \(Maintenance des statistiques de table). Pour en savoir plus sur les meilleures pratiques, consultez [Meilleures pratiques relatives à SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Vue d'ensemble]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Types de données]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Distribuer]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[Temporaire]: ./sql-data-warehouse-tables-temporary.md
[Meilleures pratiques relatives à SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0706_2016-->