<properties
	pageTitle="Copie ou déplacement des données vers le stockage avec AzCopy | Microsoft Azure"
	description="Utilisez l'utilitaire AzCopy pour déplacer ou copier des données vers ou à partir de contenu d'objets Blob, de table et de fichier. Copiez des données vers Azure Storage à partir de fichiers locaux ou copiez des données dans ou entre des comptes de stockage. Migrez facilement vos données vers Azure Storage."
	services="storage"
	documentationCenter=""
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2016"
	ms.author="micurd;tamram"/>

# Transfert de données avec l'utilitaire de ligne de commande AzCopy

## Vue d'ensemble

AzCopy est un utilitaire de ligne de commande Windows conçu pour copier des données depuis et vers un objet blob Microsoft Azure, un fichier ou un stockage de table en utilisant les commandes avec une performance optimale. Vous pouvez copier des données d’un objet vers un autre au sein de votre compte de stockage ou entre des comptes de stockage.

> [AZURE.NOTE] Ce guide part du principe que vous êtes déjà familiarisé avec [Azure Storage](https://azure.microsoft.com/services/storage/). Sinon, la lecture de la documentation [Introduction à Azure Storage](storage-introduction.md) vous sera utile. Plus important encore, vous devez [créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) afin de démarrer à l’aide d’AzCopy.

## Téléchargement et installation d’AzCopy

### Windows

Téléchargez la [dernière version d'AzCopy](http://aka.ms/downloadazcopy).

### Mac/Linux

AzCopy n’est pas disponible pour les systèmes d’exploitation Mac/Linux. Toutefois, Azure CLI constitue une alternative appropriée pour copier des données vers et depuis Azure Storage. Lisez [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](storage-azure-cli.md) pour en savoir plus.

## Écriture de votre première commande AzCopy

La syntaxe de base d’une commande AzCopy est :

	AzCopy /Source:<source> /Dest:<destination> [Options]

Ouvrez une fenêtre de commande, puis naviguez jusqu’au répertoire d’installation d’AzCopy sur votre ordinateur, où se trouve l’exécutable `AzCopy.exe`. Si vous le souhaitez, vous pouvez ajouter l’emplacement d’installation d’AzCopy au chemin de votre système. Par défaut, AzCopy est installé dans `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` ou `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

Les exemples suivants montrent différents scénarios permettant de copier des données vers et à partir d’objets blob Microsoft Azure, les fichiers et les tables. Reportez-vous à la section [Paramètres AzCopy](#azcopy-parameters) pour obtenir une explication détaillée des paramètres utilisés dans chaque échantillon.

## Blob : Téléchargement

### Télécharger un seul objet blob

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

Remarque : si le dossier `C:\myfolder` n’existe pas encore, AzCopy le crée dans le système de fichiers et télécharge `abc.txt ` dans le nouveau dossier.

### Télécharger un objet blob unique depuis la région secondaire

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Notez que vous devez avoir un accès en lecture activé pour le stockage géo-redondant.

### Télécharger tous les objets blob

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Si les objets blob suivants se trouvent dans le conteneur spécifié :

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

Après l’opération de téléchargement, le répertoire `C:\myfolder` inclut les fichiers suivants :

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\vd1\a.txt
	C:\myfolder\vd1\abcd.txt

Si vous ne spécifiez pas l’option `/S`, aucun objet blob n’est téléchargé.

### Téléchargement d’objets blob avec le préfixe spécifié

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Si les objets blob suivants se trouvent dans le conteneur spécifié, Tous les objets blob commençant par le préfixe `a` sont téléchargés :

	abc.txt
	abc1.txt
	abc2.txt
	xyz.txt
	vd1\a.txt
	vd1\abcd.txt

Après l’opération de téléchargement, le dossier `C:\myfolder` inclut les fichiers suivants :

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

Le préfixe s’applique au répertoire virtuel, qui forme la première partie du nom de l’objet blob. Dans l’exemple ci-dessus, le répertoire virtuel ne correspond pas au préfixe spécifié ; il n’est donc pas téléchargé. En outre, si l’option `\S` n’est pas spécifiée, AzCopy ne télécharge pas les objets blob.

### Définition de l’heure de la dernière modification des fichiers exportés pour qu’elle soit identique à celle des objets blob source

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

Vous pouvez également exclure des objets blob de l’opération de téléchargement en vous basant sur l’heure de leur dernière modification Par exemple, si vous souhaitez exclure les objets blob dont la dernière heure de modification est identique ou plus récente que celle du fichier de destination `/XN` :

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

Ou si vous souhaitez exclure les objets blob dont la dernière heure de modification est identique ou plus ancienne que celle du fichier de destination `/XO` :

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## Objet blob : Téléchargement

### Télécharger un fichier unique

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

Si le conteneur de destination spécifié n’existe pas, AzCopy le crée et y charge le fichier.

### Télécharger un fichier unique dans le répertoire virtuel

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Si le répertoire virtuel spécifié n’existe pas, AzCopy charge le fichier pour y inclure le répertoire virtuel dans son nom (*par exemple*, `vd/abc.txt` dans l’exemple ci-dessus).

### Télécharger tous les fichiers

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

La spécification de l’option `/S` engendre le téléchargement des contenus du répertoire spécifié vers le stockage d’objets blob récursivement, ce qui implique également la copie de tous les sous-dossiers et de leurs fichiers. Par exemple, si les fichiers suivants se trouvent dans le dossier `C:\myfolder` :

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

Après l’opération de téléchargement, le conteneur inclut les fichiers suivants :

  	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt

Si vous ne spécifiez pas l’option `/S`, AzCopy ne charge pas de manière récursive. Après l’opération de téléchargement, le conteneur inclut les fichiers suivants :

	abc.txt
	abc1.txt
	abc2.txt

### Télécharger des fichiers correspondant au modèle spécifié

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Si les fichiers suivants se trouvent dans le dossier `C:\myfolder` :

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\xyz.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

Après l’opération de téléchargement, le conteneur inclut les fichiers suivants :

	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt

Si vous ne spécifiez pas l’option `/S`, AzCopy téléchargera uniquement les objets blob qui ne se trouvent pas dans un répertoire virtuel :

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

### Spécifier le type de contenu MIME d’un objet blob de destination

Par défaut, AzCopy définit le type de contenu d'un objet blob de destination comme `application/octet-stream`. Depuis la version 3.1.0, vous pouvez spécifier explicitement le type de contenu via l'option `/SetContentType:[content-type]`. Cette syntaxe définit le type de contenu de tous les objets blob dans une opération de téléchargement.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Si vous spécifiez `/SetContentType` sans valeur, AzCopy définit chaque type de contenu d'objet blob ou de fichier en fonction de son extension de fichier.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## Objet blob : copie

### Copie d’un objet blob unique au sein d’un compte de stockage

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

Lorsque vous copiez un objet blob au sein d’un compte de stockage, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### Copier un objet blob unique sur plusieurs comptes de stockage

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Lorsque vous copiez un objet blob sur plusieurs comptes de stockage, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### Copier un objet blob unique de la région secondaire à la région principale

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Notez que vous devez avoir un accès en lecture activé pour le stockage géo-redondant.

### Copier un objet blob unique et ses captures instantanées sur plusieurs comptes de stockage

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Après l’opération de copie, le conteneur cible inclut l’objet blob et ses captures instantanées. Si on part du principe que l’exemple ci-dessus comprend deux captures instantanées, le conteneur inclut l’objet blob et les captures instantanées suivants :

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt

### Copier des objets blob de façon synchrone dans des comptes de stockage

AzCopy copie par défaut les données entre deux points de terminaison de stockage de façon asynchrone. Par conséquent, l’opération de copie s’exécute dans l’arrière-plan à l’aide de la capacité de la bande passante, non soumise à un SLA en matière de vitesse de copie d’un objet blob. AzCopy vérifie périodiquement l’état de copie jusqu’à ce que la copie soit terminée ou ait échoué.

L'option `/SyncCopy` garantit que l'opération de copie a une vitesse constante. AzCopy effectue la copie synchrone en téléchargeant les objets blob à copier à partir de la source spécifiée dans la mémoire locale, puis en les téléchargeant sur la destination de stockage d’objets blob.

	AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy` peut générer des coûts de sortie supplémentaires par rapport à la copie asynchrone, l’approche recommandée consiste à utiliser cette option dans une machine virtuelle Azure qui se trouve dans la même région que votre compte de stockage source afin d’éviter les frais de sortie.

## Fichier : Téléchargement

### Télécharger un fichier unique

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Si la source spécifiée est un partage de fichier Azure, vous devez soit spécifier le nom de fichier exact, (*par exemple,* `abc.txt`) pour télécharger un fichier unique, soit spécifier l’option `/S` pour télécharger tous les fichiers dans le partage de manière récursive. Une erreur se produit si vous tentez de spécifier à la fois un modèle de fichier et l'option `/S`.

### Charger tous les fichiers

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Notez que les dossiers vides ne seront pas téléchargés.

## Fichier : Télécharger

### Télécharger un fichier unique

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### Télécharger tous les fichiers

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Notez que les dossiers vides ne sont pas téléchargés.

### Télécharger des fichiers correspondant au modèle spécifié

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## Fichier : Copier

### Copier d’un partage de fichier à l’autre

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### Copier d’un partage de fichiers vers un objet blob

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Notez que la copie asynchrone depuis le stockage de fichiers vers un objet blob de pages n’est pas prise en charge.

### Copier d’un objet blob vers le partage de fichiers

	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### Copier les fichiers de façon synchrone

Vous pouvez spécifier l’option `/SyncCopy` pour copier d’un stockage de fichiers vers un autre stockage de fichiers, d’un stockage de fichiers vers un stockage d’objet blob et d’un stockage d’objet blob o un stockage de fichiers de façon synchrone. Pour ce faire, AzCopy télécharge les données sources dans la mémoire locale, puis les charge à nouveau vers la destination.

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Pendant la copie depuis le stockage de fichier vers le stockage d'objets Blob, le type d'objet Blob par défaut est l'objet Blob de blocs. L'utilisateur peut spécifier l'option `/BlobType:page` pour modifier le type d'objet Blob de destination.

Notez que `/SyncCopy` peut occasionner des coûts supplémentaires par rapport à une copie asynchrone. L'approche recommandée consiste à utiliser cette option dans la machine virtuelle Azure qui se trouve dans la même région que votre compte de stockage source afin d'éviter les coûts de sortie.

## Table : Exportation

### Table d’exportation

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy écrit un fichier manifeste dans le dossier de destination spécifié. Le processus d’importation utilise ce fichier manifeste pour localiser les fichiers de données nécessaires et effectuer la validation des données. Le fichier manifeste utilise la convention de noms suivante par défaut :

	<account name>_<table name>_<timestamp>.manifest

L'utilisateur peut également spécifier l'option `/Manifest:<manifest file name>` pour définir le nom du fichier manifeste.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### Fractionnement de l’exportation en plusieurs fichiers

	AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy utilise un *index de volume* dans les noms des fichiers de données fractionnés pour distinguer les fichiers. L'index de volume se compose de deux parties : un *index de plage de clés de partition* et un *index de fichier fractionné*. Ces deux index commencent à zéro.

L’index de plage de clés de partition est égal à 0 si l’utilisateur ne spécifie pas l’option `/PKRS`.

Exemple : supposons qu'AzCopy crée deux fichiers de données après que l'utilisateur a spécifié l'option `/SplitSize`. Les noms des fichiers de données qui en résultent peuvent être :

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Remarque : la valeur minimale possible pour l’option `/SplitSize` est 32 Mo. Si la destination spécifiée est un stockage d'objets blob, AzCopy fractionne le fichier de données lorsque sa taille atteint la limite de taille des objets blob (200 Go), que l'utilisateur ait spécifié ou non l'option `/SplitSize`.

### Table d’exportation vers un format de fichier de données CSV ou JSON

AzCopy par défaut exporte des tables dans des fichiers de données JSON. Vous pouvez spécifier l’option `/PayloadFormat:JSON|CSV` pour exporter les tables en tant que JSON ou CSV.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Lorsque vous spécifiez le format de charge utile CSV, AzCopy génère également un fichier de schéma avec l’extension `.schema.csv` pour chaque fichier de données.

### Exportation simultanée d’entités de table

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy lance des opérations simultanées d'exportation d'entités lorsque l'utilisateur spécifie l'option `/PKRS`. Chaque opération exporte une plage de clés de partition.

Remarque : l'option `/NC` contrôle également le nombre d'opérations simultanées. AzCopy utilise le nombre de processeurs Core comme valeur par défaut de `/NC` pendant la copie d'entités de table, même si l'option `/NC` n'a pas été spécifiée. Lorsque l'utilisateur spécifie l'option `/PKRS`, AzCopy utilise la plus petite des deux valeurs (plages de clés de partition par rapport aux opérations simultanées implicitement ou explicitement spécifiées) pour déterminer le nombre d'opérations simultanées à démarrer. Pour plus d'informations, tapez `AzCopy /?:NC` dans la ligne de commande.

### Table d’exportation d’objet blob

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy crée un fichier de données JSON dans le conteneur d’objets blob en respectant la convention de noms suivante :

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Le fichier de données JSON créé respecte le format de charge utile pour les métadonnées minimales. Pour des informations sur le format de charge utile, consultez la page [Format de charge utile pour les opérations du service de Table](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Notez que lors de l’exportation des tables vers les objets blob, AzCopy télécharge les entités de Table vers les fichiers de données temporaires locaux et téléchargez ensuite ces entités dans l’objet blob. Ces fichiers de données temporaires sont placés dans le dossier du fichier journal avec le chemin d’accès par défaut « <code>%LocalAppData%\\Microsoft\\Azure\\AzCopy</code> », vous pouvez spécifier l’option /Z:[dossier de fichier journal] pour modifier l’emplacement de dossier du fichier et ainsi modifier l’emplacement des fichiers de données temporaires. La taille des fichiers de données temporaires est définie par la taille de vos entités de table et la taille spécifiée avec l’option /SplitSize, bien que le fichier de données temporaire dans le disque local soit supprimé instantanément. Une fois qu’il a été téléchargé vers l’objet blob, assurez-vous que vous disposez de suffisamment d’espace sur le disque local pour stocker ces fichiers de données temporaires avant qu’ils soient supprimés,

## Table : importation

### Table d’importation

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

L'option `/EntityOperation` indique comment insérer des entités dans la table. Les valeurs possibles sont les suivantes :

- `InsertOrSkip` : ignore une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.
- `InsertOrMerge` : fusionne une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.
- `InsertOrReplace` : remplace une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.

Remarque : vous ne pouvez pas spécifier l'option `/PKRS` dans le scénario d'importation. À la différence du scénario d’exportation dans lequel vous devez spécifier l’option `/PKRS` pour démarrer des opérations simultanées, AzCopy lance par défaut des opérations simultanées lorsque vous importez une table. Le nombre par défaut d’opérations simultanées démarrées est égal au nombre de processeurs Core. Cependant, vous pouvez spécifier un nombre différent d’opérations simultanées avec l’option `/NC`. Pour plus d'informations, tapez `AzCopy /?:NC` dans la ligne de commande.

Notez qu’AzCopy ne prend en charge que l’importation pour JSON, et non CSV. AzCopy ne prend pas en charge les importations de table à partir de fichiers JSON créés par l’utilisateur et de fichiers manifeste. Ces deux types de fichiers doivent provenir d’une exportation de table AzCopy. Pour éviter les erreurs, ne modifiez pas le fichier JSON ou le fichier manifeste exporté.

### Importez des entités vers la table à l’aide d’objets blob

Supposons qu’un conteneur d’objets blob contient les éléments suivants : fichier JSON représentant une table Azure et le fichier manifeste associé.

	myaccount_mytable_20140103T112020.manifest
	myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Vous pouvez exécuter la commande suivante pour importer des entités dans une table en utilisant le fichier manifeste dans le conteneur d’objets blob :

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## Autres fonctionnalités AzCopy

### Copier uniquement les données qui n’existent pas dans la destination

Les paramètres `/XO` et `/XN` vous permettent d’exclure les ressources source plus anciennes ou plus récentes d’être copiées respectivement. Si vous souhaitez copier uniquement des ressources de code source qui n’existent pas dans la destination, vous pouvez spécifier les deux paramètres dans la commande AzCopy :

	/Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

	/Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

	/Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Remarque : cela n’est pas pris en charge lorsque la source ou la destination est une table.

### Utilisation d’un fichier réponse pour spécifier les paramètres de ligne de commande

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

Vous pouvez inclure n’importe quels paramètres de ligne de commande AzCopy dans un fichier réponse. AzCopy traite les paramètres du fichier comme s’ils avaient été spécifiés sur la ligne de commande, réalisant une substitution directe avec les contenus du fichier.

Si un fichier réponse nommé `copyoperation.txt`, qui contient les lignes suivantes : Chaque paramètre AzCopy peut être spécifié sur une seule ligne

	/Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

ou sur des lignes distinctes :

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S
	/Y

AzCopy échouera si vous écrivez le paramètre sur deux lignes, comme démontré ici pour le paramètre `/sourcekey` :

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	<sourcekey>
	/S
	/Y

### Utilisation de plusieurs fichiers réponse pour spécifier les paramètres de ligne de commande

Si un fichier réponse dénommé `source.txt` qui spécifie un conteneur source :

	/Source:http://myaccount.blob.core.windows.net/mycontainer

Et un fichier réponse nommé `dest.txt` qui spécifie un dossier de destination dans le système de fichiers :

	/Dest:C:\myfolder

Et un fichier réponse nommé `options.txt` qui spécifie les options pour AzCopy :

	/S /Y

Appellent AzCopy avec ces fichiers réponse résidant tous dans un répertoire `C:\responsefiles`, utilisez la commande suivante :

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy traite cette commande comme si vous aviez inclus tous les paramètres individuels de la ligne de commande :

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### Spécification d’une signature d’accès partagé (SAP)

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

Vous pouvez également spécifier une SAP sur l’URI du conteneur :

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### Dossier du fichier journal

Chaque fois que vous émettez une commande sur AzCopy, il vérifie si un fichier journal existe dans le dossier par défaut ou dans un dossier que vous avez spécifié via cette option. Si le fichier journal n’existe à aucun de ces emplacements, AzCopy considère l’opération comme nouvelle et génère un nouveau fichier journal.

Si le fichier journal existe, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande du fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l’opération incomplète. Si elles ne correspondent pas, il vous sera demandé soit d’écraser le fichier journal pour démarrer une nouvelle opération, soit d’annuler l’opération actuelle.

Si vous souhaitez utiliser l’emplacement par défaut pour le fichier journal :

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Si vous omettez l'option `/Z`, ou spécifiez l'option `/Z` sans le chemin du dossier, comme démontré ci-dessus, AzCopy crée le fichier journal à l'emplacement par défaut, qui est `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Si le fichier journal existe déjà, AzCopy reprend l’opération en se basant sur le fichier journal.

Si vous souhaitez spécifier un emplacement personnalisé pour le fichier journal :

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

Cet exemple crée le fichier journal s’il n’existe pas déjà. S’il existe, AzCopy reprend l’opération en se basant sur le fichier journal.

Si vous souhaitez reprendre une opération AzCopy :

	AzCopy /Z:C:\journalfolder\

Cet exemple reprend la dernière opération, qui est susceptible de ne pas avoir abouti.

### Génération d’un fichier journal

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Si vous spécifiez l’option `/V` sans fournir de chemin de fichier pour le journal détaillé, AzCopy crée le fichier journal à l’emplacement par défaut, qui est `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Autrement, vous pouvez créer un fichier journal dans un emplacement personnalisé :

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Remarque : si vous spécifiez un chemin relatif suivant l'option `/V`, tel que `/V:test/azcopy1.log`, le journal détaillé est alors créé dans le répertoire en cours d'utilisation dans un sous-dossier nommé `test`.

### Spécification du nombre d’opérations simultanées pour démarrer

L'option `/NC` spécifie le nombre d'opérations de copie simultanées. Par défaut, AzCopy lance un certain nombre d’opérations simultanées pour augmenter la vitesse de transfert des données. Pour les opérations sur les tables, le nombre d’opérations simultanées est égal au nombre de processeurs dont vous disposez. Pour les opérations sur les objets blob et les fichiers, le nombre d’opérations simultanées est égal à 8 fois le nombre de processeurs dont vous disposez. Si vous exécutez AzCopy sur un réseau à bande passante étroite, vous pouvez spécifier un nombre inférieur pour /NC afin d’éviter l’échec causé par la compétition de ressources.

### Exécutez AzCopy sur l’émulateur de stockage Azure

Vous pouvez exécuter AzCopy sur l’[émulateur de stockage Azure](storage-use-emulator.md) pour les objets blob :

	AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

et les tables :

	AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## Paramètres AzCopy

Les paramètres d’AzCopy sont décrits ci-dessous. Vous pouvez également taper une des commandes suivantes dans la ligne de commande pour obtenir de l’aide sur l’utilisation d’AzCopy :

- Pour l'aide détaillée sur la ligne de commande AzCopy : `AzCopy /?`
- Pour l'aide détaillée sur un paramètre AzCopy : `AzCopy /?:SourceKey`
- Pour des exemples de ligne de commande : `AzCopy /?:Samples`

### /Source:"source"

Spécifie les données sources à partir desquelles la copie peut s’effectuer. La source peut être un répertoire du système de fichiers, un conteneur d’objets blob, un répertoire virtuel d’objets blob, un partage de fichiers de stockage, un répertoire de fichiers de stockage ou une table Azure.

**S’applique à :** objets blob, fichiers, tables

### /Dest:"destination"

Spécifie la destination vers laquelle la copie va s’effectuer. La destination peut être un répertoire du système de fichiers, un conteneur d’objets blob, un répertoire virtuel d’objets blob, un partage de fichiers de stockage, un répertoire de fichiers de stockage ou une table Azure.

**S’applique à :** objets blob, fichiers, tables

### /Pattern:"file-pattern"

Spécifie un modèle de fichier qui indique les fichiers à copier. Le comportement du paramètre /Pattern est déterminé par l’emplacement des données sources et la présence de l’option mode récursif. Le mode récursif est spécifié via l’option /S.

Si la source spécifiée est un répertoire dans le système de fichiers, les caractères génériques standard sont appliqués et le modèle de fichier fourni est comparé aux fichiers présents dans le répertoire. Si l’option /S est spécifiée, AzCopy compare également le modèle spécifié à tous les fichiers présents dans les sous-dossiers du répertoire.

Si la source spécifiée est un conteneur d’objets blob ou un répertoire virtuel, les caractères génériques ne sont pas appliqués. Si l’option /S est spécifiée, AzCopy interprète le modèle de fichier spécifié comme un préfixe d’objet blob. Si l’option /S n’est pas spécifiée, AzCopy compare le modèle de fichier aux noms exacts d’objets blob.

Si la source spécifiée est un partage de fichiers Azure, vous devez soit spécifier le nom exact du fichier (abc.txt) pour copier un seul fichier, soit spécifier l’option /S pour copier récursivement tous les fichiers dans le partage. Une erreur se produit si vous tentez de spécifier à la fois un modèle de fichier et l’option /S.

AzCopy tient compte de la casse uniquement quand la /Source est un conteneur d’objets blob ou un répertoire virtuel d’objets blob.

Le modèle de fichier par défaut utilisé lorsqu’aucun modèle de fichier n’est spécifié est *.* pour un emplacement de système de fichiers, ou un préfixe vide pour un emplacement Azure Storage. La spécification de plusieurs modèles de fichiers n’est pas prise en charge.

**S’applique à :** objets blob, fichiers

### /DestKey:"storage-key"

Spécifie la clé du compte de stockage pour la ressource de destination.

**S’applique à :** objets blob, fichiers, tables

### /DestSAS:"sas-token"

Spécifie une signature d’accès partagé (SAP) avec les autorisations de lecture et d’écriture pour la destination (le cas échéant). Ajoutez des guillemets à la SAP, car elle peut contenir des caractères spéciaux de ligne de commande.

Si la ressource de destination est un conteneur d’objets blob, un partage de fichiers ou une table, vous pouvez spécifier soit cette option suivie du jeton SAP, soit la SAP comme élément d’URI de l’objet blob, du partage de fichiers ou de la table de destination, sans cette option.

Si la source et la destination sont toutes les deux des objets blob, l’objet blob de destination doit se trouver dans le même compte de stockage que l’objet blob source.

**S’applique à :** objets blob, fichiers, tables

### /SourceKey:"storage-key"

Spécifie la clé du compte de stockage pour la ressource source.

**S’applique à :** objets blob, fichiers, tables

### /SourceSAS:"sas-token"

Spécifie une signature d’accès partagé avec les autorisations de lecture et de listing pour la source (le cas échéant). Ajoutez des guillemets à la SAP, car elle peut contenir des caractères spéciaux de ligne de commande.

Si la ressource source est un conteneur d’objets blob et si aucune clé ou SAP n’est fournie, le conteneur est lu via un accès anonyme.

Si la source est un partage de fichiers ou une table, une clé ou une SAP doit être fournie.

**S’applique à :** objets blob, fichiers, tables

### /S

Spécifie le mode récursif pour les opérations de copie. En mode récursif, AzCopy copie tous les objets blob ou fichiers correspondant au modèle de fichier spécifié, incluant ceux qui se trouvent dans les sous-dossiers.

**S’applique à :** objets blob, fichiers

### /BlobType:"block" | "page" | "append"

Spécifie si la destination est un objet blob de blocs, un objet blob de pages ou un objet blob d’ajout. Cette option s’applique uniquement lorsque vous téléchargez un objet blob. Sinon, une erreur se produit. Si la destination est un objet blob et si cette option n’est pas spécifiée, AzCopy crée par défaut un objet blob de blocs.

**S’applique à :** objets blob

### /CheckMD5

Calcule un hachage MD5 pour les données téléchargées et vérifie que le hachage MD5 stocké dans la propriété Content-MD5 de l'objet blob ou du fichier correspond au hachage calculé. La vérification MD5 est désactivée par défaut ; vous devez donc spécifier cette option pour lancer la vérification MD5 lorsque vous téléchargez des données.

Remarque : Azure Storage ne garantit pas que le hachage MD5 stocké pour l’objet blob ou le fichier est à jour. Il est de la responsabilité du client de mettre à jour le MD5 lorsque l’objet blob ou le fichier est modifié.

AzCopy établit toujours la propriété Content-MD5 pour un objet blob ou fichier Azure après l’avoir chargé sur le service.

**S’applique à :** objets blob, fichiers

### /Snapshot

Indique si le transfert de captures instantanées est activé ou non. Cette option est valide uniquement lorsque la source est un objet blob.

Les captures instantanées d’objets blob transférées sont renommées de cette façon : nom\_d’objet\_Blob (durée de capture instantanée).extension

Par défaut, les captures instantanées ne sont pas copiées.

**S’applique à :** objets blob

### /V:[verbose-log-file]

Stocke les messages de statut détaillés dans un fichier journal.

Par défaut, le fichier journal détaillé est nommé dans `%LocalAppData%\Microsoft\Azure\AzCopy`. Si vous spécifiez un emplacement de fichier existant pour cette option, le journal détaillé est ajouté à ce fichier.

**S’applique à :** objets blob, fichiers, tables

### /Z:[journal-file-folder]

Spécifie un dossier de fichier journal pour reprendre une opération.

AzCopy peut toujours reprendre une opération qui a été interrompue.

Si cette option n’est pas spécifiée ou est spécifiée sans chemin de dossier, AzCopy crée le fichier journal à l’emplacement par défaut, qui est %LocalAppData%\\Microsoft\\Azure\\AzCopy.

Chaque fois que vous émettez une commande sur AzCopy, il vérifie si un fichier journal existe dans le dossier par défaut ou dans un dossier que vous avez spécifié via cette option. Si le fichier journal n’existe à aucun de ces emplacements, AzCopy considère l’opération comme nouvelle et génère un nouveau fichier journal.

Si le fichier journal existe, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande du fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l’opération incomplète. Si elles ne correspondent pas, il vous sera demandé soit d’écraser le fichier journal pour démarrer une nouvelle opération, soit d’annuler l’opération actuelle.

Le fichier journal est supprimé lorsque l’opération est achevée avec succès.

Remarque : reprendre une opération à partir d’un fichier journal créé par une version précédente d’AzCopy n’est pas pris en charge.

**S’applique à :** objets blob, fichiers, tables

### /@:"parameter-file"

Spécifie un fichier qui contient des paramètres. AzCopy traite les paramètres dans le fichier comme s’ils avaient été spécifiés dans la ligne de commande.

Dans un fichier réponse, vous pouvez soit spécifier de multiples paramètres sur une seule ligne, soit spécifier chaque paramètre sur sa propre ligne. Remarque : un paramètre individuel ne peut pas couvrir plusieurs lignes.

Les fichiers réponse peuvent inclure des lignes de commentaires qui commencent par le symbole #.

Vous pouvez spécifier plusieurs fichiers réponse. Toutefois, AzCopy ne prend pas en charge les fichiers réponse imbriqués.

**S’applique à :** objets blob, fichiers, tables

### /Y

Supprime toutes les invites de confirmation d’AzCopy.

**S’applique à :** objets blob, fichiers, tables

### /L

Spécifie une opération de listing uniquement : aucune donnée n’est copiée.

AzCopy interprète l’utilisation de cette option comme une simulation de l’exécution de la ligne de commande sans cette option /L et compte le nombre d’objets copiés. Vous pouvez spécifier l’option /V en même temps pour déterminer les objets destinés à être copiés dans le journal détaillé.

Le comportement de cette option est également déterminé par l’emplacement des données sources et la présence de l’option mode récursif /S et de l’option modèle de fichier /Pattern.

AzCopy nécessite les autorisations de listing et de lecture sur cet emplacement source quand cette option est utilisée.

**S’applique à :** objets blob, fichiers

### /MT

Définit l’heure de la dernière modification du fichier pour qu’elle soit identique à celle de l’objet blob ou du fichier source.

**S’applique à :** objets blob, fichiers

### /XN

Exclut une ressource de source plus récente. La ressource n’est pas copiée si la dernière heure de modification de la source est identique ou plus récente que la destination.

**S’applique à :** objets blob, fichiers

### /XO

Exclut une ressource de source plus ancienne. La ressource n’est pas copiée si la dernière heure de modification de la source est identique ou plus ancienne que la destination.

**S’applique à :** objets blob, fichiers

### /A

Charge uniquement les fichiers dont l’attribut Archive est défini.

**S’applique à :** objets blob, fichiers

### /IA:[RASHCNETOI]

Télécharge uniquement les fichiers qui ont le jeu d’attributs spécifiés.

Les attributs disponibles incluent :

- R = Fichiers en lecture seule
- A = Fichiers prêts à être archivés
- S = Fichiers système
- H = Fichiers masqués
- C = Fichiers compressés
- N = Fichiers normaux
- E = Fichiers cryptés
- T = Fichiers temporaires
- O = Fichiers hors ligne
- I = Fichiers non indexés

**S’applique à :** objets blob, fichiers

### /XA:[RASHCNETOI]

Exclut les fichiers dont l’un des attributs spécifiés est défini.

Les attributs disponibles incluent :

- R = Fichiers en lecture seule
- A = Fichiers prêts à être archivés
- S = Fichiers système
- H = Fichiers masqués
- C = Fichiers compressés
- N = Fichiers normaux
- E = Fichiers cryptés
- T = Fichiers temporaires
- O = Fichiers hors ligne
- I = Fichiers non indexés

**S’applique à :** objets blob, fichiers

### /Delimiter:"délimiteur"

Indique le caractère délimiteur utilisé pour délimiter les répertoires virtuels dans un nom d’objet blob.

Par défaut, AzCopy utilise / comme caractère délimiteur. Toutefois, AzCopy prend en charge n’importe quel caractère commun (tel que @, #, ou %) comme délimiteur. Si vous avez besoin d’inclure l’un de ces caractères spéciaux dans la ligne de commande, ajoutez des guillemets doubles au nom du fichier.

Cette option est applicable uniquement au téléchargement d’objets blob.

**S’applique à :** objets blob

### /NC:"nombre-d’opérations-simultanées"

Spécifie le nombre d’opérations simultanées.

AzCopy lance par défaut un certain nombre d’opérations simultanées pour augmenter la vitesse de transfert des données. Remarque : un grand nombre d’opérations simultanées dans un environnement à faible bande passante peut surcharger la connexion réseau et entraver le bon déroulement des opérations. Limitez les opérations simultanées en fonction de la bande passante de réseau qui est disponible.

Le nombre maximal d’opérations simultanées est égal à 512.

**S’applique à :** objets blob, fichiers, tables

### /SourceType:"Blob" | "Table"

Spécifie que la ressource `source` est un objet blob disponible dans l’environnement de développement local, exécuté sur l’émulateur de stockage.

**S’applique à :** objets blob, tables

### /DestType:"Blob" | "Table"

Spécifie que la ressource `destination` est un objet blob disponible dans l’environnement de développement local, exécuté sur l’émulateur de stockage.

**S’applique à :** objets blob, tables

### /PKRS:"key1#key2#key3#..."

Fractionne la plage de clés de partition pour activer l’exportation des données de la table en parallèle, ce qui augmente la vitesse d’exportation.

Si cette option n’est pas spécifiée, AzCopy utilise un seul thread pour exporter des entités de table. Exemple : si l’utilisateur spécifie /PKRS:"aa#bb", AzCopy lance trois opérations simultanées.

Chaque opération exporte une des trois plages de clés de partition (voir ci-dessous) :

  [first-partition-key, aa)

  [aa, bb)

  [bb, dernière-clé-de-partition]

**S’applique à :** tables

### /SplitSize:"file-size"

Spécifie la taille de fractionnement du fichier exporté en Mo. La valeur minimale autorisée est de 32.

Si cette option n’est pas spécifiée, AzCopy exporte les données de la table dans un seul fichier.

Si les données de la table sont exportées dans un objet blob et si la taille du fichier exporté atteint la limite de 200 Go pour la taille de l’objet blob, AzCopy fractionne le fichier exporté, même si cette option n’est pas spécifiée.

**S’applique à :** tables

### /EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Spécifie le comportement pour l’importation des données d’une table.

- InsertOrSkip - Ignore une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.

- InsertOrMerge - Fusionne une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.

- InsertOrReplace - Remplace une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.

**S’applique à :** tables

### /Manifest:"manifest-file"

Spécifie le fichier manifeste pour l’importation et l’exportation de la table.

Cette option est facultative pendant l’exportation ; AzCopy génère un fichier manifeste avec un nom prédéfini si cette option n’est pas spécifiée.

Cette option est nécessaire pendant l’importation pour localiser les fichiers de données.

**S’applique à :** tables

### /SyncCopy

Indique s’il faut copier de manière synchronisée les objets blob ou les fichiers entre deux points de terminaison Azure Storage.

AzCopy utilise par défaut la copie asynchrone du côté serveur. Spécifiez cette option pour effectuer une copie synchrone, qui télécharge les objets blob ou les fichiers vers la mémoire locale et les télécharge Azure Storage.

Vous pouvez utiliser cette option pour la copie de fichiers dans le stockage d’objets blob, le stockage de fichiers ou depuis le stockage d’objets blob vers le stockage de fichiers ou vice versa.

**S’applique à :** objets blob, fichiers

### /SetContentType:"content-type"

Spécifie le type de contenu MIME pour les fichiers ou les objets blob de destination.

AzCopy définit le type de contenu d’un objet blob ou un fichier sur application/octet-stream par défaut. Vous pouvez définir le type de contenu pour tous les objets blob ou les fichiers en spécifiant explicitement une valeur pour cette option.

Si vous spécifiez cette option sans valeur, AzCopy définit chaque type de contenu d’objet blob ou de fichier en fonction de son extension de fichier.

**S’applique à :** objets blob, fichiers

### /PayloadFormat:"JSON" | "CSV"

Spécifie le format du fichier de données de table exporté.

Si cette option n’est pas spécifiée, AzCopy exporte le fichier de données de table au format JSON par défaut.

**S’applique à :** tables

## Problèmes connus et les meilleures pratiques

### Limitation des écritures simultanées lors de la copie des données

Lorsque vous copiez des objets blob ou des fichiers avec AzCopy, gardez en tête qu’une autre application peut être en train de modifier les données pendant que vous les copiez. Si possible, assurez-vous que les données que vous copiez ne sont pas modifiées pendant l’opération de copie. Par exemple, lorsque vous copiez un disque dur virtuel (VHD) associé à une machine virtuelle Azure, assurez-vous qu’aucune autre application n’est en train d’écrire sur le disque VHD. Un bon moyen pour ce faire consiste à louer la ressource à copier. Sinon, vous pouvez commencer par créer une capture instantanée du disque VHD et copier ensuite la capture instantanée.

Si vous ne pouvez pas empêcher d’autres applications d’écrire sur les objets blob ou les fichiers pendant qu’ils sont copiés, gardez en tête qu’au moment où la tâche sera terminée, les ressources copiées n’auront peut-être plus une parité complète avec les ressources source.

### Exécuter une instance de AzCopy sur un même ordinateur.
AzCopy est conçu pour optimiser l'utilisation de votre ressource de l'ordinateur afin d’accélérer le transfert de données. Nous vous recommandons d'exécuter une seule instance de AzCopy sur un même ordinateur et de spécifier l'option `/NC` si vous avez besoin de plus d'opérations simultanées. Pour plus d'informations, tapez `AzCopy /?:NC` dans la ligne de commande.

### Activer les algorithmes MD5 compatibles FIPS pour AzCopy quand vous « utilisez des algorithmes compatibles FIPS pour le chiffrement, le hachage et la signature ».
Par défaut, AzCopy utilise l’implémentation MD5 .NET pour calculer le hachage MD5 pendant la copie d’objets, mais en raison de certaines exigences de sécurité, AzCopy doit activer le paramètre MD5 compatible FIPS.

Vous pouvez créer un fichier app.config `AzCopy.exe.config` avec la propriété `AzureStorageUseV1MD5` et le mettre à part avec AzCopy.exe.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  <appSettings>
	    <add key="AzureStorageUseV1MD5" value="false"/>
	  </appSettings>
	</configuration>

Si la propriété AzureStorageUseV1MD5 a pour valeur true (valeur par défaut), AzCopy utilise l’implémentation MD5 .NET ; si elle a pour valeur false, AzCopy utilise l’algorithme MD5 compatible FIPS.

Notez que les algorithmes compatibles FIPS sont désactivés par défaut sur votre ordinateur Windows ; vous pouvez taper secpol.msc dans la fenêtre Exécuter et activer le commutateur « Chiffrement système : utilisez des algorithmes compatibles FIPS pour le chiffrement, le hachage et la signature » (Paramètres de sécurité -> Stratégies locales -> Options de sécurité).

## Étapes suivantes

Pour plus d’informations sur Azure Storage et AzCopy, reportez-vous aux ressources suivantes :

### Documentation d’Azure Storage :

- [Introduction à Azure Storage](storage-introduction.md)
- [Utilisation du stockage d’objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md)
- [Utilisation du stockage de fichiers à partir de .NET](storage-dotnet-how-to-use-files.md)
- [Utilisation du stockage de tables à partir de .NET](storage-dotnet-how-to-use-tables.md)
- [Création, gestion ou suppression d'un compte de stockage](storage-create-storage-account.md)

### Billets de blog Azure Storage :
- [Présentation de la bibliothèque de déplacement des données dans Azure Storage en version préliminaire](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy : Présentation de la copie synchrone et du type de contenu personnalisé](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy : Annonce de la disponibilité générale d'AzCopy 3.0 plus version préliminaire d'AzCopy 4.0 avec prise en charge de fichier et de table](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy : Optimisation pour les scénarios de copie à grande échelle](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy : Prise en charge des comptes de stockage géo-redondants avec accès en lecture](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy : Transfert des données avec mode reprise et jeton SAP](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy : Utilisation de copie d'objets blob sur plusieurs comptes](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy : Chargement/téléchargement des fichiers pour les objets blob Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

<!---HONumber=AcomDC_0921_2016-->