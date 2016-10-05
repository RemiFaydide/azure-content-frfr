<properties
   pageTitle="Sources de données prises en charge par Azure Data Catalog | Microsoft Azure"
   description="Spécification des sources de données actuellement prises en charge."
   services="data-catalog"
   documentationCenter=""
   authors="trhabe"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="trhabe"/>

# Sources de données prises en charge par Azure Data Catalog

Les utilisateurs d’Azure Data Catalog peuvent publier des métadonnées à l’aide d’une API publique, en utilisant un outil d’inscription par simple clic, ou en entrant manuellement les informations directement dans le portail Web de Data Catalog. Le tableau ci-dessous résume toutes les sources actuellement prises en charge par le catalogue ainsi que les fonctionnalités de publication disponibles pour chacune d’elles. Sont également répertoriés les outils de données externes que chaque source peut initier à partir du portail. Le deuxième tableau dans cet article spécifie les propriétés de connexion de chaque source de données sous un angle plus technique.


## Liste des sources de données prises en charge

<table>

    <tr>
       <td><b>Objet de la source de données</b></td>
       <td><b>API</b></td>
       <td><b>Saisie manuelle</b></td>
       <td><b>Outil de référencement</b></td>
       <td><b>Outils d’ouverture intégrés</b></td>
       <td><b>Remarques</b></td>
    </tr>

    <tr>
      <td>Répertoire Azure Data Lake Store</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fichier Azure Data Lake Store</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Storage Blob</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Répertoire de stockage Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Storage Table</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>Répertoire HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fichier HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Affichage Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table de base de données Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue de base de données Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Autre (élément multimédia générique)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table SQL&#160;Data&#160;Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue SQL&#160;Data&#160;Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Dimension SQL&#160;Server Analysis&#160;Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Indicateur de performance clé de SQL&#160;Server Analysis&#160;Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Mesure SQL&#160;Server Analysis&#160;Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table SQL&#160;Server Analysis&#160;Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Rapport SQL&#160;Server&#160;Reporting&#160;Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Browser</font></td>
      <td><font size=2>Serveurs en mode natif uniquement. Le mode SharePoint n’est pas pris en charge.</font></td>
    </tr>

    <tr>
      <td>Table SQL&#160;Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue SQL&#160;Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue SAP Hana</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2>Vues de calcul et vues analytiques uniquement ; les vues d’attribut ne sont pas prises en charge.</font></td>
    </tr>

    <tr>
      <td>Table DB2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue DB2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fichier du système de fichiers</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Répertoire FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fichier FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Rapport HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Point de terminaison HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fichier HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Jeu d’entités OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fonction OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table Postgresql</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue Postgresql</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue SAP Hana</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Objet Salesforce</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Liste SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Si vous avez besoin d’une prise en charge de sources supplémentaires, envoyez une demande de fonctionnalité par l’intermédiaire du [forum Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br> <br>
## Spécification de référence de la source de données
> [AZURE.NOTE] La colonne « Structure DSL » du tableau ci-dessous répertorie uniquement les propriétés de connexion du conteneur de propriétés « address » qui sont utilisées par Azure Data Catalog (autrement dit, le conteneur de propriétés « address » peut contenir les autres propriétés de connexion de la source de données conservées, mais pas utilisées par Azure Data Catalog.)
<table>
    <tr>
       <td><b>Type de source</b></td>
       <td><b>Type de ressource</b></td>
       <td><b>Type(s) d’objet</b></td>
       <td><b>Structure DSL<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Conteneur</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocole&#160;: webhdfs
            <br>authentification : {de base, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Table</td>
      <td>Répertoire, Fichier</td>
      <td>
        <font size=2> protocole&#160;: webhdfs
            <br>authentification : {de base, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Conteneur</td>
      <td>Conteneur</td>
      <td>
        <font size=2> protocole : azure-blobs
            <br>authentification&#160;: {azure-access-key}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; container </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Table</td>
      <td>Objet blob, Répertoire</td>
      <td>
        <font size=2> protocole : azure-blobs
            <br>authentification&#160;: {azure-access-key}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; container
            <br>&#160;&#160;&#160;&#160;&#160; name </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Conteneur</td>
      <td>Conteneur</td>
      <td>
        <font size=2> protocol : azure-tables
            <br>authentification&#160;: {azure-access-key}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        <font size=2> protocol : azure-tables
            <br>authentification&#160;: {azure-access-key}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; name </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Conteneur</td>
      <td>Cluster virtuel</td>
      <td>
        <font size=2> protocole&#160;: cosmos
            <br>authentification&#160;: {base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Table</td>
      <td>Flux, Ensemble de flux, Vue</td>
      <td>
        <font size=2> protocole&#160;: cosmos
            <br>authentification&#160;: {base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Conteneur</td>
      <td>Site</td>
      <td>
        <font size=2> protocole&#160;: http
            <br>authentification : {aucune, de base, windows, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Rapport</td>
      <td>Rapport, Tableau de bord</td>
      <td>
        <font size=2> protocole&#160;: http
            <br>authentification : {aucune, de base, windows, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> protocole&#160;: db2
            <br>authentification&#160;: {base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Table</td>
      <td>Table, Vue</td>
      <td>
        <font size=2> protocole&#160;: db2
            <br>authentification&#160;: {base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objet
            <br>&#160;&#160;&#160;&#160;&#160; schema </font>
      </td>
    </tr>
    <tr>
      <td>Système de fichiers</td>
      <td>Table</td>
      <td>Fichier</td>
      <td>
        <font size=2> protocole&#160;: file
            <br>authentification&#160;: {aucune, base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; path </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Table</td>
      <td>Répertoire, Fichier</td>
      <td>
        <font size=2> protocol&#160;: ftp
            <br>authentification&#160;: {aucune, base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Conteneur</td>
      <td>Cluster</td>
      <td>
        <font size=2> protocole&#160;: webhdfs
            <br>authentification : {de base, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Table</td>
      <td>Répertoire, Fichier</td>
      <td>
        <font size=2> protocole&#160;: webhdfs
            <br>authentification : {de base, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> protocol : hive
            <br>authentification&#160;: {hdinsight, base, nom d’utilisateur, aucune}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>connectionProperties :
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol : {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Table</td>
      <td>Table, Vue</td>
      <td>
        <font size=2> protocol : hive
            <br>authentification&#160;: {hdinsight, base, nom d’utilisateur, aucune}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objet
            <br>connectionProperties :
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol : {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Conteneur</td>
      <td>Site</td>
      <td>
        <font size=2> protocole&#160;: http
            <br>authentification : {aucune, de base, windows, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Rapport</td>
      <td>Rapport, Tableau de bord</td>
      <td>
        <font size=2> protocole&#160;: http
            <br>authentification : {aucune, de base, windows, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Table</td>
      <td>Point de terminaison, Fichier</td>
      <td>
        <font size=2> protocole&#160;: http
            <br>authentification : {aucune, de base, windows, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> protocole&#160;: mysql
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Table</td>
      <td>Table, Vue</td>
      <td>
        <font size=2> protocole&#160;: mysql
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>Odata</td>
      <td>Conteneur</td>
      <td>Conteneur d’entités</td>
      <td>
        <font size=2> protocole&#160;: odata
            <br>authentification&#160;: {aucune, base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Odata</td>
      <td>Table</td>
      <td>Jeu d’entités, Fonction</td>
      <td>
        <font size=2> protocole&#160;: odata
            <br>authentification&#160;: {aucune, base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; resource </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> protocole&#160;: oracle
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Table</td>
      <td>Table, Vue</td>
      <td>
        <font size=2> protocole&#160;: oracle
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> protocole&#160;: postgresql
            <br>authentification&#160;: {base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>Table</td>
      <td>Table, Vue</td>
      <td>
        <font size=2> protocole&#160;: postgresql
            <br>authentification&#160;: {base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Conteneur</td>
      <td>Site</td>
      <td>
        <font size=2> protocole&#160;: http
            <br>authentification : {aucune, de base, windows, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Rapport</td>
      <td>Rapport, Tableau de bord</td>
      <td>
        <font size=2> protocole&#160;: http
            <br>authentification : {aucune, de base, windows, oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Table</td>
      <td>Application Web hybride</td>
      <td>
        <font size=2> protocole : power-query
            <br>authentification : {oauth}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Table</td>
      <td>Object</td>
      <td>
        <font size=2> protocole&#160;: salesforce-com
            <br>authentification&#160;: {base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Conteneur</td>
      <td>Serveur</td>
      <td>
        <font size=2> protocole&#160;: sap hana-sql
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Table</td>
      <td>Affichage</td>
      <td>
        <font size=2> protocole&#160;: sap hana-sql
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Table</td>
      <td>Énumérer</td>
      <td>
        <font size=2> protocole&#160;: sharepoint-list
            <br>authentification&#160;: {base, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL&#160;Data&#160;Warehouse</td>
      <td>Commande</td>
      <td>Procédure stockée</td>
      <td>
        <font size=2> protocole&#160;: tds
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL&#160;Data&#160;Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Fonction table</td>
      <td>
        <font size=2> protocole&#160;: tds
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL&#160;Data&#160;Warehouse</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> protocole&#160;: tds
          <br>authentification&#160;: {protocole, windows}
          <br>adresse&#160;:
          <br>&#160;&#160;&#160;&#160;&#160; server
          <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>
    <tr>
      <td>SQL&#160;Data&#160;Warehouse</td>
      <td>Table</td>
      <td>Table, Vue</td>
      <td>
        <font size=2> protocole&#160;: tds
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Commande</td>
      <td>Procédure stockée</td>
      <td>
        <font size=2> protocole&#160;: tds
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Fonction table</td>
      <td>
        <font size=2> protocole&#160;: tds
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> protocole&#160;: tds
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Table</td>
      <td>Table, Vue</td>
      <td>
        <font size=2> protocole&#160;: tds
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensionnel</td>
      <td>Conteneur</td>
      <td>Modèle</td>
      <td>
        <font size=2> protocole&#160;: analysis-services
            <br>authentification : {windows, de base, anonyme, aucune}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensionnel</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocole&#160;: analysis-services
            <br>authentification : {windows, de base, anonyme, aucune}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modèle
            <br>&#160;&#160;&#160;&#160;&#160; objet
            <br>&#160;&#160;&#160;&#160;&#160; objectType : {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensionnel</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> protocole&#160;: analysis-services
            <br>authentification : {windows, de base, anonyme, aucune}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modèle
            <br>&#160;&#160;&#160;&#160;&#160; objet
            <br>&#160;&#160;&#160;&#160;&#160; objectType&#160;: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensionnel</td>
      <td>Table</td>
      <td>Dimension</td>
      <td>
        <font size=2> protocole&#160;: analysis-services
            <br>authentification : {windows, de base, anonyme, aucune}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modèle
            <br>&#160;&#160;&#160;&#160;&#160; objet
            <br>&#160;&#160;&#160;&#160;&#160; objectType&#160;: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>Table SQL&#160;Server Analysis&#160;Services</td>
      <td>Conteneur</td>
      <td>Modèle</td>
      <td>
        <font size=2> protocole&#160;: analysis-services
            <br>authentification : {windows, de base, anonyme, aucune}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model </font>
      </td>
    </tr>
    <tr>
      <td>Table SQL&#160;Server Analysis&#160;Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocole&#160;: analysis-services
            <br>authentification : {windows, de base, anonyme, aucune}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modèle
            <br>&#160;&#160;&#160;&#160;&#160; objet
            <br>&#160;&#160;&#160;&#160;&#160; objectType : {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Table SQL&#160;Server Analysis&#160;Services</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> protocole&#160;: analysis-services
            <br>authentification : {windows, de base, anonyme, aucune}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modèle
            <br>&#160;&#160;&#160;&#160;&#160; objet
            <br>&#160;&#160;&#160;&#160;&#160; objectType&#160;: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>Table SQL&#160;Server Analysis&#160;Services</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        <font size=2> protocole&#160;: analysis-services
            <br>authentification : {windows, de base, anonyme, aucune}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modèle
            <br>&#160;&#160;&#160;&#160;&#160; objet
            <br>&#160;&#160;&#160;&#160;&#160; objectType : {Table} </font>
      </td>
    </tr>
    <tr>
      <td>SQL&#160;Server&#160;Reporting&#160;Services</td>
      <td>Conteneur</td>
      <td>Serveur</td>
      <td>
        <font size=2> protocole&#160;: reporting-services
            <br>authentification&#160;: {windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; version : {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL&#160;Server&#160;Reporting&#160;Services</td>
      <td>Rapport</td>
      <td>Rapport</td>
      <td>
        <font size=2> protocole&#160;: reporting-services
            <br>authentification&#160;: {windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; path
            <br>&#160;&#160;&#160;&#160;&#160; version : {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> protocole&#160;: teradata
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Table</td>
      <td>Table, Vue</td>
      <td>
        <font size=2> protocole&#160;: teradata
            <br>authentification&#160;: {protocole, windows}
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Conteneur</td>
      <td>Modèle</td>
      <td>
        <font size="2"> protocole : mssql-mds
          <br>authentification&#160;: {windows}
          <br>adresse&#160;:
          <br>&#160;&#160;&#160;&#160;&#160; url
          <br>&#160;&#160;&#160;&#160;&#160; modèle
          <br>&#160;&#160;&#160;&#160;&#160; version </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Table</td>
      <td>Entité</td>
      <td>
        <font size="2"> protocole : mssql-mds
          <br>authentification&#160;: {windows}
          <br>adresse&#160;:
          <br>&#160;&#160;&#160;&#160;&#160; url
          <br>&#160;&#160;&#160;&#160;&#160; modèle
          <br>&#160;&#160;&#160;&#160;&#160; version
          <br>&#160;&#160;&#160;&#160;&#160; entité </font>
      </td>
    </tr>
    <tr>
      <td>Autre (pas l’un des types ci-dessus)</td>
      <td>*</td>
      <td>*</td>
      <td>
        <font size=2> protocole&#160;: generic-asset
            <br>adresse&#160;:
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>
</table>

<!---HONumber=AcomDC_0921_2016-->