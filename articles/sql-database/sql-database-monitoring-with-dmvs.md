<properties
   pageTitle="Analyse de base de données SQL Azure à l’aide de vues de gestion dynamique | Microsoft Azure"
   description="Apprenez à détecter et à diagnostiquer des problèmes de performances courants à l’aide de vues de gestion dynamique pour surveiller une base de données SQL Microsoft Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# Analyse d’une base de données SQL Azure à l’aide de vues de gestion dynamique

La Base de données SQL Microsoft Azure active un sous-ensemble de vues de gestion dynamique permettant de diagnostiquer des problèmes de performances qui peuvent être causés par des requêtes bloquées ou longues, des goulots d’étranglement des ressources, des plans de requête médiocres, et ainsi de suite. Cette rubrique fournit des informations sur la façon de détecter des problèmes de performances courants à l’aide des vues de gestion dynamique.

La base de données SQL prend partiellement en charge trois catégories de vues de gestion dynamique :

- vues de gestion dynamique liées à la base de données ;
- vues de gestion dynamique liées à l’exécution ;
- vues de gestion dynamique liées à la transaction.

Pour plus d’informations sur les vues de gestion dynamique, voir [Fonctions et vues de gestion dynamique (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) dans la documentation en ligne de SQL Server.

## Autorisations

Dans la base de données SQL, l’interrogation d’une vue de gestion dynamique nécessite des autorisations **AFFICHER L'ÉTAT DE LA BASE DE DONNÉES**. L’autorisation **AFFICHER L'ÉTAT DE LA BASE DE DONNÉES** renvoie des informations sur tous les objets de la base de données active. Pour accorder l’autorisation **AFFICHER L'ÉTAT DE LA BASE DE DONNÉES** à un utilisateur de base de données spécifique, exécutez la requête suivante :

```GRANT VIEW DATABASE STATE TO database_user;```

Dans une instance de SQL Server local, des vues de gestion dynamiques renvoient des informations sur l’état du serveur. Dans Base de données SQL, elles renvoient des informations relatives à votre base de données logique actuelle.

## Calcul de la taille de la base de données

La requête suivante renvoie la taille de votre base de données en mégaoctets :

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

La requête suivante retourne la taille d’objets individuels (en mégaoctets) dans votre base de données :

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## Analyse des connexions

La vue [sys.dm\_exec\_connections](https://msdn.microsoft.com/library/ms181509.aspx) permet de récupérer des informations sur les connexions établies avec un serveur de base de données SQL Azure spécifique et les détails de chaque connexion. En outre, la vue [sys.dm\_exec\_sessions](https://msdn.microsoft.com/library/ms176013.aspx) permet de récupérer des informations sur toutes les connexions utilisateur et tâches internes actives. La requête suivante récupère des informations sur la connexion en cours :

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] Lorsque vous exécutez **sys.dm\_exec\_requests** et **sys.dm\_exec\_sessions views**, si vous disposez d’une autorisation **AFFICHER L’ÉTAT DE LA BASE DE DONNÉES** sur la base de données, vous voyez toutes les sessions en cours d’exécution sur la base de données. Sinon, vous voyez uniquement la session en cours.

## Analyse des performances des requêtes

Des requêtes lentes ou longues peuvent consommer des ressources système significatives. Cette section montre comment utiliser des vues de gestion dynamique pour détecter quelques problèmes courants liés aux performances de requête. L’article [Résolution des problèmes de performances dans SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) sur Microsoft TechNet constitue une référence plus ancienne, mais toujours utile, pour le dépannage.

### Recherche des N premières requêtes

L’exemple suivant renvoie des informations relatives aux cinq premières requêtes, classées sur la base du temps processeur moyen. Cet exemple agrège les requêtes selon leur hachage de requête et, logiquement, les requêtes équivalentes sont regroupées sur la base de leur consommation cumulée de ressources.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### Surveillance des requêtes bloquées

Des requêtes lentes ou longues peuvent contribuer à une consommation excessive de ressources et être la conséquence de requêtes bloquées. La cause du blocage peut être une conception médiocre d’application, de mauvais plans de requête, le manque d’index utiles, et ainsi de suite. La vue sys.dm\_tran\_locks permet d’obtenir des informations sur l’activité de verrouillage actuelle dans votre base de données SQL Azure. Pour un exemple de code, voir [sys.dm\_tran\_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) dans la documentation en ligne de SQL Server.

### Analyse des plans de requête

Un plan de requête inefficace peut également augmenter la consommation du processeur. L’exemple suivant utilise la vue [sys.dm\_exec\_query\_stats](https://msdn.microsoft.com/library/ms189741.aspx) pour déterminer quelle requête utilise le plus de temps processeur total.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## Voir aussi

[Présentation de la base de données SQL](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0921_2016-->