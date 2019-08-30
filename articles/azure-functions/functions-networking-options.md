---
title: Azure Functions のネットワーク オプション
description: Azure Functions で利用可能なすべてのネットワーク オプションの概要
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f4f081001f2573bccc58205ccc7955739b7f5c4c
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779285"
---
# <a name="azure-functions-networking-options"></a>Azure Functions のネットワーク オプション

この記事では、Azure Functions のホスティング オプションで利用できるネットワーク機能について説明します。 次のネットワーク オプションはすべて、インターネットのルーティング可能アドレスを使用せずにリソースにアクセスする機能、または関数アプリへのインターネット アクセスを制限する機能を提供します。 

ホスティング モデルには、さまざまなレベルのネットワーク分離機能があります。 正しいものを選択すると、ネットワーク分離の要件を満たすために役立ちます。

関数アプリは、いくつかの方法でホストできます。

* さまざまなレベルの仮想ネットワーク接続性とスケーリングのオプションが設定された、マルチテナント インフラストラクチャ上で実行される一連のプラン オプションがあります。
    * [従量課金プラン](functions-scale.md#consumption-plan)。負荷に応じて動的にスケーリングし、最小限のネットワークの分離オプションを提供します。
    * [Premium プラン](functions-scale.md#premium-plan)。このプランでも動的なスケーリングが行われますが、より包括的なネットワークの分離が提供されます。
    * [App Service プラン](functions-scale.md#app-service-plan)。固定されたスケールで動作し、Premium プランと同様のネットワークの分離を提供します。
* [App Service Environment](../app-service/environment/intro.md) で関数を実行できます。 この方法では、関数を仮想ネットワークにデプロイし、完全なネットワーク制御と分離を提供します。

## <a name="matrix-of-networking-features"></a>ネットワーク機能のマトリックス

|                |[従量課金プラン](functions-scale.md#consumption-plan)|[Premium プラン](functions-scale.md#premium-plan) (プレビュー)|[[App Service プラン]](functions-scale.md#app-service-plan)|[App Service 環境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[受信 IP の制限とプライベート サイトへのアクセス](#inbound-ip-restrictions)|✅はい|✅はい|✅はい|✅はい|
|[仮想ネットワークの統合](#virtual-network-integration)|❌いいえ|✅はい (リージョン)|✅はい (リージョンとゲートウェイ)|✅はい|
|[仮想ネットワーク トリガー (非 HTTP)](#virtual-network-triggers-non-http)|❌いいえ| ❌いいえ|✅はい|✅はい|
|[VNet](#hybrid-connections)|❌いいえ|❌いいえ|✅はい|✅はい|
|[送信 IP の制限](#outbound-ip-restrictions)|❌いいえ| ❌いいえ|❌いいえ|✅はい|


## <a name="inbound-ip-restrictions"></a>受信 IP の制限

IP 制限を使用すると、アプリへのアクセスを許可または拒否する IP アドレスの優先順のリストを定義できます。 このリストには、IPv4 アドレスと IPv6 アドレスを含めることができます。 1 つまたは複数のエントリがある場合、リストの末尾には暗黙的な "すべて拒否" が存在します。 IP 制限は、すべての関数ホスティング オプションで有効です。

> [!NOTE]
> ネットワーク制限が適用されている場合、ポータル エディターを使用できるのは、仮想ネットワーク内から、または Azure portal にアクセスするために使用しているコンピューターの IP がホワイトリストに登録されている場合のみです。 ただし、 **[プラットフォーム機能]** タブの機能にはすべてのコンピューターから引き続きアクセスできます。

詳細については、「[Azure App Service の静的なアクセス制限](../app-service/app-service-ip-restrictions.md)」を参照してください。

## <a name="private-site-access"></a>プライベート サイトへのアクセス

プライベート サイト アクセスとは、Azure 仮想ネットワークなどプライベート ネットワークのみからアプリにアクセスできるようにすることです。 
* プライベート サイトへのアクセスは、**サービス エンドポイント**が構成されている場合に [Premium](./functions-premium-plan.md) と [App Service プラン](functions-scale.md#app-service-plan)で利用可能です。 詳細については、「[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)」を参照してください。
    * サービス エンドポイントがあれば、仮想ネットワーク統合が構成されていても、関数はインターネットへの完全な送信アクセスを引き続き持つことに注意してください。
* プライベート サイトには、内部ロード バランサー (ILB) を使用して App Service Environment が構成されている場合にもアクセスできます。 詳細については、「[App Service Environment で内部ロード バランサーを作成して使用する](../app-service/environment/create-ilb-ase.md)」を参照してください。

## <a name="virtual-network-integration"></a>仮想ネットワークの統合

仮想ネットワーク統合により、関数アプリは仮想ネットワーク内のリソースにアクセスできます。 この機能は、Premium プランと App Service プランの両方で使用できます。 アプリが App Service Environment 内にある場合は、既に仮想ネットワーク内にそのアプリが存在しているので、同じ仮想ネットワーク内のリソースに到達するために 仮想ネットワーク統合を使用する必要はありません。

仮想ネットワーク統合を使用すると、アプリから、仮想ネットワークで実行されているデータベースや Web サービスにアクセスできます。 仮想ネットワーク統合では、VM 上でアプリケーション用にパブリック エンドポイントを公開する必要はありません。 代わりに、インターネット ルーティングできないプライベート アドレスを使用できます。

仮想ネットワーク統合機能には、次の 2 つの形式があります

1. リージョンの仮想ネットワーク統合により、同じリージョン内の仮想ネットワークとの統合が可能になります。 この形式の機能では、同じリージョン内の仮想ネットワーク内にサブネットが必要です。 この機能はまだプレビュー段階ですが、Windows アプリケーションの運用環境ワークロードではサポートされており、以下に注意点がいくつかあります。
2. ゲートウェイが必要な仮想ネットワーク統合により、リモート リージョン内の仮想ネットワーク、またはクラシック仮想ネットワークとの統合が可能になります。 このバージョンの機能では、VNet への Virtual Network ゲートウェイのデプロイが必要です。 これはポイント対サイト VPN ベースの機能であり、Windows アプリでのみサポートされています。

アプリが同時に使用できる VNet 統合機能の形式は 1 つだけです。 そのため、どちらの機能を使用すべきかが問題になります。 これらはどちらも多くの目的に使用できます。 ただし、明確な差別化要素を次に示します。

| 問題点  | 解決策 | 
|----------|----------|
| 同じリージョン内の RFC 1918 アドレス (10.0.0.0/8、172.16.0.0/12、192.168.0.0/16) に到達する必要がある | リージョン Vnet 統合 |
| クラシック VNet または別のリージョン内の VNet にあるリソースに到達する必要がある | ゲートウェイが必要な Vnet 統合 |
| ExpressRoute にまたがる RFC 1918 エンドポイントに到達する必要がある | リージョン Vnet 統合 |
| サービス エンドポイントにまたがるリソースに到達する必要がある | リージョン Vnet 統合 |

どちらの機能を使用しても、ExpressRoute にまたがる RFC 1918 以外のアドレスに到達することはできません。 それを行うには、今のところ ASE を使用する必要があります。

リージョン Vnet 統合を使用しても、VNet がオンプレミスに接続されたり、サービス エンドポイントが構成されたりはしません。 それは別のネットワーク構成です。 リージョン Vnet 統合は単純に、アプリがこれらの接続の種類をまたがって呼び出しを行うことができるようにします。

使用されているバージョンに関係なく、VNet 統合により、関数アプリは仮想ネットワーク内のリソースにアクセスできるようになりますが、仮想ネットワークからその関数アプリへのプライベート サイト アクセスは付与されません。 プライベート サイト アクセスとは、Azure 仮想ネットワークなどプライベート ネットワークのみからアプリにアクセスできるようにすることです。 Vnet 統合は、アプリから VNet への送信呼び出しを行うためにのみ存在します。 

以下は、VNet 統合機能の特徴です。

* Standard、Premium、または PremiumV2 の App Service プランが必要
* TCP と UDP をサポート
* App Service アプリや関数アプリで動作可能

以下に、VNet 統合でサポートされていないことの例を示します。

* ドライブのマウント
* AD 統合 
* NetBIOS

Functions の仮想ネットワーク統合では、App Service Web アプリでの共有インフラストラクチャを使用します。 2 種類の仮想ネットワーク統合の詳細については、次を参照してください。
* [リージョン VNET 統合](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [ゲートウェイが必要な VNet 統合](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

仮想ネットワーク統合の使用について詳しくは、「[関数アプリを Azure 仮想ネットワークに統合する](functions-create-vnet.md)」を参照してください。

## <a name="virtual-network-triggers-non-http"></a>仮想ネットワーク トリガー (非 HTTP)

現時点では、仮想ネットワーク内から HTTP 以外の関数トリガーを使用できるようにするには、関数アプリを App Service プランまたは App Service Environment で実行する必要があります。

たとえば、仮想ネットワークからのトラフィックのみを受け入れるように Azure Cosmos DB を構成する場合、その仮想ネットワークとの仮想ネットワーク統合を使用して App Service プランで関数アプリをデプロイして、そのリソースからの Azure Cosmos DB トリガーを構成する必要があります。 プレビュー段階では、VNET 統合を構成しても、その Azure Cosmos DB リソースからの Premium プランでのトリガーは許可されません。

[HTTP 以外のすべてのトリガーについてこちらの一覧](./functions-triggers-bindings.md#supported-bindings)をチェックして、サポートされているものを再度確認してください。

## <a name="hybrid-connections"></a>ハイブリッド接続と

[ハイブリッド接続](../service-bus-relay/relay-hybrid-connections-protocol.md)は、他のネットワークのアプリケーション リソースにアクセスするために使用できる Azure Relay の機能です。 アプリからアプリケーション エンドポイントにアクセスできます。 アプリケーションにアクセスするために使用することはできません。 ハイブリッド接続は、[App Service プラン](functions-scale.md#app-service-plan)および [App Service Environment](../app-service/environment/intro.md) で実行されている関数に使用できます。

Azure Functions で使用されるとき、各ハイブリッド接続は、単一の TCP ホストとポートの組み合わせに相互に関連付けられます。 つまり、TCP リッスン ポートにアクセスしている限り、任意のオペレーティング システムの任意のアプリケーションがハイブリッド接続エンドポイントになることができます。 ハイブリッド接続では、アプリケーション プロトコルやアクセス先は認識されません。 それは、ネットワーク アクセスを提供するだけです。

詳細については、App Service プランで Functions をサポートする[ハイブリッド接続の App Service ドキュメント](../app-service/app-service-hybrid-connections.md)を参照してください。

## <a name="outbound-ip-restrictions"></a>送信 IP の制限

送信 IP の制限は、App Service Environment に展開された関数のみで利用可能です。 App Service Environment が展開されている仮想ネットワークの送信制限を構成することができます。

Premium プランまたは App Service プランの関数アプリを仮想ネットワークと統合する場合でも、アプリはインターネットへの送信呼び出しを行うことができます。

## <a name="next-steps"></a>次の手順
ネットワークと Azure Functions の詳細については、以下を参照してください。 

* [仮想ネットワーク統合の概要に関するチュートリアル](./functions-create-vnet.md)
* [関数のネットワークに関する FAQ](./functions-networking-faq.md)
* [仮想ネットワーク統合と App Service/Functions に関する詳細情報](../app-service/web-sites-integrate-with-vnet.md)
* [Azure の仮想ネットワークに関する詳細情報](../virtual-network/virtual-networks-overview.md)
* [App Service Environment でさらなるネットワーク機能と制御を可能にする](../app-service/environment/intro.md)
* [ハイブリッド接続を使用して、ファイアウォールを変更せずに個々のオンプレミス リソースに接続する](../app-service/app-service-hybrid-connections.md)
