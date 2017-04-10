---
title: "Azure Event Hubs 認証とセキュリティ モデルの概要 | Microsoft Docs"
description: "Event Hubs の認証とセキュリティ モデルの概要"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2017
ms.author: sethm;clemensv
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 930b3da630b88eecdec56e86d621daee53070257
ms.lasthandoff: 03/08/2017


---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Event Hubs の認証とセキュリティ モデルの概要
Azure Event Hubs のセキュリティ モデルは、次の要件に対応します。

* 有効な資格情報を提示するデバイスだけが Event Hub にデータを送信できる。
* デバイスが別のデバイスを偽装できないようにする。
* 悪意のあるデバイスをブロックして Event Hub にデータを送信できないようにする。

## <a name="device-authentication"></a>デバイスの認証
Event Hubs のセキュリティ モデルは、[Shared Access Signature (SAS)](../service-bus-messaging/service-bus-sas.md) トークンと "*イベント パブリッシャー*" の組み合わせに基づいています。 イベント パブリッシャーは Event Hub の仮想エンドポイントを定義します。 パブリッシャーは、Event Hub にメッセージを送信するためにのみ使用できます。 パブリッシャーからメッセージを受信することはできません。

通常、Event Hub はデバイスごとに&1; つのパブリッシャーを使用します。 Event Hub のパブリッシャーに送信されるすべてのメッセージは、その Event Hub 内でキューに格納されます。 パブリッシャーにより、きめの細かいアクセス制御と調整を行うことができます。

各デバイスには、デバイスにアップロードされる一意のトークンが割り当てられます。 一意のトークンは、異なる一意のパブリッシャーにアクセス権を与えるように生成されます。 トークンを所有するデバイスは、1 つのパブリッシャーにのみ送信でき、それ以外のパブリッシャーには送信できません。 複数のデバイスが同じトークンを共有する場合、これらのデバイスは&1; つのパブリッシャーを共有します。

推奨されませんが、Event Hub への直接アクセスを許可するトークンをデバイスに割り当てることができます。 このトークンを保持するデバイスは、Event Hub にメッセージを直接送信できます。 このようなデバイスは調整の対象になりません。 さらに、Event Hub への送信を禁止するブラックリストの対象にすることはできません。

すべてのトークンは、SAS キーで署名されます。 通常、すべてのトークンは、同じキーで署名されます。 デバイスはキーを認識しません。これによって、デバイスがトークンを生成することを防いでいます。

### <a name="create-the-sas-key"></a>SAS キーを作成する
Azure Event Hubs 名前空間を作成するときに、**RootManageSharedAccessKey** という名前の 256 ビットの SAS キーが生成されます。 このキーは、名前空間に対する送信、リッスン、および管理権限を与えます。 追加のキーを作成できます。 特定の Event Hub に送信アクセス許可を与えるキーを生成することをお勧めします。 これ以降、このトピックでは、このキーは **EventHubSendKey** という名前であることを前提とします。

次の例では、Event Hub を作成するときに送信専用のキーを作成します。

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>トークンを生成する
SAS キーを使用してトークンを生成できます。 デバイスごとにトークンを&1; つだけ作成する必要があります。 トークンは、次のメソッドを使用して生成できます。 すべてのトークンは、 **EventHubSendKey** キーを使用して生成されます。 各トークンには、一意の URI が割り当てられます。

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

このメソッドを呼び出すときは、URI に `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`を指定する必要があります。 すべてのトークンで同じ URI を使用しますが、 `PUBLISHER_NAME`のみ、トークンごとに異なるものにする必要があります。 `PUBLISHER_NAME` はそのトークンを受信するデバイスの ID を表していると理想的です。

このメソッドは、次の構造を持つトークンを生成します。

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

トークンの有効期限は、1970 年 1 月 1 日からの秒単位で指定されます。 次に、トークンの例を示します。

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

通常、トークンは、デバイスの寿命とほぼ同じかそれより長い寿命を持っています。 デバイスが新しいトークンを取得できる場合は、短い寿命のトークンを使用できます。

### <a name="devices-sending-data"></a>データを送信するデバイス
トークンが作成された後、各デバイスは独自のトークンと共にプロビジョニングされます。

デバイスは Event Hub にデータを送信するときに、トークンを送信要求にタグ付けします。 攻撃者による送信の傍受とトークンの盗難を防ぐために、デバイスと Event Hub 間の通信は暗号化されたチャネルを介して行う必要があります。

### <a name="blacklisting-devices"></a>デバイスのブラックリスト化
トークンが攻撃者によって盗まれた場合、攻撃者は、トークンが盗まれたデバイスを偽装できます。 デバイスをブラックリスト化すると、デバイスは、別のパブリッシャーを使用する新しいトークンが与えられるまで使用できなくなります。

## <a name="authentication-of-back-end-applications"></a>バックエンド アプリケーションの認証
デバイスによって生成されたデータを使用するバックエンド アプリケーションを認証するため、Event Hubs は、Service Bus トピックで使用されるモデルに似たセキュリティ モデルを使用します。 Event Hubs のコンシューマー グループは、Service Bus トピックに対するサブスクリプションに相当します。 クライアントは、コンシューマー グループを作成する要求に Event Hub または Event Hub が所属している名前空間の管理権限を与えるトークンが付属している場合に、コンシューマー グループを作成できます。 クライアントは、受信要求にコンシューマー グループ、Event Hub、または Event Hub が所属している名前空間の受信権限を与えるトークンが付属している場合に、コンシューマー グループを使用できます。

Service Bus の現在のバージョンは、個々のサブスクリプションに対する SAS ルールをサポートしません。 これは Event Hubs のコンシューマー グループにも当てはまります。 両方の機能に対する SAS のサポートは、今後追加される予定です。

個々のコンシューマー グループに対する SAS 認証が存在しない場合は、SAS キーを使用することで、一般的なキーを持つすべてのコンシューマー グループを保護できます。 この方法によって、アプリケーションは、Event Hub のすべてのコンシューマー グループのデータを使用できます。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のトピックを参照してください。

* [Event Hubs の概要]
* [Shared Access Signature の概要]
* [Event Hubs を使用するサンプル アプリケーション]

[Event Hubs の概要]: event-hubs-what-is-event-hubs.md
[Event Hubs を使用するサンプル アプリケーション]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Shared Access Signature の概要]: ../service-bus-messaging/service-bus-sas.md


