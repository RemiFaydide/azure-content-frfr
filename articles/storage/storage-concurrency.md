<properties
	pageTitle="Gestion de l’accès concurrentiel dans Microsoft Azure Storage"
	description="Gestion de l’accès concurrentiel pour les services BLOB, de File d’attente, de Table et de Fichier"
	services="storage"
	documentationCenter=""
	authors="jasonnewyork"
	manager="tadb"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="jahogg;tamram"/>

# Gestion de l’accès concurrentiel dans Microsoft Azure Storage

## Vue d'ensemble

Dans les applications Internet modernes, les données sont généralement consultées et mises à jour par plusieurs utilisateurs à la fois. Les développeurs d'applications doivent donc bien réfléchir à la manière de proposer une expérience prévisible à leurs utilisateurs finaux, notamment lorsque plusieurs utilisateurs peuvent mettre à jour les mêmes données. Les développeurs prennent généralement en compte trois grandes stratégies d'accès concurrentiel aux données :


1.	Accès concurrentiel optimiste – Une application procédant à une mise à jour vérifie, dans le cadre de la mise à jour, que les données n'ont pas été modifiées depuis la dernière lecture. Par exemple, si deux utilisateurs qui consultent une page wiki procèdent à une mise à jour de la même page, la plateforme wiki doit veiller à ce que la deuxième mise à jour n'écrase pas la première et à ce que les deux utilisateurs sachent si leur mise à jour a fonctionné ou non. Cette stratégie est la plus souvent utilisée dans les applications web.
2.	Accès concurrentiel pessimiste – L'application qui cherche à procéder à une mise à jour verrouille l'objet, ce qui empêche les autres utilisateurs de mettre les données à jour jusqu'à ce qu'elles soient déverrouillées. Par exemple, dans un scénario de réplication de données maître/esclave où seul le maître procède aux mises à jour, le maître verrouille généralement les données de manière exclusive pendant une période de temps prolongée de manière à ce que personne d'autre ne puisse les mettre à jour.
3.	Règle de Thomas (Last writer wins) – Approche qui permet de procéder aux mises à jour sans vérifier si les données ont été ou non mises à jour par une autre application depuis la première lecture des données par l'application. Cette stratégie (ou ce manque de stratégie formelle) est généralement utilisée lorsque les données font l'objet d'une partition telle qu'il est peu probable que plusieurs utilisateurs accèdent aux mêmes données. Elle peut également être utile lors du traitement de flux de données à durée de vie limitée.

Cet article propose une vue d'ensemble de la manière dont la plateforme Azure Storage simplifie le développement en proposant une prise en charge de premier ordre pour ces trois stratégies d'accès concurrentiel.

## Azure Storage – Simplification du développement dans le cloud
Le service de stockage Azure prend en charge les trois stratégies. Il se distingue cependant dans sa capacité à proposer une prise en charge complète pour les accès concurrentiels optimistes et pessimistes. Il a en effet été conçu pour adopter un modèle de cohérence forte qui garantit que lorsque le service de stockage procède à une mise à jour ou à un ajout de données, la dernière mise à jour s'affiche pour les utilisateurs qui accèdent aux données par la suite. Les plateformes de stockage qui utilisent un modèle de cohérence éventuelle présentent un décalage entre le moment où des données sont ajoutées par un utilisateur et le moment où les données mises à jour peuvent être consultées par les autres utilisateurs, ce qui complique le développement d'applications clientes, afin d'éviter que les incohérences n'affectent les utilisateurs finaux.

Parallèlement à la sélection d'une stratégie d'accès concurrentiel adaptée, les développeurs doivent savoir comment la plateforme de stockage isole les changements, notamment ceux apportés à un même objet au fil des transactions. Le service de stockage Azure utilise l'isolement de capture instantanée pour permettre l'exécution simultanée des opérations de lecture et d'écriture au sein d'une même partition. Contrairement à d'autres niveaux d'isolement, l'isolement de capture instantanée permet de garantir l'affichage d'une capture instantanée cohérente des données pour tous les lecteurs, même lorsque des mises à jour sont en cours, en renvoyant notamment les dernières valeurs validées pendant le traitement d'une transaction de mise à jour.

## Gestion de l’accès concurrentiel dans Blob Storage
Vous pouvez choisir d'utiliser des modèles d'accès concurrentiel optimiste ou pessimiste pour gérer l'accès aux objets blob et aux conteneurs dans le service BLOB. Si vous ne sélectionnez pas une stratégie de manière explicite, la règle de Thomas est utilisée par défaut.

### Accès concurrentiel optimiste pour les objets blob et les conteneurs  

Le service de stockage attribue un identificateur à chaque objet stocké. Cet identificateur est mis à jour à chaque fois qu'une mise à jour est effectuée sur un objet. L'identificateur est renvoyé au client en tant que réponse HTTP GET à l'aide de l'en-tête ETag (balise d'entité) défini dans le protocole HTTP. L'utilisateur qui procède à une mise à jour sur un tel objet peut envoyer la balise ETag d'origine avec un en-tête conditionnel pour veiller à ce que la mise à jour ne survienne que si une certaine condition est remplie. Dans ce cas, la condition est un en-tête « If-Match », qui nécessite que le service de stockage vérifie que la valeur de la balise ETag indiquée dans la demande de mise à jour soit la même que celle stockée dans le service de stockage.

Ce processus se déroule comme suit :

1.	Récupérez un objet blob à partir du service de stockage, la réponse inclut une valeur d'en-tête ETag HTTP qui identifie la version actuelle de l'objet dans le service de stockage.
2.	Lorsque vous mettez l'objet blob à jour, incluez la valeur ETag reçue à l'étape 1 dans l'en-tête conditionnel **If-Match** de la demande que vous envoyez au service.
3.	Le service compare la valeur ETag de la demande à la valeur ETag de l'objet blob.
4.	Si la valeur ETag de l'objet blob n'est pas la même que la balise ETag dans l'en-tête conditionnel **If-Match** de la demande, le service renvoie une erreur 412 au client. Cela indique au client que l'objet blob a été mis à jour par un autre processus depuis la récupération par le client.
5.	Si la valeur ETag actuelle de l'objet blob est la même que la balise ETag dans l'en-tête conditionnel **If-Match** de la demande, le service effectue l'opération demandée et met la valeur ETag de l'objet blob à jour pour indiquer qu'il a créé une nouvelle version.

L'extrait de code C# suivant (à l'aide de la bibliothèque de stockage cliente 4.2.0) présente un exemple simple de construction d'une condition d'accès **If-Match AccessCondition** basée sur la valeur ETag obtenue à partir des propriétés d'un objet blob précédemment récupéré ou inséré. Il utilise ensuite l’objet **AccessCondition** lorsqu’il met à jour l’objet blob : l’objet **AccessCondition** ajoute l’en-tête **If-Match** à la demande. Si l’objet blob a été mis à jour par un autre processus, le service BLOB renvoie un message d’état HTTP 412 (Échec de la condition préalable). Vous pouvez télécharger l’exemple complet ici : [Gestion de l’accès concurrentiel avec Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

	// Retrieve the ETag from the newly created blob
	// Etag is already populated as UploadText should cause a PUT Blob call
	// to storage blob service which returns the etag in response.
	string orignalETag = blockBlob.Properties.ETag;

	// This code simulates an update by a third party.
	string helloText = "Blob updated by a third party.";

	// No etag, provided so orignal blob is overwritten (thus generating a new etag)
	blockBlob.UploadText(helloText);
	Console.WriteLine("Blob updated. Updated ETag = {0}",
	blockBlob.Properties.ETag);

	// Now try to update the blob using the orignal ETag provided when the blob was created
	try
	{
	    Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
	    blockBlob.UploadText(helloText,accessCondition:
	    AccessCondition.GenerateIfMatchCondition(orignalETag));
	}
	catch (StorageException ex)
	{
	    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
	    {
	        Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
	        // TODO: client can decide on how it wants to handle the 3rd party updated content.
	    }
	    else
	        throw;
	}  

Le service de stockage prend également en charge des en-têtes conditionnels supplémentaires tels que **If-Modified-Since**, **If-Unmodified-Since** et **If-None-Match**, ainsi que des associations de ces en-têtes. Pour plus d'informations, consultez la rubrique [Spécification des en-têtes conditionnels pour les opérations du service BLOB](http://msdn.microsoft.com/library/azure/dd179371.aspx) sur MSDN.

Le tableau suivant résume les opérations de conteneurs qui acceptent les en-têtes conditionnels tels que **If-Match** dans la demande et qui renvoient une valeur ETag dans la réponse.

Opération |Renvoie une valeur ETag de conteneur|	Accepte les en-têtes conditionnels|
------------|-----------------------|------------------------------------|
Create Container|	Oui|	Non|
Get Container Properties|	Oui|	Non|
Get Container Metadata|	Oui|	Non|
Set Container Metadata|	Oui|	Oui|
Get Container ACL|	Oui|	Non|
Set Container ACL|	Oui|	Oui (*)|
Delete Container| Non| Oui|
Lease Container| Oui| Oui|
List Blobs| Non| Non 

(*) Les autorisations définies par SetContainerACL sont mises en cache et les mises à jour apportées à ces autorisations sont diffusées dans un délai de 30 secondes, période pendant laquelle la cohérence des mises à jour n’est pas garantie.

Le tableau suivant résume les opérations d'objets blob qui acceptent les en-têtes conditionnels tels que **If-Match** dans la demande et qui renvoient une valeur ETag dans la réponse.

Opération |Renvoie une valeur ETag |Accepte les en-têtes conditionnels|
-----------|-------------------|----------------------------|
Put Blob|	Oui|	Oui|
Get Blob|	Oui|	Oui|
Get Blob Properties|	Oui|	Oui|
Set Blob Properties|	Oui|	Oui|
Get Blob Metadata|	Oui|	Oui|
Set Blob Metadata|	Oui|	Oui|
Lease Blob (*)| Oui| Oui|
Snapshot Blob| Oui| Oui|
Copy Blob| Oui| Oui (pour les objets blob sources et de destination)|
Abort Copy Blob| Non| Non|
Delete Blob| Non| Oui|
Put Block| Non| Non|
Put Block List| Oui| Oui|
Get Block List| Oui| Non|
Put Page| Oui| Oui|
Get Page Ranges| Oui| Oui


(*) L'opération Lease Blob n'entraîne pas la modification de la balise ETag d'un objet blob.

### Accès concurrentiel pessimiste pour les objets blob
Pour verrouiller un objet blob de manière à l'utiliser de manière exclusive, vous pouvez obtenir un [bail](http://msdn.microsoft.com/library/azure/ee691972.aspx) pour l'objet blob. Lorsque vous obtenez un bail, vous spécifiez pendant combien de temps vous en avez besoin : cette durée peut être comprise entre 15 à 60 secondes ou peut être infinie, ce qui confère un verrouillage exclusif. Vous pouvez renouveler un bail à durée limitée et vous pouvez libérer un bail lorsque vous n'en avez plus besoin. Le service BLOB libère automatiquement les baux à durée limitée lorsqu'ils expirent.

Les baux permettent la prise en charge de différentes stratégies de synchronisation, dont des stratégies d'écriture exclusive/de lecture partagée, d'écriture exclusive/de lecture exclusive et d'écriture partagée/de lecture exclusive. Si un bail existe, le service de stockage applique une stratégie d’écriture exclusive (opérations Placement, Définition et Suppression). Cependant, pour garantir l’exclusivité des opérations de lecture, le développeur doit veiller à ce que toutes les applications clientes utilisent un identificateur de bail et à ce que seul un client à la fois dispose d’un identificateur de bail valable. Les opérations de lecture sans identificateur de bail entraînent l’application d’une stratégie de lecture partagée.

L'extrait de code C# suivant présente un exemple d'obtention d'un bail exclusif de 30 secondes sur un objet blob, de mise à jour du contenu de l'objet blob et de libération du bail. Si l'objet blob fait déjà l'objet d'un bail valable lorsque vous tentez d'obtenir un nouveau bail, le service BLOB renvoie un message d'état HTTP 409 (Conflit). L'extrait de code ci-dessous utilise un objet **AccessCondition** pour encapsuler les informations relatives au bail lors de la demande de mise à jour de l'objet blob dans le service de stockage. Vous pouvez télécharger l’exemple complet ici : [Gestion de l’accès concurrentiel avec le Stockage Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

	// Acquire lease for 15 seconds
	string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
	Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

	// Update blob using lease. This operation will succeed
	const string helloText = "Blob updated";
	var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
	blockBlob.UploadText(helloText, accessCondition: accessCondition);
	Console.WriteLine("Blob updated using an exclusive lease");

	//Simulate third party update to blob without lease
	try
	{
	    // Below operation will fail as no valid lease provided
	    Console.WriteLine("Trying to update blob without valid lease");
	    blockBlob.UploadText("Update without lease, will fail");
	}
	catch (StorageException ex)
	{
	    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
	        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
	    else
	        throw;
	}  

Si vous tentez de procéder à une opération d'écriture sur un objet blob faisant l'objet d'un bail sans transmettre l'identificateur de bail, un message d'erreur 412 est renvoyé. Notez que si le bail expire avant l'appel de la méthode **UploadText**, un message d'erreur **412** est également renvoyé même si vous transmettez l'identificateur de bail. Pour plus d'informations au sujet de la gestion des délais d'expiration des baux et des identificateurs de baux, consultez la documentation REST [Lease Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx).

Les opérations d'objets blob suivantes peuvent utiliser des baux dans le cadre de la gestion de l'accès concurrentiel pessimiste :


-   Put Blob
-	Get Blob
-	Get Blob Properties
-	Set Blob Properties
-	Get Blob Metadata
-	Set Blob Metadata
-	Delete Blob
-	Put Block
-	Put Block List
-	Get Block List
-	Put Page
-	Get Page Ranges
-	Snapshot Blob - identificateur de bail en option s'il existe un bail
-	Copy Blob - identificateur de bail obligatoire s'il existe un bail sur l'objet blob cible
-	Abort Copy Blob - identificateur de bail obligatoire s'il existe un bail à durée illimitée sur l'objet blob cible
-	Lease Blob

### Accès concurrentiel pessimiste pour les conteneurs
Les baux sur les conteneurs permettent la prise en charge des mêmes stratégies de synchronisation que sur les objets blob (écriture exclusive/lecture partagée, écriture exclusive/lecture exclusive et écriture partagée/lecture exclusive). Cependant, contrairement aux objets blob, le service de stockage applique uniquement l'exclusivité aux opérations de suppression. Pour supprimer un conteneur avec un bail actif, le client doit inclure l'identificateur du bail actif dans la demande de suppression. Toutes les opérations sont correctement effectuées sur les conteneurs soumis à un bail sans que l'identificateur de bail soit inclus, il s'agit alors d'opérations partagées. Si l'exclusivité est requise pour les opérations de mise à jour (Put ou Set) ou de lecture, les développeurs doivent veiller à ce que tous les clients utilisent un identificateur de bail et à ce que seul un client à la fois dispose d'un identificateur de bail valable.

Les opérations de conteneurs suivantes peuvent utiliser des baux dans le cadre de la gestion de l'accès concurrentiel pessimiste :

-	Delete Container
-	Get Container Properties
-	Get Container Metadata
-	Set Container Metadata
-	Get Container ACL
-	Set Container ACL
-	Lease Container

Pour plus d’informations, consultez les pages suivantes :

- [Spécification des en-têtes conditionnels pour les opérations du service BLOB](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [Lease Container](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [Lease Blob ](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## Gestion de l’accès concurrentiel dans le service de Table
Le service de Table utilise les vérifications d'accès concurrentiel optimiste comme comportement par défaut lorsque vous travaillez avec des entités, contrairement au service BLOB où vous devez choisir de manière explicite de procéder à des vérifications d'accès concurrentiel optimiste. L'autre différence réside dans le fait que vous pouvez uniquement gérer le comportement d'accès concurrentiel des entités avec le service de Table alors qu'avec le service BLOB, vous pouvez gérer l'accès concurrentiel des conteneurs et des objets blob.

Pour utiliser l'accès concurrentiel optimiste et pour déterminer si un autre processus a modifié une entité depuis sa récupération à partir du service de stockage de tables, vous pouvez utiliser la valeur ETag reçue lorsque le service de Table renvoie une entité. Ce processus se déroule comme suit :

1.	Récupérez une entité à partir du service de stockage de tables, la réponse inclut une valeur ETag qui détermine l'identificateur associé à l'entité dans le service de stockage.
2.	Lorsque vous mettez l'entité à jour, incluez la valeur ETag reçue à l'étape 1 dans l'en-tête obligatoire **If-Match** de la demande que vous envoyez au service.
3.	Le service compare la valeur ETag de la demande à la valeur ETag de l'entité.
4.	Si la valeur ETag de l'entité est différente de la balise ETag dans l'en-tête obligatoire **If-Match** de la demande, le service renvoie une erreur 412 au client. Cela indique au client que l'entité a été mise à jour par un autre processus depuis la récupération par le client.
5.	Si la valeur ETag de l'entité est la même que la balise ETag dans l'en-tête obligatoire **If-Match** de la demande ou si l'en-tête **If-Match** contient le caractère générique (*), le service effectue l'opération demandée et met la valeur ETag de l'entité à jour pour indiquer qu'elle a été mise à jour.

Notez que, contrairement au service BLOB, le client doit inclure un en-tête **If-Match** dans les demandes de mise à jour dans le cadre du service de Table. Il est cependant possible de procéder de force à une mise à jour inconditionnelle (règle de Thomas) et de contourner les vérifications d'accès concurrentiel en ajoutant le caractère générique (*) dans l'en-tête **If-Match** de la demande.

L’extrait de code C# suivant présente une entité de client précédemment créée ou récupérée et dont l’adresse de messagerie a été mise à jour. L'opération d'insertion ou de récupération initiale stocke la valeur ETag dans l'objet client et, l'exemple utilisant la même instance d'objet lors de l'exécution de l'opération de remplacement, il renvoie automatiquement la valeur ETag au service de Table, ce qui permet au service de vérifier les violations d'accès concurrentiel. Si l'entité a été mise à jour par un autre processus dans le service de stockage de tables, le service renvoie un message d'état HTTP 412 (Échec de la condition préalable). Vous pouvez télécharger l’exemple complet ici : [Gestion de l’accès concurrentiel avec le Stockage Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

	try
	{
	    customer.Email = "updatedEmail@contoso.org";
	    TableOperation replaceCustomer = TableOperation.Replace(customer);
	    customerTable.Execute(replaceCustomer);
	    Console.WriteLine("Replace operation succeeded.");
	}
	catch (StorageException ex)
	{
	    if (ex.RequestInformation.HttpStatusCode == 412)
	        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
	    else
	        throw;
	}  

Pour désactiver de manière explicite la vérification d'accès concurrentiel, vous devez définir la propriété **ETag** de l'objet **employee** sur « * » avant d'exécuter l'opération de remplacement.

	customer.ETag = "*";  

Le tableau suivant résume la manière dont les opérations d'entités de table utilisent les valeurs ETag :

| Opération | Renvoie une valeur ETag | Nécessite l'en-tête de demande If-Match |
|:-------------------------|:-------------------|:---------------------------------|
| Query Entities | Oui | Non |
| Insert Entity | Oui | Non |
| Update Entity | Oui | Oui |
| Merge Entity | Oui | Oui |
| Delete Entity | Non | Oui |
| Insert or Replace Entity | Oui | Non |
| Insert or Merge Entity | Oui | Non |

Notez que les opérations **Insert or Replace Entity** et **Insert or Merge Entity** ne procèdent *pas* à des vérifications d'accès concurrentiel étant donné qu'elles n'envoient pas de valeur ETag au service de Table.

Les développeurs utilisant des tables doivent généralement s'appuyer sur l'accès concurrentiel optimiste lors du développement d'applications extensibles. Si un verrouillage pessimiste est nécessaire, les développeurs peuvent, lors de l'accès aux tables, attribuer un objet blob désigné à chaque table et essayer d'appliquer un bail à l'objet blob avant de travailler sur la table. Dans le cadre de cette approche, l'application doit veiller à ce que tous les chemins d'accès aux données obtiennent le bail avant de travailler sur la table. Vous devez également noter que la durée minimale de bail est de 15 secondes, ce qui nécessite une bonne prise en considération pour l'extensibilité.

Pour plus d’informations, consultez les pages suivantes :

- [Opérations sur les entités](http://msdn.microsoft.com/library/azure/dd179375.aspx)

## Gestion de l’accès concurrentiel dans le service de File d’attente
L'accès concurrentiel est un problème dans le cadre du service de File d'attente, où plusieurs clients récupèrent des messages à partir d'une file d'attente. Lorsqu'un message est récupéré à partir de la file d'attente, la réponse inclut le message et une valeur d'accusé pop, nécessaire à la suppression du message. Le message n'est pas automatiquement supprimé de la file d'attente mais, une fois récupéré, les autres clients ne le voient pas pendant l'intervalle défini par le paramètre visibilitytimeout. Le client qui récupère le message doit le supprimer une fois le message traité et avant expiration du délai défini par l'élément TimeNextVisible de la réponse, calculé en fonction de la valeur du paramètre visibilitytimeout. La valeur visibilitytimeout est ajoutée à l'heure à laquelle le message a été récupéré pour déterminer la valeur de l'élément TimeNextVisible.

Le service de File d'attente ne prend pas en charge l'accès concurrentiel optimiste ou pessimiste. Les clients qui traitent des messages récupérés à partir d'une file d'attente doivent donc veiller à ce que les messages soient traités de manière idempotente. La règle de Thomas est utilisée pour les opérations de mise à jour telles que SetQueueServiceProperties, SetQueueMetaData, SetQueueACL et UpdateMessage.

Pour plus d’informations, consultez les pages suivantes :

- [API REST du service de File d’attente](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [Get Messages](http://msdn.microsoft.com/library/azure/dd179474.aspx)

## Gestion de l’accès concurrentiel dans le service de Fichier
Il est possible d'accéder au service de Fichier à l'aide de deux points de terminaison de protocole différents : SMB et REST. Le service REST ne prend pas en charge le verrouillage optimiste ou pessimiste, toutes les mises à jour sont donc effectuées selon la règle de Thomas. Les clients SMB qui montent les partages de fichiers peuvent utiliser les mécanismes de verrouillage du système de fichiers pour gérer l'accès aux fichiers partagés (possibilité de procéder à un verrouillage pessimiste incluse). Lorsqu'un client SMB ouvre un fichier, il définit le mode de partage et d'accès au fichier. Si le fichier est accessible en écriture ou en lecture/écriture et si aucun mode de partage de fichiers n'est défini, le fichier est verrouillé par le client SMB jusqu'à fermeture. En cas de tentative d'opération REST sur un fichier verrouillé par un client SMB, le service REST renvoie un code d'état 409 (Conflit) avec le code d'erreur SharingViolation.

Si un client SMB ouvre un fichier en vue de le supprimer, il marque le fichier comme étant en attente de suppression jusqu'à ce que les descripteurs d'ouverture des autres clients SMB pour ce fichier soient fermés. Lorsqu'un fichier est marqué comme étant en attente de suppression, toutes les opérations REST effectuées sur le fichier renvoient un code d'état 409 (Conflit) avec le code d'erreur SMBDeletePending. Le code d'état 404 (Introuvable) n'est pas renvoyé étant donné que le client SMB peut supprimer l'indicateur de suppression en attente avant de fermer le fichier. En d'autres termes, le code d'état 404 (Introuvable) ne peut être renvoyé que si le fichier a été supprimé. Notez que, lorsqu'un fichier est en attente de suppression SMB, il n'est pas inclus dans les résultats List Files. Notez également que les opérations REST Delete File et REST Delete Directory sont automatiquement validées et n'entraînent pas un état de suppression en attente.

Pour plus d’informations, consultez les pages suivantes :

- [Gestion des verrouillages de fichiers](http://msdn.microsoft.com/library/azure/dn194265.aspx)

## Résumé et étapes suivantes
Le service Microsoft Azure Storage a été conçu pour répondre aux besoins des applications en ligne les plus complexes sans forcer les développeurs à faire des compromis ou à repenser des hypothèses de conception clés, telles que l'accès concurrentiel et la cohérence des données, qu'ils considèrent désormais comme acquises.

Pour l'exemple complet d'application auquel il est fait référence dans ce blog :

- [Gestion de l’accès concurrentiel avec Azure Storage - Exemple d’application](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

Pour plus d’informations concernant Azure Storage, consultez la page :

- [Page d’accueil de Microsoft Azure Storage](https://azure.microsoft.com/services/storage/)
- [Introduction à Azure Storage](storage-introduction.md)
- Prise en main du Stockage [Blob](storage-dotnet-how-to-use-blobs.md), [Table](storage-dotnet-how-to-use-tables.md), [File d’attente](storage-dotnet-how-to-use-queues.md) et [Fichier](storage-dotnet-how-to-use-files.md)
- Architecture de stockage – [Stockage Azure : service de stockage cloud à haute disponibilité et à forte cohérence](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

<!---HONumber=AcomDC_0921_2016-->