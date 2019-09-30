---
title: クイック スタート:LUIS ポータルを使用してアプリをデプロイする
titleSuffix: Azure Cognitive Services
description: LUIS アプリがチャット ボットなどのクライアント アプリケーションに発話予測を返す準備ができた後にアプリを予測エンドポイントにデプロイする方法を学習します。 このクイックスタートでは、アプリケーションのデプロイ方法を学習するために、予測エンドポイント リソースを作成し、そのリソースをアプリに割り当て、アプリをトレーニングし、アプリを公開します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 4e9ecdad0fb4d02f160977fa28a484b2a3a5bb30
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257073"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>クイック スタート:LUIS ポータル内でアプリをデプロイする

LUIS アプリがクライアント アプリケーション (チャット ボットなど) に発話予測を返す準備ができたら、アプリを予測エンドポイントにデプロイする必要があります。

このクイックスタートでは、アプリケーションをデプロイする方法を学習します。 予測エンドポイント リソースを作成し、そのリソースをアプリに割り当て、アプリをトレーニングし、アプリを公開します。

## <a name="prerequisites"></a>前提条件

* [Azure サブスクリプション](https://azure.microsoft.com/free)を取得します。
* [前のポータルのクイック スタート](get-started-portal-build-app.md)を完了するか、[アプリをダウンロードして、インポート](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json)します。

## <a name="create-the-endpoint-resource"></a>エンドポイント リソースを作成する

予測エンドポイント リソースは、Azure portal 内で作成します。 このリソースは、エンドポイントの予測クエリでのみ使用します。 このアプリに変更を加えるために、このリソースを使用しないでください。

1. [Azure Portal](https://ms.portal.azure.com/) にサインインします。

1. 左上のパネルにある緑色の **[+]** 記号を選択します。 マーケットプレースで `Cognitive Services` を検索して選択します。

1. 次の設定を使用して、サブスクリプションを構成します。

   |Setting|値|目的|
   |--|--|--|
   |Name|`my-cognitive-service-resource`|Azure リソースの名前。 LUIS ポータル内でアプリにリソースを割り当てるときに、この名前が必要です。|
   |Subscription|該当するサブスクリプション|ご自分のアカウントに関連付けられているサブスクリプションの 1 つを選択します。|
   |Location|**[米国西部]**|このリソースの Azure リージョン。|
   |Pricing tier|**S0**|このリソースの既定の価格レベル。|
   |Resource group|`my-cognitive-service-resource-group`|お使いのすべての Cognitive Services リソース向けに新しいリソース グループを作成します。 リソースの作業が完了したら、リソース グループを削除して、サブスクリプションをクリーンアップできます。 |
   | | | |

   ![Azure API の選択](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. **[作成]** を選択して、Azure リソースを作成します。

   次のセクションでは、LUIS ポータル内で LUIS アプリにこの新しいリソースを接続する方法を学習します。

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>LUIS ポータル内で LUIS アプリにリソース キーを割り当てる

LUIS 向けの新しいリソースを作成するたびに、LUIS アプリにそのリソースを割り当てる必要があります。 割り当てた後は、新しいリソースを作成しない限り、この手順をもう一度実行する必要はありません。 ご自分のアプリのリージョンを拡張したり、より多くの予測クエリをサポートしたりする場合に、新しいリソースを作成します。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、アプリの一覧から **myEnglishApp** アプリを選択します。

1. 右上のメニューの **[管理]** を選択し、 **[Azure リソース]** を選択します。

1. LUIS を追加するには、 **[Add prediction resource]\(予測リソースの追加)** を選択します。

    <!-- TBD: get screenshot-->

1. ご自分のテナント、サブスクリプション、リソースの名前を選択します。 **[Assign resource]\(リソースの割り当て)** を選択します。

   ![アプリにリソースを割り当てる](./media/get-started-portal-deploy-app/assign-resource.png)

1. テーブルで新しい行を見つけて、そのエンドポイント URL をコピーします。 予測のために LUIS API エンドポイントに `HTTP GET` 要求を行うことができるように正しく構築されています。

## <a name="train-and-publish-the-app"></a>アプリをトレーニングして公開する

アプリをテストする準備ができたら、アプリをトレーニングします。 クエリ予測エンドポイントのランタイムから現在トレーニング済みのバージョンをクライアント アプリケーションで使用できるようにする場合は、アプリを公開します。

1. アプリがトレーニングされていない場合は、右上のメニューから **[Train]\(トレーニング\)** を選択します。

1. 上部のメニューから **[Publish]\(公開\)** を選択します。 既定の環境設定をそのまま使用し、 **[Publish]\(公開\)** を選択します。

1. ブラウザー ウィンドウの上部に緑の成功通知バーが表示されたら、 **[Refer to the list of endpoints]\(エンドポイントの一覧を参照する\)** を選択します。

   ![ブラウザー上の正常に公開されたアプリの通知バー](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. **[Keys and Endpoint settings]\(キーとエンドポイントの設定\)** ページの下部で、割り当てられたリソースと対応するエンドポイントの URL の一覧を見つけます。

1. ご自分の新しいリソースの名前に関連付けられているエンドポイントの URL を選択します。 このアクションにより、Web ブラウザーが開き、予測エンドポイントのランタイムに対して `GET` 要求を行うための、正しく構築された URL が入力されます。

1. URL の末尾の `q=` は、**query** (クエリ) の省略形で、ここで GET 要求に対してユーザーの発話を追加します。 `q=` の後に、前のクイック スタートの最後に使用したのと同じユーザーの発話を入力します。

    ```Is there a form named hrf-234098```

    ブラウザーに応答が表示されます。これは、クライアント アプリケーションが受け取るのと同じ JSON です。

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    この応答からは、前のチュートリアルの既定のテスト ウィンドウよりも多くの情報が得られます。 テスト ウィンドウにこれと同じレベルの情報を表示する場合は、アプリを公開する必要があります。 アプリが公開されたら、テスト ウィンドウで、 **[Compare with published]\(公開済みのものと比較\)** を選択します。 公開済みのテスト ウィンドウの **[Show JSON view]\(JSON ビューの表示\)** を使用して、前の手順と同じ JSON を表示します。 このようにして、現在作業しているアプリと、エンドポイントに公開されているアプリを比較できます。

    [![アプリの現在編集中のバージョンと公開済みのバージョンを比較する](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートが完了したら、上部のナビゲーション メニューから **[マイ アプリ]** を選択します。 一覧からアプリのチェック ボックスをオンにし、一覧の上にあるコンテキスト ツール バーから **[削除]** を選択します。

[![[マイ アプリ] 一覧からアプリを削除する](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [一般的な意図とエンティティを識別する](luis-tutorial-prebuilt-intents-entities.md)
