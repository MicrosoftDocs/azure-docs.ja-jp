---
title: クイック スタート:Java 用 Computer Vision クライアント ライブラリ
titleSuffix: Azure Cognitive Services
description: Java 用 Computer Vision クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 16a487dc007526f685edb52726f5797303a30c11
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966985"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>クイック スタート:Java 用 Computer Vision クライアント ライブラリ

Java 用 Computer Vision クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Computer Vision を使用すると、画像を処理して情報を返すための高度なアルゴリズムにアクセスできます。

Java 用 Computer Vision クライアント ライブラリを使用すると、次のことができます。

* タグ、テキストの説明、顔、成人向けコンテンツなどについて、画像を分析します。

[リファレンス ドキュメント](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [成果物 (Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [サンプル](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。

## <a name="setting-up"></a>設定

### <a name="create-a-computer-vision-azure-resource"></a>Computer Vision Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) またはローカル マシン上の [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、Computer Vision 用のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)でこれを入手できます。  
* [Azure portal](https://portal.azure.com/) でご利用のリソースを表示する。

次に、キーとサービス エンドポイント文字列用に、それぞれ `COMPUTER_VISION_SUBSCRIPTION_KEY` と `COMPUTER_VISION_ENDPOINT` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

### <a name="create-a-new-gradle-project"></a>新しい Gradle プロジェクトを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

作業ディレクトリから `gradle init` コマンドを実行します。 次のコマンドを実行すると、*build.gradle.kts* を含む、Gradle 用の重要なビルド ファイルが作成されます。これは、アプリケーションを作成して構成するために、実行時に使用されます。

```console
gradle init --type basic
```

**DSL** を選択するよう求められたら、**Kotlin** を選択します。

*build.gradle.kts* を検索し、任意の IDE またはテキスト エディターで開きます。 その後、次のビルド構成をコピーします。 この構成では、エントリ ポイントが **ComputerVisionQuickstarts** クラスである Java アプリケーションとしてプロジェクトを定義します。 これによって、Computer Vision ライブラリがインポートされます。

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

作業ディレクトリから次のコマンドを実行し、プロジェクト ソース フォルダーを作成します。

```console
mkdir -p src/main/java
```

新しいフォルダーに移動し、*ComputerVisionQuickstarts.java* という名前のファイルを作成します。 それを任意のエディターまたは IDE で開き、以下の `import` ステートメントを追加します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imports)]

次に、**ComputerVisionQuickstarts** のクラス定義を追加します。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

このクイックスタートでは、Gradle 依存関係マネージャーを使用します。 クライアント ライブラリとその他の依存関係マネージャーの情報については、[Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/) を参照してください。

プロジェクトの *build.gradle.kts* ファイルに、Computer Vision クライアント ライブラリを依存関係として含めます。

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、Computer Vision Java SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | このクラスは、すべての Computer Vision の機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| このクラスは、クライアント オブジェクトから派生し、画像分析、テキスト検出、サムネイル生成など、すべての画像操作を直接処理します。|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| この列挙型は、標準の分析操作で実行できるさまざまな種類の画像分析を定義します。 必要に応じて、VisualFeatureTypes の値のセットを指定します。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Java 用 Computer Vision クライアント ライブラリを使用して以下のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [イメージを分析する](#analyze-an-image)

## <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE]
> このクイックスタートでは、Computer Vision キーのために `COMPUTER_VISION_SUBSCRIPTION_KEY` という名前の[環境変数が作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)されていることを前提としています。

次のコードは、クラスに `main` メソッドを追加し、リソースの Azure エンドポイントとキーのために変数を作成します。 独自のエンドポイント文字列を入力する必要があります。これは、Azure portal の **[概要]** セクションで確認できます。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_mainvars)]

次に、次のコードを追加して [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) オブジェクトを作成し、そのオブジェクトを他のメソッドに渡します。このメソッドは、後で定義します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

## <a name="analyze-an-image"></a>イメージを分析する

次のコードでは、`AnalyzeLocalImage` メソッドを定義しています。このメソッドは、クライアント オブジェクトを使用して、ローカル画像を分析し、結果を出力します。 このメソッドでは、テキストの説明、分類、タグの一覧、検出された顔、成人向けコンテンツ フラグ、メインの色、および画像の種類が返されます。

### <a name="set-up-test-image"></a>テスト画像の設定

まず、プロジェクトの **src/main/** フォルダーに **resources/** フォルダーを作成し、分析する画像を追加します。 次に、**ComputerVisionQuickstarts** クラスに次のメソッド定義を追加します。 必要に応じて、`pathToLocalImage` の値を画像ファイルと一致するように変更します。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> また、URL を使用してリモート画像を分析することもできます。 リモート画像を含むシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java) 上のサンプル コードを参照してください。

### <a name="specify-visual-features"></a>視覚的特徴を指定する

次に、分析で抽出する視覚的特徴を指定します。 完全な一覧については、[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) 列挙型を参照してください。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>分析
このメソッドは、画像分析のスコープごとに詳細な結果をコンソールに出力します。 このメソッドの呼び出しを、Try と Catch ブロックで囲むことをお勧めします。 **analyzeImageInStream** メソッドは、抽出されたすべての情報を含む **ImageAnalysis** オブジェクトを返します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

以降のセクションでは、この情報の解析方法について詳しく説明します。

### <a name="get-image-description"></a>画像の説明の取得

次のコードは、画像に対して生成されたキャプションの一覧を取得します。 詳細については、[画像の説明](../concept-describing-images.md)に関するページを参照してください。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>画像カテゴリの取得

次のコードは、画像の検出されたカテゴリを取得します。 詳細については、[画像の分類](../concept-categorizing-images.md)に関するページを参照してください。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>画像タグの取得

次のコードは、画像内の検出されたタグのセットを取得します。 詳細については、[コンテンツ タグ](../concept-tagging-images.md)に関するページを参照してください。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>顔を検出する

次のコードは、画像内で検出された顔を、その四角形の座標と顔選択属性と共に返します。 詳細については、[顔の検出](../concept-detecting-faces.md)に関するページを参照してください。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-or-racy-content"></a>成人向けまたはわいせつなコンテンツを検出する

次のコードは、画像内で検出された成人向けまたはわいせつなコンテンツの存在を出力します。 詳細については、[成人向けまたはわいせつなコンテンツ](../concept-detecting-adult-content.md)に関するページを参照してください。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>画像の配色の取得

次のコードは、ドミナント カラー、アクセント カラーなど、画像内で検出された色の属性を出力します。 詳細については、[配色](../concept-detecting-color-schemes.md)に関するページを参照してください。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>ドメイン固有のコンテンツの取得

Computer Vision では、特殊なモデルを使用して、画像をさらに分析できます。 詳細については、[ドメイン固有のコンテンツ](../concept-detecting-domain-content.md)に関するページを参照してください。 

次のコードは、画像内の検出された有名人に関するデータを解析します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

次のコードは、画像内の検出されたランドマークに関するデータを解析します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>画像の種類を取得する

次のコードは、画像の種類 (クリップ アートであるか線画であるか) に関する情報を出力します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="run-the-application"></a>アプリケーションの実行

次を使用してアプリをビルドできます。

```console
gradle build
```

`gradle run` コマンドを使用してアプリケーションを実行します。

```console
gradle run
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Computer Vision Java ライブラリを使用して基本的なタスクを行う方法について学習しました。 次は、リファレンス ドキュメントを参照して、ライブラリの詳細について学習してください。

> [!div class="nextstepaction"]
>[Computer Vision リファレンス (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Computer Vision とは](../Home.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java) にあります。