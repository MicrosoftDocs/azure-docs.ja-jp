---
title: クイック スタート:Anomaly Detector REST API および Java を使用して時系列データ内の異常を検出する
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API を使用して、お使いのデータ系列の異常を一括で、またはストリーミング データ上で検出する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 06/30/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: e8fdc703b094ace83e70b736c1eb0d15c461adba
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2020
ms.locfileid: "88243872"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>クイック スタート:Anomaly Detector REST API および Java を使用して時系列データ内の異常を検出する

このクイック スタートを使用して、Anomaly Detector API の 2 つの検出モードの使用を開始し、時系列データでの異常を検出します。 この Java アプリケーションは、JSON 形式の時系列データを格納している 2 つの API 要求を送信し、応答を取得します。

| API 要求                                        | アプリケーションの出力                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| バッチとして異常を検出する                        | 時系列データの各データ ポイントの異常状態 (および他のデータ) と検出された異常の位置を含んだ JSON 応答。 |
| 最新のデータ ポイントの異常状態を検出する | 時系列データの最新のデータ ポイントの異常状態 (および他のデータ) を含んだ JSON 応答。                                                                                                                                         |

 このアプリケーションは Java で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このクイック スタートのソース コードは、[GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java) にあります。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
- Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector リソースを作成"  target="_blank">Anomaly Detector リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    - 対象のアプリケーションを Anomaly Detector API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 以降。
- Maven リポジトリから以下のライブラリをインポートします
    - [JSON in Java](https://mvnrepository.com/artifact/org.json/json) パッケージ
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) パッケージ

- 時系列データ ポイントを含む JSON ファイル。 このクイック スタートのサンプル データは、[GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) にあります。

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>新しいアプリケーションを作成する

1. 新しい Java プロジェクトを作成して次のライブラリをインポートします。

    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. サブスクリプション キーとエンドポイントの変数を作成します。 異常検出に使用できる URI を以下に示します。 これらは、API 要求 URL を作成するために、後からサービス エンドポイントに付加されます。

    |検出方法  |URI  |
    |---------|---------|
    |バッチ検出    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |最新のデータ ポイントでの検出     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>要求を送信する関数を作成する

1. 上記で作成した変数を取る `sendRequest()` という新しい関数を作成します。 次に、以下の手順のようにします。

2. API に要求を送信できる `CloseableHttpClient` オブジェクトを作成します。 エンドポイントと Anomaly Detector の URL を組み合わせて、`HttpPost` 要求オブジェクトに要求を送信します。

3. 要求の `setHeader()` 関数を使用して、`Content-Type` ヘッダーを `application/json` に設定し、サブスクリプション キーを `Ocp-Apim-Subscription-Key` ヘッダーに追加します。

4. 送信するデータに対して要求の `setEntity()` 関数を使用します。

5. クライアントの `execute()` 関数を使用して、要求を送信し、それを `CloseableHttpResponse` オブジェクトに格納します。

6. `HttpEntity` オブジェクトを作成して、応答の内容を格納します。 `getEntity()` で内容を取得します。 応答が空でない場合は、それを返します。

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>バッチとして異常を検出する

1. `detectAnomaliesBatch()` というメソッドを作成し、バッチとしてデータ全体での異常を検出します。 自身のエンドポイント、URL、サブスクリプション キー、および JSON データを使用して、上記で作成した `sendRequest()` メソッドを呼び出します。 結果を取得し、それをコンソールに出力します。

2. 応答に `code` フィールドが含まれる場合は、エラー コードとエラー メッセージを印刷します。

3. そうでない場合は、データ セット内の異常の位置を検索します。 応答の `isAnomaly` フィールドには、指定のデータ ポイントが異常かどうかに関連したブール値が含まれます。 JSON 配列を取得し、その全体にわたって反復処理を行い、すべての `true` 値のインデックスを出力します。 これらの値は、異常なデータ ポイントが見つかった場合、そのインデックスに対応します。

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>最新のデータ ポイントの異常状態を検出する

`detectAnomaliesLatest()` というメソッドを作成し、データ セット内の最後のデータ ポイントの異常状態を検出します。 自身のエンドポイント、URL、サブスクリプション キー、および JSON データを使用して、上記で作成した `sendRequest()` メソッドを呼び出します。 結果を取得し、それをコンソールに出力します。

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>時系列データを読み込み、要求を送信する

1. アプリケーションの main メソッドで、要求に追加されるデータを含む JSON ファイルを読み取ります。

2. 上記で作成した 2 つの異常検出関数を呼び出します。

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>応答の例

成功応答が JSON 形式で返されます。 下のリンクをクリックして、GitHub で JSON 応答を表示します。
* [バッチ検出応答の例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新のポイント検出応答の例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
