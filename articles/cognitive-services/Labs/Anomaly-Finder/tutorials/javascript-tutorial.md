---
title: チュートリアル:JavaScript による異常検出
titlesuffix: Azure Cognitive Services
description: 異常検出 API を使用する JavaScript Web アプリについて説明します。 API に元のデータ ポイントを送信し、予期される値と異常なポイントを取得します。
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 9e66b24987b2318f3022404d951fbb911e7b592d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227831"
---
# <a name="tutorial-anomaly-detection-with-javascript-application"></a>チュートリアル:JavaScript アプリケーションによる異常検出

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

異常検出 REST API を使用して異常を検出する Web アプリケーションについて説明します。 例では、サブスクリプション キーを使用して、異常検出 API に時系列データを送信し、続いて API から、データ ポイントごとの異常ポイントと予期された値をすべて取得します。

## <a name="prerequisites"></a>前提条件

### <a name="platform-requirements"></a>プラットフォームの要件

このチュートリアルは、単純なテキスト エディターを使用して開発されています。

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>異常検出にサブスクライブしてサブスクリプション キーを取得する 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>例を取得し使用する

このチュートリアルでは、時系列データの異常検出に関する 2 つのシナリオが用意されています。 それでは始めましょう。

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>チュートリアル プロジェクトをダウンロードする

[Cognitive Services JavaScript 異常検出チュートリアル](https://github.com/MicrosoftAnomalyDetection/javascript-sample)を複製するか、または .zip ファイルをダウンロードしそれを空のディレクトリに抽出します。

<a name="Step2"></a>
### <a name="run-the-example"></a>例を実行する

この例を試す 2 つのシナリオがあります。
1. **サブスクリプション キー**を、anomalydetection.html 上の検出関数のサブスクリプション キー フィールドに入力します。
2. 異常検出 API エンドポイントを入力し、サブスクリプション リージョンに正しいリージョンを使用していることを確認します。
3. Web ブラウザーで **anomalydetection.html** ファイルを開きます。

**シナリオ 1 週次時系列データの検出**
1. [期間] フィールドに期間 **7** を入力します。 
2. サンプル データを [ポイント] フィールドの週次時系列データ ポイント (Json) に置き換えるか、直接サンプル データを使用します。
3. [異常検出] ボタンをクリックし、右側の [応答] テキスト ボックスで結果を確認します。

**シナリオ 2 期間を指定しない時系列データの検出**
1. [期間] フィールドの期間を空のままにして、時系列の期間がわからないものとします。
2. シナリオ 1 と同じ、時系列データを使用します。
3. [異常検出] ボタンをクリックし、右側の [応答] テキスト ボックスの [期間] フィールドを確認します。

<a name="Step3"></a>
### <a name="read-the-result"></a>結果を読み取る

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>確認し学習する

これで実行中のアプリケーションを取得しました。 例のアプリケーションが、Cognitive Services テクノロジとどのように統合するか見てみましょう。 この手順により、このアプリのビルドの継続や、Microsoft の異常検出を使用した独自のアプリの開発が簡単になります。
このアプリ例では、異常検出 Restful API エンドポイントを活用します。
Restful API がアプリケーション例でどのように使用されているかを確認し、anomalydetection.html からのコード スニペットを見てみましょう。
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API リファレンス](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
