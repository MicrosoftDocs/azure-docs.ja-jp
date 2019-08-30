---
title: Azure Traffic Manager のトラフィック ビュー
description: Traffic Manager の Traffic View の概要
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 5b451378fcc14106cb8731a89bcf6ccf415d0a92
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035452"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager の Traffic View

Traffic Manager では、プロファイルの作成時に指定したルーティング方法に基づいてエンド ユーザーが正常なエンドポイントに送られるように DNS レベルのルーティングが提供されます。 トラフィック ビューは、(DNS リゾルバーの細分性レベルで) ユーザー ベースとそのトラフィック パターンのビューを Traffic Manager に提供します。 Traffic View を有効にすると、この情報が処理されて、すぐに実施可能な洞察が提供されます。 

Traffic View を使用すると、次の情報を把握できます。
- ユーザー ベースの場所 (ローカル DNS リゾルバー レベルの細分性まで)。
- これらのリージョンから送信されるトラフィックの量 (Azure Traffic Manager によって処理される DNS クエリとして監視される) を表示します。
- これらのユーザーが経験した代表的な待ち時間を洞察します。
- これらの各ユーザー ベースからエンドポイントがある Azure リージョンへの特定のトラフィック パターンの詳細。 

たとえば、Traffic View を使用して、大量のトラフィックがあり長い待機時間が発生しているリージョンを把握することができます。 次に、この情報を使用して、これらのユーザーの待機時間が短くなるように新しい Azure リージョンへのフットプリントの拡大を計画できます。

## <a name="how-traffic-view-works"></a>Traffic View のしくみ

Traffic View は、この機能が有効になっているプロファイルで過去 7 日間に受信した着信クエリを Traffic Manager で参照することで機能します。 この着信クエリの情報から、ユーザーの場所を表すために使用される DNS リゾルバーのソース IP がトラフィック ビューによって抽出されます。 これらは、Traffic Manager によって管理される IP アドレスの地理的な情報を使用して DNS リゾルバー レベルの細分性でグループ化され、ユーザー ベース リージョンが作成されます。 Traffic Manager は、クエリのルーティング先の Azure リージョンを参照し、それらのリージョンからユーザーのトラフィック フロー マップを構築します。  
次の手順では、Traffic Manager は、さまざまなエンド ユーザー ネットワークについて管理されるネットワーク インテリジェンス待機時間テーブルを使用して、ユーザー ベース リージョンを Azure リージョン マッピングに関連付けることで、Azure リージョンへの接続時にそれらのリージョンのユーザーが経験する平均待機時間を把握します。 これらすべての計算は、提示される前にローカル DNS リゾルバー IP レベルごとに結合されます。 この情報は、さまざまな方法で利用することができます。

トラフィック ビュー データの更新頻度は、複数の内部サービス変数によって変わってきます。 ただし、通常、データは 24 時間に 1 回更新されます。

>[!NOTE]
>トラフィック ビューに記載される待機時間は、エンド ユーザーと、エンド ユーザーが接続した Azure リージョンとの間の代表的な待機時間を表したものであり、DNS ルックアップの待機時間ではありません。 トラフィック ビューでは、ローカル DNS リゾルバーと、クエリがルーティングされる Azure リージョン間の待機時間が可能な限り見積もられ、使用できるデータが十分でない場合、返される待機時間は null になります。 

## <a name="visual-overview"></a>視覚的な概要

Traffic Manager のページで **[トラフィック ビュー]** セクションに移動すると、地理的マップにトラフィック ビューの詳細情報がオーバーレイ表示されています。 このマップは、ユーザー ベースと、Traffic Manager プロファイルのエンドポイントに関する情報を提供しています。

![Traffic Manager トラフィック ビューの地理的ビュー][1]

### <a name="user-base-information"></a>ユーザー ベースの情報

位置情報が利用可能なローカル DNS リゾルバーは、マップに表示されます。 DNS リゾルバーの色は、Traffic Manager のクエリでその DNS リゾルバーを使用したエンドユーザーが経験した平均待機時間を表します。

マップ内で DNS リゾルバーの位置にポインターを合わせると、次の情報が表示されます。
- DNS リゾルバーの IP アドレス
- そこから Traffic Manager によって観察される DNS クエリ トラフィックの量
- DNS リゾルバーからのトラフィックがルーティングされたエンドポイント (エンドポイントと DNS リゾルバーの間の線) 
- その位置からエンドポイントまでの平均待機時間 (この 2 つを結ぶ線の色)

### <a name="endpoint-information"></a>エンドポイント情報

エンドポイントが存在する Azure リージョンは、マップでは青い点として表示されます。 エンドポイントが外部で、Azure リージョンがマップされていない場合、マップの先頭に表示されます。 いずれかのエンドポイントをクリックすると、そのエンドポイントにトラフィックが送られてきたさまざまな転送元の位置が (使用される DNS リゾルバーに基づいて) 表示されます。 接続は、エンドポイントと DNS リゾルバーの位置との間の線として表示され、そのペアの代表的な待機時間に応じて色付けされます。 さらに、エンドポイントの名前、そのエンドポイントが実行されている Azure リージョン、Traffic Manager プロファイルによってそのエンドポイントに送られた要求の合計量を見ることができます。


## <a name="tabular-listing-and-raw-data-download"></a>表形式の一覧と生データのダウンロード

Azure Portal ポータルでは、トラフィック ビューのデータが表形式で表示されます。 DNS リゾルバー IP とエンドポイントの各ペアに関するエントリがあり、DNS リゾルバーの IP アドレス、エンドポイントが配置されている Azure リージョンの名前と地理的な位置 (使用可能な場合)、そのエンドポイントへのその DNS リゾルバーに関連付けられた要求の量、その DNS を使用しているエンドユーザーに関連付けられている代表的な待機時間 (使用可能な場合) が示されます。 また、トラフィック ビューのデータを CSV ファイルとしてダウンロードすることもでき、任意の分析ワークフローの一部としてそのファイルを使用できます。

## <a name="billing"></a>課金

Traffic View を使用する場合は、提示される洞察の作成に使用されたデータ ポイントの数に基づいて課金されます。 現在使用されている唯一のデータ ポイントの種類は、Traffic Manager プロファイルに対して受信したクエリです。 価格の詳細については、[Traffic Manager の価格ページ](https://azure.microsoft.com/pricing/details/traffic-manager/)をご覧ください。

## <a name="faqs"></a>FAQ

* [トラフィック ビューは何をしますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [トラフィック ビューを使用することには、どのようなメリットがありますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [トラフィック ビューは、Azure Monitor で使用できる Traffic Manager メトリックとは、どのように異なりますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [トラフィック ビューは EDNS クライアント サブネット情報を使用しますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [トラフィック ビューは何日分のデータを使用しますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [トラフィック ビューは外部エンドポイントをどのように処理しますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [サブスクリプションのプロファイルごとにトラフィック ビューを有効にする必要がありますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [トラフィック ビューをオフにするには、どうすればよいですか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [トラフィック ビューはどのように課金されますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>次の手順

- [Traffic Manager のしくみ](traffic-manager-overview.md)
- Traffic Manager でサポートされている [トラフィック ルーティング方法](traffic-manager-routing-methods.md) の詳細を確認する。
- [Traffic Manager プロファイルの作成](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png