<properties 
	pageTitle="Créer des fonctionnalités pour les données de stockage d’objets blob Azure à l’aide de Pandas | Microsoft Azure" 
	description="Comment créer des fonctionnalités pour les données stockées dans un conteneur d’objets blob Azure avec le package Python Pandas." 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="bradsev;garye" />

#Créer des fonctionnalités pour les données de stockage d’objets blob Azure à l’aide de Pandas

Ce document montre comment créer des fonctionnalités pour les données stockées dans un conteneur d’objets blob Azure à l’aide du package Python [Pandas](http://pandas.pydata.org/). Après avoir décrit le chargement des données dans une trame de données Pandas, il montre comment générer des fonctionnalités catégorielles à l’aide de scripts Python avec des valeurs d’indicateur et des caractéristiques de compartimentage.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]
Ce **menu** pointe vers des rubriques qui expliquent comment créer des fonctionnalités pour les données dans différents environnements. Cette tâche est une étape du [processus Cortana Analytics (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## Composants requis
Cet article part du principe que vous avez :
* créé un compte de stockage d’objets blob Azure et que vous y avez stocké vos données. Si vous avez besoin d’aide pour configurer un compte, consultez [Créer un compte Azure Storage](../hdinsight-get-started.md#storage).


## Chargement des données dans une trame de données Pandas
Pour explorer et manipuler un jeu de données, celui-ci doit être téléchargé depuis la source Blob vers un fichier local qui peut ensuite être chargé dans une trame de données Pandas. Voici les étapes à suivre pour cette procédure :

1. Téléchargez les données à partir du blob Azure avec l’exemple de code Python à l’aide du service du blob. Remplacez la variable dans le code ci-dessous par vos propres valeurs : 

	    from azure.storage.blob import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Lisez les données du fichier téléchargé dans une table Pandas.

	    #LOCALFILE is the file path	
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

Vous êtes maintenant prêt à explorer les données et à générer des fonctionnalités sur cet ensemble de données.
	
##<a name="blob-featuregen"></a>Génération de fonctionnalités
	
Les deux sections suivantes indiquent comment générer des fonctionnalités catégorielles avec des valeurs d’indicateur et des caractéristiques de compartimentage à l’aide de scripts Python.

###<a name="blob-countfeature"></a>Génération de caractéristiques à partir de valeurs d’indicateur

Pour créer des caractéristiques de catégorie, procédez comme suit :

1. Examinez la distribution de la colonne de catégorie :
	
		dataframe_blobdata['<categorical_column>'].value_counts()

2. Générez les valeurs d’indicateur pour chacune des valeurs de colonne :

		#generate the indicator column
		dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Créez une jointure entre la colonne d’indicateurs et le bloc de données d’origine :
 
			#Join the dummy variables back to the original data frame
			dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Supprimez la variable d’origine :

		#Remove the original column rate_code in df1_with_dummy
		dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
	
###<a name="blob-binningfeature"></a>Génération de caractéristiques de compartimentage

Pour générer des fonctionnalités compartimentées, procédez comme suit :

1. Ajoutez une séquence de colonnes pour compartimenter une colonne numérique :
 
		bins = [0, 1, 2, 4, 10, 40]
		dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
		
2. Convertissez le compartimentage en une séquence de variables booléennes :

		dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
	
3. Enfin, créez une jointure entre les variables factices et le bloc de données d’origine :

		dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)	

##<a name="sql-featuregen"></a>Réécriture de données dans l’objet blob Azure et exploitation dans Azure Machine Learning

Après avoir exploré les données et créé les fonctionnalités nécessaires, vous pouvez charger les données (exemples ou caractéristiques) dans un objet blob Azure et les exploiter dans Azure Machine Learning en procédant comme suit : notez qu’il est également possible de créer d’autres fonctionnalités dans Azure Machine Learning Studio.
1. Écrivez le bloc de données dans le fichier local.

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Chargez les données dans le blob Azure, en procédant comme suit :

		from azure.storage.blob import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. À présent, les données sont lisibles à partir de l’objet blob à l’aide du module [Importer des données](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) d’Azure Machine Learning comme le montre l’écran ci-dessous :
 
![objet blob de lecteur](./media/machine-learning-data-science-process-data-blob/reader_blob.png)


 

<!---HONumber=AcomDC_0921_2016-->