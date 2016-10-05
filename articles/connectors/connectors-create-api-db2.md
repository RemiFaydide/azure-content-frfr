<properties
    pageTitle="Ajouter le connecteur DB2 dans vos applications logiques | Microsoft Azure"
    description="Vue d’ensemble du connecteur DB2 avec les paramètres d’API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="09/19/2016"
   ms.author="plarsen"/>


# Prise en main du connecteur DB2
Microsoft Connector for DB2 connecte Logic Apps aux ressources stockées dans une base de données IBM DB2. Ce connecteur intègre un client Microsoft pour communiquer avec des serveurs DB2 distants sur un réseau TCP/IP, y compris des bases de données cloud (par exemple, IBM Bluemix dashDB ou IBM DB2 pour Windows s’exécutant dans Azure Virtualization) et des bases de données locales à l’aide de la passerelle de données locale. Consultez la liste des plates-formes et versions IBM DB2 prises en charge à la fin de cette rubrique.

Le connecteur DB2 prend en charge les opérations de base de données suivantes :

- énumération des tables de base de données ;
- lecture d’une ligne à l’aide de l’instruction SELECT ;
- lecture de toutes les lignes à l’aide de l’instruction SELECT ;
- ajout d’une ligne à l’aide de l’instruction INSERT ;
- modification d’une ligne à l’aide de l’instruction UPDATE ;
- suppression d’une ligne à l’aide de l’instruction DELETE.

Cette rubrique décrit comment utiliser le connecteur dans une application logique pour traiter les opérations de base de données.

>[AZURE.NOTE] Cette version de l’article s’applique à la disponibilité générale des applications logiques.

Pour plus d’informations sur Logic Apps, voir [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Actions d’application logique
Le connecteur prend en charge les actions d’application logique suivantes :

- Getables
- GetRow
- GetRows
- InsertRow
- UpdateRow
- DeleteRow


## Définir une application logique pour l’énumération des tables
La définition d’une application logique pour une opération quelconque implique de nombreuses étapes exécutées par le biais du Portail Microsoft Azure. Vous pouvez définir une action d’application logique destinée à répertorier les tables d’une base de données DB2, qui demande au connecteur de traiter une instruction de schéma DB2 (CALL SYSIBM.SQLTABLES).

### Définir une instance d’application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Renseignez les zones **Nom** (par exemple, « **Db2getTables** »), **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Cliquez sur **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### Définir un déclencheur et une action d’application logique
1.	Dans la liste **Modèles** du **concepteur d’applications logiques**, cliquez sur **Blank LogicApp** (Application logique vide).
2.	Dans la liste **déclencheurs**, cliquez sur **Périodicité**.
3.	Dans le déclencheur **Périodicité**, cliquez sur **Modifier**, cliquez sur la liste déroulante **Fréquence** et sélectionnez **Jour**, puis cliquez sur **Intervalle** et tapez **7**.
4.	Cliquez sur la zone **+ Nouvelle étape**, puis cliquez sur **Ajouter une action**.
5.	Dans la liste **actions**, tapez **db2** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis cliquez sur **DB2 - Get tables (Preview)** (DB2 - Obtenir les tables (version préliminaire)).

	![](./media/connectors-create-api-db2/Db2connectorActions.png)

6.	Dans le volet de configuration **DB2 - Get tables** (DB2 - Obtenir les tables), cochez la **case** pour activer l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale). Vous pouvez remarquer que les paramètres de cloud sont remplacés par les paramètres locaux.
	- Renseignez la zone **Serveur** sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez **ibmserver01:50000**.
	- Renseignez la zone **Base de données**. Par exemple, tapez **nwind**.
	- Renseignez la zone **Authentification**. Par exemple, sélectionnez **De base**.
	- Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez **db2admin**.
	- Renseignez la zone **Mot de passe**. Par exemple, tapez **Password1**.
	- Renseignez la zone **Passerelle**. Par exemple, cliquez sur **datagateway01**.
7. Cliquez sur **Créer**, puis sur **Enregistrer**.

	![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

8.	Au niveau du panneau **Db2getTables**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, cliquez sur le premier élément répertorié (dernière exécution).
9.	Dans le panneau **Exécution d’application logique**, cliquez sur **Détails de l’exécution**. Dans la liste **Action**, cliquez sur **Get\_tables**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Cliquez sur le **lien Entrées**. Visualisez les entrées. Cliquez sur le **lien Sorties**. Visualisez les sorties, qui doivent inclure une liste de tables.

	![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## Définir des connexions DB2
Le connecteur prend en charge les connexions aux bases de données locales et aux bases de données cloud à l’aide des propriétés de connexion ci-après.

Propriété | Description
--- | ---
server | La propriété de serveur requise accepte une valeur de chaîne représentant une adresse ou un alias TCP/IP, au format IPv4 ou IPv6, suivis d’un caractère deux-points et d’un numéro de port TCP/IP. 
database | La propriété de base de données requise accepte une valeur de chaîne représentant un nom de base de données relationnelle DRDA (RDBNAM). DB2 pour z/OS accepte une chaîne de 16 octets (la propriété de base de données correspond à un emplacement IBM DB2 pour z/OS). DB2 pour i5/OS accepte une chaîne de 18 octets (la propriété de base de données correspond à une base de données relationnelle IBM DB2 pour i). DB2 pour LUW accepte une chaîne de 8 octets.
authentication | La propriété d’authentification facultative accepte la valeur d’élément de liste De base ou Windows (Kerberos). 
username | La propriété de nom d’utilisateur requise accepte une valeur de chaîne. DB2 pour z/OS accepte une chaîne de 8 octets. DB2 pour i accepte une chaîne de 10 octets. DB2 pour Linux ou UNIX accepte une chaîne de 8 octets. DB2 pour Windows accepte une chaîne de 30 octets.
password | La propriété de mot de passe requise accepte une valeur de chaîne.
gateway | La propriété de passerelle requise accepte une valeur d’élément de liste représentant la passerelle de données locale définie pour Logic Apps dans le groupe de stockage.  

## Définir une connexion de passerelle locale
Le connecteur peut accéder à une base de données DB2 locale par le biais de la passerelle locale. Pour plus d’informations, voir les rubriques consacrées aux passerelles.

1. Dans le volet de configuration **Passerelles**, cochez la **case** pour activer l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale). Vous pouvez remarquer que les paramètres de cloud sont remplacés par les paramètres locaux.
2. Renseignez la zone **Serveur** sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez **ibmserver01:50000**.
3. Renseignez la zone **Base de données**. Par exemple, tapez **nwind**.
4. Renseignez la zone **Authentification**. Par exemple, sélectionnez **De base**.
5. Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez **db2admin**.
6. Renseignez la zone **Mot de passe**. Par exemple, tapez **Password1**.
7. Renseignez la zone **Passerelle**. Par exemple, cliquez sur **datagateway01**.
8. Cliquez sur **Créer** pour continuer.

	![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## Définir une connexion cloud
Le connecteur peut accéder à une base de données DB2 cloud.

1. Dans le volet de configuration **Passerelles**, laissez la **case** décochée pour désactiver l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale).
2. Renseignez la zone **Nom de la connexion**. Par exemple, tapez **hisdemo2**.
3. Renseignez la zone **DB2 server name** (Nom du serveur DB2) sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez **hisdemo2.cloudapp.net:50000**.
3. Renseignez la zone **DB2 database name** (Nom de la base de données DB2). Par exemple, tapez **nwind**.
4. Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez **db2admin**.
5. Renseignez la zone **Mot de passe**. Par exemple, tapez **Password1**.
6. Cliquez sur **Créer** pour continuer.

	![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## Définir une application logique pour la récupération de la totalité des lignes à l’aide de l’instruction SELECT
Vous pouvez définir une action d’application logique destinée à récupérer toutes les lignes d’une table DB2, qui demande au connecteur de traiter une instruction DB2 SELECT (par exemple, **SELECT * FROM AREA**).

### Définir une instance d’application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Renseignez les zones **Nom** (par exemple, « **Db2getRows** »), **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Cliquez sur **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### Définir un déclencheur et une action d’application logique
1.	Dans la liste **Modèles** du **concepteur d’applications logiques**, cliquez sur **Blank LogicApp** (Application logique vide).
2.	Dans la liste **déclencheurs**, cliquez sur **Périodicité**.
3.	Dans le déclencheur **Périodicité**, cliquez sur **Modifier**, cliquez sur la liste déroulante **Fréquence** et sélectionnez **Jour**, puis cliquez sur **Intervalle** et tapez **7**.
4.	Cliquez sur la zone **+ Nouvelle étape**, puis cliquez sur **Ajouter une action**.
5.	Dans la liste **actions**, tapez **db2** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis cliquez sur **DB2 - Get rows (Preview)** (DB2 - Obtenir les lignes (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), cliquez sur **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, cliquez sur **Créer**.

	![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
  
8. Dans le volet de configuration **Passerelles**, laissez la **case** décochée pour désactiver l’option **Connect via on-premises data gateway** (Connecter par le biais de la passerelle de données locale).
	- Renseignez la zone **Nom de la connexion**. Par exemple, tapez **HISDEMO2**.
	- Renseignez la zone **DB2 server name** (Nom du serveur DB2) sous la forme d’une adresse ou d’un alias suivis d’un caractère deux-points et d’un numéro de port. Par exemple, tapez **HISDEMO2.cloudapp.net:50000**.
	- Renseignez la zone **DB2 database name** (Nom de la base de données DB2). Par exemple, tapez **NWIND**.
	- Renseignez la zone **Nom d’utilisateur**. Par exemple, tapez **db2admin**.
	- Renseignez la zone **Mot de passe**. Par exemple, tapez **Password1**.
9. Cliquez sur **Créer** pour continuer.

	![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

10. Dans la liste **Nom de la table**, cliquez sur la **flèche vers le bas**, puis cliquez sur **AREA**.
11. Si vous le souhaitez, cliquez sur **Afficher les options avancées** pour spécifier les options de requête.
12. Cliquez sur **Save**.

	![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)

13.	Au niveau du panneau **Db2getRows**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, cliquez sur le premier élément répertorié (dernière exécution).
14.	Dans le panneau **Exécution d’application logique**, cliquez sur **Détails de l’exécution**. Dans la liste **Action**, cliquez sur **Get\_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Cliquez sur le **lien Entrées**. Visualisez les entrées. Cliquez sur le **lien Sorties**. Visualisez les sorties, qui doivent inclure une liste de lignes.

	![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## Définir une application logique pour l’ajout d’une ligne à l’aide de l’instruction INSERT
Vous pouvez définir une action d’application logique destinée à ajouter une ligne dans une table DB2, qui demande au connecteur de traiter une instruction DB2 INSERT (par exemple, **INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)**).

### Définir une instance d’application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Renseignez les zones **Nom** (par exemple, « **Db2insertRow** »), **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Cliquez sur **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### Définir un déclencheur et une action d’application logique
1.	Dans la liste **Modèles** du **concepteur d’applications logiques**, cliquez sur **Blank LogicApp** (Application logique vide).
2.	Dans la liste **déclencheurs**, cliquez sur **Périodicité**.
3.	Dans le déclencheur **Périodicité**, cliquez sur **Modifier**, cliquez sur la liste déroulante **Fréquence** et sélectionnez **Jour**, puis cliquez sur **Intervalle** et tapez **7**.
4.	Cliquez sur la zone **+ Nouvelle étape**, puis cliquez sur **Ajouter une action**.
5.	Dans la liste **actions**, tapez **db2** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis cliquez sur **DB2 - Insert row (Preview)** (DB2 - Insérer une ligne (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), cliquez sur **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, cliquez sur une connexion pour la sélectionner. Par exemple, cliquez sur **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **Nom de la table**, cliquez sur la **flèche vers le bas**, puis cliquez sur **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez la valeur « **99999** » pour **AREAID**, tapez « **Area 99999** », puis tapez la valeur « **102** » pour **REGIONID**.
10. Cliquez sur **Save**.

	![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
 
11.	Au niveau du panneau **Db2insertRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, cliquez sur le premier élément répertorié (dernière exécution).
12.	Dans le panneau **Exécution d’application logique**, cliquez sur **Détails de l’exécution**. Dans la liste **Action**, cliquez sur **Get\_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Cliquez sur le **lien Entrées**. Visualisez les entrées. Cliquez sur le **lien Sorties**. Visualisez les sorties, qui doivent inclure la nouvelle ligne.

	![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## Définir une application logique pour la récupération d’une ligne à l’aide de l’instruction SELECT
Vous pouvez définir une action d’application logique destinée à récupérer une ligne d’une table DB2, qui demande au connecteur de traiter une instruction DB2 SELECT WHERE (par exemple, **SELECT FROM AREA WHERE AREAID = '99999'**).

### Définir une instance d’application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Renseignez les zones **Nom** (par exemple, « **Db2getRow** »), **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Cliquez sur **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### Définir un déclencheur et une action d’application logique
1.	Dans la liste **Modèles** du **concepteur d’applications logiques**, cliquez sur **Blank LogicApp** (Application logique vide).
2.	Dans la liste **déclencheurs**, cliquez sur **Périodicité**.
3.	Dans le déclencheur **Périodicité**, cliquez sur **Modifier**, cliquez sur la liste déroulante **Fréquence** et sélectionnez **Jour**, puis cliquez sur **Intervalle** et tapez **7**.
4.	Cliquez sur la zone **+ Nouvelle étape**, puis cliquez sur **Ajouter une action**.
5.	Dans la liste **actions**, tapez **db2** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis cliquez sur **DB2 - Get rows (Preview)** (DB2 - Obtenir les lignes (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), cliquez sur **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, cliquez sur une connexion existante pour la sélectionner. Par exemple, cliquez sur **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **Nom de la table**, cliquez sur la **flèche vers le bas**, puis cliquez sur **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez « **99999** » pour **AREAID**.
10. Si vous le souhaitez, cliquez sur **Afficher les options avancées** pour spécifier les options de requête.
11. Cliquez sur **Save**.

	![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)

12.	Au niveau du panneau **Db2getRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, cliquez sur le premier élément répertorié (dernière exécution).
13.	Dans le panneau **Exécution d’application logique**, cliquez sur **Détails de l’exécution**. Dans la liste **Action**, cliquez sur **Get\_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Cliquez sur le **lien Entrées**. Visualisez les entrées. Cliquez sur le **lien Sorties**. Visualisez les sorties, qui doivent inclure une ligne.

	![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## Définir une application logique pour la modification d’une ligne à l’aide de l’instruction UPDATE
Vous pouvez définir une action d’application logique destinée à modifier une ligne d’une table DB2, qui demande au connecteur de traiter une instruction DB2 UPDATE (par exemple, **UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)**).

### Définir une instance d’application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Renseignez les zones **Nom** (par exemple, « **Db2updateRow** »), **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Cliquez sur **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### Définir un déclencheur et une action d’application logique
1.	Dans la liste **Modèles** du **concepteur d’applications logiques**, cliquez sur **Blank LogicApp** (Application logique vide).
2.	Dans la liste **déclencheurs**, cliquez sur **Périodicité**.
3.	Dans le déclencheur **Périodicité**, cliquez sur **Modifier**, cliquez sur la liste déroulante **Fréquence** et sélectionnez **Jour**, puis cliquez sur **Intervalle** et tapez **7**.
4.	Cliquez sur la zone **+ Nouvelle étape**, puis cliquez sur **Ajouter une action**.
5.	Dans la liste **actions**, tapez **db2** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis cliquez sur **DB2 - Update row (Preview)** (DB2 - Mettre à jour une ligne (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), cliquez sur **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, cliquez sur une connexion existante pour la sélectionner. Par exemple, cliquez sur **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **Nom de la table**, cliquez sur la **flèche vers le bas**, puis cliquez sur **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez la valeur « **99999** » pour **AREAID**, tapez « **Updated 99999** », puis tapez la valeur « **102** » pour **REGIONID**.
10. Cliquez sur **Save**.

	![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)

11.	Au niveau du panneau **Db2updateRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, cliquez sur le premier élément répertorié (dernière exécution).
12.	Dans le panneau **Exécution d’application logique**, cliquez sur **Détails de l’exécution**. Dans la liste **Action**, cliquez sur **Get\_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Cliquez sur le **lien Entrées**. Visualisez les entrées. Cliquez sur le **lien Sorties**. Visualisez les sorties, qui doivent inclure la nouvelle ligne.

	![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## Définir une application logique pour la suppression d’une ligne à l’aide de l’instruction DELETE
Vous pouvez définir une action d’application logique destinée à supprimer une ligne d’une table DB2, qui demande au connecteur de traiter une instruction DB2 DELETE (par exemple, **DELETE FROM AREA WHERE AREAID = '99999'**).

### Définir une instance d’application logique
1.	Dans le **Panneau de démarrage Azure**, sélectionnez **+** (signe plus), **Web + mobile**, puis **Application logique**.
2.	Renseignez les zones **Nom** (par exemple, « **Db2deleteRow** »), **Abonnement**, **Groupe de ressources**, **Emplacement** et **Plan App Service**. Cliquez sur **Épingler au tableau de bord**, puis sélectionnez **Créer**.

### Définir un déclencheur et une action d’application logique
1.	Dans la liste **Modèles** du **concepteur d’applications logiques**, cliquez sur **Blank LogicApp** (Application logique vide).
2.	Dans la liste **déclencheurs**, cliquez sur **Périodicité**.
3.	Dans le déclencheur **Périodicité**, cliquez sur **Modifier**, cliquez sur la liste déroulante **Fréquence** et sélectionnez **Jour**, puis cliquez sur **Intervalle** et tapez **7**.
4.	Cliquez sur la zone **+ Nouvelle étape**, puis cliquez sur **Ajouter une action**.
5.	Dans la liste **actions**, cliquez sur **db2** dans la zone de modification **Search for more actions** (Rechercher d’autres actions), puis cliquez sur **DB2 - Delete row (Preview)** (DB2 - Supprimer une ligne (version préliminaire)).
6. Dans l’action **Get rows (Preview)** (Obtenir les lignes (version préliminaire)), cliquez sur **Modifier la connexion**.
7. Dans le volet de configuration **Connexions**, cliquez sur une connexion existante pour la sélectionner. Par exemple, cliquez sur **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Dans la liste **Nom de la table**, cliquez sur la **flèche vers le bas**, puis cliquez sur **AREA**.
9. Entrez des valeurs pour toutes les colonnes requises (signalées par un astérisque rouge). Par exemple, tapez « **99999** » pour **AREAID**.
10. Cliquez sur **Save**.

	![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)

11.	Au niveau du panneau **Db2deleteRow**, dans la liste **Toutes les exécutions** figurant sous **Résumé**, cliquez sur le premier élément répertorié (dernière exécution).
12.	Dans le panneau **Exécution d’application logique**, cliquez sur **Détails de l’exécution**. Dans la liste **Action**, cliquez sur **Get\_rows**. Examinez la zone **État**, qui doit présenter la valeur **Opération réussie**. Cliquez sur le **lien Entrées**. Visualisez les entrées. Cliquez sur le **lien Sorties**. Visualisez les sorties, qui doivent inclure la ligne supprimée.

	![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## Détails techniques

## Actions
Une action est une opération effectuée par le flux de travail défini dans une application logique. Le connecteur de base de données DB2 inclut les actions ci-après.

|Action|Description|
|--- | ---|
|[GetRow](connectors-create-api-db2.md#get-row)|Récupère une ligne unique d’une table DB2|
|[GetRows](connectors-create-api-db2.md#get-rows)|Récupère les lignes d’une table DB2|
|[InsertRow](connectors-create-api-db2.md#insert-row)|Insère une nouvelle ligne dans une table DB2|
|[DeleteRow](connectors-create-api-db2.md#delete-row)|Supprime une ligne d’une table DB2|
|[GetTables](connectors-create-api-db2.md#get-tables)|Récupère les tables d’une base de données DB2|
|[UpdateRow](connectors-create-api-db2.md#update-row)|Met à jour une ligne existante d’une table DB2|

### Détails de l’action

Dans cette section, consultez les détails relatifs à chaque action, y compris toutes les propriétés d’entrée requises ou facultatives et toute sortie correspondante associée au connecteur.

#### Obtenir une ligne 
Récupère une ligne unique d’une table DB2.

| Nom de la propriété| Display Name |Description|
| ---|---|---|
|table * | Nom de la table |Nom de la table DB2|
|id * | ID de la ligne |Identificateur unique de la ligne à récupérer|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Item

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|


#### Obtenir des lignes 
Récupère les lignes d’une table DB2.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table DB2|
|$skip|Nombre à ignorer|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|Nombre maximal à récupérer|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|Requête de filtre|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|Trier par|Requête orderBy ODATA pour spécifier l’ordre des entrées|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
|---|---|
|value|array|


#### Insérer une ligne 
Insère une nouvelle ligne dans une table DB2.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table DB2|
|item*|Ligne|Ligne à insérer dans la table DB2 spécifiée|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Item

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|


#### Supprimer la ligne 
Supprime une ligne d’une table DB2.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table DB2|
|id*|ID de la ligne|Identificateur unique de la ligne à supprimer|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Aucune.

#### Obtenir des tables 
Récupère les tables d’une base de données DB2.

Il n'existe aucun paramètre pour cet appel.

##### Détails des résultats 
TablesList

| Nom de la propriété | Type de données |
|---|---|
|value|array|

#### Mettre à jour une ligne 
Met à jour une ligne existante d’une table DB2.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Nom de la table|Nom de la table DB2|
|id*|ID de la ligne|Identificateur unique de la ligne à mettre à jour|
|item*|Ligne|Ligne avec valeurs mises à jour|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats  
Item

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|


### Réponses HTTP

Lorsque vous exécutez des appels de diverses actions, vous pouvez obtenir certaines réponses. Le tableau suivant présente les réponses et leurs descriptions :

|Nom|Description|
|---|---|
|200|OK|
|202|Acceptée|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s'est produite|
|default|L’opération a échoué.|


## Plateformes et versions DB2 prises en charge
Le connecteur prend en charge les plateformes et versions IBM DB2 ci-après, ainsi que les produits IBM DB2 compatibles (par exemple, IBM Bluemix dashDB) qui prennent en charge les versions 10 et 11 de SQL Access Manager (SQLAM) DRDA (Distributed Relational Database Architecture).

- IBM DB2 pour z/OS 11.1
- IBM DB2 pour z/OS 10.1
- IBM DB2 pour i 7.3
- IBM DB2 pour i 7.2
- IBM DB2 pour i 7.1
- IBM DB2 pour LUW 11
- IBM DB2 pour LUW 10.5


## Étapes suivantes

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0921_2016-->