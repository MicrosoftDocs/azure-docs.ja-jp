---
title: Azure SignalR Service についてよく寄せられる質問
description: Azure SignalR Service のトラブルシューティングと一般的な使用シナリオについてよく寄せられる質問を紹介しています。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 9aa510a055cb76b30508cb98a25cd9c919eb117d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928311"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service の FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service は運用環境で利用できますか?

はい。
一般提供の発表については、「[Azure SignalR Service の一般提供](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/)」を参照してください。 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) は完全にサポートされています。

ASP.NET SignalR のサポートはまだ*パブリック プレビュー*段階です。 [コード例](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)はこちらです。

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>クライアント接続が閉じられ、"利用可能なサーバーがありません" というエラー メッセージが表示されます。 これはどういうことですか?

このエラーは、クライアントが SignalR Service にメッセージを送信している場合にのみ発生します。

アプリケーション サーバーがなく、SignalR Service REST API のみを使用している場合、この動作は**仕様**です。
サーバーレス アーキテクチャの場合、クライアント接続は**リッスン** モードになり、SignalR Service にメッセージを送信しません。
[REST API](./signalr-quickstart-rest-api.md) の詳細を参照してください。

アプリケーション サーバーがある場合、このエラー メッセージはアプリケーション サーバーが SignalR Service インスタンスに接続されていないことを示します。

次の原因が考えられます。
- SignalR Service と接続されているアプリケーション サーバーがない。 アプリケーション サーバーのログを調べて、接続エラーの可能性を確認してください。 これは、アプリケーション サーバーが複数ある高可用性設定ではまれです。
- SignalR Service インスタンスとの接続に問題があります。 この問題は一時的なもので、自動的に復旧します。
1 時間以上続く場合は、[GitHub で問題をオープン](https://github.com/Azure/azure-signalr/issues/new)するか、[Azure でサポート要求を作成](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)してください。

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>複数のアプリケーション サーバーが存在する場合、クライアント メッセージはすべてのサーバーに送信されますか? それとも 1 つのサーバーだけに送信されますか?

クライアントとアプリケーション サーバーのマッピングは 1 対 1 です。 クライアントからのメッセージは常に同じアプリケーション サーバーに送信されます。

クライアントとアプリケーション サーバーのマッピングは、クライアントまたはアプリケーション サーバーが切断されるまで保持されます。

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>いずれかのアプリケーション サーバーがダウンした場合に、それを見つけたり、通知を受け取ったりするにはどうすればよいですか?

SignalR Service では、アプリケーション サーバーからのハートビートを監視します。
指定された期間ハートビートを受信しなかった場合、アプリケーション サーバーはオフラインと見なされます。 このアプリケーション サーバーにマップされているすべてのクライアント接続が切断されます。

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>ASP.NET Core SignalR SDK から Azure SignalR Service SDK に切り替えると、カスタムの `IUserIdProvider` が例外をスローするのはなぜですか?

ASP.NET Core SignalR SDK と Azure SignalR Service SDK では、`IUserIdProvider` を呼び出すときのパラメーター `HubConnectionContext context` が異なります。

ASP.NET Core SignalR の場合、`HubConnectionContext context` はすべてのプロパティに有効な値が指定された物理クライアント接続からのコンテキストです。

Azure SignalR Service SDK の場合、`HubConnectionContext context` は論理クライアント接続からのコンテキストです。 物理クライアント接続は SignalR Service インスタンスに接続されます。そのため、限られた数のプロパティのみが指定されます。

現在のところ、`HubConnectionContext.GetHttpContext()` と `HubConnectionContext.User` のみがアクセス可能です。
ソース コードは、[こちら](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)で確認できます。

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>ASP.NET Core SignalR を使用した場合のサーバー側での構成と同じように、SignalR Service で使用できる転送を構成できますか? たとえば、WebSocket 転送を無効にできますか?

いいえ。

Azure SignalR Service は、ASP.NET Core SignalR が既定でサポートする 3 つの転送をすべて提供します。 これは構成できません。 SignalR Service がすべてのクライアント接続の接続と転送を処理します。

[こちら](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)に記載されているように、クライアント側の転送は構成できます。

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>メッセージ数や接続数など、Azure portal に表示されるメトリックの意味を教えてください。 集計の種類としてどれを選べばよいでしょうか?

これらのメトリックの計算方法の詳細は、[こちら](signalr-concept-messages-and-connections.md)でご覧いただけます。

Azure SignalR Service リソースの概要ブレードでは、適切な集計の種類が選択されています。 また、[Metrics]\(メトリック\) ブレードにアクセスする場合、参考として[こちら](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr)で集計の種類をご覧いただけます。

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>サービス モード `Default`/`Serverless`/`Classic` の意味を教えてください。 どのように選べばよいでしょうか?

モード:
* `Default` モードには、ハブ サーバーが*必要*です。 このモードでは、Azure SignalR がその接続先のハブ サーバー接続にクライアント トラフィックをルーティングします。 Azure SignalR は、接続先のハブ サーバーをチェックします。 接続先のハブ サーバーが見つからない場合、Azure SignalR は、受信クライアント接続を拒否します。 このモードでは、**Management Api** を使用し、Azure SignalR 経由で直接、接続先のクライアントを管理することもできます。
* `Serverless` モードでは、サーバー接続が一切許可 "*されません*"。つまり、すべてのサーバー接続が拒否されます。 すべてのクライアントがサーバーレス モードである必要があります。 クライアントは Azure SignalR に接続し、ユーザーは通常、サーバーレス テクノロジ (**Azure Function** など) を使用して、ハブのロジックを処理します。 Azure SignalR のサーバーレス モードを使用した[簡単な例](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu)を参照してください。
* `Classic` モードは、混在した状態です。 ハブにサーバー接続がある場合、新しいクライアントはハブ サーバーにルーティングされます。それ以外の場合、クライアントはサーバーレス モードになります。

  これが原因で問題が発生することがあります。たとえば、すべてのサーバー接続が少しの間失われ、一部のクライアントがハブ サーバーにルーティングされずに、サーバーレス モードになります。

選択:
1. ハブ サーバーがない場合は、`Serverless` を選択します。
1. すべてのハブにハブ サーバーがある場合は、`Default` を選択します。
1. ハブ サーバーがあるハブとないハブがある場合は、`Classic` を選択します。ただしこれが原因で問題が生じることがあります。代わりに、`Serverless` と `Default` の 2 つのインスタンスを作成することをお勧めします。

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>ASP.NET SignalR の代わりに Azure SignalR を使用した場合、機能上の違いはありますか?
Azure SignalR を使用すると、ASP.NET SignalR のいくつかの API と機能はサポートされなくなります。
- Azure SignalR の使用時に、クライアントとハブとの間で任意の状態 (一般に `HubState` と呼ばれる) を渡す機能はサポートされません。
- Azure SignalR を使用している場合、`PersistentConnection` クラスはまだサポートされません。
- Azure SignalR を使用している場合、**Forever Frame の転送**はサポートされません。
- クライアントがオフラインのとき、Azure SignalR では、クライアントに送信されたメッセージが再生されません。
- Azure SignalR を使用している場合、1 つのクライアント接続のトラフィックは、接続期間中、常に 1 つのアプリ サーバー インスタンスにルーティングされます ("スティッキー" セッションとも呼ばれます)。 ****

ASP.NET SignalR のサポートは互換性に重点が置かれているため、ASP.NET Core SignalR の一部の新機能はサポートされません。 たとえば、**MessagePack** や **Streaming** は ASP.NET Core SignalR アプリケーションでのみ提供されます。

SignalR Service は、各種のサービス モード (`Classic`/`Default`/`Serverles`) で構成できます。 この ASP.NET サポートでは、`Serverless` モードがサポート対象外となります。 また、データプレーンの REST API もサポートされません。

## <a name="where-do-my-data-reside"></a>自分のデータはどこに存在しますか?

Azure SignalR Service は、データ プロセッサ サービスとして機能します。 ユーザーのコンテンツは一切保存されず、データ所在地は仕様によって保証されています。 診断用の Azure Storage など、Azure SignalR Service と他の Azure サービスを併用する場合、データ所在地を Azure リージョンで維持する方法に関するガイダンスについては、[こちら](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)をご覧ください。
