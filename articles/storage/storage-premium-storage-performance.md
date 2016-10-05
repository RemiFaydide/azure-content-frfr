<properties
    pageTitle="Azure Premium Storage : conception sous le signe de la haute performance | Microsoft Azure"
    description="Concevoir des applications hautes performances avec Azure Premium Storage. Premium Storage offre une prise en charge très performante et à faible latence des disques pour les charges de travail utilisant beaucoup d'E/S exécutées sur les machines virtuelles Azure."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager=""
	editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="aungoo-msft"/>

# Azure Premium Storage : conception sous le signe de la haute performance

## Vue d'ensemble  
Cet article fournit des instructions pour la création d’applications hautes performances avec Azure Premium Storage. Vous pouvez utiliser les instructions fournies dans ce document parallèlement aux bonnes pratiques de performances applicables aux technologies utilisées par votre application. Pour illustrer les instructions, nous avons utilisé comme exemple un SQL Server exécuté sur Premium Storage.

Bien que cet article couvre plusieurs scénarios de performances au niveau de la couche de stockage, vous devrez optimiser la couche applicative. Par exemple, si vous hébergez une batterie de serveurs SharePoint sur Azure Premium Storage, vous pouvez utiliser les exemples SQL Server de cet article pour optimiser le serveur de base de données. Vous devrez également optimiser le serveur Web et le serveur d’applications de la batterie de serveurs SharePoint pour obtenir de meilleures performances.

Cet article vous aidera à répondre à certaines questions courantes relatives à l’optimisation des performances applicatives sur Azure Premium Storage :

-   Comment mesurer les performances de votre application ?
-   Pourquoi n’obtenez-vous pas les hautes performances attendues ?
-   Quels sont les facteurs qui influencent les performances de votre application sur Premium Storage ?
-   Comment ces facteurs influencent-ils les performances de votre application sur Premium Storage ?
-   Comment optimiser les performances d’E/S par seconde, de bande passante et de latence ?

Ces instructions vous sont spécifiquement fournies pour Premium Storage, car les charges de travail exécutées sur Premium Storage sont extrêmement sensibles aux performances. Nous vous proposons des exemples lorsque cela s’y prête. Vous pouvez également appliquer certaines de ces instructions aux applications qui s’exécutent sur des machines virtuelles IaaS avec des disques de stockage Standard.

Avant de commencer, si vous ne connaissez pas Premium Storage, lisez tout d’abord l’article [Premium Storage : stockage haute performance pour les charges de travail Azure Virtual Machines](storage-premium-storage.md) et la publication [Objectifs de performance et évolutivité d’Azure Premium Storage](storage-scalability-targets.md#premium-storage-accounts).

## Indicateurs de performances d’une application  
Pour évaluer le degré de performances d’une application, nous nous appuyons sur divers indicateurs de performances, par exemple la vitesse à laquelle une application traite une demande utilisateur, la quantité de données qu’une application est capable de traiter pour chaque demande, le nombre de requêtes traitées par une application dans un intervalle donné, ou encore le temps d’attente que doit subir l’utilisateur avant d’obtenir une réponse à sa demande. Sur le plan technique, ces indicateurs de performances s’expriment en termes d’E/S par seconde, de débit (ou bande passante) et de latence.

Dans cette section, nous allons aborder les indicateurs de performances courants dans le contexte de Premium Storage. Dans la section suivante, intitulée « Collecte des exigences de performances de l’application », vous apprendrez à mesurer ces indicateurs de performances pour votre propre application. Dans la section consacrée à l’optimisation des performances applicatives, vous découvrirez les facteurs qui affectent ces indicateurs de performances et obtiendrez des recommandations afin de les optimiser.

## E/S par seconde  
Le nombre d’E/S par seconde représente le nombre de demandes que votre application envoie aux disques de stockage en une seconde. Une opération d’entrée/sortie peut être accessible en lecture ou écriture, et être séquentielle ou aléatoire. Les applications OLTP (un site web de vente en ligne, par exemple) doivent traiter immédiatement de nombreuses requêtes d’utilisateurs simultanées. Les requêtes utilisateurs représentent des transactions d’insertion et de mise à jour qui pèsent lourdement sur la base de données, et que l’application doit traiter rapidement. Les applications OLTP requièrent dont un nombre d’E/S par seconde très élevé. Ces applications gèrent des millions de demandes d’E/S petites et aléatoires. Si vous possédez une telle application, vous devez concevoir votre infrastructure applicative de manière à optimiser les E/S. Dans la section *Optimisation des performances applicatives*, nous aborderons en détail tous les facteurs que vous devez prendre en compte pour obtenir un nombre d’E/S par seconde élevé.

Lorsque vous attachez un disque de stockage Premium à votre machine virtuelle à grande échelle, Azure met à disposition un nombre garanti d’E/S par seconde, conformément à la spécification de disque. Par exemple, un disque P30 configure 5 000 E/S par seconde. Chaque taille de machine virtuelle à grande échelle est également associée à une limite spécifique d’E/S par seconde qu’elle peut prendre en charge. Par exemple, une machine virtuelle GS5 Standard a une limite de 80 000 E/S par seconde.

## Débit  
Le débit ou la bande passante est la quantité de données que votre application envoie aux disques de stockage dans un intervalle spécifié. Si votre application effectue des opérations d’entrée/sortie avec des tailles d’unité d’E/S importantes, elle aura besoin d’un débit élevé. Les applications d’entrepôt de données ont tendance à émettre des opérations d’analyse intensives qui accèdent simultanément à de grandes quantités de données et exécutent généralement des opérations en bloc. En d’autres termes, ces applications nécessitent un débit plus élevé. Si vous possédez une telle application, vous devez concevoir votre infrastructure de manière à en optimiser le débit. Dans la section suivante, nous décrirons en détail les facteurs que vous devrez ajuster pour y parvenir.

Lorsque vous attachez un disque de stockage premium à une machine virtuelle à grande échelle, Azure met à disposition un débit conforme à la spécification de ce disque. Par exemple, un disque P30 configure un débit de disque de 200 Mo par seconde. Chaque taille de machine virtuelle à grande échelle est également associée à une limite spécifique de débit qu’elle peut prendre en charge. Par exemple, une machine virtuelle GS5 Standard a un débit maximal de 2 000 Mo par seconde.

La formule ci-dessous illustre la relation entre le débit et le nombre d’E/S par seconde.

![](media/storage-premium-storage-performance/image1.png)

Par conséquent, il est important de déterminer les valeurs optimales de débit et d’E/S par seconde dont a besoin votre application. Lorsque vous essayez d’optimiser une de ces valeurs, l’autre est également affectée. Dans la section *Optimisation des performances applicatives*, nous aborderons plus en détail la question de l’optimisation des E/S par seconde et du débit.

## Latence  
La latence est le temps nécessaire à une application pour recevoir une demande unique, l’envoyer aux disques de stockage et transmettre la réponse au client. Il s’agit d’une mesure critique des performances d’une application, qui s’ajoute à celle des E/S par seconde et du débit. La latence d’un disque de stockage premium correspond au temps nécessaire pour récupérer les informations d’une demande et les retourner à votre application. Premium Storage offre de faibles latences. Si vous activez une mise en cache de l’hôte en lecture seule sur des disques de stockage premium, vous pourrez obtenir une latence de lecture bien plus faible. Nous aborderons la mise en cache du disque plus en détail dans la section *Optimisation des performances applicatives*.

Lorsque vous optimisez votre application pour augmenter le nombre d’E/S par seconde et le débit, la latence de votre application s’en trouve affectée. Après avoir ajusté les performances de votre application, pensez toujours à évaluer la latence de l’application afin d’éviter un comportement de latence élevée inattendu.

## Collecte des exigences de performances de l’application  
La première étape de la conception d’applications hautes performances exécutées sur Azure Premium Storage consiste à comprendre les exigences de performances de votre application. Après avoir recueilli ces exigences de performances, vous pourrez optimiser votre application de manière à obtenir les meilleures performances possibles.

Dans la section précédente, nous avons expliqué les indicateurs de performances courants, à savoir le nombre d’E/S par seconde, le débit et la latence. Vous devez déterminer, parmi ces indicateurs de performances, lesquels sont essentiels à votre application pour fournir l’expérience utilisateur recherchée. Par exemple, un nombre d’E/S par seconde élevé est plus important pour les applications OLTP qui traitent des millions de transactions en une seconde. En revanche, un débit élevé sera plus pertinent pour les applications d’entrepôt de données qui traitent de grandes quantités de données en une seconde. De même, une très faible latence sera cruciale pour les applications en temps réel, telles que les sites web de diffusion en continu de vidéo en direct.

Ensuite, vous devrez mesurer les exigences de performances maximales de votre application tout au long de sa durée de vie. Utilisez l’exemple de liste de contrôle ci-dessous comme point de départ. Notez les exigences de performances maximales relevées au cours des périodes de charge de travail normales, des périodes de pointe et des heures creuses. En identifiant les conditions requises pour tous les niveaux de charges de travail, vous serez en mesure de déterminer les exigences de performances globales de votre application. Par exemple, la charge de travail normale d’un site web de commerce électronique correspond aux transactions habituellement traitées au quotidien au cours d’une année. Les pics de charge du site web correspondront aux transactions traitées pendant la période de Noël ou les périodes de soldes. Les pics de charge ne durent généralement que pendant un temps limité, mais ils peuvent vous obliger à mettre à l’échelle votre application en multipliant au moins par deux sa capacité par rapport à son fonctionnement normal. Déterminez les besoins au 50e percentile, au 90e percentile et au 99e percentile. Cela vous permettra d’éliminer les observations aberrantes en termes d’exigences de performances et de concentrer vos efforts sur l’optimisation des valeurs adéquates.

**Liste de contrôle pour les exigences de performances de l’application**

| **Exigences de performances** | **50e percentile** | **90e percentile** | **99e percentile** |
|---|---|---|---|
| Bande passante Transactions par seconde | | | |
| % d’opérations de lecture | | | |
| % d’opérations d’écriture | | | |
| % d’opérations aléatoires | | | |
| % d’opérations séquentielles | | | |
| Taille de la demande d’E/S | | | |
| Débit moyen | | | |
| Bande passante Débit | | | |
| min. Latence | | | |
| Latence moyenne | | | |
| Bande passante UC | | | |
| Utilisation moyenne de l’UC | | | |
| Bande passante Mémoire | | | |
| Utilisation moyenne de la mémoire | | | |
| Profondeur de file d’attente | | | |

>**Remarque importante :** vous devez anticiper la mise à l’échelle de ces nombres en fonction de la croissance prévue de votre application. Il est judicieux de planifier la croissance, car il pourrait être plus difficile de modifier l’infrastructure ultérieurement afin d’en améliorer les performances.

Si vous possédez une application existante et que vous souhaitez migrer vers Premium Storage, commencez par compléter la liste de contrôle ci-dessus pour l’application existante. Développez ensuite un prototype de votre application sur Premium Storage et concevez l’application selon les instructions décrites dans la section *Optimisation des performances applicatives* dans la suite de ce document. La section suivante décrit les outils que vous pouvez utiliser pour collecter les mesures de performances.

Créez une liste de contrôle semblable à votre application prototype existante. À l’aide des outils de benchmarking, vous pouvez simuler les charges de travail et mesurer les performances de l’application prototype. Consultez la section [Benchmarking](#benchmarking) pour en savoir plus. En procédant ainsi, vous pouvez déterminer si Premium Storage peut satisfaire ou dépasser les exigences de performances de votre application. Vous pouvez ensuite implémenter les mêmes règles pour votre application de production.

### Compteurs de mesure des exigences de performances de l’application  
La meilleure façon de mesurer les exigences de performances de votre application consiste à utiliser les outils d’analyse des performances fournies par le système d’exploitation du serveur. Vous pouvez utiliser PerfMon pour Windows et iostat pour Linux. Ces outils capturent des compteurs correspondant à chaque mesure expliquée dans la section ci-dessus. Vous devez capturer les valeurs de ces compteurs lorsque votre application exécute ses charges de travail normales, de pointe et d’heures creuses.

Les compteurs PerfMon sont disponibles pour le processeur, pour la mémoire et pour chaque disque logique et chaque disque physique de votre serveur. Lorsque vous utilisez les disques de stockage premium avec une machine virtuelle, les compteurs de disque physique s’appliquent à chaque disque de stockage premium et les compteurs de disque logique à chaque volume créé sur les disques de stockage premium. Vous devez capturer les valeurs des disques qui hébergent la charge de travail de votre application. S’il existe un mappage un-à-un entre les disques physiques et logiques, vous pouvez consulter les compteurs de disque physique ; sinon, référez-vous aux compteurs de disque logique. Sous Linux, la commande iostat génère un rapport d’utilisation du processeur et du disque. Le rapport d’utilisation du disque fournit des statistiques par unité physique ou par partition. Si vous avez un serveur de base de données dont les données et les journaux résident sur des disques distincts, récupérez ces données pour les deux disques. Le tableau ci-dessous décrit les compteurs de disques, de processeur et de mémoire :

| Compteur | Description | PerfMon | Iostat |
|---|---|---|---|
| **Nombre d’E/S par seconde ou de transactions par seconde** | Nombre de demandes d’E/S émises sur le disque de stockage par seconde. | Nb d’opérations de lectures de disque/s <br> Nb d’opération d’écriture de disque/s | tps <br> r/s <br> w/s |
| **Opérations de lecture et d’écriture de disque** | % d’opérations de lecture et d’écriture exécutées sur le disque. | % temps de lecture du disque <br> % temps d’écriture du disque | r/s <br> w/s |
| **Débit** | Quantité de données lues ou écrites sur le disque par seconde. | Nb d’octets de lecture de disque/s <br> Nb d’octets d’écriture de disque/s | kB\_read/s <br> kB\_wrtn/s |
| **Latence** | Durée totale d’exécution d’une demande d’E/S sur le disque. | Temps de lecture moyen du disque/s <br> Temps d’écriture moyen du disque/s | await <br> svctm |
| **Taille d’E/S** | La taille des demandes d’E/S émises sur les disques de stockage. | Nb moyen d’octets en lecture du disque <br> Nb moyen d’octets en écriture du disque | avgrq-sz |
| **Profondeur de file d’attente** | Nombre de demandes d’E/S en attente de lecture ou d’écriture sur le disque de stockage. | Longueur de file d’attente actuelle du disque | avgqu-sz |
| **IOPS Mémoire** | Quantité de mémoire nécessaire pour une exécution fluide de l’application | % d’octets dédiés utilisés | Use vmstat |
| **Bande passante UC** | Quantité d’UC nécessaire pour une application fluide de l’application | % temps processeur | %util |

En savoir plus sur [iostat](http://linuxcommand.org/man_pages/iostat1.html) et [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).


## Optimisation des performances applicatives  
La nature des demandes d’E/S, la taille de machine virtuelle, la taille de disque, le nombre de disques, la mise en cache du disque, le traitement multithread et la profondeur de file d’attente représentent les principaux facteurs qui influencent les performances d’une application exécutée sur Premium Storage. Vous pouvez contrôler certains de ces facteurs avec les dispositifs fournis par le système. La plupart des applications ne vous permettront peut-être pas de modifier directement la taille d’E/S et la profondeur de file d’attente. Par exemple, si vous utilisez SQL Server, vous ne pouvez choisir ni la taille d’E/S ni la profondeur de file d’attente. SQL Server choisit les valeurs optimales de taille d’E/S et de profondeur de file d’attente profondeur de manière à obtenir les meilleures performances. Il est important de comprendre les effets de ces deux types de facteurs sur les performances de votre application, afin que vous puissiez configurer les ressources appropriées pour répondre à vos besoins de performances.

Dans cette section, reportez-vous à la liste de contrôle des exigences de performances applicatives que vous avez créée pour identifier le degré nécessaire d’optimisation des performances de votre application. Sur cette base, vous serez en mesure de déterminer les facteurs de cette section que vous devrez paramétrer. Pour évaluer les effets de chaque facteur sur les performances de votre application, exécutez les outils de benchmarking sur l’installation de votre application. Reportez-vous à la section [Benchmarking](#Benchmarking) à la fin de cet article pour connaître les étapes à suivre pour exécuter les outils d’évaluation courants sur les machines virtuelles Windows et Linux.

### Aperçu de l’optimisation des E/S, du débit et de la latence  
Le tableau ci-dessous résume les facteurs de performances et les étapes nécessaires à l’optimisation des E/S, du débit et de la latence. Les sections qui suivent ce résumé décrivent en détail chaque facteur.

| | **D’OPÉRATIONS D’E/S PAR SECONDE** | **Débit** | **Latence** |
|---|---|---|---|
| **Exemple de scénario** | Application OLTP d’entreprise nécessitant un taux très élevé de transactions par seconde. | Application d’entreposage de données d’entreprise traitant de grandes quantités de données. | Applications en temps quasi-réel nécessitant une réponse instantanée aux demandes utilisateur, telles que les jeux en ligne. |
| Facteurs de performances | | | |
| **Taille d’E/S** | Une plus petite taille d’E/S génère un nombre d’E/S par seconde plus élevé. | Une plus grande taille d’E/S génère un débit plus élevé. | |
| **Taille de la machine virtuelle** | Utilisez une taille de machine virtuelle qui offre un nombre d’E/S par seconde supérieur aux besoins de votre application. Voir ici les tailles de machine virtuelle et les limites d’E/S par seconde qui leur sont associées. | Utilisez une taille de machine virtuelle qui offre une limite de débit supérieure aux besoins de votre application. Voir ici les tailles de machine virtuelle et les limites de débit qui leur sont associées. | Utilisez une taille de machine virtuelle qui offre une limite de mise à l’échelle supérieure aux besoins de votre application. Voir ici les tailles de machine virtuelle et les limites qui leur sont associées. |
| **Taille du disque** | Utilisez une taille de disque qui offre un nombre d’E/S par seconde supérieur aux besoins de votre application. Voir ici les tailles de disque et les limites d’E/S par seconde qui leur sont associées. | Utilisez une taille de disque qui offre une limite de débit supérieure aux besoins de votre application. Vous trouverez ici les tailles de disque et les limites de débit qui leur sont associées. | Utilisez une taille de disque qui offre une limite de mise à l’échelle supérieure aux besoins de votre application. Vous trouverez ici les tailles de disque et les limites qui leur sont associées. |
| **Limites de mises à l’échelle des machines virtuelles et des disques** | La limite d’E/S par seconde de la taille de machine virtuelle choisie doit être supérieure au nombre total d’E/S par seconde générées par les disques de stockage premium qui lui sont associés. | La limite de débit par seconde de la taille de machine virtuelle choisie doit être supérieure au débit total généré par les disques de stockage premium qui lui sont associés. | Les limites de mise à l’échelle de la taille de machine virtuelle choisie doivent être supérieures aux limites totales de mise à l’échelle des disques de stockage premium qui lui sont associés. |
| **Mise en cache du disque** | Activez le cache en lecture seule sur les disques de stockage premium avec des opérations de lecture intensives pour obtenir un taux d’E/S en lecture par seconde plus élevé. | | Activez le cache en lecture seule sur les disques de stockage premium avec des opérations de lecture intensives pour obtenir de très faibles latences en lecture. |
| **Entrelacement de disques** | Utilisez plusieurs disques et entrelacez-les pour augmenter la limite combinée de débit et d’E/S par seconde. Notez que la limite combinée par machine virtuelle doit être supérieure aux limites combinées des disques premium associés. | |
| **Taille de l’entrelacement** | Taille d’entrelacement plus petite pour les petits schémas d’E/S aléatoires associés aux applications OLTP. Utilisez par exemple une taille d’entrelacement de 64 Ko pour les applications OLTP sous SQL Server. | Plus grande taille d’entrelacement pour les grands schémas d’E/S séquentiels associés aux applications d’entrepôt de données. Utilisez par exemple une taille d’entrelacement de 256 Ko pour les applications d’entrepôt de données sous SQL Server. | |
| **Traitement multithread** | Utilisez le traitement multithread pour envoyer un plus grand nombre de demandes à Premium Storage et augmenter ainsi le débit et le nombre d’E/S par seconde. Sur SQL Server, par exemple, définissez une valeur MAXDOP élevée pour allouer plus de ressources processeur à SQL Server. | |
| **Profondeur de file d’attente** | Une plus grande profondeur de file d’attente génère un nombre plus élevé d’E/S par seconde. | Une plus grande profondeur de file d’attente génère un débit plus élevé. | Une plus petite profondeur de file d’attente réduit les latences. |

## Nature des demandes d’E/S  
Une demande d’E/S représente une unité d’opération d’entrée/sortie exécutée par votre application. Identifier la nature des demandes d’E/S (aléatoires ou séquentielles, en lecture ou en écriture, petites ou grandes) vous permet de déterminer les exigences de performances de votre application. Il est très important de comprendre la nature des demandes d’E/S afin de prendre les bonnes décisions lors de la conception de votre infrastructure d’applications.

La taille des E/S compte parmi les facteurs les plus importants. La taille des E/S correspond à la taille de la demande d’opération d’entrée/sortie générée par votre application. La taille des E/S a un impact significatif sur les performances, en particulier sur le nombre d’E/S par seconde et sur la bande passante, que l’application sera en mesure de délivrer. La formule suivante montre la relation entre le nombre d’E/S par seconde, la taille des E/S et la bande passante ou le débit. ![](media/storage-premium-storage-performance/image1.png)

Certaines applications vous permettent de modifier leur taille d’E/S, contrairement à d’autres. Par exemple, SQL Server détermine lui-même la taille d’E/S optimale et ne fournit aux utilisateurs aucun dispositif permettant de la modifier. De son côté, Oracle propose un paramètre appelé [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) que vous pouvez utiliser pour configurer la taille de la demande d’E/S de la base de données.

Si vous utilisez une application qui ne permet pas de modifier la taille d’E/S, suivez les indications de cet article pour optimiser les indicateurs de performances qui correspondent le mieux à votre application. Par exemple,

-   Une application OLTP génère des millions de demandes d’E/S petites et aléatoires. Pour gérer ces types de demandes d’E/S, vous devez concevoir votre infrastructure d’applications de manière à augmenter le nombre d’E/S par seconde.
-   Une application d’entrepôt de données génère d’importantes demandes d’E/S séquentielles. Pour gérer ces types de demandes d’E/S, vous devez concevoir votre infrastructure d’applications de manière à augmenter la bande passante ou le débit.

Si vous utilisez une application qui vous permet de modifier la taille d’E/S, utilisez cette règle générale tout en respectant les autres instructions de performances.

-   Une plus petite taille d’E/S pour générer un nombre d’E/S par seconde plus élevé. Par exemple, 8 Ko pour une application OLTP.
-   Une plus grande taille d’E/S pour augmenter la bande passante ou le débit. Par exemple, 1 024 Ko pour une application d’entrepôt de données.

Voici un exemple de calcul du nombre d’E/S par seconde et du débit ou de la bande passante pour votre application. Supposons une application utilisant un disque P30. Un disque P30 peut atteindre au maximum 5 000 E/S par seconde et 200 Mo de débit/bande passante par seconde. À présent, si votre application a besoin d’utiliser le nombre maximal d’E/S par seconde du disque P30 alors que vous utilisez une taille d’E/S inférieure à 8 Ko, vous devriez obtenir 40 Mo de bande passante par seconde. En revanche, si votre application requiert la valeur maximale de débit et de bande passante à partir du disque P30 et que vous utilisez une taille d’E/S supérieure à 1 024 Ko, vous obtiendrez seulement 200 E/S par seconde. Vous devez donc ajuster la taille d’E/S pour qu’elle réponde aux besoins de votre application tant en terme d’E/S que de débit et de bande passante. Le tableau ci-dessous récapitule les différentes tailles d’E/S en indiquant le nombre d’E/S par seconde et le débit correspondant dans le cas d’un disque P30.

| **Spécification de l’application** | **Taille des E/S** | **D’OPÉRATIONS D’E/S PAR SECONDE** | **Débit/bande passante** |
|-----------------------------|--------------|----------|--------------------------|
| Nb max. d’E/S par seconde | 8 Ko | 5 000 | 40 Mo par seconde |
| Débit max. | 1 024 Ko | 200 | 200 Mo par seconde |
| Débit max + nb élevé d’E/S par seconde | 64 Ko | 3 200 | 200 Mo par seconde |
| Nb max d’E/S par seconde + débit élevé | 32 Ko | 5 000 | 160 Mo par seconde |

Pour obtenir une valeur d’E/S par seconde et de bande passante supérieure à la valeur maximale d’un seul disque de stockage premium, utilisez plusieurs disques premium entrelacés ensemble. Par exemple, entrelacez deux disques P30 pour obtenir un total de 10 000 E/S par seconde ou un débit combiné de 400 Mo par seconde. Comme nous l’expliquons dans la section suivante, vous devez utiliser une taille de machine virtuelle qui prend en charge la valeur combinée d’E/S par seconde et de débit du disque.

>**Remarque :** lorsque vous augmentez les E/S par seconde ou le débit, l’autre valeur augmente également ; assurez-vous de ne pas franchir les limites de débit ou d’E/S par seconde du disque ou de la machine virtuelle lorsque vous augmentez l’une de ces valeurs.

Pour évaluer les effets de la taille des E/S sur les performances de l’application, vous pouvez exécuter des outils d’évaluation sur votre machine virtuelle et sur vos disques. Créez plusieurs séries de tests et utilisez une taille d’E/S différente pour chaque exécution afin d’en déterminer l’impact. Reportez-vous à la section [Benchmarking](#Benchmarking) à la fin de cet article pour plus de détails.

## Tailles des machines virtuelles à grande échelle  
Lorsque vous commencez la conception d’une application, l’une des premières choses à faire est de choisir une machine virtuelle qui hébergera votre application. Premium Storage est fourni avec des tailles de machine virtuelle à grande échelle capables d’exécuter des applications qui requièrent une plus grande puissance de calcul et de hautes performances d’E/S du disque local. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et l’utilisation d’un disque SSD comme disque local. Les séries DS, DSv2 et GS sont des exemples de machines virtuelles à grande échelle prenant en charge Premium Storage.

Ces machines virtuelles sont disponibles en différentes tailles, avec un nombre différent de cœurs de processeur, de mémoire, de système d’exploitation et de taille du disque temporaire. Chaque taille de chaque machine virtuelle possède également un nombre maximal de disques de données que vous pouvez attacher à la machine virtuelle. Par conséquent, la taille de machine virtuelle choisie affectera la quantité de traitement, de mémoire et de capacité de stockage disponible pour votre application. Elle affecte également le coût de traitement et de stockage. Vous trouverez ci-dessous les spécifications de la plus grande taille de machine virtuelle d’une série DS, d’une série DSv2 et d’une série GS :

| Taille de la machine virtuelle | Cœurs d’unité centrale | Mémoire | Tailles du disque de la machine virtuelle | Disques de données max. | Taille du cache | E/S par seconde | Limites d’E/S du cache de bande passante |
|---|---|---|---|---|---|---|---|
| Standard\_DS14 | 16 | 112 Go | OS = 1023 Go <br> SSD local = 224 Go | 32 | 576 Go | 50 000 E/S par seconde <br> 512 Mo par seconde | 4 000 E/S par seconde et 33 Mo par seconde |
| Standard\_GS5 | 32 | 448 Go | OS = 1 023 Go <br> SSD local = 896 Go | 64 | 4 224 Go | 80 000 E/S par seconde <br> 2 000 Mo par seconde | 5 000 E/S par seconde et 50 Mo par seconde |

Pour afficher une liste complète de toutes les tailles de machine virtuelle Azure disponibles, consultez les articles [Tailles des machines virtuelles dans Azure (Windows)](../virtual-machines/virtual-machines-windows-sizes.md) ou [Tailles des machines virtuelles dans Azure (Linux)](../virtual-machines/virtual-machines-linux-sizes.md). Choisissez une taille de machine virtuelle capable de s’adapter aux exigences de performances souhaitées de votre application. En outre, prenez en compte les considérations suivantes lors du choix de tailles de machine virtuelle.


*Limites de mise à l’échelle* Les limites d’E/S par seconde par machine virtuelle et par disque sont différentes et indépendantes les unes des autres. Assurez-vous que l’application génère un nombre d’E/S par seconde dans les limites de la machine virtuelle et des disques premium qui lui sont associés. Dans le cas contraire, les performances de l’application seront limitées.

Par exemple, supposons une application exigeant un maximum de 4 000 E/S par seconde. Pour atteindre cette valeur, vous devez configurer un disque P30 sur une machine virtuelle DS1. Le disque P30 peut fournir jusqu’à 5 000 E/S par seconde. En revanche, la machine virtuelle DS1 est limitée à 3 200 E/S par seconde. Par conséquent, les performances de l’application seront contraintes par la limite de la machine virtuelle à 3 200 E/S par seconde, ce qui affectera les performances. Pour éviter cette situation, choisissez une taille de machine virtuelle et de disque qui satisfont toutes deux aux exigences de l’application.

*Coût d’exploitation* Dans de nombreux cas, il est possible que le coût global d’exploitation lié à l’utilisation de Premium Storage soit inférieur à celui associé à l’utilisation d’un stockage Standard.

Imaginez par exemple une application nécessitant 16 000 E/S par seconde. Pour atteindre ces performances, vous aurez besoin d’une machine virtuelle IaaS Azure Standard\_D14, qui peut délivrer 16 000 E/S par seconde maximum en utilisant 32 disques de stockage standard de 1 To. Chaque disque de stockage standard de 1 To peut atteindre un maximum de 500 E/S par seconde. Le coût mensuel estimé de cette machine virtuelle sera de 1 570 $. Le coût mensuel de 32 disques de stockage standard sera de 1 638 $. Le coût total mensuel estimé sera donc de 3 208 $.

Toutefois, si vous aviez hébergé la même application sur Premium Storage, vous auriez besoin d’une plus petite taille de machine virtuelle et de moins de disques de stockage premium, ce qui réduirait le coût global. Une machine virtuelle Standard\_DS13 peut couvrir les 16 000 E/S par seconde requis à l’aide de quatre disques P30. La machine virtuelle DS13 délivre 25 600 E/S par seconde au maximum et chaque disque P30 délivre 5 000 E/S par seconde. Globalement, cette configuration peut atteindre 5 000 x 4 = 20 000 E/S par seconde. Le coût mensuel estimé de cette machine virtuelle sera de 1 003 $. Le coût mensuel de quatre disques de stockage premium P30 sera de 544,34 $. Le coût total mensuel estimé sera donc de 1 544 $.

Le tableau ci-dessous résume la répartition des coûts de ce scénario pour un stockage Standard et Premium.

| | **Standard** | **Premium** |
|---|---|---|
| **Coût de la machine virtuelle par mois** | 1 570,58 $ (Standard\_D14) | 1 003,66 $ (Standard\_DS13) |
| **Coût des disques par mois** | 1 638,40 $ (32 disques de 1 To) | 544,34 $ (4 disques P30) |
| **Coût global par mois** | 3 208,98 $ | 1 544,34 $ |

*Distributions Linux*

Avec Azure Premium Storage, vous obtenez le même niveau de performances pour les machines virtuelles exécutant Windows et Linux. Nous prenons en charge diverses distributions Linux, dont vous trouverez la liste complète [ici](../virtual-machines/virtual-machines-linux-endorsed-distros.md). Il est important de noter que les différentes distributions sont mieux adaptées à différents types de charges de travail. Vous verrez différents niveaux de performances selon la distribution sur laquelle votre charge de travail est exécutée. Testez les distributions Linux avec votre application et choisissez celle qui vous convient le mieux.


Lorsque vous exécutez Linux avec Premium Storage, vérifiez les dernières mises à jour sur les pilotes requis pour garantir de meilleures performances.

## Tailles de disques Premium Storage  
Azure Premium Storage offre actuellement trois tailles de disque. Chaque taille de disque a une limite de mise à l’échelle bien spécifique pour le nombre d’E/S par seconde, la bande passante et le stockage. Choisissez la taille de disque Premium Storage adaptée aux exigences de l’application et à la taille de machine virtuelle à grande échelle. Le tableau ci-dessous répertorie les trois tailles de disque et leurs fonctionnalités.

| **Type de disque** | **P10** | **P20** | **P30** |
|---------------------|-------------------|-------------------|-------------------|
| Taille du disque | 128 Go | 512 Go | 1024 Go (1 To) |
| IOPS par disque | 500 | 2 300 | 5 000 |
| Débit par disque | 100 Mo par seconde | 150 Mo par seconde | 200 Mo par seconde |

Le nombre de disques que vous choisissez dépend de la taille de disque choisie. Vous pouvez utiliser un seul disque P30 ou plusieurs disques P10 pour répondre aux besoins de votre application. Tenez compte des remarques ci-dessous pour faire votre choix.

*Limites de mise à l’échelle (E/S par seconde et débit)* Les limites d’E/S par seconde et de débit de chaque taille de disque Premium sont différentes et indépendantes des limites de mise à l’échelle de la machine virtuelle. Assurez-vous que le nombre total d’E/S par seconde et le débit total des disques respectent bien les limites de mise à l’échelle de la taille de machine virtuelle choisie.

Imaginez par exemple que votre application requiert un débit maximum de 250 Mo/s et que vous utilisez une machine virtuelle DS4 avec un seul disque P30. La machine virtuelle DS4 peut délivrer un débit maximum de 256 Mo/s. Toutefois, un seul disque P30 a une limite de débit de 200 Mo/s. Par conséquent, l’application sera contrainte à 200 Mo/s en raison de la limite de disque. Pour contourner cette limite, configurez plusieurs disques de données sur la machine virtuelle.

>**Remarque :** Les lectures traitées par le cache n’étant pas incluses dans les E/S et le débit de disque, elles ne sont pas soumises aux limites de disque. Le cache possède sa propre limite de débit et d’E/S pour chaque machine virtuelle.
>
>Par exemple, les lectures et écritures sont initialement de 60 Mo/s et 40 Mo/s respectivement. Au fil du temps, le cache augmente et sert de plus en plus de lectures. Vous pouvez alors obtenir un plus grand débit en écriture à partir du disque.

*Nombre de disques* Déterminez le nombre de disques dont vous aurez besoin en évaluant les besoins de l’application. Chaque taille de chaque machine virtuelle est limitée quant au nombre de disques que vous pouvez lui associer. En règle générale, ce nombre est égal à deux fois le nombre de cœurs. Assurez-vous que la taille de machine virtuelle que vous choisissez est capable de prendre en charge le nombre de disques nécessaires.

N’oubliez pas les disques Premium Storage délivrent des performances supérieures à celles des disques de stockage Standard. Par conséquent, si vous migrez votre application d’un stockage Standard à un stockage Premium à partir d’une machine virtuelle IaaS Azure, vous aurez probablement besoin de moins de disques premium pour atteindre des performances identiques ou supérieures pour votre application.

## Mise en cache du disque  
Les machines virtuelles à grande échelle qui exploitent Azure Premium Storage ont une technologie de mise en cache à plusieurs niveaux appelée BlobCache. BlobCache utilise à la fois la RAM de la machine virtuelle et le SSD local pour la mise en cache. Ce cache est disponible pour les disques persistants Premium Storage et pour les disques locaux de la machine virtuelle. Par défaut, ce paramètre de cache est défini en lecture/écriture pour les disques du système d’exploitation et en lecture seule pour les disques de données hébergés sur Premium Storage. Lorsque la mise en cache du disque est activée sur les disques Premium Storage, les machines virtuelles à grande échelle peuvent atteindre des niveaux de performances extrêmement élevés qui dépassent les performances du disque sous-jacent.

>[AZURE.WARNING] La modification du paramètre de cache d’un disque Azure détache et rattache le disque cible. S’il s’agit du disque du système d’exploitation, la machine virtuelle redémarre. Arrêtez toutes les applications et services qui risquent d’être affectés par cette indisponibilité avant de modifier le paramètre de cache du disque.

Pour en savoir plus sur le fonctionnement de BlobCache, reportez-vous à l’article du blog interne [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/).

Il est important d’activer le cache sur le jeu de disques approprié. Le fait de devoir activer ou non la mise en cache du disque sur un disque premium dépendra du modèle de charge de travail que ce disque doit gérer. Le tableau ci-dessous répertorie les paramètres de cache par défaut pour les disques du système d’exploitation et les disques de données.

| **Type de disque** | **Paramètre de cache par défaut** |
|---|---|
| Disque de système d’exploitation | Lecture/écriture |
| Disque de données | Aucun |

Voici les paramètres de cache de disque recommandés pour les disques de données :

| **Paramètre de mise en cache du disque** | **Conditions recommandées d’utilisation de ce paramètre** |
|---|---|
| Aucun | Configurer le cache hôte sur cette option pour les disques en lecture seule et les disques gourmands en écriture. |
| Lecture seule | Configurer le cache hôte en lecture seule pour les disques en lecture seule et les disques en lecture/écriture. |
| Lecture/écriture | Configurer le cache hôte en lecture/écriture uniquement si votre application gère correctement l’écriture des données mises en cache sur les disques persistants lorsque cela est nécessaire. |

*Lecture seule* En configurant une mise en cache en lecture seule sur des disques de données Premium Storage, vous pouvez obtenir une faible latence de lecture et obtenir de très hautes performances d’E/S et de débit en lecture pour votre application. Cela est dû à deux raisons :

1.  Les lectures effectuées à partir du cache, qui se trouvent sur la mémoire virtuelle et sur le SSD local, sont beaucoup plus rapides que les lectures effectuées à partir du disque de données, qui réside sur Azure Blob Storage.
2.  Premium Storage ne tient pas compte des lectures traitées à partir du cache pour le calcul du nombre d’E/S par seconde et du débit du disque. Par conséquent, votre application est en mesure de délivrer de meilleures performances totales en termes d’E/S par seconde et de débit.

*Lecture/écriture* Par défaut, le cache en lecture/écriture est activé sur les disques du système d’exploitation. Nous avons récemment ajouté la prise en charge de la mise en cache en lecture/écriture sur les données des disques. Si vous utilisez la mise en cache en lecture/écriture, vous devez disposer d’un moyen approprié d’écrire les données du cache sur des disques persistants. Par exemple, SQL Server gère lui-même l’écriture de données mises en cache sur les disques de stockage persistants. L’utilisation d’un cache en lecture/écriture avec une application qui ne gère pas la persistance des données requises peut entraîner des pertes de données en cas de panne de la machine virtuelle.

Par exemple, vous pouvez appliquer ces instructions à une instance SQL Server exécutée sur Premium Storage de la manière suivante :

1.  Configurer le cache en lecture seule sur les disques de stockage premium qui hébergent des fichiers de données. a. Les lectures rapides du cache réduisent le temps de requête de SQL Server puisque les pages de données sont récupérées à partir du cache bien plus rapidement que lorsque l’opération s’effectue directement à partir des disques de données. b. Le traitement des lectures à partir du cache signifie que les disques de données premium délivrent un débit supplémentaire. SQL Server peut utiliser ce débit supplémentaire pour la récupération d’un plus grand nombre de pages de données et d’autres opérations telles que la sauvegarde/restauration, les charges de traitement par lots et les reconstructions d’index.
2.  Choisir l’option « Aucun » pour le cache sur les disques de stockage premium qui hébergent des fichiers journaux. a. Les fichiers journaux ont principalement des opérations d’écriture intensives. Ils ne bénéficient donc pas du cache en lecture seule.

## Entrelacement de disques  
Lorsqu’une machine virtuelle à grande échelle est connectée à plusieurs disques de stockage premium persistants, les disques peuvent être entrelacés ensemble pour agréger les E/S par seconde, la bande passante et la capacité de stockage.

Sous Windows, vous pouvez utiliser les espaces de stockage pour entrelacer les disques. Vous devez configurer une seule colonne pour chaque disque dans un pool. Dans le cas contraire, les performances globales du volume entrelacé peuvent être limitées, en raison d’une distribution inégale du trafic sur les disques.

Important : avec l’interface utilisateur du Gestionnaire de serveurs, vous pouvez définir un nombre maximal de 8 colonnes au total pour un volume entrelacé. Au-delà de 8 disques, utilisez PowerShell pour créer le volume. PowerShell vous permet de définir un nombre de colonnes égal au nombre de disques. Par exemple, s’il existe 16 disques dans un agrégat unique, spécifiez 16 colonnes dans le paramètre *NumberOfColumns* de l’applet de commande *New-VirtualDisk*.


Sous Linux, utilisez l’utilitaire MDADM pour entrelacer les disques. Pour obtenir des instructions détaillées sur l’entrelacement de disques sous Linux, reportez-vous à [Configuration d’un RAID logiciel sous Linux](../virtual-machines/virtual-machines-linux-configure-raid.md).


*Taille d’entrelacement* La taille d’entrelacement constitue un facteur important dans la configuration de l’entrelacement de disques. La taille d’entrelacement ou la taille de bloc représente la plus petite partie des données que l’application peut traiter sur un volume entrelacé. La taille d’entrelacement que vous configurez varie selon le type d’application et le modèle de demande associé. Si vous choisissez une taille d’entrelacement incorrecte, vous risquez de rencontrer un défaut d’alignement des E/S, ce qui conduirait à une dégradation des performances de votre application.

Par exemple, si une demande d’E/S générée par votre application est supérieure à la taille d’entrelacement de disque, le système de stockage écrira au-delà des limites d’unité de bande sur plusieurs disques. Au moment d’accéder aux données, il devra effectuer une recherche sur plusieurs unités de bande pour exécuter la demande. L’effet cumulatif de ce comportement peut entraîner une dégradation significative des performances. En revanche, si la taille de la demande d’E/S est inférieure à la taille d’entrelacement, et si ces E/S sont aléatoires par nature, les demandes d’E/S peuvent s’ajouter sur le même disque et provoquer un goulot d’étranglement responsable d’une dégradation des performances d’E/S.


Selon le type de charge de travail que votre application exécute, choisissez une taille d’entrelacement appropriée. Pour les petites demandes d’E/S aléatoires, utilisez une plus petite taille d’entrelacement. Pour de grandes demandes d’E/S séquentielles, utilisez en revanche une plus grande taille d’entrelacement. Découvrez les tailles d’entrelacement recommandées pour l’application exécutée sur Premium Storage. Pour SQL Server, configurez une taille d’entrelacement de 64 Ko pour les charges de travail OLTP et de 256 Ko pour les charges de travail d’entrepôt de données. Pour en savoir plus, consultez [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-performance.md#disks-and-performance-considerations)


>**Remarque :** vous pouvez entrelacer au maximum 32 disques de stockage premium sur une machine virtuelle DS et 64 disques de stockage premium sur une machine virtuelle GS.

## Multithreading  
Azure a conçu une plate-forme Premium Storage hautement parallèle. Par conséquent, une application multithread délivre de bien meilleures performances qu’une application à thread unique. Une application multithread répartit ses tâches sur plusieurs threads et augmente l’efficacité de son exécution en utilisant au maximum les ressources de machine virtuelle et de disque.

Par exemple, si votre application s’exécute sur une machine virtuelle monocœur utilisant deux threads, le processeur peut basculer entre les deux threads pour gagner en efficacité. En attendant l’exécution d’un thread sur une E/S de disque, le processeur peut basculer vers l’autre thread. De cette façon, deux threads peuvent accomplir bien plus qu’un seul thread. Si la machine virtuelle possède plusieurs cœurs, elle réduit davantage le temps d’exécution dans la mesure où chaque cœur peut exécuter des tâches en parallèle.

Vous n’êtes peut-être pas en mesure de modifier la façon dont une application du commerce implémente un seul thread ou plusieurs threads. Par exemple, SQL Server est capable de gérer plusieurs processeurs et plusieurs nœuds. SQL Server détermine cependant dans quelles conditions il utilisera un ou plusieurs threads pour traiter une requête. Il peut exécuter des requêtes et créer des index à l’aide du traitement multithread. Dans le cas d’une requête impliquant une fusion de tables volumineuses et un tri des données avant le renvoi des résultats à l’utilisateur, SQL Server utilisera probablement plusieurs threads. Toutefois, un utilisateur ne peut pas décider si SQL Server exécutera une requête à l’aide d’un ou plusieurs threads.

Certains paramètres de configuration peuvent être modifiés pour influencer ce traitement multithread ou parallèle d’une application. Dans le cas de SQL Server, par exemple, il s’agit de la configuration maximale de degré de parallélisme. Ce paramètre appelé MAXDOP permet de configurer le nombre maximal de processeurs que SQL Server peut utiliser lors d’un de traitement parallèle. Vous pouvez configurer MAXDOP pour des requêtes individuelles ou pour des opérations d’index. Cela vous sera utile si vous souhaitez équilibrer les ressources de votre système pour une application stratégique en termes de performances.

Supposons par exemple que votre application basée sur SQL Server exécute simultanément une grande requête et une opération d’index. Imaginons que vous souhaitiez que l’opération d’index soit plus performante que la requête volumineuse. Dans ce cas, vous pouvez spécifier une valeur MAXDOP de l’opération d’index supérieure à la valeur MAXDOP de la requête. SQL Server dispose ainsi d’un plus grand nombre de processeurs exploitables pour l’opération d’index par rapport au nombre de processeurs qu’il peut consacrer à la requête volumineuse. N’oubliez pas que vous n’avez aucun contrôle sur le nombre de threads que SQL Server utilisera pour chaque opération. Vous pouvez contrôler le nombre maximal de processeurs dédiés au traitement multithread.

En savoir plus sur [Degrés de parallélisme](https://technet.microsoft.com/library/ms188611.aspx) dans SQL Server. Découvrez les paramètres qui influencent le traitement multithread dans votre application et leurs configurations pour optimiser les performances.

## Profondeur de file d’attente  
La profondeur, longueur ou taille de file d’attente correspond au nombre de demandes d’E/S en attente dans le système. La valeur de profondeur de file d’attente détermine le nombre d’opérations d’E/S que votre application peut aligner et qui seront traitées par les disques de stockage. Elle affecte les trois indicateurs de performance que nous avons abordés dans cet article, à savoir le nombre d’E/S par seconde, le débit et la latence.

La profondeur de file d’attente et le traitement multithread sont étroitement liés. La valeur de profondeur de file d’attente indique dans quelle mesure l’application peut prendre en charge un traitement multithread. Si la profondeur de file d’attente est importante, l’application peut exécuter davantage d’opérations simultanément, autrement dit un meilleur traitement multithread. Si la profondeur de file d’attente est petite, même si l’application est de type multithread, le nombre de demandes alignées sera insuffisant pour permettre une exécution simultanée.

En règle générale, les applications du commerce ne vous permettent pas de modifier la profondeur de la file d’attente, car un paramétrage incorrect peut faire plus de mal que de bien. Les applications définissent la valeur de la profondeur de file d’attente appropriée pour obtenir des performances optimales. Il est toutefois important de comprendre ce concept afin de pouvoir résoudre les problèmes de performances que vous pouvez rencontrer avec votre application. Vous pouvez également observer les effets de la profondeur de file d’attente en exécutant les outils de benchmarking sur votre système.

Certaines applications fournissent des paramètres qui influencent la profondeur de file d’attente. Le paramètre MAXDOP (degré maximal de parallélisme) dans SQL Server, par exemple, est expliqué dans la section précédente. MAXDOP offre un moyen d’influencer la profondeur de file d’attente et le traitement multithread, sans pour autant modifier directement la valeur de profondeur de file d’attente de SQL Server.

*Grande profondeur de file d’attente* Une grande profondeur de file d’attente permet d’aligner davantage d’opérations sur le disque. Le disque peut anticiper la prochaine demande placée dans sa file d’attente. Il peut ainsi planifier les opérations à l’avance et les traiter dans un ordre optimal. Étant donné que l’application envoie davantage de requêtes sur le disque, ce dernier peut traiter un plus grand nombre d’E/S parallèles. Au final, l’application sera en mesure d’atteindre un taux supérieur d’E/S par seconde. Puisque l’application traite davantage de demandes, le débit total de l’application augmente également.

En général, une application peut atteindre un débit maximal avec 8-16 E/S en attente par disque connecté. Si la profondeur de file d’attente est de un, l’application ne force pas suffisamment d’E/S sur le système et traite moins de données pendant une période donnée. En d’autres termes, elle atteindra un débit inférieur.

Par exemple, dans SQL Server, une valeur MAXDOP de 4 pour une requête signifiera pour SQL Server qu’il peut utiliser jusqu’à quatre cœurs pour exécuter la requête. SQL Server déterminera la meilleure valeur de profondeur de file d’attente ainsi que le nombre de cœurs à utiliser pour l’exécution de la requête.

*Profondeur de file d’attente optimale* Une valeur de profondeur de file d’attente très élevée présente aussi des inconvénients. Si la valeur de profondeur de file d’attente est trop importante, l’application tentera de générer un taux d’E/S par seconde très élevé. À moins que l’application repose sur des disques persistants associés à un nombre suffisant d’E/S par seconde, cela peut affecter sérieusement les latences d’application. La formule suivante montre la relation entre les E/S par seconde, la latence et la profondeur de file d’attente. ![](media/storage-premium-storage-performance/image6.png)

Vous ne devez pas configurer la profondeur de file d’attente à une valeur élevée, mais plutôt à une valeur optimale, qui peut générer suffisamment d’E/S par seconde pour l’application sans affecter les latences. Par exemple, si la latence de l’application doit être de 1 milliseconde, la profondeur de file d’attente requise pour atteindre 5 000 E/S par seconde sera : PF = 5 000 x 0,001 = 5.

*Profondeur de file d’attente pour le volume entrelacé* Pour un volume entrelacé, conservez une profondeur de file d’attente suffisamment élevée de sorte que chaque disque dispose individuellement d’un pic de profondeur de file d’attente. Par exemple, considérez une application qui définit une profondeur de file d’attente de 2 dans un scénario d’entrelacement à 4 disques. Les deux demandes d’E/S seront transmises à deux disques et les deux disques restants seront inactifs. Vous devez par conséquent configurer la profondeur de file d’attente afin que tous les disques puissent être occupés. La formule ci-dessous montre comment déterminer la profondeur de file d’attente des volumes entrelacés. ![](media/storage-premium-storage-performance/image7.png)

## Limitation  
Azure Premium Storage configure la valeur spécifiée d’E/S par seconde et de débit en fonction des tailles de machine virtuelle et des tailles de disque que vous choisissez. Chaque fois que votre application tentera de dépasser les limites de ce que la machine virtuelle ou le disque peut gérer, Premium Storage lui imposera une limitation. Cette limitation se manifeste sous la forme d’une dégradation des performances de votre application, à savoir une latence plus élevée, un débit réduit ou un nombre inférieur d’E/S par seconde. Sans cette limitation, votre application risquerait de planter en demandant plus que ses ressources ne lui permettent d’effectuer. Par conséquent, pour éviter les problèmes de performances associés à une limitation, veillez à toujours fournir suffisamment de ressources pour votre application. Tenez compte des explications données ci-dessus dans les sections relatives aux tailles de disque et aux tailles de machine virtuelle. Le benchmarking offre le meilleur moyen de déterminer les ressources dont vous aurez besoin pour héberger votre application.

## Benchmarking  
Le benchmarking consiste à simuler différentes charges de travail sur votre application et à mesurer les performances de l’application pour chaque charge de travail. En suivant la procédure décrite dans la section précédente, vous avez pu recueillir les exigences de performances de l’application. À présent, vous pouvez utiliser des outils de benchmarking sur les machines virtuelles qui hébergent l’application afin de déterminer les niveaux de performance que votre application peut atteindre avec Premium Storage. Dans cette section, nous allons vous fournir des exemples d’un benchmarking effectué sur une machine virtuelle DS14 Standard configurée avec des disques Azure Premium Storage.

Nous avons utilisé les outils courants Iometer et FIO, pour Windows et Linux respectivement. Ces outils génèrent plusieurs threads qui simulent une charge de travail en production et mesurent les performances du système. L’utilisation de ces outils vous permet également de configurer des paramètres tels que la taille de bloc et la profondeur de file d’attente, que vous ne pouvez normalement pas modifier pour une application. Vous bénéficiez ainsi d’une plus grande souplesse pour optimiser les performances sur une machine virtuelle à grande échelle dotée de disques premium pour différents types de charges de travail applicatives. Pour en savoir plus sur chaque outil de benchmarking, visitez [Iometer](http://www.iometer.org/) et [FIO](http://freecode.com/projects/fio).

Pour suivre les exemples ci-dessous, créez une machine virtuelle DS14 Standard et attachez-y 11 disques Premium Storage. Sur ces 11 disques, configurez 10 disques avec une mise en cache de l’hôte définie sur « Aucun » et entrelacez-les dans un volume appelé NoCacheWrites. Configurez une mise en cache de l’hôte en « Lecture seule » sur le disque restant et créez un volume appelé CacheReads avec ce disque. Avec cette configuration, vous serez en mesure d’observer les performances de lecture et d’écriture maximale à partir d’une machine virtuelle DS14 Standard. Pour connaître les étapes détaillées de la création d’une machine virtuelle DS14 avec des disques premium, accédez à [Créer et utiliser un compte de stockage Premium pour un disque de données de machine virtuelle](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Préchauffage du cache* Le disque dont la mise en cache de l’hôte est définie en lecture seule sera en mesure de générer un taux d’E/S par seconde supérieur à la limite du disque. Pour obtenir ces performances de lecture maximales à partir du cache de l’hôte, vous devez tout d’abord préchauffer le cache du disque. De cette manière, les E/S de lecture que l’outil de benchmarking simulera sur le volume CacheReads pourront effectivement atteindre le cache et non pas le disque directement. Le fait d’intervenir au niveau du cache permet de générer des E/S supplémentaires à partir du seul disque ayant une mise en cache.

>**Important :** Vous devez préchauffer le cache avant d’exécuter l’outil de benchmarking à chaque redémarrage de la machine virtuelle.

#### Iometer   
[Téléchargez l’outil Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) sur la machine virtuelle.

*Fichier de test* Iometer utilise un fichier de test stocké sur le volume sur lequel vous allez exécuter le test de benchmarking. Les lectures et écritures sont activées sur ce fichier de test afin de mesurer le taux d’E/S par seconde et le débit du disque. Iometer crée ce fichier de test si vous n’en avez pas fourni. Créez un fichier de test de 200 Go appelé iobw.tst sur les volumes CacheReads et NoCacheWrites.

*Spécifications d’accès* Les spécifications (taille d’E/S de la demande, % de lecture-écriture, % aléatoire/séquentiel) sont configurées à l’aide de l’onglet « Access Specifications » d’Iometer. Créez une spécification d’accès pour chacun des scénarios ci-dessous. Créez les spécifications d’accès et enregistrez-les avec un nom approprié du type RandomWrites\_8K, RandomReads\_8K. Sélectionnez la spécification correspondante lorsque vous exécutez le scénario de test.

Vous trouverez ci-dessous un exemple de spécifications d’accès pour un scénario d’E/S en écriture maximales. ![](media/storage-premium-storage-performance/image8.png)

*Spécifications de test du taux maximal d’E/S par seconde* Pour démontrer le taux maximal d’E/S par seconde, utilisez une taille de demande plus petite. Utilisez une taille de 8 Ko et créez des spécifications pour les lectures et écritures aléatoires.

| Spécification d’accès | Taille de la demande | % aléatoire | % écriture |
|----------------------|--------------|----------|--------|
| RandomWrites\_8K | 8 Ko | 100 | 0 |
| RandomReads\_8K | 8 Ko | 100 | 100 |

*Spécifications de test du débit maximal* Pour afficher le débit maximal, utilisez une plus grande taille de demande. Utilisez une taille de requête 64 K et créez des spécifications pour les lectures et écritures aléatoires.

| Spécification d’accès | Taille de la demande | % aléatoire | % écriture |
|----------------------|--------------|----------|--------|
| RandomWrites\_64K | 64 Ko | 100 | 0 |
| RandomReads\_64K | 64 Ko | 100 | 100 |

*Exécution du test Iometer* Procédez comme suit pour préparer le cache

1.  Créez deux spécifications d’accès avec les valeurs indiquées ci-dessous

	| Nom | Taille de la demande | % aléatoire | % écriture |
	|-------------------|--------------|----------|--------|
	| RandomWrites\_1MB | 1 Mo | 100 | 0 |
	| RandomReads\_1MB | 1 Mo | 100 | 100 |

2.  Exécutez le test Iometer pour initialiser le disque de cache avec les paramètres suivants. Utilisez trois threads de travail pour le volume cible et une profondeur de file d’attente de 128. Définissez la durée d’exécution du test sur 2 heures sous l’onglet « Test Setup ».

	| Scénario | Volume cible | Nom | Durée |
	|-----------------------|---------------|-------------------|----------|
	| Initialisation du disque de cache | CacheReads | RandomWrites\_1MB | 2 heures |

3.  Exécutez le test Iometer pour préchauffer le disque de cache avec les paramètres suivants. Utilisez trois threads de travail pour le volume cible et une profondeur de file d’attente de 128. Définissez la durée d’exécution du test sur 2 heures sous l’onglet « Test Setup ».

	| Scénario | Volume cible | Nom | Durée |
	|--------------------|---------------|------------------|----------|
	| Préchauffage du disque de cache | CacheReads | RandomReads\_1MB | 2 heures |

Une fois le disque de cache préchauffé, poursuivez avec les scénarios de test décrits ci-dessous. Pour exécuter le test Iometer, utilisez au moins trois threads de travail pour **chaque** volume cible. Pour chaque thread de travail, sélectionnez le volume cible, définissez une profondeur de file d’attente et sélectionnez l’une des spécifications de test enregistrées, comme illustré dans le tableau ci-dessous, pour exécuter le scénario de test correspondant. Le tableau indique également les résultats attendus pour les E/S par seconde et le débit lors de l’exécution de ces tests. Pour tous les scénarios, une petite taille d’E/S de 8 Ko et une profondeur de file d’attente élevée de 128 sont utilisées.

| Scénario de test | Volume cible | Nom | Résultat |
|--------------------|---------------|-------------------|--------------|
| Bande passante E/S par seconde en lecture | CacheReads | RandomWrites\_8K | 50 000 E/S par seconde |
| Bande passante E/S par seconde en écriture | NoCacheWrites | RandomReads\_8K | 64 000 E/S par seconde |
| Bande passante Taux d’E/S par seconde combiné | CacheReads | RandomWrites\_8K | 100 000 E/S par seconde |
| | NoCacheWrites | RandomReads\_8K | |
| Bande passante Mo/s en lecture | CacheReads | RandomWrites\_64K | 524 Mo/s |
| Bande passante Mo/s en écriture | NoCacheWrites | RandomReads\_64K | 524 Mo/s |
| Mo/s combiné | CacheReads | RandomWrites\_64K | 1 000 Mo/s |
| | NoCacheWrites | RandomReads\_64K | |

Voici les captures d’écran des résultats du test Iometer pour les scénarios de taux d’E/S par seconde et de débit combinés.

*Taux d’E/S maximal combiné en lecture et en écriture* ![](media/storage-premium-storage-performance/image9.png)

*Débit maximal combiné en lecture et en écriture* ![](media/storage-premium-storage-performance/image10.png)

### FIO  
FIO est un outil communément utilisé pour tester le stockage sur des machines virtuelles Linux. Cet outil offre la possibilité de sélectionner différentes tailles d’E/S, avec des lectures et des écritures séquentielles ou aléatoires. Il génère des threads de travail ou des processus pour exécuter les opérations d’E/S spécifiées. Vous pouvez spécifier le type d’opérations d’E/S que chaque thread de travail doit exécuter à l’aide de fichiers de travail. Nous avons créé un fichier de travail par scénario, comme illustré dans les exemples ci-dessous. Vous pouvez modifier les spécifications de ces fichiers de travail pour tester différentes charges de travail exécutées sur Premium Storage. Dans ces exemples, nous utilisons une machine virtuelle DS 14 standard exécutée sous **Ubuntu**. Utilisez la configuration décrite au début de la [section Benchmarking](#Benchmarking) et préchauffez le cache avant d’exécuter les tests de benchmarking.

Avant de commencer, [téléchargez FIO](https://github.com/axboe/fio) et installez-le sur votre machine virtuelle.

Exécutez la commande ci-dessous pour Ubuntu.

		apt-get install fio

Nous allons utiliser quatre threads de travail pour générer les opérations d’écriture et quatre threads de travail pour générer les opérations de lecture sur les disques. Les threads de travail dédiés à l’écriture généreront du trafic sur le volume « nocache », qui comporte 10 disques sans mise en cache (paramètre « Aucun »). Les threads de travail dédiés à la lecture généreront du trafic sur le volume « readcache », qui comporte 1 disque avec une mise en cache définie sur le paramètre « Lecture seule ».

*Taux d’E/S par seconde maximal en écriture* Créez le fichier de travail avec les spécifications suivantes pour obtenir le taux maximal d’E/S par seconde en écriture. Nommez ce fichier « fiowrite.ini ».

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Tenez compte des remarques suivantes qui sont en phase avec les instructions de conception présentées dans les sections précédentes. Ces spécifications sont essentielles pour générer le taux d’E/S par seconde maximal.
-   Une profondeur de file d’attente élevée de 256.
-   Une petite taille de bloc de 8 Ko.
-   Plusieurs threads effectuant des écritures aléatoires.

Exécutez la commande suivante pour lancer le test FIO pendant 30 secondes.

	sudo fio --runtime 30 fiowrite.ini

Pendant l’exécution du test, vous serez en mesure de voir le nombre d’E/S par seconde en écriture générées par la machine virtuelle et les disques Premium. Comme indiqué dans l’exemple ci-dessous, la machine virtuelle DS14 produit un taux d’E/S par seconde maximal en écriture limité à 50 000 E/S par seconde. ![](media/storage-premium-storage-performance/image11.png)

*Taux d’E/S par seconde maximal en lecture* Créez le fichier de travail avec les spécifications suivantes pour obtenir le taux maximal d’E/S par seconde en lecture. Nommez ce fichier « fioread.ini ».

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Tenez compte des remarques suivantes qui sont en phase avec les instructions de conception présentées dans les sections précédentes. Ces spécifications sont essentielles pour générer le taux d’E/S par seconde maximal.

-   Une profondeur de file d’attente élevée de 256.
-   Une petite taille de bloc de 8 Ko.
-   Plusieurs threads effectuant des écritures aléatoires.

Exécutez la commande suivante pour lancer le test FIO pendant 30 secondes.

	sudo fio --runtime 30 fioread.ini

Pendant l’exécution du test, vous serez en mesure de voir le nombre d’E/S par seconde en lecture générées par la machine virtuelle et les disques Premium. Comme indiqué dans l’exemple ci-dessous, la machine virtuelle DS14 génère plus de 64 000 E/S par seconde en lecture. Cette valeur représente les performances combinées du disque et du cache. ![](media/storage-premium-storage-performance/image12.png)

*Taux d’E/S par seconde maximal en lecture et en écriture* Créez le fichier de travail avec les spécifications suivantes pour obtenir le taux maximal combiné d’E/S par seconde en lecture et en écriture. Nommez ce fichier « fioreadwrite.ini ».

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Tenez compte des remarques suivantes qui sont en phase avec les instructions de conception présentées dans les sections précédentes. Ces spécifications sont essentielles pour générer le taux d’E/S par seconde maximal.

-   Une profondeur de file d’attente élevée de 128.
-   Une petite taille de bloc de 4 Ko.
-   Plusieurs threads effectuant des opérations d’écriture et de lecture aléatoires.

Exécutez la commande suivante pour lancer le test FIO pendant 30 secondes.

	sudo fio --runtime 30 fioreadwrite.ini

Pendant l’exécution du test, vous serez en mesure de voir le nombre combiné d’E/S par seconde en lecture et en écriture générées par la machine virtuelle et les disques Premium. Comme indiqué dans l’exemple ci-dessous, la machine virtuelle DS14 génère plus de 100 000 E/S par seconde en lecture et en écriture. Cette valeur représente les performances combinées du disque et du cache. ![](media/storage-premium-storage-performance/image13.png)

*Débit maximal combiné* Pour obtenir le débit maximal combiné en lecture et en écriture, utilisez une plus grande taille de bloc et une grande profondeur de file d’attente avec plusieurs threads effectuant des opérations de lecture et d’écriture. Vous pouvez utiliser une taille de bloc de 64 Ko et une profondeur de file d’attente de 128.

## Étapes suivantes  

En savoir plus sur Azure Premium Storage :

- [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage.md)

Pour les utilisateurs de SQL Server, consultez les articles relatifs aux meilleures pratiques de performances de SQL Server :

- [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-performance.md)
- [Azure Premium Storage provides highest performance for SQL Server in Azure VM (en anglais)](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)

<!---HONumber=AcomDC_0921_2016-->