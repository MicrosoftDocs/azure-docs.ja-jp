---
title: クイック スタート:Java 用 Bing Custom Search クライアント ライブラリ | Microsoft Docs
description: Bing Custom Search インスタンスに検索結果を要求することで、Java 用 Bing Custom Search クライアント ライブラリの使用を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: f0a3008dace4e9382bbb6b6d9465ce1c9b4ddc6c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976062"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>クイック スタート:Java 用 Bing Custom Search クライアント ライブラリ

Java 用 Bing Custom Search クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Bing Custom Search API を使用すると、関心のあるトピックに合わせてカスタマイズした、広告なしの検索エクスペリエンスを作成できます。

Java 用 Bing Custom Search クライアント ライブラリを使用して、次のことを行います。

* Web で Bing Custom Search インスタンスの検索結果を探します。 

[リファレンス ドキュメント](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [成果物 (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)。
* 最新バージョンの [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。
* Bing Custom Search インスタンス。 「[クイック スタート:初めての Bing Custom Search インスタンスを作成する](quick-start.md)」で詳細を確認する。

## <a name="setting-up"></a>設定

### <a name="create-a-bing-custom-search-azure-resource"></a>Bing Custom Search の Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) またはローカル コンピューター上の [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、Bing Custom Search 用のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 これは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。  
* [Azure portal](https://portal.azure.com/) でご利用のリソースを表示する。

試用版のサブスクリプションまたはリソースからキーを取得した後、`AZURE_BING_CUSTOM_SEARCH_API_KEY` という名前のキーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

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

Bing Custom Search クライアントは、[BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) オブジェクトの [authenticate()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) メソッドから作成される [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) オブジェクトです。 クライアントの [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) メソッドを使用して、検索要求を送信できます。

API の応答は、検索クエリについての情報と検索結果が含まれる [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) オブジェクトです。

## <a name="code-examples"></a>コード例

次のコード スニペットでは、Java 用 Bing Custom Search クライアント ライブラリを使用して以下のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [カスタム検索インスタンスから検索結果を取得する](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>クライアントを認証する

main メソッドには、キーを取得してその `authenticate()` を呼び出す [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) オブジェクトが含まれる必要があります。

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>カスタム検索インスタンスから検索結果を取得する

クライアントの [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) 関数を使用して、検索クエリをカスタム インスタンスに送信します。 `withCustomConfig` をカスタム構成 ID に設定します。既定値は `1` です。 API から応答を取得した後、検索結果が見つかったかどうかを確認します。 その場合は、応答の `webPages().value().get()` 関数を呼び出して最初の検索結果を取得し、結果の名前と URL を出力します。 

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

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](./tutorials/custom-search-web-page.md)
