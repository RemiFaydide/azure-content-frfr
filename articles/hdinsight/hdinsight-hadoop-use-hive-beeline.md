<properties
   pageTitle="Utiliser Beeline avec Hive sur HDInsight (Hadoop) | Microsoft Azure"
   description="Découvrez comment utiliser SSH pour vous connecter à un cluster Hadoop dans HDInsight, puis envoyer de manière interactive des requêtes Hive à l’aide de Beeline. Beeline est un utilitaire qui permet d’utiliser HiveServer2 sur JDBC."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>

#Utilisation de Hive avec Hadoop dans HDInsight via Beeline

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous découvrirez comment utiliser Secure Shell (SSH) pour vous connecter à un cluster Azure HDInsight Linux, puis envoyer ensuite de façon interactive des requêtes Hive à l’aide de l’outil de ligne de commande [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell).

> [AZURE.NOTE] Beeline utilise JDBC pour la connexion à Hive. Pour plus d’informations sur l’utilisation de JDBC avec Hive, consultez la section [Se connecter à Hive sur Azure HDInsight à l’aide du pilote Hive JDBC](hdinsight-connect-hive-jdbc-driver.md).

##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop Linux sur HDInsight.

* Un client SSH. Mac OS, Linux et Unix doivent être accompagnés d’un client SSH. Les utilisateurs Windows doivent télécharger un client, comme [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Connexion avec SSH

Connectez-vous au nom de domaine complet de votre cluster HDInsight à l’aide de la commande SSH. Le nom de domaine complet est le nom attribué au cluster, suivi de **.azurehdinsight.net**. Par exemple, la commande suivante permettrait de se connecter à un cluster nommé **myhdinsight** :

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez spécifier l’emplacement de la clé privée sur votre système client :

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si vous avez fourni un mot de passe pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez fournir le mot de passe lorsque vous y êtes invité.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez la rubrique [Utilisation de SSH avec Hadoop dans HDInsight sur Linux à partir de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (clients Windows)

Windows ne fournit pas de client SSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, consultez la rubrique [Utilisation de SSH avec Hadoop Linux dans HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="beeline"></a>Utilisez la commande Beeline

1. Une fois connecté, procédez comme suit pour démarrer Beeline :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Cette action démarre le client Beeline et assure la connexion à l’url JDBC. Ici, `localhost` est utilisé car HiveServer2 s’exécute sur les deux nœuds principaux du cluster et nous utilisons Beeline directement sur le nœud principal primaire.
    
    Une fois la commande terminée, vous arriverez à une invite `jdbc:hive2://localhost:10001/>`.

3. Les commandes Beeline commencent généralement par un caractère `!`. Par exemple, `!help` affiche l’aide. Toutefois, le `!` peut souvent être omis. Par exemple, `help` fonctionne également.

    Si vous affichez l’aide, vous remarquerez `!sql`, qui est utilisé pour exécuter des instructions HiveQL. Cependant, HiveQL est si souvent utilisé que vous pouvez omettre le `!sql` qui précède. Les deux instructions suivantes donnent exactement les mêmes résultats, et affichent les tables actuellement disponibles via Hive :
    
        !sql show tables;
        show tables;
    
    Sur un nouveau cluster, une seule table doit être répertoriée : __hivesampletable__.

4. Utilisez l’élément suivant pour afficher le schéma correspondant à hivesampletable :

        describe hivesampletable;
        
    La commande retournera les informations suivantes :
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Elle affiche les colonnes de la table. Nous pourrions exécuter des requêtes sur ces données, mais nous allons créer à la place une toute nouvelle table pour montrer comment charger des données dans Hive et appliquer un schéma.
    
5. Saisissez les instructions suivantes pour créer une table nommée **log4jLogs** avec les exemples de données fournies avec le cluster HDInsight :

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.
    * **CREATE EXTERNAL TABLE** : crée une table « externe » dans Hive. Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l'emplacement d'origine.
    * **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.
    * **STORED AS TEXTFILE LOCATION** : indique à Hive où sont stockées les données (répertoire example/data) et qu'elles sont stockées sous forme de texte.
    * **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette commande doit retourner la valeur **3**, car trois lignes contiennent cette valeur.
    * **INPUT\_\_FILE\_\_NAME LIKE ’%.log’** : indique à Hive de renvoyer uniquement des données provenant de fichiers se terminant par .log. Normalement, vous devriez obtenir uniquement les données avec le même schéma dans le même dossier lors de l’interrogation avec hive, toutefois, cet exemple de fichier journal est stocké avec d’autres formats de données.

    > [AZURE.NOTE] Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, ou par une autre opération MapReduce, mais souhaitez toujours que les requêtes Hive utilisent les données les plus récentes.
    >
    > La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de table.
    
    Le résultat de cette commande doit ressembler à ceci :
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. Pour quitter Beeline, utilisez `!quit`.

##<a id="file"></a>Exécuter un fichier HiveQL

Beeline peut également être utilisé pour exécuter un fichier contenant les instructions HiveQL. Utilisez les étapes suivantes pour créer un fichier, puis exécutez-le à l’aide de Beeline.

1. Utilisez la commande suivante pour créer un fichier nommé __query.hql__ :

        nano query.hql
        
2. Lorsque l’éditeur s’ouvre, utilisez les données suivantes comme contenu du fichier : Cette requête crée une nouvelle table « interne » nommée **errorLogs** :

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ces instructions effectuent les opérations suivantes :

    * **CREATE TABLE IF NOT EXISTS** : crée une table, si elle n'existe pas déjà. Le mot-clé **EXTERNAL** n’étant pas utilisé, il s’agit d’une table interne, stockée dans l’entrepôt de données Hive et gérée intégralement par Hive.
    * **STORED AS ORC** : stocke les données au format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
    * **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contiennent **[ERROR]**, puis insère les données dans la table **errorLogs**.
    
    > [AZURE.NOTE] Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.
    
3. Pour enregistrer le fichier, utilisez __Ctrl__+__\_X__, saisissez ensuite _Y_, puis appuyez sur __Entrée__.

4. Pour exécuter le fichier à l’aide de Beeline, utilisez les éléments suivants. Remplacez __HOSTNAME__ par le nom obtenu précédemment pour le nœud principal, et __PASSWORD__ par le mot de passe du compte d’administration :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql

    > [AZURE.NOTE] Le paramètre `-i` démarre Beeline, exécute les instructions dans le fichier query.hql et reste dans Beeline à l’invite `jdbc:hive2://localhost:10001/>`. Vous pouvez également exécuter un fichier en utilisant le paramètre `-f`, qui vous renvoie à Bash une fois le fichier traité.

5. Pour vérifier que la table **errorLogs** a été créée, utilisez l’instruction suivante pour renvoyer toutes les lignes à partir d’**errorLogs** :

        SELECT * from errorLogs;

    Trois lignes de données doivent normalement être renvoyées. Elles contiennent toutes **[ERROR]** dans la colonne t4 :
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

##<a id="summary"></a>Résumé

Comme vous pouvez le constater, la commande Beeline permet d’exécuter facilement et de façon interactive des requêtes Hive sur un cluster HDInsight.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Si vous utilisez Tez avec Hive, consultez les documents suivants pour les informations de débogage :

* [Utiliser l'interface utilisateur Tez sur HDInsight Windows](hdinsight-debug-tez-ui.md)

* [Utilisez la vue Tez Ambari sur HDInsight Linux](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

<!---HONumber=AcomDC_0921_2016-->