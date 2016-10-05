<properties
   pageTitle="Se connecter à Azure SQL Data Warehouse | Microsoft Azure"
   description="Comment trouver le nom de serveur et la chaîne de connexion pour Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# Se connecter à Azure SQL Data Warehouse

Cet article vous aidera à établir votre première connexion à SQL Data Warehouse.

## Recherche du nom de serveur

La première étape de connexion à SQL Data Warehouse consiste à déterminer le nom de votre serveur. Dans l’exemple suivant, le nom du serveur est sample.database.windows.net. Pour rechercher le nom complet du serveur :

1. Accédez au [portail Azure][].
2. Cliquez sur **Bases de données SQL**.
3. Cliquez sur la base de données à laquelle vous souhaitez vous connecter.
4. Recherchez le nom complet du serveur.

    ![Nom complet du serveur][1]

## Chaînes de connexion et pilotes pris en charge

Azure SQL Data Warehouse prend en charge [ADO.NET][], [ODBC][], [PHP][] et [JDBC][]. Cliquez sur l’un des pilotes ci-dessus pour rechercher la dernière version et accéder à la documentation connexe. Pour générer automatiquement la chaîne de connexion pour le pilote que vous utilisez à partir du portail Azure, vous pouvez cliquer sur l’option **Afficher les chaînes de connexion de la base de données** dans l’exemple précédent. Voici également quelques exemples montrant à quoi ressemble une chaîne de connexion pour chaque pilote.

> [AZURE.NOTE] Vous pouvez définir le délai de connexion à 300 secondes pour permettre à votre connexion de résister à des courtes périodes d’indisponibilité.

### Exemple de chaîne de connexion ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Exemple de chaîne de connexion ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Exemple de chaîne de connexion PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Exemple de chaîne de connexion JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Paramètres de connexion

SQL Data Warehouse standardise certains paramètres pendant la connexion et la création d’objets. Ces paramètres, qui ne peuvent pas être remplacés, comprennent notamment :

| Paramètre de base de données | Valeur |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ACTIVÉ |
| [QUOTED\_IDENTIFIERS][] | ACTIVÉ |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## Étapes suivantes

Pour vous connecter et interroger avec Visual Studio, consultez la page [Interroger Azure SQL Data Warehouse (sqlcmd) (Visual Studio)][]. Pour en savoir plus sur les options d’authentification, consultez la page [Authentification sur Azure SQL Data Warehouse][].

<!--Articles-->
[Interroger Azure SQL Data Warehouse (sqlcmd) (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Authentification sur Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[portail Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0928_2016-->