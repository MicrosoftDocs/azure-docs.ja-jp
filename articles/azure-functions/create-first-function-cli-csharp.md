---
title: コマンド ラインから C# 関数を作成する - Azure Functions
description: コマンド ラインから C# 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法を学習します。
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-csharp-ieux
ms.openlocfilehash: 08b1f2b112542a4b4772744d122ce1260b0edffd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704771"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>クイックスタート: Azure でコマンド ラインから C# 関数を作成する

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

この記事では、コマンドライン ツールを使用して、HTTP 要求に応答する C# クラス ライブラリベースの関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [Visual Studio Code ベースのバージョン](create-first-function-vs-code-csharp.md)も存在します。

## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.x。

+ 次のいずれかのツール。Azure リソースの作成に使用します。

    + [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

    + [Azure PowerShell](/powershell/azure/install-az-ps) バージョン 5.0 以降。

### <a name="prerequisite-check"></a>前提条件のチェック

前提条件を確認します。前提条件は、Azure リソースの作成に Azure CLI を使用するか、Azure PowerShell を使用するかによって異なります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 3.x であることを確認します。

+ `az --version` を実行して、Azure CLI バージョンが 2.4 以降であることを確認します。

+ `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

+ `dotnet --list-sdks` を実行して、.NET Core SDK バージョン 3.1.x がインストールされていることを確認します。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 3.x であることを確認します。

+ `(Get-Module -ListAvailable Az).Version` を実行し、バージョン 5.0 以降であることを確認します。 

+ `Connect-AzAccount` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

+ `dotnet --list-sdks` を実行して、.NET Core SDK バージョン 3.1.x がインストールされていることを確認します。

---

## <a name="create-a-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

1. 次のように `func init` コマンドを実行して、特定のランタイムを含んだ *LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。  

    ```csharp
    func init LocalFunctionProj --dotnet
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

    `func new` によって、HttpExample.cs コード ファイルが作成されます。

### <a name="optional-examine-the-file-contents"></a>(省略可) ファイルの内容を確認する

先に「[関数をローカルで実行する](#run-the-function-locally)」に進み、ファイルの内容は後から確認してもかまいません。

#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* には、`Run` メソッドが含まれています。これは、トリガー動作を定義する **HttpTriggerAttribute** で装飾された [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) 内の `req` 変数で要求データを受け取ります。

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

返されるオブジェクトは、[OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) または [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) として応答メッセージを返す [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) です。 詳細については、「[Azure Functions の HTTP トリガーとバインド](./functions-bindings-http-webhook.md?tabs=csharp)」を参照してください。

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Azure に関数アプリを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドで Azure に関数アプリを作成します。 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
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

[Azure Storage キューに接続する]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp
