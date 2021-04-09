---
title: コマンド ラインから PowerShell 関数を作成する - Azure Functions
description: コマンド ラインから PowerShell 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法を学習します。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
ms.openlocfilehash: abbe3b9ed4d9a8c9bf30c6be3e6980228d319090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937231"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>クイックスタート: コマンド ラインから Azure に PowerShell 関数を作成する

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

この記事では、コマンドライン ツールを使用して、HTTP 要求に応答する PowerShell 関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [Visual Studio Code ベースのバージョン](create-first-function-vs-code-powershell.md)も存在します。

## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.x。

+ 次のいずれかのツール。Azure リソースの作成に使用します。

    + [Azure PowerShell](/powershell/azure/install-az-ps) バージョン 5.0 以降。

    + [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

+ [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

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
    func init LocalFunctionProj --powershell
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

#### <a name="runps1"></a>run.ps1

*run.ps1* は、*function.json* の構成に従ってトリガーされる関数スクリプトを定義します。

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

HTTP トリガーの場合、この関数は、*function.json* に定義された `$Request` パラメーターに渡された要求データを受け取ります。 *function.json* で `Response` として定義されている返されるオブジェクトが、応答として `Push-OutputBinding` コマンドレットに渡されます。 

#### <a name="functionjson"></a>function.json

*function.json* は、関数の入出力 `bindings` (トリガーの型を含む) を定義する構成ファイルです。 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

各バインディングは、方向、型、一意の名前を必要とします。 HTTP トリガーには、[`httpTrigger`](functions-bindings-http-webhook-trigger.md) 型の入力バインディングと、[`http`](functions-bindings-http-webhook-output.md) 型の出力バインディングが与えられます。

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Azure に関数アプリを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドで Azure に関数アプリを作成します。 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) コマンドレットによって、Azure に関数アプリが作成されます。 
    
    ---
    
    前の例では、`<STORAGE_NAME>` を前の手順で使用したアカウントの名前に、`<APP_NAME>` を適宜グローバルに一意の名前に置き換えてください。 `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。 
    
    このコマンドでは、[Azure Functions 従量課金プラン](consumption-plan.md) (ここで発生する使用量に関しては無料) で、指定された言語ランタイムで実行される関数アプリを作成します。 また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する]

[Azure Storage キューに接続する]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell
