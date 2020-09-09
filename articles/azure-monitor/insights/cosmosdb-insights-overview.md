---
title: Azure Monitor for Cosmos DB で Azure Cosmos DB を監視する | Microsoft Docs
description: この記事では、Cosmos DB の所有者が CosmosDB アカウントのパフォーマンスと使用状況の問題をすばやく把握できる Azure Monitor for Cosmos DB の機能について説明します。
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 8043dfb7fdda972d4ede6798d07355936b23bf93
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86498918"
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

ページの上部にある **[エラー]** を選択すると、ブック テンプレートの**エラー**部分が表示されます。 要求数の合計が、それらの要求を構成する応答の配分と共に表示されます。

![HTTP 要求タイプごとの内訳を含むエラーのスクリーンショット](./media/cosmosdb-insights-overview/failures.png)

| コード      |  説明       | 
|-----------|:--------------------|
| `200 OK`  | 次のいずれかの REST 操作が成功しました。 </br>- リソースに対する GET。 </br> - リソースに対する PUT。 </br> - リソースに対する POST。 </br> - ストアド プロシージャ リソースに対する POST でのストアド プロシージャの実行。|
| `201 Created` | リソースを作成するための POST 操作が成功しました。 |
| `404 Not Found` | もう存在していないリソースを操作しようとしています。 たとえば、リソースは既に削除されている可能性があります。 |

詳細な状態コード一覧については、[Azure Cosmos DB HTTP 状態コードに関する記事](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)を参照してください。

### <a name="capacity"></a>容量

ページの上部にある **[容量]** を選択すると、ブック テンプレートの**容量**部分が表示されます。 保有するドキュメント数、時間の経過に伴うドキュメントの増加、データ使用状況、利用可能な残りストレージ容量の合計が表示されます。  これは、ストレージとデータ使用率の潜在的な問題を特定する目的で役立てることができます。

![[容量] ブック](./media/cosmosdb-insights-overview/capacity.png) 

概要ブックと同様、 **[サブスクリプション]** 列で Azure Cosmos DB リソースの横にあるドロップダウンを選択すると、データベースを構成する個々のコンテナーごとの内訳が表示されます。

### <a name="operations"></a>操作 

ページの上部にある **[操作]** を選択すると、ブック テンプレートの**操作**部分が表示されます。 送信された要求の種類ごとに要求の内訳を確認できます。 

以下の例を見ると、`eastus-billingint` は、読み取り要求の大部分を受け取っていますが、upsert 要求と作成要求はごくわずかであることがわかります。 `westeurope-billingint` は、要求の観点から言えば読み取りのみですが、現在このブックがパラメーターでスコープ設定されている時間範囲は、少なくとも過去 4 時間となります。

![操作ブック](./media/cosmosdb-insights-overview/operation.png) 

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

* [メトリック アラート](../platform/alerts-metric.md)と[サービス正常性通知](../../service-health/alerts-activity-log-service-notifications-portal.md)を構成して、問題の検出に役立つ自動アラートを設定します。

* ブックがサポートするように設計されているシナリオ、新規レポートの作成方法と既存レポートのカスタマイズ方法などについては、「[Azure Monitor ブックを使用した対話型レポートの作成](../platform/workbooks-overview.md)」で学習してください。
