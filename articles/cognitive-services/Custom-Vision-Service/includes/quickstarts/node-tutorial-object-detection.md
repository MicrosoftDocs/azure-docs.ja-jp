---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: cfcc5bbc101a37e607fcc1bdb0d374510e7c3825
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102445608"
---
このガイドでは、Node.js 用の Custom Vision クライアント ライブラリを使用して物体検出モデルを構築する際の足がかりとして役立つ手順とサンプル コードを紹介します。 プロジェクトを作成し、タグを追加し、プロジェクトをトレーニングして、プロジェクトの予測エンドポイント URL を使用してプログラムでテストします。 この例は、独自の画像認識アプリを構築するためのテンプレートとしてご利用ください。

> [!NOTE]
> コードを記述 "_せずに_" 物体検出モデルの構築とトレーニングを行う場合は、代わりに [ブラウザーベースのガイダンス](../../get-started-build-detector.md)を参照してください。

.NET 用 Custom Vision クライアント ライブラリを使用すると、次のことができます。

* 新しい Custom Vision プロジェクトを作成する
* プロジェクトにタグを追加する
* 画像をアップロードし、タグ付けする
* プロジェクトをトレーニングする
* 現在のイテレーションを公開する
* 予測エンドポイントをテストする

リファレンス ドキュメント [(トレーニング)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(予測)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | ライブラリのソース コード [(トレーニング)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(予測)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | パッケージ (npm) [(トレーニング)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(予測)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction) | [サンプル](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [Node.js](https://nodejs.org/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="Custom Vision リソースを作成"  target="_blank">Custom Vision リソースを作成</a>し、トレーニングおよび予測リソースを作成し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Custom Vision に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。 

```console
npm init
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Node.js 用の Custom Vision で画像分析アプリを作成するには、Custom Vision の NPM パッケージが必要です。 これをインストールするには、PowerShell で次のコマンドを実行します。

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

アプリの `package.json` ファイルが依存関係によって更新されます。

`index.js` という名前のファイルを作成して次のライブラリをインポートします。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js) にあり、このクイックスタートのコード例が含まれています。

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Custom Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[キー] および [エンドポイント]** ページの **[リソース管理]** にあります。 トレーニング キーと予測キーの両方およびトレーニング リソースのエンドポイントを取得する必要があります。
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../../cognitive-services/cognitive-services-security.md)に関するページを参照してください。

また、プロジェクト名と、非同期呼び出しのタイムアウト パラメーターのフィールドも追加します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>オブジェクト モデル

|名前|説明|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | このクラスでは、モデルの作成、トレーニング、および公開を処理します。 |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| このクラスでは、物体検出予測のために、モデルに対するクエリ実行を処理します。|
|[予測](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| このインターフェイスでは、単一の画像に対して単一の予測を定義します。 これには、オブジェクト ID と名前、および信頼度スコアのプロパティが含まれます。|

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、JavaScript 用 Custom Vision クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [新しい Custom Vision プロジェクトを作成する](#create-a-new-custom-vision-project)
* [プロジェクトにタグを追加する](#add-tags-to-the-project)
* [画像をアップロードし、タグ付けする](#upload-and-tag-images)
* [プロジェクトをトレーニングする](#train-the-project)
* [現在のイテレーションを公開する](#publish-the-current-iteration)
* [予測エンドポイントをテストする](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>クライアントを認証する

実際のエンドポイントとキーを使用して、クライアント オブジェクトをインスタンス化します。 自分のキーを使用して **ApiKeyCredentials** オブジェクトを作成し、それを自分のエンドポイントと共に使用して、[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) および [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient) オブジェクトを作成します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>ヘルパー関数を追加する

複数の非同期呼び出しを行うことができるように、次の関数を追加します。 これは後で使用します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>新しい Custom Vision プロジェクトを作成する

すべての Custom Vision 関数呼び出しを含む新しい関数を開始します。 新しい Custom Vision Service プロジェクトを作成するための次のコードを追加します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

最初に、このプロジェクト用のサンプル画像をダウンロードします。 [サンプル画像フォルダー](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images)の内容をお使いのローカル デバイスに保存します。

> [!NOTE]
> トレーニングを完了するために、より広範なイメージのセットが必要ですか。 Trove (Microsoft Garage プロジェクト) を使用すると、トレーニング目的で画像のセットを収集して購入することができます。 画像を収集したら、それらをダウンロードした後、通常の方法で Custom Vision プロジェクトにインポートできます。 詳細については、[Trove ページ](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3)を参照してください。

サンプルの画像をプロジェクトに追加するには、タグ作成後、次のコードを挿入します。 このコードでは、それぞれの画像を対応するタグと共にアップロードします。 物体検出プロジェクトで画像にタグを付ける際は、タグ付けする各物体の領域を正規化座標を使用して指定する必要があります。 このチュートリアルでは、リージョンがコードの行内にハードコードされています。 各領域は、正規化座標で境界ボックスを指定しており、座標は、左、上、幅、高さの順に指定しています。 1 回のバッチで最大 64 個の画像をアップロードできます。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> 画像のパス (`sampleDataRoot`) は、Cognitive Services Python SDK Samples リポジトリをダウンロードした場所に応じて変更する必要があります。

> [!NOTE]
> 領域の座標をマークするためのクリックアンド ドラッグ ユーティリティがない場合は、[Customvision.ai](https://www.customvision.ai/) で Web UI を使用できます。 この例では、座標は既に指定されています。


## <a name="train-the-project"></a>プロジェクトをトレーニングする

このコードにより、予測モデルの最初のイテレーションが作成されます。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>現在のイテレーションを公開する

このコードにより、トレーニング済みのイテレーションを予測エンドポイントに公開します。 公開されたイテレーションに付けられた名前は、予測要求を送信するために使用できます。 イテレーションは、公開されるまで予測エンドポイントで利用できません。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>予測エンドポイントをテストする

画像を予測エンドポイントに送信し、予測を取得するには、以下のコードを関数に追加します。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

次に、Custom Vision 関数を閉じて呼び出します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```shell
node index.js
```

コンソールにアプリケーションの出力が表示されると思います。 **<sampleDataRoot>/Test/** 内のテスト画像にタグが適切に付けられていること、また検出の領域が正しいことを確認してください。 [Custom Vision Web サイト](https://customvision.ai)に戻って、新しく作成されたプロジェクトの現在の状態を確認してもかまいません。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>次のステップ

以上、物体検出処理の各ステップをコードで実行しました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。 次のガイドでは、画像の分類について取り上げていますが、その原理は物体の検出と似ています。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](../../test-your-model.md)

* Custom Vision とは
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js) にあります。
* [SDK のリファレンス ドキュメント (トレーニング)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [SDK のリファレンス ドキュメント (予測)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
