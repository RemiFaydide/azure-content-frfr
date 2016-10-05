<properties
	pageTitle="Commencez par exécuter l’Assistant Activer la base de données pour Stretch | Microsoft Azure"
	description="Découvrez comment configurer une base de données pour Stretch Database en exécutant l’Assistant Activer la base de données pour Stretch."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/05/2016"
	ms.author="douglasl"/>

# Commencez par exécuter l’Assistant Activer la base de données pour Stretch

Pour configurer une base de données pour Stretch Database, exécutez l’Assistant Activer la base de données pour Stretch. Cette rubrique décrit les informations que vous devez entrer et les choix que vous devez effectuer dans l’Assistant.

Pour en savoir plus sur Stretch Database, consultez [Stretch Database](sql-server-stretch-database-overview.md).

 >   [AZURE.NOTE] Plus tard, si vous désactivez Stretch Database, gardez à l’esprit que la désactivation pour une table ou une base de données ne supprime pas l’objet distant. Si vous souhaitez supprimer la table distante ou la base de données distante, vous devez le faire à l’aide du portail de gestion Azure. Les objets distants continuent d’entraîner des coûts Azure jusqu’à leur suppression manuelle.

## Lancer l’Assistant

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la base de données sur laquelle vous souhaitez activer Stretch.

2.  Cliquez avec le bouton droit et sélectionnez **Tâches**, sélectionnez **Stretch**, puis **Activer** pour lancer l’Assistant.

## <a name="Intro"></a>Introduction
Examinez l’objectif de l’Assistant et les conditions préalables.

Vous devez au préalable remplir les conditions suivantes :

-   Vous devez être administrateur pour modifier les paramètres de base de données.
-   Vous devez avoir un abonnement Microsoft Azure.
-   Votre serveur SQL Server doit être en mesure de communiquer avec le serveur Azure à distance.

![Page d’introduction de l’Assistant Stretch Database][StretchWizardImage1]

## <a name="Tables"></a>Sélectionner des tables
Sélectionnez les tables que vous souhaitez activer pour Stretch.

Les tables comportant un grand nombre de lignes apparaissent en haut de la liste triée. Avant d’afficher la liste des tables, l’Assistant y recherche les types de données qui ne sont pas actuellement pris en charge par Stretch Database.

![Page de sélection des tables de l’Assistant Stretch Database][StretchWizardImage2]

|Colonne|Description|
|----------|---------------|
|(sans titre)|Cochez la case de cette colonne pour activer la table sélectionnée pour Stretch.|
|**Nom**|Spécifie le nom de la colonne dans la table.|
|(sans titre)|Dans cette colonne, un symbole peut représenter un avertissement qui n’empêche pas l’activation de la table sélectionnée pour Stretch. Il peut également représenter un problème de blocage qui vous empêche d’activer la table sélectionnée pour Stretch (par exemple, lorsque la table utilise un type de données non pris en charge). Placez le pointeur de la souris sur le symbole pour afficher plus d’informations dans une info-bulle. Pour plus d’informations, consultez [Limites de Stretch Database](sql-server-stretch-database-limitations.md).|
|**Étendu**|Indique si la table est déjà activée pour Stretch.|
|**Migrer**|Vous pouvez migrer une table entière (**Table entière**) ou appliquer un filtre sur une colonne existante de la table. Si vous souhaitez utiliser une autre fonction de filtre pour sélectionner les lignes à migrer, exécutez l’instruction ALTER TABLE afin de spécifier la fonction de filtre après avoir quitté l’Assistant. Pour plus d’informations sur la fonction de filtre, consultez [Sélection des lignes à migrer à l’aide d’une fonction de filtre](sql-server-stretch-database-predicate-function.md). Pour plus d’informations sur l’application de la fonction, consultez [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md) ou [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Lignes**|Spécifie le nombre de lignes dans la table.|
|**Taille (Ko)**|Spécifie la taille de la table en Ko.|

## <a name="Filter"></a>Fournir un filtre de ligne (facultatif)

Si vous souhaitez fournir une fonction de filtre pour sélectionner les lignes à migrer, effectuez les opérations suivantes dans la page **Sélectionner des tables**.

1.  Dans la liste **Sélectionner les tables à étirer**, cliquez sur **Table entière** dans la ligne de la table. La boîte de dialogue **Sélectionner les lignes à étirer** s’ouvre.

    ![Définir une fonction de filtre][StretchWizardImage2a]

2.  Dans la boîte de dialogue **Sélectionner les lignes à étirer**, sélectionnez **Choisir les lignes**.

3.  Dans le champ **Nom**, indiquez le nom de la fonction de filtre.

4.  Pour la clause **Où**, sélectionnez une colonne dans la table, choisissez un opérateur et spécifiez une valeur.

5. Cliquez sur **Vérifier** pour tester la fonction. Si la fonction renvoie des résultats de la table, autrement dit, s’il existe des lignes à migrer qui remplissent la condition, le test indique **Réussite**.

    >   [AZURE.NOTE] La zone de texte qui affiche la requête de filtre est en lecture seule. Vous ne pouvez pas modifier la requête dans la zone de texte.

6.  Cliquez sur Terminé pour revenir à la page **Sélectionner des tables**.

La fonction de filtre n’est créée dans SQL Server qu’une fois l’Assistant terminé. En attendant, vous pouvez revenir à la page **Sélectionner des tables** pour modifier ou renommer la fonction de filtre.

![Page Sélectionner des tables après définition d’une fonction de filtre][StretchWizardImage2b]

Si vous souhaitez utiliser un type différent de fonction de filtre pour sélectionner les lignes à migrer, effectuez l’une des opérations suivantes.

-   Quittez l’assistant et exécutez l’instruction ALTER TABLE pour activer Stretch pour la table et spécifier une fonction de filtre. Pour plus d’informations, consultez [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md).

-   Exécutez l’instruction ALTER TABLE pour spécifier une fonction de filtre une fois que vous avez quitté l’assistant. Pour connaître les étapes requises, reportez-vous à [Ajouter une fonction de filtre après avoir exécuté l’Assistant](sql-server-stretch-database-predicate-function.md#addafterwiz).

## <a name="Configure"></a>Configurer un déploiement Azure

1.  Connectez-vous à Microsoft Azure avec un compte Microsoft.

    ![Connexion à Azure dans l’Assistant Stretch Database][StretchWizardImage3]

2.  Sélectionnez l’abonnement Azure existant à utiliser pour Stretch Database.

3.  Sélectionnez une région Azure.
    -   Si vous créez un serveur, il est créé dans cette région.
    -   Si vous avez déjà des serveurs dans la région sélectionnée, l’Assistant les répertorie lorsque vous choisissez **Serveur existant**.

    Pour réduire la latence, choisissez la région Azure où se trouve votre ordinateur SQL Server. Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/).

4.  Spécifiez si vous souhaitez utiliser un serveur existant ou créer un serveur Azure.

    Si l’annuaire Active Directory sur votre serveur SQL Server est fédéré avec Azure Active Directory, vous pouvez éventuellement utiliser un compte de service fédéré pour que SQL Server communique avec le serveur Azure distant. Pour plus d’informations sur la configuration requise pour cette option, consultez [Options SET d’ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

	-   **Créer un serveur**

        1.  Créez une connexion et un mot de passe pour l’administrateur du serveur.

        2.  Si vous le souhaitez, utilisez un compte de service fédéré pour que SQL Server communique avec le serveur Azure distant.

		![Création d’un serveur Azure dans l’Assistant Stretch Database][StretchWizardImage4]

    -   **Serveur existant**

        1.  Sélectionnez le serveur Azure existant.

        2.  Sélectionnez la méthode d’authentification.

            -   Si vous sélectionnez **Authentification SQL Server**, fournissez l’identifiant et le mot de passe administrateur.

            -   Sélectionnez **Authentification intégrée à Active Directory** afin d’utiliser un compte de service fédéré pour la communication de SQL Server avec le serveur Azure distant. Si le serveur sélectionné n’est pas intégré à Azure Active Directory, cette option n’apparaît pas.

		![Sélection d’un serveur Azure existant dans l’Assistant Stretch Database][StretchWizardImage5]

## <a name="Credentials"></a>Informations d’identification sécurisées
Vous devez avoir une clé principale de base de données pour sécuriser les informations d’identification utilisées par Stretch Database pour se connecter à la base de données distante.

Si une clé principale de base de données existe déjà, entrez le mot de passe pour celle-ci.

![Page des informations d’identification sécurisées de l’Assistant Stretch Database][StretchWizardImage6b]

Si la base de données ne dispose pas d’une clé principale existante, entrez un mot de passe fort pour créer une clé principale de base de données.

![Page des informations d’identification sécurisées de l’Assistant Stretch Database][StretchWizardImage6]

Pour plus d’informations sur la clé principale de base de données, consultez [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) et [Créer une clé principale de base de données](https://msdn.microsoft.com/library/aa337551.aspx). Pour plus d’informations sur les informations d’identification créées par l’Assistant, consultez [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Sélectionner l’adresse IP
Utilisez la plage d’adresses IP de sous-réseau (recommandé) ou l’adresse IP publique, pour créer une règle de pare-feu sur Azure qui autorise SQL Server à communiquer avec le serveur Azure distant.

L’adresse IP, ou les adresses que vous entrez sur cette page, indiquent au serveur Azure d’autoriser les données entrantes, les requêtes et les opérations de gestion initiées par SQL Server à traverser le pare-feu Azure. L’Assistant ne modifie pas les paramètres du pare-feu sur le serveur SQL Server.

![Page de sélection de l’adresse IP de l’Assistant Stretch Database][StretchWizardImage7]

## <a name="Summary"></a>Résumé
Examinez les valeurs que vous avez entrées et les options que vous avez sélectionnées dans l’Assistant, ainsi que les coûts estimés sur Azure. Ensuite, sélectionnez **Terminer** pour activer Stretch.

![Page Résumé de l’Assistant Stretch Database][StretchWizardImage8]

## <a name="Results"></a>Résultats
Passez en revue les résultats.

Pour surveiller l’état de migration des données, consultez [Surveillance et dépannage de la migration de données (Stretch Database)](sql-server-stretch-database-monitor.md).

![Page Résultats de l’Assistant Stretch Database][StretchWizardImage9]

## <a name="KnownIssues"></a>Dépannage de l’Assistant
**Échec de l’Assistant Stretch Database.** Si Stretch Database n’est pas encore activé au niveau du serveur et que vous exécutez l’Assistant sans disposer des autorisations d’administrateur système pour l’activer, l’Assistant échoue. Demandez à l’administrateur système d’activer Stretch Database sur l’instance de serveur local, puis réexécutez l’Assistant. Pour plus d’informations, consultez [Condition préalable : Autorisation pour activer Stretch Database sur le serveur](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Étapes suivantes
Activer des tables supplémentaires pour Stretch Database. Surveiller la migration des données et gérer des tables et des bases de données Stretch.

-   [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md) afin d’activer des tables supplémentaires.

-   [Surveiller et résoudre les problèmes de migration de données (Stretch Database)](sql-server-stretch-database-monitor.md) pour connaître l’état de migration des données.

-   [Suspendre et reprendre Stretch Database](sql-server-stretch-database-pause.md)

-   [Gérer et dépanner Stretch Database](sql-server-stretch-database-manage.md)

-   [Sauvegarder des bases de données Stretch](sql-server-stretch-database-backup.md)

## Voir aussi

[Activer Stretch Database pour une base de données](sql-server-stretch-database-enable-database.md)

[Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png

<!-----HONumber=AcomDC_0810_2016-->