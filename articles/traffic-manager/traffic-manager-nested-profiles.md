---
title: Azure 内の入れ子になった Traffic Manager プロファイル
titleSuffix: Azure Traffic Manager
description: この記事では、Azure Traffic Manager の "入れ子になったプロファイル" 機能について説明します。
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: duau
ms.openlocfilehash: 5f2aa3d05d349880b5eb2d35a2c58af0741b9855
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185582"
---
# <a name="nested-traffic-manager-profiles"></a>入れ子になった Traffic Manager プロファイル

Traffic Manager では、さまざまなトラフィック ルーティング方法を使用して、Traffic Manager が各エンド ユーザーからのトラフィックを受信するエンドポイントを選択する方法を制御できます。 詳細については、「 [Traffic Manager のトラフィック ルーティング方法](traffic-manager-routing-methods.md)」を参照してください。

Traffic Manager プロファイルごとに 1 つのトラフィック ルーティング方法を指定します。 ただし、単一の Traffic Manager プロファイルで提供されるものよりも高度なトラフィック ルーティングが必要になるシナリオもあります。 Traffic Manager プロファイルを入れ子にして、複数のトラフィック ルーティング方法の利点を組み合わせることができます。 入れ子になったプロファイルを使用すると、既定の Traffic Manager の動作をオーバーライドして、より大規模でより複雑なアプリケーションのデプロイに対応することができます。

次の例で、入れ子になった Traffic Manager プロファイルをさまざまなシナリオで使用する方法を説明します。

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>例 1: "パフォーマンス" トラフィック ルーティングと "加重" トラフィック ルーティングの組み合わせ

アプリケーションを Azure リージョンの米国西部、西ヨーロッパ、東アジアにデプロイしたとします。 Traffic Manager の "パフォーマンス" トラフィック ルーティング方法を使用して、ユーザーに最も近いリージョンにトラフィックを振り分けます。

![単一の Traffic Manager プロファイル][4]

次に、サービスの更新を広くロールアウトする前にテストする必要があるとします。 トラフィックのごく一部をテスト デプロイに送信するには、"重み付け" トラフィック ルーティング方法を使用する必要があります。 西ヨーロッパに既存の運用環境デプロイと共にテスト デプロイを設定します。

単一プロファイルで "重み付け" と "パフォーマンス" の両方のトラフィック ルーティングを組み合わせることはできません。 このシナリオをサポートするには、西ヨーロッパの 2 つのエンドポイントと "重み付け" トラフィック ルーティング方法を使用して Traffic Manager プロファイルを作成します。 次に、この "子" プロファイルを "親" プロファイルにエンドポイントとして追加します。 親プロファイルでは、パフォーマンス トラフィック ルーティング方法が引き続き使用され、エンドポイントとして他のグローバル デプロイが含まれます。

次の図にこの例を示します。

![入れ子になった Traffic Manager プロファイル][2]

この構成では、親プロファイルを使用して転送されるトラフィックによって、通常どおりリージョン全体にトラフィックが振り分けられます。 西ヨーロッパ内では、入れ子になったプロファイルによって、割り当てられた重みに従って運用エンドポイントとテスト エンドポイントにトラフィックが分散されます。

親プロファイルで "パフォーマンス" トラフィック ルーティング方法を使用するときは、各エンドポイントに場所を割り当てる必要があります。 場所は、エンドポイントを構成するときに割り当てます。 デプロイメントに最も近い Azure リージョンを選択してください。 Azure のリージョンは、インターネット待機時間テーブルでサポートされている場所の値です。 詳細については、[Traffic Manager の "パフォーマンス" によるトラフィック ルーティング方法](traffic-manager-routing-methods.md#performance)に関する記事をご覧ください。

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>例 2: 入れ子になったプロファイルでのエンドポイントの監視

Traffic Manager は、各サービス エンドポイントの正常性をアクティブに監視します。 エンドポイントが正常でない場合、Traffic Manager は、別のエンドポイントにユーザーを送信してサービスの可用性を維持します。 このエンドポイントの監視とフェールオーバーの動作は、すべてのトラフィック ルーティング方法に適用されます。 詳細については、 [Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md)に関する記事をご覧ください。 エンドポイントの監視は、入れ子になったプロファイルに対しては異なった動作をします。 入れ子になったプロファイルを使用した場合、親プロファイルでは子の正常性チェックが直接には実行されません。 代わりに、子プロファイルのエンドポイントの正常性を使用して、子プロファイルの全体的な正常性が計算されます。 この正常性の情報は、入れ子になったプロファイルの階層の上位に伝達されます。 親プロファイルは、集計したこの正常性を使用して、子プロファイルにトラフィックを送信するかどうかを決定します。 入れ子になったプロファイルの正常性の監視について詳しくは、「[FAQ](traffic-manager-FAQs.md#traffic-manager-nested-profiles)」をご覧ください。

前の例に戻って、西ヨーロッパの運用環境デプロイで障害が発生したとします。 既定では、"子" プロファイルはすべてのトラフィックをテスト デプロイに送信します。 テスト デプロイにも障害が発生した場合、すべての子エンドポイントが正常ではないため、親プロファイルは、子プロファイルでトラフィックを受信するべきではないと判断します。 その後、親プロファイルは、他の地域にトラフィックを振り分けます。

![入れ子になったプロファイルのフェールオーバー (既定の動作)][3]

この配置で問題ない場合もあります。 ただし、限られた一部のトラフィックではなく、西ヨーロッパ向けのすべてのトラフィックが、このテスト デプロイに送信される懸念もあります。 テスト デプロイの状態に関係なく、西ヨーロッパの運用環境のデプロイが失敗した場合は、他のリージョンにフェールオーバーする必要があります。 このフェールオーバーを実現するには、親プロファイルのエンドポイントとして子プロファイルを構成するときに、"MinChildEndpoints" パラメーターを指定することができます。 このパラメーターは、子プロファイル内の利用可能なエンドポイントの最小数を決定します。 既定値は '1' です。 このシナリオでは、MinChildEndpoints の値を 2 に設定します。 このしきい値を下回ると、親プロファイルは、子プロファイル全体を利用不可と判断し、他のエンドポイントにトラフィックを送信します。

次の図はこの構成を示します。

!["MinChildEndpoints" が 2 に設定された、入れ子になったプロファイルのフェールオーバー][4]

> [!NOTE]
> "優先順位" によるトラフィック ルーティング方法では、単一のエンドポイントにすべてのトラフィックが振り分けられます。 そのため、子プロファイルの MinChildEndpoints を '1' 以外の値に設定してもほとんど意味はありません。

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>例 3: "パフォーマンス" トラフィック ルーティングにおける優先フェールオーバー リージョン

"パフォーマンス" トラフィック ルーティング方式の既定の動作では、エンドポイントが地理的に異なる場所にある場合、エンド ユーザーはネットワーク待ち時間が最も短いという意味で "最も近い" エンドポイントにルーティングされます。

西ヨーロッパのトラフィックをなるべく米国西部にフェールオーバーし、これらのエンドポイントがどちらも使用できない場合にのみ、他の場所に送信するとします。 このソリューションは、"優先順位" トラフィック ルーティング方法と子プロファイルを使用して作成することができます。

![優先フェールオーバーを使用する "パフォーマンス" トラフィック ルーティング][6]

西ヨーロッパ エンドポイントは米国西部エンドポイントよりも優先順位が高いため、どちらのエンドポイントもオンラインの場合は、すべてのトラフィックが西ヨーロッパ エンドポイントに送信されます。 西ヨーロッパで障害が発生した場合、トラフィックは米国西部に送信されます。 入れ子になったプロファイルを使用すると、西ヨーロッパと米国西部の両方で障害が発生した場合にのみ、トラフィックが東アジアに送信されます。

このパターンは、すべてのリージョンで繰り返すことができます。 親プロファイル内の 3 つのエンドポイントすべてを 3 つの子プロファイルに置き換え、それぞれにフェールオーバーの順序を優先順位付けして指定します。

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>例 4: 同じリージョンの複数のエンドポイント間での "パフォーマンス" トラフィック ルーティングの制御

特定のリージョンの複数のエンドポイントを含むプロファイルで "パフォーマンス" トラフィック ルーティング方法が使用されているとします。 既定では、そのリージョンに送信されるトラフィックは、そのリージョンの使用可能なすべてのエンドポイントに均等に分散されます。

!["パフォーマンス" トラフィック ルーティングのリージョン内トラフィック分散 (既定の動作)][7]

西ヨーロッパに複数のエンドポイントが追加されるのではなく、複数のエンドポイントが個別の子プロファイルに囲み込まれます。 この子プロファイルは、西ヨーロッパの唯一のエンドポイントとして親に追加されます。 そのリージョン内で優先順位ベースまたは重み付けトラフィック ルーティングを有効にすることで、子プロファイルの設定を使用して西ヨーロッパでのトラフィック分散を制御できます。

!["パフォーマンス" トラフィック ルーティングのカスタムのリージョン内トラフィック分散][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>例 5: エンドポイントごとの監視設定

従来のオンプレミス Web サイトから Azure でホストされる新しいクラウドベースのバージョンにトラフィックをスムーズに移行するために、Traffic Manager を使用するとします。 従来のサイトでは、ホーム ページの URI を使用して、サイトの正常性を監視します。 ただし、新しいクラウドベースのバージョンでは、その他のチェックも含まれたカスタムの監視ページ (パスは "/monitor.aspx") を実装します。

![Traffic Manager のエンドポイント監視 (既定の動作)][9]

Traffic Manager プロファイルの監視の設定は、1 つのプロファイル内のすべてのエンドポイントに適用されます。 入れ子になったプロファイルを使用する場合は、サイトごとに別の子プロファイルを使用して、それぞれ異なる監視設定を定義します。

![エンドポイントごとの設定を使用する Traffic Manager のエンドポイント監視][10]

## <a name="faqs"></a>FAQ

* [入れ子になったプロファイルを構成するにはどうすればよいですか。](./traffic-manager-faqs.md#traffic-manager-nested-profiles)

* [Traffic Manager では、何層の入れ子がサポートされますか。](./traffic-manager-faqs.md#how-many-layers-of-nesting-does-traffic-manger-support)

* [同じ Traffic Manager プロファイルに、入れ子になった子プロファイルと他の種類のエンドポイントを混在させることはできますか。](./traffic-manager-faqs.md#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [入れ子になったプロファイルに対して課金モデルはどのように適用されますか。](./traffic-manager-faqs.md#how-does-the-billing-model-apply-for-nested-profiles)

* [入れ子になったプロファイルでは、パフォーマンスへの影響はありますか。](./traffic-manager-faqs.md#is-there-a-performance-impact-for-nested-profiles)

* [Traffic Manager では、親プロファイルの入れ子になったエンドポイントの正常性をどのように計算するのですか。](./traffic-manager-faqs.md#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>次のステップ

[Traffic Manager のプロファイル](traffic-manager-overview.md)の詳細を確認する

[Traffic Manager プロファイルの作成](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png