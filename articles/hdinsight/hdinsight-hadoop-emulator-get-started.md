<properties
	pageTitle="Utilisation d’un bac à sable (sandbox) Hadoop pour en savoir plus sur Hadoop | Microsoft Azure"
	description="Pour en savoir plus sur l’utilisation de l’écosystème Hadoop, vous pouvez configurer un bac à sable (sandbox) Hadoop à partir de Hortonworks sur une machine virtuelle Azure. "
	keywords="émulateur Hadoop, bac à sable (sandbox) hadoop"
	editor="cgronlun"
	manager="jhubbard"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="nitinme"/>

# Prise en main de l’écosystème Hadoop avec un bac à sable (sandbox) Hadoop sur une machine virtuelle

Apprenez à installer le bac à sable (sandbox) Hadoop de Hortonworks sur une machine virtuelle pour vous familiariser avec l’écosystème Hadoop. Le bac à sable fournit un environnement de développement local pour se familiariser avec Hadoop, Hadoop Distributed File System (HDFS) et la soumission de tâches.

## Composants requis

* [Oracle VirtualBox](https://www.virtualbox.org/)

Une fois que vous êtes familiarisé avec Hadoop, vous pouvez commencer à l’utiliser sur Azure en créant un cluster HDInsight. Pour plus d’informations sur la prise en main, consultez [Prise en main de Hadoop sur HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

## Téléchargement et installation de la machine virtuelle

1. À l’adresse [http://hortonworks.com/downloads/#sandbox](http://hortonworks.com/downloads/#sandbox), sélectionnez __DOWNLOAD FOR VIRTUALBOX__ pour HDP 2.4 sur Hortonworks Sandbox. Vous serez invité à vous inscrire à Hortonworks avant que le téléchargement commence.

    ![Image du lien pour le téléchargement de Hortonworks Sandbox pour VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)

2. Dans la même page web, sélectionnez le __VirtualBox Install Guide__ (Guide d’installation VirtualBox) pour HDP 2.4 sur Hortonworks Sandbox. Cette opération téléchargera un fichier PDF contenant les instructions d’installation de la machine virtuelle.

    ![Consulter le guide d’installation](./media/hdinsight-hadoop-emulator-get-started/view-install-guide.png)

## Démarrage de la machine virtuelle

1. Démarrez VirtualBox, sélectionnez Hortonworks Sandbox, sélectionnez __Démarrer__, puis __Démarrage normal__.

    ![Démarrage normal](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)

2. Une fois que la machine virtuelle a terminé le processus de démarrage, elle affiche les instructions de connexion. Ouvrez un navigateur web et accédez à l’URL affichée (généralement http://127.0.0.1:8888).

## Définition des mots de passe

1. Dans la page de __prise en main__ de Hortonworks Sandbox, sélectionnez __View Advanced Options (Options d’affichage avancées)__. Utilisez les informations de cette page pour vous connecter à Hortonworks Sandbox à l’aide de SSH. Utilisez le nom et le mot de passe fournis.

    > [AZURE.NOTE] Si vous n’avez pas de client SSH installé, vous pouvez utiliser le client SSH en ligne fourni par la machine virtuelle à l’adresse \_\_http://localhost:4200/__.

    La première fois que vous vous connectez à l’aide du client SSH, vous serez invité à modifier le mot de passe du compte racine. Entrez le nouveau mot de passe que vous utiliserez pour vous connecter à l’aide du client SSH à l’avenir.

2. Une fois connecté, entrez la commande suivante :

        ambari-admin-password-reset
    
    Lorsque vous y êtes invité, indiquez le mot de passe du compte d’administrateur Ambari. Il sera utilisé pour accéder à l’interface utilisateur web d’Ambari.

## Utilisation de la commande Hive

1. À partir d’une connexion SSH au bac à sable, utilisez la commande suivante pour démarrer l’interpréteur de commandes Hive :

        hive

2. Une fois l’interpréteur de commandes a démarré, procédez comme suit pour afficher les tables fournies avec le bac à sable :

        show tables;

3. Utilisez la commande suivante pour récupérer 10 lignes de la table `sample_07` :

        select * from sample_07 limit 10;

## Étapes suivantes

* [Apprendre à utiliser Visual Studio avec Hortonworks Sandbox](hdinsight-hadoop-emulator-visual-studio.md)
* [Se familiariser avec Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Didacticiel Hadoop - Prise en main de HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

<!---HONumber=AcomDC_0921_2016-->