<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory au Portail du financement | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et le Portail du financement."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory au Portail du financement

Dans ce didacticiel, vous allez apprendre à intégrer le Portail du financement à Azure Active Directory (Azure AD).

L’intégration du Portail du financement à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès au Portail du financement.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement au Portail du financement (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD au Portail du financement, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement au **Portail du financement** pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout du Portail du financement à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout du Portail du financement à partir de la galerie
Pour configurer l’intégration du Portail du financement à Azure AD, vous devez ajouter le Portail du financement depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter le Portail du financement à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **Portail du financement**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_01.png)

7. Dans le volet de résultats, sélectionnez **Portail du financement**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_02.png)

##  Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec le Portail du financement avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur du Portail du financement équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur du Portail du financement associé doit être établie. Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans le Portail du financement.

Pour configurer et tester l’authentification unique Azure AD avec le Portail du financement, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test du Portail du financement](#creating-a-the-funding-portal-test-user)** pour avoir un équivalent de Britta Simon dans le Portail du financement, lié à la représentation Azure AD associée.
5. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Portail du financement.

L’application Portail du financement attend que les assertions SAML contiennent un attribut nommé « externalId1 ». La valeur de l’attribut « externalId1 » doit être un studentID reconnu. Configurez la revendication « externalId1 » pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **Attributs** de l’application. La capture d’écran suivante montre un exemple :

![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_03.png)

**Pour configurer l’authentification unique Azure AD avec le Portail du financement, procédez comme suit :**

1. Dans la page d’intégration d’applications **Portail du financement** du portail Azure Classic, dans le menu situé en haut, cliquez sur **Attributs**.
     
    ![Configurer l’authentification unique][5]

2. Dans la boîte de dialogue Attributs du jeton SAML, ajoutez l’attribut « externalId1 ».

	a. Cliquez sur **ajouter un attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter un attribut utilisateur**.
	
	![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_05.png)

	b. Dans la zone de texte **Nom de l’attribut**, indiquez le nom de l’attribut « externalId1 ».

	c. Dans la liste **Valeur d’attribut**, sélectionnez l’attribut que vous souhaitez utiliser pour votre implémentation. Par exemple, si vous avez stocké la valeur StudentID dans la zone ExtensionAttribute1, sélectionnez user.extensionattribute1.

	d. Cliquez sur **Terminé**. Cliquez sur **Appliquer les modifications**.

1. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.

	![Configurer l’authentification unique][6]

2. Dans la page d’intégration d’applications **Portail du financement** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][7]

3. Dans la page **Comment voulez-vous que les utilisateurs se connectent au Portail du financement ?**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
 	
	![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_06.png)

4. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_07.png)


    a. Dans la zone de texte URL de connexion, tapez une URL au format suivant : `https://<subdomain>.regenteducation.net/`.

	b. Cliquez sur **Suivant**.

5. Dans la page **Configurer l’authentification unique sur le Portail du financement**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

	![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_08.png)

6. Pour que l’authentification unique soit configurée pour votre application, contactez l’équipe du support technique du Portail du financement. Ils vous aideront à configurer l’authentification unique à l’aide du canal approprié. Notez que vous devez envoyer un e-mail en y joignant le fichier de métadonnées téléchargé à l’adresse info@regenteducation.com.

7. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
	
	![Authentification unique Azure AD][10]

8. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
  	
	![Authentification unique Azure AD][11]

### Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
	
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
	
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :
 
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test du Portail du financement

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans le Portail du financement. Si vous ne savez pas comment ajouter Britta Simon au Portail du financement, rapprochez-vous de l’équipe du support technique du Portail du financement pour ajouter l’utilisateur de test et activer l’authentification unique. Contactez-la à l’adresse info@regenteducation.com.

### Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès au Portail du financement.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon au Portail du financement, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur l’option **Applications** figurant dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Portail du financement**.

	![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_09.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Tous les utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]


### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Portail du financement dans le volet d’accès, vous devez être connecté automatiquement à votre application Portail du financement.

## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->