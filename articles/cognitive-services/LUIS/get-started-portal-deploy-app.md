---
title: クイック スタート:LUIS ポータルを使用してアプリをデプロイする
description: このクイックスタートでは、予測エンドポイント リソースを作成し、リソースを割り当て、アプリをトレーニング、公開することによって、アプリをデプロイする方法について説明します。
ms.topic: quickstart
ms.date: 05/06/2020
ms.openlocfilehash: 3d12fa2956f962fe7a05b9639c1d32a7069635f9
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87336372"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>クイック スタート:LUIS ポータル内でアプリをデプロイする

LUIS アプリがクライアント アプリケーション (チャット ボットなど) に発話予測を返す準備ができたら、アプリを予測エンドポイントにデプロイする必要があります。

このクイックスタートでは、アプリケーションをデプロイする方法を学習します。 予測エンドポイント リソースを作成し、そのリソースをアプリに割り当て、アプリをトレーニングし、アプリを公開します。

## <a name="prerequisites"></a>前提条件

* [Azure サブスクリプション](https://azure.microsoft.com/free)を取得します。
* [前のポータルのクイック スタート](get-started-portal-build-app.md)を完了するか、[アプリをダウンロードして、インポート](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/portal-build-app.json)します。
* Azure リソース認証より前のアプリがある場合は、[Azure リソースに移行します](luis-migration-authoring.md)。 一部のポータル ページは、メール認証が有効になっている場合に表示が異なることがあります。

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>LUIS ポータル内で LUIS アプリにリソース キーを割り当てる

LUIS 向けの新しい作成リソースまたはクエリ予測リソースを作成するたびに、LUIS アプリにそのリソースを割り当てる必要があります。 割り当てた後は、新しいリソースを作成しない限り、この手順をもう一度実行する必要はありません。 ご自分のアプリのリージョンを拡張したり、より多くの予測クエリをサポートしたりする場合に、新しいリソースを作成します。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、アプリの一覧から **myEnglishApp** アプリを選択します。

1. 右上のメニューの **[管理]** を選択し、 **[Azure リソース]** を選択します。

1. LUIS を追加するには、 **[Add prediction resource]\(予測リソースの追加)** を選択します。

    ![LUIS 予測リソースを追加するには、[Add prediction resource]\(予測リソースの追加\) を選択します。](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. ご自分のテナント、サブスクリプション、リソースの名前を選択します。 **[Assign resource]\(リソースの割り当て)** を選択します。

   > [!div class="mx-imgBorder"]
   > ![自分のアプリにリソースを割り当てる](./media/get-started-portal-deploy-app/assign-resource.png)

1. 同じ手順を実行して、オーサリング キーをアプリに追加します。

1. 新しい予測リソースのテーブルから新しい行を見つけて、エンドポイントの URL をコピーします。 予測のために LUIS API エンドポイントに `HTTP GET` 要求を行うことができるように正しく構築されています。

> [!TIP]
> アクティブ ラーニングを使用して LUIS アプリを改善する場合は、 **[Change query parameters]\(クエリ パラメーターの変更\)** を選択し、 **[Save logs]\(ログの保存\)** を選択します。 このアクションでは、`log=true` querystring パラメーターを追加することにより、サンプル URL を変更します。 ランタイム エンドポイントに対する予測クエリを実行するときに、変更したサンプル クエリ URL をコピーして使用します。

## <a name="train-the-app"></a>アプリをトレーニングする

前回のトレーニング以降、アプリを変更した場合、アプリを[トレーニング](get-started-portal-build-app.md)します。

## <a name="publish-the-app-to-the-prediction-endpoint"></a>予測エンドポイントにアプリを公開する

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>予測エンドポイントの要求

ポータルでは、URL の末尾の `query=` は、GET 要求に対してユーザーの発話を追加する場所です。 `query=` の後に、前のクイック スタートの最後に使用したのと同じユーザーの発話を入力します。

```Is there a form named hrf-234098```

クエリ文字列に次のペアが含まれていることを確認してください。

* `show-all-intents=true`
* `verbose=true`

ブラウザーには、次の応答が表示されます。

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

テスト ウィンドウにこれと同じレベルの情報を表示する場合は、アプリを公開する必要があります。 アプリが公開されたら、テスト ウィンドウで、 **[Compare with published]\(公開済みのものと比較\)** を選択します。 公開済みのテスト ウィンドウの **[Show JSON view]\(JSON ビューの表示\)** を使用して、前の手順と同じ JSON を表示します。 このようにして、現在作業中のアプリに対する変更と、エンドポイントに公開されているアプリとを比較できます。

[![アプリの現在編集中のバージョンと公開済みのバージョンを比較する](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートが完了したら、上部のナビゲーション メニューから **[マイ アプリ]** を選択します。 一覧からアプリのチェック ボックスをオンにし、一覧の上にあるコンテキスト ツール バーから **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [一般的な意図とエンティティを識別する](luis-tutorial-prebuilt-intents-entities.md)
