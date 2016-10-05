<properties
	pageTitle="Corriger une erreur de connexion SQL, erreur temporaire | Microsoft Azure"
	description="Découvrez comment diagnostiquer, résoudre et empêcher une erreur de connexion SQL ou une erreur temporaire dans Base de données SQL Azure. "
	keywords="connexion SQL,chaîne de connexion,problèmes de connectivité,erreur temporaire,erreur de connexion"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="daleche"/>


# Diagnostiquer, résoudre et empêcher les erreurs de connexion SQL et les erreurs temporaires de Base de données SQL

Cet article décrit comment empêcher, résoudre, diagnostiquer et limiter les erreurs de connexion et les erreurs temporaires que votre application cliente rencontre lorsqu’elle interagit avec Base de données SQL Azure. Découvrez comment configurer une logique de nouvelle tentative, générer la chaîne de connexion et ajuster les autres paramètres de connexion.

<a id="i-transient-faults" name="i-transient-faults"></a>

## Erreurs temporaires

Une erreur temporaire s’explique par une cause sous-jacente qui se résout d’elle-même en peu de temps. Les erreurs temporaires surviennent de temps en temps lorsque le système Azure réaffecte rapidement des ressources matérielles pour mieux équilibrer les différentes charges de travail. La plupart de ces événements de reconfiguration se terminent souvent en moins de 60 secondes. Durant cette reconfiguration, vous pouvez connaître des problèmes de connectivité avec Base de données SQL Azure. Les applications se connectant à la base de données SQL Azure doivent pouvoir tenir compte de ces erreurs temporaires, et les gérer en implémentant une logique de nouvelle tentative dans leur code au lieu de les exposer aux utilisateurs en cas d'erreurs d'application.

Si votre programme client utilise ADO.NET, votre programme est informé de l’erreur temporaire par la levée d’une exception **SqlException**. La propriété **Number** peut être comparée à la liste des erreurs temporaires au début de la rubrique : [Codes d’erreur SQL pour les applications clientes SQL Database](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### Connexion ou commande

Vous allez réessayer la connexion SQL ou la rétablir, en fonction de ce qui suit :

* **Une erreur temporaire survient pendant une tentative de connexion** : la connexion doit être retentée après un délai de quelques secondes.

* **Une erreur temporaire se produit pendant une commande de requête SQL** : cette dernière ne doit pas être retentée immédiatement. Au lieu de cela, après qu’un certain délai se soit écoulé, la connexion doit être établie. Ensuite, la commande peut être relancée.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### Logique de nouvelle tentative pour les erreurs temporaires


Les programmes clients qui rencontrent occasionnellement une erreur temporaire sont plus solides lorsqu’ils contiennent une logique de nouvelle tentative.


Si votre programme communique avec Base de données SQL Azure via un intergiciel (middleware) tiers, renseignez-vous auprès du fournisseur pour savoir s’il contient une logique de nouvelle tentative pour les erreurs temporaires.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### Principes de nouvelle tentative


- Une tentative d’ouverture de connexion doit être renouvelée si l’erreur est temporaire.


- Une instruction SQL SELECT qui échoue avec une erreur temporaire ne doit pas être retentée directement.
 - Au lieu de cela, établir une nouvelle connexion et relancer l’instruction SELECT.


- Si une instruction SQL UPDATE échoue avec une erreur temporaire, une nouvelle connexion doit être établie avant que l’instruction UPDATE ne soit retentée.
 - La logique de nouvelle tentative doit s’assurer que la transaction de base de données est complètement terminée ou que la transaction a été annulée.


#### Autres considérations


- Un programme de commandes démarré automatiquement après les heures de travail et qui s’achève avant le matin peut être très patient entre deux tentatives.


- Un programme d’interface utilisateur doit prendre en compte la tendance qu’ont les hommes à l’abandon en cas d’attente trop longue.
 - Toutefois, la solution ne consiste pas à retenter l’opération à chaque seconde, car une telle stratégie pourrait submerger le système de requêtes.


#### Augmentation de l’intervalle entre les tentatives



Nous vous recommandons de patienter 5 secondes avant votre première tentative. Si vous effectuez une nouvelle tentative avant 5 secondes, vous risquez de submerger le service cloud. Pour chaque nouvelle tentative, le délai doit augmenter de manière exponentielle, sans dépasser 60 secondes.

Pour en savoir plus sur la *période de blocage* des clients qui utilisent ADO.NET, consultez la page [Regroupement de connexions SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Vous pouvez également définir le nombre maximal de tentatives avant l’arrêt automatique du programme.


#### Exemples de code avec la logique de nouvelle tentative


Vous trouverez des exemples de code avec logique de nouvelle tentative dans divers langages de programmation ici :

- [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### Tester votre logique de nouvelle tentative


Pour tester la logique de nouvelle tentative, vous devez simuler ou provoquer une erreur pouvant être corrigée alors que votre programme est en cours d’exécution.


##### Test par le biais de la déconnexion du réseau


Pour tester votre logique de nouvelle tentative, vous pouvez déconnecter votre ordinateur client du réseau pendant l’exécution du programme. Vous obtiendrez l’erreur suivante :

- **SqlException.Number** = 11001
- Message : « Hôte inconnu »


Dans le cadre de la première nouvelle tentative, votre programme peut corriger les fautes d’orthographe et tenter de se connecter.


Pour concrétiser cela, vous pouvez débrancher votre ordinateur du réseau avant lancer votre programme. Votre programme reconnaît alors un paramètre d’exécution qui fait en sorte que le programme :

1. Ajoute temporairement 11001 à sa liste d’erreurs à considérer comme provisoire.
2. Tente sa première connexion comme d’habitude.
3. Une fois l’erreur détectée, supprime 11001 de sa liste.
4. Affiche un message indiquant à l’utilisateur de connecter l’ordinateur au réseau.
 - Suspend toute exécution à l’aide de la méthode **Console.ReadLine** ou d’une boîte de dialogue contenant un bouton OK. L’utilisateur appuie sur la touche Entrée après la connexion de l’ordinateur au réseau.
5. Essaie de nouveau de se connecter, et attend la réussite.


##### Teste en fournissant un nom de base de données mal orthographié au moment de la connexion


Votre programme peut délibérément mal orthographier le nom d’utilisateur avant la première tentative de connexion. Vous obtiendrez l’erreur suivante :

- **SqlException.Number** = 18456
- Message : « Échec de la connexion pour l’utilisateur 'WRONG\_MyUserName' »


Dans le cadre de la première nouvelle tentative, votre programme peut corriger les fautes d’orthographe et tenter de se connecter.


Pour mettre cela en pratique, votre programme peut reconnaître un paramètre d’exécution, qui fait en sorte que le programme :

1. Ajoute temporairement 18456 à sa liste d’erreurs à prendre en compte comme provisoire.
2. Ajoute volontairement ’WRONG\_’ au nom d’utilisateur.
3. Une fois l’erreur détectée, supprime 18456 de la liste.
4. Supprime ’WRONG\_’ du nom d’utilisateur.
5. Essaie de nouveau de se connecter, et attend la réussite.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### Paramètres de connexion .NET Sql pour les nouvelles tentatives de connexion


Si votre programme client se connecte à la base de données SQL Azure à l’aide de la classe .NET Framework **System.Data.SqlClient.SqlConnection**, vous devez utiliser .NET 4.6.1 ou une version ultérieure afin de pouvoir tirer parti de la fonctionnalité de nouvelle tentative de connexion. Les détails de la fonctionnalité sont disponibles [ici](http://go.microsoft.com/fwlink/?linkid=393996).


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Lorsque vous générez la [chaîne de connexion](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) pour votre objet **SqlConnection**, vous devez coordonner les valeurs entre les paramètres suivants :

- ConnectRetryCount &nbsp;&nbsp;*(La valeur par défaut est 1. La plage s’étend de 0 à 255.)*
- ConnectRetryInterval &nbsp;&nbsp;*(La valeur par défaut est 1 seconde. La plage s’étend de 1 à 60.)*
- Délai d’expiration de connexion &nbsp;&nbsp;*(La valeur par défaut est 15 secondes. La plage s’étend de 0 à 2 147 483 647.)*


Plus précisément, les valeurs que vous choisissez doivent vérifier la formule suivante :

- Délai d’expiration de connexion = ConnectRetryCount × ConnectionRetryInterval

Par exemple, si ConnectRetryCount = 3 et ConnectionRetryInterval = 10 secondes, un délai d’expiration de 29 secondes seulement ne laissera pas suffisamment de temps au système pour sa 3e et dernière tentative de connexion, 29 étant inférieur à 3 × 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### Connexion ou commande


Les paramètres **ConnectRetryCount** et **ConnectRetryInterval** permettent à votre objet **SqlConnection** de recommencer l’opération de connexion sans notification à votre programme ou renvoi du contrôle à celui-ci. Les nouvelles tentatives peuvent se produire dans les situations suivantes :

- Appel de méthode mySqlConnection.Open
- Appel de méthode mySqlConnection.Execute

Il existe une subtilité. Si une erreur temporaire se produit pendant l’exécution de votre *requête*, votre objet **SqlConnection** ne retente pas l’opération de connexion, et ne relance certainement pas votre requête. Toutefois, **SqlConnection** vérifie très rapidement la connexion avant d’envoyer votre requête pour exécution. Si la vérification rapide détecte un problème de connexion, **SqlConnection** réessaye l’opération de connexion. Si la nouvelle tentative réussit, votre requête est envoyée pour exécution.


#### Le paramètre ConnectRetryCount doit-il être combiné avec la logique de nouvelle tentative d’application ?

Supposons que votre application possède une logique de nouvelle tentative personnalisée robuste. Elle peut réessayer l’opération de connexion 4 fois. Si vous ajoutez **ConnectRetryInterval** et **ConnectRetryCount** = 3 à votre chaîne de connexion, vous augmentez le nombre de nouvelles tentatives à 4 × 3, soit 12 nouvelles tentatives. Vous ne souhaitez peut-être pas un si grand nombre de nouvelles tentatives.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## Connexion à la base de données SQL Azure

<a id="c-connection-string" name="c-connection-string"></a>

### Connexion : chaîne de connexion


La chaîne de connexion nécessaire à la connexion à la base de données SQL Azure est légèrement différente de celle qui sert à se connecter à Microsoft SQL Server. Il est possible de copier la chaîne de connexion de votre base de données à partir du [portail Azure](https://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### Connexion : adresse IP


Vous devez configurer le serveur de base de données SQL pour accepter les communications à partir de l’adresse IP de l’ordinateur qui héberge votre programme client. Pour ce faire, vous devez modifier les paramètres du pare-feu via le [portail Azure](https://portal.azure.com/).


Si vous oubliez de configurer l’adresse IP, votre programme échouera en envoyant un message d’erreur pratique indiquant l’adresse IP nécessaire.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Pour plus d’informations, consultez [Procédure : configuration des paramètres du pare-feu sur SQL Database](sql-database-configure-firewall-settings.md).


<a id="c-connection-ports" name="c-connection-ports"></a>

### Connexion : ports


En règle générale, vous devez simplement vous assurer que le port 1433 est ouvert pour la communication sortante sur l’ordinateur qui héberge le programme client.


Par exemple, lorsque votre programme client est hébergé sur un ordinateur Windows, le pare-feu Windows sur l’ordinateur hôte vous permet d’ouvrir le port 1433 :


1. Ouvrir le panneau de configuration
2. &gt; Tous les éléments du Panneau de configuration
3. &gt; Pare-feu Windows
4. &gt; Paramètres avancés
5. &gt; Règles de trafic sortant
6. &gt; Actions
7. &gt; Nouvelle règle


Si votre programme client est hébergé sur une machine virtuelle Azure, consultez <br/>[Ports au-delà de 1433 pour ADO .NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).


Pour obtenir des informations générales sur la configuration des ports et l’adresse IP, voir [Pare-feu Azure SQL Database](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### Connexion : ADO.NET 4.6.1


Si votre programme utilise des classes ADO.NET comme **System.Data.SqlClient.SqlConnection** pour se connecter à la base de données SQL Azure, nous vous recommandons d’utiliser .NET Framework version 4.6.1 ou ultérieure.


ADO.NET 4.6.1 :

- Pour la base de données SQL Azure, l’ouverture d’une connexion à l’aide de la méthode **SqlConnection.Open** garantit une meilleure fiabilité. La méthode **Open** intègre désormais de meilleurs mécanismes de nouvelle tentative en réponse à des défaillances temporaires, pour certaines erreurs se produisant avant le délai d’expiration de la connexion.
- Prend en charge le regroupement de connexions. Cela contribue à garantir que l’objet de connexion qu’il donne à votre programme fonctionne.



Lorsque vous utilisez un objet de connexion à partir d’un pool de connexions, nous conseillons de faire en sorte que votre programme interrompe temporairement la connexion lorsque vous ne l’utilisez pas immédiatement. La réouverture d’une connexion n’est pas aussi coûteuse que la création d’une nouvelle connexion.


Si vous utilisez ADO.NET 4.0 ou une version antérieure, nous vous recommandons d’effectuer une mise à niveau vers la dernière version d’ADO.NET.

- Depuis novembre 2015, [ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx) est disponible au téléchargement.


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## Diagnostics

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### Diagnostic : vérifier si les utilitaires peuvent se connecter


Si votre programme ne peut pas se connecter à la base de données SQL Azure, une option de diagnostic consiste à essayer de se connecter à un programme utilitaire. Dans l’idéal, l’utilitaire se connecte à l’aide de la bibliothèque que votre programme utilise.


Sur un ordinateur Windows, vous pouvez essayer ces utilitaires :

- SQL Server Management Studio (ssms.exe), qui se connecte à l’aide d’ADO.NET.
- sqlcmd.exe, qui se connecte à l’aide d’[ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Une fois connecté, faites un test avec une courte requête SQL SELECT.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### Diagnostic : vérifier les ports ouverts


Supposons que vous soupçonnez que les tentatives de connexion échouent en raison de problèmes de port. Depuis votre ordinateur, vous pouvez exécuter un utilitaire qui génère des rapports sur les configurations de port.


Sous Linux, les utilitaires suivants peuvent vous être utiles :

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - (Modifiez la valeur de l’exemple pour refléter votre adresse IP).


Sous Windows, l’utilitaire [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) peut s’avérer utile. Voici une exécution de l’exemple qui demandé l’état d’un port sur un serveur de base de données SQL Azure, et qui a été exécuté sur un ordinateur portable :


```
[C:\Users\johndoe]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### Diagnostic : consignation des erreurs dans un journal


Un problème intermittent est parfois mieux diagnostiqué par la détection d’une tendance générale observée sur plusieurs jours ou semaines.


Votre client peut aider à consigner toutes les erreurs qu’il rencontre un diagnostic. Vous pourrez mettre en corrélation les entrées de journal avec des informations sur les erreurs de base consignées en interne par la base de données SQL Azure elle-même.


Enterprise Library 6 (EntLib60) offre des classes .NET gérées afin de faciliter la journalisation :

- [5 - Un jeu d’enfants : utilisation du bloc d’application de journalisation](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### Diagnostics : examinez les journaux d’erreur système


Voici certaines instructions Transact-SQL SELECT qui permettent d’interroger les journaux d’erreur et d’autres informations.


| Interrogation de journaux | Description |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | La vue [sys.event\_log](http://msdn.microsoft.com/library/dn270018.aspx) affiche des informations sur des événements individuels, notamment sur les événements à l’origine d’erreurs temporaires ou de problèmes de connectivité.<br/><br/>Dans l’idéal, vous pouvez établir une corrélation entre les valeurs **start\_time** ou **end\_time** et les données relatives au moment où votre programme client a rencontré des problèmes.<br/><br/>**CONSEIL :** vous devez vous connecter à la base de données **MASTER** pour exécuter cet exemple. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | L’écran [sys.database\_connection\_stats](http://msdn.microsoft.com/library/dn269986.aspx) agrège des nombres de différents types d’événements, pour permettre des diagnostics supplémentaires.<br/><br/>**CONSEIL :** vous devez vous connecter à la base de données **MASTER** pour exécuter cet exemple. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### Diagnostics : Rechercher les événements liés aux problèmes dans le journal de base de données SQL


Vous pouvez rechercher des entrées sur les problèmes survenus dans le journal de base de données SQL Azure. Essayez l’instruction Transact-SQL SELECT qui suit dans la base de données **MASTER** :


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### quelques-unes d’entre elles ont renvoyé des lignes de sys.fn\_xe\_telemetry\_blob\_target\_read\_file


Vous trouverez plus loin une ligne renvoyée qui ressemble à ce qui suit . Les valeurs null indiquées ne sont en général pas nulles dans d’autres lignes.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## Enterprise Library 6


Enterprise Library 6 (EntLib60) est une infrastructure de classes .NET qui vous permet d’implémenter des clients de cloud fiables, et notamment le service Azure SQL Database. Vous pouvez rechercher des rubriques dédiées à chaque zone dans laquelle EntLib60 peut être utile en visitant dans un premier temps :

- [Enterprise Library 6 – avril 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


La logique de nouvelle tentative pour la gestion des erreurs temporaires est un domaine où EntLib60 peut être utile :

- [4 - La persévérance, le secret de la réussite : utilisation du bloc applicatif de gestion des erreurs temporaires](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


> [AZURE.NOTE] Le code source pour EntLib60 est disponible au public en [téléchargement](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft ne prévoit pas d’apporter des mises à jour de maintenance ou de fonctionnalité supplémentaires à EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### Classes EntLib60 pour les erreurs temporaires et les nouvelles tentatives


Les classes EntLib60 suivantes sont particulièrement utiles pour la logique de nouvelle tentative. Toutes se trouvent dans ou sous l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** :

*Dans l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** :*

- Classe **RetryPolicy**
 - Méthode **ExecuteAction**


- Classe **ExponentialBackoff**


- Classe **SqlDatabaseTransientErrorDetectionStrategy**.


- Classe **ReliableSqlConnection**
 - Méthode **ExecuteCommand**


Dans l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport** :

- Classe **AlwaysTransientErrorDetectionStrategy**

- Classe **NeverTransientErrorDetectionStrategy**


Voici des liens vers des informations sur EntLib60 :

- [Livre électronique : Guide du développeur de Microsoft Enterprise Library, 2e édition](http://www.microsoft.com/download/details.aspx?id=41145) gratuit.

- Meilleures pratiques : [Conseils généraux sur les nouvelles tentatives](../best-practices-retry-general.md) comprend une excellente présentation approfondie de la logique de nouvelle tentative.

- Téléchargement NuGet de [Bibliothèque d’entreprise - Bloc applicatif de gestion des erreurs 6.0 temporaires de Microsoft](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### EntLib60 : le bloc de journalisation


- Le bloc de journalisation est une solution très flexible et configurable qui vous permet de :
 - Créer et stocker des messages du journal dans de nombreux emplacements.
 - Classer et filtrer les messages.
 - Recueillir des informations contextuelles utiles pour le débogage et le suivi, ainsi que pour les exigences d’audit et de journalisation en général.


- Le bloc de journalisation extrait les fonctionnalités issues de la destination de journalisation de façon que le code d’application soit cohérent, quels que soient l’emplacement et le type du magasin de journalisation cible.


Pour plus de détails, consultez [5 - Un jeu d’enfants : utilisation du bloc d’application de journalisation](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### Code source de la méthode EntLib60 IsTransient


Ensuite, à partir de la classe **SqlDatabaseTransientErrorDetectionStrategy**, le code source C# pour la méthode **IsTransient**. Le code source clarifie les erreurs considérées comme temporaires qui peuvent faire l’objet de nouvelles tentatives depuis avril 2013.

De nombreuses lignes **//comment** ont été supprimées de cette copie pour en augmenter la lisibilité.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## Étapes suivantes

- Pour résoudre les autres problèmes de connexion courants à Azure SQL Database, consultez [Résolution des problèmes de connexion à Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md).

- [Regroupement de connexions SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Nouvelle tentative* est une bibliothèque de nouvelle tentative sous licence Apache 2.0 à usage général écrite en langage **Python**, pour simplifier la tâche consistant à ajouter des comportements de nouvelle tentative dans toutes les situations.](https://pypi.python.org/pypi/retrying)

<!---HONumber=AcomDC_0921_2016-->