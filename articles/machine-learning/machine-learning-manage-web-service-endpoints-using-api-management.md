<properties
	pageTitle="Apprenez à gérer les services web AzureML à l’aide de la gestion des API | Microsoft Azure"
	description="Guide montrant comment gérer les services web AzureML à l’aide de la gestion des API"
	keywords="apprentissage automatique,gestion des api"
	services="machine-learning"
	documentationCenter=""
	authors="roalexan"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="roalexan" />


# Gestion des services web AzureML à l’aide de Gestion des API

##Vue d'ensemble

Ce guide décrit la prise en main rapide de la gestion de vos services web AzureML grâce à Gestion des API.

##Qu’est-ce que Gestion des API Azure ?

Gestion des API Azure est un service Azure qui vous permet de gérer vos points de terminaison d’API REST en définissant l’accès utilisateur, la limitation d’utilisation et la surveillance du tableau de bord. Cliquez [ici](https://azure.microsoft.com/services/api-management/) pour plus d’informations sur Gestion des API Azure. Cliquez [ici](../api-management/api-management-get-started.md) pour débuter avec Gestion des API Azure. Cet autre guide, sur lequel ce guide est basé, couvre plus de rubriques, notamment les configurations de notification, la tarification, la gestion des réponses, l’authentification des utilisateurs, la création de produits, les abonnements pour développeur et le tableau de bord d’utilisation.

##Présentation d’AzureML

AzureML est un service Azure d’apprentissage automatique qui vous permet de facilement générer, déployer et partager des solutions d’analyse avancée. Cliquez [ici](https://azure.microsoft.com/services/machine-learning/) pour plus d’informations sur AzureML.

##Composants requis

Pour utiliser ce guide, il vous faut :

* Un compte Azure. Si vous n’avez pas de compte Azure, cliquez [ici](https://azure.microsoft.com/pricing/free-trial/) pour plus d’informations sur la création d’un compte d’essai gratuit.
* Un compte AzureML. Si vous n’avez pas de compte AzureML, cliquez [ici](https://studio.azureml.net/) pour plus d’informations sur la création d’un compte d’essai gratuit.
* L’espace de travail, le service et l’api\_key pour l’expérience AzureML déployés sous forme de service web. Cliquez [ici](machine-learning-create-experiment.md) pour plus d’informations sur la création d’une expérience AzureML. Cliquez [ici](machine-learning-publish-a-machine-learning-web-service.md) pour plus d’informations sur le déploiement d’une expérience AzureML comme service web. L’annexe A contient également des instructions sur la façon de créer et de tester une expérience AzureML simple et de la déployer en tant que service web.

##Création d'une instance Gestion des API

Vous trouverez ci-dessous les étapes d’utilisation de Gestion des API pour gérer votre service web AzureML. Créez d’abord une instance de service. Connectez-vous au [Portail Classic](https://manage.windowsazure.com/) et cliquez sur **Nouveau** > **App Services** > **Gestion des API** > **Créer**.

![create-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

Spécifiez une **URL** unique. Ce guide utilise **demoazureml**. Vous devez choisir un nom différent. Choisissez l’**abonnement** et la **région** souhaités pour votre instance de service. Une fois vos sélections effectuées, cliquez sur le bouton Suivant.

![create-service-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

Spécifiez une valeur pour le **nom de l’organisation**. Ce guide utilise **demoazureml**. Vous devez choisir un nom différent. Entrez votre adresse de messagerie dans le champ **Adresse de messagerie de l’administrateur**. Cette adresse de messagerie est utilisée pour les notifications provenant du système Gestion des API.

![create-service-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

Cliquez sur la coche pour créer votre instance de service. *Jusqu’à trente minutes sont nécessaires pour créer un service*.

##Création de l’API

Une fois l’instance de service créée, l’étape suivante consiste à créer une API. Une API se compose d’un ensemble d’opérations pouvant être appelées à partir d’une application cliente. Les opérations de l’API sont transmises par proxy aux services web existants. Ce guide crée des API comme proxy pour les services web AzureML RRS et BES existants.

Les API sont créées et configurées à partir du portail des éditeurs d’API, accessible via le portail Azure Classic. Pour atteindre le portail des éditeurs, sélectionnez votre instance de service.

![select-service-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

Cliquez sur **Gérer** dans le portail Azure Classic de votre service Gestion des API.

![manage-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

Cliquez sur **API** dans le menu **Gestion des API** à gauche, puis sur **Ajouter des API**.

![api-management-menu](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

Tapez **API de démonstration AzureML** comme **nom de l’API web**. Tapez **https://ussouthcentral.services.azureml.net** comme **URL du service Web**. Tapez **azureml-demo** comme **suffixe d’URL de l’API web**. Cochez **HTTPS** comme schéma **d’URL de l’API Web**. Sélectionnez **Starter** comme **produit**. Quand vous avez terminé, cliquez sur **Enregistrer** pour créer l’API.

![add-new-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##Ajout des opérations

Cliquez sur **Ajouter une opération** pour ajouter des opérations à l’API.

![add-operation](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

La fenêtre **Nouvelle opération** s'affiche et l'onglet **Signature** est sélectionné par défaut.

##Ajout d’une opération RRS

Créez une opération pour le service AzureML RRS. Sélectionnez **POST** comme **verbe HTTP**. Saisissez **/workspaces/{espace de travail}/services/{service}/execute?api-version={versionapi}&details={détails}** comme **modèle d’URL**. Saisissez **Exécution RRS** comme **nom d’affichage**.

![add-rrs-operation-signature](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Cliquez sur **Réponses** > **AJOUTER** sur la gauche et sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

![add-rrs-operation-response](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##Ajout d’opérations BES

Les captures d’écran ne sont pas incluses pour les opérations BES, car elles sont très similaires à celles de l’ajout de l’opération RRS.

###Envoi (et non démarrage) d’un travail d’exécution de lot

Cliquez sur **Ajouter une opération** pour ajouter l’opération BES AzureML à l’API. Sélectionnez **POST** comme **verbe HTTP**. Saisissez **/workspaces/{espace de travail}/services/{service}/jobs?api-version={versionapi}** comme **modèle d’URL**. Saisissez **Envoi BES** comme **nom d’affichage**. Cliquez sur **Réponses** > **AJOUTER** sur la gauche et sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

###Démarrage d’une tâche d’exécution de lot

Cliquez sur **Ajouter une opération** pour ajouter l’opération BES AzureML à l’API. Sélectionnez **POST** comme **verbe HTTP**. Saisissez **/workspaces/{espace de travail}/services/{service}/jobs/{jobid}/start?api-version={versionapi}** comme **modèle d’URL**. Saisissez **Démarrage BES** comme **nom d’affichage**. Cliquez sur **Réponses** > **AJOUTER** sur la gauche et sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

###Obtention de l’état ou du résultat d’une tâche d’exécution de lot

Cliquez sur **Ajouter une opération** pour ajouter l’opération BES AzureML à l’API. Sélectionnez **GET** comme **verbe HTTP**. Saisissez **/workspaces/{espace de travail}/services/{service}/jobs/{idjob}?api-version={versionapi}** comme **modèle d’URL**. Saisissez **État BES** comme **nom d’affichage**. Cliquez sur **Réponses** > **AJOUTER** sur la gauche et sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

###Suppression d’une tâche d’exécution de lot

Cliquez sur **Ajouter une opération** pour ajouter l’opération BES AzureML à l’API. Sélectionnez **DELETE** comme **verbe HTTP**. Saisissez **/workspaces/{espace de travail}/services/{service}/jobs/{idjob}?api-version={versionapi}** comme **modèle d’URL**. Saisissez **Suppression BES** comme **nom d’affichage**. Cliquez sur **Réponses** > **AJOUTER** sur la gauche et sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

##Appel d'une opération à partir du portail des développeurs

Les opérations peuvent être directement appelées depuis le portail des développeurs, ce qui permet d'afficher et de tester les opérations d'une API. Dans cette étape du guide, vous appelez la méthode **Exécution RRS** qui a été ajoutée à l’**API de démonstration AzureML**. Cliquez sur **Portail des développeurs** dans le menu en haut à droite du portail Classic.

![developer-portal](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

Cliquez sur **API** dans le menu en haut, puis sur **API de démonstration AzureML** pour afficher les opérations disponibles.

![demoazureml-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Sélectionnez **Exécution RRS** pour l’opération. Cliquez sur **Essayer**.

![try-it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

Pour les paramètres de requête, saisissez votre **espace de travail**, le **service**, **2.0** dans **versionapi** et **true** dans **Détails**. Vous pouvez trouver vos **espace de travail** et **service** dans le tableau de bord du service web AzureML (voir **Test du service web** dans l’annexe A).

Pour les en-têtes de requête, cliquez sur **Ajouter en-tête** et saisissez **Content-Type** et **application/json**, puis cliquez sur **Ajouter en-tête** et saisissez **Autorisation** et **Porteur <VOTRE CLÉ D’API DE SERVICE AZUREML>**. Vous pouvez trouver votre **clé API** dans le tableau de bord du service web AzureML (voir **Test du service web** dans l’annexe A).

Saisissez **{»Inputs»: {»input1»: {»ColumnNames»: [»Col2»], «Values»: [[»C’est une belle journée»]]}}, «GlobalParameters»: {}}** dans le corps de la requête.

![azureml-demo-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Cliquez sur **Envoyer**.

![send](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

Après l'appel d'une opération, le portail des développeurs affiche l'**URL requise** à partir du service principal, l'**état de la réponse**, les **en-têtes de la réponse** et tout **contenu de la réponse**.

![response-status](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##Annexe A - Création et test d’un service web AzureML simple

###Création de l’expérience

Vous trouverez ci-dessous les étapes de création d’une expérience AzureML simple et de son déploiement comme service web. Le service web prend comme entrée une colonne de texte arbitraire et retourne un ensemble de fonctionnalités représentées sous forme d’entiers. Par exemple :

Texte | Texte haché
--- | ---
C’est une belle journée | 1 1 2 2 0 2 0 1

Tout d’abord, à l’aide du navigateur de votre choix, accédez à [https://studio.azureml.net/](https://studio.azureml.net/) et entrez vos informations d’identification pour vous connecter. Ensuite, créez une nouvelle expérience vide.

![search-experiment-templates](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Renommez-la **SimpleFeatureHashingExperiment**. Développez **Datasets enregistrés** et faites glisser **Critiques de livres d’Amazon** sur votre expérience.

![simple-feature-hashing-experiment](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Développez **Transformation des données** et **Manipulation** et faites glisser **Sélectionner des colonnes dans le jeu de données** sur votre expérience. Connectez **Critiques de livres d’Amazon** à **Sélectionner des colonnes dans le jeu de données**.

![select-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

Cliquez sur **Sélectionner des colonnes dans le jeu de données**, puis sur **Exécuter le sélecteur de colonne** et sélectionnez **Col2**. Cliquez sur la coche pour appliquer ces modifications.

![select-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

Développez **Analyse de texte** et faites glisser **Fonction de hachage** sur l’expérience. Connectez **Sélectionner des colonnes dans le jeu de données** à **Fonction de hachage**.

![connect-project-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Saisissez **3** pour la **Taille de bits de hachage**. Cela crée 8 (23) colonnes.

![hashing-bitsize](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

À ce stade, vous pouvez cliquer sur **Exécuter** pour tester l’expérience.

![run](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###Création d’un service web

Maintenant, créez un service web. Développez **Service Web** et faites glisser **Entrée** sur votre expérience. Connectez **Entrée** à **Fonction de hachage**. Faites également glisser **Sortie** sur votre expérience. Connectez **Sortie** à **Fonction de hachage**.

![output-to-feature-hashing](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Cliquez sur **Publier le service Web**.

![publish-web-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

Cliquez sur **Oui** pour publier l’expérience.

![yes-to-publish](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###Test du service web

Un service web AzureML se compose de points de terminaison RRS (service de requête/réponse) et BES (service d’exécution de lot). RSS est conçu pour l’exécution synchrone. BES est conçu pour l’exécution de tâches asynchrone. Pour tester un service web avec l’exemple de source Python ci-dessous, vous devrez peut-être télécharger et installer le Kit de développement logiciel (SDK) Microsoft Azure pour Python (voir [Installation de Python](../python-how-to-install.md)).

Il vous faudra également l’**espace de travail**, le **service** et l’**api\_key** de votre expérience pour l’exemple de source ci-dessous. Vous pouvez trouver l’espace de travail et le service en cliquant sur **Requête/réponse** ou **Exécution de lot** pour votre expérience dans le tableau de bord de service web.

![find-workspace-and-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Vous pouvez trouver l’**api\_key** en cliquant sur votre expérience dans le tableau de bord de service web.

![find-api-key](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####Test du point de terminaison RRS

#####Bouton de test

Un moyen simple de tester le point de terminaison RRS consiste à cliquer sur **Test** sur le tableau de bord du service web.

![test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

Saisissez **C’est une belle journée** en **col2**. Cliquez sur la coche.

![enter-data](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

Le résultat suivant doit s’afficher :

![sample-output](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####Exemple de code

Vous pouvez également tester votre RRS à partir de votre code client. Si vous cliquez sur **Requête/réponse** sur le tableau de bord et faites défiler la liste vers le bas, vous trouverez des exemples de code pour C#, Python et R. Vous trouverez également la syntaxe de la requête RRS, y compris l’URI, les en-têtes et le corps de la requête.

Ce guide fournit un exemple Python opérationnel. Vous devrez le modifier avec les **espace de travail**, **service** et **api\_key** de votre expérience.

	import urllib2
	import json
	workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
	service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
	api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
	data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
	}
	url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
	headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	body = str.encode(json.dumps(data))
	try:
    	req = urllib2.Request(url, body, headers)
    	response = urllib2.urlopen(req)
    	result = response.read()
    	print "result:" + result
			except urllib2.HTTPError, error:
    	print("The request failed with status code: " + str(error.code))
    	print(error.info())
    	print(json.loads(error.read()))

####Test du point de terminaison BES
Cliquez sur **Exécution de lot** sur le tableau de bord et faites défiler la liste vers le bas. Vous trouverez des exemples de code pour C#, Python et R. Vous trouverez également la syntaxe des requêtes BES pour soumettre une tâche, démarrer une tâche, obtenir l’état ou les résultats d’une tâche et supprimer une tâche.

Ce guide fournit un exemple Python opérationnel. Vous devez le modifier avec les **espace de travail**, **service** et **api\_key** de votre expérience. En outre, vous devez modifier les **nom de compte de stockage**, **clé de compte de stockage** et **nom de conteneur de stockage**. Enfin, vous devez modifier l’emplacement du **fichier d’entrée** et l’emplacement du **fichier de sortie**.

	import urllib2
	import json
	import time
	from azure.storage import *
	workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
	service = "<REPLACE WITH YOUR SERVICE ID>"
	api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
	storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
	storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
	storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
	input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
	output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
	input_blob_name = "mydatablob.csv"
	output_blob_name = "myresultsblob.csv"
	def printHttpError(httpError):
	print("The request failed with status code: " + str(httpError.code))
	print(httpError.info())
	print(json.loads(httpError.read()))
	return
	def saveBlobToFile(blobUrl, resultsLabel):
	print("Reading the result from " + blobUrl)
	try:
		response = urllib2.urlopen(blobUrl)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	with open(output_file, "w+") as f:
		f.write(response.read())
	print(resultsLabel + " have been written to the file " + output_file)
	return
	def processResults(result):
	first = True
	results = result["Results"]
	for outputName in results:
		result_blob_location = results[outputName]
		sas_token = result_blob_location["SasBlobToken"]
		base_url = result_blob_location["BaseLocation"]
		relative_url = result_blob_location["RelativeLocation"]
		print("The results for " + outputName + " are available at the following Azure Storage location:")
		print("BaseLocation: " + base_url)
		print("RelativeLocation: " + relative_url)
		print("SasBlobToken: " + sas_token)
		if (first):
			first = False
			url3 = base_url + relative_url + sas_token
			saveBlobToFile(url3, "The results for " + outputName)
	return

	def invokeBatchExecutionService():
	url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
	blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
	print("Uploading the input to blob storage...")
	data_to_upload = open(input_file, "r").read()
	blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
	print "Uploaded the input to blob storage"
	input_blob_path = "/" + storage_container_name + "/" + input_blob_name
	connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
	payload =  {
		"Input": {
			"ConnectionString": connection_string,
			"RelativeLocation": input_blob_path
		},
		"Outputs": {
			"output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
		},
		"GlobalParameters": {
		}
	}
		body = str.encode(json.dumps(payload))
	headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
	print("Submitting the job...")
	# submit the job
	req = urllib2.Request(url + "?api-version=2.0", body, headers)
	try:
		response = urllib2.urlopen(req)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	result = response.read()
	job_id = result[1:-1] # remove the enclosing double-quotes
	print("Job ID: " + job_id)
	# start the job
	print("Starting the job...")
	req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
	try:
		response = urllib2.urlopen(req)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	url2 = url + "/" + job_id + "?api-version=2.0"

	while True:
		print("Checking the job status...")
		# If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
		req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
		try:
			response = urllib2.urlopen(req)
		except urllib2.HTTPError, error:
			printHttpError(error)
			return
		result = json.loads(response.read())
		status = result["StatusCode"]
		print "status:" + status
		if (status == 0 or status == "NotStarted"):
			print("Job " + job_id + " not yet started...")
		elif (status == 1 or status == "Running"):
			print("Job " + job_id + " running...")
		elif (status == 2 or status == "Failed"):
			print("Job " + job_id + " failed!")
			print("Error details: " + result["Details"])
			break
		elif (status == 3 or status == "Cancelled"):
			print("Job " + job_id + " cancelled!")
			break
		elif (status == 4 or status == "Finished"):
			print("Job " + job_id + " finished!")
			processResults(result)
			break
		time.sleep(1) # wait one second
	return
	invokeBatchExecutionService()

<!---HONumber=AcomDC_0921_2016-->