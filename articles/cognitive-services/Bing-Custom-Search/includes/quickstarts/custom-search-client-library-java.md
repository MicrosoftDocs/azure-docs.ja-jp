---
title: Bing Custom Search Java クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 300c602a62b0d6b3ba579931b2222d2cd8667656
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948282"
---
Java 用 Bing Custom Search クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Bing Custom Search API を使用すると、関心のあるトピックに合わせてカスタマイズした、広告なしの検索エクスペリエンスを作成できます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch) にあります。

Java 用 Bing Custom Search クライアント ライブラリを使用して、次のことを行います。

* Web で Bing Custom Search インスタンスの検索結果を探します。

[リファレンス ドキュメント](/java/api/overview/azure/cognitiveservices/client/bingcustomsearch) | [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [成果物 (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)。
* 最新バージョンの [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。
* Bing Custom Search インスタンス。 「[クイック スタート:初めての Bing Custom Search インスタンスを作成する](../../quick-start.md)」で詳細を確認する。

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

リソースからキーを取得した後、`AZURE_BING_CUSTOM_SEARCH_API_KEY` という名前のキーの[環境変数を作成](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)します。

### <a name="create-a-new-gradle-project"></a>新しい Gradle プロジェクトを作成する

> [!TIP]
> Gradle を使用していない場合、クライアント ライブラリとその他の依存関係マネージャーの詳細については、[Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/) を参照してください。

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir myapp && cd myapp
```

作業ディレクトリから `gradle init` コマンドを実行します。 次のコマンドでは、アプリケーションを構成するために実行時に使用される *build.gradle.kts* ファイルを含む、Gradle 用の重要なビルド ファイルが作成されます。

```console
gradle init --type basic
```

**DSL** を選択するよう求められたら、**Kotlin** を選択します。

## <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

*build.gradle.kts* を検索し、任意の IDE またはテキスト エディターで開きます。 その後、次のビルド構成をコピーします。 クライアント ライブラリは `dependencies` の下に格納してください。

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

サンプル アプリ用のフォルダーを作成します。 作業ディレクトリから、次のコマンドを実行します。

```console
mkdir src/main/java
```

新しいフォルダーに移動し、*BingCustomSearchSample.java* という名前のファイルを作成します。 それを開き、次の `import` ステートメントを追加します。


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

`BingCustomSearchSample` という名前のクラスを作成します

```java
public class BingCustomSearchSample {
}
```

そのクラスに、`main` メソッドと、リソースのキーに使用する変数を作成します。 アプリケーションを起動した後で環境変数を作成した場合、変数にアクセスするには、それを実行しているエディター、IDE、またはシェルを閉じて再度開きます。 これらのメソッドは後で定義します。

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>オブジェクト モデル

Bing Custom Search クライアントは、[BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager) オブジェクトの [authenticate()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) メソッドから作成される [BingCustomSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) オブジェクトです。 クライアントの [BingCustomInstances.search()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) メソッドを使用して、検索要求を送信できます。

API の応答は、検索クエリについての情報と検索結果が含まれる [SearchResponse](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse) オブジェクトです。

## <a name="code-examples"></a>コード例

次のコード スニペットでは、Java 用 Bing Custom Search クライアント ライブラリを使用して以下のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [カスタム検索インスタンスから検索結果を取得する](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>クライアントを認証する

main メソッドには、キーを取得してその `authenticate()` を呼び出す [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) オブジェクトが含まれる必要があります。

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>カスタム検索インスタンスから検索結果を取得する

クライアントの [BingCustomInstances.search()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) 関数を使用して、検索クエリをカスタム インスタンスに送信します。 `withCustomConfig` をカスタム構成 ID に設定します。既定値は `1` です。 API から応答を取得した後、検索結果が見つかったかどうかを確認します。 その場合は、応答の `webPages().value().get()` 関数を呼び出して最初の検索結果を取得し、結果の名前と URL を出力します。

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>アプリケーションの実行

プロジェクトのメイン ディレクトリから次のコマンドを使用して、アプリをビルドします。

```console
gradle build
```

`run` ゴールを使用してアプリケーションを実行します。

```console
gradle run
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](../../tutorials/custom-search-web-page.md)
