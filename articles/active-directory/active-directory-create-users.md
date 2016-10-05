<properties
	pageTitle="Ajout de nouveaux utilisateurs à Azure Active Directory | Microsoft Azure"
	description="Expliquez comment ajouter de nouveaux utilisateurs, ou comment modifier les informations d’un utilisateur dans Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/22/2016"
	ms.author="curtand"/>

# Ajouter de nouveaux utilisateurs ou des utilisateurs disposant de comptes Microsoft à Azure Active Directory

Ajoutez des utilisateurs afin de remplir le répertoire. Cet article explique comment ajouter de nouveaux utilisateurs dans votre organisation, et comment ajouter des utilisateurs qui disposent de comptes Microsoft. Pour en savoir plus sur l’ajout d’utilisateurs à partir d’autres répertoires dans Azure Active Directory, ou sur l’ajout d’utilisateurs à partir d’entreprises partenaires, voir [Ajouter des utilisateurs à partir d’autres répertoires ou entreprises partenaires dans Azure Active Directory](active-directory-create-users-external.md). Par défaut, les utilisateurs ajoutés ne reçoivent pas d’autorisations d’administrateur, mais vous pouvez leur attribuer des rôles à tout moment.

## Ajouter un utilisateur

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Active Directory**, puis sélectionnez le nom du répertoire de votre organisation.
3. Sélectionnez l’onglet **Utilisateurs** et, dans la barre de commandes, sélectionnez **Ajouter un utilisateur**.
4. Sur la page **Dites-nous en plus sur cet utilisateur**, sous **Type d’utilisateur**, sélectionnez l’une des options suivantes :

	- **Nouvel utilisateur dans votre organisation** : permet d’ajouter un nouveau compte d’utilisateur dans votre répertoire.
	- **Utilisateur avec un compte Microsoft existant** : permet d’ajouter un compte consommateur Microsoft existant à votre répertoire (par exemple, un compte Outlook).

5. En fonction de la valeur du champ **Type d’utilisateur**, saisissez un nom d’utilisateur (pour un nouvel utilisateur) ou une adresse e-mail (pour un utilisateur doté d’un compte Microsoft).
6. Sur la page **Profil** de l’utilisateur, entrez les nom et prénom de l’utilisateur, un nom convivial et un rôle d’utilisateur, à partir de la liste **Rôles**. Pour plus d’informations sur les utilisateurs et les rôles d’administrateur, consultez la page [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md). Indiquez si vous voulez choisir l’option **Activer l’authentification multifacteur** pour cet utilisateur.
7. Sur la page **Obtenir un mot de passe temporaire**, sélectionnez **Créer**.

> [AZURE.IMPORTANT] Si votre organisation utilise plusieurs domaines, vous devez avoir connaissance des problèmes suivants lorsque vous ajoutez un compte d’utilisateur :
>
> - Pour ajouter des comptes d’utilisateurs avec le même nom d’utilisateur principal (UPN) sur plusieurs domaines, ajoutez **d’abord** geoffgrisso@contoso.onmicrosoft.com, par exemple, **suivi de** geoffgrisso@contoso.com.
> - **N’ajoutez pas** geoffgrisso@contoso.com avant d’avoir indiqué geoffgrisso@contoso.onmicrosoft.com. Il est important de respecter cet ordre et il peut être difficile de revenir en arrière.

## Modification des informations utilisateur

Vous pouvez modifier tous les attributs de l’utilisateur à l’exception de l’ID d’objet.

1. Ouvrez votre annuaire.
2. Sélectionnez l’onglet **Utilisateurs**, puis choisissez le nom d’affichage de l’utilisateur que vous souhaitez modifier.
3. Apportez les modifications nécessaires, puis cliquez sur **Enregistrer**.

Si l’utilisateur auquel vous apportez des modifications est synchronisé avec votre service Active Directory local, vous ne pouvez pas modifier les informations utilisateur en suivant cette procédure. Pour apporter des modifications à l’utilisateur, servez-vous de vos outils de gestion Active Directory locaux.

## Gestion des utilisateurs invités et limitations

Les comptes invités représentent les utilisateurs d’autres répertoires qui ont été invités dans votre répertoire pour accéder à des documents SharePoint, des applications ou d’autres ressources Azure. L’attribut UserType d’un compte invité dans votre répertoire est défini sur « Invité ». L’attribut UserType des utilisateurs habituels (c’est-à-dire ceux qui sont membres de votre répertoire) est « Membre ».

Au sein de l’annuaire, les invités disposent de droits limités. Ces droits limitent la capacité des invités à découvrir des informations sur les autres utilisateurs du répertoire. Toutefois, les utilisateurs invités peuvent toujours interagir avec les utilisateurs et groupes associés aux ressources sur lesquelles ils travaillent. Les utilisateurs invités peuvent :

- Voir les autres utilisateurs et groupes associés à l’abonnement Azure auquel ils sont affectés
- Voir les membres des groupes auxquels ils appartiennent
- Rechercher d’autres utilisateurs dans le répertoire, à condition de connaître leur adresse e-mail complète
- Voir un ensemble d’attributs limité au nom d’affichage, à l’adresse e-mail, au nom d’utilisateur principal (UPN) et à la photo miniature des utilisateurs qu’ils recherchent
- Obtenir une liste des domaines vérifiés dans le répertoire
- Donner leur consentement aux applications, en leur accordant le même accès qu’aux membres dans votre annuaire

## Définir les stratégies d’accès utilisateur

L’onglet **Configurer** d’un répertoire inclut des options de contrôle d’accès des utilisateurs invités. Ces options peuvent uniquement être modifiées dans le portail Azure Classic par un administrateur général du répertoire. Pour le moment, il n’existe aucune méthode PowerShell ou API.

Pour ouvrir l’onglet **Configurer** dans le portail Azure Classic, sélectionnez **Active Directory**, puis le nom du répertoire.

![Onglet Configurer d’Azure Active Directory][1]

Vous pouvez ensuite modifier les options de contrôle d’accès des utilisateurs invités.

![Options de contrôle d’accès des utilisateurs invités][2]


## Étapes suivantes

- [Ajouter des utilisateurs à partir d’autres répertoires ou entreprises partenaires dans Azure Active Directory](active-directory-create-users-external.md)
- [Administration d’Azure AD](active-directory-administer.md)
- [Gestion des mots de passe dans Azure AD](active-directory-manage-passwords.md)
- [Gestion des groupes dans Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0928_2016-->