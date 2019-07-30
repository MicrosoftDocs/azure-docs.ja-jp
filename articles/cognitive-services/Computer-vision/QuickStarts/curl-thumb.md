---
title: クイック スタート:サムネイルの生成 - REST、cURL
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Computer Vision API と cURL を使って、画像からサムネイルを生成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 55fa5ffbfe8cdb266340df1a407968d542b36c1a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605987"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>クイック スタート:Computer Vision の REST API と cURL を使用したサムネイルを生成する

このクイック スタートでは、Computer Vision の REST API を使って、画像からサムネイルを生成します。 その際に指定する目的の高さと幅は、入力画像の縦横比と異なっていてもかまいません。 Computer Vision では、スマート トリミングを使ってインテリジェントに関心領域を識別し、その領域を中心にしたトリミングの座標を生成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) を作成してください。

## <a name="prerequisites"></a>前提条件

- [cURL](https://curl.haxx.se/windows) が必要です。
- Computer Vision のサブスクリプション キーが必要です。 無料試用版のキーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Computer Vision をサブスクライブし、キーを取得します。

## <a name="get-thumbnail-request"></a>サムネイル取得要求

[サムネイル取得メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)を使うと、画像のサムネイルを生成することができます。

このサンプルを実行するには、次の手順を実行します。

1. エディターに次のコードをコピーします。
1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. `<File>` を、サムネイルの保存先のパスとファイル名に置き換えます。
1. 必要に応じて、サブスクリプション キーの取得場所を使用するように要求 URL (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) を変更します。
1. 必要に応じて、分析対象の画像 (`{\"url\":\"...`) を変更します。
1. cURL がインストールされているコンピューターのコマンド ウィンドウを開きます。
1. ウィンドウにコードを貼り付けて、コマンドを実行します。

>[!NOTE]
>REST 呼び出しには、サブスクリプション キーの取得に使用したのと同じ場所を使用する必要があります。 たとえば、サブスクリプション キーを westus から取得した場合、下記の URL の "westcentralus" を "westus" に置き換えます。

## <a name="create-and-run-the-sample-command"></a>サンプル コマンドの作成と実行

このサンプルを作成して実行するには、次の手順を実行します。

1. テキスト エディターに次のコマンドをコピーします。
1. 必要に応じて、コマンドに次の変更を加えます。
    1. `<subscriptionKey>` 値を、サブスクリプション キーに置き換えます。
    1. サムネイルを保存するファイルのパスと名前に、`<thumbnailFile>` の値を置き換えます。
    1. 必要に応じて、サブスクリプション キーを取得した Azure リージョンの[サムネイル取得](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)メソッドのエンドポイント URL で要求 URL (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail`) を置き換えます。
    1. 必要に応じて、要求本文の画像 URL (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) を、サムネイルの生成元である別の画像の URL に変更します。
1. コマンド プロンプト ウィンドウを開きます。
1. テキスト エディターからコマンド プロンプト ウィンドウにコマンドを貼り付けて、コマンドを実行します。

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>結果の確認

成功応答では、`<thumbnailFile>` で指定されたファイルにサムネイル画像が書き込まれます。 要求が失敗した場合は、原因の特定につながるエラー コードとメッセージが応答に格納されます。 要求が成功したように見えても、作成されたサムネイルが有効な画像ファイルではない場合は、サブスクリプション キーが有効でない可能性があります。

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出を実行する方法の Computer Vision API の詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細を確認する](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
