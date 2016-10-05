<properties
pageTitle="Utiliser les outils Azure Data Lake pour Visual Studio avec le bac à sable Hortonworks| Microsoft Azure"
description="Apprendre à utiliser les outils Azure Data Lake pour Visual Studio avec le bac à sable Hortonworks (exécuté sur une machine virtuelle locale). Grâce à ces outils, vous pouvez créer et exécuter des tâches Hive et Pig sur le bac à sable et afficher la sortie et l’historique de la tâche."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/26/2016"
ms.author="larryfr"/>

# Utiliser les outils Azure Data Lake pour Visual Studio avec Hortonworks Sandbox

Les outils Azure Data Lake pour Visual Studio incluent des outils pour travailler avec des clusters Hadoop génériques ainsi qu’avec Azure Data Lake et HDInsight. Ce document décrit les étapes nécessaires pour utiliser les outils Azure Data Lake avec Hortonworks Sandbox en cours d’exécution sur une machine virtuelle locale.

Hortonworks Sandbox permet de travailler avec Hadoop localement sur votre environnement de développement. Une fois que vous avez développé une solution et que vous souhaitez la mettre à l’échelle, vous pouvez passer à un cluster HDInsight.

## Composants requis

* Hortonworks Sandbox en cours d’exécution sur une machine virtuelle sur votre environnement de développement. Ce document a été écrit et testé avec le bac à sable en cours d’exécution dans Oracle VirtualBox, qui a été configuré à l’aide des informations contenues dans le document [Prise en main de l’écosystème Hadoop](hdinsight-hadoop-emulator-get-started.md).

* Visual Studio 2013 ou 2015, toute édition.

* [SDK Azure pour .NET](https://azure.microsoft.com/downloads/) 2.7.1 ou ultérieure

* [Outils Azure Data Lake pour Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)

## Configuration des mots de passe pour le bac à sable

Assurez-vous que Hortonworks Sandbox est en cours d’exécution, puis suivez les étapes du document [Prise en main de l’écosystème Hadoop](hdinsight-hadoop-emulator-get-started.md#set-passwords) pour configurer le mot de passe pour le compte SSH `root` et le compte Ambari `admin`. Ces mots de passe seront utilisés lors de la connexion au bac à sable à partir de Visual Studio.

## Connexion des outils au bac à sable

1. Ouvrez Visual Studio et sélectionnez __Affichage__, puis __Explorateur de serveurs__.

2. Dans l’__Explorateur de serveurs__, cliquez avec le bouton droit sur l’entrée __HDInsight__, puis sélectionnez __Connexion à l’émulateur HDInsight__.

    ![Connexion à l’émulateur HDInsight](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Dans la boîte de dialogue __Connexion à l’émulateur HDInsight__, entrez le mot de passe que vous avez configuré pour Ambari.

    ![Entrer le mot de passe Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Sélectionnez __Suivant__ pour continuer.

4. Utilisez le champ __Mot de passe__ pour entrer le mot de passe que vous avez configuré pour le compte `root`. Conservez la valeur par défaut dans les autres champs.

    ![Entrer le mot de passe racine](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Sélectionnez __Suivant__ pour continuer.

5. Attendez la validation des services pour poursuivre. Dans certains cas, la validation peut échouer et vous inviter à mettre à jour la configuration. Dans ce cas, sélectionnez le bouton __Mettre à jour__ et attendez la fin de la configuration et de la vérification du service.

    ![Erreurs et bouton Mettre à jour](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [AZURE.NOTE] Le processus de mise à jour utilise Ambari pour remplacer la configuration de Hortonworks Sandbox par celle exigée par les outils Azure Data Lake pour Visual Studio.

    Une fois la validation terminée, sélectionnez __Terminer__ pour terminer la configuration.

    ![Terminer la connexion](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [AZURE.NOTE] Selon la vitesse de votre environnement de développement et la quantité de mémoire allouée à la machine virtuelle, la configuration et la validation des services peuvent prendre quelques minutes.

Une fois ces étapes terminées, vous avez maintenant une entrée « Cluster HDInsight local » dans l’Explorateur de serveurs sous la section HDInsight.

## Écriture d’une requête Hive

Hive fournit un langage de requête de type SQL (HiveQL), pour travailler avec les données structurées. Utilisez les étapes suivantes pour apprendre à exécuter des requêtes ad hoc sur le cluster local.

1. Dans l’__Explorateur de serveurs__, cliquez avec le bouton droit sur l’entrée du cluster local que vous avez ajouté précédemment, puis sélectionnez __Écrire une requête Hive__.

    ![Écrire une requête Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Cette opération ouvre une nouvelle fenêtre de requête qui vous permet de saisir et de soumettre rapidement une requête au cluster local.

2. Entrez les informations suivantes dans la fenêtre de requête :

        select count(*) from sample_08;
    
    En haut de la fenêtre de requête, assurez-vous que la configuration du cluster local est sélectionnée, puis sélectionnez __Envoyer__. Laissez les autres valeurs (__lot__ et nom du serveur) par défaut.

    ![fenêtre de requête et bouton Envoyer](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Notez que vous pouvez également utiliser le menu déroulant en regard d’__Envoyer__ pour sélectionner __Avancé__. Cette opération ouvre une boîte de dialogue qui vous permet de sélectionner des options supplémentaires lors de l’envoi de la tâche.

    ![envoi avancé](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Une fois que vous avez envoyé la requête, l’état de la tâche s’affiche. Il comprend des informations sur la tâche telle qu’elle est traitée par Hadoop. L’entrée __Job State (État de la tâche)__ indique l’état actuel de la tâche. Cet état sera mis à jour régulièrement. Vous pouvez également utiliser l’icône d’actualisation pour rafraîchir l’état manuellement.

    ![État de la tâche](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Une fois que l’__état de la tâche__ est __Finished (Terminée)__, un graphe orienté acyclique dirigé s’affiche. Il décrit le chemin d’exécution qui a été déterminé par Tez (le moteur d’exécution par défaut de Hive sur le cluster local).
    
    > [AZURE.NOTE] Tez est également le moteur par défaut pour les clusters HDInsight sous Linux. En revanche, ce n’est pas le moteur par défaut des clusters HDInsight sous Windows. Pour l’utiliser avec ces derniers, vous devez ajouter la ligne `set hive.execution.engine = tez;` au début de votre requête Hive.

    Utilisez le lien __Sortie de la tâche__ pour afficher la sortie. Ici, c’est __823__, le nombre de lignes dans la table sample\_08. Vous pouvez afficher des informations de diagnostic sur la tâche à l’aide des liens __Journal de la tâche__ et __Download YARN Log (Télécharger le journal YARN)__.

4. Vous pouvez également exécuter des tâches Hive en mode interactif en modifiant le champ __Lot__ sur __Interactif__, puis sélectionnez __Exécuter__.

    ![Requête interactive](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Cette opération affiche le journal de sortie généré lors du traitement dans la fenêtre __HiveServer2 Output (Sortie HiveServer2)__.
    
    > [AZURE.NOTE] Ces informations sont également disponibles dans le lien __Journal de la tâche__ une fois la tâche terminée.

    ![Sortie HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## Création d’un projet Hive

Vous pouvez également créer un projet qui contient plusieurs scripts Hive. C’est utile lorsque vous avez plusieurs scripts liés que vous devez conserver ensemble ou gérer à l’aide d’un système de contrôle de versions.

1. Dans Visual Studio, sélectionnez __Fichier__ > __Nouveau__, puis Projet.

2. Dans la liste des projets, développez __Modèles__, __Azure Data Lake__, puis sélectionnez __HIVE (HDInsight)__. Dans la liste des modèles, sélectionnez __Hive Sample (Exemple Hive)__. Entrez un nom et un emplacement, puis sélectionnez __OK__.

    ![Modèle HIVE (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

Le projet __Hive Sample (Exemple Hive)__ contient deux scripts, __WebLogAnalysis.hql__ et __SensorDataAnalysis.hql__. Vous pouvez les envoyer à l’aide du bouton __Envoyer__ situé en haut de la fenêtre.

## Création d’un projet Pig

Contrairement à Hive qui offre un langage de type SQL pour travailler avec des données structurées, Pig fournit un langage (Pig Latin) permettant de développer un pipeline des transformations appliquées à vos données. Utilisez les étapes suivantes pour utiliser Pig avec le cluster local.

1. Ouvrez Visual Studio, sélectionnez __Fichier__ > __Nouveau__, puis __Projet__. Dans la liste des projets, développez __Modèles__, __Azure Data Lake__, puis sélectionnez __Pig (HDInsight)__. Dans la liste des modèles, sélectionnez __Pig Application (Application Pig)__. Entrez un nom et un emplacement, puis sélectionnez __OK__.

    ![Projet Pig (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Entrez les informations suivantes comme contenu du fichier __script.pig__ créé dans le cadre de ce projet.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int, 
            ip_address:chararray, 
            date:chararray, 
            time:chararray, 
            landing_page:chararray, 
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Pig utilise un autre langage que Hive,mais les tâches sont exécutées via le bouton __Envoyer__ pour les deux langages. La sélection de la liste déroulante sous __Envoyer__ affiche une boîte de dialogue Envoi avancé pour Pig.

    ![Envoi avancé Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)
    
3. L’état de la tâche et la sortie s’affichent de la même façon que pour une requête Hive.

    ![image d’une tâche pig terminée](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## Affichage des tâches

Les outils Azure Data Lake vous permettent également d’afficher facilement les informations sur les tâches qui ont été exécutées sur Hadoop. Utilisez les étapes suivantes pour afficher les tâches qui ont été exécutées sur le cluster local.

1. Dans l’__Explorateur de serveurs__,cliquez avec le bouton droit sur le cluster local, puis sélectionnez __Afficher les tâches__. Cette opération affichera la liste des tâches qui ont été soumises au cluster.

    ![Affichage des tâches](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Dans la liste des tâches, sélectionnez-en une pour en afficher les détails.

    ![sélectionner une tâche](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Les informations affichées sont similaires à celles qui s’affichent après l’exécution d’une requête Hive ou Pig, et sont complétées par des liens permettant d’afficher des informations sur la sortie et le journal.

3. Vous pouvez également modifier et soumettre à nouveau la tâche à partir d’ici.

## Affichage des bases de données Hive

1. Dans l’__Explorateur de serveurs__, développez l’entrée __cluster local HDInsight__, puis développez __Bases de données Hive__. Cette commande affiche les bases de données __Par défaut__ et __xademo__ sur le cluster local. Le développement d’une base de données révèle les tables qu’elle contient.

    ![bases de données développées](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Le développement d’une table affiche les colonnes qu’elle contient. Vous pouvez cliquer sur une table avec le bouton droit de la souris et sélectionner __Afficher les 100 premières lignes__ pour afficher rapidement les données.

    ![affichage des bases de données Hive](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### Propriétés des bases de données et des tables

Vous avez peut-être remarqué que vous pouvez choisir d’afficher les __propriétés__ d’une base de données ou d’une table. Cette commande affichera les détails de l’élément sélectionné dans la fenêtre Propriétés.

![Propriétés](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### Création d’une table

Pour créer une table, cliquez avec le bouton droit de la souris sur une base de données, puis sélectionnez __Créer une table__.

![Créer une table](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Vous pouvez ensuite créer la table à l’aide d’un formulaire. Vous pouvez afficher le code HiveQL brut qui sera utilisé pour créer la table en bas de cette page.

![créer un formulaire de table](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## Étapes suivantes

* [Se familiariser avec Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Didacticiel Hadoop - Prise en main de HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

<!---HONumber=AcomDC_0921_2016-->