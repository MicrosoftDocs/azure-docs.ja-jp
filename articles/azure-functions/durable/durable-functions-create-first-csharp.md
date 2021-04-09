---
title: C# を使用して Azure で最初の永続関数を作成する
description: Visual Studio または Visual Studio Code を使用することで、Azure 永続関数を作成して発行します。
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: d5b6ecc12cee983cee0772da8b6f8f26a3b5a8ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008202"
---
# <a name="create-your-first-durable-function-in-c"></a>C\# で最初の Durable Functions を作成する

*Durable Functions* は、サーバーレス環境でステートフル関数を記述できる [Azure Functions](../functions-overview.md) の拡張機能です。 この拡張機能は状態、チェックポイント、再起動を管理します。

::: zone pivot="code-editor-vscode"

この記事では、Visual Studio Code を使用して、"hello world" 永続関数をローカルで作成してテストする方法を学習します。  この関数は、他の関数の呼び出しを調整し、連結します。 その後、関数コードを Azure に発行します。 これらのツールは、VS Code の [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)の一部として利用できます。

![持続的関数が表示されている Visual Studio Code ウィンドウを示すスクリーンショット。](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/download) をインストールします。

* 次の VS Code 拡張機能をインストールします。
    - [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* 最新バージョンの [Azure Functions Core Tools](../functions-run-local.md) があることを確認します。

* Durable Functions には Azure ストレージ アカウントが必要です。 Azure サブスクリプションが必要です。

* バージョン 3.1 以降の [.NET Core SDK](https://dotnet.microsoft.com/download) がインストールされていることを確認します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>ローカル プロジェクトを作成する 

このセクションでは、Visual Studio Code を使用して、ローカル Azure Functions プロジェクトを作成します。 

1. Visual Studio Code で、F1 (または Ctrl/Cmd + Shift + P) キーを押してコマンド パレットを開きます。 コマンド パレットで、`Azure Functions: Create New Project...` を検索して選択します。

    ![関数プロジェクトを作成する](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. プロジェクト用に空のフォルダーの場所を選択し、 **[選択]** を選択します。

1. プロンプトに従って、次の情報を入力します。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | Select a language for your function app project (関数アプリ プロジェクトの言語を選択してください) | C# | ローカル C# 関数プロジェクトを作成します。 |
    | Select a version (バージョンを選択してください) | Azure Functions v3 | このオプションが表示されるのは、Core Tools がまだインストールされていない場合だけです。 その場合、アプリの初回実行時に Core Tools がインストールされます。 |
    | Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください) | 今はしない | |
    | Select how you would like to open your project (プロジェクトを開く方法を選択してください) | 現在のウィンドウで開く | 選択したフォルダーで VS Code を開き直します。 |

Azure Functions Core Tools は、必要に応じて Visual Studio Code によりインストールされます。 また、関数アプリ プロジェクトがフォルダーに作成されます。 このプロジェクトには、[host.json](../functions-host-json.md) および [local.settings.json](../functions-run-local.md#local-settings-file) 構成ファイルが含まれています。

## <a name="add-functions-to-the-app"></a>アプリに関数を追加する

次の手順では、テンプレートを使用してプロジェクト内に永続関数のコードを作成します。

1. コマンド パレットで、`Azure Functions: Create Function...` を検索して選択します。

1. プロンプトに従って、次の情報を入力します。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | Select a template for your function (関数のテンプレートを選択してください) | DurableFunctionsOrchestration | Durable Functions オーケストレーションを作成します |
    | Provide a function name (関数名を指定してください) | HelloOrchestration | 関数が作成されるクラスの名前 |
    | Provide a namespace (名前空間を指定してください) | Company.Function | 生成されるクラスの名前空間 |

1. VS Code でストレージ アカウント選択のプロンプトが表示されたら、 **[ストレージ アカウントの選択]** を選択します。 プロンプトに従って次の情報を入力し、Azure で新しいストレージ アカウントを作成します。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | サブスクリプションの選択 | *<お使いのサブスクリプションの名前>* | Azure サブスクリプションを選択します。 |
    | Select a storage account (ストレージ アカウントを選択する) | 新しいストレージ アカウントの作成 |  |
    | Enter the name of the new storage account (新しいストレージ アカウントの名前を入力する) | *<一意の名前>* | 作成するストレージ アカウントの名前 |
    | リソース グループの選択 | *<一意の名前>* | 作成するリソース グループの名前 |
    | 場所を選択します。 | *リージョン* | 近くのリージョンを選択します |

新しい関数を含むクラスがプロジェクトに追加されます。 また VS Code により、ストレージ アカウントの接続文字列が *local.settings.json* に追加され、[`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) NuGet パッケージへの参照が *.csproj* プロジェクトファイルに追加されます。

新しい *HelloOrchestration.cs* ファイルを開いて内容を表示します。 この永続関数は、次のメソッドを使用した単純な関数チェーンの例です。  

| Method | FunctionName | 説明 |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | 持続的オーケストレーションを管理します。 このケースでは、オーケストレーションが起動し、一覧が作成され、3 つの関数呼び出しの結果が一覧に追加されます。  3 つの関数呼び出しが完了すると、一覧が返されます。 |
| **`SayHello`** | `HelloOrchestration_Hello` | 関数から hello が返されます。 これが、オーケストレーションされるビジネス ロジックを含む関数です。 |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | オーケストレーションのインスタンスを開始し、チェック状態の応答を返す、[HTTP によってトリガーされる関数](../functions-bindings-http-webhook.md)。 |

関数プロジェクトと Durable Functions を作成できたので、この関数をローカル コンピューターでテストすることができます。

## <a name="test-the-function-locally"></a>関数をローカルでテストする

Azure Functions Core Tools を使用すると、ローカルの開発用コンピューター上で Azure Functions プロジェクトを実行できます。 Visual Studio Code から初めて関数を起動すると、これらのツールをインストールするよう求めるメッセージが表示されます。

1. 関数をテストするには、`SayHello` アクティビティの関数コードにブレークポイントを設定し、F5 キーを押して関数アプリ プロジェクトを開始します。 Core Tools からの出力が **ターミナル** パネルに表示されます。

    > [!NOTE]
    > デバッグについて詳しくは、[Durable Functions の診断](durable-functions-diagnostics.md#debugging)に関する記事をご覧ください。

1. **ターミナル** パネルで、HTTP によってトリガーされる関数の URL エンドポイントをコピーします。

    ![Azure のローカル出力](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. [Postman](https://www.getpostman.com/) または [cURL](https://curl.haxx.se/) のようなツールを使用して、HTTP POST 要求を URL エンドポイントに送信します。

   応答は、永続的なオーケストレーションが正常に開始されたことを示す HTTP 関数の最初の結果です。 これはまだオーケストレーションの最終的な結果ではありません。 応答には、いくつかの便利な URL が含まれています。 ここでは、オーケストレーションの状態を照会してみましょう。

1. `statusQueryGetUri` の URL 値をコピーし、ブラウザーのアドレス バーに貼り付け、要求を実行します。 また、引き続き Postman を使用して GET 要求を発行することもできます。

   この要求によって、オーケストレーション インスタンスの状態が照会されます。 インスタンスが完了したことを示し、持続的な関数の出力または結果を含む、最終的な応答を受け取ります。 次のように表示されます。 

    ```json
    {
        "name": "HelloOrchestration",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. デバッグを停止するには、VS Code で **Shift キーを押しながら F5 キー** を押します。

関数がローカル コンピューター上で正常に動作することを確認したら、プロジェクトを Azure に発行します。

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Azure で関数をテストする

1. **出力** パネルから HTTP トリガーの URL をコピーします。 HTTP によってトリガーされる関数を呼び出す URL は、次の形式である必要があります。

    `https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart`

1. HTTP 要求のこの新しい URL をブラウザーのアドレス バーに貼り付けます。 以前の発行済みアプリの使用時と同じ状態応答を受け取るはずです。

## <a name="next-steps"></a>次のステップ

Visual Studio Code を使用して、C# の永続関数アプリを作成して発行しました。

> [!div class="nextstepaction"]
> [Durable Functions の一般的なパターンについて学習する](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

この記事では、Visual Studio 2019 を使用して、"hello world" 永続関数をローカルで作成してテストする方法を学習します。  この関数は、他の関数の呼び出しを調整し、連結します。 その後、関数コードを Azure に発行します。 これらのツールは、Visual Studio 2019 の Azure の開発ワークロードの一部として使用できます。

![持続的関数が表示されている Visual Studio 2019 ウィンドを示すスクリーンショット。](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) をインストールします。 **Azure 開発** ワークロードもインストールされていることを確認します。 Visual Studio 2017 でも Durable Functions 開発はサポートされていますが、UI と手順は異なります。

* [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) がインストールされ、実行されていることを確認します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>関数アプリ プロジェクトを作成する

Azure Functions テンプレートでは、Azure の関数アプリに発行できるプロジェクトを作成します。 関数アプリを使用すると、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。

1. Visual Studio で、 **[ファイル]** メニューから **[新規]**  >  **[プロジェクト]** の順に選択します。

1. **[新しいプロジェクトの作成]** ダイアログで `functions` を検索して **[Azure Functions]** テンプレートを選択し、 **[次へ]** を選択します。 

    ![Visual Studio で関数を作成するための [新しいプロジェクト] ダイアログ](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. プロジェクトの **プロジェクト名** を入力し、 **[OK]** を選択します。 プロジェクト名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。

1. **[新しい Azure Functions アプリケーションの作成]** で、図の下の表に示されている設定を使用してください。

    ![Visual Studio の [新しい Azure Functions アプリケーションの作成] ダイアログ](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | 設定      | 推奨値  | 説明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Version** | Azure Functions 3.0 <br />(.NET Core) | .NET Core 3.1 をサポートする Azure Functions のバージョン 3.0 ランタイムを使用する関数プロジェクトを作成します。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](../functions-versions.md)」をご覧ください。   |
    | **テンプレート** | Empty | 空の関数アプリを作成します。 |
    | **ストレージ アカウント**  | ストレージ エミュレーター | Durable Functions の状態管理にはストレージ アカウントが必要です。 |

4. **[作成]** を選択して、空の関数プロジェクトを作成します。 このプロジェクトには、関数を実行するために必要な基本的な構成ファイルがあります。

## <a name="add-functions-to-the-app"></a>アプリに関数を追加する

次の手順では、テンプレートを使用してプロジェクト内に永続関数のコードを作成します。

1. Visual Studio でプロジェクトを右クリックし、 **[追加]**  >  **[新しい Azure 関数]** を選択します。

    ![新しい関数を追加する](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. 追加メニューから **[Azure Function]** が選択されていることを確認し、C# ファイルの名前を入力してから **[追加]** を選択します。

1. **[Durable Functions のオーケストレーション]** テンプレートを選択し、 **[OK]** を選択します。

    ![Durable Functions Orchestration テンプレートを選択する](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

新しい永続関数がアプリに追加されます。  新しい .cs ファイルを開いて内容を表示します。 この永続関数は、次のメソッドを使用した単純な関数チェーンの例です。  

| Method | FunctionName | 説明 |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | 持続的オーケストレーションを管理します。 このケースでは、オーケストレーションが起動し、一覧が作成され、3 つの関数呼び出しの結果が一覧に追加されます。  3 つの関数呼び出しが完了すると、一覧が返されます。 |
| **`SayHello`** | `<file-name>_Hello` | 関数から hello が返されます。 これが、オーケストレーションされるビジネス ロジックを含む関数です。 |
| **`HttpStart`** | `<file-name>_HttpStart` | オーケストレーションのインスタンスを開始し、チェック状態の応答を返す、[HTTP によってトリガーされる関数](../functions-bindings-http-webhook.md)。 |

関数プロジェクトと Durable Functions を作成できたので、この関数をローカル コンピューターでテストすることができます。

## <a name="test-the-function-locally"></a>関数をローカルでテストする

Azure Functions Core Tools を使用すると、ローカルの開発用コンピューター上で Azure Functions プロジェクトを実行できます。 Visual Studio から初めて関数を開始すると、これらのツールをインストールするよう求めるメッセージが表示されます。

1. 関数をテストするには、F5 キーを押します。 メッセージが表示されたら、Visual Studio からの要求に同意し、Azure Functions Core (CLI) ツールをダウンロードしてインストールします。 また、ツールで HTTP 要求を処理できるようにファイアウォールの例外を有効にすることが必要になる場合もあります。

2. Azure Functions のランタイムの出力から、関数の URL をコピーします。

    ![Azure ローカル ランタイム](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. HTTP 要求の URL をブラウザーのアドレス バーに貼り付け、要求を実行します。 関数によって返されたローカルの GET 要求に対するブラウザーでの応答を次に示します。

    ![statusQueryGetUri が強調表示されているブラウザー ウィンドウを示すスクリーンショット。](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    応答は、永続的なオーケストレーションが正常に開始されたことを示す HTTP 関数の最初の結果です。  これはまだオーケストレーションの最終的な結果ではありません。  応答には、いくつかの便利な URL が含まれています。  ここでは、オーケストレーションの状態を照会してみましょう。

4. `statusQueryGetUri` の URL 値をコピーし、ブラウザーのアドレス バーに貼り付け、要求を実行します。

    この要求によって、オーケストレーション インスタンスの状態が照会されます。 最終的な応答は次のようになります。  この出力はインスタンスが完了したことを示し、永続的な関数の出力または結果を含みます。

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. デバッグを停止するには、**Shift キーを押しながら F5 キー** を押します。

関数がローカル コンピューター上で正常に動作することを確認した後、プロジェクトを Azure に発行します。

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

プロジェクトを発行するには、Azure サブスクリプションに関数アプリがあることが必要です。 関数アプリは、Visual Studio から直接作成できます。

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure で関数をテストする

1. [発行プロファイル] ページから関数アプリのベース URL をコピーします。 関数をローカルでテストしたときに使用した URL の `localhost:port` 部分を新しいベース URL に置き換えます。

    Durable Functions の HTTP トリガーを呼び出す URL は、次の形式である必要があります。

    `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart`

2. HTTP 要求のこの新しい URL をブラウザーのアドレス バーに貼り付けます。 以前の発行済みアプリの使用時と同じ状態応答を受け取るはずです。

## <a name="next-steps"></a>次のステップ

Visual Studio を使用して、C# の Durable Functions アプリを作成して発行しました。

> [!div class="nextstepaction"]
> [Durable Functions の一般的なパターンについて学習する](durable-functions-overview.md#application-patterns)

::: zone-end
