---
title: クイック スタート:Node.js 用 Computer Vision クライアント ライブラリ
description: このクイックスタートでは、Node.js 用 Computer Vision クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: be583fd530424262e8119295d2ff3df303acbfac
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622051"
---
<a name="HOLTop"></a>

Computer Vision クライアント ライブラリは次に使用できます。

* タグ、テキストの説明、顔、成人向けコンテンツなどについて、画像を分析します。
* Read API を使用して、印刷されたテキストと手書きのテキストを読み取ります。

[リファレンスのドキュメント](/javascript/api/@azure/cognitiveservices-computervision/) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [サンプル](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [Node.js](https://nodejs.org/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
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

`ms-rest-azure` および `@azure/cognitiveservices-computervision` NPM パッケージをインストールします。

```console
npm install @azure/cognitiveservices-computervision
```

また、async モジュールもインストールします。

```console
npm install async
```

アプリの `package.json` ファイルが依存関係によって更新されます。

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) にあり、このクイックスタートのコード例が含まれています。

*index.js* という新しいファイルを作成し、テキスト エディターで開きます。

### <a name="find-the-subscription-key-and-endpoint"></a>サブスクリプション キーとエンドポイントを見つける

Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Computer Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 サブスクリプション キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 

Computer Vision のサブスクリプション キーとエンドポイントの変数を作成します。 サブスクリプション キーとエンドポイントを、次のコード内に示されている場所に貼り付けます。 Computer Vision エンドポイントの形式は `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` です。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> 終わったらコードからサブスクリプション キーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../../key-vault/general/overview.md) が考えられます。

> [!div class="nextstepaction"]
> [クライアントを設定しました](?success=set-up-client#object-model) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、Computer Vision Node.js SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | このクラスは、すべての Computer Vision の機能に必要です。 サブスクリプション情報を使用してインスタンス化し、そのインスタンスを使用して、画像に対するほとんどの操作を実行することができます。|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes)| この列挙型は、標準の分析操作で実行できるさまざまな種類の画像分析を定義します。 必要に応じて、**VisualFeatureTypes** の値のセットを指定します。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Node.js 用 Computer Vision クライアント ライブラリを使用して以下のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [イメージを分析する](#analyze-an-image)
* [印刷されたテキストと手書きのテキストを読み取る](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>クライアントを認証する


ご利用のエンドポイントとキーを使用してクライアントをインスタンス化します。 キーとエンドポイントを使用して [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) オブジェクトを作成し、それを使用して [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) オブジェクトを作成します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

次に、関数 `computerVision` を定義し、プライマリ関数とコールバック関数を使用して非同期の series を宣言します。 主要な関数にクイックスタート コードを追加し、スクリプトの一番下にある `computerVision` を呼び出します。 このクイックスタートの残りのコードは、`computerVision` 関数に属します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [クライアントを認証しました](?success=authenticate-client#analyze-an-image) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)

## <a name="analyze-an-image"></a>イメージを分析する

このセクションのコードでは、リモートの画像を分析して、さまざまなビジュアル機能を抽出します。 これらの操作は、クライアント オブジェクトの **analyzeImage** メソッドの一部として実行するか、個別のメソッドを使用して呼び出すことができます。 詳細については、[リファレンス ドキュメント](/javascript/api/@azure/cognitiveservices-computervision/)を参照してください。

> [!NOTE]
> ローカルの画像を分析することもできます。 [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) のメソッドを参照してください (**analyzeImageInStream** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) 上のサンプル コードを参照してください。

### <a name="get-image-description"></a>画像の説明の取得

次のコードは、画像に対して生成されたキャプションの一覧を取得します。 詳細については、[画像の説明](../../concept-describing-images.md)に関するページを参照してください。

まず分析する画像の URL を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

次に画像の説明を取得してコンソールに出力する次のコードを追加します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>画像カテゴリの取得

次のコードは、画像の検出されたカテゴリを取得します。 詳細については、[画像の分類](../../concept-categorizing-images.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

ヘルパー関数 `formatCategories` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>画像タグの取得

次のコードは、画像内の検出されたタグのセットを取得します。 詳細については、[コンテンツ タグ](../../concept-tagging-images.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

ヘルパー関数 `formatTags` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>物体を検出する

次のコードは、画像から身の回りの物を検出してコンソールに出力します。 詳細については、[物体の検出](../../concept-object-detection.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

上、左、下、および右の座標と、幅および高さを返すように、ヘルパー関数 `formatRectObjects` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>ブランドを検出する

次のコードは、企業のブランドとロゴを画像から検出してコンソールに出力します。 詳細については、[ブランドの検出](../../concept-brand-detection.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>顔を検出する

次のコードは、画像内で検出された顔を、その四角形の座標と顔選択属性と共に返します。 詳細については、[顔の検出](../../concept-detecting-faces.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

ヘルパー関数 `formatRectFaces` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>成人向け、わいせつ、または不快なコンテンツを検出する

次のコードは、画像内で検出された成人向けのコンテンツの存在を出力します。 詳細については、「[成人向けコンテンツ、わいせつなコンテンツ、不快なコンテンツ](../../concept-detecting-adult-content.md)」を参照してください。

使用する画像の URL を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

次に、アダルト コンテンツを検出し、結果をコンソールに出力する次のコードを追加します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>画像の配色の取得

次のコードは、ドミナント カラー、アクセント カラーなど、画像内で検出された色の属性を出力します。 詳細については、[配色](../../concept-detecting-color-schemes.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

コンソールに配色の詳細を出力するヘルパー関数 `printColorScheme` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>ドメイン固有のコンテンツの取得

Computer Vision では、特殊なモデルを使用して、画像をさらに分析できます。 詳細については、[ドメイン固有のコンテンツ](../../concept-detecting-domain-content.md)に関するページを参照してください。

まず分析する画像の URL を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

次のコードは、画像内の検出されたランドマークに関するデータを解析します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

検出されたランドマークに関する位置データを解析するヘルパー関数 `formatRectDomain` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>画像の種類を取得する

次のコードは、画像の種類 (クリップ アートであるか線画であるか) に関する情報を出力します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

ヘルパー関数 `describeType` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [画像を分析しました](?success=analyze-image#read-printed-and-handwritten-text) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>印刷されたテキストと手書きのテキストを読み取る

Computer Vision では、画像に映っているテキストを抽出し、文字ストリームに変換することができます。 このサンプルでは、Read 操作を使用します。

### <a name="set-up-test-images"></a>テスト画像を設定する

テキストを抽出する画像の URL の参照を保存します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> また、ローカルの画像からテキストを読み取ることもできます。 [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) のメソッドを参照してください (**readInStream** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) 上のサンプル コードを参照してください。

### <a name="call-the-read-api"></a>Read API を呼び出す

Read 呼び出しの状態値を表す次のフィールドを関数内に定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

次のコードを追加します。これによって、指定された画像の `readTextFromURL` 関数が呼び出されます。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

`readTextFromURL` 関数を定義します。 これにより、クライアント オブジェクトの **read** メソッドが呼び出され、操作 ID が返され、画像の内容を読み取る非同期プロセスが開始されます。 次に、結果が返されるまで、操作 ID を使用して操作の状態が確認されます。 その後、抽出された結果が返されます。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

次に、ヘルパー関数 `printRecText` を定義します。これにより、Read 操作の結果がコンソールに出力されます。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [テキストを読み取りました](?success=read-printed-handwritten-text#run-the-application) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```

> [!div class="nextstepaction"]
> [アプリケーションを実行しました](?success=run-the-application#clean-up-resources) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [リソースをクリーンアップしました](?success=clean-up-resources#next-steps) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Computer Vision API リファレンス (Node.js)](/javascript/api/@azure/cognitiveservices-computervision/)


* [Computer Vision とは](../../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) にあります。
