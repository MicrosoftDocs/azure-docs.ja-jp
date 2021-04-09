---
title: Azure でのルーティング優先設定
description: ルーティング優先設定を使用して、トラフィックを Azure とインターネットのどちらにルーティングするかを選択する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 56fd9898065dd55b9a56547a775f7f2aa4a56156
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101693732"
---
# <a name="what-is-routing-preference"></a>ルーティング設定とは

Azure のルーティング優先設定を使用すると、Azure とインターネットにトラフィックをルーティングする方法を選択できます。 Microsoft ネットワーク経由または ISP ネットワーク (パブリック インターネット) 経由のどちらでトラフィックをルーティングするかを選択できます。 これらのオプションはそれぞれ "*コールド ポテト ルーティング*" および "*ホット ポテト ルーティング*" とも呼ばれます。 エグレス データ転送の料金は、ルーティングの選択によって異なります。 パブリック IP アドレスを作成するときに、ルーティング オプションを選択できます。 パブリック IP アドレスは、仮想マシン、仮想マシン スケール セット、インターネットに接続するロード バランサーなどのリソースに関連付けることができます。また、BLOB、Files、Web、Azure Data Lake などの Azure Storage リソースに対してルーティング優先設定を設定することもできます。 既定では、すべての Azure サービスについて、トラフィックは Microsoft グローバル ネットワーク経由でルーティングされます。

## <a name="routing-via-microsoft-global-network"></a>Microsoft グローバル ネットワーク経由のルーティング

"*Microsoft グローバル ネットワーク*" 経由でトラフィックをルーティングすると、165 以上のエッジ ポイント オブ プレゼンス (POP) が含まれ 16 万マイルを超えるファイバーに広がる世界で最大のネットワークの 1 つにトラフィックが配信されます。 ネットワークは、非常に高い信頼性と可用性を確保するために、複数の冗長ファイバー パスを使用して適切にプロビジョニングされています。 トラフィックエンジニアリングは、トラフィックに対して待機時間の短いパス選択を保証し、Premium ネットワークのパフォーマンスを提供する、ソフトウェア定義の WAN コントローラーによって管理されます。

![Microsoft グローバル ネットワーク経由のルーティング](media/routing-preference-overview/route-via-microsoft-global-network.png)

**イングレス トラフィック:** グローバル BGP エニーキャスト アナウンスにより、イングレス トラフィックはユーザーに最も近い Microsoft ネットワークに入ることが保証されます。 たとえば、シンガポールのユーザーが米国シカゴでホストされている Azure リソースにアクセスした場合、トラフィックはシンガポールのエッジ POP で Microsoft グローバル ネットワークに入り、シカゴでホストされているサービスまで Microsoft ネットワーク上を転送されます。

**エグレス トラフィック:** エグレス トラフィックは同じ原則に従います。 トラフィックは、大部分を Microsoft グローバル ネットワークで転送され、ユーザーに最も近い場所で出ます。 たとえば、Azure シカゴからのトラフィックがシンガポールのユーザーに送信される場合、トラフィックは Microsoft ネットワークでシカゴからシンガポールまで送られ、シンガポールのエッジ POP で Microsoft ネットワークから出ます。

イングレス トラフィックもエグレス トラフィックも、移動のほとんどは Microsoft グローバル ネットワーク上で行われます。 これは "*コールド ポテト ルーティング*" とも呼ばれます。


## <a name="routing-over-public-internet-isp-network"></a>パブリック インターネット経由のルーティング (ISP ネットワーク)

新しいルーティングの選択である "*インターネット ルーティング*" では、Microsoft グローバル ネットワーク上の移動が最小限に抑えられ、トランジット ISP ネットワークを使用してトラフィックがルーティングされます。 このコストが最適化されたルーティング オプションでは、他のクラウド プロバイダーと同等のネットワーク パフォーマンスが提供されます。

![パブリック インターネット経由のルーティング](media/routing-preference-overview/route-via-isp-network.png)

**イングレス トラフィック:** イングレス パスでは "*ホット ポテト ルーティング*" が使用されます。これは、トラフィックがホステッド サービスのリージョンに最も近い Microsoft ネットワークに入ることを意味します。 たとえば、シンガポールのユーザーがシカゴでホストされている Azure リソースにアクセスする場合、トラフィックはパブリック インターネット経由で送信され、シカゴで Microsoft グローバル ネットワークに入ります。

**エグレス トラフィック:** エグレス トラフィックは同じ原則に従います。 トラフィックは、サービスがホストされているのと同じリージョンで Microsoft ネットワークから出ます。 たとえば、Azure シカゴのサービスからのトラフィックがシンガポールのユーザーに送信される場合、トラフィックはシカゴで Microsoft ネットワークから出て、パブリック インターネット経由でシンガポールのユーザーに転送されます。

## <a name="supported-services"></a>サポートされているサービス

ルーティング優先設定として "Microsoft グローバル ネットワーク" が選択されたパブリック IP アドレスは、任意の Azure サービスに関連付けることができます。 一方、ルーティング優先設定として **インターネット** が選択されたパブリック IP アドレスは、次の Azure リソースに関連付けることができます。

* 仮想マシン
* 仮想マシン スケール セット
* Azure Kubernetes Service (AKS)
* インターネットに接続するロード バランサー
* Application Gateway
* Azure Firewall

ストレージの場合、プライマリ エンドポイントでは常に **Microsoft グローバル ネットワーク** が使用されます。 セカンダリ エンドポイントでは、トラフィック ルーティングとして **インターネット** を選択できます。 サポートされているストレージ サービスは次のとおりです。

* BLOB
* ファイル
* Web
* Azure Data Lake

## <a name="pricing"></a>価格
両方のオプションの価格差は、インターネット エグレス データ転送の価格に反映されます。 **Microsoft グローバル ネットワーク** 経由のルーティングのデータ転送料金は、現在のインターネット エグレス料金と同じです。 最新の価格の情報については、[Azure 帯域幅の価格に関するページ](https://azure.microsoft.com/pricing/details/bandwidth/)を参照してください。

## <a name="limitations"></a>制限事項


* ルーティング優先設定は、パブリック IP アドレスのゾーン冗長 Standard SKU とのみ互換性があります。 パブリック IP アドレスの Basic SKU はサポートされていません。
* 現在、ルーティング優先設定では IPv4 のパブリック IP アドレスのみがサポートされています。 IPv6 のパブリック IP アドレスはサポートされていません。


## <a name="next-steps"></a>次のステップ

* [Azure PowerShell を使用して VM 用にルーティング優先設定を構成する](configure-routing-preference-virtual-machine-powershell.md)
* [Azure CLI を使用して VM 用にルーティング優先設定を構成する](configure-routing-preference-virtual-machine-cli.md)
