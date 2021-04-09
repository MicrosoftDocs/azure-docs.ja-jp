---
title: Azure portal を使用して Durable Functions を作成する
description: ポータル開発用に Azure Functions の Durable Functions 拡張機能をインストールする方法を説明します。
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: b029fa246977dfe4210f6e8df242415f7e4103f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87081918"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Azure portal を使用して Durable Functions を作成する

Azure Functions の [Durable Functions](durable-functions-overview.md) 拡張機能は、NuGet パッケージ [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) に提供されます。 この拡張機能は、お使いの関数アプリにインストールする必要があります。 この記事では、Azure portal で Durable Functions を開発できるように、このパッケージをインストールする方法を示します。

> [!NOTE]
> 
> * C# で Durable Functions を開発している場合は、代わりに [Visual Studio 2019 開発](durable-functions-create-first-csharp.md)を検討します。
> * JavaScript で Durable Functions を開発している場合は、代わりに [Visual Studio Code での開発](./quickstart-js-vscode.md)を検討してください。

## <a name="create-a-function-app"></a>Function App を作成する

すべての関数の実行をホストするには関数アプリが必要です。 関数アプリを使用すると、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。 .NET アプリまたは JavaScript アプリを作成することができます。

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

既定では、作成される関数アプリは、Azure Functions ランタイムのバージョン 2.x を使用します。 Durable Functions 拡張機能は、Azure Functions ランタイムのバージョン 1.x と 2.x の両方 (C# の場合) およびバージョン 2.x (JavaScript の場合) で機能します。 ただし、選択した言語に関係なく、テンプレートはランタイムのバージョン 2.x をターゲットとしている場合にのみ使用できます。

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>durable-functions npm パッケージをインストールする (JavaScript のみ)

JavaScript Durable Functions を作成する場合、[`durable-functions` npm パッケージ](https://www.npmjs.com/package/durable-functions)をインストールする必要があります。

1. 関数アプリのページで、左側のウィンドウの **[開発ツール]** の下の **[高度なツール]** を選択します。

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="関数のプラットフォーム機能が Kudu を選択":::

2. **[高度なツール]** ページで、 **[移動]** を選択します。

3. Kudu コンソール内で、 **[デバッグ コンソール]** 、 **[CMD]** を選択します。

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu デバッグ コンソール":::

3. 関数アプリのファイルのディレクトリ構造が表示されます。 `site/wwwroot` フォルダーに移動します。 そこから、`package.json` ファイルをファイル ディレクトリ ウィンドウにドラッグ アンド ドロップしてアップロードすることができます。 `package.json` のサンプルを次に示します。

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu アップロード (package.json)":::

4. `package.json` がアップロードされたら、Kudu リモート実行コンソールから `npm install` コマンドを実行します。

   ![Kudu の実行 (npm install)](./media/durable-functions-create-portal/kudu-npm-install.png)
   
## <a name="create-an-orchestrator-function"></a>オーケストレーター関数を作成する

1. 関数アプリで、左側のウィンドウから **[関数]** を選択し、上部のメニューから **[追加]** を選択します。 

1. **[新規関数]** ページの検索フィールドに「`durable`」と入力し、 **[Durable Functions HTTP スターター]** テンプレートを選択します。

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="[Durable Functions HTTP スターター] の選択":::

1. **[新規関数]** の名前を「`HttpStart`」と入力し、 **[関数の作成]** をクリックします。

   作成された関数はオーケストレーションの開始に使用されます。

1. 関数アプリで別の関数を作成します。今回は、 **[Durable Functions オーケストレーター]** テンプレートを使用します。 新しいオーケストレーション関数の名前を「`HelloSequence`」にします。

1. **[Durable Functions のアクティビティ]** テンプレートを使用して、「`Hello`」という名前の 3 つ目の関数を作成します。

## <a name="test-the-durable-function-orchestration"></a>Durable Functions のオーケストレーションをテストする

1. **HttpStart** 関数に戻り、 **[関数の URL の取得]** を選択し、 **[クリップボードにコピー]** アイコンを選択して URL をコピーします。 この URL を使用して **HelloSequence** 関数を開始します。

1. Postman や cURL などの HTTP ツールを使用して、コピーした URL に POST 要求を送信します。 次の例は、Durable Functions に POST 要求を送信する cURL コマンドです。

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence --header "Content-Length: 0"
    ```

    この例では、`{your-function-app-name}` は対象の関数アプリの名前であるドメインです。 応答メッセージには、実行を監視および管理するために使用できる、一連の URI エンドポイントが含まれます。次の例のようになります。

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. `statusQueryGetUri` エンドポイント URI を呼び出すと、その Durable Functions の現在の状態が表示されます。次の例のようになります。

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. 状態が **[完了]** に変わるまで `statusQueryGetUri` の呼び出しを続けます。次の例のような応答が表示されます。

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

これで最初の Durable Functions が稼働し、Azure で実行されるようになります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Durable Functions の一般的なパターンについて学習する](durable-functions-overview.md#application-patterns)
