---
title: Azure Traffic Manager のトラフィック ビュー
description: この概要では、Traffic Manager のトラフィック ビューのしくみについて説明します。
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743051"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager の Traffic View

Traffic Manager では、DNS (ドメイン ネーム システム) レベルのルーティングが提供されます。 このサービスを使用すると、選択したルーティング方法に基づいて、エンド ユーザーが正常なエンドポイントに誘導されるようにすることができます。 トラフィック ビューは、(DNS リゾルバーの細分性レベルで) ユーザー ベースとそのトラフィック パターンのビューを Traffic Manager に提供します。 Traffic View を有効にすると、この情報が処理されて、すぐに実施可能な洞察が提供されます。 

Traffic View を使用すると、次の情報を把握できます。
- ユーザー ベースの場所 (ローカル DNS リゾルバー レベルの細分性まで)。
- これらのリージョンから送信されるトラフィックの量 (Azure Traffic Manager によって処理される DNS クエリとして監視される) を表示します。
- これらのユーザーが経験した代表的な待ち時間を洞察します。
- これらの各ユーザー ベースからエンドポイントがある Azure リージョンへの特定のトラフィック パターンの詳細。 

たとえば、トラフィック ビューを使用して、大量のトラフィックがあり長い待機時間が発生しているリージョンを把握できます。 その場合、この情報を使用して、新しい Azure リージョンへのフットプリントの拡大を計画します。 このようにすると、ユーザーの待機時間が短くなります。

## <a name="how-traffic-view-works"></a>Traffic View のしくみ

トラフィック ビューは、あるプロファイルについて過去 7 日間に受信した受信クエリを調べることで機能します。 この受信クエリの情報から、ユーザーの位置を表すために使用される DNS リゾルバーのソース IP がトラフィック ビューによって抽出されます。 この情報は、ユーザー ベースのリージョンを作成するために、DNS リゾルバー レベルでグループ化されます。 Traffic Manager で、IP アドレスの地理情報が保持されます。 その後、Traffic Manager は、クエリのルーティング先の Azure リージョンを参照し、そのリージョンからユーザーのトラフィック フロー マップを構築します。
 
次の段階で、Traffic Manager は、ユーザー ベース リージョンから Azure リージョンへのマッピングをネットワーク インテリジェンス待機時間テーブルと関連付けます。 このテーブルは、さまざまなエンドユーザー ネットワークについて、Azure リージョンに接続するときに、そのリージョンのユーザーが経験した平均待機時間を把握するために保守されます。 その後、これらすべての計算がローカル DNS リゾルバー IP レベルごとに結合されたうえで提示されます。 この情報は、さまざまな方法で利用することができます。

トラフィック ビュー データの更新頻度は、複数の内部サービス変数によって変わってきます。 ただし、データは 24 時間に 1 回更新されます。

>[!NOTE]
>トラフィック ビューに記載される待機時間は、エンド ユーザーと、エンド ユーザーが接続した Azure リージョンとの間の代表的な待機時間を表したものであり、DNS ルックアップの待機時間ではありません。 トラフィック ビューでは、ローカル DNS リゾルバーと、クエリがルーティングされる Azure リージョン間の待機時間が可能な限り見積もられ、使用できるデータが十分でない場合、返される待機時間は null になります。 

## <a name="visual-overview"></a>視覚的な概要

Traffic Manager のページで **[トラフィック ビュー]** セクションに移動すると、地理的マップにトラフィック ビューの分析情報がオーバーレイ表示されます。 このマップは、ユーザー ベースと、Traffic Manager プロファイルのエンドポイントに関する情報を提供しています。

![Traffic Manager トラフィック ビューの地理的ビュー][1]

### <a name="user-base-information"></a>ユーザー ベースの情報

位置情報が利用可能なローカル DNS リゾルバーは、マップに表示されます。 DNS リゾルバーの色は、Traffic Manager のクエリでその DNS リゾルバーを使用したエンドユーザーが経験した平均待機時間を表します。

マップ内で DNS リゾルバーの位置にポインターを合わせると、次の情報が表示されます。
- DNS リゾルバーの IP アドレス
- そこから Traffic Manager によって観察される DNS クエリ トラフィックの量
- DNS リゾルバーからのトラフィックがルーティングされたエンドポイント (エンドポイントと DNS リゾルバーの間の線) 
- その位置からエンドポイントまでの平均待機時間 (この 2 つを結ぶ線の色)

### <a name="endpoint-information"></a>エンドポイント情報

エンドポイントが配置されている Azure リージョンは、マップでは青い点として表示されます。 エンドポイントが外部にあり、Azure リージョンがそれにマップされていない場合、マップの上部に表示されます。 任意のエンドポイントを選択すると、そのエンドポイントに送られたトラフィックの転送元のさまざまな位置が (使用された DNS リゾルバーに基づいて) 表示されます。 接続は、エンドポイントと DNS リゾルバーの位置を結ぶ線として表示されます。 これは、そのペア間の代表的な待機時間に従って色付けされます。 エンドポイントの名前と、それが実行されている Azure リージョンを確認できます。 この Traffic Manager プロファイルによってそちらに送られる要求の合計量も表示されます。


## <a name="tabular-listing-and-raw-data-download"></a>表形式の一覧と生データのダウンロード

Azure Portal ポータルでは、トラフィック ビューのデータが表形式で表示されます。 DNS リゾルバー IP/エンドポイントのペアごとにエントリがあり、以下が表示されます。

* DNS リゾルバーの IP アドレス。
* 名前。
* エンドポイントが配置されている Azure リージョンの地理的な場所 (提供可能な場合)。
* そのエンドポイントに対する、その DNS リゾルバーに関連付けられている要求の量。
* その DNS を使用するエンド ユーザーに関連付けられている代表的な待機時間 (提供可能な場合)。 

また、トラフィック ビューのデータを CSV ファイルとしてダウンロードすることもでき、任意の分析ワークフローの一部としてそのファイルを使用できます。

## <a name="billing"></a>課金

トラフィック ビューを使用すると、提示される分析情報の作成に使用されたデータ ポイントの数に基づいて課金されます。 現在使用されている唯一のデータ ポイントの種類は、Traffic Manager プロファイルに対して受信したクエリです。 価格の詳細については、[Traffic Manager の価格ページ](https://azure.microsoft.com/pricing/details/traffic-manager/)をご覧ください。

## <a name="faqs"></a>FAQ

* [Traffic View は何をしますか。](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [Traffic View を使用することには、どのようなメリットがありますか。](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [Traffic View は、Azure Monitor で使用できる Traffic Manager メトリックとは、どのように異なりますか。](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Traffic View は EDNS クライアント サブネット情報を使用しますか。](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [Traffic View は何日分のデータを使用しますか。](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [Traffic View は外部エンドポイントをどのように処理しますか。](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [サブスクリプションのプロファイルごとに Traffic View を有効にする必要がありますか。](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Traffic View をオフにするには、どうすればよいですか。](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [Traffic View はどのように課金されますか。](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>次のステップ

- [Traffic Manager のしくみ](traffic-manager-overview.md)
- Traffic Manager でサポートされている [トラフィック ルーティング方法](traffic-manager-routing-methods.md) の詳細を確認する。
- [Traffic Manager プロファイルの作成](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png