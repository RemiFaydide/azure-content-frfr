<properties 
	pageTitle="Configurer l’encodeur NewTek TriCaster pour envoyer un flux en direct à débit binaire unique | Microsoft Azure" 
	description="Cette rubrique explique comment configurer l’encodeur en direct TriCaster afin d’envoyer un flux à débit binaire unique à des canaux AMS activés pour l’encodage en temps réel." 
	services="media-services" 
	documentationCenter="" 
	authors="cenkdin" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="juliako;cenkd;anilmur"/>

#Utiliser l’encodeur NewTek TriCaster pour envoyer un flux en direct à débit binaire unique

> [AZURE.SELECTOR]
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Cette rubrique explique comment configurer l’encodeur en direct [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) afin d’envoyer un flux à débit binaire unique à des canaux AMS activés pour l’encodage en temps réel. Pour plus d’informations, consultez [Utilisation de canaux activés pour effectuer un encodage en temps réel avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Ce didacticiel montre comment gérer Azure Media Services (AMS) avec l’outil Azure Media Services Explorer (AMSE). Cet outil est uniquement compatible avec les PC Windows. Si vous êtes sous Mac ou Linux, utilisez le portail Azure Classic pour créer des [canaux](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) et des [programmes](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

>[AZURE.NOTE]Pendant l’utilisation de Tricaster pour l’envoi dans un flux de contribution pour des canaux AMS activés pour l’encodage live, il peut y avoir des problèmes vidéo/audio dans votre événement en direct si vous utilisez certaines fonctionnalités de Tricaster, telles que le découpage rapide entre des flux ou le basculement de/vers les ardoises. L’équipe AMS travaille à la résolution de ces problèmes. En attendant, il est déconseillé d’utiliser ces fonctionnalités.


##Composants requis

- [Créer un compte Azure Media Services](media-services-create-account.md)
- Assurez-vous qu’il y a un point de terminaison de diffusion en continu en cours d’exécution avec au moins une unité de diffusion en continu allouée. Pour plus d’informations, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-portal-manage-streaming-endpoints.md).
- Installez la dernière version de l’outil [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer).
- Lancez l’outil et connectez-vous à votre compte AMS.

##Conseils

- Si possible, utilisez une connexion Internet câblée.
- Une bonne règle pour déterminer les besoins en bande passante consiste à doubler les débits binaires de diffusion en continu. Bien qu’il ne s’agisse pas d’une obligation, cela permet de réduire l’impact de l’encombrement du réseau.
- Lors de l’utilisation d’encodeurs logiciels, fermez tous les programmes inutiles.

## Créer un canal

1.  Dans l’outil AMSE, accédez à l’onglet **Live**, puis cliquez avec le bouton droit dans la zone des canaux. Dans le menu qui s’affiche, sélectionnez **Créer un canal...**.

![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Spécifiez un nom de canal (le champ Description est facultatif). Sous Paramètres du canal, sélectionnez **Standard** pour l’option Live Encoding, avec le protocole d’entrée défini sur **RTPM**. Vous pouvez laisser tous les autres paramètres inchangés.


Vérifiez que l’option **Démarrer maintenant le nouveau canal** est sélectionnée.

3. Cliquez sur **Créer un canal**. ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

>[AZURE.NOTE] Le démarrage du canal peut prendre jusqu’à 20 minutes.


Pendant le démarrage du canal, vous pouvez [configurer l’encodeur](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

>[AZURE.IMPORTANT] Notez que la facturation commence dès que l’état du canal indique qu’il est prêt à être utilisé. Pour plus d’informations, consultez [États du canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_tricaster_rtmp></a>Configurer l’encodeur NewTek TriCaster

Dans ce didacticiel, les paramètres de sortie ci-dessous sont utilisés. Le reste de cette section décrit la procédure de configuration plus en détail.

**Vidéo** :
 
- Codec : H.264
- Profil : Élevé (niveau 4.0)
- Débit binaire : 5 000 kbit/s
- Image clé : 2 secondes (60 secondes)
- Fréquence d’images : 30
 
**Audio** :

- Codec : AAC (LC)
- Débit binaire : 192 kbit/s
- Taux d’échantillonnage : 44,1 kHz


###Configuration

1. Créez un projet **NewTek TriCaster** en fonction de la source d’entrée vidéo utilisée.
2. Une fois dans ce projet, recherchez le bouton **Flux**, puis cliquez sur l’icône en forme d’engrenage pour accéder au menu de configuration du flux de données.

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Une fois le menu ouvert, cliquez sur **Nouveau** sous le titre Connexion. Lorsque vous êtes invité à saisir un type de connexion, sélectionnez **Adobe Flash**.

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)

4. Cliquez sur **OK**.

5. Vous pouvez à présent importer un profil FMLE en cliquant sur la flèche sous le menu déroulant **Profil Streaming** et en accédant à **Parcourir**.

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)

6. Accédez à l’emplacement d’enregistrement de votre profil FMLE configuré.
7. Sélectionnez-le, puis appuyez sur **OK**.

	Lorsque le profil est téléchargé, passez à l’étape suivante.

6. Récupérez l’URL d’entrée du canal pour l’affecter au **Point de terminaison RTMP** Tricaster.
	
	Revenez à l’outil AMSE et vérifiez l’état d’achèvement du canal. Une fois que l’état est passé de **Démarrage** à **En cours d’exécution**, vous pouvez obtenir l’URL d’entrée.
	  
	Une fois le canal en cours d’exécution, cliquez avec le bouton droit sur le nom du canal, déplacez le pointeur vers le bas pour le placer sur **Copier l’URL entrée dans le Presse-papiers**, puis sélectionnez **URL d’entrée principale**.
	
	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)

7. Collez ces informations dans le champ **Emplacement** sous **Serveur Flash** dans le projet Tricaster. Indiquez également un nom de flux dans le champ **ID de flux**.

	Si des informations de flux de données ont été ajoutées au profil FMLE, vous pouvez également les importer dans cette section en cliquant sur **Importer les paramètres**, en accédant au profil FMLE enregistré et en cliquant sur **OK**. Les champs Serveur Flash adéquats doivent être remplis par des informations de FMLE.

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)

9. Lorsque vous avez terminé, cliquez sur **OK** en bas de l’écran. Lorsque les entrées audio et vidéo dans Tricaster sont prêtes, commencez le streaming vers AMS en cliquant sur le bouton **Flux**.

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

>[AZURE.IMPORTANT] Avant de cliquer sur **Flux**, vous **devez** vérifier que le canal est prêt. Veillez également à ne pas laisser le canal à l’état d’exécution sans un flux de contribution d’entrée pendant plus de 15 minutes.

##Tester la lecture
  
1. Accédez à l’outil AMSE et cliquez avec le bouton droit sur le canal à tester. Dans le menu, placez le pointeur sur **Lire l’aperçu** et sélectionnez **avec Azure Media Player**.

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Si le flux s’affiche dans le lecteur, cela signifie que l’encodeur a été correctement configuré pour se connecter à AMS.

Si vous recevez une erreur, vous devrez réinitialiser le canal et ajuster les paramètres de l’encodeur. Pour obtenir des instructions détaillées, reportez-vous à la rubrique consacrée à la [résolution des problèmes](media-services-troubleshooting-live-streaming.md).

##Créer un programme

1. Une fois que vous avez vérifié que la lecture fonctionne sur le canal, créez un programme. Sous l’onglet **Live** de l’outil AMSE, cliquez avec le bouton droit dans la zone des programmes et sélectionnez **Créer un programme**.

	![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)

2. Nommez le programme et, si nécessaire, ajustez la **longueur de la fenêtre d’archive** (qui est de 4 heures par défaut). Vous pouvez également spécifier un emplacement de stockage ou conserver la valeur par défaut.
3. Cochez la case **Démarrer le programme maintenant**.
4. Cliquez sur **Créer le programme**.
  
	Remarque : la création d’un programme prend moins de temps que la création d’un canal.
 
5. Une fois le programme en cours d’exécution, vérifiez que la lecture fonctionne. Pour ce faire, cliquez avec le bouton droit sur le programme, placez le pointeur sur **Lire le(s) programme(s)**, puis sélectionnez **avec Azure Media Player**.
6. Après confirmation, cliquez à nouveau avec le bouton droit sur le programme et sélectionnez **Copier l’URL de sortie dans le Presse-papiers** (ou obtenez cette information à l’aide de l’option **Informations et paramètres du programme** du menu).

Le flux est maintenant prêt à être incorporé dans un lecteur ou distribué à une audience pour un affichage en direct.


## Résolution de problèmes

Pour obtenir des instructions détaillées, reportez-vous à la rubrique consacrée à la [résolution des problèmes](media-services-troubleshooting-live-streaming.md).


##Étape suivante

Consultez les parcours d’apprentissage de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->