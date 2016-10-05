<properties 
	pageTitle="Test de différence des proportions | Microsoft Azure" 
	description="Test de différence des proportions" 
	services="machine-learning" 
	documentationCenter="" 
	authors="aniedea" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="aniedea"/>


#Test de différence des proportions


Deux proportions sont-elles différentes d'un point de vue statistique ? Supposons qu'un utilisateur souhaite comparer deux films pour déterminer si l'un présente une proportion nettement supérieure de « J'aime » par rapport à l'autre. Avec un échantillon de grande taille, il peut exister une différence statistiquement importante entre les proportions 0,50 et 0,51. Avec un petit échantillon, il peut ne pas y avoir suffisamment de données pour déterminer si ces proportions sont réellement différentes.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Ce [service web](https://datamarket.azure.com/dataset/aml_labs/prop_test) effectue un test d’hypothèse de la différence entre deux proportions basé sur le nombre de réussites entré par l’utilisateur et le nombre total d’essais pour les 2 groupes de comparaison. Exemple de scénario possible : ce service web pourrait être appelé à partir d’une application de comparaison de films afin d’indiquer à l’utilisateur si un film est vraiment « aimé » plus souvent que les autres, en fonction de la classification des films.

>Les utilisateurs peuvent potentiellement accéder à ce service web par le biais d’une application mobile, d’un site web ou même d’un ordinateur local, par exemple. Mais l’objectif du service web est également de servir d’exemple d’utilisation d’Azure Machine Learning pour créer des services web avec le code R. Avec seulement quelques lignes de code R et quelques clics dans Azure Machine Learning Studio, vous pouvez créer une expérience avec le code R et la publier en tant que service web. Le service web peut ensuite être publié sur Azure Marketplace afin que les utilisateurs et les appareils du monde entier l’utilisent sans que l’auteur du service web n’ait à configurer l’infrastructure.


##Utilisation du service web

Ce service accepte 4 arguments et effectue un test d'hypothèse des proportions.

Les arguments d'entrée sont les suivants :

* Successes1 : nombre d’événements de succès dans l’échantillon 1
* Successes2 : nombre d’événements de succès dans l’échantillon 2
* Total1 : taille de l’échantillon 1
* Total2 : taille de l’échantillon 2

La sortie du service correspond au résultat du test d’hypothèse, ainsi qu’à la statistique de khi carré, la fraction décimale, la valeur p, la proportion dans l’échantillon 1/2 et les limites d’intervalle de confiance.

>Étant hébergé sur Azure Marketplace, ce service est un service OData. Il peut être appelé à l’aide des méthodes POST ou GET.

Il existe plusieurs façons d’utiliser le service de manière automatique (un exemple d’application est disponible [ici](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx)).

###Début du code C# pour l'utilisation du service web :

	public class Input
	{
	        public string successes1;
	        public string successes2;
	        public string total1;
	        public string total2;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}

	void Main()
	{
	        var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
	    	var scoreResult = result.ReadAsStringAsync().Result;
	}


##Création du service web

>Ce service web a été créé à l’aide d’Azure Machine Learning. Pour un essai gratuit, ainsi que des vidéos de présentation relatives à la création d’expériences et à la [publication de services web](machine-learning-publish-a-machine-learning-web-service.md), consultez la page [azure.com/ml](http://azure.com/ml). Voici une capture d'écran de l'expérience qui a créé le service web et l'exemple de code pour chacun des modules dans l'expérience.

À partir d’Azure Machine Learning, une nouvelle expérience vide a été créée avec deux modules [Exécuter le script R][execute-r-script]. Dans le premier module, le schéma de données est défini alors que le second module utilise la commande prop.test dans R pour effectuer le test d’hypothèse pour 2 proportions.


###Flux de l’expérience :

![Flux de l’expérience][2]


####Module 1 :
	####Schema definition  
	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####Module 2 :

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##Limitations 

Il s’agit d’un exemple très simple permettant de tester la différence dans 2 proportions. Comme illustré dans l'exemple de code ci-dessus, aucune interception des erreurs n'est implémentée et le service suppose que toutes les variables sont continues.

##Forum Aux Questions
Pour les questions fréquemment posées relatives à l’utilisation du service web ou à la publication sur Azure Marketplace, consultez [ce lien](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0921_2016-->