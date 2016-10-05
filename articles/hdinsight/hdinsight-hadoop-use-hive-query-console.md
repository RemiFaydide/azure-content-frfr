<properties
   pageTitle="Utilisation de Hadoop Hive sur la console de requêtes dans HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser la console de requêtes Web pour exécuter des requêtes Hive sur un cluster Hadoop HDInsight à partir de votre navigateur."
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# Exécution de requêtes Hive à l'aide de la console de requêtes

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous découvrirez comment utiliser la console de requêtes HDInsight pour exécuter des requêtes Hive sur un cluster Hadoop HDInsight à partir de votre navigateur.

> [AZURE.IMPORTANT] La console de requêtes HDInsight n’est disponible que sur les clusters HDInsight Windows. Si vous utilisez un cluster HDInsight Linux, consultez [Utilisez la vue Hive avec Hadoop dans HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).


##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop HDInsight Windows

* Un navigateur Web moderne

##<a id="run"></a> Exécution de requêtes Hive à l'aide de la console de requêtes

1. Ouvrez un navigateur web et accédez à __https://CLUSTERNAME.azurehdinsight.net__, où __CLUSTERNAME\_\_ est le nom de votre cluster HDInsight. Lorsque vous y êtes invité, entrez le nom d'utilisateur et le mot de passe que vous avez entrés lors de la création du cluster.


2. À partir des liens situés en haut de la page, sélectionnez **Éditeur Hive**. Cela affiche un formulaire qui peut être utilisé pour saisir les instructions HiveQL que vous souhaitez exécuter sur le cluster HDInsight.

	![l’éditeur Hive](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)

	Remplacez le texte `Select * from hivesampletable` par les instructions HiveSQL suivantes :

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.
    * **CREATE EXTERNAL TABLE** : crée une table « externe » dans Hive. Les tables externes stockent uniquement la définition de table dans Hive ; les données restent à leur emplacement d’origine.

    > [AZURE.NOTE] Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe (comme un processus de téléchargement de données automatisé) ou par une autre opération MapReduce, mais souhaitez toujours que les requêtes Hive utilisent les données les plus récentes.
    >
    > La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

    * **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.
    * **STORED AS TEXTFILE LOCATION** : indique à Hive l'emplacement des données (le répertoire exemple/données) et précise qu'elles sont stockées sous la forme de texte.
    * **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette commande renvoie la valeur **3**, car trois lignes contiennent cette valeur.
    * **INPUT\_\_FILE\_\_NAME LIKE '%.log'** : indique à Hive de retourner uniquement des données provenant de fichiers se terminant par .log. Cela limite la recherche au fichier sample.log qui contient les données et l'empêche de renvoyer des données provenant d'autres fichiers d'exemple qui ne correspondent pas au schéma que nous avons défini.

2. Cliquez sur **Envoyer**. La **session de la tâche** située au bas de la page devrait afficher les détails de la tâche.

3. Une fois le champ **État** défini sur **Terminé**, sélectionnez **Afficher les détails** de la tâche. Dans la page relative aux détails, la **sortie de la tâche** contient `[ERROR]	3`. Vous pouvez utiliser le bouton **Télécharger**, situé en dessous de ce champ, pour télécharger un fichier contenant la sortie de la tâche.


##<a id="summary"></a>Résumé

Comme vous pouvez le constater, la console de requêtes permet d'exécuter facilement des requêtes Hive sur un cluster HDInsight, de surveiller l'état de la tâche et de récupérer le résultat.

Pour en savoir plus sur l’utilisation de la console de requêtes Hive pour l’exécution de tâches Hive, sélectionnez **Prise en main** en haut de la console de requêtes, puis utilisez les exemples fournis. Chaque exemple aborde le processus d'analyse de données à l'aide de Hive, y compris les explications des instructions HiveQL utilisées dans l'exemple.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Si vous utilisez Tez avec Hive, consultez les documents suivants pour les informations de débogage :

* [Utiliser l'interface utilisateur Tez sur HDInsight Windows](hdinsight-debug-tez-ui.md)

* [Utilisez la vue Tez Ambari sur HDInsight Linux](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

<!---HONumber=AcomDC_0921_2016-->