---
title: Azure Cosmos DB と Functions を使用して非構造化データを格納する
description: Azure Functions と Cosmos DB を使用して非構造化データを格納する
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5bc3895cb219338acde492b871dce806db70622b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91661161"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Azure Functions と Azure Cosmos DB を使用して非構造化データを格納する

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) は、非構造化 JSON データを格納するうえで最適な手段です。 Cosmos DB は、Azure Functions と連携させると、リレーショナル データベースにデータを格納する場合よりもはるかに少ないコードですばやく簡単にデータを格納することができます。

> [!NOTE]
> 現時点で、Azure Cosmos DB トリガー、入力バインディング、出力バインディングは、SQL API アカウントと Graph API アカウントにのみ使用できます。

Azure Functions では、入力および出力バインディングによって、関数から外部サービス データに接続する宣言方法が提供されます。 この記事では、Azure Cosmos DB ドキュメント内に非構造化データを格納する出力バインディングを追加するように既存の関数を更新する方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

出力バインディングを作成する前に、SQL API を使用する Azure Cosmos DB アカウントが必要です。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>出力バインディングを追加する

1. Azure portal で、前に作成した関数アプリに移動して選択します。

1. **[関数]** を選択し、HttpTrigger 関数を選択します。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="Azure portal で Http 関数を選択する。" border="true":::

1. **[統合]** を選択し、 **[+ 出力を追加する]** を選択します。

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="Azure Cosmos DB 出力バインディングを追加する。" border="true":::

1. 次の表に指定されている **[出力の作成]** 設定を使用します。

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="Azure Cosmos DB 出力バインディングを構成する。" border="true":::

    | 設定      | 推奨値  | 説明                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **バインドの種類** | Azure Cosmos DB | Azure Cosmos DB への出力バインディングを作成するために選択するバインディングの種類の名前。 |
    | **[ドキュメント パラメーター名]** | taskDocument | コード内で Cosmos DB オブジェクトを指す名前。 |
    | **データベース名** | taskDatabase | ドキュメントを保存するデータベースの名前。 |
    | **[コレクション名]** | taskCollection | データベース コレクションの名前 |
    | **[If true, creates the Cosmos DB database and collection]\(オンの場合、Cosmos DB データベースとコレクションを作成する\)** | はい | コレクションはまだ存在していないため、作成します。 |
    | **Cosmos DB アカウント接続** | 新しい設定 | **[新規]** を選択します。**Azure Cosmos DB アカウント** と前の手順で作成した **データベース アカウント** を選択し、 **[OK]** を選択します。 アカウントの接続のアプリケーション設定を作成します。 この設定は、データベースへの接続へのバインディングによって使用されます。 |

1. **[OK]** を選択してバインディングを作成します。

## <a name="update-the-function-code"></a>関数コードを更新する

既存の関数コードを、次の選択した言語のコードに置き換えます。

# <a name="c"></a>[C#](#tab/csharp)

既存の C# 関数を次のコードに置き換えます。

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

既存の JavaScript 関数を次のコードに置き換えます。

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

このサンプル コードは、HTTP 要求のクエリ文字列を読み取り、それらを `taskDocument` オブジェクトのフィールドに代入します。 `taskDocument` バインディングは、このバインディング パラメーターのオブジェクト データを、バインドされたドキュメント データベースに格納されるように送信します。 データベースは、関数が初めて実行されるときに作成されます。

## <a name="test-the-function-and-database"></a>関数とデータベースをテストする

1. **[Test/Run]\(テストと実行\)** を選択します。 **[クエリ]** の下の **[+ パラメーターの追加]** を選択し、クエリ文字列に以下のパラメーターを追加します。

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="関数をテストする。" border="true":::


1. **[実行]** を選択して、200 状態が返されることを確認します。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="[実行] を選択した後で HTTP 応答コード 200 状態が強調表示されているスクリーンショット。" border="true":::


1. Azure portal で、**Azure Cosmos DB** を検索して選択します。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="Cosmos DB サービスを検索する。" border="true":::

1. 対象の Azure Cosmos DB アカウントを選択してから、 **[データ エクスプローラー]** を選択します。

1. **[TaskCollection]** ノードを展開して新しいドキュメントを選択し、ドキュメントにクエリ文字列値といくつかの追加のメタデータが含まれていることを確認します。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="ドキュメント内の文字列値を確認する。" border="true":::

Azure Cosmos DB に非構造化データを格納するバインディングを HTTP トリガーに正常に追加できました。

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ

Cosmos DB データベースへのバインドの詳細については、「[Azure Functions における Cosmos DB のバインド](functions-bindings-cosmosdb.md)」を参照してください。

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
