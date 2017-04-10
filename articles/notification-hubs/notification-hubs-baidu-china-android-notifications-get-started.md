---
title: "Baidu での Azure Notification Hubs の使用 | Microsoft Docs"
description: "このチュートリアルでは、Baidu で Azure Notification Hubs を使用して、Android デバイスにプッシュ通知を送信する方法について学習します。"
services: notification-hubs
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 23bde1ea-f978-43b2-9eeb-bfd7b9edc4c1
ms.service: notification-hubs
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 08/19/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: ec2a69ff5a7f1f3a954eae70f1cd776242b0b7f4
ms.lasthandoff: 12/08/2016


---
# <a name="get-started-with-notification-hubs-using-baidu"></a>Baidu での Notification Hubs の使用
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
Baidu クラウド プッシュは、プッシュ通知をモバイル デバイスに送信するために使用することのできる中国のクラウド サービスです。 このサービスは、さまざまなアプリケーション ストアやプッシュ サービスの存在に加え、GCM (Google Cloud Messaging) に接続されていることが少ない Android デバイスの可用性にも差があるために Android へのプッシュ通知の送信方法が複雑な中国では特に便利です。

## <a name="prerequisites"></a>前提条件
このチュートリアルには、次のものが必要です。

* Android SDK (Eclipse を使用することが前提)。<a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android サイト</a>からダウンロードできます。
* [Mobile Services Android SDK]
* [Baidu Push Android SDK]

> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F)を参照してください。
> 
> 

## <a name="create-a-baidu-account"></a>Baidu アカウントを作成する
Baidu を使用するには、Baidu アカウントが必要です。 既にアカウントを持っている場合は、 [Baidu ポータル] にログインし、次の手順にスキップします。 アカウントを持っていない場合は、Baidu アカウントの作成方法に関する次の手順を参照してください。  

1. [Baidu ポータル]で **[登录]** (**ログイン**) のリンクをクリックします。 **[立即注册]** をクリックしてアカウントの登録プロセスを開始します。
   
   ![][1]
2. 電話番号/電子メール アドレス、パスワード、認証コードなどの必要情報を入力し、 **[Signup]**をクリックします。
   
   ![][2]
3. 入力した電子メール アドレスに、Baidu アカウントをアクティブ化するリンクが記載された電子メールが届きます。
   
   ![][3]
4. 電子メール アカウントにログインし、Baidu のアクティブ化メールを開き、リンクをクリックして Baidu アカウントをアクティブ化します。
   
   ![][4]

アクティブ化した Baidu アカウントを取得したら、 [Baidu ポータル]にログインします。

## <a name="register-as-a-baidu-developer"></a>Baidu 開発者として登録する
1. [Baidu ポータル]にログインしたら、**[更多>>]** (**詳細**) をクリックします。
   
      ![][5]
2. **[站长与开发者服务] \(Web 管理者および開発者向けサービス)** セクションを下にスクロールし、**[百度开放云平台]** \(**Baidu オープン クラウド プラットフォーム**) をクリックします。
   
      ![][6]
3. 次のページで、右上にある **[开发者服务]** (**開発者向けサービス**) をクリックします。
   
      ![][7]
4. 次のページで、右上のメニューから **[注册开发者]** (**登録済み開発者**) をクリックします。
   
      ![][8]
5. 確認用のテキスト メッセージを受信するために名前、説明、および携帯電話の番号を入力し、**[送验证码]** (**確認コードを送信する**) をクリックします。 国際電話番号の場合は、国コードをかっこで囲む必要があります。 たとえば、米国の電話番号の場合は、 **(1)1234567890**になります。
   
      ![][9]
6. その後、次の例に示すような、認証番号を含むテキスト メッセージが届きます。
   
      ![][10]
7. メッセージに含まれている確認番号を **[验证码]** (**確認コード**) に入力します。
8. 最後に、Baidu 契約書に同意して **[提交]** (**送信**) をクリックし、開発者の登録を完了します。 登録が正常に終了すると、次のページが表示されます。
   
      ![][11]

## <a name="create-a-baidu-cloud-push-project"></a>Baidu クラウド プッシュ プロジェクトを作成する
Baidu クラウド プッシュ プロジェクトを作成すると、アプリケーション ID、API キー、およびシークレット キーが届きます。

1. [Baidu ポータル]にログインしたら、**[更多>>]** (**詳細**) をクリックします。
   
      ![][5]
2. **[站长与开发者服务]** \(**Web 管理者および開発者向けサービス**) セクションを下にスクロールし、**[百度开放云平台]** \(**Baidu オープン クラウド プラットフォーム**) をクリックします。
   
      ![][6]
3. 次のページで、右上にある **[开发者服务]** (**開発者向けサービス**) をクリックします。
   
      ![][7]
4. 次のページで、**[云服务]** (**クラウド サービス**) セクションから **[云推送]** (**クラウド プッシュ**) をクリックします。
   
      ![][12]
5. 登録済み開発者の場合は、上部メニューに **[管理控制台]** (**管理コンソール**) が表示されます。 **[开发者服务管理]** (**開発者サービスの管理**) をクリックします。
   
      ![][13]
6. 次のページで **[创建工程]** (**プロジェクトの作成**) をクリックします。
   
      ![][14]
7. アプリケーション名を入力し、**[创建]** (**作成**) をクリックします。
   
      ![][15]
8. Baidu クラウド プッシュ プロジェクトが正常に作成されると、**AppID**、**API キー**、**シークレット キー**を含むページが表示されます。 API キーとシークレット キーは後で使用するので書き留めておきます。
   
      ![][16]
9. 左側のウィンドウで **[云推送]** (**クラウド プッシュ**) をクリックし、プッシュ通知用のプロジェクトを構成します。
   
      ![][31]
10. 次のページで、**[推送设置]** (**プッシュの設定**) ボタンをクリックします。
    
    ![][32]  
11. 構成ページで、Android プロジェクトで使用するパッケージ名を **[应用包名]** (**アプリケーション パッケージ**) フィールドに入力し、**[保存设置]** (**保存**) をクリックします。  
    
    ![][33]

**"保存成功！"** (**正常に保存されました**) というメッセージが表示されます。

## <a name="configure-your-notification-hub"></a>通知ハブを構成する
1. [Azure クラシック ポータル]にサインインし、画面の下部にある **[+新規]** をクリックします。
2. **[App Services]**、**[Service Bus]**、**[通知ハブ]** の順にクリックし、**[簡易作成]** をクリックします。
3. **[通知ハブ]** の名前を指定し、この通知ハブが作成される **[リージョン]** と **[名前空間]** を選択して、**[新しい通知ハブを作成する]** をクリックします。  
   
      ![][17]
4. 通知ハブを作成した名前空間をクリックし、上部にある **[通知ハブ]** をクリックします。
   
      ![][18]
5. 作成した通知ハブを選択し、上部のメニューから **[構成]** をクリックします。
   
      ![][19]
6. **[Baidu の通知設定]** セクションまで下へスクロールし、Baidu クラウド プッシュ プロジェクトのために Baidu コンソールから取得した API キーとシークレット キーを入力します。 [ **Save**] をクリックします。
   
      ![][20]
7. 通知ハブの上部にある **[ダッシュボード]** タブをクリックし、**[接続文字列の表示]** をクリックします。
   
      ![][21]
8. **[接続情報へのアクセス]** ウィンドウに表示される **DefaultListenSharedAccessSignature** と **DefaultFullSharedAccessSignature** をメモしておきます。
   
    ![][22]

## <a name="connect-your-app-to-the-notification-hub"></a>通知ハブにアプリケーションを接続する
1. Eclipse ADT で、新しい Android プロジェクトを作成します (**[ファイル]**  >  **[新規]**  >  **[Android アプリケーション プロジェクト]** の順に選択します)。
   
    ![][23]
2. **[アプリケーション名]** に名前を入力し、**[最小必須 SDK]** のバージョンが **API 16: Android 4.1** に設定されていることを確認します。
   
    ![][24]
3. **[次へ]** をクリックし、**[アクティビティの作成]** ウィンドウが表示されるまでウィザードに従います。 **[空のアクティビティ]** が選択されていることを確認し、最後に **[完了]** を選択して新しい Android アプリケーションを作成します。
   
    ![][25]
4. **[プロジェクト ビルド ターゲット]** が正しく設定されていることを確認します。
   
    ![][26]
5. **Bintray の Notification-Hubs-Android-SDK** にある [[Files]](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4)タブから notification-hubs-0.4.jar ファイルをダウンロードします。 このファイルを Eclipse プロジェクトの **libs** フォルダーに追加し、 *libs* フォルダーを最新の情報に更新します。
6. [Baidu Push Android SDK] をダウンロードし、解凍します。**libs** フォルダーを開き、**pushservice-x.y.z jar** ファイルと **armeabi** & および **mips** フォルダーを Android アプリケーションの **libs** フォルダーに コピーします。
7. Android プロジェクトの **AndroidManifest.xml** ファイルを開き、Baidu SDK に必要なアクセス許可を追加します。
   
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
8. **android:name** プロパティを **AndroidManifest.xml** の **application** 要素に追加し、*yourprojectname* を置き換えます (例: **com.example.BaiduTest**)。 このプロジェクト名が、Baidu コンソールで構成したプロジェクト名と一致していることを確認します。
   
        <application android:name="yourprojectname.DemoApplication"
9. application 要素内で、**.MainActivity** アクティビティ要素の後に次の構成を追加し、*yourprojectname* を置き換えます (例: **com.example.BaiduTest**)。
   
        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>
   
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>
10. プロジェクトに **ConfigurationSettings.java** という名前の新しいクラスを追加します。
    
     ![][28]
    
     ![][29]
11. このクラスに次のコードを追加します。
    
        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }
    
    **API_KEY** には前もって Baidu クラウド プロジェクトから取得した値、**NotificationHubName** には Azure クラシック ポータルの通知ハブ名、**NotificationHubConnectionString** には Azure クラシック ポータルの DefaultListenSharedAccessSignature をそれぞれ設定します。
12. **DemoApplication.java**という名前の新しいクラスを追加し、それに次のコードを追加します。
    
        import com.baidu.frontia.FrontiaApplication;
    
        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }
13. **MyPushMessageReceiver.java**という名前の別の新しいクラスを追加し、それに次のコードを追加します。 これは、Baidu プッシュ サーバーから受信したプッシュ通知を処理するクラスです。
    
        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;
    
        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();
    
            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;
    
                try {
                    if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }
    
                registerWithNotificationHubs();
            }
    
            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                 + ConfigurationSettings.NotificationHubName + "'"
                                 + " with UserId - '"
                                 + mUserId + "' and Channel Id - '"
                                 + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }
    
            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }
    
            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }
14. **MainActivity.java** を開き、**onCreate** メソッドに次のコードを追加します。
    
            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);
15. 先頭に、次の import ステートメントを追加します。
    
            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

## <a name="send-notifications-to-your-app"></a>アプリケーションに通知を送信する
以下の画面に示すように、通知ハブの [[テスト送信]](https://portal.azure.com/) ボタンを使用して、 **Azure ポータル** で通知を送信し、アプリケーションで通知の受信テストをすばやく行うことができます。

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Services などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドで利用できない場合、REST API を直接使用して通知メッセージを送信できます。

このチュートリアルでは、バックエンド サービスではなく、コンソール アプリケーションの通知ハブに .NET SDK を使用して通知を送信することで例を単純にして、クライアント アプリのテスト手順のみを説明します。 ASP.NET バックエンドから通知を送信するには、次のステップとして「 [Notification Hubs を使用したユーザーへのプッシュ通知](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 」を参照することをお勧めします。 ただし、通知の送信には、次の方法も使用できます。

* **REST インターフェイス**: [REST インターフェイス](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)を使用すると、任意のバックエンド プラットフォームの通知をサポートできます。
* **Microsoft Azure Notification Hubs .NET SDK**: Nuget Package Manager for Visual Studio で、 [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)を実行します。
* **Node.js** : [Node.js から Notification Hubs を使用する方法](notification-hubs-nodejs-push-notification-tutorial.md)。
* **Mobile Apps**: Notification Hubs に統合されている Azure App Service Mobile Apps のバックエンドから通知を送信する方法の例については、[モバイル アプリにプッシュ通知を追加](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md)する方法を説明したページを参照してください。
* **Java / PHP**: REST API を使用した通知の送信方法の例については、「Java/PHP から Notification Hubs を使用する方法」([Java](notification-hubs-java-push-notification-tutorial.md)  |  [PHP](notification-hubs-php-push-notification-tutorial.md)) を参照してください。

## <a name="optional-send-notifications-from-a-net-console-app"></a>(省略可能) .NET コンソール アプリケーションから通知を送信する
このセクションでは、.NET コンソール アプリケーションを使用した通知の送信方法について説明します。

1. Visual C# の新しいコンソール アプリケーションを作成します。
   
    ![][30]
2. パッケージ マネージャー コンソール ウィンドウで **[既定のプロジェクト]** に新しいコンソール アプリケーション プロジェクトを設定した後、そのコンソール ウィンドウから次のコマンドを実行します。
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    これにより <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet パッケージ</a>を利用して Azure Notification Hubs SDK に参照が追加されます。
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
3. **Program.cs** ファイルを開き、次の using ステートメントを追加します。
   
        using Microsoft.Azure.NotificationHubs;
4. `Program` クラスで、次のメソッドを追加し、*DefaultFullSharedAccessSignatureSASConnectionString* と *NotificationHubName* を実際の値に置き換えます。
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }
5. **Main** メソッド内に次の行を追加します。
   
         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-your-app"></a>アプリケーションをテストする
実際の電話でこのアプリケーションをテストする場合は、USB ケーブルを使用してコンピューターに電話を接続するだけです。 これで、接続した電話にアプリケーションが読み込まれます。

エミュレーターでこのアプリケーションをテストするには、上部の Eclipse ツール バーで **[実行]**をクリックし、アプリケーションを選択します。 これによりエミュレーターが起動し、アプリケーションが読み込まれて実行されます。

アプリケーションが Baidu プッシュ通知サービスから 'userId' と 'channelId' を取得し、通知ハブに登録します。

テスト通知は、Azure クラシック ポータルの [デバッグ] タブを使用して送信できます。 Visual Studio で .Net コンソール アプリケーションを作成する場合、Visual Studio で F5 キーを押してアプリケーションを実行します。 アプリケーションから通知が送信され、デバイスまたはエミュレーターの上部の通知領域に表示されます。

<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Push Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[Baidu ポータル]: http://www.baidu.com/

