<properties 
	pageTitle="Analyse de sentiments basée sur un lexique | Microsoft Azure" 
	description="Analyse de sentiments basée sur un lexique" 
	services="machine-learning" 
	documentationCenter="" 
	authors="pengxia" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2016" 
	ms.author="pengxia"/>



#Analyse de sentiments basée sur un lexique 

Comment pouvez-vous évaluer les avis et les attitudes des utilisateurs envers des marques ou des rubriques sur les réseaux sociaux en ligne, tels que des publications Facebook, des Tweets, des critiques, etc. ? L’analyse de sentiments fournit une méthode d’analyse de ces questions.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Il existe en général deux méthodes pour l’analyse de sentiments. L’une utilise un algorithme d’apprentissage supervisé et l’autre peut être traitée comme un apprentissage non supervisé. Un algorithme d’apprentissage supervisé crée généralement un modèle de classification pour les corpus volumineux annotés. Sa précision est principalement basée sur la qualité de l’annotation et la phase d’apprentissage prend généralement beaucoup de temps. En outre, lorsque nous appliquons l'algorithme à un autre domaine, le résultat n'est généralement pas bon. Par rapport à l’apprentissage supervisé, l’apprentissage non supervisé basé sur un lexique utilise un dictionnaire de sentiments, qui ne nécessite pas le stockage d’un corpus de données volumineux et de formation, ce qui permet d’accélérer considérablement l’ensemble du processus.

Notre [service](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) repose sur le lexique MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), qui est l’un des lexiques de subjectivité les plus fréquemment utilisés. Il existe 5 097 mots négatifs et 2 533 mots positifs dans le MPQA. Par ailleurs, tous ces mots sont annotés avec une polarité forte ou faible. Le corpus entier fait l'objet d'une licence GPL GNU. Le service web peut être utilisé pour toutes les phrases courtes telles que les Tweets et les publications Facebook.

>Les utilisateurs peuvent potentiellement accéder à ce service web par le biais d’une application mobile, d’un site web ou même d’un ordinateur local, par exemple. Mais l’objectif du service web est également de servir d’exemple d’utilisation d’Azure Machine Learning pour créer des services web avec le code R. Avec seulement quelques lignes de code R et quelques clics dans Azure Machine Learning Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les appareils du monde entier l’utilisent sans que l’auteur du service web n’ait à configurer l’infrastructure.

##Utilisation du service web

Les données en entrée peuvent correspondre à n'importe quel texte, mais le service web fonctionne mieux avec des phrases courtes. Le résultat est une valeur numérique comprise entre -1 et 1. Toute valeur inférieure à 0 indique que le sentiment à l’égard du texte est négatif, il est positif si la valeur est supérieure à 0. La valeur absolue du résultat indique la puissance du sentiment associé.

>Étant hébergé sur Azure Marketplace, ce service est un service OData. Il peut être appelé à l’aide des méthodes POST ou GET.

Il existe plusieurs façons d’utiliser le service de manière automatique (un exemple d’application est disponible [ici](http://microsoftazuremachinelearning.azurewebsites.net/)).

###Début du code C# pour l'utilisation du service web :

	public class ScoreResult
	{
	        [DataMember]
	        public double result
	        {
	            get;
	            set;
	        }
	}

	void main()
	{
	        using (var wb = new WebClient())
	        {
	            var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
	            DataServiceContext ctx = new DataServiceContext(acitionUri);
	            var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
	            var cache = new CredentialCache();
	
	            cache.Add(acitionUri, "Basic", cred);
	            ctx.Credentials = cache;
	            var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
	            ScoreResult scoreResult = query.ElementAt(0);
	            double result = scoreResult.result;
	    	}
	}



L’entrée est la suivante : « Aujourd’hui est une belle journée. ». Le résultat est « 1 », ce qui indique un sentiment positif associé à la phrase en entrée.

##Création du service web
>Ce service web a été créé à l’aide d’Azure Machine Learning. Pour un essai gratuit, ainsi que des vidéos de présentation relatives à la création d’expériences et à la [publication de services web](machine-learning-publish-a-machine-learning-web-service.md), consultez la page [azure.com/ml](http://azure.com/ml). Voici une capture d'écran de l'expérience qui a créé le service web et l'exemple de code pour chacun des modules dans l'expérience.


Dans Azure Machine Learning, une nouvelle expérience vide a été créée. La figure ci-dessous illustre le flux d’expérience pour l’analyse de sentiments basée sur un lexique. Le fichier « sent\_dict.csv » correspond au lexique de subjectivité MPQA ; il est défini en tant que l’une des entrées du module [Exécuter le script R][execute-r-script]. Une autre entrée correspond à une critique partielle provenant du jeu de données des critiques Amazon à des fins de test, dans laquelle nous avons effectué des opérations de sélection, de modification des noms de colonne et de fractionnement. Nous utilisons un package de hachage pour stocker le lexique de subjectivité en mémoire et accélérer le processus de calcul du score. L'ensemble du texte sera converti en jetons par le package « tm » et comparé au mot dans le dictionnaire de sentiments. Enfin, un score sera calculé en ajoutant le poids de chaque mot subjectif dans le texte.

###Flux de l’expérience :

![Flux de l’expérience][2]


####Module 1 :
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # Installez le package de hachage install.packages("src/hash\_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE) success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE) library(tm) library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
	polarity_ratio <- 0
	polarity_total <- 0
	not <- 0
	sentence <- tolower(dataset2[m,1])
	if (nchar(sentence) > 0){
		token_array <- scan_tokenizer(sentence)
		for (j in 1:length(token_array)){
			word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
		    for (k in 1:length(negation_word)){
		      if (word == negation_word[k]){
		        not <- (not+1) %% 2

			  }
		    }
			if (word != ""){
			    if (!is.null(sent_dict[[word]])){
			      polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
			      polarity_total <- polarity_total + abs(sent_dict[[word]])
			    }
			}
		  
		}
	}
	if (polarity_total > 0){
		result <- c(result, polarity_ratio/polarity_total)
	}else{
		result<- c(result,0)
	}
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
	


##Limitations

Du point de vue d’un algorithme, l’analyse de sentiments basée sur un lexique est un outil général d’analyse de sentiments qui peut ne pas être plus performant que la méthode de classification pour des champs spécifiques. Le problème de la négation n'est pas correctement pris en charge. Nous codons en dur plusieurs négations dans notre programme, mais une meilleure méthode consiste à utiliser un dictionnaire de négations et à créer certaines règles. Le service web fonctionne mieux avec des phrases courtes et simples, telles que les Tweets et les publications Facebook, qu’avec les phrases longues et complexes, telles que les critiques Amazon.

##Forum Aux Questions
Pour les questions fréquemment posées relatives à l’utilisation du service web ou à la publication sur Azure Marketplace, consultez [ce lien](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 

<!---HONumber=AcomDC_0921_2016-->