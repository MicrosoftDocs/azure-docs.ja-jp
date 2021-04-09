---
title: Azure Notification Hubs を使用して Xamarin.Android アプリにプッシュ通知を送信する | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.Android アプリケーションにプッシュ通知を送信する方法について学習します。
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: matthewp
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: e7d4206de1e097c30e9f5e96bbd935e94892ce0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221036"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>チュートリアル:Notification Hubs を使用して Xamarin.Android アプリにプッシュ通知を送信する

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要

このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.Android アプリケーションにプッシュ通知を送信する方法について説明します。 Firebase Cloud Messaging (FCM) を使用してプッシュ通知を受信する空の Xamarin.Android アプリを作成します。 通知ハブを使用して、アプリを実行しているすべてのデバイスにプッシュ通知をブロードキャストします。 完成したコードは、[NotificationHubs アプリ](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid) サンプルで参照できます。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * Firebase プロジェクトを作成し、Firebase Cloud Messaging を有効にする
> * 通知ハブを作成する
> * Xamarin.Android アプリを作成し、それを通知ハブに接続する
> * Azure Portal からテスト通知を送信する

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
* Windows 上では [Visual Studio と Xamarin]。OS X 上では [Visual Studio for Mac]。
* アクティブな Google アカウント

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Firebase プロジェクトを作成し、Firebase Cloud Messaging を有効にする

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>通知ハブを作成する

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>GCM/FCM の設定を通知ハブ用に構成する

1. 左側のメニューの **[設定]** で、 **[Google (GCM/FCM)]** を選択します。
2. Google Firebase Console からメモした **サーバー キー** を入力します。
3. ツールバーの **[保存]** を選択します。

    ![Google G C M F C M オプションが強調表示され、赤線で囲まれた、Azure portal の通知ハブのスクリーンショット。](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

通知ハブが FCM と連動するように構成されました。接続文字列により、アプリが通知を受信すると共に、プッシュ通知を送信するように登録されます。

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Xamarin.Android アプリを作成し、それを通知ハブに接続する

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Visual Studio プロジェクトを作成し、NuGet パッケージを追加する

> [!NOTE]
> このチュートリアルに記載されている手順は、Visual Studio 2017 を対象としています。 

1. Visual Studio の **[ファイル]** メニューを開き、 **[新規作成]** 、 **[プロジェクト]** の順に選択します。 **[新しいプロジェクト]** ウィンドウで、次の手順のようにします。
    1. **[インストール済み]** 、 **[Visual C#]** の順に展開し、 **[Android]** をクリックします。
    2. 一覧から **[Android アプリ (Xamarin)]** を選択します。
    3. プロジェクトの **名前** を入力します。
    4. プロジェクトの **場所** を選択します。
    5. **[OK]** を選択します。

        ![[新しいプロジェクト] ダイアログ](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. **[New Android App]\(新しい Android アプリ\)** ダイアログ ボックスで **[空のアプリ]** を選択し、 **[OK]** を選択します。

    ![[空のアプリ] テンプレートが強調表示されているスクリーンショット。](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. **ソリューション エクスプローラー** ウィンドウで、 **[プロパティ]** を展開し、 **[AndroidManifest.xml]** をクリックします。 Google Firebase Console でプロジェクトに Firebase Cloud Messaging を追加するときに入力したパッケージ名に一致するようにパッケージ名を更新します。

    ![GCM でのパッケージ名](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. 次の手順に従って、プロジェクトのターゲット Android バージョンを **Android 10.0** に設定します。 
    1. プロジェクトを右クリックし、 **[プロパティ]** を選択します。 
    1. **[Android バージョンを使用したコンパイル:(ターゲット フレームワーク)]** フィールドで、 **[Android 10.0]** を選択します。 
    1. ターゲット フレームワークの変更を続行するには、メッセージ ボックスで **[はい]** を選択します。
1. 次の手順に従って、必要な NuGet パッケージをプロジェクトに追加します。
    1. プロジェクトを右クリックし、 **[NuGet パッケージの管理...]** を選択します。
    1. **[インストール済み]** タブに切り替えて、 **[Xamarin.Android.Support.Design]** を選択し、右側のウィンドウで **[更新]** を選択して、パッケージを最新バージョンに更新します。
    1. **[参照]** タブに切り替えます。**Xamarin.GooglePlayServices.Base** を検索します。 結果の一覧で **[Xamarin.GooglePlayServices.Base]** を選択します。 その後、 **[インストール]** を選択します。

        ![Google Play 開発者サービス NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. **[NuGet パッケージ マネージャー]** ウィンドウで、**Xamarin.Firebase.Messaging** を検索します。 結果の一覧で **[Xamarin.Firebase.Messaging]** を選択します。 その後、 **[インストール]** を選択します。
    7. 次に、**Xamarin.Azure.NotificationHubs.Android** を検索します。 結果の一覧で **[Xamarin.Azure.NotificationHubs.Android]** を選択します。 その後、 **[インストール]** を選択します。

### <a name="add-the-google-services-json-file"></a>Google Services JSON ファイルを追加する

1. Google Firebase Console からダウンロードした `google-services.json` ファイルをプロジェクト フォルダーにコピーします。
2. プロジェクトに `google-services.json` を追加します。
3. **[ソリューション エクスプローラー]** ウィンドウで `google-services.json` を選択します。
4. **[プロパティ]** ウィンドウで、ビルド アクションを **[GoogleServicesJson]** に設定します。 **[GoogleServicesJson]** が表示されない場合は、Visual Studio を閉じて再起動し、プロジェクトを再度開いて再試行します。

    ![GoogleServicesJson ビルド アクション](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>プロジェクトで Notification Hubs を設定する

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging の登録

1. Google Cloud Messaging から Firebase に移行する場合、プロジェクトの `AndroidManifest.xml` ファイルに古い GCM 構成が含まれていることが原因で通知が重複する可能性があります。 ファイルを編集し、`<application>` セクション内の次の行 (存在する場合) を削除します。

    ```xml
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
        android:exported="false" />
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. **application 要素の前** に、以下のステートメントを追加します。

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Android アプリケーションと通知ハブについて次の情報を収集します。

   * **接続文字列のリッスン**: [Azure portal] のダッシュボードで **[接続文字列の表示]** を選択します。 この値に使用するための `DefaultListenSharedAccessSignature` 接続文字列をコピーします。
   * **ハブ名**: [Azure portal] からのハブの名前。 たとえば、 *mynotificationhub2* です。
4. **[ソリューション エクスプローラー]** ウィンドウで、該当する **プロジェクト** を右クリックし、 **[追加]** を選択して、 **[クラス]** を選択します。
5. Xamarin プロジェクトの `Constants.cs` クラスを作成し、このクラスに次の定数値を定義します。 プレースホルダーを実際の値に置き換えます。

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. 次の using ステートメントを `MainActivity.cs` に追加します。

    ```csharp
    using Azure.Messaging.NotificationHubs;
    ```

7. MainActivity クラスに次のプロパティを追加します。

    ```csharp
    internal static readonly string CHANNEL_ID = "my_notification_channel";

8. In `MainActivity.cs`, add the following code to `OnCreate` after `base.OnCreate(savedInstanceState)`:

    ```csharp
    // Listen for push notifications
    NotificationHub.SetListener(new AzureListener());

    // Start the SDK
    NotificationHub.Start(this.Application, HubName, ConnectionString);
    ```

9. `AzureListener` という名前のクラスをプロジェクトに追加します。
10. 次の using ステートメントを `AzureListener.cs` に追加します。

    ```csharp
    using Android.Content;
    using WindowsAzure.Messaging.NotificationHubs;
    ```

11. クラスの宣言の上に次のコードを追加して、クラスが `Java.Lang.Object` から継承し、`INotificationListener` を実装するようにします。

    ```csharp
    public class AzureListener : Java.Lang.Object, INotificationListener
    ```

12. 次のコードを `MyFirebaseMessagingService` クラス内に追加して、受信したメッセージを処理します。

    ```csharp
        public void OnPushNotificationReceived(Context context, INotificationMessage message)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle(message.Title)
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(message.Body)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

1. プロジェクトを **構築します**。
1. デバイスまたは読み込まれたエミュレーターでアプリを **実行する**

## <a name="send-test-notification-from-the-azure-portal"></a>Azure Portal からテスト通知を送信する

アプリの通知の受信をテストするには、[Azure Portal] の **[テスト送信]** オプションを使用します。 これは、デバイスにテスト プッシュ通知を送信します。

![Azure Portal - テスト送信](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Services などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドに使用できない場合は、REST API を直接使用して通知メッセージを送信することもできます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、バックエンドに登録されているすべての Android デバイスにブロードキャスト通知を送信しました。 特定の Android デバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[特定のデバイスにプッシュ通知を送信する](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio と Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure Portal]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android