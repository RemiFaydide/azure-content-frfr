<properties 
	pageTitle="Stream Analytics : inverser les informations d'identification de connexion pour les entrées et sorties | Microsoft Azure" 
	description="Découvrez comment mettre à jour les informations d’identification pour les entrées et les sorties Stream Analytics."
	keywords="informations d’identification"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>

#Rotation des informations d'identification pour les entrées et les sorties dans des travaux Stream Analytics

##Résumé
Actuellement, Azure Stream Analytics n’autorise pas le remplacement des informations d’identification d’une entrée/sortie pendant l’exécution du travail.

Bien qu’Azure Stream Analytics ne prenne pas en charge la reprise d’une tâche à partir de la dernière sortie, nous souhaitons partager l’ensemble du processus pour réduire le délai entre l’arrêt et le démarrage de la tâche et pour procéder à la rotation des informations d’identification.

##Partie 1 – Préparation du nouveau jeu d’informations d’identification :
Cette partie concerne les entrées/sorties suivantes :

* Stockage d’objets blob
* Hubs d'événements
* Base de données SQL
* Stockage de tables

Pour les autres entrées/sorties, passez à la partie 2.

###Stockage d’objets blob/de tables
1.  Dans le portail de gestion Azure, accédez à l’extension Stockage : ![graphic1][graphic1]
2.  Recherchez le stockage utilisé par votre travail et accédez-y : ![graphic2][graphic2]
3.  Cliquez sur la commande Gérer les clés d’accès : ![graphic3][graphic3]
4.  Entre la clé d’accès primaire et la clé d’accès secondaire, **choisissez celle qui n’est pas utilisée par votre travail**.
5.  Appuyez sur Régénérer : ![graphic4][graphic4]
6.  Copiez la clé qui vient d’être générée : ![graphic5][graphic5]
7.  Passez à la partie 2.

###Event Hubs
1.  Accédez à l’extension Service Bus dans le portail de gestion Azure : ![graphic6][graphic6]
2.  Recherchez l’espace de noms Service Bus utilisé par votre travail et accédez-y : ![graphic7][graphic7]
3.  Si votre travail utilise une stratégie d’accès partagé sur l’espace de noms Service Bus, passez à l’étape 6
4.  Accédez à l’onglet Hubs d’événements : ![graphic8][graphic8]
5.  Recherchez le hub d’événements utilisé par votre travail et accédez-y : ![graphic9][graphic9]
6.  Cliquez sur l’onglet Configurer : ![graphic10][graphic10]
7.  Dans la liste déroulante Nom de la stratégie, recherchez la stratégie d’accès partagé utilisée par votre travail : ![graphic11][graphic11]
8.  Entre la clé primaire et la clé secondaire, **choisissez celle qui n’est pas utilisée par votre travail**.
9.  Appuyez sur Régénérer : ![graphic12][graphic12]
10. Copiez la clé qui vient d’être générée : ![graphic13][graphic13]
11. Passez à la partie 2.

###Base de données SQL

>[AZURE.NOTE] Remarque : vous devez vous connecter au service Base de données SQL. Nous allons montrer comment procéder à l’aide de l’expérience de gestion sur le portail de gestion Azure, mais vous pouvez également choisir d’utiliser un outil côté client tel que SQL Server Management Studio.

1.  Accédez à l’extension Bases de données SQL dans le portail de gestion Azure : ![graphic14][graphic14]
2.  Recherchez la base de données SQL utilisée par votre travail et **cliquez sur le lien du serveur** sur la même ligne : ![graphic15][graphic15]
3.  Cliquez sur la commande Gérer : ![graphic16][graphic16]
4.  Tapez Base de données principale : ![graphic17][graphic17]
5.  Tapez votre nom d’utilisateur, votre mot de passe et cliquez sur Ouvrir une session : ![graphic18][graphic18]
6.  Cliquez sur Nouvelle requête : ![graphic19][graphic19]
7.  Tapez la requête suivante en remplaçant <login\_name> par votre nom d’utilisateur et <enterStrongPasswordHere> par votre nouveau mot de passe : `CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Cliquez sur Exécuter : ![graphic20][graphic20]
9.  Revenez à l’étape 2 et, cette fois, cliquez sur la base de données : ![graphic21][graphic21]
10. Cliquez sur la commande Gérer : ![graphic22][graphic22]
11. Tapez votre nom d’utilisateur, votre mot de passe et cliquez sur Ouvrir une session : ![graphic23][graphic23]
12. Cliquez sur Nouvelle requête : ![graphic24][graphic24]
13. Tapez la requête suivante en remplaçant <nom\_utilisateur> par le nom avec lequel vous souhaitez identifier cette connexion dans le contexte de cette base de données (vous pouvez fournir la même valeur que celle attribuée à <nom\_utilisateur>, par exemple) et en remplaçant <nom\_connexion> par votre nouveau nom d’utilisateur : `CREATE USER <user_name> FROM LOGIN <login_name>`
14. Cliquez sur Exécuter : ![graphic25][graphic25]
15. Vous devez maintenant fournir à votre nouvel utilisateur les mêmes rôles et privilèges que ceux de l’utilisateur d’origine.
16. Passez à la partie 2.

##Partie 2 – Arrêt du travail Stream Analytics
1.  Accédez à l’extension Stream Analytics sur le portail de gestion Azure : ![graphic26][graphic26]
2.  Recherchez votre travail et accédez-y : ![graphic27][graphic27]
3.  Accédez à l’onglet Entrées ou à l’onglet Sorties selon que vous remplacez les informations d’identification d’une entrée ou d’une sortie. ![graphic28][graphic28]
4.  Cliquez sur la commande d’arrêt et vérifiez que le travail s’est arrêté : ![graphic29][graphic29] Attendez que le travail s’arrête.
5.  Recherchez l’entrée/la sortie pour laquelle vous souhaitez remplacer les informations d’identification et accédez-y : ![graphic30][graphic30]
6.  Passez à la partie 3.

##Partie 3 – Modification des informations d’identification pour le travail Stream Analytics

###Stockage d’objets blob/de tables
1.	Recherchez le champ Clé du compte de stockage et collez la clé qui vient d’être générée dans ce dernier : ![graphic31][graphic31]
2.	Cliquez sur la commande Enregistrer et confirmez l’enregistrement de vos modifications : ![graphic32][graphic32]
3.	Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications ; assurez-vous qu’il a réussi.
4.	Passez à la partie 4.

###Hubs d’événements
1.	Recherchez le champ Clé de la stratégie du hub d’événements et collez votre nouvelle clé dans ce dernier : ![graphic33][graphic33]
2.	Cliquez sur la commande Enregistrer et confirmez l’enregistrement de vos modifications : ![graphic34][graphic34]
3.	Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications ; assurez-vous qu’il a réussi.
4.	Passez à la partie 4.

###Power BI
1.	Cliquez sur Renouveler l’autorisation :
* ![graphic35][graphic35]
* Le message de confirmation suivant s’affiche.
* ![graphic36][graphic36]
2.	Cliquez sur la commande Enregistrer et confirmez l’enregistrement de vos modifications : ![graphic37][graphic37]
3.	Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications ; assurez-vous qu’il a réussi.
4.	Passez à la partie 4.

###Base de données SQL
1.	Recherchez les champs Nom d’utilisateur et Mot de passe, et collez votre jeu d’informations d’identification nouvellement créé dans ces derniers : ![graphic38][graphic38]
2.	Cliquez sur la commande Enregistrer et confirmez l’enregistrement de vos modifications : ![graphic39][graphic39]
3.	Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications ; assurez-vous qu’il a réussi.
4.	Passez à la partie 4.

##Partie 4 – Démarrage de votre travail à partir de l’heure du dernier arrêt
1.	Naviguez en dehors de l’entrée/sortie : ![graphic40][graphic40]
2.	Cliquez sur la commande Démarrer : ![graphic41][graphic41]
3.	Sélectionnez l’heure du dernier arrêt et cliquez sur OK : ![graphic42][graphic42]
4.	Passez à la partie 5.

##Partie 5 – Suppression de l’ancien jeu d’informations d’identification
Cette partie concerne les entrées/sorties suivantes :
* Stockage d’objets blob
* Hubs d'événements
* Base de données SQL
* Stockage de tables

###Stockage d’objets blob/de tables
Répétez la partie 1 pour la clé d’accès qui était précédemment utilisée par votre travail afin de renouveler la clé d’accès maintenant inutilisée.

###Hubs d’événements
Répétez la partie 1 pour la clé qui était précédemment utilisée par votre travail afin de renouveler la clé maintenant inutilisée.

###Base de données SQL
1.	Revenez à la fenêtre de requête de la partie 1, étape 7, et tapez la requête suivante, en remplaçant <previous\_login\_name> par le nom d’utilisateur qui était précédemment utilisé par votre travail : `DROP LOGIN <previous_login_name>`
2.	Cliquez sur Exécuter : ![graphic43][graphic43]

Le message de confirmation suivant s’affiche.

	Command(s) completed successfully.

## Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 

<!---HONumber=AcomDC_0921_2016-->