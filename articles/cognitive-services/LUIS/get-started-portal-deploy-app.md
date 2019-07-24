---
title: クイック スタート:LUIS ポータルを使用してアプリをデプロイする
titleSuffix: Language Understanding - Azure Cognitive Services
description: アプリがチャット ボットなどのクライアント アプリケーションに発話予測を返す準備ができたら、予測エンドポイントにアプリをデプロイする必要があります。 このクイック スタートでは、アプリケーションのデプロイ方法を学習するために、予測エンドポイント リソースを作成し、そのリソースをアプリに割り当て、アプリをトレーニングし、アプリを公開します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cf7464188bb502d77c673284cba226ce91a5725
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259722"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>クイック スタート:LUIS ポータル内でアプリをデプロイする

アプリがチャット ボットなどのクライアント アプリケーションに発話予測を返す準備ができたら、予測エンドポイントにアプリをデプロイする必要があります。 

このクイック スタートでは、アプリケーションのデプロイ方法を学習するために、予測エンドポイント リソースを作成し、そのリソースをアプリに割り当て、アプリをトレーニングし、アプリを公開します。 

## <a name="prerequisites"></a>前提条件

* [Azure サブスクリプション](https://azure.microsoft.com/free)。
* [前のポータルのクイック スタート](get-started-portal-build-app.md)を完了するか、[アプリをダウンロードして、インポート](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json)します。 


## <a name="create-endpoint-resource"></a>エンドポイント リソースを作成する

予測エンドポイント リソースは、Azure portal 内で作成します。 このリソースは、エンドポイントの予測クエリでのみ使用します。 このアプリに変更を加えるために、このリソースを使用しないでください。 

1. **[Azure Portal](https://ms.portal.azure.com/)** にサインインします。 

1. 左上のパネルにある緑色の **+** 記号をクリックし、マーケットプレースで `Cognitive Services` を検索します。 

1. 次の設定を使用して、サブスクリプションを構成します。

    |Setting|値|目的|
    |--|--|--|
    |Name|`my-cognitive-service-resource`|Azure リソースの名前。 LUIS ポータル内でアプリにリソースを割り当てるときに、この名前が必要です。|
    |サブスクリプション|該当するサブスクリプション|ご自分のアカウントに関連付けられているサブスクリプションの 1 つを選択します。|
    |Location|**[米国西部]**|このリソースの Azure リージョン。|
    |価格レベル |**S0**|このリソースの既定の価格レベル。|
    |リソース グループ|`my-cognitive-service-resource-group`|お使いのすべての Cognitive Services リソース向けに新しいリソース グループを作成します。 リソースの作業が完了したら、リソース グループを削除して、サブスクリプションをクリーンアップできます。 | 

    ![Azure API の選択](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png) 

1. **[作成]** を選択して、Azure リソースを作成します。 

    次のセクションでは、LUIS ポータル内で LUIS アプリにこの新しいリソースを接続する方法を学習します。 

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>LUIS ポータル内で LUIS アプリにリソース キーを割り当てる

LUIS 向けの新しいリソースを作成するたびに、LUIS アプリにそのリソースを割り当てる必要があります。 割り当てた後は、新しいリソースを作成しない限り、この手順をもう一度実行する必要はありません。 ご自分のアプリのリージョンを拡張したり、より多くの予測クエリをサポートしたりする場合に、新しいリソースを作成します。 

1. [LUIS ポータル](https://www.luis.ai)にサインインし、アプリの一覧から **myEnglishApp** アプリを選択します。

1. 右上のメニューの **[管理]** を選択し、**[Keys and endpoints]\(キーとエンドポイント\)** を選択します。

1. LUIS を追加するには、**[Assign Resource +]\(リソースの割り当て +)** を選択します。

    [![ご自分のアプリにリソースを割り当てる](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. ご自分のテナント、サブスクリプション、リソースの名前を選択し、**[Assign resource]\(リソースの割り当て\)** を選択します。 

    ![アプリにリソースを割り当てる](./media/get-started-portal-deploy-app/assign-resource.png)

1. テーブルで新しい行を見つけて、そのエンドポイント URL をコピーします。 予測のために LUIS API エンドポイントに対して HTTP GET 要求を行えれば、正しく構築されています。 

## <a name="train-and-publish-the-app"></a>アプリをトレーニングして公開する 

テストする準備ができたら、トレーニングする必要があります。 予測エンドポイントのランタイムから現在トレーニング済みのバージョンをクライアント アプリケーションで使用できるようにする場合は、アプリを公開する必要があります。 

1. アプリがトレーニングされていない場合は、右上のメニューから **[Train]\(トレーニング\)** を選択します。

1. 右上のメニューから **[Publish]\(公開\)** を選択します。 既定の環境設定をそのまま使用し、**[Publish]\(公開\)** を選択します。

1. ブラウザー ウィンドウの上部に緑の成功通知バーが表示されたら、**[Refer to the list of endpoints]\(エンドポイントの一覧を参照する\)** リンクを選択します。 

    ![ブラウザー上の正常に公開されたアプリの通知バー](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. **[Keys and Endpoint settings]\(キーとエンドポイントの設定\)** ページが表示されます。 割り当てられたリソースと、対応するエンドポイントの URL の一覧がページの下部にあります。 

1. ご自分の新しいリソースの名前に関連付けられているエンドポイントの URL を選択します。 Web ブラウザーが開き、予測エンドポイントのランタイムに対して **GET** 要求を行うための、正しく構築された URL が入力されます。 

1. URL の末尾の `q=` は、**query** (クエリ) の省略形で、ここで GET 要求に対してユーザーの発話を追加します。 `q=` の後に、前のクイック スタートの最後に使用したのと同じユーザーの発話を入力します。

    ```Is there a form named hrf-234098```

    ブラウザーの応答は、ご自分のクライアント アプリケーションが受け取るのと同じ JSON です。

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

    この応答からは、前のチュートリアルの既定の **[Test]\(テスト\)** ウィンドウよりも多くの情報が得られます。 [Test]\(テスト\) ウィンドウでこれと同じレベルの情報を表示する場合は、アプリを公開する必要があります。 その後、[Test]\(テスト\) ウィンドウで、**[Compare with published]\(公開済みのものと比較\)** を選択します。 公開済みの [Test]\(テスト\) ウィンドウの **[Show JSON view]\(JSON ビューの表示\)** を使用して、前の手順と同じ JSON を表示します。 これにより、作業している現在のアプリと、エンドポイントに公開されているアプリを比較できます。

    [![アプリの現在編集中のバージョンと公開済みのバージョンを比較する](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)


## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイック スタートが完了したら、上部のナビゲーション メニューから **[My apps]\(マイ アプリ\)** を選択します。 次に、一覧からアプリの左側にあるチェック ボックスをオンにし、一覧の上にあるコンテキスト ツール バーから **[Delete]\(削除\)** を選択します。 

[![[My apps]\(マイ アプリ\) の一覧からアプリを削除する](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [一般的な意図とエンティティを識別する](luis-tutorial-prebuilt-intents-entities.md)