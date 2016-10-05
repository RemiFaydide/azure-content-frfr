**Processus Analytique Data Lake de base :**
	
![Diagramme du flux de processus Analytique Data Lake Azure](./media/data-lake-analytics-basic-process-include/data-lake-analytics-process.png)
	
1. Créer un compte Analytique Data Lake.
2. Préparer les données source. Les travaux Analytique Data Lake peuvent lire les données depuis les comptes Azure Data Store Lake ou les comptes de stockage d’objets blobs Azure.
3. Développer un script U-SQL.
4. Envoyer un travail (script U-SQL) vers le compte Analytique Data Lake. Le travail lit les données source, traite les données conformément au script U-SQL, puis enregistre la sortie dans un compte Data Lake Store ou un compte de stockage d’objets Blobs.

<!---HONumber=AcomDC_0921_2016-->