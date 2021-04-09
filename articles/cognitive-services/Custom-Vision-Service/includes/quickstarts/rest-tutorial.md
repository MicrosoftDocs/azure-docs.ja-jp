---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: 76441e6c5627f4e48d77d67409ef05891c7cc1ac
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510996"
---
Custom Vision REST API の使用を開始します。 API を呼び出し、画像分類モデルを構築するには、これらの手順に従います。 プロジェクトを作成し、タグを追加し、プロジェクトをトレーニングして、プロジェクトの予測エンドポイント URL を使用してプログラムでテストします。 この例は、独自の画像認識アプリを構築するためのテンプレートとしてご利用ください。

> [!NOTE]
> Custom Vision は、クライアント ライブラリ SDK または[ブラウザーベースのガイダンス](../../get-started-build-detector.md)を通じて最も簡単に使用できます。

.NET 用 Custom Vision クライアント ライブラリを使用すると、次のことができます。

* 新しい Custom Vision プロジェクトを作成する
* プロジェクトにタグを追加する
* 画像をアップロードし、タグ付けする
* プロジェクトをトレーニングする
* 現在のイテレーションを公開する
* 予測エンドポイントをテストする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Custom Vision リソースを作成"  target="_blank">Custom Vision リソースを作成</a>し、トレーニングおよび予測リソースを作成し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Custom Vision に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* [PowerShell バージョン 6.0 以降](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)、または同様のコマンド ライン アプリケーション。


## <a name="create-a-new-custom-vision-project"></a>新しい Custom Vision プロジェクトを作成する

画像分類プロジェクトを作成するには、次のようなコマンドを使用します。 作成したプロジェクトは、[Custom Vision Web サイト](https://customvision.ai/)に表示されます。


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

コマンドをテキスト エディターにコピーし、次の変更を行います。

* `{subscription key}` を、有効な Face サブスクリプション キーに置き換えます。
* `{endpoint}` を、自分のサブスクリプション キーに対応するエンドポイントに置き換えます。
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* `{name}` を自分のプロジェクト名に置き換えます。
* 必要に応じて、他の URL パラメーターを設定して、自分のプロジェクトで使用するモデルの種類を構成します。 オプションについては、[CreatProject API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) を参照してください。

次のような JSON 応答が返されます。 プロジェクトの `"id"` 値を一時的な場所に保存します。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>プロジェクトにタグを追加する

モデルをトレーニングするタグを定義するには、次のコマンドを使用します。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* 再度、独自のキーとエンドポイント URL を挿入します。
* `{projectId}` を独自のプロジェクト ID に置き換えます。
* `{name}` を、使用するタグの名前に置き換えます。

自分がプロジェクトで使用するすべてのタグについて、この手順を繰り返します。 提供されている画像の例を使用している場合は、タグ `"Hemlock"` と `"Japanese Cherry"` を追加します。

次のような JSON 応答が得られます。 各タグの `"id"` 値を一時的な場所に保存します。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

次に、このプロジェクト用のサンプル画像をダウンロードします。 [サンプル画像フォルダー](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images)の内容をお使いのローカル デバイスに保存します。

> [!NOTE]
> トレーニングを完了するために、より広範なイメージのセットが必要ですか。 Trove (Microsoft Garage プロジェクト) を使用すると、トレーニング目的で画像のセットを収集して購入することができます。 画像を収集したら、それらをダウンロードした後、通常の方法で Custom Vision プロジェクトにインポートできます。 詳細については、[Trove ページ](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3)を参照してください。

次のコマンドを使用して、画像をアップロードし、タグを適用します。この操作を "Hemlock" 画像に対して 1 回行い、"Japanese Cherry" 画像に対しても別途行います。 その他のオプションについては、[Create Images From Data](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) API を参照してください。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* 再度、独自のキーとエンドポイント URL を挿入します。
* `{projectId}` を独自のプロジェクト ID に置き換えます。
* `{tagArray}` をタグの ID に置き換えます。
* 次に、タグを付ける画像のバイナリ データを要求の本文に設定します。

## <a name="train-the-project"></a>プロジェクトをトレーニングする

この方法では、アップロードしたタグ付き画像でモデルがトレーニングされ、現在のプロジェクト イテレーションの ID が返されます。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* 再度、独自のキーとエンドポイント URL を挿入します。
* `{projectId}` を独自のプロジェクト ID に置き換えます。
* `{tagArray}` をタグの ID に置き換えます。
* 次に、タグを付ける画像のバイナリ データを要求の本文に設定します。
* 必要に応じて、他の URL パラメーターを使用します。 オプションについては、[Train Project](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API を参照してください。

> [!TIP]
> 選択したタグでトレーニングする
>
> 必要に応じて、適用したタグのサブセットのみでトレーニングできます。 ある特定のタグが十分に適用されていないが、他のタグが十分に適用されている場合に、これを行うことができます。 オプションの JSON コンテンツを要求の本文に追加します。 `"selectedTags"` 配列に、使用するタグの ID を設定します。
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

JSON 応答には、イテレーション ID (`"id"`) など、トレーニング済みのプロジェクトに関する情報が含まれています。 次の手順で使用するため、この値を保存します。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>現在のイテレーションを公開する

このメソッドでは、モデルの現在のイテレーションをクエリの実行に使用できるようにします。 返されたモデル名は、予測要求を送信するための参照として使用します。 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* 再度、独自のキーとエンドポイント URL を挿入します。
* `{projectId}` を独自のプロジェクト ID に置き換えます。
* `{iterationId}` を前の手順で返された ID に置き換えます。
* `{publishedName}` を、自分の予測モデルに割り当てる名前に置き換えます。
* `{predictionId}` を独自の予測リソース ID に置き換えます。 これは、Azure portal の予測リソースの **[概要]** タブに、**サブスクリプション ID** として表示されます。
* 必要に応じて、他の URL パラメーターを使用します。 [Publish Iteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5) API を参照してください。

## <a name="test-the-prediction-endpoint"></a>予測エンドポイントをテストする

最後に、このコマンドを使用して、タグで分類するための新しい画像をアップロードし、トレーニング済みのモデルをテストします。 前にダウンロードしたサンプル ファイルの "Test" フォルダーの画像を使用できます。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* 再度、独自のキーとエンドポイント URL を挿入します。
* `{projectId}` を独自のプロジェクト ID に置き換えます。
* `{publishedName}` は、前のステップで使用した名前に置き換えてください。
* ローカル画像のバイナリ データを要求本文に追加します。
* 必要に応じて、他の URL パラメーターを使用します。 [Classify Image](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3) API を参照してください。

返された JSON 応答には、モデルによって画像に適用された各タグと、各タグの確率スコアが含まれます。 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>次のステップ

これで、REST API を使用した画像分類処理のすべての手順が完了しました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](../../test-your-model.md)

* Custom Vision とは
* [API リファレンス ドキュメント (トレーニング)](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
* [API リファレンス ドキュメント (予測)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
