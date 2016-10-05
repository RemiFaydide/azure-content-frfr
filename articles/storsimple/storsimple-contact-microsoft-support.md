<properties 
   pageTitle="Contacter le support Microsoft | Microsoft Azure"
   description="Découvrez comment créer une demande de support et démarrer une session de support sur votre appareil StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# Contacter le support Microsoft

Si vous rencontrez des problèmes avec votre solution Microsoft Azure StorSimple, vous pouvez créer une demande de service pour le support technique. Lors d’une session en ligne avec votre ingénieur de support, vous devrez également démarrer une session de support sur votre appareil StorSimple. Cet article vous guide tout au long des procédures suivantes :

- Création d’une demande de support
- Démarrage d’une session de support dans l’interface Windows PowerShell de votre appareil StorSimple

Examinez les [informations et les contrats de niveau de service relatifs à la prise en charge de la gamme StorSimple 8000](https://msdn.microsoft.com/library/mt433077.aspx) avant de créer une demande de support.

## Création d’une demande de support

Procédez comme suit pour créer une demande de support.

#### Création d’une demande de support

1. Sur le [portail Azure Classic](https://manage.windowsazure.com/), dans le coin supérieur droit, cliquez sur votre nom de compte, puis sur **Contacter le support Microsoft**.

	![Contacter le support MS via le Portail de gestion](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. Vous êtes redirigé vers le nouveau portail Azure (portal.azure.com). Cliquez sur la mosaïque **Nouvelle demande de support**.

	![Contacter le support MS avec le nouveau portail](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    Sur le côté droit de l'écran, le volet **Nouvelle demande de support** s'affiche.

	![Volet Nouvelle demande de support](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. Dans la boîte de dialogue **Bases**, procédez comme suit :
	1. Dans la liste déroulante **Type de problème**, sélectionnez **Technique**.
	2. Sélectionnez un **Abonnement** dans la liste déroulante.
	3. Dans la liste déroulante **Service**, sélectionnez **StorSimple**.
	4. Sélectionnez un **Plan de support** dans la liste déroulante. Vous avez besoin d'un plan de support payant pour bénéficier du support technique.

4. Cliquez sur **Next**. La boîte de dialogue **Problème** s'affiche.

	![Volet Nouvelle demande de support](./media/storsimple-contact-microsoft-support/Ibiza5a.png)

5. Dans la boîte de dialogue **Problème**, procédez comme suit :

    1.  Sélectionnez un niveau de **Gravité** dans la liste déroulante.
    2.  Sélectionnez un **Type de problème** dans la liste déroulante.
    3.  Sélectionnez une **Catégorie** dans la liste déroulante.
    4.  Dans la zone **Détails**, décrivez en quelques mots votre problème.
    5.  Dans la zone **Date**, indiquez la date, l’heure et le fuseau horaire correspondant à la dernière occurrence de votre problème.
    6.  Sous **Téléchargement du fichier**, cliquez sur l'icône du dossier pour accéder à votre package de support.
    7.  Cochez la case **Partager les informations de diagnostic**.

6. Cliquez sur **Suivant**. La boîte de dialogue **Coordonnées** s'affiche.

	![Volet Nouvelle demande de support](./media/storsimple-contact-microsoft-support/Ibiza6a.png)

7. Entrez vos informations de contact et sélectionnez une méthode de contact (téléphone ou courrier électronique).

8. Cochez la case **Enregistrer les modifications de contact pour les futures demandes de support**.

9. Cliquez sur **Create**.

Une fois votre demande envoyée, un ingénieur de support vous contactera dès que possible pour traiter votre demande.

## Démarrage d’une session de support dans Windows PowerShell pour StorSimple

Pour résoudre les problèmes que vous pouvez rencontrer avec l’appareil StorSimple, vous devrez contacter l'équipe de Support technique de Microsoft. Le support Microsoft devra peut-être utiliser une session de support pour se connecter à votre appareil.

Procédez comme suit pour démarrer une session de support :

#### Démarrage d’une session de support

1. Accédez directement à l’appareil à l'aide de la console série ou via une session telnet à partir d'un ordinateur distant. Pour cela, suivez les étapes de la section [Utilisation de PuTTY pour se connecter à la console série de l’appareil](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Dans la session qui s’ouvre, appuyez sur la touche **Entrée** pour afficher une invite de commandes.

3. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**.

4. À l'invite de commande, entrez le mot de passe suivant :

	`Password1`

5. À l'invite de commande, tapez la commande suivante :

	`Enable-HcsSupportAccess`

6. Une chaîne chiffrée s'affiche. Copiez cette chaîne dans un éditeur de texte comme le Bloc-notes.

7. Enregistrez cette chaîne et envoyez-la par e-mail au support Microsoft.

> [AZURE.IMPORTANT] Vous pouvez désactiver l’accès au support en exécutant `Disable-HcsSupportAccess`. L’appareil StorSimple tentera également de désactiver l'accès au support 8 heures après le début de la session. Il est recommandé de modifier vos informations d'identification de l’appareil StorSimple après le lancement d'une session de support.

<!---HONumber=AcomDC_0921_2016-->