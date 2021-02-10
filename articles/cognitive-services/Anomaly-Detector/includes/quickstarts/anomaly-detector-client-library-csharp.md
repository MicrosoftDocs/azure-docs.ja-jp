---
title: Anomaly Detector .NET クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: ee4dc926269d3ac66243a3953d7e41eb3a30198c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947988"
---
.NET 用 Anomaly Detector クライアント ライブラリを使ってみます。 サービスによって提供されるアルゴリズムを使用してパッケージをインストールするには、次の手順に従います。 Anomaly Detector サービスにより、業界、シナリオ、データ量に関係なく、最適なモデルを自動的に使用することで、時系列データ内の異常を検出できます。

.NET 用 Anomaly Detector クライアント ライブラリは、次の目的で使用することができます。

* バッチ要求として、時系列データセット全体で異常を検出する
* 時系列で最新のデータ ポイントの異常状態を検出する
* データセット内の傾向変化点を検出する。

[ライブラリのリファレンス ドキュメント](https://aka.ms/anomaly-detector-dotnet-ref) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.2) | [GitHub でコードを検索する](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector リソースを作成"  target="_blank">Anomaly Detector リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Anomaly Detector API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>新しい .NET Core アプリを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`anomaly-detector-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、次の C# ソース ファイルを 1 つ使用する単純な "Hello World" プロジェクトが作成されます: *Program.cs*。

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```dotnetcli
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Anomaly Detector クライアント ライブラリをインストールします。

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector
```

プロジェクト ディレクトリから *program.cs* ファイルを開いて、次の using `directives` を追加します。

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

アプリケーションの `main()` メソッド内で、ご自分のリソースの Azure の場所用の変数と、環境変数としてのキーを作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、再読み込みしなければならない場合があります。

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>オブジェクト モデル

Anomaly Detector クライアントは、キーが含まれている [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials) を使用して Azure に対する認証を行う [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) オブジェクトです。 クライアントは、[EntireDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync) を使用してデータセット全体の異常検出を行うか、または [LastDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync) を使用して最新のデータ ポイントを対象にすることができます。 [ChangePointDetectAsync](https://aka.ms/anomaly-detector-dotnet-ref) メソッドは、傾向の変化を示すポイントを検出します。

時系列データは、[Request](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) オブジェクト内の一連の [Point](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) として送信されます。 `Request` オブジェクトには、データを説明するプロパティ (たとえば、[細分性](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity)など) と異常検出のパラメーターが含まれます。

Anomaly Detector の応答は、使用するメソッドに応じて、[EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse)、[LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse)、または [changePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) オブジェクトのいずれかになります。

## <a name="code-examples"></a>コード例

以下のコード スニペットは、.NET 用 Anomaly Detector クライアント ライブラリを使用して次のことを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [ファイルから時系列データ セットを読み込む](#load-time-series-data-from-a-file)
* [データ セット全体で異常を検出する](#detect-anomalies-in-the-entire-data-set)
* [最新のデータ ポイントの異常状態を検出する](#detect-the-anomaly-status-of-the-latest-data-point)
* [データセット内の変化点を検出する](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>クライアントを認証する

新しいメソッドで、実際のエンドポイントとキーを使用してクライアントをインスタンス化します。 キーを使用して [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials) オブジェクトを作成し、それをエンドポイントと共に使用して、[AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) オブジェクトを作成します。

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>ファイルから時系列データを読み込む

このクイックスタートのサンプル データを [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) からダウンロードします。
1. ブラウザーで、 **[Raw]\(未加工\)** を右クリックします。
2. **[Save link as]\(名前を付けてリンク先を保存\)** をクリックします。
3. ファイルを .csv ファイルとしてアプリケーション ディレクトリに保存します。

この時系列データは、.csv ファイル形式として書式設定され、Anomaly Detector API に送信されます。

時系列データを読み取るための新しいメソッドを作成し、[Request](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) オブジェクトに追加します。 ファイル パスを指定して `File.ReadAllLines()` を呼び出し、[Point](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point) オブジェクトのリストを作成して、改行文字をすべて削除します。 値を抽出し、その数値から日付スタンプを分離して、新しい `Point` オブジェクトに追加します。

一連のポイントを使用して `Request` オブジェクトを作成し、データ ポイントの[細分性](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity) (または周期性) を表す `Granularity.Daily` を作成します。

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>データ セット全体で異常を検出する

`Request` オブジェクトが含まれているクライアントの [EntireDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) メソッドを呼び出すメソッドを作成し、[EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) オブジェクトとしての応答を待ちます。 時系列に異常が含まれている場合は、応答の [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly) 値を反復処理し、`true` であるものをすべて出力します。 これらの値は、異常なデータ ポイントが見つかった場合、そのインデックスに対応します。

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>最新のデータ ポイントの異常状態を検出する

`Request` オブジェクトが含まれているクライアントの [LastDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) メソッドを呼び出すメソッドを作成し、[LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) オブジェクトとしての応答を待ちます。 応答の [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly) 属性を確認して、送信された最新のデータ ポイントが異常であったかどうかを判断します。

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="detect-change-points-in-the-data-set"></a>データセット内の変化点を検出する

`Request` オブジェクトを使用してクライアントの [DetectChangePointAsync](https://aka.ms/anomaly-detector-dotnet-ref) メソッドを呼び出し、[ChangePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) オブジェクトとしての応答を待つメソッドを作成します。 応答の IsChangePoint 値を確認し、`true` であるものをすべて出力します。 これらの値は、傾向の変化点に対応します (見つかった場合)。

[!code-csharp[DetectChangePoint() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=changePointExample)]

## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリで `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
