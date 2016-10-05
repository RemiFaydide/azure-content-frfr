<properties
	pageTitle="Envoi de notifications Push vers Android avec Azure Notification Hubs et Firebase Cloud Messaging | Microsoft Azure"
	description="Dans ce didacticiel, vous découvrirez comment utiliser Azure Notification Hubs Firebase Cloud Messaging pour envoyer des notifications Push à des appareils Android."
	services="notification-hubs"
	documentationCenter="android"
	keywords="notifications push,notification push,notification push android,fcm,firebase cloud messaging"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="07/14/2016"
	ms.author="wesmc"/>

# Envoi de notifications Push vers Android avec Azure Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Vue d'ensemble

> [AZURE.IMPORTANT] Cette rubrique explique les notifications Push avec Google Firebase Cloud Messaging (FCM). Si vous utilisez encore Google Cloud Messaging (GCM), consultez [Sending push notifications to Android with Azure Notification Hubs and GCM](notification-hubs-android-push-notification-google-gcm-get-started.md) (Envoi de notifications Push vers Android avec Azure Notification Hubs et GCM).

Ce didacticiel montre comment utiliser Azure Notification Hubs et Cloud Messaging pour envoyer des notifications Push vers une application Android. Vous allez créer une application Android vide qui reçoit des notifications Push à l’aide de Firebase Cloud Messaging (FCM).



[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Le code complet de ce didacticiel peut être téléchargé depuis GitHub [ici](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).


##Configuration requise

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

- En plus du compte Azure actif mentionné ci-dessus, ce didacticiel requiert la dernière version d’[Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).
- Android 2.3 ou ultérieure pour Firebase Cloud Messaging.
- La révision 27 ou ultérieure du référentiel Google est requise pour Firebase Cloud Messaging.
- Services Google Play 9.0.2 ou version ultérieure pour Firebase Cloud Messaging.
- Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Android.


##Créer un nouveau projet Android Studio

1. Dans Android Studio, démarrez un nouveau projet Android Studio.

   	![Android Studio - nouveau projet](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)

2. Choisissez le format **Phone and Tablet** et le kit de développement logiciel (SDK) minimal (**Minimum SDK**) que vous souhaitez prendre en charge. Cliquez ensuite sur **Next**.

   	![Android Studio - workflow de création de projet](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)

3. Choisissez **Activité vide** comme activité principale, cliquez sur **Suivant**, puis sur **Terminer**.


##Créer un projet qui prend en charge Firebase Cloud Messaging

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]


##Configurer un nouveau hub de notification

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. Dans le panneau **Paramètres** de votre hub de notification, sélectionnez **Services de notification**, puis **Google (GCM)**. Entrez la clé du serveur FCM que vous avez précédemment copiée à partir de la [console Firebase](https://firebase.google.com/console/) et cliquez sur **Enregistrer**.

&emsp;&emsp;![Azure Notification Hubs - Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

Votre hub de notification est à présent configuré pour fonctionner avec Firebase Cloud Messaging, et vous disposez des chaînes de connexion vous permettant d’inscrire votre application pour la réception et l’envoi de notifications Push.

##<a id="connecting-app"></a>Connexion de votre application au hub de notification

###Ajout de services Google Play au projet

[AZURE.INCLUDE [Ajout de services Google Play](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Ajout de bibliothèques de concentrateurs de notification Azure


1. Dans le fichier `Build.Gradle` de l’**application**, dans la section **dépendances**, ajoutez les lignes suivantes.

		compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
		compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. Ajoutez le référentiel suivant après la section **dépendances**.

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

### Mise à jour du fichier AndroidManifest.xml.


1. Pour prendre en charge FCM, nous devons implémenter un service d’écoute d’ID d’instance dans notre code afin d’[obtenir des jetons d’inscription](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) à l’aide de l’[API FirebaseInstanceId Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). Dans ce didacticiel, nous nommerons la classe `MyInstanceIDService`.
 
	Ajoutez la définition de service suivante au fichier AndroidManifest.xml, dans la balise `<application>`.

        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>



2. Une fois que nous avons reçu notre jeton d’inscription FCM de la part de l’API FirebaseInstanceId, nous l’utiliserons pour nous [inscrire auprès d’Azure Notification Hub](notification-hubs-push-notification-registration-management.md). Nous effectuerons cette inscription en arrière-plan à l’aide d’un élément `IntentService` nommé `RegistrationIntentService`. Ce service gère également l’actualisation de notre jeton d’inscription FCM.
 
	Ajoutez la définition de service suivante au fichier AndroidManifest.xml, dans la balise `<application>`.

        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>



3. Nous allons également définir le destinataire qui recevra les notifications. Ajoutez la définition de destinataire suivante au fichier AndroidManifest.xml, dans la balise `<application>`. Remplacez l’espace réservé `<your package>` par le nom de votre package, qui apparaît en haut du fichier `AndroidManifest.xml`.

		<receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
		    android:permission="com.google.android.c2dm.permission.SEND">
		    <intent-filter>
		        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
		        <category android:name="<your package name>" />
		    </intent-filter>
		</receiver>



4. Ajoutez les autorisations nécessaires liées à FCM sous la balise `</application>`. Veillez à remplacer `<your package>` par le nom du package qui apparaît en haut du fichier `AndroidManifest.xml`.

	Pour plus d’informations sur ces autorisations, consultez [Setup a GCM Client app for Android](https://developers.google.com/cloud-messaging/android/client#manifest) (Configuration d’une application cliente GCM pour Android) et [Migrate a GCM Client App for Android to Firebase Cloud Messaging](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm) (Migration d’une application cliente GCM pour Android vers Firebase Cloud Messaging).

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />


### Ajout de code


1. Dans la vue du projet, développez **app** -> **src** -> **main** -> **java**. Cliquez avec le bouton droit sur le dossier de votre package sous **java**, cliquez sur **Nouveau**, puis cliquez sur **Classe Java**. Ajoutez une nouvelle classe nommée `NotificationSettings`.

	![Android Studio - nouvelle classe Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)

	Veillez à mettre à jour ces trois espaces réservés dans le code suivant pour la classe `NotificationSettings` :
	* **SenderId** : Id d’expéditeur obtenu précédemment dans l’onglet **Cloud Messaging** des paramètres de votre projet dans la [console Firebase](https://firebase.google.com/console/).
	* **HubListenConnectionString** : chaîne de connexion **DefaultListenAccessSignature** de votre hub. Vous pouvez copier cette chaîne de connexion en cliquant sur **Stratégies d’accès** dans le panneau **Paramètres** de votre hub sur le [portail Azure].
	* **HubName** : utilisez le nom de votre hub de notification qui s’affiche dans le panneau Hub sur le [portail Azure].

	`NotificationSettings` code :

		public class NotificationSettings {
		    public static String SenderId = "<Your project number>";
		    public static String HubName = "<Your HubName>";
		    public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
		}

2. À l’aide de la procédure ci-dessus, ajoutez une autre nouvelle classe nommée `MyInstanceIDService`. Il s'agira de notre implémentation de service d'écouteur d'ID d'instance.

	Le code de cette classe appellera notre `IntentService` pour [actualiser le jeton FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) en arrière-plan.

		import android.content.Intent;
		import android.util.Log;
		import com.google.firebase.iid.FirebaseInstanceIdService;
		
		
		public class MyInstanceIDService extends FirebaseInstanceIdService {
		
		    private static final String TAG = "MyInstanceIDService";
		
		    @Override
		    public void onTokenRefresh() {
		
		        Log.d(TAG, "Refreshing GCM Registration Token");
		
		        Intent intent = new Intent(this, RegistrationIntentService.class);
		        startService(intent);
		    }
		};


3. Ajoutez à votre projet une autre nouvelle classe nommée `RegistrationIntentService`. Il s’agira de l’implémentation de notre `IntentService` qui gérera [l’actualisation du jeton FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) et [l’inscription auprès du hub de notification](notification-hubs-push-notification-registration-management.md).

	Utilisez le code suivant pour cette classe.

		import android.app.IntentService;
		import android.content.Intent;
		import android.content.SharedPreferences;
		import android.preference.PreferenceManager;
		import android.util.Log;		
		import com.google.firebase.iid.FirebaseInstanceId;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class RegistrationIntentService extends IntentService {
		
		    private static final String TAG = "RegIntentService";
		
		    private NotificationHub hub;
		
		    public RegistrationIntentService() {
		        super(TAG);
		    }
		
		    @Override
		    protected void onHandleIntent(Intent intent) {
		
		        SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
		        String resultString = null;
		        String regID = null;
		        String storedToken = null;
		
		        try {
		            String FCM_token = FirebaseInstanceId.getInstance().getToken();
		            Log.d(TAG, "FCM Registration Token: " + FCM_token);
		
		            // Storing the registration id that indicates whether the generated token has been
		            // sent to your server. If it is not stored, send the token to your server,
		            // otherwise your server should have already received the token.
		            if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
		                regID = hub.register(FCM_token).getRegistrationId();
		
		                // If you want to use tags...
		                // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
		                // regID = hub.register(token, "tag1,tag2").getRegistrationId();
		
		                resultString = "New NH Registration Successfully - RegId : " + regID;
		                Log.d(TAG, resultString);
		
		                sharedPreferences.edit().putString("registrationID", regID ).apply();
		                sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
		            }
		
		            // Check if the token may have been compromised and needs refreshing.
		            else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
		                regID = hub.register(FCM_token).getRegistrationId();
		
		                // If you want to use tags...
		                // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
		                // regID = hub.register(token, "tag1,tag2").getRegistrationId();
		
		                resultString = "New NH Registration Successfully - RegId : " + regID;
		                Log.d(TAG, resultString);
		
		                sharedPreferences.edit().putString("registrationID", regID ).apply();
		                sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
		            }
		
		            else {
		                resultString = "Previously Registered Successfully - RegId : " + regID;
		            }
		        } catch (Exception e) {
		            Log.e(TAG, resultString="Failed to complete registration", e);
		            // If an exception happens while fetching the new token or updating our registration data
		            // on a third-party server, this ensures that we'll attempt the update at a later time.
		        }
		
		        // Notify UI that registration has completed.
		        if (MainActivity.isVisible) {
		            MainActivity.mainActivity.ToastNotify(resultString);
		        }
		    }
		}


		
4. Dans votre classe `MainActivity`, ajoutez les instructions `import` suivantes au-dessus de la déclaration de la classe.

		import com.google.android.gms.common.ConnectionResult;
		import com.google.android.gms.common.GoogleApiAvailability;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.content.Intent;
		import android.util.Log;
		import android.widget.TextView;
		import android.widget.Toast;

5. Ajoutez les membres privés suivants dans la partie supérieure de la classe. Nous les utiliserons pour [vérifier la disponibilité des services Google Play comme recommandé par Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

	    public static MainActivity mainActivity;
    	public static Boolean isVisible = false;	
	    private static final String TAG = "MainActivity";
	    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. Dans votre classe `MainActivity`, ajoutez la méthode suivante à la disponibilité des services Google Play.

	    /**
	     * Check the device to make sure it has the Google Play Services APK. If
	     * it doesn't, display a dialog that allows users to download the APK from
	     * the Google Play Store or enable it in the device's system settings.
	     */
	    private boolean checkPlayServices() {
	        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
	        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
	        if (resultCode != ConnectionResult.SUCCESS) {
	            if (apiAvailability.isUserResolvableError(resultCode)) {
	                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
	                        .show();
	            } else {
	                Log.i(TAG, "This device is not supported by Google Play Services.");
	                ToastNotify("This device is not supported by Google Play Services.");
	                finish();
	            }
	            return false;
	        }
	        return true;
	    }


7. Dans votre classe `MainActivity`, ajoutez le code suivant pour rechercher les services Google Play avant d’appeler votre `IntentService` pour obtenir votre jeton d’inscription FCM et vous inscrire auprès de votre hub de notification.

	    public void registerWithNotificationHubs()
	    {
	        if (checkPlayServices()) {
	            // Start IntentService to register this application with FCM.
	            Intent intent = new Intent(this, RegistrationIntentService.class);
	            startService(intent);
	        }
	    }


8. Dans la méthode `OnCreate` de la classe `MainActivity`, ajoutez le code suivant pour lancer le processus d’inscription lorsque l’activité est créée.

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        mainActivity = this;
	        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
	        registerWithNotificationHubs();
	    }


9. Ajoutez ces méthodes supplémentaires à `MainActivity` pour vérifier l’état de l’application et afficher un rapport dans votre application.

	    @Override
	    protected void onStart() {
	        super.onStart();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onPause() {
	        super.onPause();
	        isVisible = false;
	    }
	
	    @Override
	    protected void onResume() {
	        super.onResume();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onStop() {
	        super.onStop();
	        isVisible = false;
	    }
	
	    public void ToastNotify(final String notificationMessage) {
	        runOnUiThread(new Runnable() {
	            @Override
	            public void run() {
	                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
	                TextView helloText = (TextView) findViewById(R.id.text_hello);
	                helloText.setText(notificationMessage);
	            }
	        });
	    }


10. La méthode `ToastNotify` utilise le contrôle *« Hello World »* `TextView` pour afficher en permanence un rapport d’état et des notifications dans l’application. Dans votre disposition activity\_main.xml, ajoutez l'ID suivant pour ce contrôle.

        android:id="@+id/text_hello"

11. Nous allons maintenant ajouter une sous-classe pour le destinataire que nous avons défini dans le fichier AndroidManifest.xml. Ajoutez à votre projet une autre nouvelle classe nommée `MyHandler`.

12. Ajoutez les instructions d’importation suivantes au-dessus de `MyHandler.java` :

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.media.RingtoneManager;
		import android.net.Uri;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Ajoutez le code suivant pour la classe `MyHandler` afin d’en faire une sous-classe de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

	Comme ce code remplace la méthode `OnReceive`, le gestionnaire rapporte les notifications reçues. Le gestionnaire envoie également la notification Push au gestionnaire de notifications Android en utilisant la méthode `sendNotification()`. La méthode `sendNotification()` doit être exécutée quand l’application n’est pas en cours d’exécution et qu’une notification est reçue.

		public class MyHandler extends NotificationsHandler {
		    public static final int NOTIFICATION_ID = 1;
		    private NotificationManager mNotificationManager;
		    NotificationCompat.Builder builder;
		    Context ctx;
		
		    @Override
		    public void onReceive(Context context, Bundle bundle) {
		        ctx = context;
		        String nhMessage = bundle.getString("message");
		        sendNotification(nhMessage);
		        if (MainActivity.isVisible) {
		            MainActivity.mainActivity.ToastNotify(nhMessage);
		        }
		    }
		
		    private void sendNotification(String msg) {
		
		        Intent intent = new Intent(ctx, MainActivity.class);
		        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
		
		        mNotificationManager = (NotificationManager)
		                ctx.getSystemService(Context.NOTIFICATION_SERVICE);
		
		        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		                intent, PendingIntent.FLAG_ONE_SHOT);
		
		        Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
		        NotificationCompat.Builder mBuilder =
		                new NotificationCompat.Builder(ctx)
		                        .setSmallIcon(R.mipmap.ic_launcher)
		                        .setContentTitle("Notification Hub Demo")
		                        .setStyle(new NotificationCompat.BigTextStyle()
		                                .bigText(msg))
		                        .setSound(defaultSoundUri)
		                        .setContentText(msg);
		
		        mBuilder.setContentIntent(contentIntent);
		        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		    }
		}


14. Dans Android Studio, sur la barre de menus, cliquez sur **Build** (Build) -> **Rebuild Project** (Régénérer le projet) pour vous assurer que votre code ne contient aucune erreur.
15. Exécutez l’application sur votre appareil et vérifiez qu’il s’inscrit correctement auprès du hub de notification.

	> [AZURE.NOTE] L’inscription peut échouer lors du démarrage initial jusqu’à ce que la méthode `onTokenRefresh()` du service d’ID d’instance soit appelé. L’actualisation doit initier une inscription réussie auprès du hub de notification.

##Envoi de notifications Push

Vous pouvez tester la réception de notifications Push dans votre application en les envoyant via le [portail Azure] \(recherchez la section **Dépannage** dans le panneau Hub, comme illustré ci-dessous).

![Azure Notification Hubs - Test d’envoi](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## (Facultatif) Envoyer des notifications Push directement depuis l’application

>[AZURE.IMPORTANT] Cet exemple d’envoi de notifications à partir de l’application cliente est fourni à des fins d’apprentissage uniquement. Étant donné que cette opération exige la présence de la `DefaultFullSharedAccessSignature` sur l’application cliente, cela implique le risque pour votre hub de notification qu’un utilisateur puisse y accéder pour envoyer des notifications non autorisées à vos clients.

En règle générale, vous devez envoyer des notifications à l'aide d'un serveur principal. Dans la plupart des cas, vous chercherez peut-être à envoyer des notifications Push directement à partir de l’application cliente. Cette section explique comment envoyer des notifications à partir du client avec [l’API REST d’Azure Notification Hub](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. Dans la vue de projet Android Studio, développez **App** -> **src** -> **main** -> **res** -> **layout**. Ouvrez le fichier de disposition `activity_main.xml` et cliquez sur l’onglet **Texte** pour mettre à jour le texte du fichier. Mettez-le à jour avec le code suivant, qui ajoute de nouveaux contrôles `Button` et `EditText` pour l’envoi de messages de notification Push au hub de notification. Ajoutez ce code en bas, juste avant `</RelativeLayout>`.

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Dans la vue de projet Android Studio, développez **App** -> **src** -> **main** -> **res** -> **values**. Ouvrez le fichier `strings.xml` et ajoutez les valeurs de chaîne référencées par les nouveaux contrôles `Button` et `EditText`. Ajoutez-les en bas du fichier juste avant `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. Dans votre fichier `NotificationSetting.java`, ajoutez le paramètre suivant à la classe `NotificationSettings`.

	Mettez à jour `HubFullAccess` avec la chaîne de connexion **DefaultFullSharedAccessSignature** correspondant à votre hub. Vous pouvez copier cette chaîne de connexion depuis le [portail Azure] en cliquant sur **Stratégies d’accès** dans le panneau **Paramètres** de votre hub de notification.

		public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";

4. Dans votre fichier `MainActivity.java`, ajoutez les instructions `import` suivantes au-dessus de la classe `MainActivity`.

		import java.io.BufferedOutputStream;
		import java.io.BufferedReader;
		import java.io.InputStreamReader;
		import java.io.OutputStream;
		import java.net.HttpURLConnection;
		import java.net.URL;
		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;
		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

6. Dans votre fichier `MainActivity.java`, ajoutez les membres suivants au-dessus de la classe `MainActivity`.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;

6. Vous devez créer un jeton SaS (Software Access Signature) pour authentifier une demande POST d'envoi de messages à votre hub de notification. Cette opération est effectuée grâce à l’analyse des données clés de la chaîne de connexion, puis en créant le jeton SaS, comme indiqué sur la page [Concepts courants](http://msdn.microsoft.com/library/azure/dn495627.aspx) des informations de référence sur l’API REST. Le code suivant est un exemple d'implémentation.

	Dans `MainActivity.java`, ajoutez la méthode suivante à la classe `MainActivity` pour analyser votre chaîne de connexion.

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
	     * to parse the connection string so a SaS authentication token can be
	     * constructed.
	     *
	     * @param connectionString This must be the DefaultFullSharedAccess connection
	     *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);
	
	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }


7. Dans `MainActivity.java`, ajoutez la méthode suivante à la classe `MainActivity` pour créer un jeton d’authentification SaS.

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
	     * construct a SaS token from the access key to authenticate a request.
	     *
	     * @param uri The unencoded resource URI string for this operation. The resource
	     *            URI is the full URI of the Service Bus resource to which access is
	     *            claimed. For example,
	     *            "http://<namespace>.servicebus.windows.net/<hubName>"
	     */
	    private String generateSasToken(String uri) {
	
	        String targetUri;
	        String token = null;
	        try {
	            targetUri = URLEncoder
	                    .encode(uri.toString().toLowerCase(), "UTF-8")
	                    .toLowerCase();
	
	            long expiresOnDate = System.currentTimeMillis();
	            int expiresInMins = 60; // 1 hour
	            expiresOnDate += expiresInMins * 60 * 1000;
	            long expires = expiresOnDate / 1000;
	            String toSign = targetUri + "\n" + expires;
	
	            // Get an hmac_sha1 key from the raw key bytes
	            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
	            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
	
	            // Get an hmac_sha1 Mac instance and initialize with the signing key
	            Mac mac = Mac.getInstance("HmacSHA256");
	            mac.init(signingKey);
	
	            // Compute the hmac on input data bytes
	            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
	
	            // Using android.util.Base64 for Android Studio instead of
	            // Apache commons codec
	            String signature = URLEncoder.encode(
	                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
	
	            // Construct authorization string
	            token = "SharedAccessSignature sr=" + targetUri + "&sig="
	                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
	        } catch (Exception e) {
	            if (isVisible) {
	                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
	            }
	        }
	
	        return token;
	    }



8. Dans `MainActivity.java`, ajoutez la méthode suivante à la classe `MainActivity` pour gérer le clic sur le bouton **Envoyer une notification** et envoyer le message de notification Push au hub à l’aide de l’API REST intégrée.

	    /**
	     * Send Notification button click handler. This method parses the
	     * DefaultFullSharedAccess connection string and generates a SaS token. The
	     * token is added to the Authorization header on the POST request to the
	     * notification hub. The text in the editTextNotificationMessage control
	     * is added as the JSON body for the request to add a GCM message to the hub.
	     *
	     * @param v
	     */
	    public void sendNotificationButtonOnClick(View v) {
	        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
	        final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";
	
	        new Thread()
	        {
	            public void run()
	            {
	                try
	                {
	                    // Based on reference documentation...
	                    // http://msdn.microsoft.com/library/azure/dn223273.aspx
	                    ParseConnectionString(NotificationSettings.HubFullAccess);
	                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
	                            "/messages/?api-version=2015-01");
	
	                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
	
	                    try {
	                        // POST request
	                        urlConnection.setDoOutput(true);
	
	                        // Authenticate the POST request with the SaS token
	                        urlConnection.setRequestProperty("Authorization", 
								generateSasToken(url.toString()));
	
	                        // Notification format should be GCM
	                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
	
	                        // Include any tags
	                        // Example below targets 3 specific tags
	                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
	                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
							//		"tag1 || tag2 || tag3");
	
	                        // Send notification message
	                        urlConnection.setFixedLengthStreamingMode(json.length());
	                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
	                        bodyStream.write(json.getBytes());
	                        bodyStream.close();
	
	                        // Get reponse
	                        urlConnection.connect();
	                        int responseCode = urlConnection.getResponseCode();
	                        if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
	                        }
	                    } finally {
	                        urlConnection.disconnect();
	                    }
	                }
	                catch(Exception e)
	                {
	                    if (isVisible) {
	                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
	                    }
	                }
	            }
	        }.start();
	    }



##Test de votre application

####Notifications Push dans l’émulateur

Si vous souhaitez effectuer les tests de notifications Push sur un émulateur, vérifiez que votre image d’émulateur prend en charge le niveau d’API Google que vous avez choisi pour votre application. Si votre image ne prend pas en charge les API Google natives, l’exception **SERVICE\_NOT\_AVAILABLE** est levée.

Parallèlement à cela, assurez-vous que vous avez ajouté votre compte Google à l’émulateur en cours d’exécution sous **Paramètres**->**Comptes**. Sinon, vos tentatives d'inscription auprès de GCM peuvent entraîner la levée de l'exception **AUTHENTICATION\_FAILED**.

####Exécution de l'application

1. Exécutez l’application et notez qu’un ID d’inscription apparaît quand l’inscription réussit.

   	![Tests sur Android - Inscription de canal](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)

2. Entrez le message de notification à envoyer à tous les appareils Android inscrits auprès du hub.

   	![Tests sur Android - envoi d’un message](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)

3. Appuyez sur **Envoyer une notification**. Tous les appareils sur lesquels l’application est en cours d’exécution affichent une instance `AlertDialog` comportant le message de notification Push. Les appareils sur lesquels l’application n’est pas en cours d’exécution, mais qui ont déjà été inscrits aux notifications Push, reçoivent une notification dans le gestionnaire de notifications Android. Vous pouvez afficher ces notifications en effectuant un balayage vers le bas depuis l’angle supérieur gauche.

   	![Tests sur Android - notifications](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

##Étapes suivantes

Nous vous recommandons de consulter le didacticiel [Utiliser Notification Hubs pour envoyer des notifications Push aux utilisateurs] comme prochaine étape. Il vous expliquera comment envoyer des notifications à partir d’un serveur principal ASP.NET en utilisant des balises pour cibler des utilisateurs spécifiques.

Pour segmenter vos utilisateurs par groupes d’intérêt, consultez le didacticiel [Utilisation des Notification Hubs pour diffuser les dernières nouvelles].

Pour obtenir des informations générales sur Notification Hubs, consultez nos [Recommandations relatives à Notification Hubs].

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Recommandations relatives à Notification Hubs]: notification-hubs-push-notification-overview.md
[Utiliser Notification Hubs pour envoyer des notifications Push aux utilisateurs]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[portail Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0907_2016-->