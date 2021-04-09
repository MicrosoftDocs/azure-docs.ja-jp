---
title: Azure Peering Service の概要
description: Azure Peering Service の概要について説明します
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026731"
---
# <a name="azure-peering-service-overview"></a>Azure Peering Service の概要

Azure Peering Service は、Microsoft 365、Dynamics 365、サービスとしてのソフトウェア (SaaS) サービス、Azure、パブリック インターネット経由でアクセス可能な Microsoft サービスなど、Microsoft クラウド サービスへのお客様の接続を強化するネットワーク サービスです。 Microsoft は、世界中のインターネット サービス プロバイダー (ISP)、インターネット交換パートナー (IXP)、およびソフトウェアで定義されたクラウド インターコネクト (SDCI) プロバイダーと提携しており、お客様から Microsoft ネットワークへの最適なルーティングにより、信頼性が高く、高パフォーマンスのパブリック接続を提供しています。

Peering Service では、お客様は特定のリージョンで適切に接続されたパートナー サービス プロバイダーを選択できます。 パブリック接続は、クラウド サービスからエンドユーザーの場所まで、信頼性を高めて待ち時間を最小限に抑えられるように最適化されています。

![Microsoft クラウドへの分散接続](./media/peering-service-about/peering-service-what.png)

お客様は、Azure portal で Peering Service 接続を登録することにより、Microsoft ネットワークへのユーザーの待ち時間の測定、BGP ルートの監視、およびリークやハイジャックなどに対するアラートなど、Peering Service テレメトリを選択することもできます。 

Peering Service を使用する場合、お客様は Microsoft に登録する必要がありません。 唯一の要件は、サービスを取得するために [Peering Service パートナー](location-partners.md)に連絡することです。 Peering Service テレメトリをオプトインするには、お客様が Azure portal で登録する必要があります。

Peering Service 接続を登録する手順については、[Azure portal を使用した Peering Service の登録](azure-portal.md)に関するページを参照してください。 

> [!NOTE]
> この記事は、クラウドおよびインターネットへのエンタープライズ接続を担当するネットワーク設計者を対象としています。


## <a name="what-is-peering-service"></a>Peering Service とは

Peering Service は次のように定義できます。

- パブリック インターネットを使用する IP サービス。 
- サービス プロバイダーとのコラボレーション プラットフォームと、サービス プロバイダー パートナーを介したお客様に、パブリック ネットワーク経由で Microsoft クラウドへの最適で信頼性の高いルーティングを提供することを目的とした付加価値サービス。

Peering Service は、Azure ExpressRoute や VPN 製品などのプライベート接続製品ではありません。

> [!NOTE]
> ExpressRoute の詳細については、[ExpressRoute のドキュメント](../expressroute/index.yml)をご覧ください。
>

## <a name="background"></a>バックグラウンド

Microsoft 365、Dynamics 365、およびその他の Microsoft SaaS サービスは、複数の Microsoft データセンターでホストされ、任意の地理的な場所からアクセスできます。 Microsoft グローバル ネットワークには、世界中に Microsoft Edge ポイント オブ プレゼンス (PoP) の場所があり、ここでサービス プロバイダーを通じてエンド ユーザーに接続できます。 

Microsoft およびパートナー サービス プロバイダーは、Peering Service 接続に登録されているプレフィックスのトラフィックが、確実に Microsoft グローバル ネットワーク上の最も近い Microsoft Edge PoP の場所に出入りするようにします。 Microsoft は、Peering Service 接続に登録されているプレフィックスから送信するネットワーク トラフィックが、確実に Microsoft のグローバル ネットワーク上で最も近い Microsoft Edge PoP の場所を使用するようにします。

![Microsoft ネットワークとパブリック接続](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Microsoft のグローバル ネットワークの詳細については、「[マイクロソフトのグローバル ネットワーク](../networking/microsoft-global-network.md)」を参照してください。
>

## <a name="why-use-peering-service"></a>Peering Service を使用する理由

インターネットによるクラウドへのアクセスを求めていたり、SD-WAN アーキテクチャを検討している企業、または Microsoft SaaS サービスの使用率が高い企業では、堅牢で高性能なインターネット接続が必要です。 お客様は、Peering Service を使用して移行を行うことができます。 Microsoft とサービス プロバイダーは、信頼性が高くパフォーマンスに優れた Microsoft クラウドへのパブリック接続を提供するために提携しました。 主なお客様の機能の一部を次に示します。

- Microsoft Azure Cloud Services へのインターネットを介した最善のパブリック ルーティングによるパフォーマンスと信頼性の最適化。
- 優先サービス プロバイダーを選択して Microsoft クラウドに接続する機能。
- トラフィックの分析情報 (待ち時間レポート、プレフィックス監視など)。
- Microsoft クラウドからの最適なネットワーク ホップ (AS ホップ)。
- ルート分析と統計情報: ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) ルーティングの異常 (漏洩またはハイジャックの検出) やルーティング不全に関するイベントを取得できます。

### <a name="robust-reliable-peering"></a>堅牢で信頼性の高いピアリング

Peering Service は、次の 2 種類の冗長性を使用します。

- **ローカル冗長性**

   Microsoft とサービス プロバイダーは、複数の Microsoft Edge PoP の場所を相互接続して、Peering Service を提供します。 それぞれの場所で、相互接続が 2 つのルーター間のフェールオーバーをサポートする必要があります。

   ピアリングの各場所は、冗長なさまざまなピアリング リンクを使用してプロビジョニングされます。

- **geo 冗長性**

   Microsoft は複数の都市圏の場所でサービス プロバイダーと相互接続しているため、エッジ ノードのいずれかでパフォーマンスが低下した場合、トラフィックは代替サイトを介して Microsoft との間でやりとりされます。 Microsoft は、最適なパフォーマンスを得るために SDN ベースのルーティング ポリシーを使用して、グローバル ネットワークでトラフィックをルーティングします。

    この種類の冗長性では、常にエンド ユーザーに最も近い Microsoft Edge PoP を選択することで最短のルーティング パスを使用して、お客様が必ず Microsoft から 1 ネットワーク ホップ (AS ホップ) 離れているようにします。

   ![geo 冗長](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>最適なルーティング

次のルーティング方法が推奨されます。

-  **コールド ポテト ルーティング**

   ソフトウェアで定義されたコールド ポテト ルーティング方法は、Microsoft クラウドからのネットワーク トラフィックを制御します。 これにより、トラフィックは、可能な限り宛先に近くなるまで、高容量で低待機時間の、信頼性の高い Microsoft グローバル ネットワークにとどまります。
   
   コールド ポテト方法を使用しないルーティングは、ホット ポテト ルーティングと呼ばれます。 ホット・ポテト ルーティングを使用すると、Microsoft クラウドから送信されたトラフィックがインターネット経由で送信されます。

   ![コールド ポテト ルーティング](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>監視プラットフォーム

   サービスの監視は、お客様のトラフィックとルーティングを分析するために用意されており、次の機能を備えています。 

-  **インターネット BGP ルーティングの異常の検出**
          
   このサービスは、お客様のプレフィックスへのルーティングのハイジャックなどのルーティングの異常イベントを検出してアラートを送信するために使用されます。

-  **お客様の待機時間**

   このサービスは、お客様の所在地と Microsoft の間のルーティングのパフォーマンスを監視します。 
   
   ルーティングのパフォーマンスを測定するには、クライアントから Microsoft Edge PoP に到達するまでのラウンドトリップ時間を検証します。 お客様は、さまざまな地理的な場所の待機時間レポートを表示できます。

   監視は、サービスのパフォーマンスが低下した場合にイベントをキャプチャします。

   ![Peering Service の監視プラットフォーム](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>トラフィックの保護

ルーティングは、お客様が Peering Service に登録されたときに定義された優先パスによってのみ発生します。

Microsoft は、悪意のあるアクティビティが検出された場合でも、優先パス経由でトラフィックをルーティングすることを保証します。

BGP ルーティングの異常は、Azure portal で報告されます (存在する場合)。

## <a name="next-steps"></a>次のステップ

- Peering Service 接続の詳細については、「[Peering Service 接続](connection.md)」を参照してください。
- Peering Service 接続のテレメトリの詳細については、「[Peering Service 接続のテレメトリ](connection-telemetry.md)」を参照してください。
- サービス プロバイダー パートナーを検索するには、[Peering Service のパートナーと場所](location-partners.md)に関するページを参照してください。
- Peering Service 接続をオンボードする場合は、[Peering Service モデルのオンボード](onboarding-model.md)に関するページを参照してください。
- Azure portal を使用して接続を登録する方法については、[Azure portal を使用した Peering Service 接続の登録](azure-portal.md)に関するページを参照してください。
- テレメトリを測定する方法については、[接続のテレメトリの測定](measure-connection-telemetry.md)に関するページを参照してください。