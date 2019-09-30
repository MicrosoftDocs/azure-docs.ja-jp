---
title: クイック スタート:変更、アプリのトレーニング、Java - LUIS
titleSuffix: Azure Cognitive Services
description: この Java クイック スタートでは、Home Automation アプリに発話の例を追加してアプリをトレーニングします。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 934ec14356448fc03d501a407c45086035ab3e04
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307526"
---
# <a name="quickstart-change-model-using-java"></a>クイック スタート: Java を使ってモデルを変更する 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit Standard Edition)
* [Google の GSON JSON ライブラリ](https://github.com/google/gson)。

[!INCLUDE [Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>発話の例の JSON ファイル

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>クイック スタート コードの作成

1. `AddUtterances.java` という名前のファイルに Java 依存関係を追加します。

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. `AddUtterances` クラスを作成します。 このクラスには、以下のすべてのコード スニペットが含まれています。

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. LUIS 定数をこのクラスに追加します。 次のコードをコピーし、実際のオーサリング キー、アプリケーション ID、およびバージョン ID に変更します。

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. LUIS API を呼び出すメソッドを AddUtterances クラスに追加します。 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. LUIS API からの HTTP 応答のためのメソッドを AddUtterances クラスに追加します。

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. AddUtterances クラスに例外処理を追加します。 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. メイン関数を AddUtterances クラスに追加します。

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>コードのビルド

依存関係を使用して AddUtterance をコンパイルする

```console
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>コードの実行
引数を指定せずに `AddUtterance` を呼び出すと、アプリに LUIS の発話は追加されますが、トレーニングは実行されません。

```console
> java -classpath .;gson-2.8.2.jar AddUtterances
```

このコマンドラインで、発話の追加 API を呼び出した結果が表示されます。 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイック スタートで作成したファイルは、完了後、すべて削除してください。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [プログラムで LUIS アプリを構築する](luis-tutorial-node-import-utterances-csv.md) 
