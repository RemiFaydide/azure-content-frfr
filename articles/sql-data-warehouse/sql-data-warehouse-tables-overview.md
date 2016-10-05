<properties
   pageTitle="Vue d’ensemble des tables dans SQL Data Warehouse | Microsoft Azure"
   description="Prise en main des tables Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>

# Vue d’ensemble des tables dans SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d'ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

La prise en main de la création de tables dans SQL Data Warehouse est simple. La syntaxe [CREATE TABLE][] de base suit la syntaxe courante avec laquelle vous êtes certainement familiarisé grâce à votre travail sur d’autres bases de données. Pour créer une table, vous devez simplement nommer votre table, nommer vos colonnes et définir les types de données pour chaque colonne. Si vous avez déjà créé des tables dans d’autres bases de données, le processus vous semblera très familier.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

L’exemple ci-dessus crée une table nommée Customers avec deux colonnes, FirstName et LastName. Chaque colonne est définie avec un type de données de VARCHAR(25), ce qui limite les données à 25 caractères. Ces attributs fondamentaux d’une table, ainsi que d’autres, sont essentiellement les mêmes que ceux d’autres bases de données. Les types de données sont définis pour chaque colonne et garantissent l’intégrité de vos données. Les index peuvent être ajoutés pour améliorer les performances en réduisant les E/S. Le partitionnement peut être ajouté pour améliorer les performances lorsque vous avez besoin de modifier les données.

Le processus consistant à [renommer][RENAME] une table SQL Data Warehouse ressemble à ceci :

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## Tables distribuées

Un nouvel attribut fondamental introduit par les systèmes distribués comme SQL Data Warehouse est la **colonne de distribution**. La colonne de distribution porte bien son nom. C’est la colonne qui détermine comment distribuer ou diviser vos données en arrière-plan. Lorsque vous créez une table sans spécifier la colonne de distribution, la table est automatiquement distribuée à l’aide d’un **tourniquet (round robin)**. Bien que les tables de tourniquet (round robin) puissent être suffisantes dans certains scénarios, la définition des colonnes de distribution peut considérablement réduire le déplacement des données pendant les requêtes, optimisant ainsi les performances. Consultez [Distributing a Table][Distribute] \(Distribution d’une table) pour en savoir plus sur la sélection d’une colonne de distribution.

## Indexation et partitionnement des tables

Tandis que vous progressez dans votre utilisation de SQL Data Warehouse et que vous souhaitez optimiser les performances, vous souhaiterez en savoir plus sur la conception de tables. Pour plus d’informations, consultez les articles [Table Data Types][Data Types] \(Types de données de table), [Distributing a Table][Distribute] \(Distribution d’une table), [Indexing a Table][Index] \(Indexation d’une table) et [Partitioning a Table][Partition] \(Partitionnement d’une table).

## Statistiques de table

Les statistiques sont très importantes pour obtenir les meilleures performances de votre SQL Data Warehouse. Étant donné que, pour l’instant, SQL Data Warehouse ne crée pas de statistiques et ne les met pas à jour automatiquement pour vous, comme vous pouvez l’attendre dans la base de données SQL Azure, la lecture de notre article sur les [statistiques][] peut se révéler intéressante afin de garantir que vous tirez les meilleures performances de vos requêtes.

## Tables temporaires

Les tables temporaires sont des tables qui existent seulement pendant la durée de votre session et ne peuvent pas être vues par d’autres utilisateurs. Les tables temporaires peuvent constituer un bon moyen d’empêcher d’autres utilisateurs de voir les résultats temporaires et également de réduire la nécessité de nettoyage. Étant donné que les tables temporaires utilisent également le stockage local, elles peuvent offrir des performances plus rapides pour certaines opérations. Consultez les articles sur les [tables temporaires][Temporary] pour plus d’informations sur celles-ci.

## Tables externes

Les tables externes, également connues sous le nom de tables Polybase, sont des tables qui peuvent être interrogées à partir de SQL Data Warehouse, mais pointent vers des données externes à partir de SQL Data Warehouse. Par exemple, vous pouvez créer une table externe qui pointe vers les fichiers sur Azure Blob Storage. Pour plus d’informations sur la façon de créer et d’interroger une table externe, consultez [Charger des données avec PolyBase][].

## Fonctionnalités de table non prises en charge

Bien que SQL Data Warehouse contienne un grand nombre des fonctionnalités table offertes par d’autres bases de données, il existe certaines fonctionnalités qui ne sont pas encore prises en charge. Voici une liste de quelques-unes des fonctionnalités de table qui ne sont pas encore prises en charge.

| Fonctionnalités non prises en charge |
| --- |
|[Propriété Identity][] \(voir la solution de contournement [Affectation des clés de substitution][])|
|Clé primaire, clés étrangères, [contraintes de table][] uniques et de vérification|
|[Index uniques][]|
|[Colonnes calculées][]|
|[Colonnes éparses][]|
|[Types définis par l’utilisateur][]|
|[Séquence][]|
|[Déclencheurs][]|
|[Vues indexées][]|
|[Synonymes][]|

## Requêtes de taille de table

Un moyen simple d’identifier l’espace et les lignes consommées par une table dans chacune des 60 distributions, consiste à utiliser [DBCC PDW\_SHOWSPACEUSED][].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Toutefois, l’utilisation des commandes DBCC peut être très limitante. Les vues de gestion dynamique (DMV) vous permettront de voir beaucoup plus de détails, et vous offriront un contrôle supérieur sur les résultats de requête. Commencez par créer cette vue, à laquelle nombre de nos exemples dans cette section et dans d’autres articles feront référence.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### Résumé de l’espace de table

Cette requête renvoie les lignes et l’espace par table. C’est une requête intéressante pour voir quelles sont vos tables les plus volumineuses et si elles sont en tourniquet (round robin) ou à distribution par hachage. Pour les tables à distribution par hachage, elle montre également la colonne de distribution. Dans la plupart des cas, les plus grandes tables doivent être à distribution par hachage avec un index columnstore en cluster.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,	  distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,	  distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### Espace de table par type de distribution

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### Espace de table par type d’index

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### Résumé de l’espace de distribution

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## Étapes suivantes

Pour plus d’informations, consultez les articles [Types de données de table][Data Types], [Distribution d’une table][Distribute], [Indexation d’une table][Index], [Partitionnement d’une table][Partition], [Maintenance des statistiques de table][Statistics] et [Tables temporaires][Temporary]. Pour en savoir plus sur les meilleures pratiques, consultez [Meilleures pratiques relatives à SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Vue d'ensemble]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Types de données]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Distribuer]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporaire]: ./sql-data-warehouse-tables-temporary.md
[Meilleures pratiques relatives à SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Charger des données avec PolyBase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW\_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Propriété Identity]: https://msdn.microsoft.com/library/ms186775.aspx
[Affectation des clés de substitution]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[contraintes de table]: https://msdn.microsoft.com/library/ms188066.aspx
[Colonnes calculées]: https://msdn.microsoft.com/library/ms186241.aspx
[Colonnes éparses]: https://msdn.microsoft.com/library/cc280604.aspx
[Types définis par l’utilisateur]: https://msdn.microsoft.com/library/ms131694.aspx
[Séquence]: https://msdn.microsoft.com/library/ff878091.aspx
[Déclencheurs]: https://msdn.microsoft.com/library/ms189799.aspx
[Vues indexées]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonymes]: https://msdn.microsoft.com/library/ms177544.aspx
[Index uniques]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0810_2016-->