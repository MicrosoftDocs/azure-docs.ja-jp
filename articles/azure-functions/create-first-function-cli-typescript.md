---
title: コマンド ラインから TypeScript 関数を作成する - Azure Functions
description: コマンド ラインから TypeScript 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法を学習します。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 488ef9fa3fd5b6c09ed435483dbf8f6fa3eb5bef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937197"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>クイックスタート: コマンド ラインから Azure に TypeScript 関数を作成する

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

この記事では、コマンドライン ツールを使用して、HTTP 要求に応答する TypeScript 関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [Visual Studio Code ベースのバージョン](create-first-function-vs-code-typescript.md)も存在します。

## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.x。

+ 次のいずれかのツール。Azure リソースの作成に使用します。

    + [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

    + [Azure PowerShell](/powershell/azure/install-az-ps) バージョン 5.0 以降。

+ [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。

### <a name="prerequisite-check"></a>前提条件のチェック

前提条件を確認します。前提条件は、Azure リソースの作成に Azure CLI を使用するか、Azure PowerShell を使用するかによって異なります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 3.x であることを確認します。

+ `az --version` を実行して、Azure CLI バージョンが 2.4 以降であることを確認します。

+ `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 3.x であることを確認します。

+ `(Get-Module -ListAvailable Az).Version` を実行し、バージョン 5.0 以降であることを確認します。 

+ `Connect-AzAccount` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

---

## <a name="create-a-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

1. 次のように `func init` コマンドを実行して、特定のランタイムを含んだ *LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。  

    ```console
    func init LocalFunctionProj --typescript
    ```

1. プロジェクト フォルダーに移動します。

    ```console
    cd LocalFunctionProj
    ```
    
    このフォルダーにはプロジェクト用の各種ファイルが格納されています。たとえば、[local.settings.json](functions-run-local.md#local-settings-file) や [host.json](functions-host-json.md) といった名前の構成ファイルです。 *local.settings.json* には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で *.gitignore* ファイルによってソース管理から除外されます。

1. 次のコマンドを使用して、関数をプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前 (HttpExample) で、`--template` 引数は関数のトリガー (HTTP) を指定します。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    
    `func new` によって、関数と同じ名前のサブフォルダーが作成されます。ここには、プロジェクト用に選択した言語に適したコード ファイルと、*function.json* という名前の構成ファイルが含まれます。

### <a name="optional-examine-the-file-contents"></a>(省略可) ファイルの内容を確認する

先に「[関数をローカルで実行する](#run-the-function-locally)」に進み、ファイルの内容は後から確認してもかまいません。

#### <a name="indexts"></a>index.ts

*index.ts* は、*function.json* の構成に従ってトリガーされる関数をエクスポートします。

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

HTTP トリガーの場合、この関数は、*function.json* に定義されている **HttpRequest** 型の変数 `req` で要求データを受け取ります。 *function.json* で `$return` として定義されている返されるオブジェクトが応答です。 

#### <a name="functionjson"></a>function.json

*function.json* は、関数の入出力 `bindings` (トリガーの型を含む) を定義する構成ファイルです。 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

各バインディングは、方向、型、一意の名前を必要とします。 HTTP トリガーには、[`httpTrigger`](functions-bindings-http-webhook-trigger.md) 型の入力バインディングと、[`http`](functions-bindings-http-webhook-output.md) 型の出力バインディングが与えられます。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

1. *LocalFunctionProj* フォルダーから、ローカルの Azure Functions ランタイム ホストを起動して関数を実行します。

    ```console
    npm install
    npm start
    ```
    
    出力の最後の方に、次の行があります。
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > HttpExample が以下のように表示されない場合、プロジェクトのルート フォルダー以外からホストを起動したと考えられます。 その場合は **Ctrl** + **C** キーを使用してホストを停止し、プロジェクトのルート フォルダーに移動して、前出のコマンドを再度実行してください。

1. この出力から `HttpExample` 関数の URL をブラウザーにコピーし、クエリ文字列 `?name=<your-name>` を追加して、URL 全体を `http://localhost:7071/api/HttpExample?name=Functions` のようにします。 `Hello Functions` のようなメッセージがブラウザーに表示されます。

    ![ブラウザーでローカルに関数を実行した結果](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    要求を行うと、プロジェクトを起動したターミナルにもログ出力が表示されます。

1. 準備が完了したら、**Ctrl** + **C** キーを押し、`y` を選択して関数ホストを停止してください。

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Azure に関数アプリを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドで Azure に関数アプリを作成します。 Node.js 10 を使用している場合は、さらに `--runtime-version` を `10` に変更します。
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) コマンドレットによって、Azure に関数アプリが作成されます。 Node.js 10 を使用している場合は、`-RuntimeVersion` を `10` に変更します。

    ---
        
    前の例では、`<STORAGE_NAME>` を前の手順で使用したアカウントの名前に、`<APP_NAME>` を適宜グローバルに一意の名前に置き換えてください。 `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。 
    
    このコマンドでは、[Azure Functions 従量課金プラン](consumption-plan.md) (ここで発生する使用量に関しては無料) で、指定された言語ランタイムで実行される関数アプリを作成します。 また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。

## <a name="deploy-the-function-project-to-azure"></a>Azure に関数プロジェクトをデプロイする

Core Tools を使用して対象のプロジェクトを Azure にデプロイする前に、TypeScript ソース ファイルから JavaScript ファイルの運用対応のビルドを作成します。

1. 次のコマンドを使用して、TypeScript プロジェクトをデプロイする準備をします。

    ```console
    npm run build:production 
    ```

1. 必要なリソースが揃ったら、[func azure functionapp publish](functions-run-local.md#project-file-deployment) コマンドを使用して、ローカル関数プロジェクトを Azure の関数アプリにデプロイすることができます。 次の例の `<APP_NAME>` は、実際のアプリ名に置き換えてください。

    ```console
    func azure functionapp publish <APP_NAME>
    ```
    
    "... という名前のアプリが見つからない" という内容のエラーが表示された場合は、数秒待ってからやり直してください。先ほどの `az functionapp create` コマンドの後、Azure でアプリが完全には初期化されていない可能性があります。
    
    publish コマンドを実行すると、次のような出力結果が表示されます (簡潔にするため一部省略しています)。
    
    <pre>
    ...
    
    Getting site publishing info...
    Creating archive for current directory...
    Performing remote build for functions project.
    
    ...
    
    Deployment successful.
    Remote build succeeded!
    Syncing triggers...
    Functions in msdocs-azurefunctions-qs:
        HttpExample - [httpTrigger]
            Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
    </pre>

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)
