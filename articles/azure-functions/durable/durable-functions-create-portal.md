---
title: Azure portal を使用して Durable Functions を作成する
description: ポータル開発用に Azure Functions の Durable Functions 拡張機能をインストールする方法を説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 520579334d88bbab8ac28df7e446879aa9736248
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098201"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Azure portal を使用して Durable Functions を作成する

Azure Functions の [Durable Functions](durable-functions-overview.md) 拡張機能は、NuGet パッケージ [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) に提供されます。 この拡張機能は、お使いの関数アプリにインストールする必要があります。 この記事では、Azure portal で Durable Functions を開発できるように、このパッケージをインストールする方法を示します。

> [!NOTE]
> 
> * C# で Durable Functions を開発している場合は、代わりに [Visual Studio 2019 開発](durable-functions-create-first-csharp.md)を検討します。
> * JavaScript で Durable Functions を開発している場合は、代わりに [Visual Studio Code での開発](./quickstart-js-vscode.md)を検討してください。

## <a name="create-a-function-app"></a>Function App を作成する

すべての関数の実行をホストするには関数アプリが必要です。 関数アプリを使用すると、リソースの管理、デプロイ、および共有を容易にするためのロジック ユニットとして関数をグループ化できます。 .NET アプリまたは JavaScript アプリを作成することができます。

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

既定では、作成される関数アプリは、Azure Functions ランタイムのバージョン 2.x を使用します。 Durable Functions 拡張機能は、Azure Functions ランタイムのバージョン 1.x と 2.x の両方 (C# の場合) およびバージョン 2.x (JavaScript の場合) で機能します。 ただし、選択した言語に関係なく、テンプレートはランタイムのバージョン 2.x をターゲットとしている場合にのみ使用できます。

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>durable-functions npm パッケージをインストールする (JavaScript のみ)

JavaScript Durable Functions を作成する場合、[`durable-functions` npm パッケージ](https://www.npmjs.com/package/durable-functions)をインストールする必要があります。

1. 関数アプリの名前を選択し、 **[プラットフォーム機能]** を選択して、 **[高度なツール (Kudu)]** を選択します。

   ![関数のプラットフォーム機能を選択して Kudu を選択する](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Kudu コンソール内で、 **[デバッグ コンソール]** 、 **[CMD]** を選択します。

   ![Kudu デバッグ コンソール](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. 関数アプリのファイルのディレクトリ構造が表示されます。 `site/wwwroot` フォルダーに移動します。 そこから、`package.json` ファイルをファイル ディレクトリ ウィンドウにドラッグ アンド ドロップしてアップロードすることができます。 `package.json` のサンプルを次に示します。

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.1.2"
      }
    }
    ```

   ![Kudu アップロード (package.json)](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. `package.json` がアップロードされたら、Kudu リモート実行コンソールから `npm install` コマンドを実行します。

   ![Kudu の実行 (npm install)](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>オーケストレーター関数を作成する

1. Function App を展開し、 **[関数]** の横にある **[+]** ボタンをクリックします。 これが関数アプリの初めての関数の場合は、 **[ポータル内]** 、 **[続行]** の順に選択します。 それ以外の場合は、手順 3 に進みます。

   ![Azure Portal での関数のクイック スタート ページ](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. **[その他のテンプレート]** 、 **[Finish and view templates]\(終了してテンプレートを表示\)** の順に選択します。

    ![Functions のクイック スタート: [その他のテンプレート] を選択する](./media/durable-functions-create-portal/add-first-function.png)

1. 検索フィールドに「`durable`」と入力し、 **[Durable Functions HTTP starter]\(Durable Functions HTTP スターター\)** テンプレートを選択します。

1. プロンプトが表示されたら、 **[インストール]** を選択して Azure DurableTask 拡張機能とすべての依存関係を関数アプリにインストールします。 指定の関数アプリにつき、その拡張機能を 1 回のみインストールする必要があります。 インストールが正常に完了したら、 **[続行]** を選択します。

    ![バインディング拡張機能をインストールする](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. インストールが完了したら、新しい関数の名前を「`HttpStart`」にして **[作成]** を選択します。 作成された関数はオーケストレーションの開始に使用されます。

1. 関数アプリで別の関数を作成します。今回は、 **[Durable Functions Orchestrator]\(Durable Functions オーケストレーター\)** テンプレートを使用します。 新しいオーケストレーション関数の名前を「`HelloSequence`」にします。

1. **[Durable Functions Activity]\(Durable Functions アクティビティ\)** テンプレートを使用して、「`Hello`」という名前の 3 つ目の関数を作成します。

## <a name="test-the-durable-function-orchestration"></a>Durable Functions のオーケストレーションをテストする

1. **HttpStart** に戻り、 **[</> 関数の URL の取得]** を選択して、その URL を **[コピー]** します。 この URL を使用して **HelloSequence** 関数を開始します。

1. Postman や cURL などの HTTP ツールを使用して、コピーした URL に POST 要求を送信します。 次の例は、Durable Functions に POST 要求を送信する cURL コマンドです。

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
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

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Durable Functions の一般的なパターンについて学習する](durable-functions-concepts.md)
