<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec RightScale | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et RightScale."
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
	ms.date="09/19/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à RightScale

L’objectif de ce didacticiel est de vous montrer comment intégrer RightScale à Azure AD (Azure Active Directory). L’intégration de RightScale à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à RightScale
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à RightScale (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD à RightScale, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement RightScale pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de RightScale à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de RightScale à partir de la galerie
Pour configurer l’intégration de RightScale à Azure AD, vous devez ajouter RightScale, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter RightScale à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
 
	![Applications][4]

6. Dans la zone de recherche, tapez **RightScale**.
 
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_01.png)

7. Dans le volet des résultats, sélectionnez **RightScale**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Sélection de l’application dans la galerie](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_02.png)

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec RightScale avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur RightScale équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur RightScale associé doit être établie.


Pour configurer et tester l’authentification unique Azure AD avec RightScale, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test RightScale](#creating-a-rightscale-test-user)** pour avoir un équivalent de Britta Simon dans RightScale lié à la représentation Azure AD associée.
5. **[Affectation d'un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail classique et de configurer l’authentification unique dans votre application RightScale.



**Pour configurer l’authentification unique Azure AD avec RightScale, procédez comme suit :**

1. Dans le portail classique, dans la page d’intégration d’applications **RightScale**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique][6]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à RightScale**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_03.png)

3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous souhaitez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit et cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_04.png)


    a. Dans la zone de texte URL de réponse, tapez l’URL au format suivant : `https://login.rightscale.com/login/saml2/consume`

	b. Cliquez sur **Suivant**

4. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur de service** dans la page de boîte de dialogue **Configurer les paramètres d’application**, cliquez sur **Affichez les paramètres avancés (facultatif)**, saisissez **l’URL de connexion** et cliquez sur **Suivant**.
 
	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_06.png)

	a. Dans la zone de texte URL d’authentification, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application RightScale, au format suivant : `https://login.rightscale.com/`

	b. Cliquez sur **Suivant**

5. Dans la page **Configurer l’authentification unique sur RightScale**, procédez comme suit et cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat encodé en base 64 sur votre ordinateur.

    b. Cliquez sur **Next**.


5. Pour que l’authentification unique soit configurée pour votre application, vous devez vous connecter à votre locataire RightScale en tant qu’administrateur.

	a. Dans le menu situé en haut, cliquez sur l’onglet **Paramètres** et sélectionnez **Authentification unique**.

	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_001.png)

	b. Cliquez sur le bouton « **nouveau** » pour ajouter **vos fournisseurs d’identité SAML**.

	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_002.png)

	c. Dans la zone de texte **Nom d'affichage**, entrez le nom de votre société.
	
	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_003.png)

	d. Sélectionnez **Allow RightScale-initiated SSO using a discovery hint** et entrez votre **nom de domaine** dans la zone de texte ci-dessous.

	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_004.png)

	e. Copiez l’URL SSO SAML d'Azure AD vers **SAML SSO Endpoint** dans RightScale.

	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_005.png)
	
	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_006.png)

	f. Copiez l’ID entité d’Azure AD vers **SAML EntityID** dans RightScale.
	
	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_007.png)
	
	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_008.png)

	g. Cliquez sur le bouton **Explorateur** pour télécharger le certificat que vous avez téléchargé à l’étape 4.
	
	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_009.png)

	h. Cliquez sur **Save**.
	


6. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

	![Authentification unique Azure AD][10]

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
  
	![Authentification unique Azure AD][11]




### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_05.png)

    a. Dans **Type d’utilisateur**, sélectionnez **Nouvel utilisateur dans votre organisation**.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur test RightScale

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans RightScale. Communiquez avec l’équipe de support RightScale via support@rightscale.com pour ajouter des utilisateurs dans la plate-forme RightScale.


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à RightScale.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à RightScale, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail classique, dans l’affichage du répertoire, cliquez sur l’option **Applications** figurant dans le menu supérieur.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **RightScale**.

	![Configurer l’authentification unique](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_50.png)

1. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

1. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

2. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]



### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Quand vous cliquez sur la mosaïque RightScale dans le volet d’accès, vous devez être connecté automatiquement à votre application RightScale.
	

## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->