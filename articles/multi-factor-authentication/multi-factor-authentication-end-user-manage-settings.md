<properties
	pageTitle="Problèmes avec Azure Multi-Factor Authentication | Microsoft Azure"
	description="Ce document fournit aux utilisateurs des informations sur la procédure à suivre en cas de problème avec l'Azure Multi-Factor Authentication."
	services="multi-factor-authentication"
	keywords = "client de l'authentification multifacteur, problème d'authentification, ID de corrélation"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Problèmes avec Azure Multi-Factor Authentication
>[AZURE.IMPORTANT]
Aidez-nous à améliorer cette page. Si vous ne trouvez pas de réponse à votre problème sur cette page, veuillez nous envoyer des commentaires détaillés afin que nous puissions l’ajouter.

Cette section vous aidera à résoudre certains problèmes courants que vous pouvez rencontrer.


- [Erreurs d'ID de corrélation](#correlation-id-errors)
- [Mon téléphone a été perdu ou volé](#i-have-lost-my-phone-or-it-was-stolen)
- [Je souhaite modifier mon numéro de téléphone](#i-want-to-change-my-phone-number)
- [J’ai un nouveau téléphone et je dois modifier mon numéro de téléphone](#i-have-a-new-phone-and-need-to-change-my-phone-number)
- [Je ne reçois pas de code sur mon téléphone](#i-am-not-receiving-a-code-or-a-call-on-my-phone)
- [Les mots de passe d’application ne fonctionnent pas](#app-passwords-are-not-working)
- [Comment nettoyer Microsoft Authenticator sur mon ancien appareil et le transférer vers un autre ?](#how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one)
- [Je n’ai pas trouvé de réponse à mon problème](#i-didnt-find-an-answer-to-my-problem)

##Erreurs d'ID de corrélation
Si vous avez essayé les étapes de dépannage décrites ci-dessous et que vous rencontrez toujours des problèmes, vous pouvez publier une question sur les [Forums Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD), [Rechercher dans la base de connaissances Microsoft (KB)](https://www.microsoft.com/fr-FR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport) ou [contacter le support technique](https://support.microsoft.com/fr-FR) afin que nous puissions résoudre votre problème dès que possible.

Lorsque vous contactez le support, il est recommandé de fournir les informations suivantes :

 - **Description générale de l’erreur** : quel message d’erreur exact est apparu ? Si aucun message d’erreur n’est apparu, décrivez en détail le comportement inattendu remarqué.
 - **Page** : sur quelle page vous trouviez-vous lorsque l’erreur est apparue (incluez l’URL) ?
 - **ErrorCode** : le code d’erreur spécifique que vous recevez.
 - **SessionId** : l’ID de session spécifique que vous recevez.
 - **ID de corrélation** : quel était l’ID de corrélation généré lorsque l’utilisateur a remarqué l’erreur ?
 - **Horodatage** : quelles étaient la date et l’heure précises auxquelles l’erreur s’est produite (incluez le fuseau horaire) ?

![ID de corrélation :](./media/multi-factor-authentication-end-user-manage/correlation.png)

 - **ID utilisateur** : quel est l’ID de l’utilisateur qui a vu l’erreur (par exemple user@contoso.com) ?
 - **Informations sur l’utilisateur** : l’utilisateur était-il fédéré, disposait-il de la synchronisation du hachage de mot de passe ou était-ce un utilisateur du cloud uniquement ? L'utilisateur disposait-il d'une licence Azure AD Premium, Enterprise Mobility ou Azure AD de base ? L'utilisateur utilise-t-il Office 365 ? etc.

Ces informations nous aideront à résoudre votre problème aussi rapidement que possible.

## Mon téléphone a été perdu ou volé
En cas de perte ou de vol de votre téléphone, nous vous recommandons de demander à votre administrateur de réinitialiser vos [mots de passe d’application](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) et de désactiver les [appareils mémorisés](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-suspended-devices-for-a-user).

Pour revenir à votre compte, vous avez deux options. La première consiste à utiliser un autre numéro de téléphone d’authentification (si vous l’avez configuré) pour rétablir votre compte et modifier vos paramètres de sécurité.

Si vous avez spécifié un numéro de téléphone d’authentification secondaire, vous pouvez l’utiliser pour vous connecter. ![Paramétrage](./media/multi-factor-authentication-end-user-manage/altphone.png) Notez que, dans la capture d’écran ci-dessus, deux numéros de téléphone ont été configurés, l’un se terminant par 67 et l’autre par 30.

Pour vous connecter à l’aide de votre autre numéro de téléphone, suivez la procédure de connexion normale, puis sélectionnez simplement **Utiliser une autre option de vérification**. ![Vérification différente](./media/multi-factor-authentication-end-user-manage/differentverification.png)

Sélectionnez ensuite votre autre numéro de téléphone. Dans ce cas, vous devez sélectionner **M’appeler au + X XXXXXXXX30**.

![Autre téléphone](./media/multi-factor-authentication-end-user-manage/altphone2.png)

>[AZURE.IMPORTANT]
Il est important de configurer un numéro de téléphone d’authentification secondaire. Étant donné que vous utilisez probablement le même téléphone pour votre numéro de téléphone principal et votre application mobile, le numéro de téléphone secondaire est le seul moyen dont vous disposez pour revenir à votre compte en cas de perte ou de vol de votre téléphone.

Si vous n’avez pas configuré de numéro de téléphone d’authentification secondaire, vous devrez contacter votre administrateur pour lui demander d’effacer vos paramètres. À la prochaine connexion, vous serez donc invité à configurer de nouveau [l’authentification multifacteur (Multi-Factor Authentication)](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again).

## Je souhaite modifier mon numéro de téléphone
Selon la façon dont vous utilisez l’authentification multification, vous pouvez modifier vos paramètres (votre numéro de téléphone, par exemple) de différentes manières. Utilisez le tableau ci-dessous pour vous aider à choisir la méthode la plus adaptée à vos besoins.

Mode d’utilisation de l’authentification multifacteur|Description
:------------- | :------------- |
[Je l’utilise avec Office 365](#changing-your-settings-with-office-365)| Cela signifie que vous souhaitez modifier vos paramètres par le biais du portail Office 365.
[Je ne sais pas](#changing-your-settings-with-the-myapps-portal)|Cela signifie que vous souhaitez vous connecter à [http://myapps.microsoft.com](http://myapps.microsoft.com) et y modifier votre paramètre.
[Je l’utilise avec Microsoft Azure](#changing-your-settings-with-microsoft-azure)| Cela signifie que vous souhaitez modifier vos paramètres par le biais du portail Azure.



### Modification des paramètres avec Office 365


Si vous utilisez l’authentification multifacteur avec Office 365, vous souhaitez peut-être gérer vos paramètres de vérification de sécurité supplémentaires par le biais du portail Office 365.

#### Pour modifier vos paramètres dans le portail Office 365

1. Connectez-vous au [portail Office 365](https://login.microsoftonline.com/).
2. Dans l’angle supérieur droit, sélectionnez le widget et choisissez les paramètres d’Office 365.
3. Cliquez sur Vérification de sécurité supplémentaire.
4. Sur la droite, cliquez sur le lien indiquant **Mettre à jour mes numéros de téléphone utilisés pour la sécurité du compte.** ![O365](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Cette opération vous amènera sur la page qui vous permet de modifier vos paramètres. ![O365](./media/multi-factor-authentication-end-user-manage/o365b.png)


### Modification des paramètres avec le portail Myapps

Si vous n’êtes pas sûr de la façon d’utiliser l’authentification multifacteur, vous pouvez toujours modifier vos paramètres via le portail myapps.

#### Pour modifier vos paramètres dans le portail Myapps

1. Ouvrez une session sur [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. En haut de la page, sélectionnez le profil.
3. Sélectionnez Vérification de sécurité supplémentaire. ![MyApps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Ceci vous amènera sur la page qui vous permet de modifier vos paramètres.

![Vérification](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)

### Modification des paramètres avec Microsoft Azure

Si vous utilisez l’authentification multifacteur avec Azure, vous pouvez changer vos paramètres via le portail Azure.

#### Pour accéder aux paramètres de vérification de sécurité supplémentaires dans le portail Azure


1. Connectez-vous au portail Azure.
2. En haut du portail Azure, cliquez sur votre nom d’utilisateur. Une zone de liste déroulante s’affiche.
3. Dans la liste déroulante, sélectionnez Vérification de sécurité supplémentaire. ![Les tables Azure](./media/multi-factor-authentication-end-user-manage/azure1.png)
4. Ceci vous amènera sur la page qui vous permet de modifier vos paramètres. ![Vérification](./media/multi-factor-authentication-end-user-manage-azure/proofup.png)

##J’ai un nouveau téléphone et je dois modifier mon numéro de téléphone

Si vous disposez d’un nouveau téléphone et que vous devez modifier le numéro de contact principal utilisé par l’authentification MFA, vous pouvez procéder de deux manières.

>[AZURE.IMPORTANT]
Il est important de configurer un numéro de téléphone d’authentification secondaire. Étant donné que vous utilisez probablement le même téléphone pour votre numéro de téléphone principal et votre application mobile, le numéro de téléphone secondaire est le seul moyen dont vous disposez pour revenir à votre compte en cas de perte ou de vol de votre téléphone.

La première utilise une méthode d’authentification secondaire. Si vous avez spécifié un numéro de téléphone d’authentification secondaire, vous pouvez l’utiliser pour vous connecter. ![Paramétrage](./media/multi-factor-authentication-end-user-manage/altphone.png) Notez que, dans la capture d’écran ci-dessus, deux numéros de téléphone ont été configurés, l’un se terminant par 67 et l’autre par 30.

Pour vous connecter à l’aide de votre autre numéro de téléphone, suivez la procédure de connexion normale, puis sélectionnez simplement **Utiliser une autre option de vérification**. ![Vérification différente](./media/multi-factor-authentication-end-user-manage/differentverification.png)

Sélectionnez ensuite votre autre numéro de téléphone. Dans ce cas, vous devez sélectionner **M’appeler au + X XXXXXXXX30**.

![Autre téléphone](./media/multi-factor-authentication-end-user-manage/altphone2.png)

La seconde consiste à contacter votre administrateur ou la personne qui a configuré l’authentification MFA pour vous. Vous devez uniquement effectuer cette opération si vous n’avez pas configuré un numéro de téléphone d’authentification secondaire. Vous devrez contacter votre administrateur ou la personne qui a configuré l’authentification MFA pour lui demander d’effacer vos paramètres. À la prochaine connexion, vous serez donc invité à configurer de nouveau [l’authentification multifacteur (Multi-Factor Authentication)](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again).

##Je ne reçois pas de code ou d’appel sur mon téléphone

Vous devez tout d’abord vérifier les points suivants :



- Si vous avez choisi de recevoir un appel téléphonique sur votre téléphone mobile, vérifiez que vous disposez d’un signal adéquat. La vitesse et la disponibilité de la diffusion des messages peuvent varier selon l’emplacement et l’opérateur.
- Si vous avez choisi de recevoir les codes de vérification par SMS sur votre téléphone mobile, assurez-vous que votre plan de service et votre appareil prennent en charge la diffusion des messages texte. La vitesse et la disponibilité de la diffusion des messages peuvent varier selon l’emplacement et l’opérateur. Assurez-vous également que vous disposez d’une bonne qualité de signal lors de la réception de ces codes.
- Si vous avez choisi de recevoir une vérification via l’application mobile, vérifiez que votre téléphone reçoit bien le signal. N’oubliez pas que la vitesse et la disponibilité de la diffusion des messages peuvent varier selon l’emplacement et l’opérateur.

Si vous disposez d’un smartphone, nous vous recommandons d’utiliser l’application Microsoft Authenticator, disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Si vous recevez vos codes de vérification par des messages texte, vous pouvez changer le mode de réception et opter pour une diffusion via l'application mobile en sélectionnant simplement **Utiliser une autre option de vérification** lors de la connexion.

![Vérification différente](./media/multi-factor-authentication-end-user-manage/differentverification.png)

Il peut arriver que l’un de ces deux services soit plus fiable que l’autre.

Sachez que si vous avez reçu plusieurs codes de vérification, seul le plus récent fonctionnera.

Si vous avez déjà configuré un téléphone de secours, nous vous recommandons de réessayer en sélectionnant ce téléphone sur la page de connexion. Si vous n’avez pas configuré d’autre méthode, contactez votre administrateur et demandez-lui d’effacer les paramètres. À la prochaine connexion, vous devrez donc configurer de nouveau [l’authentification multifacteur (Multi-Factor Authentication)](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again).

##Les mots de passe d’application ne fonctionnent pas
Tout d’abord, assurez-vous que vous avez saisi correctement votre mot de passe. S’il ne fonctionne toujours pas, essayez de vous connecter et de [créer un mot de passe d’application](multi-factor-authentication-end-user-app-passwords.md). Si le problème persiste, contactez votre administrateur et demandez-lui de [supprimer vos mots de passe d’application existants](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords). Vous pourrez alors créer un mot de passe et l’utiliser.

##Comment nettoyer Microsoft Authenticator sur mon ancien appareil et le transférer vers un autre ?
Lorsque vous désinstallez l’application de votre appareil ou que vous réinitialisez l’appareil, l’application ne sera pas désactivée sur le serveur principal. Vous devez suivre la procédure décrite dans la section relative au [transfert vers un nouvel appareil](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app).

##Je n’ai pas trouvé de réponse à mon problème
Si n’a pas trouvé de réponse à votre problème sur cette page, vous pouvez publier une question sur les [Forums Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD), [Rechercher dans la base de connaissances Microsoft (KB)](https://www.microsoft.com/fr-FR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport) ou [contacter le support technique](https://support.microsoft.com/fr-FR).

En outre, vous pouvez contacter votre administrateur ou la personne qui a configuré Multi-Factor Authentication pour vous et voir s’ils peuvent vous aider.

Enfin, veillez à laisser des commentaires détaillés sur cette page afin que nous puissions la mettre à jour, et que nous continuions à l’améliorer en fournissant plus d’informations.

<!---HONumber=AcomDC_0921_2016-->