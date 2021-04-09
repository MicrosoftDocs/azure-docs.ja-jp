---
title: クイック スタート:Go 用 Computer Vision クライアント ライブラリ
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Go 用 Computer Vision クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 9cb46a57792ecdd650a8a9f5025a5055257057ec
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622262"
---
<a name="HOLTop"></a>

Computer Vision クライアント ライブラリは次に使用できます。

* タグ、テキストの説明、顔、成人向けコンテンツなどについて、画像を分析します。
* Read API を使用して、印刷されたテキストと手書きのテキストを読み取ります。

[リファレンス ドキュメント](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [パッケージ](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [Go](https://golang.org/dl/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* キーとエンドポイント URL 用に、それぞれ `COMPUTER_VISION_SUBSCRIPTION_KEY` と `COMPUTER_VISION_ENDPOINT` という名前の[環境変数を作成](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)します。

## <a name="setting-up"></a>設定

### <a name="create-a-go-project-directory"></a>Go プロジェクト ディレクトリを作成する

コンソール ウィンドウ (cmd、PowerShell、ターミナル、Bash) で、Go プロジェクト用に `my-app` という名前の新しいワークスペースを作成し、そこに移動します。

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

ワークスペースには次の 3 つのフォルダーが格納されます。

* **src** - このディレクトリには、ソース コードとパッケージが格納されます。 `go get` コマンドを使用してインストールされるパッケージはすべてこのディレクトリに入ります。
* **pkg** - このディレクトリには、コンパイル済みの Go パッケージ オブジェクトが格納されます。 これらのファイルにはいずれも `.a` という拡張子が付きます。
* **bin** - このディレクトリには、`go install` を実行するときに作成されたバイナリ実行可能ファイルが格納されます。

> [!TIP]
> Go ワークスペースの構造の詳細については、[Go 言語のドキュメント](https://golang.org/doc/code.html#Workspaces)を参照してください。 このガイドには、`$GOPATH` と `$GOROOT` の設定に関する情報が記載されています。

### <a name="install-the-client-library-for-go"></a>Go 用クライアント ライブラリをインストールする

次に、Go 用クライアント ライブラリをインストールします。

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

または、dep を使用している場合は、リポジトリ内で次を実行します。

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Go アプリケーションを作成する 

次に、**src** ディレクトリに `sample-app.go` という名前のファイルを作成します。

```bash
cd src
touch sample-app.go
```

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) にあり、このクイックスタートのコード例が含まれています。

普段使用している IDE またはテキスト エディターで `sample-app.go` を開きます。

スクリプトのルートでコンテキストを宣言します。 このオブジェクトは、ほとんどの Computer Vision 関数呼び出しを実行するために必要です。

### <a name="find-the-subscription-key-and-endpoint"></a>サブスクリプション キーとエンドポイントを見つける

Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Computer Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 サブスクリプション キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 

Computer Vision のサブスクリプション キーとエンドポイントの変数を作成します。 サブスクリプション キーとエンドポイントを、次のコード内に示されている場所に貼り付けます。 Computer Vision エンドポイントの形式は `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` です。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> 終わったらコードからサブスクリプション キーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../../key-vault/general/overview.md) が考えられます。

次に、さまざまな Computer Vision 操作を実行するコードの追加を開始します。

> [!div class="nextstepaction"]
> [クライアントを設定しました](?success=set-up-client#object-model) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、Computer Vision Go SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | このクラスは、イメージの分析やテキストの読み取りなど、すべての Computer Vision 機能に必要です。 サブスクリプション情報を使用してインスタンス化し、そのインスタンスを使用して、画像に対するほとんどの操作を実行することができます。|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| この型には **Analyze Eimage** 関数呼び出しの結果が含まれます。 カテゴリ固有の各関数には同様の型があります。|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| この型には、バッチ読み取り操作の結果が含まれます。 |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| この型は、標準の分析操作で実行できるさまざまな種類の画像分析を定義します。 必要に応じて、VisualFeatureTypes の値のセットを指定します。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Go 用 Computer Vision クライアント ライブラリを使用して以下のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [イメージを分析する](#analyze-an-image)
* [印刷されたテキストと手書きのテキストを読み取る](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE]
> このステップでは、`COMPUTER_VISION_SUBSCRIPTION_KEY` および `COMPUTER_VISION_ENDPOINT` という名前の、Computer Vision キーとエンドポイントのそれぞれの [環境変数を作成](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) してあることを前提としています。

`main` 関数を作成し、その関数に次のコードを追加して、エンドポイントとキーでクライアントをインスタンス化します。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [クライアントを認証しました](?success=authenticate-client#analyze-an-image) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)

## <a name="analyze-an-image"></a>イメージを分析する

次のコードでは、クライアント オブジェクトを使用して、リモート画像を分析し、コンソールに結果を出力します。 テキストの説明、分類、タグの一覧、検出されたオブジェクト、検出されたブランド、検出された顔、成人向けコンテンツ フラグ、メインの色、および画像の種類が返されます。

### <a name="set-up-test-image"></a>テスト画像の設定

最初に、分析する画像の URL への参照を保存します。 これを `main` 関数内に配置します。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> ローカルの画像を分析することもできます。 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) のメソッドを参照してください (**DescribeImageInStream** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上のサンプル コードを参照してください。

### <a name="specify-visual-features"></a>視覚的特徴を指定する

次の関数の呼び出しにより、さまざまなビジュアル機能がサンプル イメージから抽出されます。 これらの関数は、次のセクションで定義します。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>画像の説明の取得

次の関数は、画像に対して生成されたキャプションの一覧を取得します。 イメージの説明の詳細については、「[イメージの説明](../../concept-describing-images.md)」を参照してください。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>画像カテゴリの取得

次の関数は、画像の検出されたカテゴリを取得します。 詳細については、「[イメージの分類](../../concept-categorizing-images.md)」を参照してください。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>画像タグの取得

次の関数は、画像内の検出されたタグのセットを取得します。 詳細については、「[コンテンツ タグ](../../concept-tagging-images.md)」を参照してください。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>物体を検出する

次の関数は、画像から身の回りの物を検出してコンソールに出力します。 詳細については、「[物体検出](../../concept-object-detection.md)」を参照してください。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>ブランドを検出する

次のコードは、企業のブランドとロゴを画像から検出してコンソールに出力します。 詳細については、「[ブランド検出](../../concept-brand-detection.md)」を参照してください。

最初に、`main` 関数内の新しいイメージへの参照を宣言します。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

次のコードでは、ブランド検出機能を定義しています。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>顔を検出する

次の関数は、画像内で検出された顔を、その四角形の座標と特定の顔属性と共に返します。 詳しくは、「[顔検出](../../concept-detecting-faces.md)」をご覧ください。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>成人向け、わいせつ、または不快なコンテンツを検出する

次の関数は、画像内で検出された成人向けのコンテンツの存在を出力します。 詳細については、「[成人向けコンテンツ、わいせつなコンテンツ、不快なコンテンツ](../../concept-detecting-adult-content.md)」を参照してください。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>画像の配色の取得

次の関数は、ドミナント カラー、アクセント カラーなど、画像内で検出された色の属性を出力します。 詳細については、「[配色](../../concept-detecting-color-schemes.md)」を参照してください。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>ドメイン固有のコンテンツの取得

Computer Vision では、特殊なモデルを使用して、画像をさらに分析できます。 詳細については、[ドメイン固有のコンテンツ](../../concept-detecting-domain-content.md)に関するページを参照してください。 

次のコードは、画像内の検出された有名人に関するデータを解析します。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

次のコードは、画像内の検出されたランドマークに関するデータを解析します。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>画像の種類を取得する

次の関数は、画像の種類 (クリップ アートであるか線画であるか) に関する情報を出力します。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [画像を分析しました](?success=analyze-image#read-printed-and-handwritten-text) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>印刷されたテキストと手書きのテキストを読み取る

Computer Vision は、画像に映っているテキストを読み取って、文字ストリームに変換することができます。 このセクションのコードでは `RecognizeTextReadAPIRemoteImage` 関数を定義しています。クライアント オブジェクトを使用して、印刷されたテキストや手書きのテキストを画像から検出して抽出するものです。

`main` 関数にサンプル イメージ参照と関数呼び出しを追加します。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> ローカルの画像からテキストを抽出することもできます。 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) のメソッドを参照してください (**BatchReadFileInStream** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上のサンプル コードを参照してください。

### <a name="call-the-read-api"></a>Read API を呼び出す

テキストを読み取るための新しい関数 `RecognizeTextReadAPIRemoteImage` を定義します。 以下のコードは、指定された画像に対して **BatchReadFile** メソッドを呼び出すものです。このコードを追加します。 このメソッドによって操作 ID が返され、画像の内容を読み取る非同期プロセスが開始されます。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>読み取りの結果を取得する

次に、**BatchReadFile** の呼び出しから返された操作 ID を取得し、 **GetReadOperationResult** と共に使用して操作の結果をサービスに照会します。 次のコードは、結果が返されるまで 1 秒間隔で操作をチェックします。 次に、抽出されたテキスト データをコンソールに出力します。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>読み取りの結果を表示する

取得したテキスト データを解析して表示する次のコードを追加すれば、関数の定義は完了です。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [テキストを読み取りました](?success=read-printed-handwritten-text#run-the-application) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーション ディレクトリから `go run` コマンドを使用してアプリケーションを実行します。

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [アプリケーションを実行しました](?success=run-the-application#clean-up-resources) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [リソースをクリーンアップしました](?success=clean-up-resources#next-steps) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Computer Vision API リファレンス (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)


* [Computer Vision とは](../../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) にあります。
