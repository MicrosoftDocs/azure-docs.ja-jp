---
title: Face JavaScript クライアント ライブラリのクイックスタート
description: JavaScript 用 Face クライアント ライブラリを使用して、顔の検出、類似検索 (画像による顔検索)、顔の識別 (顔認識検索)、顔データの移行を行います。
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: b4a63f76cbcd9e98295f5edcf7ff2d06979e6556
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244572"
---
## <a name="quickstart-face-client-library-for-javascript"></a>クイック スタート: JavaScript 用 Face クライアント ライブラリ

JavaScript 用 Face クライアント ライブラリを使用して顔認識を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Face サービスは、画像内の人間の顔を検出および認識するための高度なアルゴリズムへのアクセスを提供します。

JavaScript 用 Face クライアント ライブラリは、次の目的で使用します。

* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)
* [人物グループを作成する](#create-a-person-group)
* [顔を識別する](#identify-a-face)

[リファレンスのドキュメント](/javascript/api/@azure/cognitiveservices-face/) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face) | [サンプル](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [Node.js](https://nodejs.org/en/)
* Azure サブスクリプションを入手したら、Azure portal で [Face リソースを作成](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace)して、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Face API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
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

`ms-rest-azure` および `azure-cognitiveservices-face` NPM パッケージをインストールします。

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

アプリの `package.json` ファイルが依存関係によって更新されます。

`index.js` という名前のファイルを作成して次のライブラリをインポートします。

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub]() にあり、このクイックスタートのコード例が含まれています。

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Face リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security)に関するページを参照してください。

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、Face .NET クライアント ライブラリの主要な機能の一部が処理されます。

|名前|説明|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | このクラスは、Face サービスを使用するための承認を表し、すべての Face 機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。 |
|[Face](/javascript/api/@azure/cognitiveservices-face/face)|このクラスは、人間の顔に対して実行できる基本的な検出と認識のタスクを処理します。 |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|このクラスは、画像内の 1 つの顔から検出されたすべてのデータを表します。 これを使用して、顔に関する詳細情報を取得できます。|
|[FaceList](/javascript/api/@azure/cognitiveservices-face/facelist)|このクラスは、クラウドに格納された **FaceList** コンストラクトを管理します。これには、さまざまな顔のセットが格納されます。 |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| このクラスは、クラウドに格納された **Person** コンストラクトを管理します。これには、1 人の人物に属している顔のセットが格納されます。|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| このクラスは、クラウドに格納された **PersonGroup** コンストラクトを管理します。これには、さまざまな **Person** オブジェクトのセットが格納されます。 |

## <a name="code-examples"></a>コード例

以下のコード スニペットでは、.NET 用 Face クライアント ライブラリを使用して次のタスクを実行する方法を示しています。

* [クライアントを認証する](#authenticate-the-client)
* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)
* [人物グループを作成する](#create-a-person-group)
* [顔を識別する](#identify-a-face)

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js) にあり、このクイックスタートのコード例が含まれています。

## <a name="authenticate-the-client"></a>クライアントを認証する

ご利用のエンドポイントとキーを使用してクライアントをインスタンス化します。 自分のキーを指定して **[ApiKeyCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials)** オブジェクトを作成し、それを自分のエンドポイントと共に使用して **[FaceClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceclient)** オブジェクトを作成します。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>グローバル値とヘルパー関数を宣言する

次のグローバル値は、後で追加する Face 操作のいくつかに必要です。

URL は、サンプル画像のフォルダーを指しています。 UUID は、作成する PersonGroup の名前と ID の両方として機能します。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

次の関数を使用して、PersonGroup のトレーニングの完了を待ちます。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>画像内の顔を検出する

### <a name="get-detected-face-objects"></a>検出された顔オブジェクトを取得する

顔を検出するための新しいメソッドを作成します。 `DetectFaceExtract` メソッドでは、指定された URL にある画像のうち 3 つを処理し、 **[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)** オブジェクトのリストをプログラムのメモリに作成します。 抽出する特徴は、 **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** 値のリストで指定します。 

その後、`DetectFaceExtract` メソッドにより、検出された各顔の属性データが解析され、出力されます。 各属性は、元の顔検出 API 呼び出しで個別に指定する必要があります ( **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** リストで)。 次のコードではすべての属性が処理されますが、通常、使用する必要があるのは 1 個または数個だけです。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> また、ローカルの画像から顔を検出することもできます。 [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____) などの [Face](/javascript/api/@azure/cognitiveservices-face/face) メソッドを参照してください。

## <a name="find-similar-faces"></a>似た顔の検索

以下のコードでは、検出された顔 (ソース) を 1 つ受け取って、他の顔のセット (ターゲット) から一致するものを見つけます (画像による顔検索)。 一致するものが見つかると、一致した顔の ID がコンソールに出力されます。

### <a name="detect-faces-for-comparison"></a>比較の対象となる顔を検出する

まず、2 つ目の顔検出メソッドを定義します。 顔を比較するためにはまず、画像で顔を検出する必要があります。また、この検出メソッドは比較操作向けに最適化されています。 前のセクションほど詳細には顔の属性を抽出せず、また、別の認識モデルを使用しています。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>一致するものを探す

次のメソッドでは、一連のターゲット画像と単一のソース画像から顔を検出します。 その後、それらを比較し、ソース画像と似ているターゲット画像をすべて見つけます。 最後に、一致の詳細がコンソールに出力されます。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>顔を識別する

[識別](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____)操作では、1 人の人物 (または複数人) の画像を受け取り、その画像内でそれぞれの顔の同一性を見つけます (顔認識検索)。 検出された顔はそれぞれ、顔の特徴が確認されているさまざまな [Person](/javascript/api/@azure/cognitiveservices-face/person) オブジェクトのデータベース、つまり [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup) と比較されます。 識別操作を行うには、最初に [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup) を作成してトレーニングする必要があります。

### <a name="add-faces-to-person-group"></a>人物グループに顔を追加する

次の関数を作成して、[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup) に顔を追加します。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-person-group"></a>人物グループのトレーニングを待機する

次のヘルパー関数を作成して、人物グループのトレーニングの終了を待ちます。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-person-group"></a>人物グループを作成する

コード例を次に示します。
- [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup) を作成します
- 以前に定義した `AddFacesToPersonGroup` を呼び出して、人物グループに顔を追加します。
- 人物グループをトレーニングします。
- 人物グループ内の顔を識別します。

これで、この **Person** グループとそれに関連付けられた **Person** オブジェクトを検証、識別、グループ化の各操作で使用する準備が整いました。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> ローカル画像から **PersonGroup** を作成することもできます。 [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_) などの [PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson) メソッドを参照してください。

## <a name="main"></a>メイン

最後に、`main` 関数を作成し、呼び出します。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、JavaScript 用の Face クライアント ライブラリを使用して基本的な顔認識タスクを行う方法について学習しました。 次は、リファレンス ドキュメントを参照して、ライブラリの詳細について学習してください。

> [!div class="nextstepaction"]
> [Face API リファレンス (JavaScript)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/)

* [Face サービスとは](../../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js) にあります。
