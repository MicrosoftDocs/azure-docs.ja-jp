---
title: クイック スタート:Java 用 Content Moderator クライアント ライブラリ |Microsoft Docs
description: Java 用 Content Moderator クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: pafarley
ms.openlocfilehash: 5d1575818ac35c45af8a7df59c0853389d01031c
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698543"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>クイック スタート:Java 用 Content Moderator クライアント ライブラリ

Java 用 Content Moderator クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Content Moderator は、テキスト、画像、ビデオのコンテンツに不快感を与える可能性がある内容、リスクのある内容、その他望ましくない可能性のある内容が含まれているかどうかを確認できるコグニティブ サービスです。 このサービスでは、そのような内容が検出されると、それに応じたラベル (フラグ) がコンテンツに適用されます。 その後は、法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、フラグの設定されたコンテンツをアプリで処理することができます。

Java 用 Content Moderator クライアント ライブラリは、次の目的で使用できます。

* 成人向けまたはきわどいコンテンツの画像、テキスト、または人間の顔をモデレートします。

[リファレンス ドキュメント](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [成果物 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [サンプル](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=content+moderator&sort=0)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。

## <a name="setting-up"></a>設定

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator の Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル コンピューター上に Content Moderator 用のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)でこれを入手できます。  
* [Azure portal](https://portal.azure.com/) でご利用のリソースを表示する。

試用版のサブスクリプションまたはリソースからキーを取得した後、`AZURE_CONTENTMODERATOR_KEY` という名前のキーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

### <a name="create-a-new-gradle-project"></a>新しい Gradle プロジェクトを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```
`gradle init` を実行します。 次のコマンドを実行すると、*build.gradle.kts* を含む、Gradle 用の重要なビルド ファイルが作成されます。これは、アプリケーションを作成して構成するために、実行時に使用されます。 作業ディレクトリから次のコマンドを実行します。

```console
gradle init --type basic
```

ビルド スクリプト DSL を選択するよう求められたら、**Kotlin** を選択します。

*build.gradle.kts* を検索し、希望する IDE またはテキストエディターで開きます。 その後、次のビルド構成をコピーします。 この構成では、エントリポイントが **ContentModeratorQuickstart** クラスである Java アプリケーションとしてプロジェクトを定義します。 これにより、Content Moderator SDK と、JSON シリアル化用に Gson SDK がインポートされます。

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

作業ディレクトリから次のコマンドを実行し、プロジェクト ソース フォルダーを作成します。

```console
mkdir -p src/main/java
```

次に、*ContentModeratorQuickstart.java* という名前のファイルを新しいフォルダーに作成します。 希望するエディターまたは IDE でファイルを開き、先頭で次のライブラリをインポートします。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>オブジェクト モデル

次のクラスでは、Content Moderator Java SDK の主要な機能の一部を処理します。

|名前|説明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|このクラスは、すべての Content Moderator の機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|このクラスでは、成人向けコンテンツ、個人情報、または人間の顔の画像を分析するための機能が提供されます。|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|このクラスでは、言語、冒涜的表現、エラー、個人情報のテキストを分析するための機能が提供されます。|
|[Reviews](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|このクラスでは、ジョブ、カスタム ワークフロー、人によるレビューを作成するためのメソッド含む、レビュー API の機能が提供されます。|


## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Java 用 Content Moderator クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [画像のモデレート](#moderate-images)

## <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE]
> このステップでは、`AZURE_CONTENTMODERATOR_KEY` という名前の Content Moderator キーの[環境変数が作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)されていることを前提とします。

アプリケーションの `main` メソッドで、サブスクリプション エンドポイント値とサブスクリプション キー環境変数を使用して、[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) オブジェクトを作成します。 

> [!NOTE]
> アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>画像のモデレート

### <a name="get-images"></a>画像を取得する

プロジェクトの **src/main/** フォルダーで、**resources** フォルダーを作成し、そこに移動します。 次に、新しいテキストファイル (*ImageFiles.txt*) を作成します。 このファイルでは、画像の URL を追加して&mdash;行ごとに 1 つの URL を分析します。 次のサンプル コンテンツを使用することができます。

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="use-helper-class"></a>ヘルパー クラスを使用する

次に、*ContentModeratorQuickstart.java* ファイルに、**ContentModeratorQuickstart** クラス内に次のクラス定義を追加します。 この内部クラスは、後で画像のモデレート プロセスで使用されます。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>画像を反復処理する

次に、`main` メソッドの末尾に、次のコードを追加します。 または、`main` から呼び出される別のメソッドに追加することもできます。 このコードは、_ImageFiles.txt_ ファイルの各行をステップ実行します。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>成人向けコンテンツやきわどい内容を確認する
コードのこの行では、指定した URL の画像の成人向けコンテンツやきわどい内容が確認されます。 これらの用語の情報については、画像のモデレートの概念的なガイドを参照してください。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>テキストを確認する
コードのこの行では、画像の表示可能なテキストが確認されます。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>顔を確認する
コードのこの行では、画像の人間の顔が確認されます。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

最後に、返された情報が `EvaluationData` リストに格納されます。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>結果を出力する

`while` ループの後に、次のコードを追加します。これにより、結果がコンソールと出力ファイル *src/main/resources/ModerationOutput.json* に出力されます。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

`try` ステートメントを終了し、`catch` ステートメントを追加してメソッドを完了します。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>アプリケーションの実行

次を使用してアプリをビルドできます。

```console
gradle build
```

`gradle run` コマンドを使用してアプリケーションを実行します。

```console
gradle run
```

次に、*src/main/resources/ModerationOutput.json* ファイルに移動し、コンテンツ モデレーションの結果を表示します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Content Moderator Java ライブラリを使用して、モデレーション タスクを行う方法について学習しました。 次は、概念的なガイドを読んで、画像や他のメディアのモデレーションに関する詳細について学習します。

> [!div class="nextstepaction"]
>[画像のモデレートの概念](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Azure Content Moderator とは](./overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java) にあります。