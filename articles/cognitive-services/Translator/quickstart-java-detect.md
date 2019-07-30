---
title: クイック スタート:テキストの言語を検出する (Java) - Translator Text API
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Java と Translator Text REST API を使用して、指定されたテキストの言語を検出する方法について説明します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 35fde6810a788e3d134388144adcd08dfc1e8919
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705580"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-using-java"></a>クイック スタート:Translator Text API と Java を使用してテキストの言語を検出する

このクイック スタートでは、Java と Translator Text REST API を使用して、指定されたテキストの言語を検出する方法について説明します。

このクイック スタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Translator Text リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/try/cognitive-services/)を使用してサブスクリプション キーを取得できます。

>[!TIP]
> すべてのコードを一度に見たい場合は、このサンプルのソース コードを [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Java) で入手できます。

## <a name="prerequisites"></a>前提条件

* [JDK 7 以降](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Translator Text の Azure サブスクリプション キー

## <a name="initialize-a-project-with-gradle"></a>Gradle を使用してプロジェクトを初期化する

まずは、このプロジェクトの作業ディレクトリを作成しましょう。 コマンド ライン (またはターミナル) から、次のコマンドを実行します。

```console
mkdir detect-sample
cd detect-sample
```

次に、Gradle プロジェクトを初期化します。 次のコマンドを実行すると、Gradle の重要なビルド ファイルが作成されます。特に重要なのは `build.gradle.kts` です。これは、アプリケーションを作成して構成するために、実行時に使用されます。 作業ディレクトリから次のコマンドを実行します。

```console
gradle init --type basic
```

**DSL** を選択するよう求められたら、**Kotlin** を選択します。

## <a name="configure-the-build-file"></a>ビルド ファイルを構成する

`build.gradle.kts` の場所を特定し、好みの IDE またはテキスト エディターで開きます。 その後、次のビルド構成をコピーします。

```
plugins {
    java
    application
}
application {
    mainClassName = "Detect"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

このサンプルでは、HTTP 要求の処理に OkHttp を使用し、JSON の処理と解析に Gson を使用していることに注意してください。 ビルド構成について詳しくは、「[Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/)」(新しい Gradle ビルドの作成) をご覧ください。

## <a name="create-a-java-file"></a>Java ファイルを作成する

サンプル アプリ用のフォルダーを作成しましょう。 作業ディレクトリから、次のコマンドを実行します。

```console
mkdir -p src/main/java
```

次に、このフォルダー内に `Detect.java` というファイルを作成します。

## <a name="import-required-libraries"></a>必要なライブラリをインポートする

`Detect.java` を開き、次のインポート ステートメントを追加します。

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>変数の定義

まず、プロジェクトのパブリック クラスを作成する必要があります。

```java
public class Detect {
  // All project code goes here...
}
```

次の行を `Detect` クラスに追加します。

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0";
```

Cognitive Services のマルチサービス サブスクリプションを使用している場合は、要求のパラメーターに `Ocp-Apim-Subscription-Region` も含める必要があります。 [マルチサービス サブスクリプションを使用した認証の詳細を参照してください](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="create-a-client-and-build-a-request"></a>クライアントを作成して要求をビルドする

次の行を `Detect` クラスに追加して、`OkHttpClient` をインスタンス化します。

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

次に、POST 要求をビルドしましょう。 言語検出するテキストは変更してかまいません。

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Salve mondo!\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>応答を解析するための関数を作成する

このシンプルな関数は、Translator Text サービスからの JSON 応答を解析し、整形するものです。

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

最後に、要求を実行して応答を取得します。 次の行をプロジェクトに追加します。

```java
public static void main(String[] args) {
    try {
        Detect detectRequest = new Detect();
        String response = detectRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

以上で、サンプル アプリを実行する準備が整いました。 コマンド ライン (またはターミナル セッション) で作業ディレクトリのルートに移動して、次のコマンドを実行します。

```console
gradle build
```

ビルドが完了したら、次のコマンドを実行します。

```console
gradle run
```

## <a name="sample-response"></a>応答のサンプル

国/地域の省略形は、こちらの[言語一覧](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)で確認してください。

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>次の手順

API のリファレンスを見て、Translator Text API でできるすべてのことを理解してください。

> [!div class="nextstepaction"]
> [API リファレンス](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>関連項目

* [テキストを翻訳する](quickstart-java-translate.md)
* [テキストを表記変換する](quickstart-java-transliterate.md)
* [入力によって言語を識別する](quickstart-java-detect.md)
* [別の翻訳を取得する](quickstart-java-dictionary.md)
* [サポートされている言語の一覧を取得する](quickstart-java-languages.md)
* [入力から文章の長さを判定する](quickstart-java-sentences.md)
