---
title: クイック スタート:ナレッジ ベースを作成する - REST (Go) - QnA Maker
titlesuffix: Azure Cognitive Services
description: この Go REST ベースのクイック スタートでは、Cognitive Services API アカウントの Azure ダッシュボードに表示される QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: b4a399901b8bba1c97a5571d3db4ef1cbe713a6c
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254192"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>クイック スタート:Go を使用して QnA Maker のナレッジ ベースを作成する

このクイック スタートでは、QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。 QnA Maker は、[データ ソース](../Concepts/data-sources-supported.md)の FAQ などの半構造化コンテンツから質問とその回答を自動的に抽出します。 ナレッジ ベースのモデルは、API 要求の本文で送信される JSON で定義されます。 

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [KB の作成](https://go.microsoft.com/fwlink/?linkid=2092179)
* [取得操作の詳細](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>前提条件

* [Go 1.10.1](https://golang.org/dl/)
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーを取得するには、ダッシュボードで **[リソース管理]** の **[キー]** を選択します。 

## <a name="create-a-knowledge-base-go-file"></a>ナレッジ ベースの Go ファイルを作成する

`create-new-knowledge-base.go` という名前でファイルを作成します。

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

`create-new-knowledge-base.go` の先頭に次の行を追加して、プロジェクトに必要な依存関係を追加します。

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>必要な定数を追加する
上記の必要な依存関係の後に、QnA Maker にアクセスするために必要な定数を追加します。 `subscriptionKey` 変数の値を自分の QnA Maker キーに置き換えます。

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>KB モデル定義を追加する
定数の後に、次の KB モデル定義を追加します。 モデルは、定義の後に文字列に変換されます。

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>補助的な構造体と関数を追加する

次に、以下の補助的な関数を追加します。

1. HTTP 要求の構造体を追加します。

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. QnA Maker API への POST を処理する次のメソッドを追加します。 このクイック スタートでは、POST を使用して KB の定義を QnA Maker に送信します。

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. QnA Maker API への GET を処理する次のメソッドを追加します。 このクイック スタートでは、GET を使用して作成操作の状態を確認します。 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>KB を作成するための関数を追加する

次の関数を追加して、ナレッジ ベースを作成するための HTTP POST 要求を作成します。 _create_ という **操作 ID** は、POST 応答のヘッダー フィールド **Location** で返された後、GET 要求でルートの一部として使用されます。 `Ocp-Apim-Subscription-Key` は、認証に使用される QnA Maker サービス キーです。 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

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

## <a name="add-function-to-get-status"></a>状態を取得するための関数を追加する

次の関数を追加して、操作の状態をチェックするための HTTP GET 要求を作成します。 `Ocp-Apim-Subscription-Key` は、認証に使用される QnA Maker サービス キーです。 

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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
## <a name="add-main-function"></a>main 関数を追加する

次の関数は、KB を作成して、状態のチェックを繰り返し実行する main 関数です。 KB の作成には多少時間がかかる場合があるため、成功と失敗のどちらかの状態になるまで、呼び出しを繰り返して状態をチェックする必要があります。

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>プログラムをコンパイルする
以下のコマンドを入力してファイルをコンパイルします。 このコマンド プロンプトから、ビルドの成功に関する情報は一切返されません。

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>プログラムの実行

コマンド ラインで次のコマンドを入力して、プログラムを実行します。 QnA Maker API に要求が送信され、KB が作成された後、30 秒おきに結果がポーリングされます。 それぞれの応答は、コンソール ウィンドウに出力されます。

```bash
go run create-new-knowledge-base
```

作成されたナレッジ ベースは、QnA Maker ポータルの [[My knowledge bases]\(マイ ナレッジ ベース\)](https://www.qnamaker.ai/Home/MyServices) ページで確認できます。 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)
