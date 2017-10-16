---
title: "Azure Traffic Manager の Traffic View | Microsoft Docs"
description: "Traffic Manager の Traffic View の概要"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: f1ab98ab173edc794bfabdf55d950ac689a37c0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager の Traffic View

>[!NOTE]
>Traffic Manager の Traffic View 機能はパブリック プレビューであり、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 機能はサポート対象ではなく、機能が制限されることもあります。また、Azure の場所によっては、利用できない場合もあります。 この機能の可用性と状態に関する最新の通知については、[Azure Traffic Manager の更新情報](https://azure.microsoft.com/updates/?product=traffic-manager)に関するページをご覧ください。

Traffic Manager では、プロファイルの作成時に設定したルーティング方法に基づいてエンド ユーザーが正常なエンドポイントに送られるように DNS レベルのルーティングが提供されます。 これは、(DNS リゾルバーの細分性レベルで) でユーザー ベースとそのトラフィック パターンのビューを Traffic Manager に提供します。 Traffic View を有効にすると、この情報が処理されて、すぐに実施可能な洞察が提供されます。 

Traffic View を使用すると、次の情報を把握できます。
- ユーザー ベースの場所 (ローカル DNS リゾルバー レベルの細分性まで)。
- これらのリージョンから発生するトラフィックの量。
- これらのユーザーが経験した代表的な待機時間。
- これらの各ユーザー ベースからエンドポイントがある Azure リージョンへの特定のトラフィック パターンの詳細。 

たとえば、Traffic View を使用して、大量のトラフィックがあり長い待機時間が発生しているリージョンを把握することができます。 次に、この情報を使用して、これらのユーザーの待機時間が短くなるように新しい Azure リージョンへのフットプリントの拡大を計画できます。

## <a name="how-traffic-view-works"></a>Traffic View のしくみ

Traffic View は、この機能が有効になっているプロファイルで過去 7 日間に受信した着信クエリを Traffic Manager で参照することで機能します。 この情報から、ユーザーの場所を表すために使用される DNS リゾルバーのソース IP が抽出されます。 これらは、Traffic Manager によって管理される IP アドレスの地理的な情報を使用して DNS リゾルバー レベルの細分性でグループ化され、ユーザー ベース リージョンが作成されます。 Traffic Manager は、クエリのルーティング先の Azure リージョンを参照し、それらのリージョンからユーザーのトラフィック フロー マップを構築します。
次の手順では、Traffic Manager は、さまざまなエンド ユーザー ネットワークについて管理するネットワーク インテリジェンス待機時間テーブルとの Azure リージョン マッピングにユーザー ベース リージョンを関連付けて、Azure リージョンへの接続時にそれらのリージョンのユーザーが経験する平均待機時間を把握します。 これらすべての計算は、提示される前にローカル DNS リゾルバー IP レベルごとに表形式化されます。 任意の分析ワークフローでこの情報を処理することができ、この情報を CSV ファイルとしてダウンロードすることもできます。
Traffic View を使用する場合は、提示される洞察の作成に使用されたデータ ポイントの数に基づいて課金されます。 現在使用されている唯一のデータ ポイント タイプは、Traffic Manager プロファイルに対して受信したクエリです。 価格の詳細については、[Traffic Manager の価格ページ](https://azure.microsoft.com/pricing/details/traffic-manager/)をご覧ください。


## <a name="next-steps"></a>次のステップ

- [Traffic Manager のしくみ](traffic-manager-overview.md)
- Traffic Manager でサポートされている [トラフィック ルーティング方法](traffic-manager-routing-methods.md) の詳細を確認する。
- [Traffic Manager プロファイルの作成](traffic-manager-create-profile.md)

