---
title: "Azure Relay のしくみと利点の概要 | Microsoft Docs"
description: "Azure Relay の概要"
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: 77ee85db0bcc701514a1a98da9405a79d658d49d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-relay"></a>Azure Relay とは

ハイブリッド アプリケーションに Azure Relay サービスを使用すると、ファイアウォール接続を開放せず、または企業ネットワークのインフラストラクチャ内部を変更せずに、企業のエンタープライズ ネットワーク内部にあるサービスを安全にパブリック クラウドに公開することができます。 Relay では、多様なトランスポート プロトコルと Web サービス標準がサポートされています。

リレー サービスは、従来の一方向トラフィック、要求/応答トラフィック、ピアツーピア トラフィックをサポートしています。 また、インターネット範囲のイベント配信もサポートしているので、発行/サブスクライブのシナリオや、双方向ソケット通信も可能になり、ポイント間の効率が向上します。 

リレー型データ転送パターンでは、オンプレミス サービスが送信ポートを介してリレー サービスに接続され、特定のランデブー アドレスに関連付けられた双方向通信用ソケットが作成されます。 その後、クライアントは、ランデブー アドレス宛てのトラフィックをリレー サービスに送信することでオンプレミス サービスと通信できます。 データを受け取ったリレー サービスは、各クライアント専用の双方向ソケットを介してオンプレミス サービスにデータを "リレー" します。 クライアントは、オンプレミス サービスと直接接続する必要はありません。また、クライアントはオンプレミス サービスの場所を知っている必要はありません。オンプレミス サービス側では、ファイアウォールの着信ポートを開く必要がありません。

Relay によって実現される主な機能要素は、ネットワーク境界越しに行われるバッファーを使用しない双方向通信です。TCP と同様の帯域幅調整、エンドポイント検出、接続状態、エンドポイント セキュリティのオーバーレイを備えています。 リレー機能は、1 台のコンピューター上にある単一のアプリケーション エンドポイントに限定できるという点でネットワーク レベルの統合テクノロジ (VPN など) とは異なります。VPN テクノロジは、ネットワーク環境を変えることによって成り立っており、その意味でリレーよりも、はるかに大がかりなテクノロジと言えます。

Azure Relay には、次の 2 つの機能があります。

1. [ハイブリッド接続](#hybrid-connections) - オープン スタンダードの Web ソケットを使用することで、マルチプラットフォームの用途に対応します。
2. [WCF リレー](#wcf-relays) - Windows Communication Foundation (WCF) を使用してリモート プロシージャ コールに対応します。 WCF リレーは従来からあるリレー サービスで、多くのお客様が自社の WCF プログラミング モデルで既に利用しています。

ハイブリッド接続と WCF リレーのどちらでも、企業のエンタープライズ ネットワーク上にある資産への安全な接続が実現します。 次の表に示すように、特定のニーズによっては、一方の機能をもう一方の機能の上に重ねて使用します。

|  | WCF リレー | ハイブリッド接続と |
| --- |:---:|:---:|
| **WCF** |○ | |
| **.NET Core** | |○ |
| **.NET Framework** |○ |○ |
| **JavaScript/NodeJS** | |○ |
| **標準ベースのオープン プロトコル** | |○ |
| **複数の RPC プログラミング モデル** | |○ |

## <a name="hybrid-connections"></a>Hybrid Connections

[Azure Relay ハイブリッド接続](relay-hybrid-connections-protocol.md)機能は、既存のリレー機能から進化した安全でオープンなプロトコルで、あらゆるプラットフォームに実装することができます。実装には、基本的な WebSocket 機能を備えていて、一般的な Web ブラウザーの WebSocket API を明示的に組み込むことができれば、どのような言語でも使用することができます。 ハイブリッド接続には、HTTP と WebSocket が使用されます。

### <a name="service-history"></a>サービスの経緯

ハイブリッド接続は、Azure Service Bus WCF Relay に基づいて構築された、従来からある "BizTalk Services" の同様の名前の機能に取って代わるものです。 新しいハイブリッド接続機能は、既存の WCF リレーを補完します。Azure リレー サービスには、この 2 つのサービス機能が一緒に存在することになります。 両者は共通のゲートウェイを共有しますが、それ以外の点では実装が異なります。

## <a name="wcf-relays"></a>WCF リレー

WCF リレーは、完全な .NET Framework (NETFX) と WCF で使用できます。 オンプレミス サービスとリレー サービス間の接続を開始するには、一連の WCF "リレー" バインディングを使用します。 バックグラウンドで、リレー バインディングは、新しいトランスポート バインディング要素にマッピングされます。この要素は、クラウド内の Service Bus と統合される WCF チャネル コンポーネントを作成するように設計されています。

## <a name="architecture-processing-of-incoming-relay-requests"></a>アーキテクチャ: 受信リレー要求の処理
クライアントが [Azure Relay](/azure/service-bus-relay/) サービスに要求を送信すると、その要求が Azure Load Balancer によってゲートウェイ ノードのいずれかにルーティングされます。 要求がリッスン要求である場合は、ゲートウェイ ノードは新しいリレーを作成します。 要求が特定のリレーへの接続要求の場合は、ゲートウェイ ノードはリレーを所有するゲートウェイ ノードに接続要求を転送します。 リレーを所有するゲートウェイ ノードは、リッスンしているクライアントにランデブー要求を送信します。その際、接続要求を受信したゲートウェイ ノードへの一時的なチャネルを作成するようリスナーに求めます。

リレー接続が確立されると、クライアントはランデブーに使用されるゲートウェイ ノードを経由してメッセージを交換できます。

![受信 WCF Relay 要求の処理](./media/relay-what-is-it/ic690645.png)

## <a name="next-steps"></a>次のステップ

* [Relay に関する FAQ](relay-faq.md)
* [名前空間を作成する](relay-create-namespace-portal.md)
* [.NET を使って作業を開始する](relay-hybrid-connections-dotnet-get-started.md)
* [Node を使って作業を開始する](relay-hybrid-connections-node-get-started.md)

