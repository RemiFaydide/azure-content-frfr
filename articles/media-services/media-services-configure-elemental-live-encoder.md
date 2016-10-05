<properties 
	pageTitle="Configurer l’encodeur Elemental Live pour envoyer un flux live à débit binaire unique | Microsoft Azure" 
	description="Cette rubrique explique comment configurer l’encodeur Elemental Live afin d’envoyer un flux à débit binaire unique à des canaux AMS activés pour l’encodage en temps réel." 
	services="media-services" 
	documentationCenter="" 
	authors="cenkdin" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="cenkdin;anilmur;juliako"/>

#Utiliser l’encodeur Elemental Live pour envoyer un flux live à débit binaire unique

> [AZURE.SELECTOR]
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Cette rubrique explique comment configurer l’encodeur [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live) afin d’envoyer un flux à débit binaire unique à des canaux AMS activés pour l’encodage en temps réel. Pour plus d’informations, consultez la rubrique [Utilisation de canaux activés pour effectuer un encodage en temps réel avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Ce didacticiel montre comment gérer Azure Media Services (AMS) avec l’outil Azure Media Services Explorer (AMSE). Cet outil est uniquement compatible avec les PC Windows. Si vous êtes sous Mac ou Linux, utilisez le portail Azure Classic pour créer des [canaux](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) et des [programmes](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

##Composants requis

- Suppose une connaissance pratique de l’utilisation de l’interface web Elemental Live pour créer des événements en direct.
- [Créer un compte Azure Media Services](media-services-create-account.md)
- Assurez-vous qu’il y a un point de terminaison de diffusion en continu en cours d’exécution avec au moins une unité de diffusion en continu allouée. Pour plus d’informations, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-portal-manage-streaming-endpoints.md).
- Installez la dernière version de l’outil [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer).
- Lancez l’outil et connectez-vous à votre compte AMS.

##Conseils

- Si possible, utilisez une connexion Internet câblée.
- Une bonne règle pour déterminer les besoins en bande passante consiste à doubler les débits binaires de diffusion en continu. Bien qu’il ne s’agisse pas d’une obligation, cela permet de réduire l’impact de l’encombrement du réseau.
- Lors de l’utilisation d’encodeurs logiciels, fermez tous les programmes inutiles.

## Elemental Live avec réception RTP

Cette section explique comment configurer l’encodeur Elemental Live qui envoie un flux live à débit binaire unique via RTP. Pour plus d’informations, consultez la rubrique [Flux MPEG-TS via RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### Créer un canal

1.  Dans l’outil AMSE, accédez à l’onglet **Live**, puis cliquez avec le bouton droit dans la zone des canaux. Dans le menu qui s’affiche, sélectionnez **Créer un canal...**.

![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Spécifiez un nom de canal (le champ Description est facultatif). Sous Paramètres du canal, sélectionnez **Standard** pour l’option Live Encoding, avec le protocole d’entrée défini sur **RTP (MPEG-TS)**. Vous pouvez laisser tous les autres paramètres inchangés.


Vérifiez que l’option **Démarrer maintenant le nouveau canal** est sélectionnée.

3. Cliquez sur **Créer un canal**. ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE] Le démarrage du canal peut prendre jusqu’à 20 minutes.

Pendant le démarrage du canal, vous pouvez [configurer l’encodeur](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

>[AZURE.IMPORTANT] Notez que la facturation commence dès que l’état du canal indique qu’il est prêt à être utilisé. Pour plus d’informations, consultez [États du canal](media-services-manage-live-encoder-enabled-channels.md#states).

###<a id=configure_elemental_rtp></a>Configurer l’encodeur Elemental Live 

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


####Configuration

1. Accédez à l’interface web **Elemental Live** et configurez l’encodeur pour la diffusion en continu **UDP/TS**.

2. Une fois qu’un nouvel événement a été créé, faites défiler l’écran jusqu’aux groupes de sortie et ajoutez le groupe de sortie **UDP/TS**.

3. Créez une nouvelle sortie en sélectionnant **New Stream**, puis en cliquant sur **Add Output**.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
	
	>[AZURE.NOTE] Il est recommandé que le code horaire de l’événement Elemental soit défini sur Horloge système pour aider l’encodeur à se reconnecter en cas d’échec d’un flux.

4. Maintenant que la sortie a été créée, cliquez sur **Add Stream**. Vous pouvez à présent configurer les paramètres de sortie.
5. Faites défiler l’écran jusqu’au flux « Stream 1 » qui vient d’être créé, cliquez sur l’onglet **Video** à gauche et développez la section de paramètres **Advanced**.

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

	Même si Elemental Live offre un large éventail de possibilités de personnalisation, les paramètres suivants sont recommandés pour commencer la diffusion en continu vers AMS.
	
	- Resolution : 1280 x 720
	- Framerate : 30
	- GOP Size : 60 frames
	- Interlace Mode : Progressive
	- Bitrate : 5000000 bit/s (cette valeur peut être ajustée en fonction des limitations du réseau)
	

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. Obtenez l’URL d’entrée du canal.
	
	Revenez à l’outil AMSE et vérifiez l’état d’achèvement du canal. Une fois que l’état est passé de **Démarrage** à **En cours d’exécution**, vous pouvez obtenir l’URL d’entrée.
	  
	Une fois le canal en cours d’exécution, cliquez avec le bouton droit sur le nom du canal, déplacez le pointeur vers le bas pour le placer sur **Copier l’URL entrée dans le Presse-papiers**, puis sélectionnez **URL d’entrée principale**.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
	
1. Collez ces informations dans le champ **Primary Destination** d’Elemental. Tous les autres paramètres peuvent rester inchangés.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

	Pour bénéficier d’une redondance supplémentaire, répétez ces étapes avec l’URL d’entrée secondaire en créant un onglet « Output » distinct pour la diffusion en continu UDP/TS.
	
7. Cliquez sur **Create** (si un événement a été créé) ou **Update** (en cas de modification d’un événement existant), puis procédez au démarrage de l’encodeur.

>[AZURE.IMPORTANT] Avant de cliquer sur **Start** dans l’interface web Elemental Live, vous **devez** vérifier que le canal est prêt. Veillez également à ne pas laisser le canal à l’état d’exécution sans événement pendant plus de 15 minutes.

Une fois que le flux a été exécuté pendant 30 secondes, revenez à l’outil AMSE et testez la lecture.

###Tester la lecture
  
1. Accédez à l’outil AMSE et cliquez avec le bouton droit sur le canal à tester. Dans le menu, placez le pointeur sur **Lire l’aperçu** et sélectionnez **avec Azure Media Player**.

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Si le flux s’affiche dans le lecteur, cela signifie que l’encodeur a été correctement configuré pour se connecter à AMS.

Si vous recevez une erreur, vous devrez réinitialiser le canal et ajuster les paramètres de l’encodeur. Pour obtenir des instructions détaillées, reportez-vous à la rubrique consacrée à la [résolution des problèmes](media-services-troubleshooting-live-streaming.md).

###Créer un programme

1. Une fois que vous avez vérifié que la lecture fonctionne sur le canal, créez un programme. Sous l’onglet **Live** de l’outil AMSE, cliquez avec le bouton droit dans la zone des programmes et sélectionnez **Créer un programme**.

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

2. Nommez le programme et, si nécessaire, ajustez la **longueur de la fenêtre d’archive** (qui est de 4 heures par défaut). Vous pouvez également spécifier un emplacement de stockage ou conserver la valeur par défaut.
3. Cochez la case **Démarrer le programme maintenant**.
4. Cliquez sur **Créer le programme**.
  
	Remarque : la création d’un programme prend moins de temps que la création d’un canal.
 
5. Une fois le programme en cours d’exécution, vérifiez que la lecture fonctionne. Pour ce faire, cliquez avec le bouton droit sur le programme, placez le pointeur sur **Lire le(s) programme(s)**, puis sélectionnez **avec Azure Media Player**.
6. Après confirmation, cliquez à nouveau avec le bouton droit sur le programme et sélectionnez **Copier l’URL de sortie dans le Presse-papiers** (ou obtenez cette information à l’aide de l’option **Informations et paramètres du programme** du menu).

Le flux est maintenant prêt à être incorporé dans un lecteur ou distribué à une audience pour un affichage en direct.

## Résolution de problèmes

Pour obtenir des instructions détaillées, reportez-vous à la rubrique consacrée à la [résolution des problèmes](media-services-troubleshooting-live-streaming.md).


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->