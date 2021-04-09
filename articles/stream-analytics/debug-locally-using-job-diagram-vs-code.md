---
title: Visual Studio Code のジョブ ダイアグラムを使用して Azure Stream Analytics のクエリをローカルでデバッグする
description: この記事では、Visual Studio Code 用 Azure Stream Analytics 拡張機能のジョブ ダイアグラムを使用して、クエリをローカルでデバッグする方法について説明します。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 25ad696ad345fbf672f6bf26eb3f35a13fb03ea5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019501"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Visual Studio Code のジョブ ダイアグラムを使用して Azure Stream Analytics のクエリをローカルでデバッグする

ストリーミング ジョブで結果が出力されないときや予期しない結果が出力されるときは、多くの場合、トラブルシューティングが必要になります。 Azure Stream Analytics の Visual Studio Code 拡張機能には、ジョブ ダイアグラム、メトリック、診断ログ、および中間結果が統合されているため、問題の原因をすばやく特定するのに役立ちます。 ジョブ ダイアグラムを使用しながらローカルでクエリをテストすることで、各ステップの中間結果セットとメトリックを調べることができます。

## <a name="debug-a-query-using-job-diagram"></a>ジョブ ダイアグラムを使用してクエリをデバッグする

Azure Stream Analytics スクリプトは、入力データを出力データに変換するために使用されます。 ジョブ ダイアグラムには、イベント ハブや IoT ハブなどの入力ソースのデータが複数のクエリ ステップを通して最終的に出力シンクまでどのように流れるかが示されます。 各クエリ ステップは、`WITH` ステートメントを使用してスクリプトに定義された一時的結果セットにマップされます。 各中間結果セット内の各クエリ ステップのデータとメトリックを表示して、問題の原因を見つけることができます。

> [!NOTE]
> このジョブ ダイアグラムには、1 つのノード内のローカル テストのデータとメトリックのみが示されます。 パフォーマンスのチューニングとトラブルシューティングには使用しないでください。

### <a name="start-local-testing"></a>ローカル テストを開始する

この[クイックスタート](quick-create-visual-studio-code.md)で、Visual Studio Code を使用して Stream Analytics ジョブを作成する方法を確認してください。また、既存のジョブをローカル プロジェクトにエクスポートする方法ついては、[こちら](visual-studio-code-explore-jobs.md)を確認してください。 入力および出力のための資格情報は、エクスポートされたジョブに対して自動的に設定されます。

ローカルの入力データを使用してクエリをテストする場合は、こちらの[手順](visual-studio-code-local-run.md)を実行してください。 ライブ入力を使用してテストする場合は、[入力を構成](stream-analytics-add-inputs.md)してから、次の手順に進んでください。 

*\.asaql* スクリプト ファイルを開き、 **[ローカルで実行]** を選択します。 次に、 **[Use local input]\(ローカル入力を使用\)** または **[Use live input]\(ライブ入力を使用\)** を選択します。 ウィンドウの右側にジョブ ダイアグラムが表示されます。

### <a name="view-the-output-and-intermediate-result-set"></a>出力と中間結果セットを表示する  

1. すべてのジョブ出力が、Visual Studio Code ウィンドウの右下にある結果ウィンドウに表示されます。

   > [!div class="mx-imgBorder"]
   > ![ジョブの出力結果](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. クエリ ステップを選択してスクリプトに移動します。 エディターの左側に、対応するスクリプトが自動的に表示されます。 中間結果が、Visual Studio Code ウィンドウの右下にある結果ウィンドウに表示されます。

   > [!div class="mx-imgBorder"]
   > ![ジョブ ダイアグラムのプレビュー結果](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>メトリックを表示する

このセクションでは、ダイアグラムの各部分で使用できるメトリックを確認します。

1. Visual Studio Code ウィンドウの右下で、 **[結果]** タブの横にある **[メトリック]** タブを選択します。

2. ドロップダウンから **[ジョブ]** を選択します。 グラフ ノードの任意の空白領域を選択すると、ジョブ レベルのメトリックに移動できます。 このビューにはすべてのメトリックが含まれています。ジョブを実行すると、これらのメトリックが 10 秒ごとに更新されます。 右側のメトリックを選択または選択解除することで、グラフに表示することができます。

   > [!div class="mx-imgBorder"]
   > ![ジョブ ダイアグラムのメトリック](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. 入力メトリックを表示するには、ドロップダウンから入力データ ソースの名前を選択します。 次のスクリーンショットの入力ソースの名前は "*見積もり*" になっています。 入力メトリックの詳細については、「[Stream Analytics ジョブ監視とクエリの監視方法の概要](stream-analytics-monitoring.md)」を参照してください。

   > [!div class="mx-imgBorder"]
   > ![ジョブ ダイアグラムの入力メトリック](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. ジョブ ダイアグラムからクエリ ステップを選択するか、ドロップダウンからステップ名を選択すると、ステップ レベルのメトリックが表示されます。 使用可能なステップ メトリックは、透かしの遅延のみです。

   > [!div class="mx-imgBorder"]
   > ![ステップ メトリック](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. ダイアグラムまたはドロップダウンから出力を選択すると、出力関連のメトリックが表示されます。 出力メトリックの詳細については、「[Stream Analytics ジョブ監視とクエリの監視方法の概要](stream-analytics-monitoring.md)」を参照してください。 ライブ出力シンクはサポートされていません。

   > [!div class="mx-imgBorder"]
   > ![出力メトリック](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>診断ログを表示する

ジョブ レベルの診断ログには、入力データ ソースと出力シンクの診断情報が含まれています。 入力ノードまたは出力ノードを選択すると、対応するログのみが表示されます。 クエリ ステップを選択しても、ログは表示されません。 ジョブ レベルですべてのログを検索することができ、重大度と時刻でログをフィルター処理できます。

   > [!div class="mx-imgBorder"]
   > ![診断ログ](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   ログ エントリを選択すると、メッセージ全体が表示されます。

   > [!div class="mx-imgBorder"]
   > ![診断ログ メッセージ](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>ジョブ ダイアグラムのその他の機能

必要に応じて、ツール バーから **[停止]** または **[一時停止]** を選択できます。 ジョブを一時停止した場合、最後の出力から再開できます。

> [!div class="mx-imgBorder"]
> ![ジョブを停止または一時停止する](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

ジョブ ダイアグラムの右上にある **[ジョブの概要]** を選択すると、ローカル ジョブのプロパティと構成が表示されます。

> [!div class="mx-imgBorder"]
> ![ローカル ジョブの概要](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>制限事項

* ライブ出力シンクは、ローカル実行ではサポートされていません。

* JavaScript 関数を使用したローカルでのジョブの実行は、Windows オペレーティング システムでのみサポートされています。

* C# カスタム コードと Azure Machine Learning 関数はサポートされていません。 

* クラウド入力オプションでのみ[日時ポリシー](./stream-analytics-time-handling.md)がサポートされており、ローカル入力オプションではサポートされていません。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Visual Studio Code を使用して Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)
* [Visual Studio Code で Azure Stream Analytics の詳細を確認する](visual-studio-code-explore-jobs.md)
* [Visual Studio Code を使用してサンプル データで Stream Analytics クエリをローカルでテストする](visual-studio-code-local-run.md)
* [Visual Studio Code を使用してライブ入力で Azure Stream Analytics ジョブをローカルでテストする](visual-studio-code-local-run-live-input.md)