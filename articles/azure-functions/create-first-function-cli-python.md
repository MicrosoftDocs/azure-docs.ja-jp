---
title: コマンド ラインから Python 関数を作成する - Azure Functions
description: コマンド ラインから Python 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法を学習します。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-python-uiex
ms.openlocfilehash: 1b6805434f8c697c1ea13925ee681fdc7b5f1a29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704754"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>クイックスタート: コマンド ラインから Azure に Python 関数を作成する

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

この記事では、コマンドライン ツールを使用して、HTTP 要求に応答する Python 関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [Visual Studio Code ベースのバージョン](create-first-function-vs-code-python.md)も存在します。

## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.x。
  
+ 次のいずれかのツール。Azure リソースの作成に使用します。

    + [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

    + [Azure PowerShell](/powershell/azure/install-az-ps) バージョン 5.0 以降。

+ [Azure Functions でサポートされているバージョンの Python](supported-languages.md#languages-by-runtime-version)

### <a name="prerequisite-check"></a>前提条件のチェック

前提条件を確認します。前提条件は、Azure リソースの作成に Azure CLI を使用するか、Azure PowerShell を使用するかによって異なります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 3.x であることを確認します。

+ `az --version` を実行して、Azure CLI バージョンが 2.4 以降であることを確認します。

+ `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

+ `python --version` (Linux と macOS の場合) または `py --version` (Windows の場合) を実行して、使用している Python のバージョンが 3.8.x、3.7.x、または 3.6.x であることを確認します。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 3.x であることを確認します。

+ `(Get-Module -ListAvailable Az).Version` を実行し、バージョン 5.0 以降であることを確認します。 

+ `Connect-AzAccount` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

+ `python --version` (Linux と macOS の場合) または `py --version` (Windows の場合) を実行して、使用している Python のバージョンが 3.8.x、3.7.x、または 3.6.x であることを確認します。

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>仮想環境を作成してアクティブにする

適切なフォルダーで次のコマンドを実行し、`.venv` という名前の仮想環境を作成してアクティブにします。 必ず、Azure Functions でサポートされている Python 3.8、3.7、または 3.6 を使用してください。

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

お使いの Linux ディストリビューションに Python の venv パッケージがインストールされていなかった場合は、次のコマンドを実行します。

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

以降のコマンドはすべて、このアクティブ化された仮想環境で実行します 

## <a name="create-a-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

1. 次のように `func init` コマンドを実行して、特定のランタイムを含んだ *LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。  

    ```console
    func init LocalFunctionProj --python
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

#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* には、*function.json* 内の構成に従ってトリガーされる Python 関数 `main()` が含まれます。

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

HTTP トリガーの場合、この関数は、*function.json* に定義された変数 `req` で要求データを受け取ります。 `req` は、[azure.functions.HttpRequest クラス](/python/api/azure-functions/azure.functions.httprequest)のインスタンスです。 *function.json* に `$return` として定義されているリターン オブジェクトは、[azure.functions.HttpResponse クラス](/python/api/azure-functions/azure.functions.httpresponse)のインスタンスです。 詳細については、「[Azure Functions の HTTP トリガーとバインド](./functions-bindings-http-webhook.md?tabs=python)」を参照してください。

#### <a name="functionjson"></a>function.json

*function.json* は、関数の入出力 `bindings` (トリガーの型を含む) を定義する構成ファイルです。

`scriptFile` を変更することで、必要に応じて異なる Python ファイルを呼び出せます。

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

各バインディングは、方向、型、一意の名前を必要とします。 HTTP トリガーには、[`httpTrigger`](functions-bindings-http-webhook-trigger.md) 型の入力バインディングと、[`http`](functions-bindings-http-webhook-output.md) 型の出力バインディングが与えられます。

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイする前に、3 つのリソースを作成する必要があります。

- リソース グループ。関連リソースの論理コンテナーです。
- ストレージ アカウント。プロジェクトについての状態とその他の情報を保持します。
- 関数アプリ。関数コードを実行するための環境となります。 関数アプリは、ローカルの関数プロジェクトと対応関係にあります。これを使用すると、リソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。

以下のコマンドを使用してこれらの項目を作成します。 Azure CLI と PowerShell の両方がサポートされます。

1. まだ Azure にサインインしていない場合は、Azure にサインインします。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az-login) コマンドで Azure アカウントにサインインします。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットで Azure アカウントにサインインします。

    ---

1. `westeurope` リージョンに `AzureFunctionsQuickstart-rg` という名前のリソース グループを作成します。 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 リソース グループとリソースは通常、近くのリージョンに作成します。`az account list-locations` コマンドから返される利用可能なリージョンを使用してください。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。 リソース グループとリソースは通常、近くのリージョンに作成します。[Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドレットから返される利用可能なリージョンを使用してください。

    ---

    > [!NOTE]
    > Linux と Windows のアプリを同じリソース グループ内でホストすることはできません。 Windows の関数アプリまたは Web アプリで `AzureFunctionsQuickstart-rg` という名前のリソース グループが存在する場合、別のリソース グループを使用する必要があります。

1. リソース グループとリージョン内に汎用ストレージ アカウントを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドでストレージ アカウントを作成します。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドレットでストレージ アカウントを作成します。

    ---

    前の例の `<STORAGE_NAME>` は、適宜、Azure Storage 内で一意の名前に置き換えてください。 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` は汎用アカウントを指定します。これは [Functions でサポート](storage-considerations.md#storage-account-requirements)されています。
    
    このクイックスタートでは、ストレージ アカウントに関して数セント (米国ドル) の料金が発生します。

1. Azure に関数アプリを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドで Azure に関数アプリを作成します。 Python 3.7 または 3.6 を使用している場合は、`--runtime-version` をそれぞれ `3.7` または `3.6` に変更します。
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) コマンドレットによって、Azure に関数アプリが作成されます。 Python 3.7 または 3.6 を使用している場合は、`-RuntimeVersion` をそれぞれ `3.7` または `3.6` に変更します。

    ---
    
    前の例では、`<STORAGE_NAME>` を前の手順で使用したアカウントの名前に、`<APP_NAME>` を適宜グローバルに一意の名前に置き換えてください。  `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。 
    
    このコマンドでは、[Azure Functions 従量課金プラン](consumption-plan.md) (ここで発生する使用量に関しては無料) で、指定された言語ランタイムで実行される関数アプリを作成します。 また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Azure portal の Application Insights に凖リアルタイムの[ストリーミング ログ](functions-run-local.md#enable-streaming-logs)を表示するには、次のコマンドを実行します。

```console
func azure functionapp logstream <APP_NAME> --browser
```

別のターミナル ウィンドウまたはブラウザーで、もう一度リモート関数を呼び出します。 Azure で実行された関数の詳細ログがターミナルに表示されます。 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[問題がある場合は、お知らせください。](https://aka.ms/python-functions-qs-survey)