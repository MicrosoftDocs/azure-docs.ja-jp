---
title: Anomaly Detector Python クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: f6206ad2f88983396fa7d0be323daad327e4d235
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947989"
---
Python 用 Anomaly Detector クライアント ライブラリを使ってみます。 サービスによって提供されるアルゴリズムを使用してパッケージをインストールするには、次の手順に従います。 Anomaly Detector サービスにより、業界、シナリオ、データ量に関係なく、最適なモデルを自動的に使用することで、時系列データ内の異常を検出できます。

Python 用 Anomaly Detector クライアント ライブラリは、次の目的で使用することができます。

* バッチ要求として、時系列データセット全体で異常を検出する
* 時系列で最新のデータ ポイントの異常状態を検出する
* データセット内の傾向変化点を検出する。

[ライブラリのリファレンス ドキュメント](https://go.microsoft.com/fwlink/?linkid=2090370) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [パッケージ (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [GitHub でサンプル コードを検索する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector/samples)

## <a name="prerequisites"></a>前提条件

* [Python 3.x](https://www.python.org/)
* [Pandas データ分析ライブラリ](https://pandas.pydata.org/)
* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector リソースを作成"  target="_blank">Anomaly Detector リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Anomaly Detector API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。


## <a name="setting-up"></a>設定

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

 新しい Python ファイルを作成して次のライブラリをインポートします。

```python
import os
from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectRequest, TimeSeriesPoint, TimeGranularity, \
    AnomalyDetectorError
from azure.core.credentials import AzureKeyCredential
import pandas as pd
```

環境変数としてのキー、時系列データ ファイルへのパス、およびサブスクリプションの Azure の場所の変数を作成します。 たとえば、「 `westus2` 」のように入力します。

```python
SUBSCRIPTION_KEY = os.environ["ANOMALY_DETECTOR_KEY"]
ANOMALY_DETECTOR_ENDPOINT = os.environ["ANOMALY_DETECTOR_ENDPOINT"]
TIME_SERIES_DATA_PATH = os.path.join("./sample_data", "request-data.csv")
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>オブジェクト モデル

Anomaly Detector クライアントは、キーを使用して Azure に対する認証を行う [AnomalyDetectorClient](https://github.com/Azure/azure-sdk-for-python/blob/0b8622dc249969c2f01c5d7146bd0bb36bb106dd/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector/azure/cognitiveservices/anomalydetector/_anomaly_detector_client.py)オブジェクトです。 クライアントでは、[detect_entire_series](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L26) を使用してデータセット全体の異常を検出するか、[detect_last_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L87) を使用して最新のデータ ポイントの異常を検出できます。 [detect_change_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/aio/operations_async/_anomaly_detector_client_operations_async.py#L142) 関数では、傾向の変化を示すポイントが検出されます。

時系列データは一連の [TimeSeriesPoints](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L370) オブジェクトとして送信されます。 `DetectRequest` オブジェクトには、データを説明するプロパティ (たとえば、`TimeGranularity` など) と異常検出のパラメーターが含まれます。

Anomaly Detector の応答は、使用するメソッドに応じて、[LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse)、[EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse)、または [ChangePointDetectResponse](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L107) オブジェクトになります。

## <a name="code-examples"></a>コード例

以下のコード スニペットは、Python 用 Anomaly Detector クライアント ライブラリを使用して次のことを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [ファイルから時系列データ セットを読み込む](#load-time-series-data-from-a-file)
* [データ セット全体で異常を検出する](#detect-anomalies-in-the-entire-data-set)
* [最新のデータ ポイントの異常状態を検出する](#detect-the-anomaly-status-of-the-latest-data-point)
* [データセット内の変化点を検出する](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>クライアントを認証する

Azure の場所の変数をエンドポイントに追加し、キーを使用してクライアントを認証します。

```python
client = AnomalyDetectorClient(AzureKeyCredential(SUBSCRIPTION_KEY), ANOMALY_DETECTOR_ENDPOINT)
```

## <a name="load-time-series-data-from-a-file"></a>ファイルから時系列データを読み込む

このクイックスタートのサンプル データを [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) からダウンロードします。
1. ブラウザーで、 **[Raw]\(未加工\)** を右クリックします。
2. **[Save link as]\(名前を付けてリンク先を保存\)** をクリックします。
3. ファイルを .csv ファイルとしてアプリケーション ディレクトリに保存します。

この時系列データは、.csv ファイル形式として書式設定され、Anomaly Detector API に送信されます。

Pandas ライブラリの `read_csv()` メソッドを使用してデータ ファイルを読み込み、データ系列を格納するための空のリスト変数を作成します。 ファイルを反復処理して、データを `TimeSeriesPoint` オブジェクトとして追加します。 このオブジェクトには、タイムスタンプと .csv データ ファイルの行の数値が格納されます。

```python
series = []
data_file = pd.read_csv(TIME_SERIES_DATA_PATH, header=None, encoding='utf-8', parse_dates=[0])
for index, row in data_file.iterrows():
    series.append(TimeSeriesPoint(timestamp=row[0], value=row[1]))
```

時系列とそのデータ ポイントの`TimeGranularity` (または周期性) を使用して `DetectRequest` オブジェクトを作成します。 たとえば、「 `TimeGranularity.daily` 」のように入力します。

```python
request = DetectRequest(series=series, granularity=TimeGranularity.daily)
```

## <a name="detect-anomalies-in-the-entire-data-set"></a>データ セット全体で異常を検出する

API を呼び出し、クライアントの `detect_entire_series` メソッドを使用して時系列データ全体で異常を検出します。 返された [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) オブジェクトを格納します。 応答の `is_anomaly` 一覧を反復処理して、すべての `true` 値のインデックスを出力します。 これらの値は、異常なデータ ポイントが見つかった場合、そのインデックスに対応します。

```python
print('Detecting anomalies in the entire time series.')

try:
    response = client.detect_entire_series(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_anomaly):
    print('An anomaly was detected at index:')
    for i, value in enumerate(response.is_anomaly):
        if value:
            print(i)
else:
    print('No anomalies were detected in the time series.')
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>最新のデータ ポイントの異常状態を検出する

Anomaly Detector API を呼び出し、クライアントの `detect_last_point` メソッドを使用して最新のデータ ポイントが異常かどうかを判断し、返された `LastDetectResponse` オブジェクトを格納します。 応答の `is_anomaly` の値は、そのポイントの異常状態を指定するブール値です。  

```python
print('Detecting the anomaly status of the latest data point.')

try:
    response = client.detect_last_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if response.is_anomaly:
    print('The latest point is detected as anomaly.')
else:
    print('The latest point is not detected as anomaly.')
```

## <a name="detect-change-points-in-the-data-set"></a>データセット内の変化点を検出する

クライアントの `detect_change_point` メソッドを使用して、時系列データ内の変化点を検出する API を呼び出します。 返された `ChangePointDetectResponse` オブジェクトを格納します。 応答の `is_change_point` 一覧を反復処理して、すべての `true` 値のインデックスを出力します。 これらの値は、傾向の変化点のインデックスに対応します (見つかった場合)。

```python
print('Detecting change points in the entire time series.')

try:
    response = client.detect_change_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_change_point):
    print('An change point was detected at index:')
    for i, value in enumerate(response.is_change_point):
        if value:
            print(i)
else:
    print('No change point were detected in the time series.')
```

## <a name="run-the-application"></a>アプリケーションの実行

`python` コマンドとファイル名を使用してアプリケーションを実行します。

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
