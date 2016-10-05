<properties
   pageTitle="Types de données pour tables dans SQL Data Warehouse | Microsoft Azure"
   description="Prise en main des types de données pour les tables Azure SQL Data Warehouse."
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

# Types de données pour tables dans SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d'ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

SQL Data Warehouse prend en charge les types de données les plus couramment utilisés. Voici une liste des types de données pris en charge par SQL Data Warehouse. Pour plus d’informations sur la prise en charge du type de données, consultez la page [create table][] \(créer une table).

|**Types de données pris en charge**|||
|---|---|---|
[bigint][]|[decimal][]|[smallint][]|
[binary][]|[float][]|[smallmoney][]|
[bit][]|[int][]|[sysname][]|
[char][]|[money][]|[time][]|
[date][]|[nchar][]|[tinyint][]|
[datetime][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[real][]|[varbinary][]|
[datetimeoffset][]|[smalldatetime][]|[varchar][]|


## Meilleures pratiques relatives au type de données

 Lorsque vous définissez vos types de colonnes, l’utilisation du plus petit type de données prenant en charge vos données améliore les performances de requête. Ceci est particulièrement important pour les colonnes CHAR et VARCHAR. Si la valeur la plus longue dans une colonne est de 25 caractères, définissez la colonne en tant que VARCHAR(25). Évitez de définir toutes les colonnes de caractères sur une grande longueur par défaut. En outre, définissez des colonnes VARCHAR lorsque cela suffit, au lieu d’utiliser [NVARCHAR][]. Utilisez NVARCHAR(4000) ou VARCHAR(8000) lorsque c’est possible au lieu de NVARCHAR(MAX) ou VARCHAR(MAX).

## Limitation de Polybase

Si vous utilisez PolyBase pour charger vos tables, définissez ces dernières de sorte que la taille de ligne maximale (longueur totale des colonnes à longueur variable comprise) ne dépasse pas 32 767 octets. Vous pouvez définir des lignes incluant des données d’une longueur variable, susceptibles de dépasser cette largeur maximale, et charger des lignes avec BCP. Toutefois, vous ne pouvez pas utiliser PolyBase pour charger ces données. La prise en charge de PolyBase pour les lignes larges sera bientôt disponible.

## Types de données non pris en charge

Si vous migrez votre base de données à partir d’une autre plateforme SQL, comme une base de données SQL Azure, au cours de la migration, vous pouvez rencontrer certains types de données qui ne sont pas pris en charge sur SQL Data Warehouse. Voici les types de données non pris en charge, ainsi que certaines solutions que vous pouvez utiliser à la place des types de données non pris en charge.

|Type de données|Solution de contournement|
|---|---|
|[geometry][]|[varbinary][]|
|[Geography][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][](4000)|
|[image][ntext,text,image]|[varbinary][]|
|[text][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql\_variant][]|Fractionnez la colonne en plusieurs colonnes fortement typées.|
|[table][]|Appliquez une conversion vers des tables temporaires.|
|[timestamp][]|Modifiez le code afin d’utiliser le paramètre [datetime2][] et la fonction `CURRENT_TIMESTAMP`. Seules les constantes sont prises en charge en tant que valeurs par défaut, par conséquent le paramètre current\_timestamp ne peut pas être défini comme une contrainte par défaut. Si vous devez migrer les valeurs de version de ligne à partir d’une colonne de type horodatage, utilisez le paramètre [BINARY][](8) ou [VARBINARY][BINARY](8) pour les valeurs de version de ligne NOT NULL ou NULL.|
|[xml][]|[varchar][]|
|[types définis par l’utilisateur][]|appliquez à nouveau les types natifs, le cas échéant|
|valeurs par défaut|les valeurs par défaut prennent uniquement en charge des littéraux et des constantes. Les expressions ou fonctions non déterministes comme `GETDATE()` ou `CURRENT_TIMESTAMP` ne sont pas prises en charge.|

La requête SQL ci-dessous peut être exécutée sur votre base de données SQL actuelle pour identifier les colonnes qui ne sont pas prises en charge par Azure SQL Data Warehouse :

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## Étapes suivantes

Pour plus d’informations, consultez les articles [Table Overview][Overview] \(Vue d’ensemble des tables), [Distributing a Table][Distribute] \(Distribution d’une table), [Indexing a Table][Index] \(Indexation d’une table), [Partitioning a Table][Partition] \(Partitionnement d’une table), [Maintaining Table Statistics][Statistics] \(Maintenance des statistiques de table) et [Tables temporaires][Temporary]. Pour en savoir plus sur les meilleures pratiques, consultez [Meilleures pratiques relatives à SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Vue d'ensemble]: ./sql-data-warehouse-tables-overview.md
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

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql\_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[types définis par l’utilisateur]: https://msdn.microsoft.com/library/ms131694.aspx

<!---HONumber=AcomDC_0706_2016-->