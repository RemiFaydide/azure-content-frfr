## En passant par le portail Azure

1. Sélectionnez la machine virtuelle que vous souhaitez redéployer, puis cliquez sur le bouton « Redéployer » dans le panneau « Paramètres ». Faites défiler vers le bas pour voir la section **Support et dépannage** section qui contient le bouton « Redéployer », comme dans l’exemple suivant :

	![Panneau Machines virtuelles Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)

2. Cliquez sur le bouton « Redéployer » pour confirmer l’opération :

	![Panneau Redéployer une machine virtuelle](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)

3. **L’état** de la machine virtuelle passe à *Mise à jour* tandis qu’elle se prépare au redéploiement, comme dans l’exemple suivant :

	![Mise à jour de la machine virtuelle](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)

4. **L’état** passe ensuite à *Démarrage* tandis que la machine virtuelle démarre sur un nouvel hôte Azure, comme dans l’exemple suivant :

	![Démarrage de la machine virtuelle](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)

5. Une fois que la machine virtuelle a terminé le processus de démarrage, son **État** redevient *Exécution*, indiquant qu’elle a été correctement redéployée :

	![Exécution de la machine virtuelle](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

<!---HONumber=AcomDC_0921_2016-->