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
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1c2ab3f0ef84007d8b9233c45d39fcfbacb5eaab
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835308"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>クイック スタート:Computer Vision の REST API と cURL を使用したサムネイルを生成する

このクイックスタートでは、Computer Vision の REST API を使って、画像からサムネイルを生成します。 その際に指定する目的の高さと幅は、入力画像の縦横比と異なっていてもかまいません。 Computer Vision では、スマート トリミングを使ってインテリジェントに関心領域を識別し、その領域を中心にしたトリミングの座標を生成します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/) 
* [cURL](https://curl.haxx.se/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
  * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
  * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="create-and-run-the-sample-command"></a>サンプル コマンドの作成と実行

このサンプルを作成して実行するには、次の手順を実行します。

1. テキスト エディターに次のコマンドをコピーします。
1. 必要に応じて、コマンドに次の変更を加えます。
    1. `<subscriptionKey>` 値を、サブスクリプション キーに置き換えます。
    1. サムネイルを保存するファイルのパスと名前に、`<thumbnailFile>` の値を置き換えます。
    1. 要求 URL (`westcentralus`) の最初の部分を独自のエンドポイント URL 内のテキストに置き換えます。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. 必要に応じて、要求本文の画像 URL (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) を、サムネイルの生成元である別の画像の URL に変更します。
1. コマンド プロンプト ウィンドウを開きます。
1. テキスト エディターからコマンド プロンプト ウィンドウにコマンドを貼り付けます。
1. Enter キーを押して、プログラムを実行します。

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>結果の確認

成功応答では、`<thumbnailFile>` で指定されたファイルにサムネイル画像が書き込まれます。 要求が失敗した場合は、原因の特定につながるエラー コードとメッセージが応答に格納されます。 要求が成功したように見えても、作成されたサムネイルが有効な画像ファイルではない場合は、サブスクリプション キーが有効でない可能性があります。

## <a name="next-steps"></a>次のステップ

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出を実行する方法の Computer Vision API の詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細を確認する](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)
