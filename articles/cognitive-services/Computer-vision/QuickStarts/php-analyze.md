---
title: クイック スタート:リモート画像の分析 - REST、PHP
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、PHP で Computer Vision API を使用して画像を分析します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f9375d3ce7c5127ed80f3f7fad93f4db8aa58b76
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604317"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-php"></a>クイック スタート:Computer Vision の REST API と PHP を使用してリモート画像を分析する

このクイック スタートでは、Computer Vision の REST API を使用して、リモートに格納されている画像を分析し、視覚的特徴を抽出します。 [画像分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)メソッドを使うと、画像の内容に基づいて視覚的特徴を抽出できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) を作成してください。

## <a name="prerequisites"></a>前提条件

- [PHP](https://secure.php.net/downloads.php) がインストールされている必要があります。
- [Pear](https://pear.php.net) がインストールされている必要があります。
- Computer Vision のサブスクリプション キーが必要です。 無料試用版のキーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Computer Vision をサブスクライブし、キーを取得します。

## <a name="create-and-run-the-sample"></a>サンプルの作成と実行

このサンプルを作成して実行するには、次の手順を実行します。

1. PHP5 [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2) パッケージをインストールします。
   1. 管理者としてコマンド プロンプト ウィンドウを開きます。
   1. 次のコマンドを実行します。

      ```console
      pear install HTTP_Request2
      ```

   1. パッケージが正常にインストールされたら、コマンド プロンプト ウィンドウを閉じます。

1. テキスト エディターに次のコードをコピーします。
1. 必要に応じて、コードに次の変更を加えます。
    1. `subscriptionKey` 値を、サブスクリプション キーに置き換えます。
    1. 必要に応じて、サブスクリプション キーを取得した Azure リージョンの[画像分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)メソッドのエンドポイント URL で `uriBase` 値を置き換えます。
    1. 必要に応じて、分析したい別の画像の URL で `imageUrl` 値を置き換えます。
    1. 必要に応じて、`language` 要求パラメーターの値を別の言語に置き換えます。
1. `.php` 拡張子のファイルとして、コードを保存します。 たとえば、「 `analyze-image.php` 」のように入力します。
1. PHP をサポートするブラウザー ウィンドウを開きます。
1. ブラウザー ウィンドウにファイルをドラッグ アンド ドロップします。

```php
<html>
<head>
    <title>Analyze Image Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'visualFeatures' => 'Categories,Description',
    'details' => '',
    'language' => 'en'
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>結果の確認

成功応答が JSON で返されます。 サンプル Web サイトによって成功応答が解析され、次の例のようにブラウザー ウィンドウに表示されます。

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "ebf5a1bc-3ba2-4c56-99b4-bbd20ba28705",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、ファイルを削除して、PHP5 `HTTP_Request2` パッケージをアンインストールします。 パッケージをアンインストールするには、次の手順を実行します。

1. 管理者としてコマンド プロンプト ウィンドウを開きます。
2. 次のコマンドを実行します。

   ```console
   pear uninstall HTTP_Request2
   ```

3. パッケージが正常にアンインストールされたら、コマンド プロンプト ウィンドウを閉じます。

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細を確認する](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
