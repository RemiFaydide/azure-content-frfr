<properties
	pageTitle="Utilisation du stockage de tables à partir de Python | Microsoft Azure"
	description="Stockez des données structurées dans le cloud à l’aide du stockage de tables Azure, un magasin de données NoSQL."
	services="storage"
	documentationCenter="python"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="gusapost;tamram"/>


# Utilisation du stockage de tables à partir de Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage de Tables Azure. Les exemples sont écrits en Python et utilisent le [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]. Les scénarios traités incluent la création et la suppression d’une table, en plus de l’insertion et de l’interrogation d’entités dans une table.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d’une table

L'objet **TableService** permet d'utiliser les services de Table. Le code suivant permet de créer un objet **TargetService**. Ajoutez ce code vers le début de tout fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

	from azure.storage.table import TableService, Entity

Le code suivant crée un objet **TableService** en utilisant le nom et la clé du compte de stockage. Remplacez « myaccount » et « mykey » par le nom et la clé réels de votre compte.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## Ajout d’une entité à une table

Pour ajouter une entité, commencez par créer un dictionnaire ou une entité définissant les noms et valeurs des propriétés de votre entité. Notez que pour chaque entité, vous devez spécifier les clés **PartitionKey** et **RowKey**. Elles permettent d’identifier vos entités de manière univoque. Vous pouvez interroger ces valeurs beaucoup plus vite que d’autres propriétés. Le système utilise **PartitionKey** pour distribuer automatiquement les entités de la table sur plusieurs nœuds de stockage. Les entités partageant la même clé **PartitionKey** sont stockées sur le même nœud. **RowKey** identifie de manière univoque l’entité dans sa partition.

Pour ajouter une entité à votre table, transmettez l'objet dictionnaire à la méthode **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

Vous pouvez également transmettre une instance de la classe **Entity** à la méthode **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## Mise à jour d'une entité

Ce code montre comment remplacer l'ancienne version d'une entité existante par une version mise à jour.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', task)

Si l'entité à remplacer n'existe pas, l'opération de mise à jour échoue. Pour stocker une entité, utilisez **insert\_or\_replace\_entity**. Dans l'exemple suivant, le premier appel remplace l'entité existante. Le deuxième appel insère une nouvelle entité, car il n'existe aucune entité ayant les clés **PartitionKey** et **RowKey** spécifiées.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', task)

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', task)

## Modification d’un groupe d’entités

Il est parfois intéressant de soumettre un lot d'opérations simultanément pour assurer un traitement atomique par le serveur. Pour cela, utilisez la classe **TableBatch**. Lorsque vous devez soumettre le lot, appelez **commit\_batch**. Notez que toutes les entités doivent se trouver dans la même partition pour pouvoir être modifiées par lot. L'exemple ci-dessous permet d'ajouter deux entités dans un lot.

	from azure.storage.table import TableBatch
	batch = TableBatch()
	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	batch.insert_entity(task10)
	batch.insert_entity(task11)
	table_service.commit_batch('tasktable', batch)

Les lots peuvent également être utilisés avec la syntaxe du gestionnaire de contexte :

	task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
	task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

	with table_service.batch('tasktable') as batch:
		batch.insert_entity(task12)
		batch.insert_entity(task13)


## Interrogation d’une entité

Pour interroger une entité dans une table, utilisez la méthode **get\_entity** en spécifiant les clés **PartitionKey** et **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## Interrogation d’un ensemble d’entités

Cet exemple recherche toutes les tâches dans Seattle avec la clé **PartitionKey**.

	tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## Interrogation d’un sous-ensemble de propriétés d’entité

Vous pouvez utiliser une requête de table pour extraire uniquement quelques propriétés d’une entité. Cette technique, nommée *projection*, réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. Utilisez le paramètre **select** et transmettez le nom des propriétés à soumettre au client.

La requête contenue dans le code suivant ne renvoie que la description des entités de la table.

[AZURE.NOTE] L’extrait suivant ne fonctionne que sur le service de stockage cloud. L’émulateur de stockage ne le prend pas en charge.

	tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
	for task in tasks:
		print(task.description)

## Suppression d’une entité

Vous pouvez supprimer une entité en utilisant ses clés de partition et de ligne.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## Suppression d’une table

Le code suivant permet de supprimer une table d'un compte de stockage.

	table_service.delete_table('tasktable')

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage de tables, consultez les liens suivants pour en savoir plus.

- [Centre de développement Python](/develop/python/)
- [API REST des services d’Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog de l'équipe Azure Storage]
- [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]

[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->