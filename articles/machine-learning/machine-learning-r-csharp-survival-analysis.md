<properties 
	pageTitle="Analyse de survie avec Azure Machine Learning | Microsoft Azure" 
	description="Probabilité d'occurrence d'un événement d'analyse de survie" 
	services="machine-learning" 
	documentationCenter="" 
	authors="zhangya" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2016" 
	ms.author="zhangya"/>


#Analyse de survie 

Dans de nombreux scénarios, le principal résultat évalué est la durée avant qu'un événement qui vous intéresse ne se produise. En d’autres termes, vous posez la question « Quand cet événement va-t-il se produire ? ». Par exemple, considérez les situations dans lesquelles les données décrivent le temps écoulé (jours, années, kilométrage, etc.) jusqu’à ce que l’événement se produise (rechute (maladie), obtention d’un doctorat, défaillance des plaquettes de frein). Dans les données, chaque instance représente un objet spécifique (un ou une patient(e), un ou une étudiant(e), une voiture, etc.).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Ce [service web](https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) répond à la question « Quelle est la probabilité que l’événement se produise en n jours/heures/etc. (temps) pour l’objet x ? » Grâce au modèle d’analyse de survie, ce service web permet aux utilisateurs de fournir des données pour entraîner le modèle et le tester. Le principal objectif de l'expérience est de modéliser la durée écoulée jusqu'à ce que l'événement se produise.

>Les utilisateurs peuvent potentiellement accéder à ce service web par le biais d’une application mobile, d’un site web ou même d’un ordinateur local, par exemple. Mais l’objectif du service web est également de servir d’exemple d’utilisation d’Azure Machine Learning pour créer des services web avec le code R. Avec seulement quelques lignes de code R et quelques clics dans Azure Machine Learning Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les appareils du monde entier l’utilisent sans que l’auteur du service web n’ait à configurer l’infrastructure.

##Utilisation du service web

Le schéma de données d'entrée du service web est présenté dans le tableau suivant. Six types d’informations sont nécessaires : les données d’apprentissage, le données de test, la durée d’intérêt, l’index de dimension « temporelle », l’index de dimension de l’« événement » et les types de variables (continues ou facteur). Les données d'apprentissage sont représentées par une chaîne, dans laquelle les lignes sont séparées par des virgules et les colonnes sont séparées par des points-virgules. Le nombre de fonctionnalités des données est flexible. Tous les éléments de la chaîne d'entrée doivent être numériques. Dans les données d’apprentissage, la dimension « temporelle » indique le nombre d’unités de temps (jours, années, kilométrage, etc.) qui s’est écoulé depuis le point de départ de l’étude (un patient suivant un programme de désintoxication, un étudiant commençant son doctorat, le début de la conduite d’une voiture, etc.) jusqu’à ce que l’événement se produise (le patient consomme de la drogue, l’étudiant obtient son doctorat, les plaquettes de frein de la voiture sont usées, etc.). La dimension de l'« évènement » indique si l'évènement se produit à la fin de l'étude. La valeur « event=1 » signifie que l’évènement se produit au moment indiqué par la dimension « temporelle » ; tandis que « event=0 » signifie que l’évènement n’a pas encore eu lieu au moment indiqué par la dimension « temporelle ».

- trainingdata : il s’agit d’une chaîne de caractères. Les lignes sont séparées par des virgules et les colonnes sont séparées par des points-virgules. Chaque ligne inclut la dimension « temporelle », la dimension de l'« évènement » et les variables de prédiction.
- testingdata : il s’agit d’une ligne de données qui contient les variables de prédiction pour un objet particulier.
- time\_of\_interest : le temps n écoulé.
- index\_time : index de colonne de la dimension « temporelle » (à partir de 1).
- index\_event : index de colonne de la dimension « événement » (à partir de 1).
- variable\_types : une chaîne de caractères séparés par des points-virgules. 0 représente les variables continues et 1 représente les variables facteur.


La sortie est la probabilité qu'un événement se produise à un moment précis.

>Étant hébergé sur Azure Marketplace, ce service est un service OData. Il peut être appelé à l’aide des méthodes POST ou GET.

Il existe plusieurs façons d’utiliser le service de manière automatique (un exemple d’application est disponible [ici](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)).

###Début du code C# pour l'utilisation du service web :
	public class Input
	{
	        public string trainingdata;
	        public string testingdata;
	        public string timeofinterest;
	        public string indextime;
	        public string indexevent;
	        public string variabletypes;
	}

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}




L'interprétation de ce test est la suivante. Supposons que l’objectif des données est de modéliser le temps écoulé jusqu’à la rechute des patients ayant suivi un des deux programmes de désintoxication. La sortie lue par le service web : pour les patients de 35 ans, ayant déjà suivi 2 traitements et suivant actuellement le programme de traitement résidentiel à long terme pour utilisation d’héroïne et de cocaïne, la probabilité de consommer de nouveau de la drogue est de 95,64 % au 500e jour.

##Création du service web

>Ce service web a été créé à l’aide d’Azure Machine Learning. Pour un essai gratuit, ainsi que des vidéos de présentation relatives à la création d’expériences et à la [publication de services web](machine-learning-publish-a-machine-learning-web-service.md), consultez la page [azure.com/ml](http://azure.com/ml). Voici une capture d'écran de l'expérience qui a créé le service web et l'exemple de code pour chacun des modules dans l'expérience.

À partir d’Azure Machine Learning, une nouvelle expérience vide a été créée et deux modules [Exécuter le script R][execute-r-script] ont été importés dans l’espace de travail. Le schéma de données a été créé avec un simple module [Exécuter le script R][execute-r-script], qui définit le schéma de données d’entrée pour le service web. Ce module est ensuite lié au deuxième module [Exécuter le script R][execute-r-script] qui effectue la majeure partie du travail. Ce module réalise le prétraitement des données, la création du modèle et les prédictions. Dans l'étape de prétraitement des données, les données d'entrée représentées par une chaîne longue sont transformées et converties en une trame de données. Dans l'étape de création du modèle, un package R externe « survival\_2.37-7.zip » est tout d'abord installé pour effectuer l'analyse de survie. La fonction « coxph » est ensuite exécutée après la tâche de traitement des données de série. Pour connaître les détails de la fonction « coxph » pour l’analyse de survie, consultez la documentation R. Dans l'étape de prédiction, une instance de test est fournie dans le modèle d'apprentissage avec la fonction « surfit » et la courbe de survie de cette instance de test est générée en tant que variable « curve ». Enfin, vous obtenez la probabilité de la durée d'intérêt.

###Flux de l’expérience :

![Flux de l’expérience][1]

####Module 1 :

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
	
####Module 2 :

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##Limitations

Ce service web accepte uniquement les valeurs numériques sous forme de variables de fonctionnalité (colonnes). La colonne « événement » peut uniquement avoir la valeur 0 ou 1. La colonne « temps » doit contenir un entier positif.

##Forum Aux Questions
Pour les questions fréquemment posées relatives à l’utilisation du service web ou à la publication sur Azure Marketplace, consultez [ce lien](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0921_2016-->