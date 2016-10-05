<properties
	pageTitle="Prendre en main le Kit de développement logiciel (SDK) de passerelle IoT Hub | Microsoft Azure"
	description="Procédure pas à pas du Kit de développement logiciel (SDK) de passerelle Azure IoT Hub sous Linux pour illustrer les concepts clés que vous devez comprendre lorsque vous utilisez le Kit de développement logiciel (SDK) de passerelle Azure IoT Hub."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# Kit de développement logiciel de passerelle IoT (bêta) - Prise en main de Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## Comment créer l'exemple

Avant de commencer, vous devez [configurer votre environnement de développement][lnk-setupdevbox] pour utiliser le Kit de développement logiciel (SDK) sous Linux.

1. Ouvrez un interpréteur de commandes.
2. Accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.
3. Exécutez le script **tools/build.sh**. Ce script utilise l’utilitaire **cmake** pour créer un dossier appelé **build** dans le dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** et pour générer un makefile. Le script crée ensuite la solution puis exécute les tests.

> [AZURE.NOTE]  Chaque fois que vous exécutez le script **build.sh**, celui-ci supprime et recrée le dossier **build** dans le dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.

## Comment exécuter l’exemple

1. Le script **build.sh** génère sa sortie dans le dossier **build** de votre copie locale du référentiel **azure-iot-gateway-sdk**. Cela inclut les deux modules utilisés dans cet exemple.

    Le script build place **liblogger\_hl.so** dans le dossier **build/modules/logger/** et **libhello\_world\_hl.so** dans le dossier **build/modules/hello\_world/**. Utilisez ces chemins d'accès pour la valeur **module path** comme indiqué dans le fichier de paramètres JSON ci-dessous.

2. Le fichier **hello\_world\_lin.json** dans le dossier **Bonjour\_monde/samples/src** est un exemple de fichier de paramètres JSON pour Linux que vous pouvez utiliser pour exécuter l'exemple. Les paramètres JSON ci-dessous supposent que vous exécuterez l'exemple à partir de la racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.

3. Pour le module **logger\_hl**, dans la section **args**, définissez la valeur **filename** sur le nom et le chemin d'accès du fichier qui contiendra les données de journalisation.

    Il s'agit d'un exemple de fichier de paramètres JSON pour Linux qui écrira les données dans le fichier **log.txt**, dans le dossier où vous exécutez l'exemple.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. Dans votre interpréteur de commandes, accédez au dossier **azure-iot-gateway-sdk**.
4. Exécutez la commande suivante :
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

<!---HONumber=AcomDC_0928_2016-->