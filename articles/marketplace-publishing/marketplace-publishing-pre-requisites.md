<properties
   pageTitle="Conditions préalables non techniques pour créer une offre pour Azure Marketplace | Microsoft Azure"
   description="Découvrez la configuration requise pour créer et déployer une offre dans Azure Marketplace que d’autres utilisateurs peuvent acheter."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="08/18/2016"
  ms.author="hascipio"/>

# Conditions préalables générales pour créer une offre pour Azure Marketplace
Découvrez les conditions préalables générales centrées sur le processus d’entreprise qui sont nécessaires dans les étapes de création de l’offre.

## S’inscrire en tant que vendeur auprès de Microsoft
Pour obtenir des instructions détaillées sur l’inscription d’un compte de vendeur auprès de Microsoft, accédez à [Création et inscription de comptes](marketplace-publishing-accounts-creation-registration.md).

- **Si votre entreprise est déjà enregistrée en tant que vendeur dans le centre de développement et que vous souhaitez créer une offre,** connectez-vous au portail de publication portail avec le même ID de messagerie que celui utilisé pour l’inscription sur le centre de développement. Cette étape est requise afin que le portail de publication et le centre de développement soient liés entre eux.
- **Si votre entreprise est déjà enregistrée en tant que vendeur dans le centre de développement et que vous souhaitez modifier une offre existante,** connectez-vous au portail de publication avec le compte administrateur ou avec un compte qui a été ajouté comme coadministrateur dans le portail de publication. Les étapes pour ajouter un compte coadministrateur sont détaillées ci-dessous.

## Étapes pour ajouter un coadministrateur dans le portail de publication
Les administrateurs du portal de publication ajouter les autres membres de l’entreprise, qui travaillent sur l’application, comme coadministrateurs dans le portail de publication. **En supposant que vous êtes l’administrateur,** voici les étapes pour ajouter un coadministrateur.

>[AZURE.NOTE] Pour les nouveaux utilisateurs, avant d’ajouter un coadministrateur au portail de publication, assurez-vous que vous avez créé au moins une application dans le portail de publication. Cela est nécessaire, car l’onglet **ÉDITEURS** s’affiche uniquement après la création d’au moins une application dans le portail de publication.

1. Assurez-vous que l’ID de messagerie du coadministrateur est un compte Microsoft (MSA). Si ce n’est pas le cas, enregistrez-le comme MSA à l’aide de ce [lien](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Vérifiez qu’il existe au moins une application sous le compte administrateur avant d’essayer d’ajouter un coadministrateur.
3. Une fois les étapes ci-dessus effectuées, connectez-vous au portail de publication avec l’ID de messagerie du coadministrateur et déconnectez-vous..
4. Maintenant, connectez-vous au portail de publication avec l’ID de messagerie du coadministrateur.
5. Accédez à Éditeurs->Sélectionnez votre compte->Administrateurs->Ajouter le compte (voir capture d’écran ci-dessous)

    ![dessin](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)

6. Assurez-vous que les ID de messagerie fournis aux divers stades du processus de publication (par ex. centre de développement, portail de publication) sont surveillés pour toutes les communications de Microsoft.
7. Pour l’inscription au centre de développement, évitez d’utiliser un compte associé à une seule personne. Cela est conseillé pour éviter la dépendance à un seul individu.
8. Si vous rencontrez des problèmes lors de l’enregistrement sur le centre de développement, envoyez un ticket à l’aide de ce [lien](https://developer.microsoft.com/fr-FR/windows/support).

## Étapes pour supprimer un coadministrateur dans le portail de publication
**En supposant que vous êtes l’administrateur,** voici les étapes pour supprimer un coadministrateur.

1. Connectez-vous au portail de publication avec l’ID de messagerie du coadministrateur.
2. Accédez à **Éditeurs** -> sélectionnez votre compte -> **Administrateurs** -> **Coadministrateurs**.
3. Cliquez sur le bouton **X** en regard du coadministrateur que vous souhaitez supprimer (capture d’écran ci-dessous).

    ![dessin](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [AZURE.IMPORTANT] Vous n’avez pas besoin de remplir les informations bancaires et fiscales de votre entreprise si vous prévoyez de ne publier que des offres gratuites (ou d’apporter votre propre licence).

> L’inscription de l’entreprise doit être terminée pour pouvoir commencer. Toutefois, si votre entreprise renseigne ses informations fiscales et bancaires dans le compte de développeur Microsoft, les développeurs peuvent commencer à créer l’image de machine virtuelle dans le [portail de publication](https://publish.windowsazure.com), pour la faire certifier et la tester dans l’environnement intermédiaire Azure. Vous avez besoin de l’approbation complète du compte de vendeur uniquement pour la dernière étape de publication de votre offre dans Azure Marketplace.

## Acquisition d’un abonnement Azure avec « paiement à l’utilisation »
C’est l’abonnement que vous utilisez pour créer vos images de machine virtuelle et les transférer dans [Azure Marketplace](https://azure.microsoft.com/marketplace/). Si vous ne possédez pas d’abonnement existant, inscrivez-vous ici : https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## Pays à partir duquel vous vendez
> [AZURE.WARNING]
Avant de pouvoir vendre vos services dans Azure Marketplace, assurez-vous que l’entité inscrite réside dans l’un des pays autorisés. Cette restriction s’applique pour des raisons de revenus et de taxes. Nous cherchons activement à étendre cette liste de pays dans un avenir proche, donc restez connecté. Pour obtenir la liste complète, consultez la Section 1b des [Politiques concernant la participation à Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

## Étapes suivantes
Une fois que les conditions préalables non techniques requises sont remplies, vous devez vous pencher sur les conditions préalables techniques propres à l’offre. Cliquez sur le lien vers l’article du type d’offre que vous voulez créer pour Azure Marketplace.

- [Conditions préalables techniques des machines virtuelles](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Conditions préalables techniques des modèles de solution](marketplace-publishing-solution-template-creation-prerequisites.md)

## Voir aussi
- [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0824_2016-->