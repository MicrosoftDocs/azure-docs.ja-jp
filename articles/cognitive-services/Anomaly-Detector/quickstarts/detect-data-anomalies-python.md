---
title: クイック スタート:Anomaly Detector REST API と Python を使用して異常を検出する
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Anomaly Detector API を使用して、データ系列の異常をバッチとして、またはストリーミング データで検出します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 38c2b3cdf40f1924a36ffd84d9dc5f9b2f7f319d
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245708"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>クイック スタート:Anomaly Detector REST API および Python を使用し、時系列データ内の異常を検出する

このクイック スタートを使用して、Anomaly Detector API の 2 つの検出モードの使用を開始し、時系列データでの異常を検出します。 この Python アプリケーションは、JSON 形式の時系列データを格納している 2 つの API 要求を送信し、応答を取得します。

| API 要求                                        | アプリケーションの出力                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| バッチとして異常を検出する                        | 時系列データの各データ ポイントの異常状態 (および他のデータ) と検出された異常の位置を含んだ JSON 応答。 |
| 最新のデータ ポイントの異常状態を検出する | 時系列データの最新のデータ ポイントの異常状態 (および他のデータ) を含んだ JSON 応答。                                                                                                                                         |

 このアプリケーションは Python で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このクイック スタートのソース コードは、[GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py) にあります。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
- Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector リソースを作成"  target="_blank">Anomaly Detector リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    - 対象のアプリケーションを Anomaly Detector API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
- [Python 2.x または 3.x](https://www.python.org/downloads/)
- Python 用の[要求ライブラリ](https://pypi.org/project/requests/)

- 時系列データ ポイントを含む JSON ファイル。 このクイック スタートのサンプル データは、[GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) にあります。

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>新しいアプリケーションを作成する

1. 新しい Python ファイルを作成し、次の import を追加します。

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. サブスクリプション キーとエンドポイントの変数を作成します。 異常検出に使用できる URI を以下に示します。 これらは、API 要求 URL を作成するために、後からサービス エンドポイントに付加されます。

    |検出方法  |URI  |
    |---------|---------|
    |バッチ検出    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |最新のデータ ポイントでの検出     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. JSON データ ファイルを開き、`json.load()` を使用することによってファイル内を読み取ります。

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>要求を送信する関数を作成する

1. 上記で作成した変数を取る `send_request()` という新しい関数を作成します。 次に、以下の手順のようにします。

2. 要求ヘッダーのディクショナリを作成します。 `Content-Type` を `application/json` に設定し、サブスクリプション キーを `Ocp-Apim-Subscription-Key` ヘッダーに追加します。

3. `requests.post()` を使用して要求を送信します。 完全な要求 URL にエンドポイントおよび異常検出 URL を組み合わせて、ヘッダーおよび JSON 要求データを含めます。 次に、応答を返します。

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>バッチとして異常を検出する

1. `detect_batch()` というメソッドを作成し、バッチとしてデータ全体での異常を検出します。 自身のエンドポイント、URL、サブスクリプション キー、および JSON データを使用して、上記で作成した `send_request()` メソッドを呼び出します。

2. 結果で `json.dumps()` を呼び出してそれを書式設定し、コンソールに出力します。

3. 応答に `code` フィールドが含まれる場合は、エラー コードとエラー メッセージを印刷します。

4. そうでない場合は、データ セット内の異常の位置を検索します。 応答の `isAnomaly` フィールドには、指定のデータ ポイントが異常かどうかに関連したブール値が含まれます。 一覧を反復処理して、すべての `True` 値のインデックスを出力します。 これらの値は、異常なデータ ポイントが見つかった場合、そのインデックスに対応します。

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>最新のデータ ポイントの異常状態を検出する

1. `detect_latest()` というメソッドを作成して、時系列内の最新のデータ ポイントが異常であるかどうかを判断します。 自身のエンドポイント、URL、サブスクリプション キー、および JSON データを使用して、上記の `send_request()` メソッドを呼び出します。

2. 結果で `json.dumps()` を呼び出してそれを書式設定し、コンソールに出力します。

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>要求を送信する

上記で作成した異常検出メソッドを呼び出します。

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>応答の例

成功応答が JSON 形式で返されます。 下のリンクをクリックして、GitHub で JSON 応答を表示します。
* [バッチ検出応答の例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新のポイント検出応答の例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
