---
title: チュートリアル - API Management で API の応答の模擬テストを実行する - Azure portal  | Microsoft Docs
description: このチュートリアルでは、実際の応答を送信するバックエンドが利用できない場合にモック応答が返されるよう、API Management を使用して API のポリシーを設定します。
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 75727d139242e1b537505d2ed907ae20fc5479f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100547266"
---
# <a name="tutorial-mock-api-responses"></a>チュートリアル:API の応答の模擬テストを実行する

バックエンド API は API Management (APIM) API にインポートするか、手動で作成して管理できます。 このチュートリアルの手順では、APIM を使用して手動で空の API を作成し、管理する方法のほか、モック応答が返されるよう、API に対するポリシーを設定する方法について説明します。 この方法を使用すると、実際の応答を送信するためにバックエンドを使用できない場合でも、開発者が、APIM インスタンスの実装とテストを勧めることができます。 

応答を模擬表示する機能は、さまざまなシナリオで役に立ちます。

+ まず API ファサードを設計し、後でバックエンド実装を行う場合。 または、バックエンドを並列で開発している場合。
+ バックエンドが一時的に動作していないか、拡大縮小できない場合。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * テスト API を作成する 
> * 操作をテスト API に追加する
> * 応答のモック作成を有効にする
> * モック API をテストする


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="モック API 応答":::

## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ [Azure API Management のポリシーの概念](api-management-howto-policies.md)を理解します。
+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。

## <a name="create-a-test-api"></a>テスト API を作成する 

このセクションの手順では、バックエンドがない空の API を作成する方法について説明します。 


1. Azure portal にサインインして、API Management インスタンスに移動します。
1. **[API]**  >  **[+ API を追加]**  >  **[空の API]** を選択します。
1. **[空の API の作成]** ウィンドウで **[Full]\(完全\)** を選択します。
1. **[表示名]** に「*テスト API*」と入力します。
1. **[製品]** に **[Unlimited]\(無制限\)** を選択します。
1. **[ゲートウェイ]** で **[Managed]\(マネージド\)** が選択されていることを確認します。
1. **［作成］** を選択します

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="空の API を作成":::

## <a name="add-an-operation-to-the-test-api"></a>操作をテスト API に追加する

API は、少なくとも 1 つの操作を公開します。 このセクションでは、作成した空の API に操作を追加します。 このセクションの手順を完了した後、操作を呼び出すと、エラーが発生します。 後で「[応答のモック作成を有効にする](#enable-response-mocking)」セクションの手順を完了した後は、エラーは発生しません。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. 前の手順で作成した API を選びます。
1. **[+ Add Operation] \(+ 操作の追加\)** を選択します。
1. **[フロントエンド]** ウィンドウに次の値を入力します。

     | 設定             | 値                             | 説明                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **[表示名]**    | "*テスト呼び出し*"                       | [開発者ポータル](api-management-howto-developer-portal.md)に表示される名前。                                                                                                                                       |
    | **URL** (HTTP 動詞) | GET                               | あらかじめ定義されている HTTP 動詞のいずれかを選択します。                                                                                                                                         |
    | **[URL]**             | */test*                           | API の URL パス。                                                                                                                                                                       |
    | **説明**     |                                   |  操作の説明 (省略可)。開発者ポータルでこの API を使用する開発者にドキュメントを提供する目的で使用されます。                                                    |
    
1. URL、表示名、および説明の各フィールドの下にある **[応答]** タブを選択します。 応答の状態コード、コンテンツの種類、例、およびスキーマを定義するための設定をこのタブに入力します。
1. **[+ 応答の追加]** を選択し、一覧から **[200 OK]** を選択します。
1. 右側の **[表現]** 見出しで、**[+ 表現の追加]** を選択します。
1. 検索ボックスに「*application/json*」と入力し、コンテンツの種類として **application/json** を選択します。
1. **[サンプル]** テキスト ボックスに「`{ "sampleField" : "test" }`」と入力します。
1. **[保存]** を選択します。

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="API 操作を追加する" border="false":::

この例では必須ではありませんが、他のタブで、API 操作に対する追加の設定を構成できます。その例を次に示します。


|タブ      |説明  |
|---------|---------|
|**クエリ**     |  クエリ パラメーターを追加します。 名前と説明を提供するだけでなく、クエリ パラメーターに割り当てる値を指定することもできます。 値の 1 つを既定値としてマークすることができます (オプション)。        |
|**Request**     |  要求のコンテンツの種類、例、およびスキーマを定義します。       |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の使用を開始するには:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

テスト API に操作を追加するには、[az apim api operation create](/cli/azure/apim/api/operation#az_apim_api_operation_create) コマンドを実行します。

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

[az apim api operation list](/cli/azure/apim/api/operation#az_apim_api_operation_list) コマンドを実行して、API のすべての操作を表示します。

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

操作を削除するには、[az apim api operation delete](/cli/azure/apim/api/operation#az_apim_api_operation_delete) コマンドを使用します。 前のコマンドから操作 ID を取得します。

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

この記事の残りの部分で使用するために、この操作を保持します。

---

## <a name="enable-response-mocking"></a>応答のモック作成を有効にする

1. 「[テスト API を作成する](#create-a-test-api)」で作成した API を選択します。
1. 追加したテスト操作を選択します。
1. 右側のウィンドウで **[デザイン]** タブが選択されていることを確認します。
1. **[受信処理]** ウィンドウで、 **[+ ポリシーの追加]** を選択します。

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="処理ポリシーを追加する" border="false":::

1. ギャラリーから **[Mock responses]\(モック応答\)** を選択します。

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="モック応答ポリシー タイル" border="false":::

1. **[API Management response]\(API Management 応答\)** ボックスに「**200 OK, application/json**」と入力します。 この選択は、API が、前のセクションで定義した応答のサンプルを返すことを示します。

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="モック応答を設定する":::

1. **[保存]** を選択します。

    > [!TIP]
    > API の "**モック作成が有効になっています**" というテキストを含む黄色のバーは、API Management から返された応答が [モック作成ポリシー](api-management-advanced-policies.md#mock-response)によってモック作成されるものの、バックエンドによって生成されるわけではないことを示しています。

## <a name="test-the-mocked-api"></a>モック API をテストする

1. 「[テスト API を作成する](#create-a-test-api)」で作成した API を選択します。
1. **[テスト]** タブを選びます。
1. **[テスト呼び出し]** API が選択されていることを確認します。 **[送信]** を選択して、テスト呼び出しを実行します。

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="モック API をテストする":::

1. **[HTTP 応答]** に、チュートリアルの最初のセクションでサンプルとして指定した JSON が表示されます。

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="モック HTTP 応答":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * テスト API を作成する
> * 操作をテスト API に追加する
> * 応答のモック作成を有効にする
> * モック API をテストする

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
