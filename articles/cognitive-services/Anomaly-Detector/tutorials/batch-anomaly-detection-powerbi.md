---
title: バッチ検出と Power BI を使用して異常を視覚化する
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API と Power BI を使用して、時系列データ全体を通しての異常を視覚化します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: 74b51d04f2706d890475c500e1e730cff75397c5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721488"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>チュートリアル:バッチ検出と Power BI を使用して異常を視覚化する

このチュートリアルを使用して、時系列データ セット内の異常をバッチとして検出します。 Power BI Desktop を使用して、Excel ファイルを取得し、Anomaly Detector API のデータを準備して、全体を通しての統計的異常を視覚化します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Power BI Desktop を使用して時系列データ セットをインポートして変換する
> * バッチの異常検出のために Power BI Desktop を Anomaly Detector API と統合する
> * 予期された値と確認された値を含み、データ内で見つかった異常と、異常検出の境界を視覚化します。

## <a name="prerequisites"></a>前提条件

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/) (無料で利用可能)。
* 時系列データ ポイントを含む Excel ファイル (.xlsx)。 このクイック スタートのサンプル データは、[GitHub](https://go.microsoft.com/fwlink/?linkid=2090962) にあります

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>時系列データを読み込んで書式を設定する

開始するには、Power BI Desktop を開き、前提条件からダウンロードした時系列データを読み込みます。 この Excel ファイルには、一連の協定世界時 (UTC) タイムスタンプと値のペアが含まれています。  

> [!NOTE]
> Power BI では、.csv ファイル、SQL データベース、Azure BLOB ストレージなど、さまざまなソースのデータを使用できます。  

Power BI Desktop のメイン ウィンドウで、 **[ホーム]** リボンをクリックします。 リボンの **[外部データ]** グループで、 **[データの取得]** ドロップダウン メニューを開き、 **[Excel]** をクリックします。

![Power BI の "データの取得" ボタンのイメージ](../media/tutorials/power-bi-get-data-button.png)

ダイアログ ボックスが表示されたら、サンプルの .xlsx ファイルをダウンロードしたフォルダーに移動し、このファイルを選択します。 **[ナビゲーター]** ダイアログが表示されたら、 **[Sheet1]** をクリックしてから **[編集]** をクリックします。

![Power BI のデータ ソースの "ナビゲーター" 画面のイメージ](../media/tutorials/navigator-dialog-box.png)

Power BI は、最初の列のタイムスタンプを `Date/Time` データ型に変換します。 Anomaly Detector API に送信するためには、これらのタイムスタンプをテキストに変換する必要があります。 Power Query エディターが自動的に開かない場合は、ホーム タブで **[クエリの編集]** をクリックします。 

Power Query エディターで **[変換]** リボンをクリックします。 **[任意の列]** グループで **[データ型:]** ドロップダウン メニューを開き、 **[テキスト]** を選択します。

![Power BI のデータ ソースの "ナビゲーター" 画面のイメージ](../media/tutorials/data-type-drop-down.png)

列の型の変更について通知されたら、 **[現在のものを置換]** をクリックします。 その後、 **[ホーム]** リボンの **[閉じて適用]** または **[適用]** をクリックします。 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>データを作成して応答の書式を設定するための関数を作成する

データ ファイルの書式を設定して Anomaly Detector API に送信するために、上記で作成したテーブルに対するクエリを呼び出すことができます。 Power Query エディターの **[ホーム]** リボンから、 **[新しいソース]** ドロップダウン メニューを開いて、 **[空のクエリ]** をクリックします。

新しいクエリが選択されていることを確認してから、 **[詳細エディター]** をクリックします。 

![Power BI の "詳細エディター" ボタンのイメージ](../media/tutorials/advanced-editor-screen.png)

詳細エディター内で、次の Power Query M スニペットを使用して、テーブルから列を抽出して API に送信します。 その後、クエリにより JSON 応答からテーブルが作成されて返されます。 `apiKey` 変数を有効な Anomaly Detector API キーに置き換え、`endpoint` をエンドポイントに置き換えます。 詳細エディターにクエリを入力したら、 **[完了]** をクリックします。

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

**[パラメーターの入力]** の下にある `Sheet1` を選択してデータ シートでクエリを呼び出して、 **[呼び出し]** をクリックします。 

!["詳細エディター" ボタンのイメージ](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>データ ソースのプライバシーと認証

> [!NOTE]
> データのプライバシーとアクセスのための組織のポリシーに注意してください。 詳細については、[Power BI Desktop のプライバシー レベル](https://docs.microsoft.com/power-bi/desktop-privacy-levels)に関するページを参照してください。

クエリは外部データ ソースを利用するため、実行しようとすると警告メッセージが表示されることがあります。 

![Power BI によって作成された警告が表示されているイメージ](../media/tutorials/blocked-function.png)

これを解決するには、 **[ファイル]** をクリックしてから、 **[オプションと設定]** をクリックします。 次に、 **[オプション]** をクリックします。 **[現在のファイル]** で **[プライバシー]** を選択して、 **[プライバシー レベルを無視し、可能であればパフォーマンスを向上させる]** を選択します。 

さらに、API に接続する方法を指定するよう求めるメッセージが表示されることもあります。

![アクセス資格情報を指定する要求が表示されているイメージ](../media/tutorials/edit-credentials-message.png)

これを解決するには、メッセージ内の **[資格情報を編集]** をクリックします。 ダイアログ ボックスが表示されたら、 **[匿名]** を選択して API に匿名で接続します。 次いで **[接続]** をクリックします。 

その後、 **[ホーム]** リボンの **[閉じて適用]** をクリックして変更内容を適用します。

## <a name="visualize-the-anomaly-detector-api-response"></a>Anomaly Detector API 応答を視覚化する

Power BI のメイン画面で、上で作成したクエリの使用を開始して、データを視覚化します。 まず、 **[視覚化]** で **[折れ線グラフ]** を選択します。 次に、呼び出された関数から折れ線グラフの **[軸]** へのタイムスタンプを追加します。 これを右クリックし、 **[タイムスタンプ]** を選択します。 

![タイムスタンプ値を右クリックしている](../media/tutorials/timestamp-right-click.png)

次に、 **[呼び出された関数]** の以下のフィールドを、グラフの **[値]** フィールドに追加します。 下のスクリーンショットを使用すると、グラフの作成に役立ちます。

    * 値
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![新しいクイック メジャー画面のイメージ](../media/tutorials/chart-settings.png)

フィールドを追加したら、グラフをクリックし、すべてのデータ ポイントが表示されるようにサイズを変更します。 次のようなスクリーンショットのようなグラフが表示されます。

![新しいクイック メジャー画面のイメージ](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>異常データ ポイントを表示する

Power BI ウィンドウの右側にある **[フィールド]** ウィンドウの **[Invoked Function query]\(呼び出された関数のクエリ)** で **[値]** を右クリックし、 **[新しいクイック メジャー]** をクリックします。

![新しいクイック メジャー画面のイメージ](../media/tutorials/new-quick-measure.png)

表示される画面で、計算として **[フィルターされた値]** を選択します。 **[基準値]** を `Sum of Value` に設定します。 次に、 **[呼び出された関数]** フィールドの `IsAnomaly` を **[フィルター]** までドラッグします。 **[フィルター]** ドロップダウン メニューから `True` を選択します。

![新しいクイック メジャー画面のイメージ](../media/tutorials/new-quick-measure-2.png)

**[OK]** をクリックすると、フィールドの一覧の下部に `Value for True` フィールドが表示されます。 これを右クリックして、名前を「**異常**」に変更します。 これをグラフの **[値]** に追加します。 次に、 **[形式]** ツールを選択し、X 軸の種類を **[カテゴリ別]** に設定します。

![新しいクイック メジャー画面のイメージ](../media/tutorials/format-x-axis.png)

**[形式]** ツールで **[データの色]** をクリックして、グラフに色を適用します。 次のようなグラフが表示されます。

![新しいクイック メジャー画面のイメージ](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
>[Azure Databricks を使用した異常検出のストリーム配信](anomaly-detection-streaming-databricks.md)
