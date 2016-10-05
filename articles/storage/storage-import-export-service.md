<properties
	pageTitle="Utilisation du service Import/Export pour transférer des données vers le stockage d’objets blob | Microsoft Azure"
	description="Découvrez comment créer des tâches d’importation et d’exportation dans le portail Azure Classic pour transférer des données vers Blob Storage."
	authors="renashahmsft"
	manager="aungoo"
	editor="tysonn"
	services="storage"
	documentationCenter=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="renash;robinsh"/>


# Transfert de données vers le stockage d’objets blob à l’aide du service Microsoft Azure Import/Export

## Vue d'ensemble

Le service Azure Import/Export vous permet de transférer en toute sécurité des volumes importants de données vers Azure Blob Storage en expédiant des disques durs vers un centre de données Azure. Vous pouvez également utiliser ce service pour transférer des données depuis Azure Blob Storage vers les disques durs et les expédier vers votre site local. Ce service est utile lorsque vous souhaitez transférer plusieurs téraoctets de données vers ou depuis Azure, mais le transfert ou le téléchargement via le réseau est impossible à cause d’une bande passante limitée et de coûts de réseau élevés.

Ce service nécessite que les disques durs soient chiffrés par BitLocker pour garantir la sécurité des données. Il prend en charge les comptes de stockage classiques présents dans toutes les régions d’Azure public. Vous devez expédier les disques durs à l’un des emplacements pris en charge spécifiés plus loin dans cet article.

Dans cet article, vous allez découvrir le service Azure Import/Export et apprendre comment expédier des disques pour copier des données vers et depuis Azure Blob Storage.

> [AZURE.IMPORTANT] Vous pouvez créer et gérer des travaux d’importation et d’exportation dans le cadre d’un modèle de stockage classique à l’aide du portail Azure Classic ou des [API REST du service Import/Export](http://go.microsoft.com/fwlink/?LinkID=329099). Les comptes de stockage Resource Manager ne sont pas pris en charge actuellement.

## À quel moment dois-je utiliser le service Azure Import/Export ?

Vous pouvez envisager d’utiliser le service Azure Import/Export lorsque le chargement ou le téléchargement de données sur le réseau est trop lent ou lorsque l’obtention d’une bande passante réseau supplémentaire est coûteuse.

Vous pouvez utiliser ce service dans des scénarios tels que :

- Migration de données vers le cloud : déplacez de grandes quantités de données vers Azure rapidement et à moindre coût.
- Distribution de contenu : envoyez rapidement des données aux sites de vos clients.
- Sauvegarde : sauvegardez vos données locales dans Azure Blob Storage.
- Récupération des données : récupérez les grandes quantités de données stockées dans Blob Storage pour les transférer vers votre site local.

## Conditions préalables

Dans cette section, nous avons répertorié les composants requis pour utiliser ce service. Vérifiez-les soigneusement avant d’expédier vos disques.

### Compte de stockage

Vous devez disposer d’un abonnement Azure et d’un ou plusieurs comptes de stockage **classiques** pour pouvoir utiliser le service Import/Export. Chaque tâche ne peut servir à transférer des données que vers ou à partir d'un seul compte de stockage classique. Autrement dit, une même tâche d’importation/exportation ne peut pas englober plusieurs comptes de stockage. Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](storage-create-storage-account.md#create-a-storage-account).

### Types d’objet blob

Vous pouvez utiliser le service Azure Import/Export pour copier des données dans des objets blob de **bloc** ou de **page**. En revanche, vous ne pouvez qu’exporter les objets blob de **bloc**, de **page** ou **d’ajout** depuis le stockage Azure.

### Travail

Pour lancer le processus d'importation ou d'exportation vers ou à partir d'un stockage d'objets blob, commencez par créer une *tâche*. Il peut s'agir d'une *tâche d'importation* ou d'une *tâche d'exportation* :

- Une tâche d’importation vise à transférer des données locales vers des objets blob de votre compte de stockage Azure.
- Une tâche d'exportation vise à transférer des données stockées sous forme d'objets blob dans votre compte de stockage sur des disques durs qui vous sont ensuite expédiés.

Lorsque vous créez une tâche, vous avertissez le service Import/Export que vous allez expédier un ou plusieurs disques durs vers un centre de données Azure.

- Dans le cas d’un travail d’importation, vous expédiez des disques durs contenant vos données.
- Dans le cas d’un travail d’exportation, vous expédiez des disques durs vides.
- Vous pouvez expédier jusqu’à 10 disques durs par travail.

Vous pouvez créer un travail d’importation ou d’exportation à l’aide du [portail Azure Classic](https://manage.windowsazure.com/) ou de [l’API REST Azure Storage Import/Export](http://go.microsoft.com/fwlink/?LinkID=329099).

### Outil client

Pour créer un travail **d’importation**, la première étape consiste à préparer le disque qui sera utilisé pour l’importation. Pour ce faire, vous devez le connecter à un serveur local et exécuter l’outil client Azure Import/Export sur le serveur local. Cet outil client facilite la copie de vos données sur le disque, leur chiffrement avec BitLocker et la génération des fichiers journaux du lecteur.

Ces fichiers journaux stockent des informations sur votre travail et sur le disque, par exemple le numéro de série du disque et le nom du compte de stockage. Ce fichier journal n’est pas stocké sur le disque. Il est utilisé lors de la création de travaux d’importation. Une procédure détaillée de la création de ces travaux est fournie plus avant dans cet article.

L’outil client n’est compatible qu’avec le système d’exploitation Windows 64 bits. Pour connaître les versions de système d’exploitation prises en charge, consultez la section [Système d’exploitation](#operating-system).

Téléchargez la dernière version de [l’outil client Azure Import/Export](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Pour plus d’informations sur l’utilisation de l’outil Azure Import/Export, consultez la [Référence de l’outil WAImportExport](http://go.microsoft.com/fwlink/?LinkId=329032).

### Disques durs

Le service Import/Export ne prend en charge que les disques durs internes SATA II/III de 3,5 pouces. La capacité maximale par disque dur est de 10 To. Dans le cas des tâches d'importation, seul le premier volume de données du lecteur est traité. Il doit être formaté avec NTFS. Pour copier les données, vous pouvez connecter votre disque dur directement à l’aide d’un connecteur SATA ou de manière externe à l’aide d’un adaptateur USB SATA II/III externe. Nous vous recommandons d’utiliser un des adaptateurs USB SATA II/III externes suivants :

- Anker 68UPSATAA-02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Sharkoon QuickPort XT HC

Si le convertisseur dont vous disposez n’est pas répertorié ci-dessus, vous pouvez tenter de l’utiliser pour préparer le lecteur avec l’outil Azure Import/Export et voir s’il fonctionne avant d’en acheter un qui soit pris en charge.

> [AZURE.IMPORTANT] Les disques durs externes équipés d’un adaptateur USB intégré ne sont pas pris en charge par ce service. En outre, le disque se trouvant à l’intérieur du boîtier d’un disque dur externe est inutilisable ; vous ne devez donc pas envoyer de disques durs externes.

### Chiffrement

Les données contenues dans le disque dur doivent être chiffrées à l’aide du chiffrement de lecteur BitLocker. Cette opération permet de protéger vos données pendant qu'elles sont en transit.

Pour les travaux d’importation, le chiffrement s’effectue de deux manières différentes. La première consiste à utiliser le paramètre /encrypt dans l’outil client pendant la préparation du disque. La seconde consiste à activer manuellement le chiffrement BitLocker sur le disque et à spécifier la clé de chiffrement dans la ligne de commande de l’outil client pendant la préparation du disque.

Pour les travaux d’exportation, une fois vos données copiées sur le disque, le service chiffre le disque à l’aide de BitLocker avant de vous le renvoyer. La clé de chiffrement vous est fournie via le portail Azure Classic.

### Système d’exploitation

Vous pouvez utiliser l’un des systèmes d’exploitation 64 bits suivants pour préparer le disque dur à l’aide de l’outil Azure Import/Export avant de l’envoyer à Azure :

Windows 7 Entreprise, Windows 7 Édition intégrale, Windows 8 Professionnel, Windows 8 Entreprise, Windows 8.1 Professionnel, Windows 8.1 Entreprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Tous ces systèmes d’exploitation prennent en charge le chiffrement de lecteur BitLocker.

> [AZURE.IMPORTANT] <sup>1</sup>Si vous utilisez un ordinateur Windows 10 pour préparer votre disque dur, téléchargez la dernière version de l’outil Azure Import/Export.

### Emplacements

Le service Azure Import/Export prend en charge la copie des données vers et depuis tous les comptes de stockage publics Azure. Vous pouvez expédier les disques durs à l’un des emplacements suivants. Si votre compte de stockage se trouve dans un emplacement Azure public non spécifié ici, un autre emplacement d’expédition sera indiqué lorsque vous créerez le travail à l’aide du portail Azure Classic ou de l’API REST Import/Export.

Emplacements d’expédition pris en charge :

- Est des États-Unis

- Ouest des États-Unis

- Est des États-Unis 2

- Centre des États-Unis

- États-Unis - partie centrale septentrionale

- États-Unis - partie centrale méridionale

- Europe du Nord

- Europe de l'Ouest

- Est de l'Asie

- Asie du Sud-Est

- Est de l’Australie

- Sud-est de l’Australie

- Ouest du Japon

- Est du Japon

- Inde centrale


### Expédition

**Expédition de disques au centre de données :**

Lorsque vous créez un travail d’importation ou d’exportation, vous recevez l’adresse de l’un des emplacements auquel expédier vos disques. L’adresse d’expédition fournie dépend de l’emplacement de votre compte de stockage, mais elle ne peut être différente de l’emplacement de ce dernier.

Vous pouvez faire appel à des transporteurs comme FedEx, DHL, UPS ou La Poste pour envoyer vos disques à l’adresse d’expédition.

**Expédition de disques depuis le centre de données :**

Lorsque vous créez un travail d’importation ou d’exportation, vous devez indiquer une adresse de retour que Microsoft utilisera pour vous remettre les disques une fois le travail terminé. Veillez à fournir une adresse de retour valide pour éviter les retards de traitement.

Vous devez également indiquer un numéro de compte de transporteur FedEx ou DHL que Microsoft utilisera pour le retour des disques. Un numéro de compte FedEx est requis pour les retours de disque à partir des États-Unis et de l’Europe. Un numéro de compte DHL est requis pour les retours de disque à partir d’Asie et d’Australie. Vous pouvez créer un compte de transporteur [FedEx](http://www.fedex.com/us/oadr/) (pour les États-Unis et l’Europe) ou [DHL](http://www.dhl.com/) (pour l’Asie et l’Australie) si vous n’en avez pas. Si vous avez déjà un compte de transporteur, vérifiez qu’il est valide.

Lorsque vous expédiez vos colis, vous devez respecter les [conditions d'utilisation des services Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [AZURE.IMPORTANT] Notez que le support physique que vous expédiez devra peut-être franchir des frontières. Vous êtes responsable de l'application des lois applicables lorsque vous importez et/ou exportez vos données et supports physiques. Avant d’expédier le support physique, demandez à vos conseillers juridiques de vérifier que vos supports multimédias et données peuvent être envoyés légalement vers le centre de données identifié. Cela vous assurera d'atteindre Microsoft dans les délais. Par exemple, tout package qui franchira des frontières internationales (à l’exception de celles de l’Union européenne) doit être accompagné d’une facture commerciale. Vous pouvez imprimer une copie de la facture commerciale à partir du site web de support du transporteur. Exemple de facture commerciale : [facture commerciale DHL](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) ou [facture commerciale FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Assurez-vous que Microsoft n’a pas été indiqué comme l’exportateur.

## Fonctionnement du service Azure Import/Export

Vous pouvez transférer des données entre votre site local et Azure Blob Storage à l’aide du service Azure Import/Export en créant des travaux et en expédiant des disques durs à un centre de données Azure. Chaque disque dur expédié est associé à un seul travail. Chaque travail est associé à un seul compte de stockage. Consultez la section [Conditions préalables](#pre-requisites) pour en savoir plus sur les particularités de ce service, comme les types d’objet blob pris en charge, les types de disque, les emplacements et l’expédition.

Dans cette section, nous allons décrire globalement la procédure permettant d’importer et d’exporter des travaux. Ensuite, dans la section [Démarrage rapide](#quick-start), nous expliquons comment créer un travail d’importation et d’exportation.

### Dans un travail d’importation

Globalement, un travail d’importation comprend les opérations suivantes :

- Déterminez les données à importer et le nombre de disques dont vous avez besoin.
- Identifiez les objets blob de destination de vos données dans Blob Storage.
- Utilisez l’outil Azure Import/Export pour copier vos données sur un ou plusieurs disques durs et les chiffrez à l’aide de BitLocker.
- Créez un travail d’importation dans votre compte de stockage classique cible à l’aide du portail Azure Classic ou de l’API REST Import/Export. Si vous utilisez le portail Azure Classic, téléchargez les fichiers journaux du disque.
- Indiquez l’adresse de retour et le numéro de compte de transporteur à utiliser pour le retour des disques.
- Envoyez les disques durs à l’adresse d’expédition indiquée lors de la création du travail.
- Mettez à jour le numéro de suivi et envoyez le travail d’importation.
- Les disques sont réceptionnés et traités dans le centre de données Azure.
- Les disques sont expédiés à l’aide de votre compte de transporteur à l’adresse de retour indiquée dans le travail d’importation.

	![Figure 1 : flux d’importation de travail](./media/storage-import-export-service/importjob.png)


### Dans un travail d’exportation

Globalement, un travail d’exportation comprend les opérations suivantes :

- Déterminez les données à exporter et le nombre de disques dont vous avez besoin.
- Identifiez les objets blob source ou les chemins d’accès au conteneur de vos données dans Blob Storage.
- Créez un travail d’exportation dans votre compte de stockage source à l’aide du portail Azure Classic ou de l’API REST Import/Export.
- Indiquez les objets blob source ou les chemins d’accès au conteneur de vos données dans le travail d’exportation.
- Indiquez l’adresse de retour et le numéro de compte de transporteur à utiliser pour le retour des disques.
- Envoyez les disques durs à l’adresse d’expédition indiquée lors de la création du travail.
- Mettez à jour le numéro de suivi et envoyez le travail d’exportation.
- Les disques sont réceptionnés et traités dans le centre de données Azure.
- Les disques sont chiffrés à l’aide de BitLocker et les clés sont disponibles via le portail Azure Classic.
- Les disques sont expédiés à l’aide de votre compte de transporteur à l’adresse de retour indiquée dans le travail d’importation.

	![Figure 2 : flux d’exportation de travail](./media/storage-import-export-service/exportjob.png)

### Affichage de l’état de votre travail

Vous pouvez suivre l’état de vos tâches d’importation ou d’exportation dans le portail Classic. Accédez à votre compte de stockage dans le portail Classic, puis cliquez sur l’onglet **Import/Export**. La liste de vos tâches s'affiche sur la page. Vous pouvez filtrer la liste en fonction du statut des tâches, de leur nom, de leur type ou du numéro de suivi.

Selon la phase de traitement de votre disque, vous obtiendrez l’un des statuts suivants :

| Statut de tâche | Description |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Creating | Votre tâche a été créée, mais vous n'avez pas encore fourni vos détails d'expédition. |
| Shipping | Votre tâche a été créée et vous avez fourni vos détails d'expédition. **Remarque** : lorsque le disque est remis au centre de données Azure, l’état « Expédition » peut rester affiché pendant un certain temps. Lorsque le service démarre la copie de vos données, l’état devient « Transfert ». Pour afficher un état plus précis de votre disque, vous pouvez utiliser l’API REST Import/Export. |
| Transferring | Vos données sont en cours de transfert de votre disque dur (pour une tâche d'importation) ou vers ce dernier (pour une tâche d'exportation). |
| Packaging | Le transfert de vos données est terminé et votre disque dur est en cours de préparation pour vous être renvoyé. |
| Complete | Votre disque dur vous a été renvoyé. |

### Temps de traitement du travail

Le temps de traitement d’un travail d’importation/exportation varie en fonction de différents facteurs, tels que le délai de livraison, le type de travail, le type et la taille des données copiées, ou encore la taille des disques fournis. Le service Import/Export n’est adossé à aucun contrat de niveau de service. Vous pouvez utiliser l’API REST pour assurer le suivi détaillé de la progression du travail. Dans l’option d’affichage des travaux, un pourcentage d’achèvement terminé indique l’état d’avancement de la copie. Contactez-nous si vous avez besoin d’estimer la durée nécessaire à un travail d’importation/exportation.

### Tarification

**Frais de manipulation de disque**

Des frais de manipulation sont appliqués pour chaque disque traité dans le cadre de votre travail d’importation/exportation. Pour plus d’informations, consultez [Tarification d’Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Frais d’expédition**

Lorsque vous envoyez des disques à Azure, vous payez le coût d’expédition au transporteur. Lorsque Microsoft vous renvoie les disques, les frais d’expédition sont facturés au compte de transporteur que vous avez spécifié lors de la création du travail.

**Frais de transaction**

Aucun frais de transaction ne s’applique pour l’importation de données dans Blob Storage. Des frais de sortie standard s’appliquent lorsque les données sont exportées depuis Blob Storage. Pour plus d’informations sur les frais de transaction, consultez [Tarification - Transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/)

## Quick Start

Cette section vous explique en détail comment créer un travail d’importation et d’exportation. Vérifiez que vous remplissez toutes les [conditions préalables](#pre-requisites) avant de poursuivre.

## Création d’un travail d’importation

Créez un travail d’importation pour copier les données de vos disques durs dans votre compte de stockage Azure en envoyant un ou plusieurs disques au centre de données spécifié. Ce travail d’importation transmet des informations sur les disques durs, les données à copier, le compte de stockage cible et l’adresse d’expédition au service Azure Import/Export. La création d’un travail d’importation comprend trois étapes. Tout d’abord, préparez vos disques à l’aide de l’outil client Azure Import/Export. Ensuite, envoyez un travail d’importation à l’aide du portail Azure Classic. Enfin, envoyez les disques à l’adresse d’expédition fournie lors de la création du travail et mettez à jour les informations d’expédition dans les détails de votre travail.

> [AZURE.IMPORTANT] Vous ne pouvez envoyer qu’un seul travail par compte de stockage. Chaque disque que vous expédiez peut être importé dans un compte de stockage. Par exemple, supposons que vous souhaitiez importer les données dans deux comptes de stockage. Vous devez utiliser un disque dur pour chaque compte de stockage et créer un travail distinct par compte de stockage.

### Préparation des lecteurs

Lors de l’importation des données à l’aide du service Azure Import/Export, la première étape consiste à préparer vos disques à l’aide de l’outil client Azure Import/Export). Suivez la procédure ci-dessous pour préparer vos disques :

1.	Identifiez les données à importer. Il peut s’agir de répertoires et de fichiers autonomes sur le serveur local ou sur un partage de réseau.

2.	Déterminez le nombre de disques nécessaires en fonction de la taille totale des données. Procurez-vous le nombre requis de disques durs SATA II/III de 3,5 pouces.

3.	Identifiez le compte de stockage cible, le conteneur, les répertoires virtuels et les objets blob.

4.	Déterminez les répertoires et/ou les fichiers à copier sur chaque disque dur.

5.	Utilisez [l’outil Azure Import/Export](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) pour copier vos données sur un ou plusieurs disques durs.

	- L’outil Azure Import/Export crée des sessions pour copier vos données sur les disques durs. Au cours d’une session de copie, l’outil peut copier un répertoire et ses sous-répertoires, ou un seul fichier.

	- Plusieurs sessions sont nécessaires si votre source de données comprend plusieurs répertoires.

	- Chaque disque dur que vous préparez requiert au moins une session de copie.

6.	Vous pouvez spécifier le paramètre /encrypt pour activer le chiffrement BitLocker sur le disque dur. L’autre solution consiste à activer le chiffrement BitLocker manuellement sur le disque dur et à fournir la clé lors de l’exécution de l’outil.

7.	L’outil Azure Import/Export génère un fichier journal lors de la préparation de chaque disque. Le fichier journal de lecteur est stocké sur votre ordinateur local, et non sur le lecteur lui-même. Vous le téléchargerez lors de la création du travail d’importation. Le fichier journal d'un lecteur contient son ID, la clé BitLocker, ainsi que d'autres informations sur le lecteur. **Important** : chaque disque dur que vous préparez entraîne la création d’un fichier journal. Lorsque vous créez le travail d’importation à l’aide du portail Azure Classic, vous devez télécharger tous les fichiers journaux des disques qui font partie de cette tâche d’importation. Les disques sans fichier journal ne sont pas traités.

8.	Ne modifiez ni les données ni le fichier journal après la préparation du disque.

Voici les commandes et des exemples utiles pour préparer le disque dur à l’aide de l’outil client Azure Import/Export.

Commande PrepImport de l’outil client Azure Import/Export pour copier un répertoire pendant la première session de copie :

	WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Exemple :**

Dans l’exemple ci-dessous, nous copions tous les fichiers et sous-répertoires de « H:\\Video » dans le disque dur monté sur « X: ». Les données sont importées dans le compte de stockage cible spécifié par la clé de compte de stockage et dans le conteneur de stockage appelé « video ». Si le compte de stockage n’existe pas, il est créé. Cette commande formate et chiffre le disque dur cible.

	WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Commande PrepImport de l’outil client Azure Import/Export pour copier un répertoire pendant les sessions de copie suivantes :

	WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Pour les sessions de copie suivantes sur le même disque dur, spécifiez le même nom de fichier journal et indiquez un nouvel ID de session. Il n’est pas nécessaire d’indiquer à nouveau la clé du compte de stockage et le disque cible, ni de formater ou chiffrer le disque. Dans cet exemple, nous copions le dossier « H:\\Photo » et ses sous-répertoires sur le même disque cible, dans le conteneur de stockage appelé « photo ».

	WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Commande PrepImport de l’outil client Azure Import/Export permettant de copier un fichier pendant la première session de copie :

	WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Commande PrepImport de l’outil client Azure Import/Export permettant de copier un fichier pendant les sessions de copie suivantes :

	WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Attention** : par défaut, les données sont importées sous la forme d’objets blob de blocs. Vous pouvez utiliser le paramètre /BlobType pour importer les données sous la forme d’objets blob de page. Par exemple, si vous importez des fichiers de disque dur virtuel qui seront montés comme des disques sur une machine virtuelle Azure, vous devez les importer en tant qu’objets blob de page. Si vous ne savez pas quel type d’objet blob utiliser, vous pouvez spécifier /blobType:auto pour nous aider à déterminer le type approprié. Dans ce cas, tous les fichiers vhd et vhdx sont importés en tant qu’objets blob de page, le reste étant importé sous la forme d’objets blob de bloc.

Pour en savoir plus sur l’utilisation de l’outil client Azure Import/Export, consultez [Préparation des disques durs pour un travail d’importation](https://msdn.microsoft.com/library/dn529089.aspx).

Pour une description étape par étape, consultez [Exemple de flux de travail pour préparer des disques durs à un travail d’importation](https://msdn.microsoft.com/library/dn529097.aspx).

### Création de la tâche d'importation

1.	Après avoir préparé votre disque, accédez à votre compte de stockage sur le [portail Classic](https://manage.windowsazure.com) et affichez le tableau de bord. Sous **Quick Glance**, cliquez sur **Create an Import Job**. Vérifiez les étapes et cochez la case pour indiquer que vous avez préparé votre disque et que son fichier journal est disponible.

2.	À l’étape 1, indiquez les coordonnées de la personne responsable de ce travail d’importation ainsi qu’une adresse de retour valide. Pour enregistrer des données de journal détaillées pour la tâche d'importation, activez l'option **Enregistrer le journal détaillé dans le conteneur d'objets blob 'waimportexport'**.

3.	À l’étape 2, téléchargez les fichiers journaux que vous avez obtenus à l’étape de préparation du disque. Vous devez télécharger un fichier pour chaque disque préparé.

	![Créer une tâche d'importation - Étape 3](./media/storage-import-export-service/import-job-03.png)

4.	À l’étape 3, indiquez une description du travail d’importation. Notez que le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, qu'il doit commencer par une lettre et qu'il ne peut pas contenir d'espaces. Le nom que vous choisissez vous servira à suivre vos travaux pendant et après son exécution.

	Sélectionnez ensuite la région du centre de données dans la liste. Cette dernière indique à quel centre de données et à quelle adresse vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.

5. 	À l’étape 4, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier les lecteurs une fois la tâche d’importation terminée.

	Si vous possédez un numéro de suivi, entrez-le après avoir sélectionné le transporteur dans la liste.

	Si vous n'avez pas encore de numéro de suivi, choisissez **I will provide my shipping information for this import job once I have shipped my package**, puis terminez le processus d'importation.

6. Pour entrer votre numéro de suivi après avoir expédié votre colis, revenez à la page **Import/Export** de votre compte de stockage dans le portail Classic, sélectionnez votre travail dans la liste, puis choisissez **Informations d’expédition**. Parcourez l’Assistant, puis entrez votre numéro de suivi à l’étape 2.

	Si le numéro de suivi n'est pas mis à jour dans les 2 semaines de création de la tâche, cette dernière expirera.

	Si la tâche a le statut Création, Expédition ou Transfert, vous pouvez également mettre à jour le numéro de compte du transporteur à l'étape 2 de l'Assistant. Une fois que la tâche a le statut Emballage, vous ne pouvez plus mettre à jour le numéro de compte de transporteur correspondant.

7. Vous pouvez suivre l’état d’avancement de votre travail sur le tableau de bord du portail. Pour connaître la signification de chaque état de travail dans la section précédente, consultez [Affichage de l’état de votre travail](#viewing-your-job-status).

## Création d’une tâche d’exportation

Créez une tâche d’exportation pour avertir le service Import/Export que vous allez expédier un ou plusieurs disques vides au centre de données, de sorte que les données puissent être exportées de votre compte de stockage vers les disques, qui vous seront ensuite renvoyés.

### Préparation des disques

Les vérifications préalables suivantes sont recommandées pour préparer vos disques en vue d’un travail d’exportation :

1. Vérifiez le nombre de disques requis à l'aide de la commande PreviewExport de l’outil Azure Import/Export. Pour plus d’informations, consultez [Aperçu de l’utilisation des lecteurs pour un travail d’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx). Celle-ci vous permet d'afficher un aperçu de l'utilisation du disque pour les objets BLOB que vous avez sélectionnés, en fonction de la taille des disques que vous voulez utiliser.

2. Vérifiez que le disque dur expédié en vue de la tâche d’exportation est accessible en lecture/écriture.

### Création du travail d’importation

1. 	Pour créer un travail d’exportation, accédez à votre compte de stockage sur le [portail Classic](https://manage.windowsazure.com), puis affichez le tableau de bord. Sous **Aperçu rapide**, cliquez sur **Créer un travail d’exportation**, puis parcourez les étapes de l’Assistant.

2. 	À l’étape 2, fournissez les coordonnées de la personne responsable de cette tâche d’exportation. Pour enregistrer des données de journal détaillées pour la tâche d'exportation, activez l'option **Enregistrer le journal détaillé dans le conteneur d'objets blob 'waimportexport'**.

3.	À l'étape 3, indiquez les données d'objets blob que vous souhaitez exporter de votre compte de stockage vers le ou les lecteurs vides. Vous pouvez choisir d’exporter toutes les données d’objets blob contenues dans le compte de stockage ou indiquer les objets blob ou ensembles d’objets blob à exporter.

	Pour spécifier un objet blob à exporter, utilisez le sélecteur **Equal To**, puis indiquez le chemin d’accès relatif de l’objet blob en le faisant précéder du nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.

	Pour spécifier tous les objets blob commençant par un préfixe, utilisez le sélecteur **Starts With**, puis spécifiez le préfixe en le faisant précéder d'une barre oblique (« / »). Il peut s'agir du préfixe du nom de conteneur, du nom de conteneur complet, ou du nom de conteneur complet suivi du préfixe du nom d'objet blob.

	Le tableau suivant présente des exemples de chemins d’accès d’objet blob valides :

	Sélecteur|Chemin d'accès d'objet blob|Description
	---|---|---
	Commence par|/|Exporte tous les objets blob présents dans le compte de stockage.
	Commence par|/$root/|Exporte tous les objets blob présents dans le conteneur racine.
	Commence par|/book|Exporte tous les objets blob présents dans un conteneur commençant par le préfixe **book**.
	Starts With|/music/|Exporte tous les objets blob présents dans le conteneur **music**.
	Commence par|/music/love|Exporte tous les objets blob présents dans le conteneur **music** qui commence par le préfixe **love**.
	Equal To|$root/logo.bmp|Exporte l'objet blob **logo.bmp** présent dans le conteneur racine.
	Equal To|videos/story.mp4|Exporte l'objet blob **story.mp4** présent dans le conteneur **videos**.

	Vous devez indiquer les chemins d’accès aux objets blob dans des formats valides pour éviter les erreurs de traitement, comme illustré dans cette capture d’écran.

	![Créer une tâche d'exportation - Étape 3](./media/storage-import-export-service/export-job-03.png)


4.	À l'étape 4, attribuez un nom descriptif à la tâche d'exportation. Le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, il doit commencer par une lettre et ne peut pas contenir d'espaces.

	La région du centre de données indique à quel centre de données vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.

5. 	À l'étape 5, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier vos lecteurs une fois la tâche d'exportation terminée.

	Si vous possédez un numéro de suivi, entrez-le après avoir sélectionné le transporteur dans la liste.

	Si vous n'avez pas encore de numéro de suivi, choisissez **Je fournirai mes informations d'expédition pour ce travail d'exportation après envoi de mon colis**, puis terminez le processus d'exportation.

6. Pour entrer votre numéro de suivi après avoir expédié votre colis, revenez à la page **Import/Export** de votre compte de stockage dans le portail Classic, sélectionnez votre travail dans la liste, puis choisissez **Informations d’expédition**. Parcourez l’Assistant, puis entrez votre numéro de suivi à l’étape 2.

	Si le numéro de suivi n'est pas mis à jour dans les 2 semaines de création de la tâche, cette dernière expirera.

	Si la tâche a le statut Création, Expédition ou Transfert, vous pouvez également mettre à jour le numéro de compte du transporteur à l'étape 2 de l'Assistant. Une fois que la tâche a le statut Emballage, vous ne pouvez plus mettre à jour le numéro de compte de transporteur correspondant.

	> [AZURE.NOTE] Si l’objet blob à exporter est en cours d’utilisation au moment de la copie sur le disque dur, le service d’importation/exportation Azure prend un instantané de l’objet blob et copie la capture instantanée.

7.	Vous pouvez suivre l’état d’avancement de votre travail sur le tableau de bord du portail Classic. Pour connaître la signification de chaque état de travail dans la section précédente, consultez Affichage de l’état de votre travail.

8.	Après avoir réceptionné les disques avec vos données exportées, vous pouvez afficher et copier les clés BitLocker générées par le service pour votre disque. Accédez à votre compte de stockage dans le portail Classic, puis cliquez sur l’onglet Import/Export. Sélectionnez votre tâche d’exportation dans la liste, puis cliquez sur le bouton Afficher les clés. Les clés BitLocker s’affichent comme ci-dessous :

	![Afficher les clés BitLocker pour une tâche d'exportation](.\media\storage-import-export-service\export-job-bitlocker-keys.png)

Accédez à la section FAQ ci-dessous, car elle aborde les questions les plus courantes concernant l’utilisation de ce service.

## Forum Aux Questions (FAQ) ##


**Combien de temps faut-il pour copier les données lorsque mon ou mes disques sont parvenus au centre de données ?**

Le temps nécessaire à la copie varie en fonction de différents facteurs, tels que le type de travail, le type et la taille des données copiées, la taille des disques fournis et la charge de travail existante. Cela peut aller de quelques jours à quelques semaines. Il est donc difficile de fournir une estimation générale. Le service tente d’optimiser votre travail en copiant plusieurs disques en parallèle dans la mesure du possible. Si votre travail d’importation/exportation est urgent, contactez-nous pour obtenir une estimation.

**Quand dois-je utiliser le service Azure Import/Export ?** On peut considérer l’utilisation d’Azure Import/Export si le chargement ou le téléchargement par le biais d’un réseau nécessite une durée estimée de plus de 7 jours. Vous pouvez calculer le temps nécessaire à l’aide de n’importe quel programme de calcul en ligne ou en [téléchargeant](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/archive/master.zip) celui situé dans notre exemple d’API REST Azure Import Export dans le référentiel d’exemples Azure ([Calculatrice de vitesse de transfert de données](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html)). Cela n’est pas un calcul exact, mais uniquement une indication approximative.

**Puis-je utiliser le service Azure Import/Export avec un compte de stockage Resource Manager ?**

Non, vous ne pouvez pas copier les données vers ou depuis un compte de stockage Resource Manager directement à l’aide du service Azure Import/Export. Le service ne prend en charge que les comptes de stockage classiques. Le compte de stockage Resource Manager sera bientôt pris en charge. Comme alternative, vous pouvez importer des données dans un compte de stockage classique et les faire migrer vers votre compte de stockage Resource Manager à l’aide de l’utilitaire [AzCopy](storage-use-azcopy.md) ou de CopyBlob.

**Puis-je copier des fichiers Azure à l’aide du service Azure Import/Export ?**

Non, le service Azure Import/Export ne prend en charge que les objets blob de bloc et de page. Les autres types de stockage, y compris les fichiers Azure, les tables et les files d’attente, ne sont pas pris en charge.

**Le service Azure Import/Export est-il disponible pour les abonnements de fournisseur de services de chiffrement ?**

Non, le service Azure Import/Export ne prend pas en charge les abonnements de fournisseur de services de chiffrement. Pour l’instant, du moins.

**Puis-je ignorer l’étape de préparation de disque pour un travail d’importation ou préparer un disque sans effectuer la copie ?**

Tout disque que vous souhaitez expédier en vue d’importer des données doit être préparé à l’aide de l’outil client Azure Import/Export. Vous devez utiliser l’outil client pour copier les données sur le disque.

**Ai-je besoin d’effectuer la préparation du disque lors de la création d'une tâche d'exportation ?**

Non, mais certaines vérifications préalables sont recommandées. Vérifiez le nombre de disques requis à l'aide de la commande PreviewExport de l’outil Azure Import/Export. Pour plus d’informations, consultez [Aperçu de l’utilisation des lecteurs pour un travail d’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx). Celle-ci vous permet d'afficher un aperçu de l'utilisation du disque pour les objets BLOB que vous avez sélectionnés, en fonction de la taille des disques que vous voulez utiliser. Vérifiez également que le disque dur qui sera utilisé pour le travail d’exportation est accessible en lecture et en écriture.

**Que se passe-t-il si j’envoie par accident un disque dur non conforme au type pris en charge ?**

Le centre de données Azure vous renvoie le lecteur non conforme au type pris en charge. Si seule une partie des lecteurs contenus dans le colis respecte la configuration requise, ceux-ci sont traités ; ceux qui ne la respectent vous sont renvoyés.

**Puis-je annuler une tâche ?**

Vous pouvez annuler une tâche dont le statut est Creating ou Shipping.

**Pendant combien de temps l’état des travaux terminés est-il consultable dans le portail Azure Classic ?**

L’état des travaux terminés est visible pendant 90 jours. Au-delà de ce délai, les travaux terminés sont supprimés.

**Que faire si je souhaite importer ou exporter plus de 10 lecteurs ?**

Le service Import/Export limite chaque tâche d’importation ou d’exportation au référencement de 10 lecteurs. Si vous souhaitez en expédier plus, vous pouvez créer plusieurs tâches. Les disques associés au même travail doivent être expédiés ensemble dans le même colis.

**Puis-je utiliser un adaptateur USB SATA qui ne figure pas dans la liste recommandée ?**

Si le convertisseur dont vous disposez n’est pas répertorié ci-dessus, vous pouvez tenter de l’utiliser pour préparer le lecteur avec l’outil Azure Import/Export et voir s’il fonctionne avant d’en acheter un qui soit pris en charge.

**Le service formate-t-il les disques avant de les renvoyer ?**

Non. Tous les disques sont chiffrés avec BitLocker.

**Est-il possible d’acheter des lecteurs auprès de Microsoft pour des tâches d’importation/exportation ?**

Non. Vous devez expédier vos propres lecteurs, aussi bien pour les tâches d'importation que pour les tâches d'exportation.

**Une fois la tâche d’importation terminée, à quoi mes données ressembleront-elles dans le compte de stockage ? Mon arborescence sera-t-elle préservée ?**

Lorsque vous préparez un disque dur pour un travail d’importation, le paramètre /dstdir: indique la destination. Il s’agit du conteneur cible dans le compte de stockage dans lequel les données du disque dur sont copiées. Dans ce conteneur cible, des répertoires virtuels sont créés pour les dossiers du disque dur et des objets blob sont créés pour les fichiers.

**Si le disque contient des fichiers déjà existants dans mon compte de stockage, le service va-t-il remplacer les objets blob dans mon compte de stockage ?**

Lors de la préparation du disque, vous pouvez spécifier si les fichiers cibles doivent être remplacés ou ignorés à l’aide du paramètre /Disposition:< rename|no-overwrite|overwrite>. Par défaut, le service ne remplace pas les objets blob mais renomme les nouveaux fichiers.

**L’outil client Azure Import/Export est-il compatible avec un système d’exploitation Windows 32 bits ?** Non. L’outil client n’est compatible qu’avec le système d’exploitation Windows 64 bits. Pour obtenir la liste complète des versions de système d’exploitation pris en charge, consultez la section [Conditions préalables](#pre-requisites).

**Dois-je ajouter quelque chose en plus du disque dur dans mon colis ?**

Incluez uniquement vos disques durs. N’incluez pas d’accessoires tels que des câbles d’alimentation ou USB.

**Dois-je expédier mes disques à l’aide de FedEx ou DHL ?**

Vous pouvez expédier des disques au centre de données à l’aide d’un transporteur connu comme FedEx, DHL, UPS ou La Poste. Toutefois, pour que les disques du centre de données puissent vous être retournés, vous devez fournir un numéro de compte FedEx aux États-Unis et dans l’UE, ou un numéro de compte DHL en Asie et en Australie.

**Existe-t-il des restrictions à l’expédition de mon disque dur hors des frontières ?**

Notez que le support physique que vous expédiez devra peut-être franchir des frontières. Vous êtes responsable de l'application des lois applicables lorsque vous importez et/ou exportez vos données et supports physiques. Avant d'expédier le support physique, demandez à vos conseillers juridiques de vérifier que vos supports multimédias et données peuvent être envoyés légalement vers le centre de données identifié. Cela vous assurera d'atteindre Microsoft dans les délais.

**Lorsque vous créez une tâche, l’adresse d’expédition désigne un emplacement différent de celui de mon compte de stockage. Que dois-je faire ?**

Certains emplacements de compte de stockage sont associés à d’autres emplacements d’expédition. Les emplacements d’expédition auparavant disponibles peuvent être temporairement associés à d’autres emplacements. Vérifiez toujours l’adresse d’expédition fournie lors de la création du travail avant d’expédier vos disques.

**Pourquoi le portail Azure Classic indique-t-il l’état « Expédition », alors que le site web du transporteur indique que mon colis est livré ?**

Sur le portail Classic, l’état passe d’Expédition à Transfert au début du traitement du disque. Si votre disque est parvenu au site, mais qu’il n’a pas encore été traité, l’état de votre travail indique Expédition.

**Lors de l’expédition de mon disque, l’opérateur me demande le nom du contact et le numéro de téléphone du centre de données. Que dois-je indiquer ?**

Le numéro de téléphone vous est fourni lors de la création du travail. Si vous avez besoin du nom d’un contact, contactez-nous au waimportexport@microsoft.com et nous vous fournirons ces informations.

**Puis-je utiliser le service Azure Import/Export pour copier des boîtes aux lettres PST et des données SharePoint vers O365 ?**

Consultez la rubrique [Importer des fichiers PST ou des données SharePoint dans Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Puis-je utiliser le service Azure Import/Export pour copier mes sauvegardes en mode hors connexion sur le service Azure Backup ?**

Consultez la rubrique [Flux de travail de la sauvegarde hors connexion dans Azure Backup](../backup/backup-azure-backup-import-export.md).

## Voir aussi :

- [Configuration de l’outil client Azure Import/Export](https://msdn.microsoft.com/library/dn529112.aspx)

- [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

- [Exemple d’API REST Azure Import Export](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

<!---HONumber=AcomDC_0921_2016-->