<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Wizergos Productivity Software | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Wizergos Productivity Software."
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
	ms.date="09/15/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory avec Wizergos Productivity Software 

L’objectif de ce didacticiel est de vous montrer comment intégrer Wizergos Productivity Software dans Azure Active Directory (Azure AD).

L’intégration de Wizergos Productivity Software dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Wizergos Productivity Software.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Wizergos Productivity Software (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD avec Wizergos Productivity Software, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Wizergos Productivity Software pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Wizergos Productivity Software depuis la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Wizergos Productivity Software depuis la galerie
Pour configurer l’intégration de Wizergos Productivity Software avec Azure AD, vous devez ajouter Wizergos Productivity Software, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Wizergos Productivity Software depuis la galerie, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
	
	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
	
	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **Wizergos Productivity Software**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_01.png)

7. Dans le volet des résultats, sélectionnez **Wizergos Productivity Software**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Sélection de l’application dans la galerie](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_001.png)

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Wizergos Productivity Software avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Wizergos Productivity Software équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Wizergos Productivity Software associé doit être établi.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Wizergos Productivity Software.

Pour configurer et tester l’authentification unique Azure AD avec Wizergos Productivity Software, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Wizergos Productivity Software](#creating-a-wizergos-productivity-software-test-user)** pour avoir un équivalent de Britta Simon dans Wizergos Productivity Software qui soit lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Wizergos Productivity Software.

**Pour configurer l’authentification unique Azure AD avec Wizergos Productivity Software, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration d’applications **Wizergos Productivity Software**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
	 
	![Configurer l’authentification unique][6]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Wizergos Productivity Software**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
    
	![Configurer l’authentification unique](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_03.png)

3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous souhaitez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit et cliquez sur **Suivant** :

    ![Configurer l’authentification unique](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_04.png)

	a. Dans la zone de texte **Identificateur**, tapez `https://www.wizergos.net`.

    b. Dans la zone de texte **URL de réponse**, tapez : `https://www.wizergos.net/register_users/saml2/`

	c. Cliquez sur **Suivant**

4. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur de service** dans la page de boîte de dialogue **Configurer les paramètres d’application**, cliquez sur **Affichez les paramètres avancés (facultatif)**, saisissez **l’URL de connexion** et cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_05.png)

	a. Dans la zone de texte **URL d’authentification**, tapez `https://www.wizergos.net`.

	b. Cliquez sur **Suivant**

5. Dans la page **Configurer l’authentification unique sur Wizergos Productivity Software**, procédez comme suit et cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_06.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Suivant**.

6. Dans une autre fenêtre de navigateur web, connectez-vous à votre client Wizergos Productivity Software en tant qu’administrateur.

7. Dans le menu de type hamburger, sélectionnez **Admin** (Administrateur).

	![Configurer l’authentification unique côté application](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

8. Dans le menu de gauche de la page Admin (Administrateur), sélectionnez **AUTHENTICATION** (AUTHENTIFICATION), puis cliquez sur **Azure AD**.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

9. Dans la section **AUTHENTICATION** (AUTHENTIFICATION), procédez comme suit :

	![Configurer l’authentification unique côté application](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)

	a. Ouvrez le certificat téléchargé depuis Azure AD et copiez l’empreinte de certificat. Ensuite, collez-la dans la zone de texte **Certificate ThumbPrint** (Empreinte de certificat).

	![Configurer l’authentification unique côté application](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_004.png)

	b. Dans la zone de texte **Issuer URL** (URL de l’émetteur), copiez la valeur de **URL de l’émetteur** indiquée dans l’Assistant Configuration de l’application Azure AD.

	c. Dans la zone de texte **Single Sign-On URL** (URL d’authentification unique), copiez la valeur de **URL du service d’authentification unique** indiquée dans l’Assistant Configuration de l’application Azure AD.

	d. Dans la zone de texte **Single Sign-Out URL** (URL de déconnexion unique), copiez la valeur de **URL du service de déconnexion unique** indiquée dans l’Assistant Configuration de l’application Azure AD.

	e. Cliquez sur le bouton **Enregistrer**.

	> [AZURE.NOTE] Notez que l’empreinte de certificat ne doit contenir aucun espace.

7. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
	![Authentification unique Azure AD][10]

8. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
    
	![Authentification unique Azure AD][11]



### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit :
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test Wizergos Productivity Software

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Wizergos Productivity Software. Collaborez avec l’équipe du support technique Wizergos Productivity Software via l’adresse [support@wizergos.com](emailTo:support@wizergos.com) pour ajouter les utilisateurs à la plateforme Wizergos Productivity Software.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Wizergos Productivity Software.
	
   ![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Wizergos Productivity Software, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur l’option **Applications** figurant dans le menu du haut.
    
	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Wizergos Productivity Software**.
    
	![Configurer l’authentification unique](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_50.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
    
	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
    
	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
 
Lorsque vous cliquez sur la vignette Wizergos Productivity Software dans le volet d’accès, vous devez être connecté automatiquement à votre application Wizergos Productivity Software.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->