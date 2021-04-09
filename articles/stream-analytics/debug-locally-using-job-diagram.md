---
title: Visual Studio のジョブ ダイアグラムを使用して Azure Stream Analytics のクエリをローカルでデバッグする
description: この記事では、Azure Stream Analytics Tools for Visual Studio のジョブ ダイアグラムを使用して、クエリをローカルでデバッグする方法について説明します。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: d0e94fda1fb21be1a01516f4cecf657426ae867e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019450"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Visual Studio のジョブ ダイアグラムを使用して Azure Stream Analytics のクエリをローカルでデバッグする

結果がまったく出力されない、または予期しない結果が出力されるジョブは、ストリーミング クエリの一般的なトラブルシューティング シナリオです。 Visual Studio でローカルでクエリをテストしながらジョブ ダイアグラムを使用して、各手順の中間結果セットとメトリックを調べることができます。 問題をトラブルシューティングするときにジョブ ダイアグラムを使用すると、問題の原因をすばやく特定できます。

## <a name="debug-a-query-using-job-diagram"></a>ジョブ ダイアグラムを使用してクエリをデバッグする

Azure Stream Analytics スクリプトは、入力データを出力データに変換するために使用されます。 ジョブ ダイアグラムには、入力ソース (イベント ハブ、IoT Hub など) のデータが複数のクエリ手順を介して最終的に出力シンクまでどのように流れるかが示されます。 各クエリ ステップは、`WITH` ステートメントを使用してスクリプトに定義された一時的結果セットにマップされます。 各中間結果セット内の各クエリ ステップのデータとメトリックを表示して、問題の原因を見つけることができます。

> [!NOTE]
> このジョブ ダイアグラムには、1 つのノード内のローカル テストのデータとメトリックのみが示されます。 パフォーマンスのチューニングとトラブルシューティングには使用しないでください。

### <a name="start-local-testing"></a>ローカル テストを開始する

この[クイックスタート](stream-analytics-quick-create-vs.md)で Visual Studio を使用して Stream Analytics ジョブを作成する方法、または[既存のジョブをローカル プロジェクトにエクスポートする方法](stream-analytics-vs-tools.md#export-jobs-to-a-project)を確認してください。 ローカルの入力データを使用してクエリをテストする場合は、こちらの[手順](stream-analytics-live-data-local-testing.md)を実行してください。 ライブ入力を使用してテストする場合は、次の手順に進みます。

> [!NOTE]
> ジョブをローカル プロジェクトにエクスポートし、ライブ入力ストリームに対してテストする場合は、すべての入力の資格情報を再度指定する必要があります。  

スクリプト エディターから入力および出力ソースを選択し、 **[ローカルで実行]** を選択します。 右側にジョブ ダイアグラムが表示されます。

### <a name="view-the-intermediate-result-set"></a>中間結果セットを表示する  

1. クエリ ステップを選択してスクリプトに移動します。 エディターの左側に対応するスクリプトが自動的に表示されます。

   ![ジョブ ダイアグラムの移動スクリプト](./media/debug-locally-using-job-diagram/navigate-script.png)

2. クエリ ステップを選択し、ポップアップ ダイアログで **[プレビュー]** を選択します。 結果セットが下部の結果ウィンドウのタブに表示されます。

   ![ジョブ ダイアグラムのプレビュー結果](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>ステップのメトリックを表示する

このセクションでは、ダイアグラムの各部分で使用できるメトリックを確認します。

#### <a name="input-sources-live-stream"></a>入力ソース (ライブ ストリーム)

![ジョブ ダイアグラムのライブ入力ソース](./media/debug-locally-using-job-diagram/live-input.png)

|メトリック|説明|
|-|-|
|**TaxiRide**| 入力の名前。|
|**イベント ハブ** | 入力ソースの種類。|
|**イベント**|読み取られたイベントの数。|
|**バックログされたイベント ソース**|Event Hubs と IoT Hub の入力について読み取る必要があるメッセージの数。|
|**バイト単位のイベント**|読み取るバイト数。|
| **デグレードしたイベント**|逆シリアル化以外の問題が発生したイベントの数。|
|**早期イベント**| 高基準値より前のアプリケーション タイムスタンプを持つイベントの数。|
|**遅延イベント**| 高基準値より後のアプリケーション タイムスタンプを持つイベントの数。|
|**イベント ソース**| 読み取られたデータ ユニットの数。 たとえば、BLOB の数です。|

#### <a name="input-sources-local-input"></a>入力ソース (ローカル入力)

![ジョブ ダイアグラムのローカル入力ソース](./media/debug-locally-using-job-diagram/local-input.png)

|メトリック|説明|
|-|-|
|**TaxiRide**| 入力の名前。|
|**行数**| ステップから生成された行数。|
|**データ サイズ**| このステップから生成されるデータのサイズ。|
|**ローカル入力**| 入力としてローカル データを使用します。|

#### <a name="query-steps"></a>クエリ ステップ

![ジョブ ダイアグラムのクエリ ステップ](./media/debug-locally-using-job-diagram/query-step.png)

|メトリック|説明|
|-|-|
|**TripData**|一時結果セットの名前。|
|**行数**| ステップから生成された行数。|
|**データ サイズ**| このステップから生成されるデータのサイズ。|
  
#### <a name="output-sinks-live-output"></a>出力シンク (ライブ出力)

![ローカルの出力シンクを示すジョブ ダイアグラム。](./media/debug-locally-using-job-diagram/live-output.png)

|メトリック|説明|
|-|-|
|**regionaggEH**|出力の名前。|
|**イベント**|シンクに出力されるイベントの数。|

#### <a name="output-sinks-local-output"></a>出力シンク (ローカル出力)

![ジョブ ダイアグラムのローカル出力シンク](./media/debug-locally-using-job-diagram/local-output.png)

|メトリック|説明|
|-|-|
|**regionaggEH**|出力の名前。|
|**ローカル出力**| ローカル ファイルへの結果の出力。|
|**行数**| ローカル ファイルに出力される行数。|
|**データ サイズ**| ローカル ファイルに出力されるデータのサイズ。|

### <a name="close-job-diagram"></a>ジョブ ダイアグラムを閉じる

ジョブ ダイアグラムが不要になった場合は、右上隅にある **[閉じる]** を選択します。 ダイアグラム ウィンドウを閉じた後に、ローカル テストを再び開始して確認する必要があります。

### <a name="view-job-level-metrics-and-stop-running"></a>ジョブ レベルのメトリックを表示して実行を停止する

その他のジョブ レベルのメトリックはポップアップ コンソールに表示されます。 ジョブを停止する場合は、コンソールで **Ctrl + C** キーを押します。

![ジョブ ダイアグラムの停止ジョブ](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>制限事項

* 認証モデルの制限により、Power BI と Azure Data Lake Storage Gen1 の出力シンクはサポートされていません。

* クラウド入力オプションのみが[日時ポリシー](./stream-analytics-time-handling.md)をサポートしており、ローカル入力オプションではサポートされていません。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Visual Studio の Azure Stream Analytics ツールを使用した Stream Analytics ジョブの作成](stream-analytics-quick-create-vs.md)
* [Visual Studio を使用して Azure Stream Analytics ジョブを表示する](stream-analytics-vs-tools.md)
* [Visual Studio の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする (プレビュー)](stream-analytics-live-data-local-testing.md)