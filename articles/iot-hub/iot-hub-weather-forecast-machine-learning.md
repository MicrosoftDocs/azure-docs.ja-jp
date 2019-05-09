---
title: Azure Machine Learning で IoT Hub からのデータを使用して天気予報を行う | Microsoft Docs
description: Azure Machine Learning を使用して、IoT Hub がセンサーから収集した気温と湿度のデータに基づき、降水確率を予測します。
author: robinsh
manager: philmea
keywords: 機械学習を使用した天気予報
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: a203018cb1a27c17b9c7162b50a380f9edae2e60
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273194"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Azure Machine Learning で IoT Hub からのセンサー データを使用して天気予報を行う

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

機械学習は、将来の動き、結果、傾向を予測するためにコンピューターで既存のデータからの学習を行う、データ サイエンスの手法の 1 つ です。 Azure Machine Learning は、クラウドの予測分析サービスであり、分析ソリューションとして予測モデルを迅速に作成し、デプロイできるようにします。

## <a name="what-you-learn"></a>学習内容

Azure Machine Learning で Azure IoT Hub から取得した気温と湿度のデータを使用して、天気 (降水確率) を予測する方法を学びます。 降水確率は、準備された天気予報モデルから出力されます。 このモデルは、気温と湿度に基づいて降水確率を予測するための履歴データを基に構築されています。

## <a name="what-you-do"></a>作業内容

- 天気予報モデルを Web サービスとしてデプロイする。
- コンシューマー グループを追加して、データにアクセスできるよう IoT ハブを準備する。
- Stream Analytics ジョブを作成し、ジョブが以下を行うように構成する。
  - IoT Hub から取得した気温と湿度のデータを読み取る。
  - Web サービスを呼び出して降水確率を取得する。
  - Azure Blob Storage に結果を保存する。
- Microsoft Azure ストレージ エクスプローラーを使用して、天気予報を表示する。

## <a name="what-you-need"></a>必要なもの

- 次の要件に対応するために、[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md)に関するチュートリアルを完了しておきます。
  - 有効な Azure サブスクリプション
  - サブスクリプションの Azure IoT Hub。
  - Azure IoT Hub にメッセージを送信するクライアント アプリケーション。
- Azure Machine Learning Studio アカウント。 ([Machine Learning Studio の無料試用版](https://studio.azureml.net/))。

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>天気予報モデルを Web サービスとしてデプロイする

1. [[weather prediction model] \(天気予報モデル) ページ](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)に移動します。
1. Microsoft Azure Machine Learning Studio の **[Studio で開く]** をクリックします。
   ![Cortana Intelligence ギャラリーで [weather prediction model] \(天気予報モデル) ページを開く](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. **[実行]** をクリックしてモデルの手順を検証します。 この手順の完了までに約 2 分かかります。
   ![Azure Machine Learning Studio で天気予報モデルを開く](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. **[Web サービスの設定]** > **[予測 Web サービス]** の順にクリックします。
   ![Azure Machine Learning Studio に天気予報モデルをデプロイする](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. ダイアグラムで、**Web サービスの入力**モジュールを**モデルのスコア付け**モジュールの近くにドラッグします。
1. **Web サービスの入力**モジュールを**モデルのスコア付け**モジュールに接続します。
   ![Azure Machine Learning Studio で 2 つのモジュールを接続する](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. **[実行]** をクリックしてモデルの手順を検証します。
1. **[DEPLOY WEB SERVICE] \(Web サービスのデプロイ)** をクリックして、モデルを Web サービスとしてデプロイします。
1. モデルのダッシュボードで、**要求/応答**用に **Excel 2010 以前のブック**をダウンロードします。

   > [!Note]
   > コンピューターで新しいバージョンの Excel を実行している場合でも、**Excel 2010 以前のブック**をダウンロードしてください。

   ![要求/応答エンドポイントに Excel をダウンロードする](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Excel ブックを開いて、**Web サービス URL** と**アクセス キー**をメモします。

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics ジョブの作成、構成、実行

### <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. [Azure Portal](https://portal.azure.com/) で、**[リソースの作成]** > **[モノのインターネット]** > **[Stream Analytics ジョブ]** の順にクリックします。
1. 次の情報をジョブに入力します。

   **ジョブ名**:ジョブの名前。 名前はグローバルに一意である必要があります。

   **[リソース グループ]**:IoT ハブと同じリソース グループを使用します。

   **[場所]**:リソース グループと同じ場所を使用します。

   **[ダッシュボードにピン留めする]**:ダッシュボードから IoT ハブに簡単にアクセスできるようにするには、このオプションをオンにします。

   ![Azure での Stream Analytics ジョブの作成](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. **Create** をクリックしてください。

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの入力の追加

1. Stream Analytics ジョブを開きます。
1. **[ジョブ トポロジ]** で **[入力]** をクリックします。
1. **[入力]** ウィンドウで **[追加]** をクリックしてから、次の情報を入力します。

   **入力のエイリアス**:入力の一意のエイリアス。

   **ソース**:**[IoT ハブ]** を選択します。

   **コンシューマー グループ**:作成したコンシューマー グループを選びます。

   ![Azure で Stream Analytics ジョブに入力を追加する](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. **Create** をクリックしてください。

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの出力の追加

1. **[ジョブ トポロジ]** で **[出力]** をクリックします。
1. **[出力]** ウィンドウで **[追加]** をクリックしてから、次の情報を入力します。

   **出力のエイリアス**:出力の一意のエイリアス。

   **シンク**: **[Blob Storage]** を選択します。

   **ストレージ アカウント**: Blob Storage のストレージ アカウント。 ストレージ アカウントを作成するか、既存のストレージ アカウントを使用できます。

   **コンテナー**:BLOB を保存するコンテナー。 コンテナーを作成するか、既存のコンテナーを使用できます。

   **イベントのシリアル化の形式**: **[CSV]** を選択します。

   ![Azure で Stream Analytics ジョブに出力を追加する](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. **Create** をクリックしてください。

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>デプロイした Web サービスを呼び出すための関数を Stream Analytics ジョブに追加する

1. **[ジョブ トポロジ]** で **[関数]** > **[追加]** の順にクリックします。
1. 次の情報を入力します。

   **関数のエイリアス**: 「 `machinelearning` 」を入力します。

   **関数の型**: **[Azure ML]** を選択します。

   **[インポート オプション]**: **[別のサブスクリプションからインポートする]** を選択します。

   **URL**: Excel ブックからメモした Web サービス URL を入力します。

   **[キー]**:Excel ブックからメモしたアクセス キーを入力します。

   ![Azure で Stream Analytics ジョブに関数を追加する](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. **Create** をクリックしてください。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics ジョブのクエリの構成

1. **[ジョブ トポロジ]** で **[クエリ]** をクリックします。
1. 既存のコードを次のコードに置き換えます。

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   `[YourInputAlias]` をジョブの入力エイリアスに置き換えます。

   `[YourOutputAlias]` をジョブの出力エイリアスに置き換えます。

1. **[Save]** をクリックします。

### <a name="run-the-stream-analytics-job"></a>Stream Analytics ジョブの実行

Stream Analytics ジョブで、**[開始]** > **[現在]** > **[開始]** の順にクリックします。 ジョブが正常に開始されると、ジョブの状態が **[停止済み]** から **[実行中]** に変わります。

![Stream Analytics ジョブの実行](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Microsoft Azure ストレージ エクスプローラーを使用して、天気予報を表示する

クライアント アプリケーションを実行して、IoT Hub に対する気温と湿度のデータの収集と送信を開始します。 IoT Hub が受信するメッセージごとに、Stream Analytics ジョブは天気予報の Web サービスを呼び出して降水確率を生成します。 結果は Azure Blob Storage に保存されます。 Azure ストレージ エクスプローラーを使用して、この結果を表示することができます。

1. [Microsoft Azure ストレージ エクスプローラーをダウンロードしてインストールします](https://storageexplorer.com/)。
1. Azure ストレージ エクスプローラーを開きます。
1. Azure アカウントにサインインします。
1. サブスクリプションを選択します。
1. Azure サブスクリプション > **[ストレージ アカウント]** > 自分のストレージ アカウント > **[BLOB コンテナー]** > 自分のコンテナーの順にクリックします。
1. .csv ファイルを開いて結果を表示します。 降水確率は最後の列に記録されています。

   ![Azure Machine Learning で天気予報の結果を取得する](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>まとめ

Azure Machine Learning を正しく使用して、IoT Hub が受信した気温と湿度のデータに基づく降水確率が生成されました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]