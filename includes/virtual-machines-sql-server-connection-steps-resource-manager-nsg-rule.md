### Configuration d'une règle de trafic entrant pour le Groupe de sécurité réseau pour la machine virtuelle

Si vous souhaitez vous connecter à SQL Server via Internet, vous devez configurer une règle de trafic entrant pour le Groupe de sécurité réseau pour le port d'écoute de votre instance SQL Server. Par défaut, il s'agit du port TCP 1433.

1. Dans le portail, sélectionnez **Machines virtuelles**, puis sélectionnez votre machine virtuelle SQL Server.

3. Sélectionnez alors les **Interfaces réseau**.

	![interface réseau](./media/virtual-machines-sql-server-connection-steps/rm-network-interface.png)

4. Sélectionnez ensuite l'interface réseau de votre machine virtuelle.

4. Cliquez sur le lien **Groupe de sécurité réseau**.

	![interface réseau](./media/virtual-machines-sql-server-connection-steps/rm-network-security-group.png)

6. Dans les propriétés du Groupe de sécurité réseau, développez la section **Règles de sécurité de trafic entrant**.

5. Cliquez sur le bouton **Add**.

6. Entrez le **nom** « SQLServerPublicTraffic ».

7. Modifiez le **protocole** sur **TCP**.

8. Spécifiez 1433 pour la **plage de ports de destination** (ou le port d’écoute de votre Instance SQL Server).

9. Vérifiez que la propriété **Action** est définie sur **Autoriser**. La boîte de dialogue de la règle de sécurité doit être similaire au contenu de la capture d'écran suivante.

	![règle de sécurité réseau](./media/virtual-machines-sql-server-connection-steps/rm-network-security-rule.png)

9. Cliquez sur **OK** pour enregistrer la règle pour votre machine virtuelle.

>[AZURE.NOTE] Il est possible d’avoir un deuxième groupe de sécurité réseau associé à votre sous-réseau (distinct du groupe de sécurité réseau sur la machine virtuelle). Cela n’est pas effectué par l’utilisateur par défaut. Toutefois, si vous avez créé un groupe de sécurité réseau sur votre sous-réseau, vous devez ouvrir le port 1433 sur le sous-réseau et le groupe de sécurité réseau de la machine virtuelle.

<!---HONumber=AcomDC_0921_2016-->