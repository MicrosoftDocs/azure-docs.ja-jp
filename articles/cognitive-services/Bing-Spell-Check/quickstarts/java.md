---
title: クイック スタート:REST API と Java を使用してスペルをチェックする - Bing Spell Check
titleSuffix: Azure Cognitive Services
description: Bing Spell Check REST API を使用してスペルと文法をチェックしてみましょう。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 56a5215daea9289a481e0a51575ef0ee3cbdb3a0
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322076"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>クイック スタート:Bing Spell Check REST API と Java を使用してスペルをチェックする

このクイック スタートを使用して、Bing Spell Check の REST API を呼び出してみましょう。 このシンプルな Java アプリケーションは、API に要求を送信して、一連の修正候補を返します。 

このアプリケーションは Java で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このアプリケーションのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java) で入手できます。

## <a name="prerequisites"></a>前提条件

* Java Development Kit(JDK) 7 以降

* [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) または最新バージョンの [Gson](https://github.com/google/gson) をインポートします。 コマンドライン実行の場合は、メイン クラスがある Java フォルダーに `.jar` を追加します。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>アプリケーションを作成して初期化する

1. 好みの IDE またはエディターで、選択したクラス名を使用して新しい Java プロジェクトを作成し、以下のパッケージをインポートします。

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. API エンドポイントのホスト、パス、サブスクリプション キーの変数を作成します。 その後、市場、スペル チェック対象のテキスト、スペル チェック モードの文字列に使用する変数を作成します。 次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>API 要求を作成して送信する

1. API 要求を作成して送信する `check()` という関数を作成します。 この関数内で、次の手順で指定したコードを追加します。 要求パラメーターの文字列を作成します。

   1. `=` 演算子を使用して、`mkt` パラメーターに市場コードを割り当てます。 

   1. `&` 演算子を使用して `mode` パラメーターを追加し、スペルチェック モードを割り当てます。 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. エンドポイントのホスト、パス、パラメーターの文字列を結合して URL を作成します。 新しい `HttpsURLConnection` オブジェクトを作成します。

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. その URL への接続を開きます。 要求メソッドを `POST` に設定し、要求パラメーターを追加します。 `Ocp-Apim-Subscription-Key` ヘッダーには必ずサブスクリプション キーを追加してください。

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. 新しい `DataOutputStream` オブジェクトを作成して API に要求を送信します。

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>API の応答を書式設定して読み取る

1. `prettify()` メソッドをクラスに追加します。これにより、読み取りやすい出力の JSON に書式設定されます。

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. `BufferedReader` を作成して、API から応答を読み取ります。 それをコンソールに出力します。
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>API を呼び出す

アプリケーションの main 関数で、前に作成した `check()` メソッドを呼び出します。
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>アプリケーションの実行

プロジェクトをビルドして実行します。 コマンド ラインを使用している場合は、次のコマンドを使用して、アプリケーションをビルドおよび実行します。

1. アプリケーションをビルドします。

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. アプリケーションを実行します。

   ```bash
   java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
   ```

## <a name="example-json-response"></a>JSON の応答例

成功した応答は、次の例に示すように JSON で返されます。

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../tutorials/spellcheck.md)

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
