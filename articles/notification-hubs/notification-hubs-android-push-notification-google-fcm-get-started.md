---
title: Wysyłanie powiadomień wypychanych do urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i Firebase Cloud Messaging | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak za pomocą usług Azure Notification Hubs i Firebase Cloud Messaging wysyłać powiadomienia wypychane do urządzeń z systemem Android.
services: notification-hubs
documentationcenter: android
keywords: powiadomienia wypychane, powiadomienie wypychane, powiadomienia wypychane w systemie android, firebase cloud messaging
author: wesmc7777
manager: erikre
editor: ''

ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 07/14/2016
ms.author: wesmc

---
# Wysyłanie powiadomień wypychanych do urządzeń z systemem Android przy użyciu usługi Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## Omówienie
> [!IMPORTANT]
> W tym temacie przedstawiono powiadomienia wypychane za pomocą usługi Google Firebase Cloud Messaging (FCM). Jeśli nadal używasz usługi Google Cloud Messaging (GCM), zobacz [Wysyłanie powiadomień wypychanych do urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i GCM](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji dla systemu Android przy użyciu usług Azure Notification Hubs i Firebase Cloud Messaging.
Utworzysz pustą aplikację dla systemu Android służącą do odbierania powiadomień wypychanych przy użyciu usługi Firebase Cloud Messaging (FCM).

[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Kompletny kod dla tego samouczka można pobrać z witryny GitHub [tutaj](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).

## Wymagania wstępne
> [!IMPORTANT]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).
> 
> 

* Oprócz aktywnego konta platformy Azure wspomnianego powyżej w tym samouczku wymagana jest najnowsza wersja programu [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).
* System Android 2.3 lub nowszy dla usługi Firebase Cloud Messaging.
* Usługa Google Repository w wersji 27 lub nowszej jest wymagana dla usługi Firebase Cloud Messaging.
* Usługi Google Play Services 9.0.2 lub nowsze dla usługi Firebase Cloud Messaging.
* Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Notification Hubs dotyczących aplikacji dla systemu Android.

## Tworzenie nowego projektu programu Android Studio
1. W programie Android Studio utwórz nowy projekt programu Android Studio.
   
    ![Android Studio — nowy projekt](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)
2. Wybierz kształt **Phone and Tablet** (Telefon i tablet) oraz odpowiednią wersję z listy **Minimum SDK** (Minimalna wersja zestawu SDK), która ma być obsługiwana. Następnie kliknij przycisk **Next** (Dalej).
   
    ![Android Studio — przepływ pracy tworzenia projektu](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)
3. Wybierz pozycję **Empty Activity** (Puste działanie) dla głównego działania i kliknij przycisk **Next** (Dalej), a następnie kliknij przycisk **Finish** (Zakończ).

## Tworzenie projektu obsługującego usługę Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## Konfigurowanie nowego centrum powiadomień
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. W bloku **Ustawienia** centrum powiadomień wybierz pozycję **Usługi powiadomień**, a następnie wybierz pozycję **Google (GCM)**. Wprowadź klucz serwera FCM skopiowany wcześniej z [konsoli Firebase](https://firebase.google.com/console/) i kliknij pozycję **Zapisz**.

&emsp;&emsp;![Azure Notification Hubs — Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

Twoje centrum powiadomień jest teraz skonfigurowane do pracy z usługą Firebase Cloud Messaging i uzyskano parametry połączenia do rejestrowania aplikacji w celu odbierania i wysyłania powiadomień wypychanych.

## <a id="connecting-app"></a>Łączenie aplikacji z centrum powiadomień
### Dodawanie usług Google Play do projektu
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### Dodawanie bibliotek usługi Azure Notification Hubs
1. W pliku `Build.Gradle` dla **aplikacji** dodaj następujące wiersze w sekcji **dependencies**.
   
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
2. Dodaj następujące repozytorium po sekcji **dependencies**.
   
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### Aktualizowanie pliku AndroidManifest.xml
1. W celu zapewnienia obsługi usługi FCM musimy zaimplementować w naszym kodzie wystąpienie usługi odbiornika interfejsu Instance ID, który służy do [uzyskiwania tokenów rejestracji](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) przy użyciu [interfejsu API FirebaseInstanceId firmy Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). W tym samouczku nazwiemy tę klasę `MyInstanceIDService`. 
   
    Dodaj następującą definicję usługi do pliku AndroidManifest.xml wewnątrz tagu `<application>`. 
   
        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>
2. Po uzyskaniu tokenu rejestracji usługi FCM z interfejsu API FirebaseInstanceId użyjemy go do [rejestracji w usłudze Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Zapewnimy obsługę rejestracji w tle przy użyciu klasy `IntentService` o nazwie `RegistrationIntentService`. Ta usługa będzie również odpowiadać za odświeżanie naszego tokenu rejestracji usługi FCM.
   
    Dodaj następującą definicję usługi do pliku AndroidManifest.xml wewnątrz tagu `<application>`. 
   
        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>
3. Zdefiniujemy również odbiornik, aby otrzymywać powiadomienia. Dodaj następującą definicję odbiornika do pliku AndroidManifest.xml wewnątrz tagu `<application>`. Zastąp symbol zastępczy `<your package>` rzeczywistą nazwą pakietu wyświetlaną u góry pliku `AndroidManifest.xml`.
   
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
4. Dodaj następujące wymagane uprawnienia powiązane z usługą FCM poniżej tagu `</application>`. Zastąp wartość `<your package>` nazwą pakietu wyświetlaną u góry pliku `AndroidManifest.xml`.
   
    Aby uzyskać więcej informacji na temat tych uprawnień, zobacz [Setup a GCM Client app for Android](https://developers.google.com/cloud-messaging/android/client#manifest) (Konfigurowanie aplikacji klienckiej usługi GCM dla systemu Android) i [Migrate a GCM Client App for Android to Firebase Cloud Messaging](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm) (Migrowanie aplikacji klienckiej usługi GCM dla systemu Android do usługi Firebase Cloud Messaging).
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

### Dodawanie kodu
1. W widoku projektu rozwiń węzeł **app** > **src** > **main** > **java**. Kliknij prawym przyciskiem folder pakietu w węźle **java**, kliknij pozycję **New** (Nowy), a następnie kliknij pozycję **Java Class** (Klasa Java). Dodaj nową klasę o nazwie `NotificationSettings`. 
   
    ![Android Studio – nowa klasa Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)
   
    Zaktualizuj te trzy symbole zastępcze w poniższym kodzie dla klasy `NotificationSettings`:
   
   * **SenderId**: identyfikator nadawcy uzyskany wcześniej na karcie **Cloud Messaging** w ustawieniach projektu w [konsoli Firebase](https://firebase.google.com/console/).
   * **HubListenConnectionString**: domyślne parametry połączenia **DefaultListenAccessSignature** dla centrum. Możesz skopiować te parametry połączenia, klikając pozycję **Zasady dostępu** w bloku **Ustawienia** centrum w witrynie [Azure Portal].
   * **HubName**: użyj nazwy centrum powiadomień wyświetlanej w bloku centrum w witrynie [Azure Portal].
     
     `NotificationSettings` — kod:
     
       public class NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
       }
2. Przy użyciu powyższych kroków dodaj nową klasę o nazwie `MyInstanceIDService`. Będzie to nasza implementacja usługi odbiornika interfejsu Instance ID.
   
    Kod dla tej klasy będzie wywoływać naszą klasę `IntentService` w celu [odświeżenia tokenu usługi FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) w tle.
   
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


1. Dodaj kolejną nową klasę o nazwie `RegistrationIntentService` do projektu. Będzie to implementacja naszej klasy `IntentService`, która będzie obsługiwać [odświeżanie tokenu usługi FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i [rejestrowanie w centrum powiadomień](notification-hubs-push-notification-registration-management.md).
   
    Użyj następującego kodu dla tej klasy.
   
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
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
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
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
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
2. W klasie `MainActivity` dodaj następujące instrukcje `import` nad deklaracją klasy.
   
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;
3. Dodaj następujące prywatne elementy członkowskie u góry klasy. Będą one używane do [sprawdzania dostępności usług Google Play zgodnie z zaleceniami firmy Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).
   
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
4. W klasie `MainActivity` dodaj następującą metodę do sprawdzania dostępności usług Google Play. 
   
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
5. W klasie `MainActivity` dodaj następujący kod, który będzie sprawdzać dostępność usług Google Play przed wywołaniem klasy `IntentService` w celu pobrania tokenu rejestracji FCM i rejestracji w centrum powiadomień.
   
        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }
6. W metodzie `OnCreate` klasy `MainActivity` dodaj następujący kod w celu uruchomienia procesu rejestracji po utworzeniu działania.
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
7. Dodaj te dodatkowe metody do klasy `MainActivity`, aby zweryfikować stan aplikacji i zgłosić stan w aplikacji.
   
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
8. Metoda `ToastNotify` używa kontrolki *"Hello World"* `TextView` do trwałego zgłaszania stanu i powiadomień w aplikacji. W układzie activity_main.xml dodaj następujący identyfikator dla tej kontrolki.
   
       android:id="@+id/text_hello"
9. Następnie dodamy podklasę dla naszego odbiornika zdefiniowanego w pliku AndroidManifest.xml. Dodaj kolejną nową klasę o nazwie `MyHandler` do projektu.
10. Dodaj następujące instrukcje import u góry pliku `MyHandler.java`:
    
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
11. Dodaj następujący kod do klasy `MyHandler`, aby stała się podklasą klasy `com.microsoft.windowsazure.notifications.NotificationsHandler`.
    
    Ten kod zastępuje metodę `OnReceive`, dlatego program obsługi będzie zgłaszać odbierane powiadomienia. Program obsługi wysyła również powiadomienie wypychane do menedżera powiadomień systemu Android za pomocą metody `sendNotification()`. Metoda `sendNotification()` powinna być wykonywana, gdy aplikacja nie jest uruchomiona i otrzymano powiadomienie.
    
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
12. W programie Android Studio na pasku menu kliknij pozycję **Build** > **Rebuild Project** (Kompiluj — Kompiluj ponownie projekt), aby upewnić się, że w kodzie nie występują żadne błędy.
13. Uruchom aplikację na urządzeniu i sprawdź, czy pomyślnie rejestruje się ona w centrum powiadomień. 
    
    > [!NOTE]
    > Rejestracja podczas początkowego uruchamiania może kończyć się niepowodzeniem, dopóki nie zostanie wywołana metoda `onTokenRefresh()` usługi identyfikatora wystąpienia. Odświeżenie powinno zainicjować pomyślną rejestrację w centrum powiadomień.
    > 
    > 

## Wysyłanie powiadomień wypychanych
Odbieranie powiadomień wypychanych w aplikacji możesz przetestować, wysyłając je z poziomu witryny [Azure Portal] za pośrednictwem sekcji **Rozwiązywanie problemów** w bloku centrum, jak przedstawiono poniżej.

![Azure Notification Hubs — Wysyłanie testowe](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## (Opcjonalnie) Wysyłanie powiadomień wypychanych bezpośrednio z poziomu aplikacji
> [!IMPORTANT]
> Ten przykład wysyłania powiadomień z aplikacji klienckiej został podany tylko dla celów szkoleniowych. Ponieważ wymaga to obecności `DefaultFullSharedAccessSignature` w aplikacji klienckiej, naraża centrum powiadomień na ryzyko, że użytkownik może uzyskać dostęp do wysyłania nieautoryzowanych powiadomień do Twoich klientów.
> 
> 

Zwykle powiadomienia są wysyłane przy użyciu serwera zaplecza. W niektórych przypadkach warto wysyłać powiadomienia bezpośrednio z aplikacji klienta. W tej sekcji wyjaśniono sposób wysyłania powiadomień z klienta przy użyciu [interfejsu API REST usługi Azure Notification Hubs](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. W widoku projektu programu Android Studio rozwiń węzeł **App** > **src** > **main** > **res** > **layout**. Otwórz plik układu `activity_main.xml` i kliknij kartę **Text** (Tekst), aby zaktualizować zawartość tekstową pliku. Zaktualizuj go przy użyciu poniższego kodu, który dodaje nowe kontrolki `Button` i `EditText` służące do wysyłania komunikatów powiadomień wypychanych do centrum powiadomień. Dodaj ten kod w dolnej części bezpośrednio przed elementem `</RelativeLayout>`.
   
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
2. W widoku projektu programu Android Studio rozwiń węzeł **App** > **src** > **main** > **res** > **values**. Otwórz plik `strings.xml` i dodaj wartości ciągu, do których odwołują się nowe kontrolki `Button` i `EditText`. Dodaj je w dolnej części pliku, bezpośrednio przed elementem `</resources>`.
   
        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>
3. W pliku `NotificationSetting.java` dodaj następujące ustawienie do klasy `NotificationSettings`.
   
    Zaktualizuj element `HubFullAccess` przy użyciu parametrów połączenia **DefaultFullSharedAccessSignature** dla swojego centrum. Parametry połączenia można skopiować z witryny [Azure Portal], klikając pozycję **Zasady dostępu** w bloku **Ustawienia** centrum powiadomień.
   
        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";
4. W pliku `MainActivity.java` dodaj następujące instrukcje `import` nad klasą `MainActivity`.
   
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
5. W pliku `MainActivity.java` dodaj następujące elementy członkowskie u góry klasy `MainActivity`.  
   
        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;
6. Należy utworzyć token sygnatury dostępu współdzielonego w celu uwierzytelnienia żądania POST do wysyłania komunikatów do centrum powiadomień. W tym celu należy przeanalizować dane klucza z parametrów połączenia, a następnie utworzyć token sygnatury dostępu współdzielonego, jak określono w dokumentacji interfejsu API REST [Common Concepts](http://msdn.microsoft.com/library/azure/dn495627.aspx) (Wspólne pojęcia). Poniższy kod jest przykładem implementacji.
   
    W pliku `MainActivity.java` dodaj następującą metodę do klasy `MainActivity`, aby przeanalizować parametry połączenia.
   
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
7. W pliku `MainActivity.java` dodaj następującą metodę do klasy `MainActivity`, aby utworzyć token uwierzytelniania sygnatury dostępu współdzielonego.
   
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
8. W pliku `MainActivity.java` dodaj następującą metodę do klasy `MainActivity` w celu obsługi kliknięcia przycisku **Send Notification** (Wyślij powiadomienie) i wysłania komunikatu do centrum przy użyciu wbudowanego interfejsu API REST.
   
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
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
   
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
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //      "tag1 || tag2 || tag3");
   
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

## Testowanie aplikacji
#### Powiadomienia wypychane w emulatorze
Aby przetestować powiadomienia wypychane w emulatorze, upewnij się, że obraz emulatora obsługuje poziom interfejsu API Google wybrany dla aplikacji. Jeśli obraz nie obsługuje natywnych interfejsów API Google, wystąpi wyjątek **SERVICE\_NOT\_AVAILABLE** (usługa niedostępna).

Oprócz powyższego, upewnij się, że dodano konto Google do uruchomionego emulatora w obszarze **Settings** > **Accounts** (Ustawienia — Konta). W przeciwnym razie próby rejestracji w usłudze GCM mogą spowodować wystąpienie wyjątku **AUTHENTICATION\_FAILED** (uwierzytelnianie nie powiodło się).

#### Uruchamianie aplikacji
1. Uruchom aplikację i zwróć uwagę, czy identyfikator rejestracji jest zgłaszany, co oznacza pomyślną rejestrację.
   
    ![Testowanie w systemie Android — rejestracja kanału](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)
2. Wprowadź komunikat powiadomienia, który ma zostać wysłany do wszystkich urządzeń z systemem Android zarejestrowanych w centrum.
   
    ![Testowanie w systemie Android — wysyłanie komunikatu](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)
3. Naciśnij przycisk **Send Notification** (Wyślij powiadomienie). Na wszystkich urządzeniach, na których jest uruchomiona aplikacja, zostanie wyświetlone wystąpienie kontrolki `AlertDialog` z komunikatem powiadomienia wypychanego. Urządzenia, na których aplikacja nie jest uruchomiona, ale zostały wcześniej zarejestrowane do otrzymywania powiadomień wypychanych otrzymają powiadomienie w menedżerze powiadomień systemu Android. Można je wyświetlić, szybko przesuwając w dół od lewego górnego rogu.
   
    ![Testowanie w systemie Android — powiadomienia](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

## Następne kroki
Zalecanym następnym krokiem jest zapoznanie się z samouczkiem [Wysyłanie powiadomień wypychanych do użytkowników przy użyciu usługi Notification Hubs]. Przedstawiono w nim sposób wysyłania powiadomień z zaplecza programu ASP.NET przy użyciu tagów służących do adresowania powiadomień do określonych użytkowników.

Jeśli chcesz podzielić użytkowników na grupy zainteresowań, zobacz samouczek [Wysyłanie najważniejszych wiadomości przy użyciu usługi Notification Hubs].

Więcej ogólnych informacji o usłudze Notification Hubs zawiera temat [Wskazówki dotyczące usługi Notification Hubs].

<!-- Images. -->



<!-- URLs. -->
[Wprowadzenie do powiadomień wypychanych w usłudze Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Zestaw SDK usługi Mobile Services dla systemu Android]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Odwołanie do projektu biblioteki]: http://go.microsoft.com/fwlink/?LinkId=389800
[klasyczny portal Azure]: https://manage.windowsazure.com/
[Wskazówki dotyczące usługi Notification Hubs]: notification-hubs-push-notification-overview.md
[Wysyłanie powiadomień wypychanych do użytkowników przy użyciu usługi Notification Hubs]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Wysyłanie najważniejszych wiadomości przy użyciu usługi Notification Hubs]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com



<!--HONumber=Sep16_HO3-->


