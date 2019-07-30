---
title: 登録管理
description: ここでは、プッシュ通知を受信するために通知ハブにデバイスを登録する方法について説明します。
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.author: jowargo
ms.date: 04/08/2019
ms.openlocfilehash: fffa6784702f239e0af0e9e88a4b9937d20b86ed
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488629"
---
# <a name="registration-management"></a>登録管理

## <a name="overview"></a>概要

ここでは、プッシュ通知を受信するために通知ハブにデバイスを登録する方法について説明します。 また、登録の概要、デバイスを登録するための 2 つの主要パターン (デバイスから通知ハブに直接登録する方法と、アプリケーション バックエンド経由で登録する方法) についても説明します。

## <a name="what-is-device-registration"></a>デバイス登録の概要

Notification Hub にデバイスを登録するには、**登録**または**インストール**を使用します。

### <a name="registrations"></a>登録

登録によって、デバイスのプラットフォーム通知サービス (PNS) ハンドルが、タグや場合によってはテンプレートに関連付けられます。 PNS ハンドルは、ChannelURI、デバイス トークン、または FCM 登録 ID の場合があります。タグは、通知を正しいデバイス ハンドル セットにルーティングするために使用されます。 詳細については、「 [ルーティングとタグ式](notification-hubs-tags-segment-push-message.md)」を参照してください。 テンプレートは、登録ごとの変換を実装するために使用されます。 詳細については、「 [テンプレート](notification-hubs-templates-cross-platform-push-messages.md)」を参照してください。

> [!NOTE]
> Azure Notification Hubs では、デバイスごとに最大 60 個のタグがサポートされます。

### <a name="installations"></a>インストール

インストールは、プッシュ関連の一連のプロパティを含む強化された登録です。 また、デバイス登録の最新の優れた方法です。 ただし、クライアント側の .NET SDK ([バックエンド操作用の Notification Hub SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) ではまだサポートされていません。  つまり、クライアント デバイス自体から登録する場合は、インストールをサポートする [Notification Hubs REST API](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) を使用する必要があります。 バックエンド サービスを使用する場合は、 [バックエンド操作用の Notification Hub SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)を使用できます。

次に、インストールを使用する方法の主な利点について説明します。

- インストールの作成または更新は、完全にべき等です。 そのため、重複した登録について心配することなく、再試行できます。
- このインストール モデルは、特定のデバイスに通知を直接送信できるようになる特殊なタグ形式 (`$InstallationId:{INSTALLATION_ID}`) をサポートしています。 たとえば、アプリのコードがこの特定のデバイスのインストール ID `joe93developer` を設定している場合、開発者は `$InstallationId:{joe93developer}` タグに通知を送信するときにこのデバイスを対象にすることができます。 こうすることで、コードを追加することなく、特定のデバイスを対象にすることができます。
- また、インストールを使用することで、部分的な登録の更新を実行できます。 インストールの部分的な更新は、 [JSON-Patch 標準](https://tools.ietf.org/html/rfc6902)を使用して、PATCH メソッドで要求されます。 これは、登録時にタグを更新する場合に役立ちます。 登録全体を取得し、前のタグすべてを再送信する必要はありません。

インストールには、次のプロパティを含めることができます。 インストール プロパティの完全なリストについては、[REST API でのインストールの作成または上書き](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation)に関するページ、または[インストール プロパティ](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation)に関するページを参照してください。

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> 既定では、登録とインストールに有効期限はありません。

登録とインストールには、各デバイス/チャネルの有効な PNS ハンドルを含める必要があります。 PNS ハンドルはデバイスのクライアント アプリでのみ取得できるので、クライアント アプリを使用してそのデバイスに直接登録する方法があります。 一方、タグに関連するセキュリティの考慮事項とビジネス ロジックによっては、アプリのバックエンドでデバイス登録を管理する作業が必要になる可能性があります。

> [!NOTE]
> インストール API では Baidu サービスはサポートされていません (Registrations API ではサポートされています)。 

### <a name="templates"></a>テンプレート

[テンプレート](notification-hubs-templates-cross-platform-push-messages.md)を使用する場合、デバイスのインストールでは、そのデバイスに関連付けられたすべてのテンプレートも JSON 形式で保持されます (上のサンプルを参照)。 テンプレート名は、1 つのデバイスに複数のテンプレートを対象にすることができます。

各テンプレート名が、テンプレートの本文とオプションの一連のタグにマップされます。 さらに、各プラットフォームには、テンプレート プロパティが他にもある場合があります。 (WNS を使用する) Windows ストアと (MPNS を使用する) Windows Phone 8 の場合、その他のヘッダー セットをテンプレートに含めることができます。 APNs の場合、expiry プロパティを定数またはテンプレート式に設定することができます。 インストール プロパティの一覧については、インストール プロパティの一覧については、 [REST でインストールを作成または上書きする方法](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) に関するトピックを参照してください。

### <a name="secondary-tiles-for-windows-store-apps"></a>Windows ストア アプリのセカンダリ タイル

Windows ストア クライアント アプリケーションの場合、セカンダリ タイルに通知を送信する処理は、プライマリ タイルに送信する場合と同じです。 この処理もインストールでサポートされています。 セカンダリ タイルには、クライアント アプリ上の SDK が透過的に処理する別の ChannelUri があります。

SecondaryTiles ディクショナリは、Windows ストア アプリで SecondaryTiles オブジェクトを作成するために使用されたものと同じ TileId を使用します。 プライマリ タイルの ChannelUri と同様に、セカンダリ タイルの ChannelUris は常に変化する可能性があります。 通知ハブのインストールを最新の状態に保つには、デバイスがセカンダリ タイルの最新の ChannelUris を使用して更新する必要があります。

## <a name="registration-management-from-the-device"></a>デバイスからの登録管理

クライアント アプリからデバイス登録を管理する場合、バックエンドは通知の送信のみを担当します。 クライアント アプリは PNS ハンドルを最新の状態に維持し、タグを登録します。 このパターンについて次の図で説明します。

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

デバイスは、まず PNS から PNS ハンドルを取得してから、通知ハブに直接登録します。 登録に成功すると、アプリ バックエンドからその登録に対して通知を送信できるようになります。 通知の送信方法の詳細については、 [ルーティングとタグ式](notification-hubs-tags-segment-push-message.md)に関するページを参照してください。

この場合は、リッスン権限のみを使用して、デバイスから通知ハブにアクセスします。 詳細については、 [セキュリティ](notification-hubs-push-notification-security.md)に関するページを参照してください。

デバイスからの登録が最も簡単な方法ですが、いくつか欠点があります。

- クライアント アプリがタグを更新できるのは、そのアプリがアクティブなときだけです。 たとえば、ユーザーがスポーツ チームに関連するタグを登録しているデバイスを 2 台持っている状態で、1 台目のデバイスが追加のタグ (Seahawks など) を登録すると、2 台目のデバイスを次に実行するまで、Seahawks に関する通知は 2 台目のデバイスに送信されません。 さらに一般的には、タグが複数のデバイスの影響を受ける場合、バックエンドからのタグ管理が推奨される選択肢です。
- アプリはハッキングされる可能性があるので、セクション「タグレベルのセキュリティ」で説明しているように、特定のタグに対する登録をセキュリティで保護する際に、特別な注意が必要です。

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>インストールを使用してデバイスから通知ハブに登録するコード例

現時点では、 [Notification Hubs REST API](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation)を使用する必要があります。

また、インストールを更新する場合は、 [JSON-Patch 標準](https://tools.ietf.org/html/rfc6902) の PATCH メソッドを使用することもできます。

```
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }
}

private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
{
    if (deviceInstallation.installationId == null)
        return HttpStatusCode.BadRequest;

    // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
    ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
    string hubResource = "installations/" + deviceInstallation.installationId + "?";
    string apiVersion = "api-version=2015-04";

    // Determine the targetUri that we will sign
    string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

    //=== Generate SaS Security Token for Authorization header ===
    // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
    string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

    using (var httpClient = new HttpClient())
    {
        string json = JsonConvert.SerializeObject(deviceInstallation);

        httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

        var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
        return response.StatusCode;
    }
}

var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

string installationId = null;
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored an installation id in application data, create and store as application data.
if (!settings.ContainsKey("__NHInstallationId"))
{
    installationId = Guid.NewGuid().ToString();
    settings.Add("__NHInstallationId", installationId);
}

installationId = (string)settings["__NHInstallationId"];

var deviceInstallation = new DeviceInstallation
{
    installationId = installationId,
    platform = "wns",
    pushChannel = channel.Uri,
    //tags = tags.ToArray<string>()
};

var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

if (statusCode != HttpStatusCode.Accepted)
{
    var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
else
{
    var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>登録を使用してデバイスから通知ハブに登録するコード例

これらのメソッドで、呼び出すデバイスの登録を作成または更新します。 つまり、ハンドルまたはタグを更新するには、登録全体を上書きする必要があります。 登録は一時的なものなので、そのデバイスに必要な最新のタグを保存できる信頼性の高いストアを常に用意する必要があります。

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device id from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration id in device
// storage. Then when the app starts, you can check if a registration id already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration id in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>バックエンドからの登録管理

バックエンドから登録を管理するには、追加のコードを作成する必要があります。 デバイスのアプリは、アプリが起動するたびに、最新の PNS ハンドル (タグとテンプレートも) をバックエンドに提供する必要があります。また、バックエンドは、通知ハブでそのハンドルを更新する必要があります。 この設計について次の図で説明します。

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

バックエンドから登録を管理する利点として、デバイスの対応するアプリがアクティブではない場合でも、登録に対してタグを変更できることと、タグを登録に追加する前にクライアント アプリを認証できることがあります。

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>インストールを使用してバックエンドから通知ハブに登録するコード例

前述のように、クライアント デバイスは、PNS ハンドルと関連するインストール プロパティを取得し、バックエンドで、登録やタグの承認などを行うカスタム API を呼び出すことができます。バックエンドは、[バックエンド操作に Notification Hub SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) を利用できます。

また、インストールを更新する場合は、 [JSON-Patch 標準](https://tools.ietf.org/html/rfc6902) の PATCH メソッドを使用することもできます。

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>登録 ID を使用してデバイスから通知ハブに登録するコード例

アプリ バックエンドから、登録に対して基本の CRUDS 操作を実行できます。 例:

```
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by id
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

バックエンドは、登録の更新が複数ある場合のコンカレンシーに対応する必要があります。 Service Bus は、登録管理向けにオプティミスティック コンカレンシーを提供しています。 HTTP レベルでは、この処理は登録管理操作に対して ETag を使用して実装されます。 この機能は、Microsoft SDK から透過的に使用されます。コンカレンシーの理由で更新が拒否された場合は、例外がスローされます。 アプリ バックエンドは、これらの例外を処理し、必要に応じて更新を再試行する役割を果たします。
