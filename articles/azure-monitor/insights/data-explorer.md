---
title: Azure Monitor for Azure Data Explorer (プレビュー) | Microsoft Docs
description: この記事では、Azure Data Explorer クラスターの Azure Monitor 分析情報について説明します。
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: dcfe12b30e336863c8e112d9ad675a2f57fe48f4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179138"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure Monitor for Azure Data Explorer (プレビュー)

Azure Monitor for Azure Data Explorer (プレビュー) は、クラスターのパフォーマンス、操作、使用状況、エラーの統合ビューを提供することで、クラスターを包括的に監視できるようにします。
この記事は、Azure Monitor for Azure Data Explorer (プレビュー) をオンボードして使用する方法を理解するうえで役立ちます。

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Azure Monitor for Azure Data Explorer (プレビュー) の概要

エクスペリエンスについて見ていく前に、情報が提供および視覚化される方法を理解する必要があります。
-    **大規模な分析観点**: クエリ、インジェスト、エクスポート操作のパフォーマンスを簡単に追跡できるように、クラスターの主要メトリックのスナップショット ビューを表示します。
-   **ドリルダウン分析**: 特定の Azure Data Explorer クラスターの詳細な分析を実行できます。
-    **カスタマイズ可能**: 表示するメトリックを変更し、制限に合わせてしきい値を変更または設定して、独自のカスタム ブックとして保存できます。 ブック内のグラフは、Azure ダッシュボードにピン留めできます。

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Azure Monitor から表示する (大規模な分析観点)

Azure Monitor から、クラスターの主要なパフォーマンス メトリック (サブスクリプション内の複数のクラスターからのクエリ、インジェスト、エクスポート操作のメトリックなど) を表示し、パフォーマンスの問題の特定に役立てることができます。

すべてのサブスクリプションでのクラスターのパフォーマンスを表示するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. Azure portal の左側のペインで **[モニター]** を選択し、[Insights Hub] セクションで **[Azure Data Explorer クラスター (プレビュー)]** を選択します。

![複数のグラフを伴う概要エクスペリエンスのスクリーンショット](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>[概要] タブ

選択したサブスクリプションの **[概要]** タブでは、サブスクリプション内でグループ化されている Azure Data Explorer クラスターの対話型メトリックがテーブルに表示されます。 次のドロップダウン リストから選択するオプションに基づいて、結果をフィルター処理できます。

* [サブスクリプション] - Azure Data Explorer クラスターを含むサブスクリプションだけが一覧表示されます。

* [Azure Data Explorer クラスター] - 既定では、最大 5 つのクラスターだけが事前に選択されています。 スコープ セレクターですべてまたは複数のクラスターを選択すると、最大 200 個のクラスターが返されます。

* [時間の範囲] - 既定では、選択結果に応じて過去 24 時間の情報が表示されます。

ドロップダウン リストのカウンター タイルには、選択したサブスクリプション内の Azure Data Explorer クラスターの総数がロールアップされ、選択されている数が反映されます。 キープ アライブ、CPU、インジェスト使用率、キャッシュ使用率の各列には、条件付き色分けがあります。 オレンジで色分けされたセルには、クラスターの持続可能ではない値が含まれています。 

各メトリックが表す内容について理解を深めるために、[Azure Data Explorer のメトリック](/azure/data-explorer/using-metrics#cluster-metrics)に関するドキュメントを参照することをお勧めします。

### <a name="query-performance-tab"></a>[クエリ パフォーマンス] タブ

このタブには、クエリ期間、同時クエリの総数、スロットルされたクエリの総数が表示されます。

![[クエリ パフォーマンス] タブのスクリーンショット](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>[Ingestion Performance]\(インジェストのパフォーマンス\) タブ

このタブには、インジェストの待機時間、成功したインジェストの結果、失敗したインジェストの結果、インジェストの量、イベント ハブおよび IoT ハブの処理されたイベントが表示されます。

[![[Ingestion Performance]\(インジェストのパフォーマンス\) タブのスクリーンショット](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>[Streaming Ingest Performance]\(ストリーミング インジェストのパフォーマンス\) タブ

このタブでは、平均データ速度、平均期間、要求レートに関する情報が提供されます。

### <a name="export-performance-tab"></a>[Export Performance]\(エクスポートのパフォーマンス\) タブ

このタブでは、連続エクスポート操作のエクスポートされたレコード、遅延、保留中の数、使用率に関する情報が提供されます。

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Azure Data Explorer クラスター リソースから表示する (ドリルダウン分析)

Azure Data Explorer クラスターから Azure Data Explorer クラスター用の Azure Monitor に直接アクセスするには、次の手順に従います。

1. Azure portal で、 **[Azure Data Explorer クラスター]** を選択します。

2. 一覧から、Azure Data Explorer クラスターを選択します。 [監視] セクションで **[分析情報 (プレビュー)]** を選択します。

これらのビューには、Azure Monitor 分析情報ビュー内から Azure Data Explorer クラスターのリソース名を選択してアクセスすることもできます。

Azure Monitor for Azure Data Explorer では、ログとメトリックを組み合わせて、グローバルな監視ソリューションを提供します。 ログベースの視覚化を含めるには、[Azure Data Explorer クラスターの診断ログを有効にし、Log Analytics ワークスペースに送信する](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)必要があります。 有効にする必要がある診断ログは、**Command**、**Query**、**TableDetails**、**TableUsageStatistics** です。

!["Enable Logs for Monitoring\(監視のログを有効にする\)" というテキストが表示された青いボタンのスクリーンショット](./media/data-explorer/enable-logs.png)


 **[概要]** タブには、以下が表示されます。

- クラスターの可用性と全体的な状態を強調表示して、正常性をすばやく評価するメトリック タイル。

- アクティブな [Advisor の推奨事項](/azure/data-explorer/azure-advisor)と[リソースの正常性](/azure/data-explorer/monitor-with-resource-health)状態の概要。

- CPU およびメモリの上位コンシューマーと一意のユーザーの数を経時的に示すグラフ。


[![Azure Data Explorer クラスター リソースからの表示のスクリーンショット](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

**[Key Metrics]\(主要なメトリック\)** タブには、クラスターの複数のメトリックの統合ビューが表示されます。ビューは、一般的なメトリック、クエリ関連、インジェスト関連、ストリーミング インジェスト関連の各メトリックでグループ化されています。

[![[エラー] ビューのスクリーンショット](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

**[Usage]\(使用状況\)** タブでは、クラスターのコマンドとクエリのパフォーマンスの詳細を確認できます。 このページでは次のことが可能です。
 
 - 最も多くのクエリを送信している、または最も多くの CPU とメモリを消費しているワークロード グループ、ユーザー、およびアプリケーションを確認します (これにより、クラスターが処理する最も負荷の高いクエリを送信しているユーザーを把握できます)。
 - 失敗したクエリで上位のワークロード グループ、ユーザー、およびアプリケーションを特定します。
 - ワークロード グループ、ユーザー、およびアプリケーションごとに、過去の 1 日平均 (過去 16 日間) と比較したクエリ数の最近の変化を示します。
 - ワークロード グループ、ユーザー、アプリケーション、コマンドの種類別に、クエリ数、メモリ消費量、CPU 消費量の傾向とピークを示します。

[![コマンドおよびクエリ数で上位のアプリケーション、コマンドおよびクエリ数で上位のプリンシパル、コマンドの種類で上位のコマンドのドーナツ グラフが含まれた操作ビューのスクリーンショット](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![アプリケーション別のクエリ数、アプリケーション別のメモリ合計、アプリケーション別の CPU の合計の折れ線グラフが含まれた操作ビューのスクリーンショット](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

**[テーブル]** タブには、クラスター内のテーブルの最新および過去のプロパティが表示されます。 最も多くの領域を消費しているテーブルを確認し、増加の過程を、テーブル サイズ、ホット データ、行数で経時的に追跡できます。

**[キャッシュ]** タブでは、実際のクエリのルックバック パターンを分析し、構成済みのキャッシュ ポリシーと比較できます (テーブルごと)。 最も多くのクエリで使用されているテーブルと、まったく照会されていないテーブルを特定し、キャッシュ ポリシーを適宜調整できます。 特定のテーブルについて、キャッシュ ポリシーの特定の推奨事項を Azure Advisor で取得できます (現時点では、キャッシュに関する推奨事項は、[Azure Advisor メイン ダッシュボード](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations) からのみ利用できます)。推奨事項は、過去 30 日間の実際のクエリのルックバックと、少なくとも 95% のクエリに対して最適化されていないキャッシュ ポリシーに基づいています。 Azure Advisor のキャッシュ削減の推奨事項は、"データの制限がある" クラスター (つまり、CPU 使用率とインジェスト使用率が低いにもかかわらず、データ容量が大きいために、スケールインまたはスケールダウンできなかったクラスター) で利用できます。

[![キャッシュの詳細のスクリーンショット](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Azure ダッシュボードにピン留めする

セクションの右上にある画びょうアイコンを選択することで、("大規模な" 分析観点の) メトリック セクションのいずれかを Azure ダッシュボードにピン留めできます。

![ピン留めアイコンが選択されているスクリーンショット](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Azure Data Explorer クラスター用の Azure Monitor をカスタマイズする

このセクションでは、データ分析のニーズに合わせてブックを編集してカスタマイズする一般的なシナリオについて説明します。
* 特定のサブスクリプションまたは Azure Data Explorer クラスターが常に選択されるようにブックのスコープを設定する
* グリッドでメトリックを変更する
* しきい値、カラー レンダリング、色分けを変更する

カスタマイズを開始するには、上部のツールバーの **[カスタマイズ]** ボタンを選択して編集モードを有効にします。

![[カスタマイズ] ボタンのスクリーンショット](./media/data-explorer/customize.png)

カスタマイズはカスタム ブックに保存されるため、発行されたブックで既定の構成が上書きされることはありません。 ブックは、リソース グループ内の、ユーザー個人の [個人用レポート] セクション、またはリソース グループにアクセスできるユーザーであればだれでもアクセスできる [共有レポート] セクションのいずれかに保存されます。 カスタム ブックを保存した後は、ブック ギャラリーに移動して起動する必要があります。

![ブック ギャラリーのスクリーンショット](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>トラブルシューティング

一般的なトラブルシューティングのガイダンスについては、専用のブックベースの分析情報の[トラブルシューティングに関する記事](troubleshoot-workbooks.md)を参照してください。

このセクションは、Azure Data Explorer クラスター用の Azure Monitor (プレビュー) の使用時に発生する可能性があるいくつかの一般的な問題の診断とトラブルシューティングに役立ちます。 以下のリストを使用して、特定の問題に関連する情報を見つけてください。

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>サブスクリプション ピッカーに自分のすべてのサブスクリプションが表示されないのはなぜですか。

選択したサブスクリプション フィルターから選択された、Azure Data Explorer クラスターを含むサブスクリプションだけが表示されます。このフィルターは、Azure portal ヘッダーの [ディレクトリ + サブスクリプション] で選択されています。

![サブスクリプション フィルターのスクリーンショット](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>使用状況、テーブル、またはキャッシュ セクションに Azure Data Explorer クラスターのデータが表示されないのはなぜですか。

ログベースのデータを表示するには、監視する Azure Data Explorer クラスターごとに[診断ログを有効にする](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)必要があります。 これは、各クラスターの診断設定で行うことができます。 Log Analytics ワークスペースにデータを送信する必要があります。 有効にする必要がある診断ログは、Command、Query、TableDetails、TableUsageStatistics です。

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Azure Data Explorer クラスターのログを既に有効にしていますが、[Commands and Queries]\(コマンドとクエリ\) でデータを表示できないのはなぜですか。

現時点では、診断ログは以前にさかのぼって機能しないので、Azure Data Explorer に対してアクションが実行された後にのみ、データが表示されるようになります。 そのため、Azure Data Explorer クラスターがどの程度アクティブかに応じて、数時間から 1 日かかることがあります。


## <a name="next-steps"></a>次のステップ

ブックがサポートするように設計されているシナリオ、新規レポートの作成方法と既存レポートのカスタマイズ方法などについては、「[Azure Monitor ブックを使用した対話型レポートの作成](../visualize/workbooks-overview.md)」で学習してください。
