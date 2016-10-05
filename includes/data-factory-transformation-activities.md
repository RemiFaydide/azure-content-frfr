Azure Data Factory prend en charge les activités de transformation suivantes, qui peuvent être ajoutées à des pipelines, soit individuellement soit de façon chaînée avec une autre activité.

Activités de transformation des données | Environnement de calcul 
:----------------------- | :--------------------
[Hive](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] [Pig](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop] [MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop] [Hadoop Streaming](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] [Activités Machine Learning : exécution par lot et mise à jour de la ressource](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Microsoft Azure 
[Procédure stockée](../articles/data-factory/data-factory-stored-proc-activity.md) | SQL Azure, Azure SQL Data Warehouse ou SQL Server |
[Langage U-SQL du service Analytique Data Lake](../articles/data-factory/data-factory-usql-activity.md) | Service Analytique Azure Data Lake 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou Azure Batch
   
> [AZURE.NOTE] 
Vous pouvez utiliser l'activité MapReduce pour exécuter des programmes Spark sur votre cluster HDInsight Spark. Consultez la page [Appeler des programmes Spark à partir d’Azure Data Factory](../articles/data-factory/data-factory-spark.md) pour plus d’informations. Vous pouvez créer une activité personnalisée pour exécuter des scripts R sur votre cluster HDInsight si R est installé. Consultez la page [Exécuter des scripts R avec Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).

<!---HONumber=AcomDC_0928_2016-->