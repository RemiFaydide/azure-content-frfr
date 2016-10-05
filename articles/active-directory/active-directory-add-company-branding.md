<properties
	pageTitle="Ajout d’une marque de société aux pages de connexion et du volet d’accès"
	description="Découvrir comment ajouter une société à la page de connexion Azure et à la page du panneau d’accès"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/22/2016"
	ms.author="curtand"/>

# Ajout d’une marque de société aux pages de connexion et du volet d’accès


Pour éviter toute confusion, de nombreuses entreprises veulent que tous les sites Web et services qu’elles gèrent aient un aspect similaire. Azure Active Directory offre cette possibilité en vous permettant de personnaliser l’apparence des pages Web suivantes pour qu’elles affichent le logo de votre société et sa palette de couleurs personnalisée :

- **Page de connexion** : il s’agit de la page qui s’affiche lorsque vous vous connectez à Office 365 ou à d’autres applications Web qui utilisent Azure AD comme fournisseur d’identité. Vous interagissez avec cette page lors de la découverte d’accueil de domaine ou pour entrer vos informations d’identification. La découverte de domaine d’accueil permet au système de rediriger les utilisateurs fédérés vers leurs STS local (par exemple, AD FS).

- **Page du volet d’accès** : le volet d’accès est un portail Web qui vous permet de consulter et d’exécuter les applications cloud auxquelles votre administrateur Azure AD vous a autorisé à accéder. Pour accéder au volet d’accès, utilisez l’URL suivante : [https://myapps.microsoft.com](https://myapps.microsoft.com).

Cette rubrique explique comment personnaliser la page de connexion et la page de panneau d’accès.

> [AZURE.NOTE]
>
- La promotion de la marque de société est une fonctionnalité disponible uniquement si vous avez effectué une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory, ou si vous êtes un utilisateur Office 365. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
- Les clients vivant en Chine peuvent accéder aux éditions de base et Premium d’Azure Active Directory à l’aide de l’instance mondiale d’Azure Active Directory. Actuellement, les éditions De base et Premium d’Azure Active Directory ne sont pas prises en charge dans le service Microsoft Azure utilisé par 21Vianet en Chine. Pour plus d’informations, contactez-nous via le [Forum Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).



## Personnalisation de la page de connexion

En règle générale, vous utilisez la page de connexion pour accéder à l’aide d’un navigateur à vos applications et services cloud auxquels votre organisation est abonnée.

Lorsque vous apportez des modifications à votre page de connexion, un délai pouvant atteindre une heure peut être nécessaire pour qu’elles apparaissent.

Une page de connexion personnalisée s’affiche uniquement lorsque vous accédez à un service avec une URL spécifique au client, telle que https://outlook.com/**contoso**.com ou https://mail.**contoso**.com.

Lorsque vous accédez à un service avec une URL indépendante du client (par exemple : https://mail.office365.com), une page de connexion non personnalisée s’affiche. Dans ce cas, votre marque apparaît une fois que vous avez entré votre ID d’utilisateur ou que vous avez sélectionné une vignette utilisateur.

> [AZURE.NOTE]
>
- Votre nom de domaine doit apparaître « Actif » dans la section **Active Directory** > **Répertoire** > **Domaines** du portail Azure Classic dans lequel vous avez effectué la personnalisation.
- La personnalisation de la page de connexion ne s’étend pas à la page de connexion client de Microsoft. Si vous vous connectez avec un compte Microsoft personnel, vous pouvez voir une liste personnalisée de vignettes utilisateur générées par Azure AD, mais la marque de votre organisation ne s’applique pas à la page de connexion du compte Microsoft.


Si vous souhaitez afficher la marque, la palette de couleurs de votre société ou tout autre élément personnalisable sur cette page, reportez-vous aux images qui suivent pour comprendre la différence entre les deux.

La capture d’écran suivante montre un exemple de la page de connexion Office 365 sur un ordinateur de bureau **avant** une personnalisation :

![Page de connexion Office 365 avant la personnalisation][1]

La capture d’écran suivante affiche l’exemple de la page de connexion Office 365 sur un ordinateur de bureau **après** une personnalisation :

![Page de connexion d’Office 365 après la personnalisation][2]

La capture d’écran suivante affiche l’exemple de la page de connexion Office 365 sur un appareil mobile **avant** une personnalisation :

![Page de connexion Office 365 avant la personnalisation][3]


La capture d’écran suivante affiche l’exemple de la page de connexion Office 365 sur un appareil mobile **après** une personnalisation :

![Page de connexion d’Office 365 après la personnalisation][4]


Lors du redimensionnement de la fenêtre du navigateur, la grande illustration, comme celle présentée précédemment, est souvent rognée pour s’adapter aux différentes tailles d’écrans. Ainsi, il est conseillé de conserver les éléments visuels clés dans l’illustration pour qu’ils apparaissent toujours dans l’angle supérieur gauche (à droite pour les langues qui se lisent de droite à gauche). Ceci est important, car le redimensionnement s’effectue généralement à partir de l’angle inférieur droit vers l’angle supérieur gauche, ou du bas vers le haut.

L’illustration suivante montre comment l’illustration est rognée lorsque le navigateur est redimensionné à gauche :

![][6]

Voici comment elle apparaît une fois le navigateur redimensionné vers le haut :

![][7]

## Quels sont les éléments de la page que je peux personnaliser ?

Vous pouvez personnaliser les éléments suivants sur la page de connexion :

![][5]

 Élément de la page | Emplacement sur la page
	------------- | -------------
Logo de bannière | Affiché en haut à droite de la page. Remplace le logo du site de destination auquel vous vous connectez (par exemple Office 365 ou Azure).
Grande illustration/couleur d’arrière-plan | Affichée à gauche de la page. Remplace l’image du site de destination auquel vous vous connectez. La couleur d’arrière-plan peut s’afficher à la place de la grande illustration pour les connexions à faible bande passante ou lorsque l’écran est étroit.
Texte de la page de connexion | S’affiche au-dessus du pied de page, lorsque vous devez transmettre des informations avant une connexion avec un compte professionnel ou scolaire. Par exemple, vous pouvez mentionner le numéro de téléphone de votre support technique ou une mention légale.

> [AZURE.NOTE]
Tous ces éléments sont facultatifs. Par exemple, si vous spécifiez un logo de bannière mais aucune grande illustration, la page de connexion affiche votre logo et l’illustration du site de destination (autrement dit, l’image de l’autoroute de Californie d’Office 365).

Vous pouvez également traduire tous les éléments de cette page. Une fois que vous avez configuré un jeu d’éléments de personnalisation « par défaut », vous pouvez configurer des versions supplémentaires avec différents paramètres régionaux. Vous pouvez également combiner différents éléments. Vous pouvez par exemple afficher :

- Créez une grande illustration par défaut adaptée à toutes les cultures, puis créez des versions spécifiques pour l’anglais et le français. Lorsque vous définissez vos navigateurs sur l’une de ces deux langues, l’image spécifique s’affiche, tandis que l’illustration par défaut apparaît pour toutes les autres langues.

- Configurez différents logos pour votre organisation (par exemple, des versions en japonais ou en hébreu).



## Personnalisation de la page du volet d’accès

La page du volet d’accès est essentiellement une page de portail permettant un accès rapide aux applications cloud auxquelles votre administrateur vous a autorisé à accéder. Dans cette page, vos applications apparaissent comme des vignettes interactives.


La capture d’écran suivante représente un exemple de page du panneau d’accès après la personnalisation.

![][8]

## Ajout de la marque de votre société à votre annuaire

Vous pouvez configurer un ensemble par défaut d’éléments personnalisables par répertoire dans le portail Azure Classic. Une fois que les valeurs par défaut ont été enregistrées, un administrateur peut ajouter des versions localisées de chaque élément, pour différentes langues ou différents paramètres régionaux. Tous les éléments personnalisables sont facultatifs.

Par exemple, si vous configurez un logo de bannière par défaut mais aucune grande illustration, la page de connexion affiche votre logo dans le coin supérieur. Toutefois, l’illustration par défaut du site s’affiche.

Imaginez la configuration suivante :

- Un logo de bannière par défaut et le texte de la page de connexion en anglais
- Un texte spécifique pour la page de connexion en allemand

Si votre langue préférée est l’allemand, vous voyez le logo de bannière par défaut, mais le texte en allemand.

Bien que vous puissiez configurer un jeu pour chaque langue prise en charge par Azure AD, nous vous recommandons de limiter le nombre de variantes, pour des raisons de performances et de maintenance.

**Pour ajouter la marque de votre société à votre répertoire, procédez comme suit :**

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) en tant qu’administrateur de l’annuaire que vous voulez personnaliser.
2. Sélectionnez le répertoire que vous voulez personnaliser.
3. Dans la barre d’outils située en haut, cliquez sur **Configurer**.
4. Cliquez sur **Personnaliser la marque**.
4. Modifiez les éléments que vous voulez personnaliser. Tous les champs sont facultatifs.
5. Cliquez sur **Save**.

Il peut s’écouler jusqu’à une heure avant que les modifications que vous avez apportées à la page de connexion soient visibles.

**Pour ajouter une marque de société spécifique à la langue, procédez comme suit :**

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) en tant qu’administrateur de l’annuaire que vous voulez personnaliser.
2. Sélectionnez le répertoire que vous voulez personnaliser.
3. Dans la barre d’outils située en haut, cliquez sur **Configurer**.
4. Cliquez sur **Personnaliser la marque**.
2. Cliquez sur **Ajouter des paramètres de marque pour une langue spécifique**.
3. Sélectionnez la langue pour laquelle vous souhaitez personnaliser le logo, puis cliquez sur **Suivant**.
3. Modifiez uniquement les éléments pour lesquels vous souhaitez configurer des remplacements spécifiques par langue. Tous les champs sont facultatifs. Si un champ est vide, la valeur personnalisée par défaut s’affiche à la place (ou la valeur par défaut Microsoft si aucune valeur par défaut personnalisée n’est configurée).
4. Cliquez sur **Save**.

**Pour supprimer la marque de votre société de votre répertoire, procédez comme suit :**

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) en tant qu’administrateur de l’annuaire que vous voulez personnaliser.
2. Sélectionnez le répertoire que vous voulez personnaliser.
3. Dans la barre d’outils située en haut, cliquez sur **Configurer**.
4. Cliquez sur **Personnaliser la marque**.
5. Dans la page Personnaliser la marque, sélectionnez **Modifier les paramètres de marque existants**, puis passez à la page suivante.
3. En fonction des éléments que vous voulez supprimer, effectuez une ou plusieurs des opérations suivantes :

	a. Sous **Logo de bannière**, sélectionnez **Supprimer le logo téléchargé**.

    b. Sous **Logo de la mosaïque**, sélectionnez **Supprimer le logo téléchargé**.

    c. Supprimez le texte de toutes les zones de texte.

    d. Cliquez sur **Suivant**.

    e. Supprimez le texte de toutes les zones de texte.

4. Cliquez sur **Enregistrer** pour supprimer les éléments.
5. Si nécessaire, cliquez à nouveau sur **Personnaliser la marque** et répétez ces étapes pour tous les éléments de personnalisation à supprimer. Tous les paramètres de personnalisation sont supprimés lorsque vous cliquez sur **Personnaliser la marque** et que vous voyez le formulaire **Personnaliser la marque par défaut** sans aucun paramètre configuré.

## Test et exemples

Nous vous recommandons d’effectuer un test avec un client test avant d’apporter des modifications à votre environnement de production.

**Pour vérifier si votre marque a été appliquée :**

1. Ouvrez une session de navigateur InPrivate ou Incognito.
2. Rendez-vous sur https://outlook.com/contoso.com, en remplaçant contoso.com par le domaine que vous avez personnalisé.

Cela fonctionne également avec les domaines qui ressemblent à contoso.onmicrosoft.com.

Pour vous aider à créer des personnalisations efficaces, nous avons personnalisé deux pages de connexion fictives :

- [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

Pour tester les paramètres spécifiques à une langue, vous devez modifier la langue par défaut dans votre navigateur web et choisir une langue que vous avez définie dans votre personnalisation. Dans Internet Explorer, vous configurez ce paramètre dans le menu **Options Internet**.

## Éléments personnalisables

Certains éléments personnalisables d’Azure AD peuvent être utilisés de plusieurs manières. Vous pouvez configurer des logos d’entreprise une seule fois par répertoire. Ils seront utilisés à la fois sur la page de connexion et sur la page du volet d’accès. Certains éléments personnalisables sont spécifiques à la page de connexion. Le tableau suivant fournit des détails sur les différents éléments personnalisables.

Nom | Description | Contraintes | Recommandations
	------------- | ------------- | ------------- | -------------
Logo de bannière | Le logo de bannière s’affiche sur la page de connexion et sur le volet d’accès. | <p>JPG ou PNG</p><p>60 x 280 pixels</p><p>10 Ko</p> | <p>Utilisez le logo complet de votre organisation (avec pictogramme et logo)</p><p>La taille doit être inférieure à 30 pixels pour éviter l’apparition de barres de défilement sur les appareils mobiles</p><p>La taille ne doit pas dépasser 4 Ko</p><p>Utilisez un fichier PNG transparent. (Dites-vous que l’arrière-plan de la page de connexion ne sera pas toujours blanc.)</p>
Logo de la mosaïque | (Actuellement non utilisé dans la page de connexion.) À l’avenir, ce texte pourrait être utilisé pour remplacer le pictogramme générique « Compte professionnel ou scolaire » à différents endroits. | <p>JPG ou PNG</p><p>120 x 120 pixels</p><p>10 Ko</p> | <p>Restez simple (pas de texte de petite taille), cette image peut être redimensionnée à 50 %
</p> |
Étiquette du nom d’utilisateur de la page de connexion | (Actuellement non utilisé dans la page de connexion.) À l’avenir, ce texte pourrait être utilisé pour remplacer la chaîne générique « Compte professionnel ou scolaire » à différents endroits. Vous pouvez définir par exemple « Compte Contoso » ou « ID Contoso ». | <p>Texte Unicode, jusqu’à 50 caractères</p><p>Texte brut uniquement (pas de liens, ni de balises HTML)</p> | <p>Court et simple</p><p>Demandez à vos utilisateurs comment ils font généralement référence au compte professionnel ou scolaire que vous leur fournissez.</p>
Texte de la page de connexion | Ce texte « standard » apparaît sous le formulaire de la page de connexion et peut être utilisé pour communiquer des instructions supplémentaires ou des informations sur l’aide et l’assistance. | <p>Texte Unicode, jusqu’à 256 caractères</p><p>Texte brut uniquement (pas de liens, ni de balises HTML)</p> | Moins de 250 caractères (environ 3 lignes de texte)
Illustration de la page de connexion | Cette illustration est une grande image qui s’affiche sur la page de connexion à gauche du formulaire. | <p>JPG ou PNG</p><p>1420 x 1200</p><p>500 Ko</p> | <p>1420 x 1200 pixels</p><p>Important : la plus petite possible, idéalement moins de 200 Ko. Si cette image est trop grande, elle affecte les performances de la page de connexion lorsque l’image n’est pas mise en cache.</p><p>Cette image est presque toujours rognée, pour s’adapter aux différentes tailles d’écrans. Conservez les principaux éléments visuels dans l’angle supérieur gauche (en haut à droite pour les langues qui se lisent de droite à gauche), car le redimensionnement s’effectue à partir de l’angle inférieur droit vers l’angle supérieur gauche, à mesure que la fenêtre du navigateur se réduit.</p>
Couleur d’arrière-plan de la page de connexion | La couleur d’arrière-plan de la page de connexion est utilisée dans la zone à gauche du formulaire de la page. | Il doit s’agir d’une couleur RVB au format hexadécimal (par exemple #FFFFFF) | <p>La couleur d’arrière-plan peut s’afficher à la place de la grande illustration pour les connexions à faible bande passante</p><p>Nous vous suggérons de choisir la couleur principale du logo de bannière</p>


## Étapes suivantes

- [Prise en main d’Azure Active Directory Premium (AD)](active-directory-get-started-premium.md)
- [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png

<!---HONumber=AcomDC_0928_2016-->