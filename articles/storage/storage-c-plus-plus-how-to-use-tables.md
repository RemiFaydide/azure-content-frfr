<properties
    pageTitle="Utilisation du stockage de table (C++) | Microsoft Azure"
	description="Stockez des données structurées dans le cloud à l’aide du stockage de tables Azure, un magasin de données NoSQL."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
	ms.date="09/20/2016"
    ms.author="dineshm;tamram"/>

# Utilisation du stockage de table à partir de C++

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## Vue d'ensemble  
Ce guide décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage de table Azure. Les exemples ont été écrits en C++ et utilisent la [bibliothèque cliente Azure Storage pour C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Les scénarios traités incluent la **création et la suppression d’une table**, ainsi que l’**utilisation d’entités de table**.

>[AZURE.NOTE] Ce guide cible la bibliothèque cliente Azure Storage pour C++ version 1.0.0 et les versions ultérieures. La version recommandée est la bibliothèque cliente de stockage version 2.2.0, disponible par le biais de [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## Création d’une application C++  
Dans ce guide, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application C++. Pour ce faire, vous devez installer la bibliothèque cliente Azure Storage pour C++ et créer un compte Azure Storage dans votre abonnement Azure.

Pour installer la bibliothèque cliente Azure Storage pour C++, vous pouvez procéder comme suit :

-	**Linux :** suivez les instructions disponibles dans la page [Bibliothèque cliente Azure Storage pour C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).
-	**Windows :** dans Visual Studio, cliquez sur **Outils > Gestionnaire de package NuGet > Console du gestionnaire de package**. Entrez la commande suivante dans la [console du gestionnaire du package NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) et appuyez sur Entrée.

		Install-Package wastorage

## Configuration de votre application pour accéder au stockage de table  
Ajoutez l’instruction import suivante au début du fichier C++ dans lequel vous voulez utiliser des API de stockage Azure pour accéder aux tables :

	#include "was/storage_account.h"
	#include "was/table.h"

## Configuration d’une chaîne de connexion au stockage Azure  
Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker des points de terminaison et des informations d’identification permettant d’accéder aux services de gestion des données. Quand vous exécutez une application cliente, vous devez fournir la chaîne de connexion de stockage dans le format suivant. Utilisez le nom de votre compte de stockage et la clé d’accès de stockage pour le compte de stockage répertorié sur le [portail Azure](https://portal.azure.com) pour les valeurs *AccountName* et *AccountKey*. Pour plus d’informations sur les comptes et les clés d’accès de stockage, consultez [À propos des comptes Azure Storage](storage-create-storage-account.md). Cet exemple vous montre comment déclarer un champ statique pour qu’il contienne une chaîne de connexion :

	// Define the connection string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Pour tester votre application sur votre ordinateur Windows local, vous pouvez utiliser l’[émulateur de stockage](storage-use-emulator.md) Azure installé avec le [Kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/). L’émulateur de stockage est un utilitaire qui simule les services Azure d’objet blob, de file d’attente et de table disponibles sur votre ordinateur de développement local. L’exemple suivant vous montre comment déclarer un champ statique pour qu’il contienne une chaîne de connexion vers votre émulateur de stockage local :

	// Define the connection string with Azure storage emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Pour démarrer l’émulateur de stockage Azure, cliquez sur le bouton **Démarrer** ou appuyez sur la touche Windows. Commencez à taper **Émulateur de stockage Azure**, puis sélectionnez **Émulateur de stockage Microsoft Azure** dans la liste des applications.

Les exemples ci-dessous partent du principe que vous avez utilisé l’une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## Récupération de votre chaîne de connexion  
Vous pouvez utiliser la classe **cloud\_storage\_account** pour représenter les informations de votre compte de stockage. Pour extraire les informations de votre compte de stockage de la chaîne de connexion de stockage, vous pouvez utiliser la méthode parse.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Ensuite, récupérez une référence à une classe **cloud\_table\_client**, car elle vous permet d’obtenir des objets de référence pour les tables et entités stockées dans le service de stockage de Table. Le code suivant crée un objet **cloud\_table\_client** en utilisant l’objet de compte de stockage récupéré ci-dessus :

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## Création d’une table
Un objet **cloud\_table\_client** vous permet d’obtenir les objets de référence pour les tables et entités. Le code suivant crée un objet **cloud\_table\_client** et l’utilise pour créer une table.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();  

## Ajout d’une entité à une table
Pour ajouter une entité à une table, créez un objet **table\_entity** et transmettez-le à **table\_operation::insert\_entity**. Le code suivant utilise le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition. Ensemble, les clés de partition et de ligne d’une entité identifient l’entité de façon unique dans la table. Les requêtes d’entités dont les clés de partition sont identiques sont plus rapides que celles d’entités dont les clés de partition sont différentes, mais le fait d’utiliser différentes clés de partition améliore l’extensibilité des opérations parallèles. Pour plus d’informations, consultez [Liste de contrôle des performances et de l’extensibilité de Microsoft Azure Storage](storage-performance-checklist.md).

Le code suivant crée une instance de la classe **table\_entity** avec des données client à stocker. Le code appelle ensuite **table\_operation::insert\_entity** pour créer un objet **table\_operation** pour insérer une entité dans une table et y associer la nouvelle entité de table. Enfin, le code appelle la méthode execute sur l’objet **cloud\_table**. Puis le nouvel objet **table\_operation** envoie une demande au service de Table pour insérer la nouvelle entité de client dans la table « people ».

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();

	// Create a new customer entity.
	azure::storage::table_entity customer1(U("Harp"), U("Walter"));

	azure::storage::table_entity::properties_type& properties = customer1.properties();
	properties.reserve(2);
	properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

	properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

	// Create the table operation that inserts the customer entity.
	azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

	// Execute the insert operation.
	azure::storage::table_result insert_result = table.execute(insert_operation);

## Insertion d’un lot d’entités
Vous pouvez insérer un lot d’entités dans le service de Table en une seule opération d’écriture. Le code suivant crée un objet **table\_batch\_operation**, puis y ajoute trois opérations d’insertion. Chaque opération d’insertion est ajoutée en créant un objet d’entité, en définissant ses valeurs, puis en appelant la méthode insert sur l’objet **table\_batch\_operation** pour associer l’entité avec une nouvelle opération d’insertion. La méthode **cloud\_table.execute** est ensuite appelée pour exécuter l’opération.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define a batch operation.
	azure::storage::table_batch_operation batch_operation;

	// Create a customer entity and add it to the table.
	azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

	azure::storage::table_entity::properties_type& properties1 = customer1.properties();
	properties1.reserve(2);
	properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
	properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

	// Create another customer entity and add it to the table.
	azure::storage::table_entity customer2(U("Smith"), U("Ben"));

	azure::storage::table_entity::properties_type& properties2 = customer2.properties();
	properties2.reserve(2);
	properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
	properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

	// Create a third customer entity to add to the table.
	azure::storage::table_entity customer3(U("Smith"), U("Denise"));

	azure::storage::table_entity::properties_type& properties3 = customer3.properties();
	properties3.reserve(2);
	properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
	properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

	// Add customer entities to the batch insert operation.
	batch_operation.insert_or_replace_entity(customer1);
	batch_operation.insert_or_replace_entity(customer2);
	batch_operation.insert_or_replace_entity(customer3);

	// Execute the batch operation.
	std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Quelques remarques sur les opérations par lots :

-	Vous pouvez effectuer jusqu’à 100 opérations d’insertion, de suppression, de fusion, de remplacement, d’insertion ou fusion et d’insertion ou de remplacement dans n’importe quelle combinaison en un seul lot.
-	Une opération par lot peut comporter une opération d’extraction, s’il s’agit de la seule opération du lot.
-	Toutes les entités d’une opération par lot doivent avoir la même clé de partition.
-	Une opération par lot est limitée à une charge utile de données de 4 Mo.

## Extraction de toutes les entités d’une partition
Pour exécuter une requête de table pour toutes les entités d’une partition, utilisez un objet **table\_query**. L’exemple de code suivant indique un filtre pour les entités où ’Smith’ est la clé de partition. Il imprime les champs de chaque entité dans les résultats de requête vers la console.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Construct the query operation for all customer entities where PartitionKey="Smith".
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Print the fields for each customer.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
			<< U(", Property1: ") << properties.at(U("Email")).string_value()
			<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
	}  

La requête de cet exemple affiche toutes les entités qui correspondent aux critères de filtre. Si vous avez des tables volumineuses et que vous devez télécharger les entités de table souvent, nous vous recommandons de plutôt stocker vos données dans des objets blob de stockage Azure.

## Extraction d’un ensemble d’entités dans une partition
Si vous ne voulez pas exécuter une requête pour toutes les entités d'une partition, vous pouvez spécifier un ensemble en combinant le filtre de clé de partition avec un filtre de clé de ligne. L’exemple de code suivant utilise deux filtres pour obtenir toutes les entités dans la partition « Smith » où la clé de ligne (prénom) commence par une lettre située avant la lettre « E » dans l’ordre alphabétique, puis imprime les résultats de la requête.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table query.
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
		azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
		azure::storage::query_comparison_operator::equal, U("Smith")),
		azure::storage::query_logical_operator::op_and,
		azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Loop through the results, displaying information about the entity.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
			<< U(", Property1: ") << properties.at(U("Email")).string_value()
			<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
	}  

## Extraction d'une seule entité
Vous pouvez écrire une requête pour extraire une seule entité. Le code suivant utilise **table\_operation::retrieve\_entity** pour spécifier le client « Jeff Smith ». Cette méthode renvoie une seule entité, au lieu d’une collection. De plus, la valeur renvoyée est dans **table\_result**. La méthode la plus rapide pour extraire une seule entité dans le service de Table consiste à spécifier une clé de partition et une clé de ligne.

	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Output the entity.
	azure::storage::table_entity entity = retrieve_result.entity();
	const azure::storage::table_entity::properties_type& properties = entity.properties();

	std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
		<< U(", Property1: ") << properties.at(U("Email")).string_value()
		<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## Remplacement d’une entité
Pour remplacer une entité, récupérez-la dans le service de Table, modifiez l’objet d’entité, puis enregistrez les modifications dans le service de Table. Le code suivant modifie le numéro de téléphone et l’adresse de messagerie électronique d’un client existant. Au lieu d’appeler **table\_operation::insert\_entity**, ce code utilise **table\_operation::replace\_entity**. Ceci entraîne le remplacement complet de l’entité sur le serveur, sauf si cette dernière a été modifiée depuis sa récupération, auquel cas l’opération échoue. Cet échec survient pour empêcher votre application de remplacer par erreur une modification apportée entre la récupération et la mise à jour par un autre composant de votre application. Pour gérer correctement cet échec, vous devez récupérer de nouveau l’entité, apporter vos modifications (si elles sont toujours valides), puis effectuer une autre opération **table\_operation::replace\_entity**. La prochaine section vous apprendra à remplacer ce comportement.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Replace an entity.
	azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
	properties_to_replace.reserve(2);

	// Specify a new phone number.
	properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

	// Specify a new email address.
	properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

	// Create an operation to replace the entity.
	azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

	// Submit the operation to the Table service.
	azure::storage::table_result replace_result = table.execute(replace_operation);

## Insertion ou remplacement d’une entité
Les opérations **table\_operation::replace\_entity** échouent si l’entité est modifiée depuis sa récupération à partir du serveur. De plus, pour que l’opération **table\_operation::replace\_entity** réussisse, vous devez d’abord récupérer l’entité à partir du serveur. Cependant, il se peut parfois que vous ne sachiez pas si l’entité existe sur le serveur et si les valeurs stockées sont inadaptées. Votre mise à jour doit donc toutes les remplacer. Pour ce faire, utilisez une opération **table\_operation::insert\_or\_replace\_entity**. Cette opération insère l’entité (s’il n’y en a pas déjà une) ou la remplace (s’il y en a une), indépendamment du moment de la dernière mise à jour. Dans l’exemple de code suivant, l’entité de client pour Jeff Smith est toujours récupérée, mais elle est ensuite enregistrée sur le serveur via **table\_operation::insert\_or\_replace\_entity**. Les mises à jour apportées à l’entité entre les opérations de récupération et de mise à jour sont remplacées.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Insert-or-replace an entity.
	azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

	properties_to_insert_or_replace.reserve(2);

	// Specify a phone number.
	properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

	// Specify an email address.
	properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

	// Create an operation to insert-or-replace the entity.
	azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

	// Submit the operation to the Table service.
	azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## Interrogation d’un sous-ensemble de propriétés d’entité  
Vous pouvez utiliser une requête de table pour extraire uniquement quelques propriétés d’une entité. La requête contenue dans le code suivant utilise la méthode **table\_query::set\_select\_columns** pour renvoyer uniquement les adresses de messagerie des entités dans la table.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define the query, and select only the Email property.
	azure::storage::table_query query;
	std::vector<utility::string_t> columns;

	columns.push_back(U("Email"));
	query.set_select_columns(columns);

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Display the results.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

		const azure::storage::table_entity::properties_type& properties = it->properties();
		for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
		{
			std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
		}

		std::wcout << std::endl;
	}

>[AZURE.NOTE] L’interrogation d’un petit nombre de propriétés d’une entité est une opération plus efficace que l’extraction de toutes les propriétés.

## Suppression d’une entité
Il est facile de supprimer une entité après l’avoir récupérée. Une fois que l’entité est extraite, appelez **table\_operation::delete\_entity** avec l’entité à supprimer. Appelez ensuite la méthode **cloud\_table.execute**. Le code suivant récupère et supprime une entité dont la clé de partition est « Smith » et la clé de ligne « Jeff ».

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the Table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);  

## Suppression d’une table
Pour finir, l’exemple de code suivant supprime une table d’un compte de stockage. Une table supprimée ne peut plus être recréée pendant un certain temps.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the Table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);

## Étapes suivantes
Les bases du stockage des tables étant assimilées, voir les liens suivants pour en savoir plus sur Azure Storage :

-	[Utilisation du stockage d’objets blob à partir de C++](storage-c-plus-plus-how-to-use-blobs.md)
-	[Utilisation du service de stockage de files d’attente à partir de C++](storage-c-plus-plus-how-to-use-queues.md)
-	[Listage des ressources Azure Storage en C++](storage-c-plus-plus-enumeration.md)
-	[Référence de la bibliothèque cliente de stockage pour C++](http://azure.github.io/azure-storage-cpp)
-	[Documentation d’Azure Storage](https://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=AcomDC_0921_2016-->