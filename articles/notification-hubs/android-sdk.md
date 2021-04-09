---
title: Azure Notification Hubs と Firebase SDK バージョン 1.0.0-preview1 を使用して Android にプッシュ通知を送信する
description: このチュートリアルでは、Azure Notification Hubs と Google Firebase Cloud Messaging を使用して Android デバイス (バージョン 1.0.0-preview1) にプッシュ通知を送信する方法について学習します。
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 31a411cbcecab8192643f86b6b54d09ac03e7f45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581705"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>チュートリアル:Firebase SDK バージョン 1.0.0-preview1 を使用して Android デバイスにプッシュ通知を送信する

このチュートリアルでは、Azure Notification Hubs と最新版の Firebase Cloud Messaging (FCM) SDK (バージョン 1.0.0-preview1) を使用して Android アプリケーションにプッシュ通知を送信する方法について説明します。 このチュートリアルでは、Firebase Cloud Messaging (FCM) を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。

このチュートリアルの完成したコードは、[GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh) からダウンロードできます。

このチュートリアルに含まれる手順は次のとおりです。

- Android Studio プロジェクトを作成します。
- Firebase Cloud Messaging をサポートする Firebase プロジェクトを作成します。
- 通知ハブを作成します。
- そのハブにアプリを接続します。
- アプリをテストします。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/free/)」を参照してください。

次のものも必要です。

- [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) の最新バージョンをお勧めします。
- サポートされる API レベルは 19 以上です。

## <a name="create-an-android-studio-project"></a>Android Studio プロジェクトの作成

まず、Android Studio でプロジェクトを作成します。

1. Android Studio を起動します。

2. **[File]\(ファイル\)** を選択し、 **[New]\(新規\)** を選択して、 **[New Project]\(新しいプロジェクト\)** を選択します。

3. **[Choose your project]\(プロジェクトの選択\)** ページで、 **[Empty Activity]\(空のアクティビティ\)** を選択してから、 **[Next]\(次へ\)** を選択します。

4. **[Configure your project]\(プロジェクトの構成\)** ページで、次の手順を実行します。
   1. アプリケーションの名前を入力します。
   2. プロジェクト ファイルを保存する場所を指定します。
   3. **[完了]** を選択します。

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="プロジェクトを構成する":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>FCM をサポートする Firebase プロジェクトを作成する

1. [Firebase コンソール](https://firebase.google.com/console/)にサインインします。 Firebase プロジェクトがまだない場合は、新しく作成します。

2. プロジェクトを作成した後、 **[Add Firebase to your Android app]\(Android アプリに Firebase を追加する\)** を選択します。

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Firebase を追加する":::

3. **[Android アプリへの Firebase の追加]** ページで、次の手順を実行します。

   1. **[Android package name]\(Android パッケージ名\)** については、お使いのアプリケーションの **build.gradle** ファイル内にある **applicationId** の値をコピーします。 この例では `com.fabrikam.fcmtutorial1app` です。

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="パッケージ名を指定する":::

   2. **[アプリの登録]** を選択します。

4. **[google-services.json をダウンロード]** を選択し、プロジェクトの **app** フォルダーにファイルを保存して、**[次へ]** をクリックします。

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Google サービスをダウンロードする":::

5. Firebase コンソールで、プロジェクトの歯車アイコンを選択します。 次に、 **[Project Settings]\(プロジェクト設定\)** を選択します。

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="プロジェクト設定":::

6. **google-services.json** ファイルを Android Studio プロジェクトの **app** フォルダーにダウンロードしていない場合は、このページでダウンロードできます。

7. **[クラウド メッセージング]** タブに切り替えます。

8. 後で使用するために、**サーバー キー** をコピーし、保存します。 この値を使用して、ハブを構成します。

## <a name="configure-a-notification-hub"></a>通知ハブを構成する

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. 左側のメニューで **[すべてのサービス]** を選択し、 **[モバイル]** セクションの **[Notification Hubs]** を選択します。 サービス名の横にある星のアイコンを選択して、左側のメニューにある **[お気に入り]** セクションにサービスを追加します。 **Notification Hubs** を **[お気に入り]** に追加した後、左側のメニューでそれを選択します。

3. **[Notification Hubs]** ページで、ツール バーの **[追加]** を選択します。

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="ハブを追加する":::

4. **[Notification Hub]** ページで、次の手順を実行します。

   1. **[通知ハブ]** に名前を入力します。

   2. **[新しい名前空間の作成]** に名前を入力します。 名前空間には、少なくとも 1 つのハブが存在します。

   3. **[場所]** ボックスの一覧から値を選択します。 この値には、ハブを作成する場所を指定します。

   4. **[リソース グループ]** で既存のリソース グループを選択するか、新しいリソース グループを作成します。

   5. **[作成]** を選択します。

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="ハブを作成する":::

5. **[通知]** (ベルのアイコン) を選択し、 **[リソースに移動]** を選択します。 **[Notification Hubs]** ページの一覧を最新の情報に更新して、お使いの通知ハブを選択することもできます。

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="ハブを選択する":::

6. リストから **[アクセス ポリシー]** を選択します。 使用可能な接続文字列が 2 つあることに注意してください。 これらは、後でプッシュ通知を処理するために必要になります。

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="アクセス ポリシー":::

   > [!IMPORTANT]
   > アプリケーションで **DefaultFullSharedAccessSignature** ポリシーを使用しないでください。 このポリシーは、アプリのバックエンドでのみ使用されます。

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Firebase Cloud Messaging の設定をハブ用に構成する

1. 左側のウィンドウの **[Settings]\(設定\)** で、 **[Google (GCM/FCM)]** を選択します。

2. 前に保存した FCM プロジェクトの **サーバー キー** を入力します。

3. ツールバーで、 **[Save]\(保存\)** をクリックします。

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="サーバー キー":::

4. Azure portal に、ハブが正常に更新されたことを示すメッセージが表示されます。 **[Save]\(保存\)** ボタンが無効になります。

これで、Firebase Cloud Messaging と連携するように通知ハブが構成されました。 また、デバイスに通知を送信したり、通知を受信するアプリを登録したりするために必要な接続文字列も取得されました。

## <a name="connect-your-app-to-the-notification-hub"></a>通知ハブにアプリケーションを接続する

### <a name="add-google-play-services-to-the-project"></a>プロジェクトへの Google Play Services の追加

1. Android Studio で、メニューから **[ツール]** を選択し、 **[SDK Manager]** を選択します。

2. 自分のプロジェクトで使用される Android SDK のターゲット バージョンを選択します。 次に、 **[パッケージの詳細ページの表示]** を選択します。

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="SDK マネージャー":::

3. まだインストールされていない場合は、 **[Google API]** を選択します。

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="API":::

4. **[SDK Tools]\(SDK ツール\)** タブに切り替えます。Google Play 開発者サービスをまだインストールしていない場合は、次の図に示すように **[Google Play 開発者サービス]** を選択します。 次に、 **[適用]** をクリックしてインストールします。 SDK のパスは後の手順で使用するので、メモしておいてください。

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Play サービス":::

5. **[Confirm Change]\(変更の確認\)** ダイアログ ボックスが表示される場合は、 **[OK]** を選択します。 コンポーネント インストーラーによって、要求したコンポーネントがインストールされます。 コンポーネントがインストールされた後、 **[Finish]\(完了\)** を選択します。

6. **[OK]** を選択して、 **[Settings for New Projects]\(新しいプロジェクトの設定\)** ダイアログ ボックスを閉じます。

### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs ライブラリの追加

1. アプリの **build.gradle** ファイルで、dependencies セクションに次の行を追加します。

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk-fcm:1.1.4'
   implementation 'androidx.appcompat:appcompat:1.0.0'
   ```

2. dependencies セクションの後に次のリポジトリを追加します。

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Google Firebase のサポートを追加する

1. ファイルの最後に次のプラグインを追加します (まだそこにない場合)。

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. ツール バーの **[Sync Now]\(今すぐ同期\)** を選択します。

### <a name="add-code"></a>コードの追加

1. Azure Notification Hubs からのメッセージをインターセプトする **NotificationHubListener** オブジェクトを作成します。

   ```csharp
   public class CustomNotificationListener implements NotificationHubListener {

      @override
      public void onNotificationReceived(Context context, RemoteMessage message) {
    
         /* The following notification properties are available. */
         Notification notification = message.getNotification();
         String title = notification.getTitle();
         String body = notification.getBody();
         Map<String, String> data = message.getData();
    
         if (message != null) {
            Log.d(TAG, "Message Notification Title: " + title);
            Log.d(TAG, "Message Notification Body: " + message);
         }

         if (data != null) {
             for (Map.Entry<String, String> entry : data.entrySet()) {
                 Log.d(TAG, "key, " + entry.getKey() + " value " + entry.getValue());
             }
         }
      }
   }
   ```

2. `MainActivity` クラスの `OnCreate` メソッドで、アクティビティの作成時に Notification Hubs の初期化プロセスを開始するために次のコードを追加します。

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.initialize(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. Android Studio のメニュー バーで、 **[Build]\(ビルド\)** 、 **[Rebuild Project]\(プロジェクトのリビルド\)** の順に選択し、コードにエラーがないことを確認します。 **ic_launcher** アイコンに関するエラーが発生する場合は、AndroidManifest.xml ファイルから次のステートメントを削除します。

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. アプリを実行するための仮想デバイスがあることを確認します。 ない場合は、次の手順に従って追加してください。

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="デバイス マネージャー":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="仮想デバイス":::
   3. 選択したデバイスでアプリを実行し、そのアプリがハブに正常に登録されることを確認します。

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="デバイスの登録":::

      > [!NOTE]
      > インスタンス ID サービスの `onTokenRefresh()` メソッドが呼び出されるまでは、初期起動時に登録が失敗する場合があります。 更新すると、通知ハブに正常に登録されるようになります。

## <a name="send-a-test-notification"></a>テスト通知を送信する

[Azure portal](https://portal.azure.com/) から次の手順に従って、通知ハブにプッシュ通知を送信することができます。

1. Azure portal 内のお使いのハブの [Notification Hub] ページで、 **[トラブルシューティング]** セクションの **[テスト送信]** を選択します。

2. **[プラットフォーム]** として、 **[Android]** を選択します。

3. **[送信]** を選択します。 Android デバイス上でモバイル アプリを実行していないため、まだそのデバイスに通知は表示されません。 モバイル アプリを実行したら、再度 **[送信]** ボタンを選択して通知メッセージを表示します。

4. ポータル ページの一番下の一覧にある操作の結果を確認します。

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="テスト通知の送信":::

5. デバイスに通知メッセージが表示されます。

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Apps などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドに使用できない場合は、REST API を直接使用して通知メッセージを送信することもできます。

## <a name="run-the-mobile-app-on-emulator"></a>エミュレーターでモバイル アプリを実行する

エミュレーターの内部でプッシュ通知をテストする前に、エミュレーター イメージがアプリ用に選択した Google API レベルをサポートしていることを確認してください。 イメージでネイティブの Google API がサポートされていない場合、**SERVICE_NOT_AVAILABLE** 例外が発生することがあります。

また、 **[設定]**  >  **[アカウント]** で、実行中のエミュレーターに Google アカウントを追加したことを確認してください。 そうでない場合、FCM で登録しようとすると、**AUTHENTICATION_FAILED** 例外が発生する可能性があります。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Firebase Cloud Messaging を使用して、このサービスに登録されたすべての Android デバイスに通知をブロードキャストしました。 特定のデバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[チュートリアル:特定のユーザーに通知を送信する](push-notifications-android-specific-users-firebase-cloud-messaging.md)

その他、通知の送信に関するチュートリアルの一覧を次に示します。

- Azure Mobile Apps:Notification Hubs に統合されている Mobile Apps バックエンドから通知を送信する方法の例については、「[iOS アプリへのプッシュ通知の追加](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)」を参照してください。

- ASP.NET:[Notification Hubs を使用してユーザーにプッシュ通知を送信する方法](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)。

- Azure Notification Hub Java SDK: Java からの通知を送信するには「 [Java から Notification Hubs を使用する方法](notification-hubs-java-push-notification-tutorial.md) 」を参照してください。 これは Android の開発用に Eclipse でテストされています。

- PHP: [PHP から Notification Hubs を使用する方法](notification-hubs-php-push-notification-tutorial.md)