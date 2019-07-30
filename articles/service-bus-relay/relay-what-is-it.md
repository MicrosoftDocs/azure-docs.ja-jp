---
title: Azure Relay とは | Microsoft Docs
description: この記事では、Azure Relay サービスの概要について説明します。このサービスを使用すると、ファイアウォール接続を開いたり、ネットワーク インフラストラクチャ内に及ぶような変更を加えたりせずに、企業ネットワーク内で実行されるオンプレミスのサービスを利用するクラウド アプリケーションを開発できます。
services: service-bus-relay
author: spelluru
manager: ''
editor: ''
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: 964a472a5c0a6350090f83755747a12e89a1650e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422917"
---
# <a name="what-is-azure-relay"></a>Azure Relay とは
Azure Relay サービスでは、企業ネットワーク内で実行されているサービスをパブリック クラウドに安全に公開することができます。 ファイアウォールでポートを開放したり、企業ネットワーク インフラストラクチャ内に及ぶような変更を加えたりすることなく、それが可能です。 

このリレー サービスは、オンプレミス サービスと、クラウドまたは別のオンプレミス環境で実行されているアプリケーションとの間で、以下のシナリオをサポートしています。 

- 従来の一方向の通信、要求/応答の通信、ピアツーピア通信 
- 発行/サブスクライブのシナリオを可能にするインターネット スコープでのイベント配信 
- ネットワーク境界を越える双方向の、バッファーを使用しないソケット通信。

Azure Relay は、VPN のようなネットワーク レベルの統合テクノロジとは異なります。 Azure のリレーの範囲を、1 台のコンピューター上の 1 つのアプリケーション エンドポイントに設定できます。 VPN テクノロジは、ネットワーク環境の変更を必要とするため、これよりずっと内側まで入り込みます。 

## <a name="basic-flow"></a>基本のフロー
リレー型のデータ転送パターンに伴う基本のステップは次のとおりです。

1. オンプレミスのサービスが、送信ポートを介してリレー サービスに接続します。 
2. 特定のアドレスに関連付けられた通信の双方向ソケットが作成されます。 
3. クライアントはその後、そのアドレス宛てのトラフィックをリレー サービスに送信することで、オンプレミス サービスと通信できます。 
4. リレー サービスは次に、そのクライアント専用の双方向ソケットを介してオンプレミス サービスにデータを*リレー*します。 クライアントに、オンプレミス サービスへの直接接続は必要ありません。 サービスの場所を知っている必要がありません。 また、オンプレミス サービスのために、ファイアウォールで受信ポートを開いておく必要はありません。


## <a name="features"></a>機能 
Azure Relay には、次の 2 つの機能があります。

- [ハイブリッド接続](#hybrid-connections) - オープン スタンダードの Web ソケットを使用することで、マルチプラットフォームの用途に対応します。
- WCF リレー - Windows Communication Foundation (WCF) を使用してリモート プロシージャ コールに対応します。 WCF リレーは従来からあるリレー サービスで、多くのお客様が自社の WCF プログラミング モデルで既に利用しています。

## <a name="hybrid-connections"></a>ハイブリッド接続と

Azure Relay のハイブリッド接続機能は、以前存在していたリレー機能の、セキュリティで保護されたオープン プロトコル仕様の進化版です。 これは、どのプラットフォームのどの言語でも使用できます。 Azure Relay のハイブリッド接続機能は、HTTP と WebSocket プロトコルに基づいています。 Web ソケットまたは HTTP(S) 経由で、要求を送信し、応答を受信することができます。 この機能は、一般的な Web ブラウザーの WebSocket API と互換性があります。 

ハイブリッド接続プロトコルの詳細については、[ハイブリッド接続プロトコルのガイド](relay-hybrid-connections-protocol.md)を参照してください。 ハイブリッド接続は、どのランタイム/言語についても、任意の Web ソケット ライブラリで使用できます。

> [!NOTE]
> Azure Relay のハイブリッド接続は、BizTalk Services の以前ハイブリッド接続機能を置き換えるものです。 BizTalk Services のハイブリッド接続機能は、Azure Service Bus WCF Relay に基づいて構築されました。 Azure Relay のハイブリッド接続機能は、以前から存在する WCF Relay 機能を補完します。 これら 2 つのサービスの機能 (WCF Relay とハイブリッド接続) は、Azure Relay サービス内で並列して存在します。 両者は共通のゲートウェイを共有しますが、それ以外の点では実装が異なります。

## <a name="wcf-relay"></a>WCF リレー
WCF Relay は、完全な .NET Framework (NETFX) と共に、WCF で動作します。 一連の WCF "リレー" バインディングを使用して、オンプレミス サービスとリレー サービスとの間に接続を作成します。 リレー バインディングは、新しいトランスポート バインディング要素にマッピングされます。この要素は、クラウド内の Service Bus と統合される WCF チャネル コンポーネントを作成するように設計されています。 詳細については、[WCF リレーの概要](service-bus-relay-tutorial.md)に関するページを参照してください。

## <a name="hybrid-connections-vs-wcf-relay"></a>ハイブリッド接続とWCF リレー
ハイブリッド接続と WCF Relay のどちらでも、企業ネットワーク内に存在する資産へのセキュアな接続を実現できます。 どちらの機能を優先して使用するかは、次の表に示すように、個々のニーズによって決まります。

|  | WCF リレー | ハイブリッド接続と |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |○ |
| **.NET Framework** |x |x |
| **Java script/Node.JS** | |x |
| **標準ベースのオープン プロトコル** | |x |
| **RPC プログラミング モデル** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>アーキテクチャ:受信リレー要求の処理
次の図は、受信するリレー要求が、Azure Relay サービスでどのように処理されるかを示しています。

![受信 WCF Relay 要求の処理](./media/relay-what-is-it/ic690645.png)

1. リッスンしているクライアントは、Azure Relay サービスにリッスン要求を送信します。 その要求を、Azure ロード バランサーがゲートウェイ ノードの 1 つにルーティングします。 
2. Azure Relay サービスは、ゲートウェイ ストア内にリレーを作成します。 
3. 送信側クライアントは、リッスンしているサービスへの接続要求を送信します。 
4. 要求を受信するゲートウェイは、ゲートウェイ ストアでリレーを検索します。 
5. ゲートウェイは、ゲートウェイ ストアで挙げられた正しいゲートウェイに、接続要求を転送します。 
6. ゲートウェイはリッスンしているクライアントに要求を送信し、そのクライアントが、送信側クライアントに最も近いゲートウェイ ノードへの一時的なチャネルを作成します。 
7. リッスンしているクライアントは、送信側クライアントに最も近いゲートウェイに対して一時的なチャネルを作成します。 ゲートウェイを介したクライアント間の接続が確立されたら、クライアントどうしがメッセージを交換できます。 
8. ゲートウェイは、リッスンしているクライアントからのメッセージをすべて送信側クライアントに転送します。 
9. ゲートウェイは、送信側クライアントからのすべてのメッセージをリッスンしているクライアントに転送します。  

## <a name="next-steps"></a>次の手順
* [.NET WebSocket の概要](relay-hybrid-connections-dotnet-get-started.md)
* [.NET HTTP 要求の概要](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Node WebSocket の概要](relay-hybrid-connections-node-get-started.md)
* [Node HTTP 要求の概要](relay-hybrid-connections-http-requests-node-get-started.md)
* [Relay に関する FAQ](relay-faq.md)

