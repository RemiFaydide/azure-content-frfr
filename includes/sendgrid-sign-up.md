Les clients Azure peuvent débloquer 25 000 courriers électroniques gratuits chaque mois. Ces 25 000 courriers électroniques mensuels gratuits vous donnent accès aux rapports avancés, aux analyses et à [toutes les API][] \(Web, SMTP, Event, Parse, etc.). Pour plus d’informations sur les services supplémentaires proposés par SendGrid, consultez la page [Fonctionnalités SendGrid][].

### Création d’un compte SendGrid

1. Connectez-vous au [portail de gestion Azure][].

2. Dans le volet inférieur du portail de gestion, cliquez sur **New**.

	![command-bar-new][command-bar-new]

3. Cliquez sur **Marketplace**.

	![store-sendgrid][sendgrid-store]

4. Dans la boîte de dialogue **Choisir une application et un service**, sélectionnez **SendGrid** et cliquez sur la flèche droite.

5. Dans la boîte de dialogue **Personnaliser l'application et le service**, sélectionnez le plan **SendGrid** de votre choix pour l’inscription.

6. Entrez un nom pour identifier le service **SendGrid** dans vos paramètres Azure ou utilisez la valeur par défaut **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Les noms doivent contenir entre 1 et 100 caractères et ne peuvent inclure que des lettres, des chiffres, des tirets, des points et des traits de soulignement. Le nom doit être unique dans la liste des éléments de l’Azure Store auxquels vous êtes abonné.

	![store-screen-2][store-screen-2]

7. Choisissez une valeur pour la région ; par exemple, Bretagne.

8. Cliquez sur la flèche droite.

9. Sous l’onglet **Review Purchase**, vérifiez le plan et les informations tarifaires, ainsi que les conditions juridiques. Si vous acceptez les conditions juridiques, cliquez sur la coche. Une fois que vous avez coché la case, votre compte SendGrid lance le [processus d'approvisionnement de SendGrid].

	![store-screen-3][store-screen-3]

10. Après la confirmation de votre achat, vous êtes redirigé vers le tableau de bord des modules et le message **Purchasing SendGrid** s’affiche.

	![sendgrid-purchasing-message][sendgrid-purchasing-message]

	Votre compte SendGrid est immédiatement déployé et le message **Successfully purchased Add-On SendGrid** s’affiche. Votre compte et vos informations d’identification sont créés. Vous pouvez désormais envoyer des courriers électroniques.

	Pour modifier votre plan d’abonnement ou voir les paramètres de contact de SendGrid, cliquez sur le nom du service SendGrid pour ouvrir le tableau de bord Marketplace SendGrid.

	![sendgrid-add-on-dashboard][sendgrid-add-on-dashboard]

	Pour envoyer un courrier électronique à l’aide de SendGrid, vous devez fournir les informations d’identification de votre compte (nom d’utilisateur et mot de passe).

### Recherche de vos informations d’identification SendGrid ###

1. Cliquez sur **Connection Info**.

	![sendgrid-connection-info-button][sendgrid-connection-info-button]

2. Dans la boîte de dialogue *Connection info*, copiez le **Mot de passe** et le nom d’utilisateur pour une utilisation ultérieure dans ce didacticiel.

	![sendgrid-connection-info][sendgrid-connection-info]

	Pour définir les paramètres de remise des courriers électroniques, cliquez sur le bouton **Manage**. Cette action vous redirige vers le panneau de configuration de SendGrid.

	![sendgrid-control-panel][sendgrid-control-panel]

	Pour plus d’informations sur la mise en route de SendGrid, consultez la page [Mise en route de SendGrid][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[Fonctionnalités SendGrid]: http://sendgrid.com/features
[portail de gestion Azure]: https://manage.windowsazure.com
[Mise en route de SendGrid]: http://sendgrid.com/docs
[processus d'approvisionnement de SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[toutes les API]: https://sendgrid.com/docs/API_Reference/index.html

<!---HONumber=Oct15_HO3-->