<properties
   pageTitle="Création de rapports Azure Active Directory - version préliminaire | Microsoft Azure"
   description="Répertorie les différents rapports disponibles pour la version préliminaire d’Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/25/2016"
   ms.author="markvi"/>

# Création de rapports Azure Active Directory - version préliminaire

*Cette documentation fait partie du guide [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Dans la version préliminaire d’Azure Active Directory, la création de rapports vous permet d’obtenir toutes les informations dont vous avez besoin pour déterminer quel est l’état de votre environnement. [Nouveautés de la version préliminaire](active-directory-preview-explainer.md)

La création de rapports couvre principalement deux domaines :

- **Activités de connexion** – Informations sur l’utilisation des applications gérées et les activités de connexion des utilisateurs

- **Activités du système** – Informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, à vos applications gérées et aux activités de répertoire

Selon l’étendue des données que vous recherchez, vous pouvez accéder à ces rapports en cliquant sur **Utilisateurs et groupes** ou **Applications d’entreprise** dans la liste des services au sein du [portail Azure](https://portal.azure.com).

## Activités de connexion

### Activités de connexion des utilisateurs

Avec les informations fournies par le rapport sur les connexions des utilisateurs, trouvez des réponses aux questions telles que :

- Quel est le modèle de connexion d’un utilisateur ?
- Combien d’utilisateurs se sont connectés au cours d’une semaine ?
- Quel est l’état de ces connexions ?

Votre point d’entrée pour ces données est le graphique des connexions des utilisateurs dans la section **Overview** (Vue d’ensemble) sous **Utilisateurs et groupes**.

 ![Reporting](./media/active-directory-reporting-azure-portal/05.png "Reporting")

Le graphique des connexions des utilisateurs affiche les agrégations hebdomadaires des connexions de tous les utilisateurs au cours d’une période donnée. La valeur par défaut de cette période est de 30 jours.

![Reporting](./media/active-directory-reporting-azure-portal/02.png "Reporting")

Lorsque vous cliquez sur un jour dans le graphique des connexions, vous obtenez une liste détaillée des activités de connexion.

![Reporting](./media/active-directory-reporting-azure-portal/03.png "Reporting")

Chaque ligne dans la liste des activités de connexion vous fournit des informations détaillées sur la connexion sélectionnée, par exemple :

- Qui s’est connecté ?

- Qui a l’UPN associé ?

- Quelle application a été la cible de la connexion ?

- Quelle est l’adresse IP de la connexion ?

- Quel est l’état de la connexion ?

### Utilisation des applications gérées

En disposant d’une vue centrée sur les applications de vos données de connexion, vous pouvez répondre aux questions telles que :

- Qui utilise mes applications ?

- Quelles sont les 3 principales applications dans votre organisation ?

- J’ai récemment déployé une application. Comment se comporte-t-elle ?


Les 3 principales applications de votre organisation dans le rapport sur les 30 derniers jours apparaissant dans la section **Vue d’ensemble** sous **Enterprise applications** (Applications d’entreprise) constituent votre point d’entrée.

 ![Reporting](./media/active-directory-reporting-azure-portal/06.png "Reporting")


Le graphique d’utilisation des applications affiche les agrégations hebdomadaires des connexions pour vos 3 principales applications au cours d’une période donnée. La valeur par défaut de cette période est de 30 jours.

![Reporting](./media/active-directory-reporting-azure-portal/78.png "Reporting")

Si vous le souhaitez, vous pouvez définir la focalisation sur une application spécifique.

![Reporting](./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Reporting")


Lorsque vous cliquez sur un jour dans le graphique d’utilisation des applications, vous obtenez une liste détaillée des activités de connexion.


![Reporting](./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Reporting")



L’option **Connexions** vous fournit une vue d’ensemble complète de tous les événements de connexion à vos applications.

![Reporting](./media/active-directory-reporting-azure-portal/85.png "Reporting")

En utilisant le sélecteur de colonne, vous pouvez sélectionner les champs de données que vous souhaitez afficher.

![Reporting](./media/active-directory-reporting-azure-portal/column_chooser.png "Reporting")



### Filtrage des connexions

Vous pouvez filtrer les connexions selon un intervalle de temps pour limiter la quantité de données affichées.

![Reporting](./media/active-directory-reporting-azure-portal/927.png "Reporting")


Une autre méthode de filtrage des entrées des activités de connexion consiste à rechercher des entrées spécifiques. La méthode de recherche vous permet de centrer les connexions sur des **utilisateurs**, des **groupes** ou des **applications** spécifiques.


![Reporting](./media/active-directory-reporting-azure-portal/84.png "Reporting")

## Journaux d’audit

Les journaux d’audit dans Azure Active Directory fournissent des enregistrements des activités du système pour la conformité.

Dans le portail Azure, les activités connexes à l’audit portent sur trois catégories principales :

- Utilisateurs et groupes

- Applications

- Répertoire


Pour obtenir une liste complète des activités de rapport d’audit, consultez la [liste des événements de rapport d’audit](active-directory-reporting-audit-events.md#list-of-audit-report-events).


Les **Journaux d’audit** dans la section **Activité** **d’Azure Active Directory** constituent votre point d’entrée pour toutes les données d’audit.


![Audit](./media/active-directory-reporting-azure-portal/61.png "Audit")


Un journal d’audit comporte un affichage de liste qui montre les acteurs (qui), les activités (quoi) et les cibles.


![Audit](./media/active-directory-reporting-azure-portal/345.png "Audit")


En cliquant sur un élément dans l’affichage de liste, vous pouvez obtenir plus d’informations sur cet élément.

![Audit](./media/active-directory-reporting-azure-portal/873.png "Audit")




### Journaux d’audit des utilisateurs et des groupes


Les rapports d’audit basés sur les utilisateurs et les groupes vous permettent d’obtenir des réponses aux questions telles que :

- Quels types de mises à jour ont été appliquées aux utilisateurs ?

- Combien d’utilisateurs ont été modifiés ?

- Combien de mots de passe ont été modifiés ?

- Qu’a fait un administrateur dans un répertoire ?

- Quels sont les groupes qui ont été ajoutés ?

- Existe-t-il des groupes comportant des modifications d’adhésion ?

- Les propriétaires de groupe ont-ils été modifiés ?

- Quelles licences ont été attribuées à un groupe ou un utilisateur ?


Si vous souhaitez simplement consulter les données d’audit connexes aux utilisateurs et aux groupes, vous pouvez trouver une vue filtrée sous **Journaux d’audit** dans la section **Activité** de **Utilisateurs et groupes**.


![Audit](./media/active-directory-reporting-azure-portal/93.png "Audit")


### Journaux d’audit des applications

Les rapports d’audit basés sur les applications vous permettent d’obtenir des réponses aux questions telles que :

- Quelles applications ont été ajoutées ou mises à jour ?

- Quelles applications ont été supprimées ?

- Le principal du service d’une application a-t-il été modifié ?

- Les noms des applications ont-ils été modifiés ?

- Qui a donné son consentement à une application ?


Si vous souhaitez simplement consulter les données d’audit connexes aux applications, vous pouvez trouver une vue filtrée sous **Journaux d’audit** dans la section **Activité** de **Applications d’entreprise**.


![Audit](./media/active-directory-reporting-azure-portal/134.png "Audit")


### Filtrage des journaux d’audit

Vous pouvez filtrer un rapport d’audit selon un intervalle de temps pour limiter la quantité de données affichées.

![Audit](./media/active-directory-reporting-azure-portal/324.png "Audit")

Une autre méthode de filtrage des entrées d’un journal d’audit consiste à rechercher des entrées spécifiques.

![Audit](./media/active-directory-reporting-azure-portal/237.png "Audit")

## Étapes suivantes

Consultez le [Guide Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).

<!---HONumber=AcomDC_0928_2016-->