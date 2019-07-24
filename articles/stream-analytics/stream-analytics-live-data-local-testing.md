---
title: Visual Studio 用の Azure Stream Analytics を使用してライブ データをテストする
description: ライブ ストリーミング データを使用して Azure Stream Analytics ジョブをローカルにテストする方法について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ea55d2f96a87503d43a69d288ce85dcff32a39ce
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090331"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Visual Studio の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする (プレビュー)

Visual Studio の Azure Stream Analytics ツールを使用すると、Azure Event Hub、IoT Hub、および Blob Storage からのライブ イベント ストリームを使用して、IDE からローカルでジョブをテストできます。 ライブ データのローカル テストでは、クラウドで実行できる[パフォーマンスおよびスケーラビリティ テスト](stream-analytics-streaming-unit-consumption.md)を置き換えることはできませんが、Stream Analytics ジョブをテストするたびにクラウドに送信する必要がないようにすることで、機能テストの時間を節約できます。 この機能はプレビューであり、運用環境のワークロードには使用しないでください。

## <a name="testing-options"></a>テストのオプション

次のローカル テスト オプションがサポートされています。

|**Input (入力)**  |**Output**  |**ジョブの種類**  |
|---------|---------|---------|
|ローカル静的データ   |  ローカル静的データ   |   クラウド/エッジ |
|ライブ入力データ   |  ローカル静的データ   |   クラウド |
|ライブ入力データ   |  ライブ出力データ   |   クラウド |

## <a name="local-testing-with-live-data"></a>ライブ データでのローカル テスト

1. [Visual Studio で Azure Stream Analytics クラウド プロジェクト](stream-analytics-quick-create-vs.md)を作成した後、**script.asaql** を開きます。 ローカル テストでは、ローカル入力とローカル出力が既定で使用されます。

   ![Azure Stream Analytics Visual Studio のローカル入力とローカル出力](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. ライブ データをテストするには、ドロップダウン ボックスから **[Use Cloud Input]\(クラウド入力を使用\)** を選択します。

   ![Azure Stream Analytics Visual Studio のライブ クラウド入力](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. **[Start Time]\(開始日時\)** を設定して、ジョブが入力データの処理を開始する日時を定義します。 ジョブは、正確な結果を得るため、事前に入力データを読み取ることが必要な場合があります。 既定の日時は、現在の日時の 30 分前に設定されます。

   ![Azure Stream Analytics Visual Studio のライブ データ開始時刻](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. **[Run Locally]\(ローカルに実行\)** をクリックします。 コンソール ウィンドウに、実行の進行状況とジョブのメトリックが表示されます。 プロセスを停止する場合は、手動で行うことができます。 

   ![Azure Stream Analytics Visual Studio のライブ データ処理ウィンドウ](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   出力結果は 3 秒ごとに更新され、ローカル実行結果ウィンドウには最初の 500 出力行が表示されて、出力ファイルはプロジェクト パスの **ASALocalRun** フォルダーに格納されます。 ローカル実行結果ウィンドウの **[Open Results Folder]\(結果フォルダーを開く\)** ボタンをクリックして、出力ファイルを開くこともできます。

   ![Azure Stream Analytics Visual Studio のライブ データ結果フォルダーを開く](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. クラウド出力シンクに結果を出力する場合は、2 番目のドロップダウン ボックスから **[Output to Cloud]\(クラウドに出力\)** を選択します。 Power BI と Azure Data Lake Storage は、出力シンクとしてサポートされていません。

   ![Azure Stream Analytics Visual Studio のクラウドへのライブ データ出力](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>制限事項

* 認証モデルの制限により、Power BI と Azure Data Lake Storage は、出力シンクとしてサポートされていません。

* クラウド入力オプションのみが[日時ポリシー](stream-analytics-out-of-order-and-late-events.md)をサポートしており、ローカル入力オプションではサポートされていません。

## <a name="next-steps"></a>次の手順

* [Visual Studio の Azure Stream Analytics ツールを使用した Stream Analytics ジョブの作成](stream-analytics-quick-create-vs.md)
* [Visual Studio の Azure Stream Analytics ツールのインストール](stream-analytics-tools-for-visual-studio-install.md)
* [Visual Studio で Stream Analytics クエリをローカルでテストする](stream-analytics-vs-tools-local-run.md)
* [Visual Studio を使用して Azure Stream Analytics ジョブを表示する](stream-analytics-vs-tools.md)