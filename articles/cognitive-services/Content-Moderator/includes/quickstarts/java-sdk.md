---
title: Content Moderator Java クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Java 用 Azure Content Moderator クライアント ライブラリの使用を開始する方法について説明します。 法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、コンテンツ フィルタリング ソフトウェアをアプリに組み込みます。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 147985ce5aff54f3784363720a576b7e46d6e647
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948332"
---
Java 用 Azure Content Moderator クライアント ライブラリの使用を開始します。 以下の手順に従って、Maven パッケージをインストールし、基本タスクのコード例を試してみましょう。 

Content Moderator は、不快感を与える可能性がある内容、リスクのある内容、その他望ましくない可能性のある内容を管理できる AI サービスです。 AI を利用したコンテンツ モデレーション サービスを使用して、テキスト、画像、ビデオをスキャンし、コンテンツ フラグを自動的に適用します。 法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、コンテンツ フィルタリング ソフトウェアをアプリに組み込みます。

Java 用 Content Moderator クライアント ライブラリは、次の目的で使用できます。

* テキストのモデレート
* 画像のモデレート

[リファレンス ドキュメント](/java/api/overview/azure/cognitiveservices/client/contentmoderator) | [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator) |[成果物 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [サンプル](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Content Moderator リソースを作成"  target="_blank">Content Moderator リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Content Moderator に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

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

## <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

*build.gradle.kts* を検索し、希望する IDE またはテキストエディターで開きます。 その後、次のビルド構成をコピーします。 この構成では、エントリポイントが **ContentModeratorQuickstart** クラスである Java アプリケーションとしてプロジェクトを定義します。 これにより、Content Moderator クライアント ライブラリと、JSON シリアル化用に GSON SDK がインポートされます。

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

### <a name="create-a-java-file"></a>Java ファイルを作成する


作業ディレクトリから次のコマンドを実行し、プロジェクト ソース フォルダーを作成します。

```console
mkdir -p src/main/java
```

新しいフォルダーに移動し、*ContentModeratorQuickstart.java* という名前のファイルを作成します。 それを任意のエディターまたは IDE で開き、以下の `import` ステートメントを追加します。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java) にあり、このクイックスタートのコード例が含まれています。

アプリケーションの **ContentModeratorQuickstart** クラスに、リソースのキーとエンドポイントの変数を作成します。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Content Moderator リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

アプリケーションの **main** メソッドで、このクイックスタートで使用するメソッドの呼び出しを追加します。 その定義は後から行います。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>オブジェクト モデル

以下のクラスでは、Content Moderator Java クライアント ライブラリの主要な機能の一部を処理します。

|名前|説明|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|このクラスは、すべての Content Moderator の機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|このクラスでは、成人向けコンテンツ、個人情報、または人間の顔の画像を分析するための機能が提供されます。|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|このクラスでは、言語、冒涜的表現、エラー、個人情報のテキストを分析するための機能が提供されます。|
|[Reviews](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|このクラスでは、ジョブ、カスタム ワークフロー、人によるレビューを作成するためのメソッド含む、レビュー API の機能が提供されます。|


## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Java 用 Content Moderator クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [テキストのモデレート](#moderate-text)
* [画像のモデレート](#moderate-images)


## <a name="authenticate-the-client"></a>クライアントを認証する

アプリケーションの `main` メソッドで、サブスクリプション エンドポイント値とサブスクリプション キーを使用して、[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient) オブジェクトを作成します。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>テキストのモデレート

### <a name="set-up-sample-text"></a>サンプル テキストを設定する

**ContentModeratorQuickstart** クラスの一番上に、ローカル テキスト ファイルの参照を定義します。 プロジェクト ディレクトリに .txt ファイルを追加し、分析したいテキストを入力します。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>テキストを分析する

.txt ファイルを読み取る新しいメソッドを作成し、それぞれの行で **screenText** メソッドを呼び出します。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>テキストのモデレート結果を出力する

モデレート結果を出力する次のコードを、プロジェクト ディレクトリの .json ファイルに追加します。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

`try` と `catch` のステートメントをしあげてメソッドを完成させます。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>画像のモデレート

### <a name="set-up-sample-image"></a>サンプル画像を設定する

新しいメソッドで **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** オブジェクトを構築し、画像を指し示す URL 文字列を渡します。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>ヘルパー クラスを定義する

次に、*ContentModeratorQuickstart.java* ファイルに、**ContentModeratorQuickstart** クラス内に次のクラス定義を追加します。 この内部クラスは、画像のモデレート プロセスで使用されます。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>コンテンツの分析
コードのこの行では、指定した URL の画像の成人向けコンテンツやきわどい内容が確認されます。 これらの用語の情報については、画像のモデレートの概念的なガイドを参照してください。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>テキストを確認する
コードのこの行では、画像の表示可能なテキストが確認されます。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>顔を確認する
コードのこの行では、画像の人間の顔が確認されます。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

最後に、返された情報が `EvaluationData` リストに格納されます。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>結果を出力する

`while` ループの後に、次のコードを追加します。これにより、結果がコンソールと出力ファイル *src/main/resources/ModerationOutput.json* に出力されます。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

`try` ステートメントを終了し、`catch` ステートメントを追加してメソッドを完了します。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

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

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Content Moderator Java ライブラリを使用して、モデレーション タスクを行う方法について学習しました。 次は、概念的なガイドを読んで、画像や他のメディアのモデレーションに関する詳細について学習します。

> [!div class="nextstepaction"]
> [画像のモデレートの概念](../../image-moderation-api.md)

* [Azure Content Moderator とは](../../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java) にあります。
