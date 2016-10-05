1. Dans l’**Explorateur de solutions** Visual Studio, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter > Prise en charge Docker** dans le menu contextuel.

    ![Ajouter le menu contextuel Prise en charge Docker](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. L’ajout de la prise en charge Docker à un projet web ASP.NET Core entraîne l’ajout de plusieurs fichiers liés à Docker au projet, y compris des fichiers Docker Compose, des scripts de déploiement Windows PowerShell et des fichiers de propriétés de Docker.

    ![Fichiers Docker ajoutés au projet](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE] Si vous utilisez la [version bêta de Docker pour Windows](https://beta.docker.com), ouvrez Properties\\Docker.props et supprimez la valeur par défaut, puis redémarrez Visual Studio pour que la valeur prenne effet.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```

<!---HONumber=AcomDC_0921_2016-->