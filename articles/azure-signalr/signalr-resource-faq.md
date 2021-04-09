---
title: Azure SignalR Service についてよく寄せられる質問
description: トラブルシューティングや一般的な使用シナリオなど、Azure SignalR Service に関してよく寄せられる質問への回答を示します。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c65bc7e92d925f819a48fd8ab9a8160bc3eb72e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579314"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service の FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service は運用環境で利用できますか?

はい。[ASP.NET Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) と [ASP.NET SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr) のサポートは両方ともすべて一般公開されています。

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>複数のアプリケーション サーバーが存在する場合、クライアント メッセージはすべてのサーバーに送信されますか? それとも 1 つのサーバーだけに送信されますか?

クライアントとアプリケーション サーバーとの間には 1 対 1 のマッピングが存在します。 クライアントからのメッセージは常に同じアプリケーション サーバーに送信されます。

このマッピングは、クライアントまたはアプリケーション サーバーが切断されるまで保持されます。

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>いずれかのアプリケーション サーバーがダウンした場合に、それを見つけたり、通知を受け取ったりするにはどうすればよいですか?

Azure SignalR Service によって、アプリケーション サーバーからのハートビートが監視されます。
指定された期間ハートビートを受信しなかった場合、アプリケーション サーバーはオフラインと見なされます。 このアプリケーション サーバーにマップされているすべてのクライアント接続が切断されます。

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>ASP.NET Core SignalR SDK から Azure SignalR Service SDK に切り替えると、カスタムの `IUserIdProvider` で例外がスローされるのはなぜですか?

ASP.NET Core SignalR SDK と Azure SignalR Service SDK では、`IUserIdProvider` を呼び出すときのパラメーター `HubConnectionContext context` が異なります。

ASP.NET Core SignalR の場合、`HubConnectionContext context` はすべてのプロパティに有効な値が指定された物理クライアント接続からのコンテキストです。

Azure SignalR Service SDK の場合、`HubConnectionContext context` は論理クライアント接続からのコンテキストです。 物理クライアントは Azure SignalR Service インスタンスに接続されるため、限られた数のプロパティのみが指定されます。

現在のところ、`HubConnectionContext.GetHttpContext()` と `HubConnectionContext.User` のみがアクセス可能です。
[ソース コードを確認](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)できます。

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>ASP.NET Core SignalR を使用して、サーバー側の Azure SignalR Service で使用可能な転送を構成できますか? たとえば、WebSocket 転送を無効にすることはできますか?

いいえ。

Azure SignalR Service は、ASP.NET Core SignalR が既定でサポートする 3 つの転送をすべて提供します。 これは構成できません。 Azure SignalR Service によって、すべてのクライアント接続の接続と転送が処理されます。

「[ASP.NET Core SignalR の構成](/aspnet/core/signalr/configuration#configure-allowed-transports-1)」に記載されているように、クライアント側の転送を構成することができます。

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Azure portal に表示されるメッセージ数や接続数などのメトリックの意味を教えてください。 集計の種類としてどれを選べばよいでしょうか?

これらのメトリックの計算の詳細については、「[Azure SignalR Service でのメッセージと接続](signalr-concept-messages-and-connections.md)」を参照してください。

Azure SignalR Service リソースの概要ペインでは、適切な集計の種類が既に選択されています。 メトリック ペインに移動すると、参照として [Azure SignalR Service でのメッセージと接続](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr)に対して集計の種類を使用できます。

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>`Default`、`Serverless`、`Classic` の各サービス モードの意味を教えてください。 どのように選べばよいでしょうか?

新しいアプリケーションの場合、既定モードとサーバーレス モードのみを使用する必要があります。 主な違いは、サービスへのサーバー接続を確立するアプリケーション サーバー (つまり、`AddAzureSignalR()` を使用してサービスに接続する) があるかどうかです。 はいの場合は既定モードを使用し、それ以外の場合はサーバーレス モードを使用します。

クラシック モードは、既存のアプリケーションの下位互換性のために用意されているため、新しいアプリケーションには使用しないでください。

サービス モードの詳細については、[このドキュメント](concept-service-mode.md)を参照してください。

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>サーバーレス モードでクライアントからメッセージを送信できますか?

SignalR インスタンスでアップストリームを設定すると、クライアントからメッセージを送信できます。 アップストリームは、SignalR Service からメッセージおよび接続イベントを受信できる一連のエンドポイントです。 アップストリームが設定されていない場合、クライアントからのメッセージは無視されます。

アップストリームの詳細については、[このドキュメント](concept-upstream.md)を参照してください。

アップストリームは現在、パブリック プレビュー段階にあります。

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Azure SignalR Service を使用する場合と ASP.NET SignalR を使用する場合で、機能上の違いはありますか?

Azure SignalR Service を使用する場合、ASP.NET SignalR のいくつかの API と機能はサポートされません。
- クライアントとハブとの間で任意の状態 (一般に `HubState` と呼ばれる) を渡す機能はサポートされません。
- `PersistentConnection` クラスはサポートされません。
- "*Forever Frame の転送*" はサポートされません。
- クライアントがオフラインの場合、Azure SignalR Service ではクライアントに送信されたメッセージが再生されなくなりました。
- Azure SignalR Service を使用する場合、1 つのクライアント接続のトラフィックは、接続期間中、常に 1 つのアプリ サーバー インスタンスにルーティングされます ("*スティッキー*" ともいう)。

ASP.NET SignalR のサポートは互換性に重点が置かれているため、ASP.NET Core SignalR の一部の新機能はサポートされません。 たとえば、*MessagePack* や *Streaming* は ASP.NET Core SignalR アプリケーションでのみ使用できます。

さまざまなサービス モード (`Classic`、`Default`、`Serverless`) に対して、Azure SignalR Service を構成できます。 ASP.NET では、`Serverless` モードはサポートされません。 また、データプレーンの REST API もサポートされません。

## <a name="where-does-my-data-reside"></a>データはどこに存在するか?

Azure SignalR Service は、データ プロセッサ サービスとして機能します。 お客様のコンテンツは格納されず、データ所在地は仕様で含まれています。 診断用の Azure Storage など、他の Azure サービスを Azure SignalR Service と併用する場合は、データ所在地を Azure リージョンで維持する方法に関するガイダンスについて、[こちらのホワイト ペーパー](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)をご覧ください。