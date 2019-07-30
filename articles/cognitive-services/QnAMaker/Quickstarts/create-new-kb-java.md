---
title: クイック スタート:ナレッジ ベースを作成する - REST (Java) - QnA Maker
titlesuffix: Azure Cognitive Services
description: この Java REST ベースのクイック スタートでは、Cognitive Services API アカウントの Azure ダッシュボードに表示される QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 298f7e90bb6848d7b0af032bdaff8724a69c227f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249669"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>クイック スタート:Java を使用して QnA Maker のナレッジ ベースを作成する

このクイック スタートでは、QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。 QnA Maker は、[データ ソース](../Concepts/data-sources-supported.md)の FAQ などの半構造化コンテンツから質問とその回答を自動的に抽出します。 ナレッジ ベースのモデルは、API 要求の本文で送信される JSON で定義されます。

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="create-a-knowledge-base-file"></a>ナレッジ ベース ファイルを作成する

`CreateKB.java` という名前でファイルを作成します。

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

`CreateKB.java` の先頭に次の行を追加して、プロジェクトに必要な依存関係を追加します。

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>必要な定数を追加する
上記の必要な依存関係の後に、QnA Maker にアクセスするために必要な定数を `CreateKB` クラスに追加します。 `subscriptionKey` 変数の値を自分の QnA Maker キーに置き換えます。 クラスを閉じる最後の中かっこを追加する必要はありません。このクイック スタートの終わりにある最終的なコード スニペットに存在します。

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>KB モデル定義クラスを追加する
これらの定数に続けて、モデル定義オブジェクトを JSON にシリアル化するための次のクラスと関数を `CreateKB` クラス内に追加します。

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>補助的な関数を追加する

次に、以下の補助的な関数を `CreateKB` クラス内に追加します。

1. JSON を読みやすい形式で出力するために、次の関数を追加します。

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. HTTP 応答を管理するために、次のクラスを追加します。

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. QnA Maker API に対して POST 要求を行う次のメソッドを追加します。 `Ocp-Apim-Subscription-Key` は、認証に使用される QnA Maker サービス キーです。

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. QnA Maker API に対して GET 要求を行う次のメソッドを追加します。

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>KB を作成するためのメソッドを追加する
Post メソッドを呼び出すことによって KB を作成する次のメソッドを追加します。

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

この API 呼び出しは、操作 ID を含んだ JSON 応答を返します。 この操作 ID を使用して、KB が正常に作成されたかどうかを判断します。

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>状態を取得するためのメソッドを追加する
作成状態を確認するための次のメソッドを追加します。

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

成功または失敗が返されるまで、この呼び出しを繰り返します。

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>main メソッドを追加する
main メソッドで KB を作成した後、状態をポーリングします。 POST 応答のヘッダー フィールド **Location** に操作 ID が返された後、GET 要求でルートの一部として使用されます。 完了していない場合は、`while` ループによって状態が再試行されます。

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>プログラムをコンパイルして実行する

1. gson ライブラリが `./libs` ディレクトリに存在することを確認します。 コマンド ラインで、`CreateKB.java` ファイルをコンパイルします。

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. コマンド ラインで次のコマンドを入力して、プログラムを実行します。 QnA Maker API に要求が送信され、KB が作成された後、30 秒おきに結果がポーリングされます。 それぞれの応答は、コンソール ウィンドウに出力されます。

    ```base
    java -cp ",;libs/*" CreateKB
    ```

作成されたナレッジ ベースは、QnA Maker ポータルの [[My knowledge bases]\(マイ ナレッジ ベース\)](https://www.qnamaker.ai/Home/MyServices) ページで確認できます。

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)
