---
title: Azure Front Door Service - Azure のアプリケーション配信スイートでの負荷分散 | Microsoft Docs
description: この記事は、Azure で勧められているアプリケーション配信スイートでの負荷分散の方法を学習するのに役立ちます。
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 3d5c0ac068a6644f3499da6c3b642a4a04408370
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879661"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Azure のアプリケーション配信スイートでの負荷分散

## <a name="introduction"></a>はじめに
Microsoft Azure では、ネットワーク トラフィックの分散や負荷分散の方法を管理するための多数のグローバル サービスおよびリージョン サービス(Traffic Manager、Front Door Service、Application Gateway、および Load Balancer) を利用できます。  Azure の多くのリージョンとゾーン アーキテクチャと共に、これらのサービスを一緒に使用することで、堅牢でスケーラブルな高パフォーマンスのアプリケーションをビルドすることができます。

![アプリケーション配信スイート ][1]
 
これらのサービスは、次の 2 つのカテゴリに分類されます。
1. **グローバル負荷分散サービス** (Traffic Manager や Front Door など)。リージョン バックエンド全体、クラウド全体、さらにはハイブリッド オンプレミス サービス全体に、エンド ユーザーからのトラフィックを分散します。 グローバル負荷分散では最も近いサービス バックエンドにトラフィックをルーティングし、サービスの信頼性やパフォーマンスの変化に対応し、ユーザーに対して常時、最高のパフォーマンスが維持されます。 
2. **リージョン負荷分散サービス** (Standard Load Balancer や Application Gateway など)。ご利用の仮想マシン (VM) またはリージョン内のゾーン サービス エンドポイント全体の仮想ネットワーク (VNET) 内のトラフィックを分散できます。

アプリケーションのグローバルおよびリージョン サービスを組み合わせることで、エンドツーエンドの信頼できるパフォーマンスに優れた安全な方法で、ユーザーとの間のトラフィックを IaaS、PaaS、またはオンプレミス サービスにルーティングすることができます。 次のセクションでは、これらの各サービスについて説明します。

## <a name="global-load-balancing"></a>グローバル負荷分散
**Traffic Manager** には、グローバルな DNS グローバル負荷分散機能が備わっています。 受信 DNS 要求を調べ、ユーザーが選択したルーティング ポリシーに従って、正常なバックエンドを使用して応答します。 ルーティング方法のオプションは次のとおりです。
- 待機時間の点で最も近いバックエンドに要求元を送信するパフォーマンスによるルーティング。
- すべてのトラフィックを 1 つのバックエンドに送信し、その他のバックエンドはバックアップとする優先順位によるルーティング。
- 各バックエンドに割り当てた重み付けに基づいてトラフィックを分散する、重み付けラウンド ロビンによるルーティング。
- 特定の地理的リージョンにある要求元がそのリージョンにマップされているバックエンドに確実に送られるようにする (たとえば、スペインからのすべての要求がフランス中部の Azure リージョンに送られるようにする) ための地理的なルーティング。
- IP アドレスの範囲をバックエンドにマップし、そこからの要求が特定のバックエンドに送信されるようにする (たとえば、企業の HQ の IP アドレス範囲から接続しているすべてのユーザーが一般ユーザーとは異なる Web コンテンツを取得するようにする) ためのサブネット ルーティング。

クライアントはそのバックエンドに直接接続されます。 Azure Traffic Manager で、バックエンドが正常でないことが検出されると、クライアントが別の正常なインスタンスにリダイレクトされます。 サービスの詳細については、[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) に関するドキュメントを参照してください。

**Azure Front Door Service** では、グローバル HTTP 負荷分散を含む動的 Web サイトの高速化 (DSA) が提供されます。  特定のホスト名、URL パス、構成されたルールについて、最も近いサービス バックエンドやリージョンへの受信 HTTP 要求ルートを調べます。  
Front Door では、Microsoft のネットワークのエッジで HTTP 要求を終了し、アクティブに調べ、アプリケーションまたはインフラストラクチャの正常性や待機時間の変化を検出します。  その後、Front Door では常に、最も高速で利用可能な (正常な) バックエンドにトラフィックをルーティングします。 サービスの詳細については、Front Door の[ルーティング アーキテクチャ](front-door-routing-architecture.md)の詳細と[トラフィックのルーティング方法](front-door-routing-methods.md)に関するページを参照してください。

## <a name="regional-load-balancing"></a>リージョン負荷分散
Application Gateway では、アプリケーション配信コントローラー (ADC) をサービスとして提供することで、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。 これにより、CPU を集中的に使用する SSL ターミネーションをお客様が Application Gateway にオフロードし、Web ファームの生産性を最適化できます。 着信トラフィックのラウンド ロビン分散、Cookie ベースのセッション アフィニティ、URL パス ベースのルーティング、単一の Application Gateway の背後で複数の Web サイトをホストする機能など、その他のレイヤー 7 ルーティング機能も用意されています。 Application Gateway は、インターネット接続ゲートウェイ、または内部的にのみ使用されるゲートウェイのいずれかとして構成できるほか、この両方を組み合わせて使用することも可能です。 Application Gateway は Azure によって完全に管理され、非常にスケーラブルで、高い可用性を備えています。 管理しやすいように診断機能とログ機能が豊富に用意されているほか、
Load Balancer は、Azure SDN スタックの重要な構成要素であり、すべての UDP と TCP プロトコル向けの高パフォーマンス、低待機時間のレイヤー 4 負荷分散サービスを備えています。 受信接続と送信接続を管理します。 負荷分散されるパブリックおよび内部のエンドポイントを構成したり、TCP を使用して受信接続をバックエンド プールの送信先にマッピングする規則や、サービスの可用性を管理するための HTTP の正常性プローブ オプションを定義したりできます。


## <a name="choosing-a-global-load-balancer"></a>グローバル ロード バランサーの選択
グローバルなルーティングのために Traffic Manager と Azure Front Door の間でグローバル ロード バランサーを選択する場合は、2 つのサービスの類似点と相違点を考慮する必要があります。   両方のサービスで以下が提供されます。
- **複数の地理的冗長性:** 1 つのリージョンがダウンした場合は、最も近いリージョンにトラフィックがシームレスにルーティングされます。アプリケーション所有者による操作は必要ありません。
- **最も近いリージョンへのルーティング:** トラフィックは自動的に最も近いリージョンにルーティングされます。

</br>次の表で、Traffic Manager と Azure Front Door Service との違いについて説明します。</br>

| Traffic Manager | Azure Front Door Service |
| --------------- | ------------------------ |
|**あらゆるプロトコル:** Traffic Manager は DNS レイヤーで動作するため、HTTP、TCP、UDP など、あらゆる種類のネットワーク トラフィックをルーティングすることができます。 | **HTTP の高速化:** Front Door では、Microsoft のネットワークのエッジでトラフィックがプロキシ処理されます。  このため、SSL ネゴシエーションの待機時間が短縮され、AFD からアプリケーションへのホット接続が使用されることで、HTTP (S) 要求での待機時間とスループットが改善されます。|
|**オンプレミス ルーティング:** DNS レイヤーでのルーティングにより、トラフィックは常にポイント間で送信されるようになります。  ブランチ オフィスからオンプレミス データセンターへのルーティングでは、直接パスを使用できます。Traffic Manager を使用する独自のネットワークでも同様です。 | **独立したスケーラビリティ:** Front Door で HTTP 要求が処理されるため、ルールと各アプリケーションのマイクロサービスの正常性に基づいて、さまざまな URL パスへの要求をさまざまなバックエンドやリージョン サービス プール (マイクロサービス) にルーティングすることができます。|
|**課金形式:** DNS ベースの課金ではユーザーに合わせてスケーリングが行われ、より多くのユーザーが使用するサービスでは、使用率上昇時の安定したコスト削減が実現します。 |**インライン セキュリティ:** Front Door ではレート制限や IP ACL 設定などのルールを有効にすることで、トラフィックがアプリケーションに到達する前にバックエンドを保護することができます。 

</br>Front Door では HTTP ワークロードに対するパフォーマンス、運用およびセキュリティ上の利点が得られるため、HTTP ワークロードでは Front Door を使用することをお勧めします。    Traffic Manager と Front Door を並行して使用することで、アプリケーションのすべてのトラフィックを処理できます。 

## <a name="building-with-azures-application-delivery-suite"></a>Azure のアプリケーション配信スイートでのビルド 
すべての Web サイト、API、サービスに地理的な冗長性を持たせ、そのユーザーへのトラフィックを、できる限り最も近い (待機時間が最も短い) 場所から配信することをお勧めします。  Traffic Manager、Front Door Service、Application Gateway、Load Balancer のサービスを組み合わせることで、地理的およびゾーン的な冗長性を構築し、信頼性、スケール、パフォーマンスを最大化できます。

次の図では、これらすべてのサービスの組み合わせを使用してグローバル Web サービスを構築するサービス例について説明します。   ここでは、アーキテクトは Traffic Manager を使用して、ファイルやオブジェクトを配信するためにグローバル バックエンドにルーティングしています。同時に、Front Door を使用して、App Service に移行されたサービスに対して /store/* のパターンと一致する URL パスをグローバルにルーティングし、その他のすべての要求をリージョンの Application Gateway にルーティングします。

リージョンでは、IaaS サービスについて、アプリケーション開発者は、/images/* のパターンと一致する URL を、Web ファームの残りではなく、VM の専用プールから提供することに決めました。

さらに、動的コンテンツを提供する既定の VM プールは、高可用性クラスターでホストされているバックエンドのデータベースと通信する必要があります。 デプロイ全体は Azure Resource Manager を通じて設定されます。

次の図は、このシナリオのアーキテクチャを示します。

![アプリケーション配信スイートの詳細なアーキテクチャ][2] 

> [!NOTE]
> この例は、Azure が提供する多数の負荷分散サービスの 1 つの例に過ぎません。 Traffic Manager、Front Door、Application Gateway、Load Balancer は、負荷分散のニーズに合わせて組み合わせて使用できます。 たとえば、SSL オフロードやレイヤー 7 の処理が必要ない場合は、Application Gateway の代わりにロード バランサーを使用できます。


## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
