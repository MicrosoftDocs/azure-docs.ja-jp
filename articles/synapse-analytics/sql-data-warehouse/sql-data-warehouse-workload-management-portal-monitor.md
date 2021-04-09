---
title: ワークロード管理ポータル監視
description: Azure Synapse Analytics でのワークロード管理ポータル監視に関するガイダンス。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/01/2021
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 702cc1fc8b135b2eb4af9106a81946873918c4fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694395"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure Synapse Analytics - ワークロード管理ポータル監視

この記事では、[ワークロード グループ](sql-data-warehouse-workload-isolation.md#workload-groups)のリソース使用率とクエリ アクティビティを監視する方法について説明します。
Azure メトリックス エクスプローラーの構成方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../../azure-monitor/essentials/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)」を参照してください。  システム リソースの消費を監視する方法の詳細については、Azure Synapse Analytics 監視に関するドキュメントの「[リソース使用率](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization)」セクションを参照してください。
ワークロード管理を監視するために用意されているワークロード グループ メトリックには、リソース割り当てとクエリ アクティビティの 2 つの異なるカテゴリがあります。  これらのメトリックは、ワークロード グループ別に分割およびフィルター処理できます。  メトリックは、システム定義メトリック (リソース クラス ワークロード グループ) であるか、([CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文を使用してユーザーによって作成された) ユーザー定義メトリックであるかに基づいて、分割およびフィルター処理できます。

## <a name="workload-management-metric-definitions"></a>ワークロード管理メトリックの定義

|メトリックの名前                    |説明  |集計の種類 |
|-------------------------------|-------------|-----------------|
|Effective cap resource percent (有効な上限リソース割合) | "*Effective cap resource percent (有効な上限リソース割合)* " は、他のワークロード グループに割り当てられた "*Effective min resource percentage (有効な最小リソース割合)* " が考慮された、ワークロード グループがアクセスできるリソースの割合に対するハード制限です。 "*Effective cap resource percent (有効な上限リソース割合)* " メトリックを構成するには、[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文で `CAP_PERCENTAGE_RESOURCE` パラメーターを使用します。  有効な値については、こちらで説明されています。<br><br>たとえば、ワークロード グループ `DataLoads` が `CAP_PERCENTAGE_RESOURCE` = 100 で作成され、別のワークロード グループが Effective min resource percentage (有効な最小リソース割合) を 25% として作成された場合、`DataLoads` ワークロード グループの "*Effective cap resource percent (有効な上限リソース割合)* " は 75% になります。<br><br>"*Effective cap resource percent (有効な上限リソース割合)* " によって、ワークロード グループが達成できるコンカレンシー (および潜在的なスループット) の上限が決まります。  "*Effective cap resource percent (有効な上限リソース割合)* " メトリックによって現在レポートされている値を超える追加のスループットが必要な場合は、`CAP_PERCENTAGE_RESOURCE` を大きくするか、他のワークロード グループの `MIN_PERCENTAGE_RESOURCE` を小さくするか、インスタンスをスケールアップしてリソースを追加します。  `REQUEST_MIN_RESOURCE_GRANT_PERCENT` を小さくするとコンカレンシーが向上しますが、全体的なスループットが向上するとは限りません。| Min、Avg、Max |
|Effective min resource percent (有効な最小リソース割合) |"*Effective min resource percent (有効な最小リソース割合)* " は、サービス レベルの最小値が考慮された、ワークロード グループ用に予約および分離されるリソースの最小割合です。  Effective min resource percent (有効な最小リソース割合) メトリックを構成するには、[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文で `MIN_PERCENTAGE_RESOURCE` パラメーターを使用します。  有効な値については、[こちら](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values)で説明されています。<br><br>このメトリックがフィルター処理および分割されていない場合は、Sum 集計型を使用して、システム上で構成されているワークロードの分離の合計を監視します。<br><br>"*Effective min resource percent (有効な最小リソース割合)* " によって、ワークロード グループが達成できる保証されたコンカレンシー (および保証されたスループット) の下限が決まります。  "*Effective min resource percent (有効な最小リソース割合)* " メトリックによって現在レポートされている値を超える追加の保証されたリソースが必要な場合は、ワークロード グループに対して構成されている `MIN_PERCENTAGE_RESOURCE` パラメーターを大きくします。  `REQUEST_MIN_RESOURCE_GRANT_PERCENT` を小さくするとコンカレンシーが向上しますが、全体的なスループットが向上するとは限りません。 |Min、Avg、Max|
|Workload group active queries (ワークロード グループのアクティブなクエリ)  |このメトリックでは、ワークロード グループ内のアクティブなクエリがレポートされます。  フィルター処理も分割も適用しないでこのメトリックを使用すると、システム上で実行されているすべてのアクティブなクエリが表示されます。|SUM         |
|Workload group allocation by cap resource percent (上限リソース割合別のワークロード グループの割り当て) |このメトリックは、ワークロード グループあたりの "*Effective cap resource percent (有効な上限リソース割合)* " に対するリソースの割り当て割合を表示します。  このメトリックでは、ワークロード グループの有効使用率がわかります。<br><br>"*Effective cap resource percent (有効な上限リソース割合)* " が 75%、`REQUEST_MIN_RESOURCE_GRANT_PERCENT` が 25% に構成されているワークロード グループ `DataLoads` があるとします。  このワークロード グループで単一のクエリが実行されていた場合、`DataLoads` にフィルター処理された "*Workload group allocation by cap resource percent (上限リソース割合別のワークロード グループの割り当て)* " 値は、33% (25% / 75%) になります。<br><br>このメトリックは、ワークロード グループの使用率を特定するために使用します。  100% に近い値は、ワークロード グループで使用できるすべてのリソースが使用されていることを示します。  さらに、同じワークロード グループの "*Workload group queued queries (ワークロード グループのキューに登録されたクエリ) メトリック*" にゼロより大きい値が示されている場合は、追加のリソースが割り当てられるとそれがワークロード グループによって使用される可能性があります。  逆に、このメトリックが一貫して低く、"*Workload group active queries (ワークロード グループのアクティブなクエリ)* " が低い場合、ワークロード グループは使用されていません。  この状況は、"*Effective cap resource percent (有効な上限リソース割合)* " がゼロより大きい場合に特に問題となります。それは、[使用率が低いワークロードの分離](#underutilized-workload-isolation)を示すからです。|Min、Avg、Max |
|Workload group allocation by system percent (システム割合別のワークロード グループの割り当て) | このメトリックは、システム全体に対するリソースの割り当て割合を表示します。<br><br>`REQUEST_MIN_RESOURCE_GRANT_PERCENT` が 25% に構成されているワークロード グループ `DataLoads` があるとします。  このワークロード グループで単一のクエリが実行されていた場合、`DataLoads` にフィルター処理された "*Workload group allocation by system percent (システム割合別のワークロード グループの割り当て)* " 値は、25% (25% / 100%) になります。|Min、Avg、Max |
|Workload group query timeouts (ワークロード グループのクエリのタイムアウト) |タイムアウトしたワークロード グループのクエリ。このメトリックでクエリのタイムアウトがレポートされるのは、クエリの実行が開始された場合だけです (ロックまたはリソースの待機による待ち時間は含まれません)。<br><br>クエリのタイムアウトを構成するには、[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文で `QUERY_EXECUTION_TIMEOUT_SEC` パラメーターを使用します。  値を大きくすると、クエリのタイムアウトの回数が減る可能性があります。<br><br>ワークロード グループの `REQUEST_MIN_RESOURCE_GRANT_PERCENT` パラメーターを大きくして、タイムアウトの数を減らし、クエリごとにより多くのリソースを割り当てることを検討してください。  `REQUEST_MIN_RESOURCE_GRANT_PERCENT` を大きくするとワークロード グループのコンカレンシーの量が低下することに注意してください。 |SUM |
|Workload group queued queries (ワークロード グループのキューに登録されたクエリ) | 現在キューに登録されて実行されるのを待機している、ワークロード グループのキュー。  リソースまたはロックを待機しているクエリがキューに登録されます。<br><br>クエリは、さまざまな理由で待機している可能性があります。  システムが過負荷状態になり、コンカレンシー要求が使用可能な量を超えると、クエリはキューに登録されます。<br><br>[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ステートメントの `CAP_PERCENTAGE_RESOURCE` パラメーターを大きくして、より多くのリソースをワークロード グループに追加することを検討してください。  `CAP_PERCENTAGE_RESOURCE` が "*Effective cap resource percent (有効な上限リソース割合)* " メトリックより大きい場合、他のワークロード グループに対して構成されているワークロードの分離が、このワークロード グループに割り当てられているリソースに影響を与えています。  他のワークロード グループの `MIN_PERCENTAGE_RESOURCE` を小さくすることを検討するか、インスタンスをスケールアップしてリソースを追加してください。 |SUM |

## <a name="monitoring-scenarios-and-actions"></a>監視シナリオとアクション

トラブルシューティングのためのワークロード管理メトリックの使用法を明確に示す一連のグラフ構成と、問題に対処するための関連アクションを次に示します。

### <a name="underutilized-workload-isolation"></a>使用率が低いワークロードの分離

次のワークロード グループと分類子の構成を考えてみましょう。ここでは、`wgPriority` という名前のワークロード グループが作成され、`wcCEOPriority` ワークロード分類子を使用してそれに *TheCEO* `membername` がマップされています。  `wgPriority` ワークロード グループには、25% のワークロードの分離が構成されています (`MIN_PERCENTAGE_RESOURCE` = 25)。  *TheCEO* によって送信される各クエリには、システム リソースの 5% が割り当てられます (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5)。

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

下のグラフは、次のように構成されています。<br>
メトリック 1: "*Effective min resource percent (有効な最小リソース割合)* " (Avg 集計、`blue line`)<br>
メトリック 2: "*Workload group allocation by system percent (システム割合別のワークロード グループの割り当て)* " (Avg 集計、`purple line`)<br>
フィルター: [ワークロード グループ] = `wgPriority`<br>
![グラフのスクリーンショット。2 つのメトリックとフィルターがあります。](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)
このグラフは、25% のワークロードの分離に対して平均で 10% しか使用されていないことを示しています。  この場合、`MIN_PERCENTAGE_RESOURCE` パラメーター値を 10 または 15 に小さくすると、システム上の他のワークロードでリソースを使用できるようになります。

### <a name="workload-group-bottleneck"></a>ワークロード グループのボトルネック

次のワークロード グループと分類子の構成を考えてみましょう。ここでは、`wgDataAnalyst` という名前のワークロード グループが作成され、`wcDataAnalyst` ワークロード分類子を使用してそれに *DataAnalyst* `membername` がマップされています。  `wgDataAnalyst` ワークロード グループには、6% のワークロードの分離 (`MIN_PERCENTAGE_RESOURCE` = 6) と、9% のリソースの上限 (`CAP_PERCENTAGE_RESOURCE` = 9) が構成されています。  *DataAnalyst* によって送信される各クエリには、システム リソースの 3% が割り当てられます (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3)。

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

下のグラフは、次のように構成されています。<br>
メトリック 1: "*Effective cap resource percent (有効な上限リソース割合)* " (Avg 集計、`blue line`)<br>
メトリック 2: "*Workload group allocation by cap resource percent (上限リソース割合別のワークロード グループの割り当て)* " (Avg 集計、`purple line`)<br>
メトリック 3: "*Workload group queued queries (ワークロード グループのキューに登録されたクエリ)* " (Sum 集計、`turquoise line`)<br>
フィルター: [ワークロード グループ] = `wgDataAnalyst`<br>
![グラフのスクリーンショット。3 つのメトリックとフィルターがあります。](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)
このグラフは、9% のリソースの上限に対してこのワークロード グループの使用率が 90% を超えていることを示しています ("*Workload group allocation by cap resource percent (上限リソース割合別のワークロード グループの割り当て) メトリック*" より)。  "*Workload group queued queries (ワークロード グループのキューに登録されたクエリ) メトリック*" に示されているように、クエリが常にキューに登録されています。  この場合、`CAP_PERCENTAGE_RESOURCE` を大きくして 9% を超える値にすると、より多くのクエリを同時に実行できるようになります。  `CAP_PERCENTAGE_RESOURCE` を大きくする場合は、使用可能なリソースが十分にあり、他のワークロード グループによって分離されていないことが前提となります。  "*Effective cap resource percent (有効な上限リソース割合) メトリック*" を確認して、上限が引き上げられていることを確認します。  さらにスループットが必要な場合は、`REQUEST_MIN_RESOURCE_GRANT_PERCENT` を大きくして 3 を超える値にすることも検討してください。  `REQUEST_MIN_RESOURCE_GRANT_PERCENT` を大きくすると、クエリの実行速度が向上する可能性があります。

## <a name="next-steps"></a>次のステップ

- [クイック スタート: T-SQL を使用してワークロードの分離を構成する](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [リソース使用率の監視](sql-data-warehouse-concept-resource-utilization-query-activity.md)
