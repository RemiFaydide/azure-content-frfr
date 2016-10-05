<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Cezanne HR Software | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cezanne HR Software."
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


# Didacticiel : Intégration d’Azure Active Directory avec Cezanne HR Software

L’objectif de ce didacticiel est de vous montrer comment intégrer Cezanne HR Software dans Azure AD (Azure Active Directory).

L’intégration de Cezanne HR Software dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Cezanne HR Software
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Cezanne HR Software (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD avec Cezanne HR Software, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Cezanne HR Software pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Cezanne HR Software à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Cezanne HR Software à partir de la galerie
Pour configurer l’intégration de Cezanne HR Software avec Azure AD, vous devez ajouter Cezanne HR Software, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Cezanne HR Software à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
	
	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
	
	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, entrez **Cezanne HR Software**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. Dans le volet des résultats, sélectionnez **Cezanne HR Software**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Sélection de l’application dans la galerie](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Cezanne HR Software avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Cezanne HR Software équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Cezanne HR Software associé doit être établi.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Cezanne HR Software.

Pour configurer et tester l’authentification unique Azure AD avec Cezanne HR Software, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Cezanne HR Software](#creating-a-cezanne-hr-software-test-user)** pour avoir un équivalent de Britta Simon dans Cezanne HR Software qui soit lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Cezanne HR Software.

**Pour configurer l’authentification unique Azure AD avec Cezanne HR Software, procédez comme suit :**

1. Dans le portail classique, dans la page d’intégration d’applications **Cezanne HR Software**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
	 
	![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Cezanne HR Software**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
    
	![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant** :

    ![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

	a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.

    b. Dans la zone de texte **Identificateur**, tapez : `https://w3.cezanneondemand.com/CezanneOnDemand/`.

	c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.

	d. Cliquez sur **Suivant**

	> [AZURE.NOTE] Vous devez mettre à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’équipe de support Cezanne HR Software via <mailto:info@cezannehr.com>.

4. Dans la page **Configurer l’authentification unique sur Cezanne HR Software**, procédez comme suit et cliquez sur **Suivant** :

	![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **Suivant**.

5. Dans une autre fenêtre de navigateur web, connectez-vous à votre client Cezanne HR Software en tant qu’administrateur.

6. Dans le volet de navigation gauche, cliquez sur **Configuration système**. Accédez aux **Paramètres de sécurité**. Accédez ensuite à **Configuration de l’authentification unique**.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. Dans le panneau **Autoriser les utilisateurs à se connecter à l’aide de l’authentification unique**, cochez la case **SAML 2.0** et sélectionnez l’option **Configuration avancée** en regard.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. Cliquez sur le bouton **Ajouter nouveau**.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. Dans la section **SAML 2.0 IDENTITY PROVIDERS**, procédez comme suit.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

	a. Entrez le nom de votre fournisseur d’identité en tant que **Nom d’affichage**.

	b. Dans la zone de texte **Identificateur de l’entité**, copiez la valeur de **ID d’entité** indiquée dans l’Assistant Configuration de l’application Azure AD.

	c. Modifiez la **Liaison SAML** sur POST.

	d. Dans la zone de texte **Point de terminaison de service du jeton de sécurité**, copiez la valeur de **URL du service d’authentification unique** indiquée dans l’Assistant Configuration de l’application Azure AD.

	e. Entrez http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name dans la zone de texte **Nom d’attribut de l’ID d’utilisateur**.

	f. Cliquez sur **Télécharger** pour charger le certificat obtenu auprès d’Azure AD.

	g. Cliquez sur le bouton **OK**.

10. Cliquez sur le bouton **Enregistrer**.

	![Configurer l’authentification unique côté application](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
	![Authentification unique Azure AD][10]

12. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
    
	![Authentification unique Azure AD][11]



### Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation de gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit :
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :
    
	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.



### Création d’un utilisateur de test Cezanne HR Software

Pour permettre aux utilisateurs Azure AD de se connecter à Cezanne HR Software, vous devez les approvisionner dans Cezanne HR Software. Dans le cas de Cezanne HR Software, cet approvisionnement est une tâche manuelle.

####Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise Cezanne HR Software en tant qu’administrateur.

2.  Dans le volet de navigation gauche, cliquez sur **Configuration système**. Accédez à **Gérer les utilisateurs**. Accédez ensuite à **Ajouter un nouvel utilisateur**.

    ![Nouvel utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nouvel utilisateur")

3.  À la section **Détails de la personne**, procédez comme suit :

    ![Nouvel utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nouvel utilisateur")

	a. Désactivez l’option **Utilisateur interne**.

	b. Dans la zone de texte **Prénom**, entrez **Britta**.

    c. Dans la zone de texte **Nom**, entrez **Simon**.

	d. Dans la zone de texte **E-mail**, tapez l’adresse de messagerie du compte de Britta Simon.

4.  À la section **Informations sur le compte**, procédez comme suit :

    ![Nouvel utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nouvel utilisateur")

	a. Dans la zone de texte **Nom d’utilisateur**, tapez l’adresse de messagerie du compte de Britta Simon.

	b. Dans la zone de texte **Mot de passe**, tapez le mot de passe du compte de Britta Simon.

	c. Sélectionnez **Professionnel des RH** en tant que **Rôle de sécurité**.

	d. cliquez sur **OK**.

5. Accédez à l’onglet **Authentification unique** et sélectionnez **Ajouter nouveau** dans la zone **Identificateurs SAML 2.0**.

	![Utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utilisateur")

6. Choisissez votre **Fournisseur d’identité** et dans la zone de texte **Identificateur d’utilisateur**, entrez l’adresse de messagerie du compte de Britta Simon.

	![Utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utilisateur")
	
7. Cliquez sur le bouton **Enregistrer**.

	![Utilisateur](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utilisateur")


### Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Cezanne HR Software.
	
![Affecter des utilisateurs][200]

**Pour attribuer Britta Simon à Cezanne HR Software, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur l’option **Applications** figurant dans le menu du haut.
    
	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Cezanne HR Software**.
    
	![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
    
	![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
    
	![Affecter des utilisateurs][205]

### Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
 
Lorsque vous cliquez sur la vignette Cezanne HR Software dans le volet d’accès, vous devez être connecté automatiquement à votre application Cezanne HR Software.

## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->