---
title: 認証とセキュリティ モデル - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs の認証とセキュリティ モデルについて説明します。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 19b347423c28b4c615f90f325ead462b9d3e8e9e
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990036"
---
# <a name="azure-event-hubs---authentication-and-security-model"></a>Azure Event Hubs - 認証とセキュリティ モデル

Azure Event Hubs のセキュリティ モデルは、次の要件に対応します。

* 有効な資格情報を提示するクライアントだけが Event Hub にデータを送信できる。
* クライアントが別のクライアントを偽装できないようにする。
* 悪意のあるクライアントをブロックして Event Hub にデータを送信できないようにする。

## <a name="client-authentication"></a>クライアント認証

Event Hubs のセキュリティ モデルは、[Shared Access Signature (SAS)](../service-bus-messaging/service-bus-sas.md) トークンと "*イベント パブリッシャー*" の組み合わせに基づいています。 イベント パブリッシャーは Event Hub の仮想エンドポイントを定義します。 パブリッシャーは、Event Hub にメッセージを送信するためにのみ使用できます。 パブリッシャーからメッセージを受信することはできません。

通常、Event Hub はクライアントごとに 1 つのパブリッシャーを使用します。 Event Hub のパブリッシャーに送信されるすべてのメッセージは、その Event Hub 内でキューに格納されます。 パブリッシャーにより、きめの細かいアクセス制御と調整を行うことができます。

それぞれの Event Hub クライアントに割り当てられる一意のトークンは、クライアントにアップロードされます。 一意のトークンは、異なる一意のパブリッシャーにアクセス権を与えるように生成されます。 トークンを所有するクライアントは、1 つのパブリッシャーにのみ送信でき、それ以外のパブリッシャーには送信できません。 複数のクライアントが同じトークンを共有する場合、これらのクライアントそれぞれが 1 つのパブリッシャーを共有します。

推奨されませんが、Event Hub への直接アクセスを許可するトークンをデバイスに割り当てることができます。 このトークンを保持するデバイスは、Event Hub にメッセージを直接送信できます。 このようなデバイスは調整の対象になりません。 さらに、Event Hub への送信を禁止するブラックリストの対象にすることはできません。

すべてのトークンは、SAS キーで署名されます。 通常、すべてのトークンは、同じキーで署名されます。 クライアントはキーを認識しません。これによって、他のクライアントがトークンを生成することを防いでいます。

### <a name="create-the-sas-key"></a>SAS キーを作成する

Event Hubs 名前空間を作成すると、**RootManageSharedAccessKey** という名前の 256 ビットの SAS キーが自動的に生成されます。 このルールに関連して、名前空間に送信、リッスン、管理の権限を付与する主キーと 2 次キーのペアがあります。 追加のキーも作成できます。 特定の Event Hub に送信アクセス許可を与えるキーを生成することをお勧めします。 これ以降、このトピックでは、このキーは **EventHubSendKey** という名前であることを前提とします。

次の例では、Event Hub を作成するときに送信専用のキーを作成します。

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>トークンを生成する

SAS キーを使用してトークンを生成できます。 クライアントごとにトークンを 1 つだけ作成する必要があります。 トークンは、次のメソッドを使用して生成できます。 すべてのトークンは、 **EventHubSendKey** キーを使用して生成されます。 各トークンには、一意の URI が割り当てられます。 "resource" パラメーターは、サービス (ここではイベント ハブ) の URI エンドポイントに対応します。

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

このメソッドを呼び出すときは、URI に `https://<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`を指定する必要があります。 すべてのトークンで同じ URI を使用しますが、 `PUBLISHER_NAME`のみ、トークンごとに異なるものにする必要があります。 `PUBLISHER_NAME` はそのトークンを受信するクライアントの ID を表していると理想的です。

このメソッドは、次の構造を持つトークンを生成します。

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

トークンの有効期限は、1970 年 1 月 1 日からの秒単位で指定されます。 次に、トークンの例を示します。

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

通常、トークンは、クライアントの寿命とほぼ同じかそれより長い寿命を持っています。 クライアントが新しいトークンを取得できる場合は、短い寿命のトークンを使用できます。

### <a name="sending-data"></a>データの送信

トークンが作成された後、各クライアントは独自のトークンと共にプロビジョニングされます。

クライアントは、Event Hub にデータを送信するときに、トークンで送信要求にタグ付けします。 攻撃者による送信の傍受とトークンの盗難を防ぐために、クライアントと Event Hub 間の通信は暗号化されたチャネルを介して行う必要があります。

### <a name="blacklisting-clients"></a>クライアントのブラックリスト化

トークンが攻撃者によって盗まれた場合、攻撃者は、トークンが盗まれたクライアントを偽装できます。 クライアントをブラックリスト化するとそのクライアントは、別のパブリッシャーを使用する新しいトークンを受信するまで、使用できなくなります。

## <a name="authentication-of-back-end-applications"></a>バックエンド アプリケーションの認証

Event Hub クライアントによって生成されたデータを使用するバックエンド アプリケーションを認証するため、Event Hub は、Service Bus トピックで使用されるモデルに似たセキュリティ モデルを使用します。 Event Hubs のコンシューマー グループは、Service Bus トピックに対するサブスクリプションに相当します。 クライアントは、コンシューマー グループを作成する要求に Event Hub または Event Hub が所属している名前空間の管理権限を与えるトークンが付属している場合に、コンシューマー グループを作成できます。 クライアントは、受信要求にコンシューマー グループ、Event Hub、または Event Hub が所属している名前空間の受信権限を与えるトークンが付属している場合に、コンシューマー グループを使用できます。

Service Bus の現在のバージョンは、個々のサブスクリプションに対する SAS ルールをサポートしません。 これは Event Hubs のコンシューマー グループにも当てはまります。 両方の機能に対する SAS のサポートは、今後追加される予定です。

個々のコンシューマー グループに対する SAS 認証が存在しない場合は、SAS キーを使用することで、一般的なキーを持つすべてのコンシューマー グループを保護できます。 この方法によって、アプリケーションは、Event Hub のすべてのコンシューマー グループのデータを使用できます。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のトピックを参照してください。

* [Event Hubs の概要]
* [Shared Access Signature の概要]
* [Event Hubs を使用するサンプル アプリケーション]

[Event Hubs の概要]: event-hubs-what-is-event-hubs.md
[Event Hubs を使用するサンプル アプリケーション]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Shared Access Signature の概要]: ../service-bus-messaging/service-bus-sas.md

