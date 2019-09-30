---
title: クイック スタート:QnA Maker ポータルで質問と回答を追加する
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: diberry
ms.openlocfilehash: dc4548e0c07adc485d1bb5785179aeb7ea2f3fe1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196382"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>クイック スタート:QnA Maker ポータルで質問と回答を追加する

ナレッジ ベースを作成したら、ユーザーが自分の質問に対する適切な回答を見つけられるように、質問と回答のセットをメタデータと共に追加します。

適切な回答は 1 つですが、その回答に結びつく質問はいくつも考えられます。

たとえば、次の表には Azure サービスの制限についての質問が記載されていますが、関係する Azure サービスはそれぞれ異なります。 

<a name="qna-table"></a>


|Set|疑問がある場合|Answer|Metadata|
|--|--|--|--|
|1 番|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2 番|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Search tier supports.`|`service=search`<br>`link_in_answer=false`|

質問と回答のセットにメタデータが追加されると、クライアント アプリケーションでは次のことを実行できます。

* 特定のメタデータとのみ一致する回答を要求します。
* すべての回答を受け取ったうえで、それぞれのメタデータに応じた後処理を回答に対して行います。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="prerequisites"></a>前提条件

* QnA Maker サービス
* その QnA Maker サービスで作成されたナレッジ ベース

両方とも[最初のクイックスタート](../how-to/create-knowledge-base.md)で作成しました。

## <a name="sign-in-to-the-qna-maker-portal"></a>QnA Maker ポータルにサインインする

1. [QnA Maker](https://www.qnamaker.ai) ポータルにサインインします。

1. 既存のナレッジ ベースを選択します。 まだナレッジ ベースがない場合は、[前のクイックスタート](../how-to/create-knowledge-base.md)に戻って、ナレッジ ベースの作成手順を完了してください。

## <a name="add-additional-alternatively-phrased-questions"></a>言い回しを変えて質問を追加する 

[前のクイックスタート](../how-to/create-knowledge-base.md)で作成した最新のナレッジ ベースには、QnA Maker のトラブルシューティングに関する質問と回答のセットが含まれています。 これらのセットは、作成プロセスでナレッジ ベースに URL を追加したときに作成されました。 

この URL をインポートした時点では、1 つの回答について質問が 1 つしか作成されていませんでした。 

この手順では、別の質問を追加します。

1. **[編集]** ページで、質問と回答のセットの上にある検索ボックスを使用し、`How large a knowledge base can I create?` という質問を探します。

1. **[質問]** 列で、 **[+ Add alternative phrasing]\(+ 代わりの言い回しの追加\)** を選択し、次の表に示した新しい言い回しをそれぞれ追加します。

    |代わりの言い回し|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. **[Save and train]\(保存してトレーニング\)** を選択してナレッジ ベースを再トレーニングします。 

1. **[テスト]** を選択し、新しく追加した、代わりの言い回しのいずれかに近い質問を入力します。ただし、まったく同じ言い回しは使用しません。

    `What GB size can a knowledge base be?`

    次のようにマークダウン形式で正しい回答が返されます: `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    返された回答の下にある **[検査]** を選択すると、異なる信頼度で質問を満たした回答が他にも表示されます。 

    代わりの言い回しとして考えられる組み合わせをすべて追加しようとしないでください。 QnA Maker の[アクティブ ラーニング](../how-to/improve-knowledge-base.md)を有効にすれば、ナレッジ ベースでユーザーのニーズを最もよく満たす代わりの言い回しが検出されます。

1. もう一度 **[テスト]** を選択して、テスト ウィンドウを閉じます。

## <a name="add-metadata-to-filter-the-answers"></a>回答をフィルター処理するためのメタデータを追加する

質問と回答のセットにメタデータを追加すると、フィルター処理済みの回答をクライアント アプリケーションが要求できるようになります。 このフィルターは、[1 つ目と 2 つ目のランカー](../concepts/knowledge-base.md#ranker-process)が適用される前に適用されます。

1. [このクイックスタートの最初の表](#qna-table)にある 2つ目の質問と回答のセットをメタデータなしで追加したうえで、次の手順を続行します。 

1. **[オプションの表示]** を選択し、 **[メタデータの表示]** を選択します。 

1. 追加した質問と回答のセットについて、 **[Add metadata tags]\(メタデータ タグの追加\)** を選択し、`service` という名前と `search` という値を追加します (`service:search`)。

1. `link_in_answer` という名前と `false` という値で別のメタデータ タグを追加します (`link_in_answer:false`)。

1. 表から 1 つ目の回答を検索します (`How large a knowledge base can I create?`)。 
1. 同じ 2 つのメタデータ タグに対し、メタデータのペアを追加します。

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    同じメタデータ タグで値が異なる 2 つの質問が完成しました。 

1. **[Save and train]\(保存してトレーニング\)** を選択してナレッジ ベースを再トレーニングします。 

1. トップ メニューの **[公開]** をクリックして公開ページに移動します。 
1. **[公開]** ボタンを選択して、クエリ可能なエンドポイントに最新のナレッジ ベースを公開します。 
1. ナレッジ ベースが公開された後、 **[Curl]** タブを選択すると、ナレッジ ベースから回答を生成するためのサンプル cURL コマンドが表示されます。
1. このコマンドを編集できるように、メモ帳などの編集環境にコピーします。 実際のリソース名、ナレッジ ベース ID、エンドポイント キーに合わせて編集します。

    |Replace|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    質問はたったの 1 単語 (`size`) であることに注目してください。この単語から、質問と回答のセットを取得することができます。 `strictFilters` 配列の指定により、`qna_maker` の回答のみに応答が限定されます。 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. この応答には、フィルター条件を満たした回答のみが含まれます。 

    次の cURL 応答は、読みやすくするために書式設定されています。

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    検索語句は満たしていないもののフィルターの条件は満たしているような質問と回答のセットがあったとしても、それは返されません。 代わりに一般的な回答 `No good match found in KB.` が返されます。

    メタデータの名前と値のペアは、所定の制限内に保つようにしてください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Postman を使用して回答を取得する](get-answer-from-kb-using-postman.md)