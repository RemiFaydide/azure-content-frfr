<properties
	pageTitle="Répliquer des machines virtuelles VMware locales ou des serveurs physiques dans un site secondaire | Microsoft Azure"
	description="Utilisez cet article pour répliquer les machines virtuelles VMware ou les serveurs physiques Windows/Linux sur un site secondaire avec Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="nsoneji"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="nisoneji"/>


# Répliquer des machines virtuelles VMware locales ou des serveurs physiques dans un site secondaire


## Vue d'ensemble

InMage Scout dans Azure Site Recovery assure une réplication en temps réel entre des sites VMware locaux. InMage Scout est inclus dans les abonnements au service Azure Site Recovery.


## Composants requis

**Compte Azure** : vous devez disposer d’un compte [Microsoft Azure](https://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery.


## Étape 1 : Créer un coffre

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.
3. Cliquez sur **Créer nouveau** > **Création rapide**.
4. Dans **Name**, entrez un nom convivial pour identifier le coffre.
5. Dans **Region**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez [Tarification d’Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** dans la page **Recovery Services**.

## Étape 2 : Configurer le coffre et télécharger les composants InMage Scout

1. Cliquez sur **Create vault**.
2. Dans la page **Recovery Services**, cliquez sur le coffre pour ouvrir la page **Démarrage rapide**.
3. Dans la liste déroulante, sélectionnez **Entre deux sites VMware locaux**.
4. Téléchargez InMage Scout. Les fichiers d’installation pour tous les composants requis figurent dans le fichier .zip téléchargé.


## Étape 3 : Installer les mises à jour de composants

Consultez les dernières [mises à jour](#updates). Vous installerez les fichiers de mises à jour sur les serveurs dans l’ordre suivant :

1. Serveur RX, s'il existe
2. Serveurs de configuration
3. Serveurs de traitement
3. Serveurs cibles maîtres
4. Serveurs vContinuum
5. Serveur source (uniquement pour Windows Server)

Installez les mises à jour comme suit :

1. Téléchargez le fichier .zip de [mise à jour](https://aka.ms/asr-scout-update3). Ce fichier .zip contient les fichiers suivants :

	-  RX_8.0.3.0_GA\_Update_3_6684045\_17Mar16.tar.gz
	-  CX\_Windows_8.0.3.0_GA\_Update_3_5048668\_16Mar16.exe
	-  UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe
	-  UA\_RHEL6-64_8.0.3.0_GA\_Update_3_7101745\_04Apr16.zip
	-  vCon\_Windows_8.0.3.0_GA\_Update_3_6873369\_16Mar16.exe

2. Extrayez les fichiers .zip.
3. **Pour le serveur RX** : copiez **RX_8.0.3.0_GA\_Update_3_6684045\_17Mar16.tar.gz** sur le serveur RX et extrayez-le. Dans le dossier extrait, exécutez **/Install**.
4. **Pour le serveur de configuration/serveur de traitement** : copiez **CX\_Windows_8.0.3.0_GA\_Update_3_5048668\_16Mar16.exe** sur le serveur de configuration et le serveur de traitement. Double-cliquez dessus pour l’exécuter.
5. **Pour le serveur cible maître Windows** : pour mettre à jour l’agent unifié, copiez **UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe** sur le serveur cible maître. Double-cliquez dessus pour l’exécuter. Notez que l’agent unifié s’applique aussi au serveur source. Vous devez l’installer aussi sur le serveur source, comme indiqué plus loin dans cette liste.
6. **Pour le serveur cible maître Linux** : pour mettre à jour l’agent unifié, copiez **UA\_RHEL6-64_8.0.3.0_GA\_Update_3_7101745\_04Apr16.zip** sur le serveur cible maître et extrayez-le. Dans le dossier extrait, exécutez **/Install**.
7. **Pour le serveur vContinuum** : copiez **vCon\_Windows_8.0.3.0_GA\_Update_3_6873369\_16Mar16.exe** sur le serveur vContinuum. Vérifiez que vous avez fermé l’Assistant vContinuum. Double-cliquez sur le fichier pour l’exécuter.
8. **Pour le serveur source Windows** : pour mettre à jour l’agent unifié, copiez **UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe** sur le serveur source. Double-cliquez dessus pour l’exécuter.

## Étape 4 : Configurer la réplication
1. Configurez la réplication entre les sites VMware sources et cibles.
2. Pour obtenir des instructions, consultez la documentation InMage Scout téléchargée avec le produit. Vous pouvez également accéder à la documentation comme suit :

	- [Notes de publication](https://aka.ms/asr-scout-release-notes)
	- [Matrice de compatibilité](https://aka.ms/asr-scout-cm)
	- [Guide d’utilisation](https://aka.ms/asr-scout-user-guide)
	- [Guide d’utilisation de RX](https://aka.ms/asr-scout-rx-user-guide)
	- [Guide d'installation rapide](https://aka.ms/asr-scout-quick-install-guide)


## Mises à jour

### Azure Site Recovery Scout 8.0.1 Update 3
Update 3 comprend les résolutions de bogues et améliorations suivantes :

- L’inscription du serveur de configuration et de RX dans le coffre Site Recovery échoue quand ils sont derrière un proxy.
- Le nombre d’heures duquel l’objectif de point de récupération (RPO) n’est pas atteint n’est pas mis à jour dans le rapport d’intégrité.
- Le serveur de configuration ne se synchronise pas avec RX quand les détails du matériel ESX ou du réseau contiennent des caractères UTF-8.
- Le démarrage des contrôleurs de domaine Windows Server 2008 R2 échoue après la récupération.
- La synchronisation hors connexion ne fonctionne pas comme prévu.
- Après le basculement de machine virtuelle (VM), la suppression de la paire de réplication reste bloquée dans l’interface utilisateur CX pendant un laps de temps prolongé, et les utilisateurs ne peuvent pas terminer l’opération de restauration ou de reprise.
- Les opérations d’instantané globales effectuées par la tâche de cohérence ont été optimisées pour réduire les déconnexions d’applications telles que les clients SQL.
- Les performances de l’outil de cohérence (VACP.exe) ont été améliorées en réduisant l’utilisation de la mémoire requise pour créer des instantanés sur Windows.
- Le service d’installation push se bloque quand le mot de passe comporte plus de 16 caractères.
- vContinuum ne vérifie et ne demande pas de nouvelles informations d’identification vCenter lors de leur modification.
- Sur Linux, le gestionnaire de cache cible maître (cachemgr) ne télécharge pas les fichiers à partir du serveur de traitement, ce qui provoque la limitation de la paire de réplication.
- Quand l’ordre des disques du cluster de basculement physique (MSCS) n’est pas le même sur tous les nœuds, la réplication n’est pas définie pour certains volumes de cluster. <br/>Notez que le cluster doit être reprotégé pour tirer parti de ce correctif.
- La fonctionnalité SMTP ne fonctionne pas comme attendu après la mise à niveau de RX de Scout 7.1 vers Scout 8.0.1.
- Des statistiques ont été ajoutées au journal pour l’opération de restauration, pour suivre la durée nécessaire à cette dernière.
- Ajout de la prise en charge des systèmes d’exploitation Linux sur le serveur source :
	- Red Hat Enterprise Linux (RHEL) 6 Update 7
	- CentOS 6 Update 7
- L’interface utilisateur RX et CX peut maintenant afficher la notification pour la paire qui entre en mode bitmap.
- Les correctifs de sécurité suivants ont été ajoutés dans RX :

**Description du problème**|**Procédures de mise en œuvre**
---|---
Contournement de l’autorisation par falsification de paramètres|Accès restreint pour des utilisateurs non applicables.
Falsification de requête intersite|Implémentation du concept de jeton de page généré aléatoirement pour chaque page. <br/>Vous constaterez ce qui suit : <li> Il n’y a qu’une seule instance de connexion pour le même utilisateur.</li><li>L’actualisation de la page ne fonctionne pas. Elle redirige vers le tableau de bord.</li>
Téléchargement de fichiers malveillants|Fichiers restreints à certaines extensions. Extensions autorisées : 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml et zip.
Scripts intersites permanents | Ajout des validations des entrées.


>[AZURE.NOTE]
>
>-	Toutes les mises à jour de Site Recovery sont cumulatives. Update 3 contient tous les correctifs d’Update 1 et 2. Vous pouvez directement appliquer l’Update3 à 8.0.1 GA.
>-	Les mises à jour du serveur de configuration et de RX ne peuvent pas être annulées après avoir été appliquées sur le système.

### Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)

Les révisions apportées dans Update 2 sont les suivantes :

- **Serveur de configuration** : résolution d’un problème affectant l’exécution de la fonctionnalité de contrôle (gratuite pendant 31 jours) en cas d’inscription du serveur auprès de Site Recovery.
- **Agent unifié** : résolution d’un problème dans Update 1 empêchant l’installation de la mise à jour sur le serveur cible maître lors de sa mise à niveau de la version 8.0 à la version 8.0.1.


### Azure Site Recovery Scout 8.0.1 Update 1

Update 1 contient les correctifs de bogues et les nouvelles fonctionnalités suivants :

- 31 jours de protection gratuite par instance de serveur. Cela vous permet de tester les fonctionnalités ou de procéder à une validation technique.
	- Toutes les opérations sur le serveur, notamment le basculement et la restauration, sont gratuites pendant les 31 premiers jours à partir de l’heure à laquelle un serveur a été protégé à l’aide de Site Recovery Scout.
	- À compter du 32e jour, chaque serveur protégé sera facturé au tarif d’instance standard pour la protection Azure Site Recovery sur un site appartenant au client.
	- À tout moment, le nombre de serveurs protégés actuellement facturés est disponible sur le tableau de bord du coffre Azure Site Recovery.
- Ajout de la prise en charge de vSphere vCLI (Command-Line Interface) 5.5 Update 2.
- Ajout de la prise en charge des systèmes d'exploitation Linux sur le serveur source :
	- RHEL 6 Update 6
	- RHEL 5 Update 11
	- CentOS 6 Update 6
	- CentOS 5 Update 11
- Correctifs de bogues pour résoudre les problèmes suivants :
	- L’inscription du coffre échoue pour le serveur de configuration ou le serveur RX.
	- Les volumes de cluster n’apparaissent pas comme prévu quand les machines virtuelles en cluster sont à nouveau protégées lors de leur reprise.
	- La restauration automatique échoue quand le serveur cible maître est hébergé sur un autre serveur ESXi que les machines virtuelles de production locales.
	- Les autorisations du fichier de configuration sont modifiées lors de la mise à niveau vers la version 8.0.1, ce qui affecte la protection et les opérations.
	- Le seuil de resynchronisation n’est pas appliqué comme prévu, entraînant un comportement de réplication incohérent.
	- Les paramètres RPO n’apparaissent pas correctement dans l’interface du serveur de configuration. La valeur de données non compressée apparaît par erreur comme valeur compressée.
	-  L’opération Remove n’effectue pas la suppression prévue dans l’Assistant vContinuum, et la réplication n’est pas supprimée de l’interface du serveur de configuration.
	-  Dans l’Assistant vContinuum, le disque est désactivé automatiquement quand vous cliquez sur **Details** dans la vue de disque pendant la protection des machines virtuelles MSCS.
	- Pendant le scénario physique-à-virtuel (P2V), les services HP requis, tels que CIMnotify et CqMgHost, ne passent pas à l’état manuel dans la récupération de machine virtuelle. Cela allonge la durée de démarrage.
	- La protection de machine virtuelle Linux échoue quand le serveur cible maître contient plus de 26 disques.

## Étapes suivantes

Publiez vos questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

<!---HONumber=AcomDC_0921_2016-->