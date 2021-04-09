---
title: Azure Monitor for Cosmos DB で Azure Cosmos DB を監視する | Microsoft Docs
description: この記事では、Cosmos DB の所有者が CosmosDB アカウントのパフォーマンスと使用状況の問題をすばやく把握できる Azure Monitor for Cosmos DB の機能について説明します。
author: lgayhardt
ms.author: lagayhar
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: d88bf65f1bd94e29bd9f60f5597d655f0040623b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725764"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>Azure Monitor for Azure Cosmos DB の探索

Azure Monitor for Azure Cosmos DB を使用すると、すべての Azure Cosmos DB リソースの全体的なパフォーマンス、障害、容量、操作の正常性を、統合された対話型エクスペリエンスで表示できます。 この記事では、この新しい監視エクスペリエンスの利点のほか、組織固有のニーズを満たすようそのエクスペリエンスに変更を加え、調整する方法をわかりやすく説明しています。   

## <a name="introduction"></a>はじめに

エクスペリエンスについて詳しく調べる前に、情報が提供および視覚化される方法を理解する必要があります。 

次のことが実現されます。

* **大規模な分析観点**: すべてのサブスクリプションの Azure Cosmos DB リソースを 1 か所で分析すると共に、関心のあるサブスクリプションとリソースに限定して評価することができます。

* **ドリルダウン分析**: 特定の Azure CosmosDB リソースについて、カテゴリ (使用率、エラー、容量、操作) ごとに問題を診断したり詳細な分析を実行したりするのに役立ちます。 これらのオプションのいずれかを選択すると、該当する Azure Cosmos DB のメトリックの詳細が表示されます。  

* **カスタマイズ可能**: このエクスペリエンスは、Azure Monitor ブック テンプレートをベースに作成されています。表示されるメトリックを変更したり、実際の制限に合わせてしきい値を変更または設定したりしてから、カスタム ブックに保存することができます。 ブック内のグラフは、Azure ダッシュボードにピン留めできます。  

この機能を使用するために、何かを有効にしたり構成したりする必要はありません。これらの Azure Cosmos DB メトリックは既定で収集されます。

>[!NOTE]
>この機能へのアクセスに対して料金はかからず、構成または有効にした Azure Monitor の基本機能に対してのみ課金されます。[Azure Monitor の価格の詳細](https://azure.microsoft.com/pricing/details/monitor/)に関するページをご覧ください。

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Azure Cosmos DB の使用率とパフォーマンスのメトリックを表示する

所有するすべてのサブスクリプションのストレージ アカウントの使用状況とパフォーマンスを確認するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 「**監視**」を検索し、 **[監視]** を選択します。

    ![[サービス] の [監視] ドロップダウンとスピードメーターのスタイル画像が表示される検索ボックスに「監視」と入力したところ](./media/cosmosdb-insights-overview/search-monitor.png)

3. **[Cosmos DB]** を選択します。

    ![Cosmos DB の概要ブックのスクリーンショット](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>概要

**[概要]** のテーブルには、インタラクティブな Azure Cosmos DB のメトリックが表示されます。 次のドロップダウン リストから選択するオプションに基づいて、結果をフィルター処理できます。

* **[サブスクリプション]** - Azure Cosmos DB リソースを含むサブスクリプションのみが一覧表示されます。  

* **[Cosmos DB]** - すべての Azure Cosmos DB リソースか、そのサブセット、またはいずれか 1 つを選択できます。

* **[時間の範囲]** - 既定では、選択結果に応じて過去 4 時間の情報が表示されます。

ドロップダウン リストの下に表示されるカウンター タイルには、選択したサブスクリプションに含まれる Azure Cosmos DB リソースの総数が集計されます。 ブックの列には、条件に基づく色分け (ヒートマップ) が示され、トランザクションのメトリックがレポートされます。 最も濃い色は最大値で、薄い色は最小値に基づいています。 

いずれかの Azure Cosmos DB リソースの横にあるドロップダウン矢印を選択すると、個々のデータベース コンテナー レベルでパフォーマンス メトリックの内訳が表示されます。

![ドロップダウンを展開すると、個々のデータベース コンテナーおよび関連するパフォーマンスの内訳が表示されます。](./media/cosmosdb-insights-overview/container-view.png)

青色で強調表示されている Azure Cosmos DB リソース名を選択すると、関連付けられている Azure Cosmos DB アカウントの既定の **[概要]** に移動します。 

### <a name="failures"></a>エラー

ページの上部にある **[エラー]** を選択すると、ブック テンプレートの **エラー** 部分が表示されます。 要求数の合計が、それらの要求を構成する応答の配分と共に表示されます。

![HTTP 要求タイプごとの内訳を含むエラーのスクリーンショット](./media/cosmosdb-insights-overview/failures.png)

| コード |  説明       | 
|-----------|:--------------------|
| `200 OK`  | 次のいずれかの REST 操作が成功しました。 </br>- リソースに対する GET。 </br> - リソースに対する PUT。 </br> - リソースに対する POST。 </br> - ストアド プロシージャ リソースに対する POST でのストアド プロシージャの実行。|
| `201 Created` | リソースを作成するための POST 操作が成功しました。 |
| `404 Not Found` | もう存在していないリソースを操作しようとしています。 たとえば、リソースは既に削除されている可能性があります。 |

詳細な状態コード一覧については、[Azure Cosmos DB HTTP 状態コードに関する記事](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)を参照してください。

### <a name="capacity"></a>容量

ページの上部にある **[容量]** を選択すると、ブック テンプレートの **容量** 部分が表示されます。 保有するドキュメント数、時間の経過に伴うドキュメントの増加、データ使用状況、利用可能な残りストレージ容量の合計が表示されます。  これは、ストレージとデータ使用率の潜在的な問題を特定する目的で役立てることができます。

![[容量] ブック](./media/cosmosdb-insights-overview/capacity.png) 

概要ブックと同様、 **[サブスクリプション]** 列で Azure Cosmos DB リソースの横にあるドロップダウンを選択すると、データベースを構成する個々のコンテナーごとの内訳が表示されます。

### <a name="operations"></a>操作

ページの上部にある **[操作]** を選択すると、ブック テンプレートの **操作** 部分が表示されます。 送信された要求の種類ごとに要求の内訳を確認できます。

以下の例を見ると、`eastus-billingint` は、読み取り要求の大部分を受け取っていますが、upsert 要求と作成要求はごくわずかであることがわかります。 `westeurope-billingint` は、要求の観点から言えば読み取りのみですが、現在このブックがパラメーターでスコープ設定されている時間範囲は、少なくとも過去 4 時間となります。

![操作ブック](./media/cosmosdb-insights-overview/operation.png)

## <a name="view-from-an-azure-cosmos-db-resource"></a>Azure Cosmos DB リソースから表示する

1. 既存の Azure Cosmos DB アカウントを検索または選択します。

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-search.png" alt-text="Azure Cosmos DB を検索します。" border="true":::

2. Azure Cosmos DB アカウントに移動したら、[監視] セクションで **[Insights (プレビュー)]** または **[ブック]** を選択して、スループット、要求、ストレージ、可用性、待機時間、システム、およびアカウント管理に関する詳細な分析を実行します。

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-overview.png" alt-text="Cosmos DB Insights の概要。" border="true":::

### <a name="time-range"></a>時間の範囲

既定では、 **[時間の範囲]** フィールドには **過去 24 時間** のデータが表示されます。 時間の範囲を変更すれば、過去 5 分間から過去7日間までの任意の期間について、データを表示することができます。 時間範囲セレクターには、 **[カスタム]** モードも含まれています。このモードでは、開始日/終了日を入力して、選択したアカウントの使用可能なデータに基づく、カスタムの時間枠を表示することができます。

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-time-range.png" alt-text="Cosmos DB の時間範囲。" border="true":::

### <a name="insights-overview"></a>Insights の概要

**[概要]** タブには、選択した Azure Cosmos DB アカウントについて、最も一般的なメトリックが表示されます。これには、以下のものが含まれます。

* 要求の合計数
* 失敗した要求 (429)
* 正規化された RU 消費量 (最大値)
* データとインデックスの使用量
* コレクション別の Cosmos DB アカウント メトリック

**要求の合計数:** このグラフには、アカウントの要求の合計が状態コード別に分類されて表示されます。 グラフの下部の欄には、その期間の合計要求数が表示されます。

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-total-requests.png" alt-text="Cosmos DB の要求の合計数グラフ。" border="true":::

**失敗した要求 (429)** : このグラフには、状態コードが 429 となっている、失敗した要求が表示されます。 グラフの下部の欄には、その期間における失敗した要求の合計数が表示されます。

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-429.png" alt-text="Cosmos DB の失敗した要求のグラフ。" border="true":::

**正規化された RU 消費量 (最大値)** : このグラフには、指定された期間を対象に、正規化された RU 消費量が 0 ～ 100% の最大パーセンテージで表示されます。

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-normalized-ru.png" alt-text="Cosmos DB の正規化された RU 消費量。" border="true":::

## <a name="pin-export-and-expand"></a>ピン留め、エクスポート、展開

セクションの右上にある画びょうアイコンを選択すると、メトリックのどのセクションでも [Azure ダッシュボード](../../azure-portal/azure-portal-dashboards.md)にピン留めできます。

![メトリック セクションのダッシュボードへのピン留めの例](./media/cosmosdb-insights-overview/pin.png)

データを Excel 形式にエクスポートするには、押しピン アイコンの左側にある下矢印アイコンを選択します。

![ブックのエクスポート アイコン](./media/cosmosdb-insights-overview/export.png)

ブックのすべてのドロップダウン ビューを展開したり折りたたんだりするには、エクスポート アイコンの左側の展開アイコンを選択します。

![ブックの展開アイコン](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>Azure Monitor for Azure Cosmos DB のカスタマイズ

このエクスペリエンスは、Azure Monitor ブック テンプレートをベースに作成されているため、 **[カスタマイズ]**  >  **[編集]** して、編集したバージョンのコピーをカスタム ブックに **[保存]** することができます。 

![カスタマイズ バー](./media/cosmosdb-insights-overview/customize.png)

ブックは、リソース グループ内の、ユーザー個人の **[個人用レポート]** セクション、またはリソース グループにアクセスできるユーザーであればだれでもアクセスできる **[共有レポート]** セクションのいずれかに保存されます。 カスタム ブックを保存した後は、ブック ギャラリーに移動して起動する必要があります。

![コマンド バーからブック ギャラリーを起動する](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>トラブルシューティング

トラブルシューティングのガイダンスについては、専用のブックベースの分析情報の[トラブルシューティングの記事](troubleshoot-workbooks.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* [メトリック アラート](../alerts/alerts-metric.md)と[サービス正常性通知](../../service-health/alerts-activity-log-service-notifications-portal.md)を構成して、問題の検出に役立つ自動アラートを設定します。

* ブックがサポートするように設計されているシナリオ、新規レポートの作成方法と既存レポートのカスタマイズ方法などについては、「[Azure Monitor ブックを使用した対話型レポートの作成](../visualize/workbooks-overview.md)」で学習してください。
