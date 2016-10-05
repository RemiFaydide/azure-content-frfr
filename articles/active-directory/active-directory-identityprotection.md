<properties
	pageTitle="Azure Active Directory Identity Protection | Microsoft Azure"
	description="Découvrez comment Azure AD Identity Protection vous permet de limiter la capacité d’un cybercriminel à exploiter une identité ou un appareil compromis et de sécuriser une identité ou un appareil déjà identifié comme potentiellement ou effectivement compromis."
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

Azure Active Directory Identity Protection est une fonctionnalité de l’édition Azure AD Premium P2 offrant une vue consolidée des événements à risque et des vulnérabilités potentielles qui affectent les identités de votre organisation. Microsoft sécurise les identités dans le cloud depuis plus de dix ans. Avec Azure AD Identity Protection, Microsoft propose met ces mêmes systèmes de protection à disposition des clients d’entreprise. Identity Protection tire parti des fonctionnalités existantes de détection des anomalies d’Azure AD (disponibles via les rapports d’activités anormales d’Azure AD) et introduit de nouveaux types d’événements à risque capables de détecter les anomalies en temps réel.



##Mise en route

La grande majorité des violations de sécurité ont lieu lorsque des cybercriminels parviennent à accéder à un environnement en volant l’identité d’un utilisateur. Les cybercriminels arrivent de plus en plus à exploiter les failles de fournisseurs tiers et utilisent des attaques par hameçonnage (ou « phishing ») sophistiquées toujours plus efficaces. Une fois qu’un cybercriminel accède à un compte d’utilisateur, même si les privilèges de celui-ci sont faibles, il est relativement simple pour lui d’accéder à des ressources d’entreprise importantes de manière latérale. Il est donc essentiel de protéger toutes les identités et, lorsqu’une identité est compromise, d’empêcher que celle-ci soit utilisée à des fins malveillantes.

Détecter les identités compromises n’est pas chose aisée. Fort heureusement, le service Identity Protection peut vous y aider : s’appuyant sur des algorithmes d’apprentissage automatique adaptatif et des règles heuristiques, il assure la détection d’anomalies et d’événements à risque susceptibles d’indiquer qu’une identité a été compromise.
 
À l’aide de ces données, Identity Protection génère des rapports et des alertes qui vous permettent d’analyser ces événements à risque et de prendre les mesures de correction ou d’atténuation qui s’imposent.
 
Mais Azure Active Directory Identity Protection est plus qu’un outil de surveillance et de création de rapports. En fonction des événements à risque, Identity Protection calcule le niveau de risque des utilisateurs pour chaque utilisateur, ce qui vous permet de configurer des stratégies basées sur les risques pour protéger automatiquement les identités de votre organisation. Ces stratégies basées sur les risques, en plus des autres contrôles d’accès conditionnel fournis par Azure Active Directory et EMS, peuvent automatiquement bloquer l’accès ou appliquer des mesures de correction adaptatives qui incluent la réinitialisation de mot de passe et la mise en œuvre l’authentification multifacteur.

####Exploration du service Identity Protection 

**Détection des événements et des comptes à risque :**

- Détection de 6 types d’événements à risque à l’aide de l’apprentissage automatique et des règles heuristiques

- Calcul du niveau de risque des utilisateurs

- Recommandations personnalisées visant à améliorer la posture de sécurité globale en mettant en évidence les vulnérabilités



**Examen des événements à risque :**

- Envoi de notifications pour les événements à risque

- Examen des événements à risque à l’aide d’informations contextuelles et pertinentes

- Workflows de base pour le suivi des investigations

- Accès rapide à des mesures de correction telles que la réinitialisation de mot de passe



**Stratégies d’accès conditionnel en fonction des risques :**

- Stratégie pour atténuer les connexions à risque en bloquant les connexions ou en imposant des demandes d’authentification multifacteur.

- Stratégie pour bloquer ou sécuriser les comptes d’utilisateurs à risque

- Stratégie pour exiger que les utilisateurs s’inscrivent à l’authentification multifacteur


## Détection et risque

### Événements à risque

Les événements à risque sont des événements qui ont été signalés comme suspects par Identity Protection et indiquent qu’une identité a peut-être été compromise. Pour obtenir la liste complète des événements à risque, consultez [Types d’événements à risque détectés par Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md).

Certains de ces événements à risque sont disponibles via les rapports d’activités anormales d’Azure AD dans le portail Azure. Le tableau ci-dessous répertorie les différents types d’événements à risque et le rapport d’**activités anormales d’Azure AD** correspondant. Microsoft poursuit ses investissements dans ce domaine et prévoit d’améliorer la précision de détection des événements à risque existants et d’ajouter de nouveaux types d’événements à risque de façon continue.



| Type d’événement à risque signalé par Identity Protection | Rapport d’activités anormales d’Azure AD correspondant |
| :-- | :-- |
| Informations d’identification divulguées | Utilisateurs avec des informations d’identification volées |
| Voyage impossible vers des emplacements inhabituels |	Activité de connexion anormale |
| Connexions depuis des appareils infectés | Connexions à partir d’appareils potentiellement infectés |
| Connexions depuis des adresses IP anonymes | Connexions à partir de sources inconnues |
| Connexions depuis des adresses IP avec des activités suspectes |	Connexions depuis des adresses IP avec des activités suspectes |
| Connexions depuis des emplacements non connus | - |
| Événements de verrouillage (non disponible dans la version préliminaire publique) | - |

Les rapports d’activités anormales d’Azure AD suivants ne sont pas inclus en tant qu’événements à risque dans Azure AD Identity Protection et ne sont donc pas disponibles via Identity Protection. Ces rapports sont toujours disponibles dans le portail Azure, mais ils seront rendus obsolètes ultérieurement du fait de leur remplacement par des événements à risque dans Identity Protection.

- Connexions après plusieurs échecs
- Connexions depuis plusieurs zones géographiques

### Niveau de risque

Le niveau de risque d’un événement à risque est une indication (Élevé, Moyen ou Faible) de la gravité de l’événement à risque. Le niveau de risque permet aux utilisateurs du service Identity Protection de hiérarchiser les mesures qu’ils doivent prendre afin de réduire le risque pour leur organisation. Le niveau de gravité de l’événement à risque signalé représente la probabilité qu’une identité soit compromise, avec la quantité de bruit que cela implique généralement.

- **Élevé** : probabilité élevée et gravité élevée de l’événement à risque. Ces événements donnent une indication forte que l’identité de l’utilisateur a été compromise et les comptes d’utilisateurs concernés doivent immédiatement faire l’objet de mesures de correction.

- **Moyen** : sévérité élevée, mais probabilité moindre de l’événement à risque, ou inversement. Ces événements présentent des risques potentiels et les comptes d’utilisateurs concernés doivent faire l’objet de mesures de correction.

- **Faible** : probabilité faible et gravité limitée de l’événement à risque. Cet événement peut ne pas nécessiter une action immédiate, mais l’association à d’autres événements à risque peut donner une indication forte que l’identité a été compromise.


![Niveau de risque](./media/active-directory-identityprotection/01.png "Niveau de risque")

 

Les événements à risque sont soit identifiés en **temps réel**, soit en post-traitement après la survenue de l’événement à risque (hors connexion). À l’heure actuelle, la plupart des événements à risque sont calculés hors connexion et s’affichent dans Identity Protection dans un délai de 2 à 4 heures. Bien qu’évalués au moment où ils surviennent, les événements à risque en temps réel s’affichent dans la console d’Identity Protection dans un délai de 5 à 10 minutes.

Plusieurs clients hérités ne prennent pas en charge la détection et la prévention des événements en temps réel pour le moment. Par conséquent, les connexions depuis ces clients ne peuvent pas être détectées ou bloquées en temps réel.


## Investigation
Votre parcours dans Identity Protection commence généralement par le tableau de bord d’Identity Protection.

![Correction](./media/active-directory-identityprotection/1000.png "Correction")

Le tableau de bord vous donne accès à :
 
- des rapports comme **Utilisateurs associés à un indicateur de risque**, **Événements à risque** et **Vulnérabilités** ;
- des paramètres vous permettant notamment de configurer vos **stratégies de sécurité**, vos **notifications** et l’**inscription à l’authentification multifacteur**.
 

Il s’agit généralement de votre point de départ pour l’investigation, processus consistant à vérifier les activités, les journaux et les autres informations pertinentes concernant un événement à risque, afin de déterminer s’il est nécessaire d’appliquer des mesures de correction ou d’atténuation, de comprendre comment l’identité a été compromise et d’identifier la manière dont l’identité compromise a été exploitée.

Vous pouvez lier vos activités d’investigation aux [notifications](active-directory-identityprotection-notifications.md) d’Azure Active Directory Protection envoyées par courrier électronique.

Les sections suivantes fournissent plus de détails, ainsi que les étapes liées à une investigation.



## Qu’est-ce que le niveau de risque d’un utilisateur ?

Le niveau de risque d’un utilisateur est une indication (Élevé, Moyen ou Faible) de la probabilité que l’identité de l’utilisateur ait été compromise. Il est calculé en fonction des événements à risque associés à l’identité de l’utilisateur.

L’état d’un événement à risque est soit **Actif**, soit **Fermé**. Seuls les événements à risque dont l’état est défini sur **Actif** entrent dans le calcul du risque d’un utilisateur.

Le niveau de risque d’un utilisateur est calculé à l’aide des données suivantes :

- Événements à risque actifs ayant un impact sur l’utilisateur
- Niveau de risque de ces événements
- Application ou non de mesures de correction

![Risque des utilisateurs](./media/active-directory-identityprotection/1001.png "Risque des utilisateurs")



Vous pouvez utiliser le niveau de risque des utilisateurs pour créer des stratégies d’accès conditionnel empêchant les utilisateurs à risque de se connecter ou les forçant à modifier leur mot de passe de façon sécurisée.


## Fermeture manuelle des événements à risque

Dans la plupart des cas, vous pouvez prendre des mesures de correction telles qu’une réinitialisation de mot de passe sécurisée pour fermer automatiquement les événements à risque. Toutefois, il se peut que cela ne soit pas toujours possible. C’est par exemple le cas quand :

- un utilisateur avec des événements à risque actifs a été supprimé ;
- une enquête révèle qu’un événement à risque signalé a été effectué par l’utilisateur légitime.

Comme les événements à risque dont l’état est défini sur **Actif** entrent dans le calcul du risque des utilisateurs, vous pouvez avoir besoin de réduire manuellement un niveau de risque en fermant manuellement les événements à risque. Au cours de l’investigation, vous pouvez choisir d’effectuer n’importe laquelle des actions suivantes pour modifier l’état d’un événement à risque :

![Actions](./media/active-directory-identityprotection/34.png "Actions")

- **Résoudre** : si après avoir examiné un événement à risque, vous avez pris une mesure de correction appropriée en dehors d’Identity Protection et pensez que l’événement à risque doit être considéré comme fermé, marquez l’événement comme résolu. L’état des événements à risque résolus est défini sur Fermé et ces événements n’entrent plus dans le calcul du risque d’un utilisateur.

- **Marquer comme faux positif** : dans certains cas, il se peut qu’un événement à risque soit signalé à tort en tant que tel. Vous pouvez réduire le nombre de ces événements en les marquant comme faux positifs. Vous aiderez ainsi les algorithmes d’apprentissage automatique à améliorer la classification des événements similaires par la suite. L’état des événements marqués comme faux positifs est défini sur **Fermé** et ces événements n’entrent plus dans le calcul du risque de l’utilisateur.

- **Ignorer** : si vous n’avez pris aucune mesure de correction, mais que vous souhaitez que l’événement à risque soit supprimé de la liste active, vous pouvez choisir de l’ignorer. Son état sera alors défini sur Fermé. Les événements ignorés n’entrent plus dans le calcul du risque d’un utilisateur. Cette option doit uniquement être utilisée dans des circonstances inhabituelles.

- **Réactiver** : les événements à risque qui ont été fermés manuellement (en choisissant **Résoudre**, **Marquer comme faux positif** ou **Ignorer**) peuvent être réactivés. Leur statut est alors défini à nouveau sur **Actif**. Les événements à risque réactivés contribuent au calcul du niveau de risque d’un utilisateur. Les événements à risque fermés à l’aide d’une mesure de correction (telle qu’une réinitialisation de mot de passe sécurisée) ne peuvent pas être réactivés.




**Pour ouvrir la boîte de dialogue de configuration connexe** :

1. Dans le panneau **Azure AD Identity Protection**, sous **Examiner**, cliquez sur **Événements à risque**.

	![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1002.png "Réinitialisation manuelle du mot de passe")

2. Dans la liste **Événements à risque**, cliquez sur un risque.

	![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1003.png "Réinitialisation manuelle du mot de passe")

2. Dans le panneau Risque, cliquez avec le bouton droit sur un utilisateur.

	![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1004.png "Réinitialisation manuelle du mot de passe")



### Fermeture manuelle de tous les événements à risque pour un utilisateur

Au lieu de fermer manuellement les événements à risque pour chaque utilisateur, Azure Active Directory Identity Protection propose une méthode permettant de fermer tous les événements pour un utilisateur en un seul clic.


![Actions](./media/active-directory-identityprotection/2222.png "Actions")

Lorsque vous cliquez sur **Ignorer tous les événements**, tous les événements sont fermés et l’utilisateur concerné n’est plus exposé.



## Correction des événements à risque d’un utilisateur

Une correction est une mesure visant à sécuriser une identité ou un appareil déjà identifié comme potentiellement ou effectivement compromis. Une mesure de correction permet de rétablir la sécurité de l’identité ou de l’appareil et de résoudre les anciens événements à risque associés à l’identité ou à l’appareil.

Pour corriger les événements à risque d’un utilisateur, vous pouvez procéder comme suit :

- Effectuez une réinitialisation de mot de passe sécurisée pour corriger manuellement les événements à risque de l’utilisateur.

- Configurez une stratégie de sécurité en matière de risque des utilisateurs pour atténuer ou corriger automatiquement les événements à risque de l’utilisateur.

- Réimager l’appareil infecté.


### Réinitialisation manuelle et sécurisée du mot de passe

Une réinitialisation de mot de passe sécurisée est une mesure de correction efficace pour de nombreux événements à risque. Lorsqu’elle est effectuée, ces événements à risque sont fermés et le niveau de risque de l’utilisateur correspondant recalculé automatiquement. Vous pouvez utiliser le tableau de bord d’Identity Protection afin de lancer une réinitialisation de mot de passe pour un utilisateur à risque.

La boîte de dialogue connexe fournit deux méthodes différentes pour réinitialiser le mot de passe :

**Réinitialiser le mot de passe** : sélectionnez **Demander à l’utilisateur de réinitialiser le mot de passe** pour permettre à l’utilisateur de récupérer lui-même son compte s’il s’est inscrit à l’authentification multifacteur. La prochaine fois que l’utilisateur se connectera, il devra résoudre une demande d’authentification multifacteur, puis sera obligé de changer le mot de passe. Cette option n’est pas disponible si le compte d’utilisateur n’est pas déjà inscrit à l’authentification multifacteur.

**Mot de passe temporaire** : sélectionnez **Générer un mot de passe temporaire** pour invalider immédiatement le mot de passe existant et créer un nouveau mot de passe temporaire pour l’utilisateur. Envoyez le nouveau mot de passe temporaire à une autre adresse de messagerie de l’utilisateur ou au responsable de l’utilisateur. Étant donné que le mot de passe est temporaire, l’utilisateur sera invité à changer le mot de passe lors de la connexion.


![Stratégie](./media/active-directory-identityprotection/1005.png "Stratégie")


**Pour ouvrir la boîte de dialogue de configuration connexe** :

1. Dans le panneau d’**Azure AD Identity Protection**, cliquez sur **Utilisateurs associés à un indicateur de risque**.

	![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1006.png "Réinitialisation manuelle du mot de passe")


2. Dans la liste des utilisateurs, sélectionnez un utilisateur comportant au moins un risque.

	![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1007.png "Réinitialisation manuelle du mot de passe")


2. Dans le panneau Utilisateur, cliquez sur **Réinitialiser le mot de passe**.

	![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1008.png "Réinitialisation manuelle du mot de passe")





## Stratégie de sécurité en matière de risque des utilisateurs

Une stratégie de sécurité en matière de risque des utilisateurs est une stratégie d’accès conditionnel qui évalue le niveau de risque d’un utilisateur spécifique et applique des mesures de correction et d’atténuation en fonction de conditions et de règles prédéfinies.


![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1009.png "Stratégie en matière de risque des utilisateurs")


Azure AD Identity Protection vous aide à gérer les mesures de correction et d’atténuation pour les utilisateurs associés à un indicateur de risque, en vous permettant d’effectuer les opérations suivantes :

- Définir les utilisateurs et les groupes auxquels la stratégie s’applique :

	![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1010.png "Stratégie en matière de risque des utilisateurs")


- Définir le niveau de risque d’un utilisateur (Faible, Moyen ou Élevé) qui déclenche la stratégie :

	![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1011.png "Stratégie en matière de risque des utilisateurs")


- Définir les contrôles à appliquer lorsque la stratégie est déclenchée :

	![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1012.png "Stratégie en matière de risque des utilisateurs")


- Basculer l’état de votre stratégie :

	![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/403.png "Inscription à MFA")


- Examiner et évaluer l’impact d’un changement avant de l’appliquer :

	![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1013.png "Stratégie en matière de risque des utilisateurs")


La sélection d’un niveau de risque **Élevé** réduit la fréquence de déclenchement d’une stratégie et minimise l’impact sur les utilisateurs. Cependant, cela a pour effet d’exclure les utilisateurs associés à un indicateur de risque **Faible** et **Moyen**. Par conséquent, il se peut que des identités ou des appareils déjà identifiés comme potentiellement ou effectivement compromis ne soient pas sécurisés.

Pour définir la stratégie

- Excluez les utilisateurs susceptibles de générer un grand nombre de faux positifs (développeurs, analystes de sécurité).

- Excluez les utilisateurs situés dans des régions où l’activation de la stratégie n’est pas adaptée (par exemple, aucun accès au support technique).

- Utilisez un niveau de risque **Élevé** pendant le déploiement initial de la stratégie ou si vous devez minimiser la complexité pour les utilisateurs finaux.

- Utilisez un niveau de risque **Faible** si votre organisation nécessite une sécurité accrue. La sélection d’un niveau de risque **Faible** complique la connexion pour les utilisateurs, mais renforce la sécurité.

Pour la plupart des organisations, nous recommandons de configurer un niveau de risque **Moyen** afin d’établir un juste équilibre entre facilité d’utilisation et sécurité.

Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

- [Flux de récupération de compte compromis](active-directory-identityprotection-flows.md#compromised-account-recovery).

- [Flux de compte compromis bloqué](active-directory-identityprotection-flows.md#compromised-account-blocked).


**Pour ouvrir la boîte de dialogue de configuration connexe** :

1. Dans le panneau **Azure AD Identity Protection**, à la section**Configurer**, cliquez sur **Stratégie de risque d’utilisateur**.

	![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1009.png "Stratégie en matière de risque des utilisateurs")






## Atténuation des événements à risque d’un utilisateur
Les administrateurs peuvent définir une stratégie de sécurité en matière de risque des utilisateurs pour bloquer les utilisateurs lors de la connexion selon le niveau de risque.

Le blocage d’une connexion :
 
- empêche la génération de nouveaux événements à risque pour l’utilisateur concerné ;

- permet aux administrateurs de corriger manuellement les événements à risques affectant l’identité de l’utilisateur pour sécuriser à nouveau cette dernière.



## Qu’est-ce que le niveau de risque à la connexion ?

Le niveau de risque à la connexion est une indication (élevée, moyenne ou faible) indiquant la probabilité qu’un tiers tente de s’authentifier à l’aide de l’identité de l’utilisateur dans le cadre d’une connexion spécifique. Le niveau de risque à la connexion est évalué au moment de la connexion, en prenant en compte les risques et les indicateurs détectés en temps réel pour cette connexion.

## Atténuation des événements à risque à la connexion 
Une atténuation est une mesure visant à limiter la probabilité qu’un pirate exploite une identité ou un appareil compromis, mais qui ne permet pas de rétablir la sécurité de l’identité ou de l’appareil en question. Une atténuation ne résout pas les précédents événements de connexion à risque associés à l’identité ou à l’appareil.

Vous pouvez utiliser l’accès conditionnel dans Azure AD Identity Protection pour atténuer automatiquement les événements à risque à la connexion. Avec ce type de stratégie, vous prenez en compte le niveau de risque de l’utilisateur ou de la connexion pour bloquer les connexions à risque ou exiger une authentification multifacteur de la part de l’utilisateur. Ces mesures peuvent empêcher un cybercriminel d’exploiter une identité volée pour causer des dommages et vous donner le temps de sécuriser l’identité.


## Stratégie de sécurité en matière de risque à la connexion

Une stratégie en matière de risque à la connexion est une stratégie d’accès conditionnel consistant à évaluer le risque associé à une connexion spécifique et qui applique des mesures d’atténuation à partir de règles et de conditions prédéfinies.

![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1014.png "Stratégie en matière de risque à la connexion")


Azure AD Identity Protection vous aide à gérer l’atténuation des connexions à risque, en vous permettant d’effectuer les opérations suivantes :

- Définir les utilisateurs et les groupes auxquels la stratégie s’applique :

	![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1015.png "Stratégie en matière de risque à la connexion")


- Définir le niveau de risque à la connexion (Faible, Moyen ou Élevé) qui déclenche la stratégie :

	![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1016.png "Stratégie en matière de risque à la connexion")


- Définir les contrôles à appliquer lorsque la stratégie est déclenchée :

	![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1017.png "Stratégie en matière de risque à la connexion")


- Basculer l’état de votre stratégie :

	![Inscription à MFA](./media/active-directory-identityprotection/403.png "Inscription à MFA")

- Examiner et évaluer l’impact d’un changement avant de l’appliquer :

	![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1018.png "Stratégie en matière de risque à la connexion")

 
La sélection d’un niveau de risque **Élevé** réduit la fréquence de déclenchement d’une stratégie et minimise l’impact sur les utilisateurs.
 
Cependant, cela a pour effet d’exclure les connexions associées à un indicateur de risque **Faible** et **Moyen**. Par conséquent, il se peut qu’un cybercriminel soit en mesure d’exploiter une identité compromise.

Pour définir la stratégie

- Excluez les utilisateurs qui ne sont pas inscrits/ne peuvent pas s’inscrire à l’authentification multifacteur.

- Excluez les utilisateurs situés dans des régions où l’activation de la stratégie n’est pas adaptée (par exemple, aucun accès au support technique).

- Excluez les utilisateurs susceptibles de générer un grand nombre de faux positifs (développeurs, analystes de sécurité).

- Utilisez un niveau de risque **Élevé** pendant le déploiement initial de la stratégie ou si vous devez minimiser la complexité pour les utilisateurs finaux.

- Utilisez un niveau de risque **Faible** si votre organisation nécessite une sécurité accrue. La sélection d’un niveau de risque **Faible** complique la connexion pour les utilisateurs, mais renforce la sécurité.

Pour la plupart des organisations, nous recommandons de configurer un niveau de risque **Moyen** afin d’établir un juste équilibre entre facilité d’utilisation et sécurité.

 
La stratégie en matière de risque à la connexion :

- est appliquée à l’ensemble du trafic de navigateur et des connexions utilisant une authentification moderne ;
- n’est pas appliquée aux applications utilisant des protocoles de sécurité plus anciens en désactivant le point de terminaison WS-Trust sur le fournisseur d’identité fédérée, tels qu’ADFS.

La page **Événements à risque** de la console Identity Protection répertorie tous les événements :

- auxquels cette stratégie a été appliquée ;
- pour lesquels vous pouvez consulter l’activité afin de déterminer si la mesure était appropriée ou non.

Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

- [Récupération de connexion à risque](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

- [Connexion à risque bloquée](active-directory-identityprotection-flows.md#risky-sign-in-blocked)

- [Inscription à l’authentification multifacteur au cours d’une connexion à risque](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)





**Pour ouvrir la boîte de dialogue de configuration connexe** :

1. Dans le panneau **Azure AD Identity Protection**, à la section**Configurer**, cliquez sur **Stratégie en matière de risque à la connexion**.

	![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1014.png "Stratégie en matière de risque des utilisateurs")





## Stratégie d’inscription à l’authentification multifacteur

Azure Multi-Factor Authentication est une méthode permettant de vérifier votre identité qui requiert l’utilisation d’autres méthodes que le nom d’utilisateur et le mot de passe. Ce service fournit une deuxième couche de sécurité pour les connexions et les transactions de l'utilisateur. Nous vous recommandons d’exiger l’authentification multifacteur d’Azure des connexions des utilisateurs pour les raisons suivantes :

- Elle fournit une authentification renforcée avec un éventail d’options de vérification simples.

- Elle joue un rôle clé dans la préparation de votre organisation pour protéger et récupérer les comptes compromis.

![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1019.png "Stratégie en matière de risque des utilisateurs")



Pour plus d’informations, consultez [Qu’est-ce qu’Azure Multi-Factor Authentication ?](../multi-factor-authentication/multi-factor-authentication.md)


Azure AD Identity Protection vous permet de gérer le déploiement de l’inscription à l’authentification multifacteur en configurant une stratégie qui vous permet d’effectuer les opérations suivantes :



- Définir les utilisateurs et les groupes auxquels la stratégie s’applique :

	![Stratégie MFA](./media/active-directory-identityprotection/1020.png "Stratégie MFA")



- Définir les contrôles à appliquer lorsque la stratégie est déclenchée :

	![Stratégie MFA](./media/active-directory-identityprotection/1021.png "Stratégie MFA")


- Basculer l’état de votre stratégie :

	![Stratégie MFA](./media/active-directory-identityprotection/403.png "Stratégie MFA")

- Afficher l’état d’inscription actuel :

	![Stratégie MFA](./media/active-directory-identityprotection/1022.png "Stratégie MFA")


Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

- [Processus d’inscription à l’authentification multifacteur](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).

- [Inscription à l’authentification multifacteur au cours d’une connexion à risque](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).





**Pour ouvrir la boîte de dialogue de configuration connexe** :

1. Dans le panneau **Azure AD Identity Protection**, à la section**Configurer**, cliquez sur **Inscription à l’authentification multifacteur**.

	![Stratégie MFA](./media/active-directory-identityprotection/1019.png "Stratégie MFA")





## Étapes suivantes

 - [Channel 9 : Azure AD and Identity Show: Identity Protection Preview (Émission sur Azure AD et l’identité : présentation d’Identity Protection)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Types d’événements à risque détectés par Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)
 - [Vulnérabilités détectées par Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md)
 - [Notifications d’Azure Active Directory Identity Protection](active-directory-identityprotection-notifications.md)
 - [Flux Azure Active Directory Identity Protection](active-directory-identityprotection-flows.md)
 - [Manuel d’Azure Active Directory Identity Protection](active-directory-identityprotection-playbook.md)
 - [Glossaire d’Azure Active Directory Identity Protection](active-directory-identityprotection-glossary.md)

 - [Activer Azure Active Directory Identity Protection](active-directory-identityprotection-enable.md)
 - [Azure Active Directory Identity Protection - Déblocage des utilisateurs](active-directory-identityprotection-unblock-howto.md)

 - [Prise en main d’Azure Active Directory Identity Protection et de Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)

<!---HONumber=AcomDC_0921_2016-->