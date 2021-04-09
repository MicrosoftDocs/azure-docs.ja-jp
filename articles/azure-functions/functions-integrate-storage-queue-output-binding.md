---
title: Functions を使用して Azure Storage キューにメッセージを追加する
description: Azure Functions を使用して、HTTP 要求によって呼び出されて、Azure Storage キューにメッセージを作成する、サーバーレス関数を作成します。
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d2821a16e0b72b32cc392b7ae626d782734458a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98674203"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Functions を使用して Azure Storage キューにメッセージを追加する

Azure Functions では、入力および出力バインディングによって、外部サービスからのデータをコードに使用できるようにする宣言方法が提供されます。 このクイックスタートでは、出力バインディングを使用して、関数が HTTP 要求によってトリガーされたときにキューにメッセージを作成します。 関数で作成するキュー メッセージを表示するには、Azure ストレージ コンテナーを使用します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- 「[Azure Portal で初めての関数を作成する](./functions-get-started.md)」の指示に従い、「**リソースのクリーンアップ**」の手順は実行しないでください。 ここで使用する関数と Function App は、そのクイックスタートで作成します。

## <a name="add-an-output-binding"></a><a name="add-binding"></a>出力バインディングを追加する

このセクションでは、Portal UI を使用して、前に作成した関数にキュー ストレージの出力バインディングを追加します。 このバインディングにより、最小限のコードの記述でキューにメッセージを作成できるようになります。 ストレージ接続のオープン、キューの作成、キューへの参照の取得などのタスクのためにコードを記述する必要はありません。 Azure Functions ランタイムおよびキューの出力バインディングが、ユーザーに代わってこれらのタスクを処理します。

1. Azure Portal で、「[Azure Portal で初めての関数を作成する](./functions-get-started.md)」で作成した Function App の Function App ページを開きます。 ページを開くには、**関数アプリ** を検索して選択します。 次に、関数アプリを選択します。

1. 関数アプリを選択し、前のクイックスタートで作成した関数を選択します。

1. **[統合]** を選択し、 **[+ 出力を追加する]** を選択します。

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="関数の出力バインディングを作成します。" border="true":::

1. **[Azure Queue Storage]** というバインディングの種類を選択して、このスクリーンショットの次の表で指定されている設定を追加します。 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="Azure portal 内の関数に Queue Storage の出力バインディングを追加します。" border="true":::
    
    | 設定      |  推奨値   | 説明                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **メッセージ パラメーター名** | outputQueueItem | 出力バインディング パラメーターの名前。 | 
    | **キュー名**   | outqueue  | ストレージ アカウント内の接続先のキューの名前。 |
    | **ストレージ アカウント接続** | AzureWebJobsStorage | Function App によって既に使用されているストレージ アカウント接続を使用するか、新しく作成できます。  |

1. **[OK]** を選択して、バインディングを追加します。

出力バインディングが定義されたので、コードを更新し、バインディングを使用して、メッセージをキューに追加する必要があります。  

## <a name="add-code-that-uses-the-output-binding"></a>出力バインディングを使用するコードを追加する

このセクションでは、出力キューにメッセージを書き込むコードを追加します。 メッセージには、クエリ文字列の HTTP トリガーに渡される値が含まれています。 たとえば、クエリ文字列に `name=Azure` が含まれる場合、キュー メッセージは「*Name passed to the function: Azure*」(関数に渡された名前: Azure) になります。

1. 関数で、 **[Code + Test]\(コード + テスト\)** を選択して、エディターに関数コードを表示します。

1. 関数の言語に応じて関数コードを更新します。

    # <a name="c"></a>[C\#](#tab/csharp)

    次の例で示すように、**outputQueueItem** パラメーターをメソッド シグネチャに追加します。

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    `return` ステートメントの直前の関数の本体で、パラメーターを使用してキュー メッセージを作成するコードを追加します。

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    `context.bindings` オブジェクトの出力バインディングを使用してキュー メッセージを作成するコードを追加します。 このコードを `context.done` ステートメントの前に追加します。

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. **[保存]** を選択して変更を保存します。

## <a name="test-the-function"></a>関数をテストする

1. コードの変更が保存されたら、 **[テスト]** を選択します。
1. テストが下の画像と一致することを確認し、 **[実行]** を選択します。 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="Azure portal でキュー ストレージのバインディングをテストします。" border="true":::

    **要求本文** に `name` 値 *Azure* が含まれていることに注意してください。 この値は、関数が呼び出されたときに作成されるキュー メッセージに表示されます。
    
    ここで **[実行]** を選択する代わりに、ブラウザーで URL を入力してクエリ文字列に `name` 値を指定することで、関数を呼び出すことができます。 このブラウザーの方法は、[前のクイック スタート](./functions-get-started.md)で示されています。

1. ログを確認して、関数が成功したことを確認します。 

出力バインディングを最初に使用するときに、**outqueue** という名前の新しいキューが、Functions ランタイムによってストレージ アカウントに作成されます。 キューとその中のメッセージが作成されたことを確認するには、ストレージ アカウントを使用します。

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>AzureWebJobsStorage に接続されているストレージ アカウントを検索します。


1. 関数アプリに移動して、 **[構成]** を選択します。

1. **[アプリケーション設定]** で、 **[AzureWebJobsStorage]** を選択します。

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="スクリーンショットには、AzureWebJobsStorage が選択されている構成ページが示されています。" border="true":::

1. アカウント名を検索してメモします。

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="AzureWebJobsStorage に接続されているストレージ アカウントを検索します。" border="true":::

### <a name="examine-the-output-queue"></a>出力キューを確認する

1. 関数アプリのリソース グループで、このクイックスタートに使用するストレージ アカウントを選択します。

1. **[Queue サービス]** で、 **[キュー]** を選択し、**outqueue** という名前のキューを選択します。 

   このキューには、HTTP によってトリガーされる関数を実行したときにキューの出力バインディングが作成されたというメッセージが含まれます。 *Azure* の既定の `name` 値で関数を呼び出した場合、キュー メッセージは「*Name passed to the function: Azure*」(関数に渡された名前: Azure) になります。

1. 関数を再度実行すると、キューに新しいメッセージが表示されます。  

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、既存の関数に出力バインディングを追加しました。 Queue Storage へのバインディングの詳細については、「[Azure Functions における Storage キュー バインド](functions-bindings-storage-queue.md)」を参照してください。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
