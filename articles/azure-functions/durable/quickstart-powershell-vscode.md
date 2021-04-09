---
title: PowerShell を使用して Azure Functions で最初の持続的関数を作成する
description: Visual Studio Code を使用して PowerShell で Azure Durable Functions を作成して発行します。
author: anthonychu
ms.topic: quickstart
ms.date: 08/10/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: edd02085abe63b124082255247362f096248ba82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317261"
---
# <a name="create-your-first-durable-function-in-powershell"></a>PowerShell で最初の持続的関数を作成する

*Durable Functions* は、サーバーレス環境でステートフル関数を記述できる [Azure Functions](../functions-overview.md) の拡張機能です。 この拡張機能は状態、チェックポイント、再起動を管理します。

この記事では、Visual Studio Code Azure Functions 拡張機能を使用して、"hello world" Durable Functions をローカルで作成してテストする方法について説明します。  この関数は、他の関数の呼び出しを調整し、連結します。 その後、関数コードを Azure に発行します。

![Azure で Durable Functions を実行する](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/download) をインストールします。

* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) VS Code 拡張機能をインストールします。

* 最新バージョンの [Azure Functions Core Tools](../functions-run-local.md) があることを確認します。

* Durable Functions には Azure ストレージ アカウントが必要です。 Azure サブスクリプションが必要です。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>ローカル プロジェクトを作成する 

このセクションでは、Visual Studio Code を使用して、ローカル Azure Functions プロジェクトを作成します。 

1. Visual Studio Code で、F1 (または Ctrl/Cmd + Shift + P) キーを押してコマンド パレットを開きます。 コマンド パレットで、`Azure Functions: Create New Project...` を検索して選択します。

    ![関数の作成](media/quickstart-js-vscode/functions-create-project.png)

1. プロジェクト用に空のフォルダーの場所を選択し、 **[選択]** を選択します。

1. プロンプトに従って、次の情報を入力します。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | Select a language for your function app project (関数アプリ プロジェクトの言語を選択してください) | PowerShell | ローカル PowerShell 関数プロジェクトを作成します。 |
    | Select a version (バージョンを選択してください) | Azure Functions v3 | このオプションが表示されるのは、Core Tools がまだインストールされていない場合だけです。 その場合、アプリの初回実行時に Core Tools がインストールされます。 |
    | Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください) | 今はしない | |
    | Select how you would like to open your project (プロジェクトを開く方法を選択してください) | 現在のウィンドウで開く | 選択したフォルダーで VS Code を開き直します。 |

Azure Functions Core Tools は、必要に応じて Visual Studio Code によりインストールされます。 また、関数アプリ プロジェクトがフォルダーに作成されます。 このプロジェクトには、[host.json](../functions-host-json.md) および [local.settings.json](../functions-run-local.md#local-settings-file) 構成ファイルが含まれています。

また、ルート フォルダーには、package.json ファイルが作成されます。

### <a name="configure-function-app-to-use-powershell-7"></a>PowerShell 7 を使用するように関数アプリを構成する

*local.settings.json* ファイルを開き、`FUNCTIONS_WORKER_RUNTIME_VERSION` という名前の設定が `~7` に設定されていることを確認します。 設定が存在しない場合や、別の値に設定されている場合は、ファイルの内容を更新します。

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

## <a name="create-your-functions"></a>関数を作成する

最も基本的な Durable Functions アプリには、3 つの関数が含まれています。

* "*オーケストレーター関数*" - 他の関数を調整するワークフローを記述します。
* "*アクティビティ関数*" - オーケストレーター関数によって呼び出され、作業を実行し、必要に応じて値を返します。
* *クライアント関数* - オーケストレーター関数を開始する通常の Azure Functions。 この例では、HTTP によってトリガーされる関数を使用しています。

### <a name="orchestrator-function"></a>オーケストレーター関数

テンプレートを使用してプロジェクト内に永続関数のコードを作成します。

1. コマンド パレットで、`Azure Functions: Create Function...` を検索して選択します。

1. プロンプトに従って、次の情報を入力します。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | Select a template for your function (関数のテンプレートを選択してください) | Durable Functions オーケストレーター (プレビュー) | Durable Functions オーケストレーションを作成します |
    | Provide a function name (関数名を指定してください) | HelloOrchestrator | 持続的な関数の名前 |

アクティビティ関数を調整するオーケストレーターを追加しました。 *HelloOrchestrator/run.ps1* を開いて、オーケストレーター関数を確認します。 `Invoke-ActivityFunction` コマンドレットを呼び出すたびに、`Hello` という名前のアクティビティ関数が呼び出されます。

次に、参照先の `Hello` アクティビティ関数を追加します。

### <a name="activity-function"></a>アクティビティ関数

1. コマンド パレットで、`Azure Functions: Create Function...` を検索して選択します。

1. プロンプトに従って、次の情報を入力します。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | Select a template for your function (関数のテンプレートを選択してください) | Durable Functions アクティビティ (プレビュー) | アクティビティ関数を作成する |
    | Provide a function name (関数名を指定してください) | こんにちは | アクティビティ関数の名前 |

オーケストレーターによって呼び出される `Hello` アクティビティ関数を追加しました。 *Hello/run.ps1* を開き、これが入力として名前を受け取って、あいさつを返すことを確認します。 アクティビティ関数では、データベース呼び出しや計算の実行などのアクションを実行します。

最後に、オーケストレーションを開始する、HTTP によってトリガーされる関数を追加します。

### <a name="client-function-http-starter"></a>クライアント関数 (HTTP スターター)

1. コマンド パレットで、`Azure Functions: Create Function...` を検索して選択します。

1. プロンプトに従って、次の情報を入力します。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | Select a template for your function (関数のテンプレートを選択してください) | Durable Functions HTTP スターター (プレビュー) | HTTP スターター関数を作成する |
    | Provide a function name (関数名を指定してください) | HttpStart | アクティビティ関数の名前 |
    | 承認レベル | Anonymous | デモの目的で、認証を使用せずに関数を呼び出すことができるようにします。 |

オーケストレーションを開始する、HTTP によってトリガーされる関数を追加しました。 *HttpStart/run. ps1* を開き、これが `Start-NewOrchestration` コマンドレットを使用して新しいオーケストレーションを開始することを確認します。 次に、`New-OrchestrationCheckStatusResponse` コマンドレットを使用して HTTP 応答を返します。この応答には、新しいオーケストレーションを監視および管理するために使用できる URL が含まれています。

これで、ローカルで実行して Azure にデプロイできる Durable Functions アプリが作成されました。

## <a name="test-the-function-locally"></a>関数をローカルでテストする

Azure Functions Core Tools を使用すると、ローカルの開発用コンピューター上で Azure Functions プロジェクトを実行できます。 Visual Studio Code から初めて関数アプリを起動すると、これらのツールをインストールするよう求めるメッセージが表示されます。

1. 関数をテストするには、`Hello` アクティビティ関数のコード (*Hello/run.ps1*) にブレークポイントを設定します。 F5 キーを押すか、コマンド パレットから `Debug: Start Debugging` を選択して、関数アプリ プロジェクトを開始します。 Core Tools からの出力が **ターミナル** パネルに表示されます。

    > [!NOTE]
    > デバッグについて詳しくは、[Durable Functions の診断](durable-functions-diagnostics.md#debugging)に関する記事をご覧ください。

1. Durable Functions を実行するには Azure ストレージ アカウントが必要です。 VS Code でストレージ アカウント選択のプロンプトが表示されたら、 **[ストレージ アカウントの選択]** を選択します。

    ![ストレージ アカウントの作成](media/quickstart-js-vscode/functions-select-storage.png)

1. プロンプトに従って次の情報を入力し、Azure で新しいストレージ アカウントを作成します。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | サブスクリプションの選択 | *<お使いのサブスクリプションの名前>* | Azure サブスクリプションを選択します。 |
    | Select a storage account (ストレージ アカウントを選択する) | 新しいストレージ アカウントの作成 |  |
    | Enter the name of the new storage account (新しいストレージ アカウントの名前を入力する) | *<一意の名前>* | 作成するストレージ アカウントの名前 |
    | リソース グループの選択 | *<一意の名前>* | 作成するリソース グループの名前 |
    | 場所を選択します。 | *リージョン* | 近くのリージョンを選択します |

1. **ターミナル** パネルで、HTTP によってトリガーされる関数の URL エンドポイントをコピーします。

    ![Azure のローカル出力](media/quickstart-js-vscode/functions-f5.png)

1. ブラウザーもしくは、[Postman](https://www.getpostman.com/) または [cURL](https://curl.haxx.se/) のようなツールを使用して、HTTP POST 要求を URL エンドポイントに送信します。 最後のセグメントをオーケストレーター関数の名前 (`HelloOrchestrator`) に置き換えます。 この URL は `http://localhost:7071/api/orchestrators/HelloOrchestrator` のようになります。

   応答は、永続的なオーケストレーションが正常に開始されたことを示す HTTP 関数の最初の結果です。 これはまだオーケストレーションの最終的な結果ではありません。 応答には、いくつかの便利な URL が含まれています。 ここでは、オーケストレーションの状態を照会してみましょう。

1. `statusQueryGetUri` の URL 値をコピーし、ブラウザーのアドレス バーに貼り付け、要求を実行します。 また、引き続き Postman を使用して GET 要求を発行することもできます。

   この要求によって、オーケストレーション インスタンスの状態が照会されます。 インスタンスが完了したことを示し、持続的な関数の出力または結果を含む、最終的な応答を受け取ります。 次のように表示されます。 

    ```json
    {
        "name": "HelloOrchestrator",
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

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

このセクションでは、ご利用の Azure サブスクリプションに関数アプリと関連リソースを作成し、コードをデプロイします。 

> [!IMPORTANT]
> 既存の関数アプリに発行すると、Azure のそのアプリのコンテンツが上書きされます。 


1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で、 **[Deploy to function app]\(関数アプリにデプロイ\)** ボタンを選択します。

    ![プロジェクトを Azure に発行する](../../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. プロンプトで、次の情報を入力します。

    + **Select folder (フォルダーを選択してください)** : ワークスペースのフォルダーを選択するか、関数アプリが格納されているフォルダーを参照します。 既に有効な関数アプリが開いている場合には、このプロンプトは表示されません。

    + **Select subscription (サブスクリプションを選択してください)** : 使用するサブスクリプションを選択します。 サブスクリプションが 1 つのみの場合、このプロンプトは表示されません。

    + **Select function app in Azure (Azure で関数アプリを選択してください)** : [`+ Create new Function App`] を選択します。 (`Advanced` オプションはこの記事では取り上げません。選択しないでください。)
      
    + **Enter a globally unique name for the function app (関数アプリのグローバルに一意の名前を入力します)** : URL パスに有効な名前を入力します。 入力した名前は、Azure Functions 内での一意性を確保するために検証されます。 

    + **Select a runtime (ランタイムを選択してください)** : ローカルで実行している PowerShell のバージョンを選択します。 `pwsh -version` コマンドを使用してバージョンを確認してください。

        > [!NOTE]
        > Azure Functions VS Code 拡張機能は、PowerShell 7 をまだサポートしていない可能性があります。 PowerShell 7 をオプションとして使用できない場合、現時点では PowerShell 6.x を選択し、関数アプリを作成した後、[手動でバージョンを更新](#update-function-app-ps7)してください。

    + **Select a location for new resources (新しいリソースの場所を選択してください)** : パフォーマンスを向上させるために、お近くの [リージョン](https://azure.microsoft.com/regions/)を選択してください。 
    
1.  完了すると、関数アプリ名に基づく名前を使用して、次の Azure リソースがサブスクリプションに作成されます。
    
    + リソース グループ。関連リソースの論理コンテナーです。
    + Standard Azure ストレージ アカウント。プロジェクトについての状態とその他の情報を保持します。
    + 従量課金プラン。サーバーレス関数アプリの実行環境となるホストを定義します。 
    + 関数アプリ。関数コードを実行するための環境となります。 関数アプリを使用すると、同じホスティング プランに含まれるリソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。
    + 関数アプリに接続された Application Insights インスタンス。サーバーレス関数の使用を追跡します。

    関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。

1. <a name="update-function-app-ps7"></a>先ほど、関数アプリの作成時に *PowerShell 7* を選択できなかった場合は、F1 キー (あるいは Ctrl または Cmd + Shift + P キー) を押してコマンド パレットを開きます。 コマンド パレットで、`Azure Functions: Upload Local Settings...` を検索して選択します。 画面の指示に従って、作成した関数アプリを選択します。 既存の設定を上書きするよう求めるメッセージが表示されたら、 *[すべていいえ]* を選択します。
    
1. この通知の **[View Output]\(出力の表示\)** を選択すると、作成済みの Azure リソースなど、作成とデプロイの結果が表示されます。 通知を見逃した場合は、右下隅にあるベル アイコンを選択して、再度確認します。

    ![作成完了通知](../../../includes/media/functions-publish-project-vscode/function-create-notifications.png)


## <a name="test-your-function-in-azure"></a>Azure で関数をテストする

1. **出力** パネルから HTTP トリガーの URL をコピーします。 HTTP によってトリガーされる関数を呼び出す URL は、次の形式である必要があります: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. HTTP 要求のこの新しい URL をブラウザーのアドレス バーに貼り付けます。 以前の発行済みアプリの使用時と同じ状態応答を受け取るはずです。

## <a name="next-steps"></a>次のステップ

Visual Studio Code を使用して、PowerShell の持続的関数アプリを作成して発行しました。

> [!div class="nextstepaction"]
> [Durable Functions の一般的なパターンについて学習する](durable-functions-overview.md#application-patterns)
