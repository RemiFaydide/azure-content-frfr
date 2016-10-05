<properties 
    pageTitle="Notes de publication de StorSimple 8000 Update 0.1 | Microsoft Azure"
    description="Décrit les nouvelles fonctionnalités, les correctifs, les problèmes non résolus et les solutions de contournement disponibles pour la version d’octobre 2014 de Microsoft Azure StorSimple (Update 0.1)."
    services="storsimple"
    documentationCenter="NA"
    authors="alkohli"
    manager="carmonm"
    editor="" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="09/21/2016"
    ms.author="alkohli" />

# Notes de publication de StorSimple série 8000 Update 0.1 – Octobre 2014  

## Vue d'ensemble

Les notes de publication suivantes identifient les problèmes critiques non résolus relatifs à la version StorSimple série 8000 Update 0.1, publiée en octobre 2014. Elles contiennent également une liste des mises à jour du logiciel et du microprogramme StorSimple incluses dans cette version. Il s’agit de la première version importante postérieure à la mise à disposition générale de la version StorSimple série 8000, en juillet 2014. Elle correspond à la version logicielle 6.3.9600.17312.

Nous vous recommandons de rechercher les mises à jour éventuelles et de les appliquer immédiatement après avoir installé l’appareil. Vous pouvez également activer les mises à jour automatiques pour télécharger et installer les mises à jour de haute priorité à partir de Microsoft dès qu’elles sont mises à disposition. Consultez la rubrique [Mettre à jour votre appareil StorSimple](storsimple-update-device.md) pour plus d'informations.

Lisez les informations contenues dans les notes de publication avant de déployer les mises à jour dans votre solution StorSimple.

>[AZURE.IMPORTANT]
> 
-	Utilisez le service StorSimple Manager, pas Windows PowerShell pour StorSimple, pour installer les mises à jour d’octobre.
-	Les mises à jour prennent généralement environ 3 heures.
-	La version d’octobre de StorSimple ne contient aucune mise à jour pour l’appareil virtuel StorSimple. Vous pouvez toujours appliquer les mises à jour Windows disponibles, y compris les correctifs de sécurité récents, mais celles-ci ne modifieront pas la version de l’appareil virtuel.

Assurez-vous que les conditions préalables suivantes sont remplies avant de mettre l’appareil StorSimple à jour.

- Vérifiez que les deux contrôleurs de l’appareil sont actifs avant de rechercher des mises à jour. Si un des contrôleurs n’est pas actif, la recherche échoue. Pour vérifier que les contrôleurs fonctionnent correctement, accédez à **État du matériel** dans la page **Maintenance**. Si des composants **nécessitent une attention**, contactez le support technique de Microsoft avant de continuer.
- Vérifiez que les adresses IP fixes des contrôleurs 0 et 1 prennent le routage en charge et permettent la connexion à Internet, car elles sont utilisées pour installer les mises à jour sur l’appareil. Vous pouvez utiliser l’[applet de commande Test-Connection](https://technet.microsoft.com/library/hh849808.aspx) pour effectuer un test ping sur une adresse connue en dehors du réseau, telle que outlook.com, afin de vérifier que le contrôleur peut se connecter au réseau externe.
- Vérifiez que les ports de sortie requis sont disponibles pour la communication sortante sur votre appareil StorSimple. Pour plus d’informations, consultez la rubrique [Configuration réseau requise pour votre appareil StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
- Si la version logicielle de l’appareil est supérieure à 6.3.9600.17312 (mise à jour d’octobre 2014), désactivez les ports de données 2 et 3, le cas échéant, avant de commencer la mise à jour. Si vous laissez le port de données 2 ou 3 activé lors de la mise à jour, le contrôleur de l’appareil risque de passer en mode de récupération. Notez que lorsque vous désactivez les interfaces réseau, tous les volumes associés sont mis hors connexion, et les E/S sont interrompues pendant toute la durée de la mise à jour.

## Nouveautés dans la version d’octobre

Cette mise à jour inclut les améliorations suivantes :

- Vous pouvez désormais utiliser l’interface utilisateur du service StorSimple Manager pour gérer les contrôleurs d’appareil. Les actions de gestion comprennent le redémarrage, l’arrêt ou la mise sous tension des contrôleurs. Pour plus d’informations, consultez la page [Gestion des contrôleurs d’appareil](storsimple-manage-device-controller.md).
- Vous pouvez planifier l’allocation de bande passante WAN suivant des combinaisons jours de la semaine/heure de la journée. Cela vous permet d’optimiser l’utilisation de la bande passante WAN pendant les heures creuses. Des modèles de bande passante différents sont autorisés pour des conteneurs de volumes distincts. Pour plus d’informations, consultez la page [Gérer vos modèles de bande passante StorSimple](storsimple-manage-bandwidth-templates.md).
- Vous pouvez configurer des notifications par courrier électronique pour informer le ou les administrateurs et d’autres personnes des problèmes existants ou potentiels. Pour plus d’informations, consultez la page [Configuration des paramètres d’alerte](storsimple-manage-alerts.md#configure-alert-settings).

## Problèmes résolus dans la version d’octobre


Le tableau suivant récapitule les problèmes qui ont été résolus dans cette mise à jour.

| Non. | Fonctionnalité | Problème | S’applique à un appareil physique | S’applique à un appareil virtuel |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Interfaces réseau | Dans la version précédente, les interfaces réseau DATA 2 et DATA 3 étaient échangées dans le logiciel. Ce problème a été résolu dans cette mise à jour. Effacez les paramètres et désactivez ces interfaces réseau avant d’installer la mise à jour. Après avoir installé la mise à jour, vous devez reconfigurer ces interfaces. | Oui | Non |
| 2 | Package de prise en charge | Dans la version précédente, si vous exécutiez l’applet de commande **Export-HcsSupportPackage** de Windows PowerShell pour récupérer le contrôleur de gestion de la carte de base (BMC), l’opération échouait avec l’avertissement suivant : « L’opération a réussi sur ce contrôleur, mais a échoué sur le contrôleur homologue en raison des erreurs suivantes. Vérifiez si l’homologue est sain et si le nœud actuel peut se connecter à l’homologue. » Ce problème est maintenant résolu. | Oui | Non |
| 3 | Basculement de l’appareil | Dans la version précédente, il existait un risque d’incohérence de données si une tâche de **sauvegarde de détection** échouait lors d’un basculement d’appareil. Ce problème est maintenant résolu. | Oui | Non |
| 4 | Basculement de l’appareil | Dans la version précédente, après un basculement d’appareil, les sauvegardes étaient visibles, mais le conteneur de volumes associé n’était pas présent sur l’appareil cible. Ce problème est maintenant résolu. | Oui | Non |
| 5 | Basculement de l’appareil | Dans la version précédente, l’énumération des sauvegardes cloud réalisée pendant l’opération de restauration du Registre comportait un bogue qui pouvait conduire à une incohérence de données en cas de problèmes de connectivité au cloud. | Oui | Non |
| 6 | Mise à jour du microprogramme | Dans la version précédente, la tâche de mise à jour du microprogramme de l’appareil échouait et renvoyait une erreur qui indiquait que les applets de commande n’étaient pas reconnaissables et que la mise à jour avait échoué pour cette raison. Le contrôleur passait ensuite en mode de récupération. Ce problème est maintenant résolu. | Oui | Non |
| 7 | Installation | Les erreurs causées par une mise en image incorrecte de l’appareil lors de l’installation ont été résolues. | Oui | Non |
| 8 | Réinitialisation aux paramètres d’usine | Si vous le souhaitez, vous pouvez désormais ignorer la vérification des microprogrammes pour la réinitialisation aux paramètres d’usine. Il s’agit d’une modification par rapport à la version précédente. | Oui | Non |
| 9 | Réinitialisation aux paramètres d’usine | Dans la version précédente, lorsqu’une applet de commande de réinitialisation aux paramètres d’usine était exécutée, la version des microprogrammes était vérifiée uniquement pour certains composants matériels. Des vérifications de microprogramme supplémentaires étaient effectuées après le premier redémarrage du processus, ce qui pouvait entraîner l’échec de la réinitialisation. Ce correctif garantit que toutes les vérifications de microprogramme sont effectuées lors de l’exécution de l’applet de commande de réinitialisation aux paramètres d’usine et avant le premier redémarrage du système. | Oui | Non |
| 10 | Rotation des clés du compte de stockage | L’applet de commande **Invoke-HcsmServiceDataEncryptionKeyChange** utilisée pour effectuer la rotation des clés du compte de stockage invite désormais l’utilisateur à entrer la clé de chiffrement des données du service. Il s’agit d’une modification de la version précédente, dans laquelle la clé de chiffrement était considérée comme un paramètre incorporé. | Oui | Non |
| 11 | Restauration automatique dans les 24 heures | Lors de la récupération d’urgence, le nettoyage sur l’appareil n’était pas effectué correctement, ce qui provoquait l’échec de la restauration automatique. Ce problème a été résolu dans cette version. | Oui | Non |

## Problèmes connus dans la version d’octobre

Le tableau suivant récapitule les problèmes connus de cette version.

| Non. | Fonctionnalité | Problème | Commentaires/solution de contournement | S’applique à un appareil physique | S’applique à un appareil virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Réinitialisation aux paramètres d’usine | Dans certains cas, lorsque vous effectuez une réinitialisation aux paramètres d’usine, l’appareil StorSimple peut se bloquer et afficher le message suivant : **La réinitialisation aux paramètres d’usine est en cours (phase 8)**. Cela se produit si vous appuyez sur Ctrl+C alors que l’applet de commande est en cours. | N’appuyez pas sur Ctrl+C après avoir lancé une réinitialisation aux paramètres d’usine. Si vous avez déjà effectué cette opération, contactez le support technique Microsoft. | Oui | Non |
| 2 | Réinitialisation aux paramètres d’usine | Ne réinitialisez pas aux paramètres d’usine un appareil StorSimple qui a été mis à jour vers la version d’octobre 2014 à partir de la version mise la disposition générale. | Cette opération fonctionne uniquement si un correctif est installé. Contactez le support technique Microsoft pour obtenir ce correctif. | Oui | Non |	
| 3 | Disque quorum | Dans de rares cas, si la majorité des disques du boîtier EBOD d’un appareil 8600 sont déconnectés, ce qui signifie qu’il n’y a pas de disque quorum, le pool de stockage est hors connexion. Il reste hors connexion même si les disques sont reconnectés. | Vous devez redémarrer l’appareil. Si le problème persiste, contactez le support technique Microsoft. | Oui | Non |
| 4 | Échec des instantanés cloud | Dans de rares cas, un instantané cloud peut échouer et renvoyer l’erreur **Limite de sauvegarde maximale atteinte**. Cela se produit si vous avez plus de 255 clones en ligne sur un même appareil, provenant du volume d’origine qui a été supprimé. | | Oui | Oui |
| 5 | ID de contrôleur incorrect | Lorsqu’un contrôleur est remplacé, le contrôleur 0 peut apparaître comme contrôleur 1. Pendant le remplacement du contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme l’ID du contrôleur homologue. Dans de rares cas, ce comportement peut également se produire après un redémarrage du système. |Aucune action utilisateur n’est requise. Cette situation se résout automatiquement une fois le contrôleur remplacé. | Oui | Non |
| 6 | Graphiques d’analyse de l’appareil | Dans le service StorSimple Manager, les graphiques d’analyse de l’appareil ne fonctionnent pas lorsque l’authentification de base ou NTLM est activée dans la configuration du serveur proxy pour l’appareil. | Modifiez la configuration du proxy web pour l’appareil inscrit auprès du service StorSimple Manager afin que l’authentification soit définie sur AUCUNE. Pour cela, exécutez l’applet de commande Set-HcsWebProxy de Windows PowerShell pour StorSimple. | Oui | Oui |
| 7 | Comptes de stockage | La suppression du compte de stockage à l’aide du service de stockage n’est pas prise en charge. En effet, cette opération donnerait lieu à une situation dans laquelle il serait impossible de récupérer les données utilisateur. | | Oui | Oui |
| 8 | Basculement de l’appareil | Le basculement multiple d’un conteneur de volumes d’un même appareil source vers différents appareils cibles n’est pas pris en charge. | Si un appareil inactif est basculé vers plusieurs appareils, les conteneurs de volumes du premier appareil basculé perdent la propriété des données. Après un basculement de ce type, les conteneurs de volumes concernés apparaissent ou se comportent différemment lorsque vous les affichez dans le portail Azure Classic. | Oui | Non |
| 9 | Installation | Lors de l’installation de l’adaptateur StorSimple pour SharePoint, vous devez fournir une adresse IP d’appareil pour que l’installation s’effectue correctement. | | Oui | Non |
| 10 | Proxy web | Si HTTPS est défini comme protocole dans la configuration du proxy web, la communication appareil-service est altérée et l’appareil se met hors connexion. Des packages de prise en charge sont également générés, ce qui consomme de nombreuses ressources de l’appareil. | Vérifiez que le protocole défini pour l’URL du proxy web est bien HTTP. Découvrez comment [configurer le proxy web pour votre appareil](storsimple-configure-web-proxy.md). | Oui | Non |
| 11 | Proxy web | Si vous configurez et activez le proxy web sur un appareil inscrit, vous devez redémarrer le contrôleur actif sur votre appareil. | | Oui | Non |
| 12 | Latence de cloud élevée et charge de travail d’E/S élevée | Lorsque l’appareil StorSimple doit gérer à la fois des latences de cloud très élevées (de l’ordre de quelques secondes) et une charge de travail d’E/S élevée, ses volumes se détériorent et une défaillance peut se produire au niveau des E/S, avec l’erreur « appareil non prêt ». | Vous devez redémarrer les contrôleurs de l’appareil manuellement ou effectuer un basculement d’appareil pour résoudre ce problème. | Oui | Non |

## Mises à jour de l’appareil physique dans la version d’octobre

Lorsque ces mises à jour sont appliquées à un appareil physique, la version du logiciel devient 6.3.9600.17312. Sauf indication contraire, ces notes s’appliquent à tous les modèles d’appareil StorSimple. Pour plus d’informations sur ces mises à jour, consultez la page [Mise à jour de l’appareil physique d’octobre 2014 pour Microsoft Azure StorSimple Appliance](http://support.microsoft.com/kb/2986997).

## Mises à jour pour le microprogramme et le contrôleur SAS (Serial Attached SCSI) dans la version d’octobre

Cette version met à jour le pilote et le microprogramme du contrôleur SAS de votre appareil physique. Pour plus d’informations sur la mise à jour du contrôleur SAS, consultez la page [Mise à jour d’octobre 2014 pour les contrôleurs SAS LSI dans Microsoft Azure StorSimple Appliance](http://support.microsoft.com/kb/2987020).

Cette version applique également une mise à jour de microprogramme cumulative, qui résout les problèmes de fiabilité posés par les composants matériels de l’appareil. Pour plus d’informations sur la mise à jour du microprogramme, consultez la page [Mise à jour du microprogramme d’octobre 2014 pour Microsoft Azure StorSimple Appliance](http://support.microsoft.com/kb/2987015).

## Mises à jour de l’appareil virtuel dans la version d’octobre

Cette version ne contient aucune mise à jour pour l’appareil virtuel. L’installation de cette mise à jour ne modifie pas la version logicielle de l’appareil virtuel.
 

<!---HONumber=AcomDC_0921_2016-->