---
title: クイック スタート:ナレッジ ベースを作成する - REST (Python) - QnA Maker
titlesuffix: Azure Cognitive Services
description: この Python REST ベースのクイック スタートでは、Cognitive Services API アカウントの Azure ダッシュボードに表示される QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 27471f07438bac7f40e6047306aac7d2658ee519
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249674"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>クイック スタート:Python を使用して QnA Maker のナレッジ ベースを作成する

このクイック スタートでは、QnA Maker ナレッジ ベースのサンプルをプログラムから作成して発行する手順を紹介しています。 QnA Maker は、[データ ソース](../Concepts/data-sources-supported.md)の FAQ などの半構造化コンテンツから質問とその回答を自動的に抽出します。 ナレッジ ベースのモデルは、API 要求の本文で送信される JSON で定義されます。 

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [KB の作成](https://go.microsoft.com/fwlink/?linkid=2092179)
* [取得操作の詳細](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>前提条件

* [Python 3.7](https://www.python.org/downloads/)
* QnA Maker サービスが必要です。 キーを取得するには、ダッシュボードで [リソース管理] の [キー] を選択します。

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="create-a-knowledge-base-python-file"></a>ナレッジ ベースの Python ファイルを作成する

`create-new-knowledge-base-3x.py` という名前でファイルを作成します。

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

`create-new-knowledge-base-3x.py` の先頭に次の行を追加して、プロジェクトに必要な依存関係を追加します。

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>必要な定数を追加する
上記の必要な依存関係の後に、QnA Maker にアクセスするために必要な定数を追加します。 `subscriptionKey` 変数の値を自分の QnA Maker キーに置き換えます。

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=5-13 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>KB モデル定義を追加する

定数の後に、次の KB モデル定義を追加します。 モデルは、定義の後に文字列に変換されます。

[!code-python[Add the KB model definition](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=15-41 "Add the KB model definition")]

## <a name="add-supporting-function"></a>補助的な関数を追加する

JSON を読みやすい形式で出力するために、次の関数を追加します。

[!code-python[Add supporting function](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=43-45 "Add supporting function")]

## <a name="add-function-to-create-kb"></a>KB を作成するための関数を追加する

ナレッジ ベースを作成するための HTTP POST 要求を行う次の関数を追加します。 この API 呼び出しは、ヘッダー フィールド **Location** に操作 ID を含んだ JSON 応答を返します。 この操作 ID を使用して、KB が正常に作成されたかどうかを判断します。 `Ocp-Apim-Subscription-Key` は、認証に使用される QnA Maker サービス キーです。 

[!code-python[Add function to create KB](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=48-59 "Add function to create KB")]

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

## <a name="add-function-to-check-creation-status"></a>作成状態を確認するための関数を追加する

次の関数は、URL ルートの最後に操作 ID を追加して送信することにより、作成状態を確認します。 `check_status` 呼び出しは、main の _while_ ループ内に存在します。

[!code-python[Add function to check creation status](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=61-67 "Add function to check creation status")]

この API 呼び出しは、操作の状態を含んだ JSON 応答を返します。 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-main-code-block"></a>main コード ブロックを追加する
次のループは、作成操作が完了するまで、その操作状態を定期的にポーリングします。 

[!code-python[Add main code block](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=70-96 "Add main code block")]

## <a name="build-and-run-the-program"></a>プログラムをビルドして実行する

コマンド ラインで次のコマンドを入力して、プログラムを実行します。 QnA Maker API に要求が送信され、KB が作成された後、30 秒おきに結果がポーリングされます。 それぞれの応答は、コンソール ウィンドウに出力されます。

```bash
python create-new-knowledge-base-3x.py
```

作成されたナレッジ ベースは、QnA Maker ポータルの [[My knowledge bases]\(マイ ナレッジ ベース\)](https://www.qnamaker.ai/Home/MyServices) ページで確認できます。 ナレッジ ベースの名前 (QnA Maker FAQ など) を選択すると表示されます。

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)
