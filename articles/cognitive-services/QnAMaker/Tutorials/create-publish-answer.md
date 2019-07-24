---
title: 作成、発行、回答
titleSuffix: QnA Maker - Azure Cognitive Services
description: この REST ベースのチュートリアルでは、プログラムでナレッジ ベースを作成して発行した後、そのナレッジ ベースから質問に回答する手順について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: d209d73d67af96e99589dddcb71b6b50214356ee
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877280"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>チュートリアル: C# を使用してナレッジ ベースを作成し、質問に回答する

このチュートリアルでは、プログラムでナレッジ ベース (KB) を作成、発行した後、そのナレッジ ベースを使用してユーザーの質問に回答する手順について説明します。 

> [!div class="checklist"]
> * ナレッジ ベースの作成 
> * 作成の状態を確認する
> * ナレッジ ベースをトレーニングして発行する
> * エンドポイント情報を取得する
> * Curl を使用してナレッジ ベースを照会する


このクイック スタートで呼び出す QnA Maker API は次のとおりです。

* [ナレッジ ベース (KB) を作成する](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [取得操作の詳細](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [ナレッジ ベースの詳細を取得する](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) 
* [ナレッジ ベースのエンドポイントを取得する](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys)
* [[発行]](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>前提条件

* 最新の [**Visual Studio Community エディション**](https://www.visualstudio.com/downloads/)。
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーを取得するには、ダッシュボードで **[リソース管理]** の **[キー]** を選択します。 

> [!NOTE] 
> 完全なソリューション ファイルは、[**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base)から入手できます。

## <a name="create-a-knowledge-base-project"></a>ナレッジ ベース プロジェクトを作成する

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

Program.cs の先頭にある 1つの _using_ ステートメントを次の行に置き換えて、プロジェクトに必要な依存関係を追加します。

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>KBDetails クラスを追加する
次の KBDetails クラスを名前空間の角かっこ内に追加します。 このクラスにより、NewtonSoft ライブラリが JSON 応答を C# のクラスに逆シリアル化できるようになります。

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>必要な定数を追加する

Program クラスの先頭に、QnA Maker にアクセスするための次の定数を追加します。

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>KB 定義を追加する

定数の後に、次の KB モデル定義を追加します。

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>補助的な関数と構造体を追加する
Program クラス内に次のコード ブロックを追加します。

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>KB を作成するための POST 要求を追加する

次のコードは、KB を作成するための HTTPS 要求を QnA Maker API に送信し、その応答を受信するものです。

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

この API 呼び出しは、操作 ID を含んだ JSON 応答を返します。 後で、このプログラムではこの操作 ID を使用して、KB が正常に作成されたかどうかを判断します。 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>作成状態を確認するための GET 要求を追加する

作成操作の状態を確認します。

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

この API 呼び出しは、操作の状態を含んだ JSON 応答を返します。 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

成功または失敗が返されるまで、この呼び出しを繰り返します。 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>CreateKB メソッドを追加する

次のメソッドでは、KB を作成して状態を確認するための呼び出しをカプセル化します。  _create_ という **操作 ID** は、POST 応答のヘッダー フィールド **Location** で返された後、GET 要求でルートの一部として使用されます。 KB の作成には多少時間がかかる場合があるため、成功と失敗のどちらかの状態になるまで、呼び出しを繰り返して状態をチェックする必要があります。 操作が成功すると、KB ID が **resourceLocation** で返されます。 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>publish メソッドを追加する

ナレッジ ベースの作成に成功したら、その KB を発行します。 トレーニング API の呼び出しが必要になる場合があります。 このバージョンではそれは必要ありません。 

次のコードは、KB を公開するための HTTPS 要求を QnA Maker API に送信し、その応答を受信するものです。

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

正常に公開されると、この API 呼び出しから状態 204 が返されます。応答の本文にはコンテンツが含まれません。 このクイック スタートのコードでは、結果が表示されるように 204 の応答のテキストが追加されます。

それ以外の応答の場合は、その応答がそのまま返されます。

## <a name="generating-an-answer"></a>回答を生成する
KB にアクセスして質問を送信し、最適な回答を受け取るために、プログラムには、KB の詳細 API の "_エンドポイント ホスト_" とエンドポイント API の "_プライマリ エンドポイント キー_"が必要です。 これらのメソッドは、回答を生成するメソッドと合わせて次のセクションに含まれています。 

次の表は、データを使用して URI を構築する方法を示しています。

|回答の URI テンプレートの生成|
|--|
|https://**HOSTNAME**.azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

"_プライマリ エンドポイント_" は、回答を生成するための要求を認証するためにヘッダーとして渡されます。

|ヘッダー名|ヘッダー値|
|--|--|
|Authorization|`Endpoint` + **プライマリ エンドポイント**<br>例: `Endpoint xxxxxxx`<br>`Endpoint` というテキストとプライマリ エンドポイントの値の間のスペースに注意してください。 

要求の本文では、適切な JSON を渡す必要があります。

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>KB の詳細を取得する
KB の詳細を取得するために次のメソッドを追加します。 これらの詳細には、KB のホスト名が含まれます。 ホスト名は、QnA Maker のリソースの作成時に入力した QnA Maker Azure Web サービスの名前です。 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

この API 呼び出しにより、JSON 応答が返されます。 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>KB のエンドポイントを取得する
QnA Maker のプライマリ エンドポイントを取得するために次のメソッドを追加します。 これらのエンドポイントは KB に関連付けられておらず、Azure portal の QnA Maker のリソース キーに関連付けられたすべての KB に対して有効です。  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

この API 呼び出しにより、JSON 応答が返されます。 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>回答を取得する
ユーザーの質問に対する回答を取得するために次のメソッドを追加します。 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

この API 呼び出しにより、JSON 応答が返されます。 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Main メソッド
Main メソッドでは、作成、発行、回答の生成を実行する同期呼び出しが示されています。 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>プログラムをビルドして実行する

プログラムをビルドして実行します。 

作成されたナレッジ ベースは、QnA Maker ポータルの [[My knowledge bases]\(マイ ナレッジ ベース\)](https://www.qnamaker.ai/Home/MyServices) ページで確認できます。 回答の生成 API を使用する方法がわかったら、任意の言語または HTTP 要求フレームワークでその API を使用できます。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
