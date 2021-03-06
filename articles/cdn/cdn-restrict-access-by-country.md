<properties
	pageTitle="Restriction de l’accès à votre contenu par pays | Microsoft Azure"
	description="Découvrez comment limiter l’accès à votre contenu Azure CDN à l’aide de la fonctionnalité de filtrage par pays."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>

#Restriction de l'accès à votre contenu par pays

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Par défaut, lorsqu'un utilisateur demande du contenu, le contenu est pris en charge, quel que soit l’endroit d’où vient la demande. Dans certains cas, vous souhaiterez limiter l'accès à votre contenu par pays. Cette rubrique explique comment utiliser la fonctionnalité de **Filtrage par pays** afin de configurer le service pour autoriser ou bloquer l'accès par pays.

>[AZURE.NOTE] Une fois la configuration définie, elle s’applique à tous les points de terminaison du **CDN Azure fourni par Verizon** dans ce profil CDN Azure.

Pour plus d’informations sur les considérations qui s’appliquent à la configuration de ce type de restriction, consultez la section [Considérations](cdn-restrict-access-by-country.md#considerations) à la fin de la rubrique.

![Filtrage par pays](./media/cdn-filtering/cdn-country-filtering.png)


##Étape 1 : définir le chemin d'accès

Lorsque vous configurez un filtre de pays, vous devez spécifier le chemin d'accès relatif à l'emplacement auquel les accès utilisateurs sont autorisés ou refusés. Vous pouvez appliquer le filtrage de pays pour tous vos fichiers avec « / » ou certains dossiers en spécifiant des chemins d'accès au répertoire.

Exemple de filtre de chemin d'accès au répertoire :

	/                                 
	/Photos/
	/Photos/Strasbourg

##Étape 2 : Définir l'action : autoriser ou bloquer

**Bloquer** : l’accès aux ressources demandées à partir de ce chemin d'accès récursif est bloqué aux utilisateurs des pays spécifiés. Si aucune autre option de filtrage par pays n'a été configurée pour cet emplacement, tous les autres utilisateurs sont autorisés à y accéder.

**Autoriser** : l’accès aux ressources demandées à partir de ce chemin d'accès récursif est autorisé aux seuls utilisateurs des pays spécifiés.

##Étape 3 : définir les pays

Sélectionnez les pays que vous souhaitez bloquer ou autoriser pour le chemin d'accès. Pour plus d’informations, consultez l’article [Azure CDN from Verizon Country Codes](https://msdn.microsoft.com/library/mt761717.aspx) (Codes de pays Azure CDN par Verizon).

Par exemple, la règle de blocage /Photos/Strasbourg/ filtre les fichiers, notamment :

	http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
	http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##Codes de pays

La fonctionnalité de **Filtrage par pays** utilise des codes de pays pour définir les pays à partir desquels une demande est autorisée ou bloquée pour un répertoire sécurisé. Vous trouverez les codes de pays sur la page [Azure CDN from Verizon Country Codes](https://msdn.microsoft.com/library/mt761717.aspx) (Code de pays Azure CDN par Verizon). Si vous spécifiez « EU » (Europe) ou « AP » (Asie/Pacifique), un sous-ensemble des adresses IP issues de n'importe quel pays de ces régions est bloqué ou autorisé.


##<a id="considerations"></a>Considérations

- L’entrée en vigueur des modifications apportées à votre configuration de filtrage par pays peut prendre jusqu’à 90 minutes.
- Cette fonctionnalité ne prend pas en charge les caractères génériques (par exemple, « * »).
- La configuration de filtrage par pays associée avec le chemin d'accès relatif de filtrage est appliquée de manière récursive à ce chemin d’accès.
- Une seule règle peut être appliquée au même chemin d'accès relatif (vous ne pouvez pas créer plusieurs filtres de pays qui pointent vers le même chemin d'accès relatif). Toutefois, un dossier peut avoir plusieurs filtres par pays. Cela est dû à la nature récursive des filtres par pays. En d'autres termes, un filtre par pays différent peut être attribué à un sous-dossier d'un dossier déjà configuré.

<!---HONumber=AcomDC_0803_2016-->