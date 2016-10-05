1. Dans le portail, accédez au réseau virtuel auquel vous souhaitez connecter une passerelle.

2. Dans la section**Paramètres** du panneau de votre réseau virtuel, cliquez sur **Sous-réseaux** pour développer le panneau Sous-réseaux.

3. Dans le panneau **Sous-réseaux**, cliquez sur **+ Sous-réseau de passerelle** en haut. Cette opération ouvre le panneau **Ajouter un sous-réseau**. Le **Nom** de votre sous-réseau est automatiquement rempli avec la valeur « GatewaySubnet ». Cette valeur est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle.

	![Ajouter le sous-réseau de passerelle](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsubnet250.png)

4. Vous pouvez modifier le bloc CIDR de plages d’adresses si nécessaire. Vérifiez les exigences spécifiques de votre configuration pour confirmer le bloc CIDR recommandé.

5. Cliquez sur **OK** en bas du panneau pour créer le sous-réseau.

<!-------HONumber=AcomDC_0810_2016-->