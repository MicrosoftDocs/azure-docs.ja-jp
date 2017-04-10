---
title: "Java から Notification Hubs を使用する方法"
description: "Java バックエンドから Azure Notification Hubs を使用する方法について説明します。"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 41f978750ddef9f7e878c65b0017e909720154aa


---
# <a name="how-to-use-notification-hubs-from-java"></a>Java から Notification Hubs を使用する方法
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

このトピックでは、新しい Azure Notification Hub Java SDK の主な機能について説明します。Azure Notification Hub Java SDK は、完全にサポートされている公式の SDK です。 これはオープン ソース プロジェクトであり、SDK コード全体は [Java SDK] で確認できます。 

MSDN のトピック「 [Notification Hubs の REST API](http://msdn.microsoft.com/library/dn223264.aspx)」の説明にあるように、通常は、Notification Hub REST インターフェイスを使用して、Java/PHP/Python/Ruby バックエンドから Notification Hubs のすべての機能にアクセスできます。 この Java SDK は、これらの REST インターフェイスを使用して、Java で Thin ラッパーを提供します。 

現在 SDK でサポートされている項目は次のとおりです。

* Notification Hubs の CRUD 
* 登録の CRUD
* インストール管理
* 登録のインポート/エクスポート
* 通常の送信
* スケジュールされた送信
* Java NIO を使用した非同期操作
* サポート対象のプラットフォーム: APNS (iOS)、GCM (Android)、WNS (Windows ストア アプリ)、MPNS (Windows Phone)、ADM (Amazon Kindle Fire)、Baidu (Google のサービスを使用しない Android) 

## <a name="sdk-usage"></a>SDK の使用例
### <a name="compile-and-build"></a>コンパイルとビルド
 [Maven]

次のコードを使用してビルドします。

    mvn package

## <a name="code"></a>コード
### <a name="notification-hub-cruds"></a>Notification Hub の CRUD
**NamespaceManager を作成する:**

    NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Notification Hub を作成する:**

    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);

 または

    hub = new NotificationHub("connection string", "hubname");

**Notification Hub を取得する:**

    hub = namespaceManager.getNotificationHub("hubname");

**Notification Hub を更新する:**

    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);

**Notification Hub を削除する:**

    namespaceManager.deleteNotificationHub("hubname");

### <a name="registration-cruds"></a>登録の CRUD
**Notification Hub クライアントを作成する:**

    hub = new NotificationHub("connection string", "hubname");

**Windows の登録を作成する:**

    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");    
    hub.createRegistration(reg);

**iOS の登録を作成する:**

    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

同様に、Android (GCM)、Windows Phone (MPNS)、および Kindle Fire (ADM) の登録を作成できます。

**テンプレートの登録を作成する:**

    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);

**登録 ID の作成と upsert パターンを使用して登録を作成する**

デバイスで登録 ID を格納する場合に応答が失われるため、重複を削除します。

    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);

**登録を更新する:**

    hub.updateRegistration(reg);

**登録を削除する:**

    hub.deleteRegistration(regid);

**登録をクエリする:**

* **1 つの登録を取得する:**
  
    hub.getRegistration(regid);
* **ハブ内のすべての登録を取得する:**
  
    hub.getRegistrations();
* **タグ付きの登録を取得する:**
  
    hub.getRegistrationsByTag("myTag");
* **チャネルにより登録を取得する:**
  
    hub.getRegistrationsByChannel("devicetoken");

すべてのコレクション クエリでは $top トークンと継続トークンがサポートされます。

### <a name="installation-api-usage"></a>インストール API の使用例
インストール API は登録管理の代替メカニズムです。 間違った方法または非効率的な方法で容易に完了した可能性のある重要な複数の登録を保持する代わりに、単一の Installation オブジェクトを使用できるようになりました。 Installation には、プッシュ チャネル (デバイス トークン)、タグ、テンプレート、セカンダリ タイル (WNS および APNS 用) などの必要な情報がすべて格納されます。 ID を取得するためにサービスを呼び出す必要はありません。GUID またはその他の識別子を生成してデバイスに保存し、プッシュ チャネル (デバイス トークン) と共にバックエンドに送信するだけです。 バックエンドで行う必要があるのは、単一の CreateOrUpdateInstallation の呼び出しのみです。CreateOrUpdateInstallation はべき等であるため、必要に応じて自由に再試行してください。

Amazon Kindle Fire の場合の例は次のようになります。

    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);

更新する場合は、次のようになります。 

    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);

高度なシナリオでは、Installation オブジェクトの特定のプロパティだけを変更する部分更新機能を使用できます。 基本的に、部分更新は Installation オブジェクトに対して実行できる JSON Patch 操作の一部です。

    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Installation を削除する:

    hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate、Patch、および Delete は、最終的には Get と一致します。 要求した操作は呼び出しの際にシステム キューに送信され、バックグラウンドで実行されます。 Get は実行時の主要なシナリオ用ではなく、デバッグおよびトラブルシューティングを目的として設計されており、サービスによって緊密に調整されます。

インストールの送信フローは登録の場合と同じです。 特定の Installation に通知を送信するためのオプションが導入されました。"InstallationId:{desired-id}" タグを使用してください。 上記の例の場合、コードは次のようになります。

    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");

複数のテンプレートのうちの 1 つを使用する場合、コードは次のようになります。

    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### <a name="schedule-notifications-available-for-standard-tier"></a>通知のスケジュール設定 (標準階層の場合に使用可能)
通常の送信と同じですが、scheduledTime という追加のパラメーターがあります。このパラメーターは、通知をいつ配信するかを指定します。 サービスは、現在 + 5 分～現在 + 7 日の任意の時点を受け入れます。

**Windows のネイティブの通知のスケジュールを設定する:**

    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);    
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());

### <a name="importexport-available-for-standard-tier"></a>インポート/エクスポート (標準階層の場合に使用可能)
登録に対する一括操作の実行が必要な場合があります。 通常は、別のシステムとの統合や、タグの更新を指定するための大規模な修正を行う場合です。 数千件の登録がある場合は、Get/Update のフローを使用しないことを強くお勧めします。 インポート/エクスポート機能は、このようなシナリオに対応しています。 基本的には、受信データのソースおよび出力のための場所として、ストレージ アカウントの一部の BLOB コンテナーへのアクセスを提供します。

**エクスポート ジョブを送信する:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);


**インポート ジョブを送信する:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);

**ジョブが完了するまで待機する:**

    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }       

**すべてのジョブを取得する:**

    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**SAS 署名を含む URI:** これは、一部の BLOB ファイルまたは BLOB コンテナーの URL に、一連のパラメーター (アクセス許可や有効期限など)、およびアカウントの SAS キーを使用して作成されたこれらすべての項目の署名を加えたものです。 Azure Storage Java SDK には、このような種類の URI を作成を含む豊富な機能が用意されています。 シンプルな代替手段として、署名アルゴリズムの非常に基本的かつコンパクトな実装を含む (GitHub の) ImportExportE2E テスト クラスを使用できます。

### <a name="send-notifications"></a>通知の送信
Notification オブジェクトはヘッダー付きの本文にすぎません。一部のユーティリティ メソッドはネイティブ オブジェクトとテンプレート通知オブジェクトのビルドに役立ちます。

* **Windows ストアおよび Windows Phone 8.1 (Silverlight 以外)**
  
        String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
        Notification n = Notification.createWindowsNotification(toast);
        hub.sendNotification(n);
* **iOS**
  
        String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
        Notification n = Notification.createAppleNotification(alert);
        hub.sendNotification(n);
* **Android**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createGcmNotification(message);
        hub.sendNotification(n);
* **Windows Phone 8.0 および 8.1 Silverlight**
  
        String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>Hello from Java!</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
        Notification n = Notification.createMpnsNotification(toast);
        hub.sendNotification(n);
* **Kindle Fire**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createAdmNotification(message);
        hub.sendNotification(n);
* **タグに送信する**
  
        Set<String> tags = new HashSet<String>();
        tags.add("boo");
        tags.add("foo");
        hub.sendNotification(n, tags);
* **タグ式に送信する**       
  
        hub.sendNotification(n, "foo && ! bar");
* **テンプレート通知を送信する**
  
        Map<String, String> prop =  new HashMap<String, String>();
        prop.put("prop1", "v1");
        prop.put("prop2", "v2");
        Notification n = Notification.createTemplateNotification(prop);
        hub.sendNotification(n);

Java コードを実行すると、ターゲット デバイスに表示される通知が生成されます。

## <a name="a-namenext-stepsanext-steps"></a><a name="next-steps"></a>次のステップ
このトピックでは、Notification Hubs 用の単純な Java REST クライアントの作成方法を説明しました。 次は、以下を実行できます。

* [Java SDK] をすべてダウンロードします。Java SDK には SDK コード全体が含まれています。 
* サンプルを試します。
  * [Notification Hubs の使用]
  * [ニュース速報の送信]
  * [ローカライズ ニュース速報の送信]
  * [認証されたユーザーへの通知の送信]
  * [認証されたユーザーへのクロスプラットフォーム通知の送信]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Notification Hubs の使用]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Notification Hubs の使用]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[ニュース速報の送信]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[ローカライズ ニュース速報の送信]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[認証されたユーザーへの通知の送信]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[認証されたユーザーへのクロスプラットフォーム通知の送信]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/




<!--HONumber=Nov16_HO3-->


