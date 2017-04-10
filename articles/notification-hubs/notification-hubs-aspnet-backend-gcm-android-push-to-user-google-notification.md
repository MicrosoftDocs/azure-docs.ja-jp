---
title: "Azure Notification Hubs と .NET バックエンドによる Android ユーザーへの通知"
description: "Azure のユーザーにプッシュ通知を送信する方法について説明します。 コード サンプルは Android 向けJava で記述されています。"
documentationcenter: android
services: notification-hubs
author: ysxu
manager: erikre
editor: 
ms.assetid: ae0e17a8-9d2b-496e-afd2-baa151370c25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 418a4b638dfaa3fee33a7a7242433699205c79f7


---
# <a name="azure-notification-hubs-notify-users-for-android-with-net-backend"></a>Azure Notification Hubs と .NET バックエンドによる Android ユーザーへの通知
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>概要
Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。 このチュートリアルでは、Azure Notification Hubs を使用して特定のデバイスで特定のアプリケーション ユーザーにプッシュ通知を送信する方法について説明します。 [アプリ バックエンドからの登録管理](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)に関するガイダンス トピックに示すように、ASP.NET WebAPI バックエンドを使用してクライアントを認証し、通知を生成します。 このチュートリアルは、「[Notification Hubs の使用 (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md)」チュートリアルで作成した通知ハブが基になっています。

> [!NOTE]
> このチュートリアルでは、「 [Notification Hubs の使用 (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md)」での説明に従って通知が作成され、構成されていると想定しています。
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="create-the-android-project"></a>Android プロジェクトを作成する
次の手順では、Android アプリケーションを作成します。

1. 「 [Getting Started with Notification Hubs (Android) (Notification Hubs (Android) の使用)](notification-hubs-android-push-notification-google-gcm-get-started.md) 」チュートリアルに従って、アプリケーションを作成、構成して GCM からプッシュ通知を受け取ります。
2. **res/layout/activity_main.xml** ファイルを開き、コンテンツの定義を次と置き換えます。
   
    これでユーザーとしてログインするための新しい EditText コントロールが追加されます。 また、送信する通知の一部となるユーザー名のタグのフィールドが追加されます。
   
        <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
            android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
   
        <EditText
            android:id="@+id/usernameText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/usernameHint"
            android:layout_above="@+id/passwordText"
            android:layout_alignParentEnd="true" />
        <EditText
            android:id="@+id/passwordText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/passwordHint"
            android:inputType="textPassword"
            android:layout_above="@+id/buttonLogin"
            android:layout_alignParentEnd="true" />
        <Button
            android:id="@+id/buttonLogin"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/loginButton"
            android:onClick="login"
            android:layout_above="@+id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:layout_marginBottom="24dp" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="WNS on"
            android:textOff="WNS off"
            android:id="@+id/toggleButtonWNS"
            android:layout_toLeftOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="GCM on"
            android:textOff="GCM off"
            android:id="@+id/toggleButtonGCM"
            android:checked="true"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="APNS on"
            android:textOff="APNS off"
            android:id="@+id/toggleButtonAPNS"
            android:layout_toRightOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessageTag"
            android:layout_below="@id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_tag_hint" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessage"
            android:layout_below="@+id/editTextNotificationMessageTag"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_hint" />
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/send_button"
            android:id="@+id/sendbutton"
            android:onClick="sendNotificationButtonOnClick"
            android:layout_below="@+id/editTextNotificationMessage"
            android:layout_centerHorizontal="true" />
        </RelativeLayout>
3. **res/values/strings.xml** ファイルを開き、`send_button` の定義を `send_button` の文字列を再定義する次の行に置き換え、その他のコントロールの文字列を追加します。
   
        <string name="usernameHint">Username</string>
        <string name="passwordHint">Password</string>
        <string name="loginButton">1. Log in</string>
        <string name="send_button">2. Send Notification</string>
        <string name="notification_message_tag_hint">
            Recipient username tag
        </string>
   
    main_activity.xml の画像レイアウトは次のようになります。
   
    ![][A1]
4. `MainActivity` クラスと同じパッケージに **RegisterClient** という新しいクラスを作成します。 新しいクラス ファイルに次のコードを使用します。
   
        import java.io.IOException;
        import java.io.UnsupportedEncodingException;
        import java.util.Set;
   
        import org.apache.http.HttpResponse;
        import org.apache.http.HttpStatus;
        import org.apache.http.client.ClientProtocolException;
        import org.apache.http.client.HttpClient;
        import org.apache.http.client.methods.HttpPost;
        import org.apache.http.client.methods.HttpPut;
        import org.apache.http.client.methods.HttpUriRequest;
        import org.apache.http.entity.StringEntity;
        import org.apache.http.impl.client.DefaultHttpClient;
        import org.apache.http.util.EntityUtils;
        import org.json.JSONArray;
        import org.json.JSONException;
        import org.json.JSONObject;
   
        import android.content.Context;
        import android.content.SharedPreferences;
        import android.util.Log;
   
        public class RegisterClient {
            private static final String PREFS_NAME = "ANHSettings";
            private static final String REGID_SETTING_NAME = "ANHRegistrationId";
            private String Backend_Endpoint;
            SharedPreferences settings;
            protected HttpClient httpClient;
            private String authorizationHeader;
   
            public RegisterClient(Context context, String backendEnpoint) {
                super();
                this.settings = context.getSharedPreferences(PREFS_NAME, 0);
                httpClient =  new DefaultHttpClient();
                Backend_Endpoint = backendEnpoint + "/api/register";
            }
   
            public String getAuthorizationHeader() {
                return authorizationHeader;
            }
   
            public void setAuthorizationHeader(String authorizationHeader) {
                this.authorizationHeader = authorizationHeader;
            }
   
            public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
                String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
   
                JSONObject deviceInfo = new JSONObject();
                deviceInfo.put("Platform", "gcm");
                deviceInfo.put("Handle", handle);
                deviceInfo.put("Tags", new JSONArray(tags));
   
                int statusCode = upsertRegistration(registrationId, deviceInfo);
   
                if (statusCode == HttpStatus.SC_OK) {
                    return;
                } else if (statusCode == HttpStatus.SC_GONE){
                    settings.edit().remove(REGID_SETTING_NAME).commit();
                    registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                    statusCode = upsertRegistration(registrationId, deviceInfo);
                    if (statusCode != HttpStatus.SC_OK) {
                        Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                        throw new RuntimeException("Error upserting registration");
                    }
                } else {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            }
   
            private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                    throws UnsupportedEncodingException, IOException,
                    ClientProtocolException {
                HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
                request.setEntity(new StringEntity(deviceInfo.toString()));
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                request.addHeader("Content-Type", "application/json");
                HttpResponse response = httpClient.execute(request);
                int statusCode = response.getStatusLine().getStatusCode();
                return statusCode;
            }
   
            private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
                if (settings.contains(REGID_SETTING_NAME))
                    return settings.getString(REGID_SETTING_NAME, null);
   
                HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                HttpResponse response = httpClient.execute(request);
                if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                    throw new RuntimeException("Error creating Notification Hubs registrationId");
                }
                String registrationId = EntityUtils.toString(response.getEntity());
                registrationId = registrationId.substring(1, registrationId.length()-1);
   
                settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();
   
                return registrationId;
            }
        }
   
    プッシュ通知を登録するために、このコンポーネントはアプリケーション バックエンドにアクセスするのに必要な REST 呼び出しを実装します。 「 *アプリ バックエンドからの登録* 」で説明しているとおり、Notification Hubs によって作成された [registrationIds](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)もローカルに格納されます。 **[ログイン]** ボタンをクリックすると、ローカル ストレージに格納した認証トークンが使用されることに注意してください。
5. `MainActivity` クラスを削除するか、`NotificationHub` のプライベート フィールドをコメント アウトして、`RegisterClient` クラスのフィールドと、ASP.NET バックエンドのエンドポイントの文字列を追加します。 必ず、前に取得したバックエンド エンドポイントで `<Enter Your Backend Endpoint>` を置き換えてください。 たとえば、「 `http://mybackend.azurewebsites.net`」のように入力します。

        //private NotificationHub hub;
        private RegisterClient registerClient;
        private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";


1. `MainActivity` クラスの `onCreate` メソッドで、`hub` フィールドの初期化と `registerWithNotificationHubs` メソッドへのコールを削除するか、コメント アウトします。 その後 `RegisterClient` クラスのインスタンスを初期化するコードを追加します。 メソッドには次の行が含まれています。
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
   
            MyHandler.mainActivity = this;
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
            gcm = GoogleCloudMessaging.getInstance(this);
   
            //hub = new NotificationHub(HubName, HubListenConnectionString, this);
            //registerWithNotificationHubs();
   
            registerClient = new RegisterClient(this, BACKEND_ENDPOINT);
   
            setContentView(R.layout.activity_main);
        }
2. `MainActivity` クラスで、`registerWithNotificationHubs` メソッド全体を削除するかコメント アウトします。 このチュートリアルで使用しません。
3. **MainActivity.java** ファイルに次の `import` ステートメントを追加します。
   
        import android.widget.Button;
        import java.io.UnsupportedEncodingException;
        import android.content.Context;
        import java.util.HashSet;
        import android.widget.Toast;
        import org.apache.http.client.ClientProtocolException;
        import java.io.IOException;
        import org.apache.http.HttpStatus;
4. その後、 **[ログイン]** ボタン クリック イベントとプッシュ通知の送信を処理する次のメソッドを追加します。
   
        @Override
        protected void onStart() {
            super.onStart();
            Button sendPush = (Button) findViewById(R.id.sendbutton);
            sendPush.setEnabled(false);
        }
   
        public void login(View view) throws UnsupportedEncodingException {
            this.registerClient.setAuthorizationHeader(getAuthorizationHeader());
   
            final Context context = this;
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(SENDER_ID);
                        registerClient.register(regid, new HashSet<String>());
                    } catch (Exception e) {
                        DialogNotify("MainActivity - Failed to register", e.getMessage());
                        return e;
                    }
                    return null;
                }
   
                protected void onPostExecute(Object result) {
                    Button sendPush = (Button) findViewById(R.id.sendbutton);
                    sendPush.setEnabled(true);
                    Toast.makeText(context, "Logged in and registered.",
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
            return basicAuthHeader;
        }
   
        /**
         * This method calls the ASP.NET WebAPI backend to send the notification message
         * to the platform notification service based on the pns parameter.
         *
         * @param pns     The platform notification service to send the notification message to. Must
         *                be one of the following ("wns", "gcm", "apns").
         * @param userTag The tag for the user who will receive the notification message. This string
         *                must not contain spaces or special characters.
         * @param message The notification message string. This string must include the double quotes
         *                to be used as JSON content.
         */
        public void sendPush(final String pns, final String userTag, final String message)
                throws ClientProtocolException, IOException {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
   
                        String uri = BACKEND_ENDPOINT + "/api/notifications";
                        uri += "?pns=" + pns;
                        uri += "&to_tag=" + userTag;
   
                        HttpPost request = new HttpPost(uri);
                        request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                        request.setEntity(new StringEntity(message));
                        request.addHeader("Content-Type", "application/json");
   
                        HttpResponse response = new DefaultHttpClient().execute(request);
   
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            DialogNotify("MainActivity - Error sending " + pns + " notification",
                                response.getStatusLine().toString());
                            throw new RuntimeException("Error sending notification");
                        }
                    } catch (Exception e) {
                        DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                        return e;
                    }
   
                    return null;
                }
            }.execute(null, null, null);
        }

    **[ログイン]** ボタンに対する `login` ハンドラ―は、入力したユーザー名とパスワードを使用して基本的な認証トークンを生成し (これは認証スキームが使用する任意のトークンを表します)、`RegisterClient` を使用して登録用のバックエンドを呼び出します。

    `sendPush` メソッドでは、バックエンドを呼び出して、ユーザーのタグに基づいてユーザーにセキュリティて保護された通知をトリガーします。 `sendPush` がターゲットとするプラットフォーム通知サービスは渡される `pns` 文字列によって異なります。

1. `MainActivity` クラスで `sendNotificationButtonOnClick` メソッドを更新して、次のようにユーザーが選択したプラットフォーム通知サービスで `sendPush` メソッドを呼び出します。
   
       /**
        * Send Notification button click handler. This method sends the push notification
        * message to each platform selected.
        *
        * @param v The view
        */
       public void sendNotificationButtonOnClick(View v)
               throws ClientProtocolException, IOException {
   
           String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                   .getText().toString();
           String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                   .getText().toString();
   
           // JSON String
           nhMessage = "\"" + nhMessage + "\"";
   
           if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
           {
               sendPush("wns", nhMessageTag, nhMessage);
           }
           if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
           {
               sendPush("gcm", nhMessageTag, nhMessage);
           }
           if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
           {
               sendPush("apns", nhMessageTag, nhMessage);
           }
       }

## <a name="run-the-application"></a>アプリケーションの実行
1. デバイスまたは Android Studio を使用したエミュレーターでアプリケーションを実行します。
2. Android アプリケーションで、ユーザー名とパスワードを入力します。 どちらも同じ文字列値にする必要があり、空白や特殊文字が含まれることはありません。
3. Android アプリケーションで、 **[ログイン]**をクリックします。 「 **Logged in and registered (ログイン・登録済み)**」というトースト メッセージが表示されるまで待機します。 これで、 **[Send Notification (通知の送信)]** ボタンが有効になります。
   
    ![][A2]
4. トグル ボタンをクリックして、アプリケーションを実行し、ユーザーを登録しているすべてのプラットフォームを有効にします。
5. 通知メッセージを受信するユーザーの名前を入力します。 そのユーザーは、ターゲット デバイスで通知登録されている必要があります。
6. プッシュ通知メッセージとしてユーザーが受信するメッセージを入力します。
7. **[Send Notification (通知の送信)]**をクリックします。  一致するユーザー名のタグで登録されている各デバイスに、プッシュ通知が送信されます。

[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png



<!--HONumber=Nov16_HO3-->


