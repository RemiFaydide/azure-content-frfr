<properties
	pageTitle="Machine virtuelle pour la science des données dans Azure | Microsoft Azure"
	description="Configurer une machine virtuelle pour la science des données"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="xibingao;bradsev" />

# Machine virtuelle pour la science des données dans Azure

Les instructions fournies ici décrivent la procédure de configuration d'une machine virtuelle Azure et d'une machine virtuelle Azure équipée du service SQL en tant que serveurs Notebook IPython. La machine virtuelle Windows est configurée avec des outils connexes, tels que Notebook IPython, l’Explorateur de stockage Azure et AzCopy, ainsi que d’autres utilitaires utiles pour les projets de science des données. Par exemple, l’Explorateur de stockage Azure et AzCopy facilitent le chargement de données dans le stockage Azure depuis votre ordinateur local ou le téléchargement de ces données vers votre ordinateur local à partir du stockage.

Ce menu pointe vers des rubriques qui décrivent comment configurer les différents environnements de science de données utilisés par le [processus TDSP (Team Data Science Process)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Vous pouvez approvisionner et configurer plusieurs types de machines virtuelles Azure en vue de les utiliser au sein d’un environnement de science des données dans le cloud. Le choix du type de machine virtuelle à utiliser dépend du type et de la quantité des données à modéliser avec l’apprentissage automatique, ainsi que de la destination cible de ces données dans le cloud.

* Pour plus d’informations sur les questions à prendre en compte lors de cette prise de décision, consultez la page [Planifier votre environnement de science des données Azure Machine Learning](machine-learning-data-science-plan-your-environment.md).
* Pour accéder à un catalogue de scénarios que vous pouvez rencontrer lorsque vous effectuez des analyses avancées, consultez la page [Scénarios du processus de science des données Azure en action dans Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md)

Deux ensembles d’instructions vous sont fournis :

* L’article [Configurer une machine virtuelle Azure comme serveur IPython Notebook pour des analyses avancées](machine-learning-data-science-setup-virtual-machine.md) indique comment approvisionner une machine virtuelle Azure avec Notebook IPython et d’autres outils utilisés pour des projets de science des données permettant de recourir à une forme de stockage Azure autre que SQL pour le stockage des données.

* L’article [Configurer une machine virtuelle Azure SQL Server comme serveur IPython Notebook pour des analyses avancées](machine-learning-data-science-setup-sql-server-virtual-machine.md) indique comment approvisionner une machine virtuelle Azure SQL Server avec Notebook IPython et d’autres outils utilisés pour des projets de science des données permettant de recourir à une base de données SQL pour le stockage des données.

Une fois approvisionnées et configurées, ces machines virtuelles sont utilisables sous forme de serveurs Notebook IPython pour l’exploration et le traitement des données, ainsi que pour l’exécution d’autres tâches avec Azure Machine Learning et le processus TDSP (Team Data Science Process). Les étapes suivantes du processus de science des données sont présentées dans le [parcours d'apprentissage TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et peuvent inclure des étapes de déplacement de données dans SQL Server ou HDInsight, de traitement et d'échantillonnage des données en vue d'en extraire de l'information pertinente avec Azure Machine Learning.


> [AZURE.NOTE] Le service Azure Virtual Machines est facturé au tarif du **paiement à l’utilisation**. Pour vous assurer de ne pas être facturé lorsque vous n’utilisez pas votre machine virtuelle, elle doit être définie sur l’état **Arrêté (désalloué)** à partir du [Portail Azure Classic](http://manage.windowsazure.com/). Pour obtenir des instructions détaillées sur la libération de votre machine virtuelle, consultez la page [Arrêter et libérer une machine virtuelle inutilisée](machine-learning-data-science-setup-virtual-machine.md#shutdown)
 

<!---HONumber=AcomDC_0921_2016-->