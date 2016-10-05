<properties
   pageTitle="Notes de publication sur Azure Data Catalog | Microsoft Azure"
   description="Notes de publication d’Azure Data Catalog."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# Notes de publication sur Azure Data Catalog

## Notes pour la version du 20 novembre 2015 d’Azure Data Catalog

### Ouverture de sources de données dans Power BI Desktop

Lorsque vous utilisez l'option « Ouvrir dans Power BI Desktop » à partir du portail **Catalogue de données Azure**, les utilisateurs peuvent rencontrer un ou deux problèmes dans l'application Power BI Desktop :

- Une boîte de dialogue avec le titre « Impossible d’ouvrir le document » s'affiche.
- L'application Power BI Desktop s'ouvre, mais le fichier semble être vide.

Pour chaque cas, le problème peut être résolu en téléchargeant et en installant la dernière version de Power BI Desktop sur [PowerBI.com](https://powerbi.com).

## Notes pour la version du 13 novembre 2015 d’Azure Data Catalog

### Inscription et connexion sur Teradata

Lors de la connexion aux sources de données Teradata, les utilisateurs doivent avoir installé le pilote ODBC Teradata qui correspond au nombre de bits (32 ou 64) du logiciel utilisé.

À compter de cette date de version d’ADC, le [pilote ODBC de Teradata pour Windows (version 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) le plus récent est compatible avec Office 2013, mais pas avec Office 2016.

## Notes pour la version du 13 juillet 2015 d’Azure Data Catalog

### Inscription et connexion à Oracle Database

Lors de la connexion aux sources de données Oracle Database, les utilisateurs doivent avoir installé les pilotes Oracle appropriés qui correspondent au nombre de bits (32 ou 64) du logiciel utilisé.

-	Lors de l’inscription des sources de données Oracle sur un ordinateur exécutant Windows 32 bits, les pilotes Oracle 32 bits seront utilisés.
-	Lors de l’inscription des sources de données Oracle sur un ordinateur exécutant Windows 64 bits, les pilotes Oracle 64 bits seront utilisés.
-	Lors de la connexion aux sources de données Oracle à l'aide d'Excel sur un ordinateur exécutant la version 32 bits de Microsoft Office, y compris sur Windows 64 bits, les pilotes Oracle 32 bits seront utilisés.
-	Lors de la connexion aux sources de données Oracle à l'aide d'Excel sur un ordinateur exécutant la version 64 bits de Microsoft Office, les pilotes Oracle 64 bits seront utilisés.

### Inscription et connexion à SQL Server Reporting Services

La prise en charge des sources de données SQL Server Reporting Services (SSRS) est actuellement limitée aux serveurs en mode natif. La prise en charge de SSRS en mode SharePoint sera disponible dans une version ultérieure.

### Ouverture des ressources de données dans Excel

Au moment de l’ouverture des ressources de données dans Microsoft Excel à partir du portail **Azure Data Catalog**, les utilisateurs peuvent voir apparaître la boîte de dialogue **Avis de sécurité Microsoft Excel**. Il s’agit là d’un comportement attendu standard et les utilisateurs peuvent sélectionner **Activer** pour continuer.

Pour plus d’informations, consultez [Activer ou désactiver les alertes de sécurité relatives aux liens et aux fichiers de sites web suspects](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### Configuration du proxy et de la stratégie, et inscription de la source de données

Les utilisateurs parviennent parfois à se connecter au portail d’Azure Data Catalog, mais lorsqu'ils tentent de se connecter à l'outil de référencement de la source de données, un message d'erreur s’affiche et les empêche de se connecter.

Deux causes possibles à ce problème de comportement :

**Cause n°1 : configuration d’Active Directory Federation Services** L’outil d’inscription de la source de données utilise l’authentification par formulaire pour valider les connexions utilisateur avec Active Directory. Pour une ouverture de session réussie, l'authentification par formulaire doit être activée dans la stratégie d'authentification globale par un administrateur Active Directory.

Cette erreur peut également survenir lorsque l'utilisateur est connecté au réseau d'entreprise ou lorsque l'utilisateur se connecte en dehors du réseau d'entreprise. La stratégie d'authentification globale permet d’activer séparément des méthodes d'authentification pour les connexions intranet et extranet. Des erreurs de connexion peuvent survenir si l'authentification par formulaire n'est pas activée pour le réseau à partir duquel l'utilisateur se connecte.

Pour plus d’informations, consultez [Configuration des stratégies d’authentification](https://technet.microsoft.com/library/dn486781.aspx).

**Cause n°2 : configuration du proxy réseau** Si le réseau d’entreprise utilise un serveur proxy, l’outil d’inscription ne peut peut-être pas se connecter à Azure Active Directory via le proxy. Les utilisateurs peuvent s’assurer de l'outil d'inscription en modifiant le fichier de configuration de l'outil, et en ajoutant au fichier la section suivante :


	  <system.net>
	    <defaultProxy useDefaultCredentials="true" enabled="true">
	      <proxy usesystemdefault="True"
	                      proxyaddress="http://<your corporate network proxy url>"
	                      bypassonlocal="True"/>
	    </defaultProxy>
	  </system.net>


Pour localiser le fichier RegistrationTool.exe.config, lancez l'outil d'inscription, puis ouvrez l'utilitaire Gestionnaire des tâches de Windows. Sous l'onglet Détails du Gestionnaire des tâches, cliquez avec le bouton droit sur RegistrationTool.exe et choisissez Ouvrir l'emplacement du fichier dans le menu contextuel.

<!---HONumber=AcomDC_0921_2016-->