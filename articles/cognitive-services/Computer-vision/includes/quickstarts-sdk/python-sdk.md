---
title: クイック スタート:Python 用 Computer Vision クライアント ライブラリ
description: このクイックスタートでは、Python 用 Computer Vision クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 744ef58b54dc3718c4693ce2aad69024e2d6e2fe
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321898"
---
<a name="HOLTop"></a>

[リファレンス ドキュメント](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [パッケージ (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [サンプル](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* キーとエンドポイント URL 用に、それぞれ `COMPUTER_VISION_SUBSCRIPTION_KEY` と `COMPUTER_VISION_ENDPOINT` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

> [!NOTE]
> 下の[サンプルの完全ソース コード](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)や [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) から利用できるあらゆる関数の例はダウンロードできます。

## <a name="setting-up"></a>設定
 
### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

新しい Python スクリプト (たとえば *quickstart-file.py*) を作成します。 次に、それを任意のエディターまたは IDE で開き、以下のライブラリをインポートします。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

次に、リソースの Azure エンドポイントおよびキー用の変数を作成します。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のようにして、クライアント ライブラリをインストールできます。

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、Computer Vision Python SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| このクラスは、画像分析、テキスト検出、サムネイル生成など、すべての画像操作を直接処理します。|
| [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | このクラスは、すべての Computer Vision の機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。 **ComputerVisionClientOperationsMixin** が実装されています。|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| この列挙型は、標準の分析操作で実行できるさまざまな種類の画像分析を定義します。 必要に応じて、**VisualFeatureTypes** の値のセットを指定します。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Computer Vision クライアント ライブラリを使用して以下のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [イメージを分析する](#analyze-an-image)
* [印刷されたテキストと手書きのテキストを読み取る](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE]
> このクイックスタートでは、Computer Vision キーのために `COMPUTER_VISION_SUBSCRIPTION_KEY` という名前の[環境変数が作成](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)されていることを前提としています。

ご利用のエンドポイントとキーを使用してクライアントをインスタンス化します。 キーを使用して [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) オブジェクトを作成し、それをエンドポイントと共に使用して、[ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) オブジェクトを作成します。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>イメージを分析する

分析する画像の URL への参照を保存します。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>画像の説明の取得

次のコードは、画像に対して生成されたキャプションの一覧を取得します。 詳細については、[画像の説明](../../concept-describing-images.md)に関するページを参照してください。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>画像カテゴリの取得

次のコードは、画像の検出されたカテゴリを取得します。 詳細については、[画像の分類](../../concept-categorizing-images.md)に関するページを参照してください。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>画像タグの取得

次のコードは、画像内の検出されたタグのセットを取得します。 詳細については、[コンテンツ タグ](../../concept-tagging-images.md)に関するページを参照してください。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>物体を検出する

次のコードは、画像から身の回りの物を検出してコンソールに出力します。 詳細については、[物体の検出](../../concept-object-detection.md)に関するページを参照してください。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>ブランドを検出する

次のコードは、企業のブランドとロゴを画像から検出してコンソールに出力します。 詳細については、[ブランドの検出](../../concept-brand-detection.md)に関するページを参照してください。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>顔を検出する

次のコードは、画像内で検出された顔を、その四角形の座標と顔選択属性と共に返します。 詳細については、[顔の検出](../../concept-detecting-faces.md)に関するページを参照してください。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>成人向け、わいせつ、または不快なコンテンツを検出する

次のコードは、画像内で検出された成人向けのコンテンツの存在を出力します。 詳細については、「[成人向けコンテンツ、わいせつなコンテンツ、不快なコンテンツ](../../concept-detecting-adult-content.md)」を参照してください。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>画像の配色の取得

次のコードは、ドミナント カラー、アクセント カラーなど、画像内で検出された色の属性を出力します。 詳細については、[配色](../../concept-detecting-color-schemes.md)に関するページを参照してください。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>ドメイン固有のコンテンツの取得

Computer Vision では、特殊なモデルを使用して、画像をさらに分析できます。 詳細については、[ドメイン固有のコンテンツ](../../concept-detecting-domain-content.md)に関するページを参照してください。 

次のコードは、画像内の検出された有名人に関するデータを解析します。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

次のコードは、画像内の検出されたランドマークに関するデータを解析します。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>画像の種類を取得する

次のコードは、画像の種類 (クリップ アートであるか線画であるか) に関する情報を出力します。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>印刷されたテキストと手書きのテキストを読み取る

Computer Vision は、画像に映っているテキストを読み取って、文字ストリームに変換することができます。 これは、次の 2 つの処理から成ります。

### <a name="call-the-read-api"></a>Read API を呼び出す

まず、次のコードを使用して、指定した画像の **read** メソッドを呼び出します。 これによって操作 ID が返され、画像の内容を読み取る非同期プロセスが開始されます。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>読み取りの結果を取得する

次に、**read** の呼び出しから返された操作 ID を取得し、操作の結果をサービスに照会します。 次のコードは、結果が返されるまで 1 秒間隔で操作をチェックします。 次に、抽出されたテキスト データをコンソールに出力します。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `python` コマンドを使用して、アプリケーションを実行します。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Python 用 Computer Vision ライブラリを使用して基本的なタスクを行う方法について学習しました。 次は、リファレンス ドキュメントを参照して、ライブラリの詳細について学習してください。


> [!div class="nextstepaction"]
>[Computer Vision API リファレンス (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Computer Vision とは](../../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) にあります。
