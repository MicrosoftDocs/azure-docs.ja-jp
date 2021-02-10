---
title: Anomaly Detector JavaScript クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.custom: devx-track-js
ms.openlocfilehash: 36b8a6952a8dc0b34df7bf32a708c71547bf5b33
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948343"
---
JavaScript 用 Anomaly Detector クライアント ライブラリを使ってみます。 サービスによって提供されるアルゴリズムを使用してパッケージをインストールするには、次の手順に従います。 Anomaly Detector サービスにより、業界、シナリオ、データ量に関係なく、最適なモデルを自動的に使用することで、時系列データ内の異常を検出できます。

JavaScript 用 Anomaly Detector クライアント ライブラリは、次の目的で使用することができます。

* バッチ要求として、時系列データセット全体で異常を検出する
* 時系列で最新のデータ ポイントの異常状態を検出する
* データセット内の傾向変化点を検出する。

[ライブラリのリファレンス ドキュメント](https://go.microsoft.com/fwlink/?linkid=2090788) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [パッケージ (npm)](https://www.npmjs.com/package/%40azure/ai-anomaly-detector) | [GitHub でコードを検索する](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Node.js](https://nodejs.org/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector リソースを作成"  target="_blank">Anomaly Detector リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Anomaly Detector API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。 

```console
npm init
```

`index.js` という名前のファイルを作成して次のライブラリをインポートします。

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。 後続の手順でダウンロードすることになるサンプル データ ファイル用にもう 1 つの変数を作成し、さらにデータ ポイント用に空のリストを作成します。 その後、キーを保持する `ApiKeyCredentials` オブジェクトを作成します。

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

`ms-rest-azure` および `azure-cognitiveservices-anomalydetector` NPM パッケージをインストールします。 このクイックスタートでは、csv-parse ライブラリも使用します。

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

アプリの `package.json` ファイルが依存関係によって更新されます。

## <a name="object-model"></a>オブジェクト モデル

Anomaly Detector クライアントは、キーを使用して Azure に対する認証を行う [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient) オブジェクトです。 クライアントは、[entireDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#entiredetect-request--servicecallback-entiredetectresponse--) を使用してデータセット全体の異常検出を行うか、または [LastDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#lastdetect-request--msrest-requestoptionsbase-) を使用して最新のデータ ポイントを対象にすることができます。 [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090788) メソッドは、傾向の変化を示すポイントを検出します。 

時系列データは、[Request](/javascript/api/@azure/cognitiveservices-anomalydetector/request) オブジェクト内の一連の [Point](/javascript/api/@azure/cognitiveservices-anomalydetector/point) として送信されます。 `Request` オブジェクトには、データを説明するプロパティ (たとえば、[細分性](/javascript/api/@azure/cognitiveservices-anomalydetector/request#granularity)など) と異常検出のパラメーターが含まれます。 

Anomaly Detector の応答は、使用するメソッドに応じて、[LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse)、[EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse)、または [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788) オブジェクトになります。 

## <a name="code-examples"></a>コード例 

以下のコード スニペットは、Node.js 用 Anomaly Detector クライアント ライブラリを使用して次のことを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [ファイルから時系列データ セットを読み込む](#load-time-series-data-from-a-file)
* [データ セット全体で異常を検出する](#detect-anomalies-in-the-entire-data-set) 
* [最新のデータ ポイントの異常状態を検出する](#detect-the-anomaly-status-of-the-latest-data-point)
* [データセット内の変化点を検出する](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>クライアントを認証する

ご利用のエンドポイントと資格情報を使用して [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient) オブジェクトをインスタンス化します。

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>ファイルから時系列データを読み込む

このクイックスタートのサンプル データを [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv) からダウンロードします。
1. ブラウザーで、 **[Raw]\(未加工\)** を右クリックします。
2. **[Save link as]\(名前を付けてリンク先を保存\)** をクリックします。
3. ファイルを .csv ファイルとしてアプリケーション ディレクトリに保存します。

この時系列データは、.csv ファイル形式として書式設定され、Anomaly Detector API に送信されます。

csv-parse ライブラリの `readFileSync()` メソッドでデータ ファイルを読み取り、`parse()` でファイルを解析します。 行ごとに、タイムスタンプと数値を含んだ [Point](/javascript/api/@azure/cognitiveservices-anomalydetector/point) オブジェクトをプッシュします。

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>データ セット全体で異常を検出する 

クライアントの [entireDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#entiredetect-request--msrest-requestoptionsbase-) メソッドを使用して時系列全体で異常を検出する API をバッチとして呼び出します。 返された [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse) オブジェクトを格納します。 応答の `isAnomaly` 一覧を反復処理して、すべての `true` 値のインデックスを出力します。 これらの値は、異常なデータ ポイントが見つかった場合、そのインデックスに対応します。

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>最新のデータ ポイントの異常状態を検出する

Anomaly Detector API を呼び出し、クライアントの [lastDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#lastdetect-request--msrest-requestoptionsbase-) メソッドを使用して最新のデータ ポイントが異常かどうかを判断し、返された [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse) オブジェクトを格納します。 応答の `isAnomaly` の値は、そのポイントの異常状態を指定するブール値です。  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="detect-change-points-in-the-data-set"></a>データセット内の変化点を検出する

クライアントの [detectChangePoint()](https://go.microsoft.com/fwlink/?linkid=2090788) メソッドを使用して、時系列内の変化点を検出する API を呼び出します。 返された [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788) オブジェクトを格納します。 応答の `isChangePoint` 一覧を反復処理して、すべての `true` 値のインデックスを出力します。 これらの値は、傾向の変化点のインデックスに対応します (見つかった場合)。

[!code-javascript[detect change points](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=changePointDetection)]

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
