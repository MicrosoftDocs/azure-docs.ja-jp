---
title: Azure Functions を使用した .NET 5 関数の開発と発行
description: .NET 5.0 を使用して C# 関数を作成およびデバッグしてから、Azure Functions でサーバーレス ホスティングにローカル プロジェクトをデプロイする方法について説明します。
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 9751d5d354d738a64d6b02e8153464491d78ec14
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201360"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Azure Functions を使用した .NET 5 関数の開発と発行 

この記事では、.NET 5.0 を使用して C# 関数を操作する方法について説明します。この C# 関数は、Azure Functions ランタイムからプロセス外で実行されます。 これらの .NET 分離プロセス関数を作成してローカルでデバッグし、Azure に発行する方法について説明します。 Azure では、これらの関数は、.NET 5.0 をサポートする分離プロセスで実行されます。 詳細については、「[Azure において関数を .NET 5.0 で実行するためのガイド](dotnet-isolated-process-guide.md)」を参照してください。

.NET 5.0 をサポートしたり関数をアウトプロセスで実行したりする必要がない場合は、代わりに [C# クラス ライブラリ関数を作成](functions-create-your-first-function-visual-studio.md)することをお勧めします。

>[!NOTE]
>.NET 分離プロセス関数の開発は現在、Azure portal でサポートされていません。 .NET 5.0 アプリのアウトプロセスでの実行をサポートする関数アプリを Azure で作成するには、Azure CLI または Visual Studio Code 発行を使用する必要があります。   

## <a name="prerequisites"></a>前提条件

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [.NET SDK 5.0](https://www.microsoft.com/net/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.0.3381 以降のバージョン。

+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.20 移行のバージョン。  
::: zone pivot="development-environment-vscode"
+ [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。  

+ Visual Studio Code 用の [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。  

+ Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)バージョン 1.3.0 以降。
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/) (**Azure 開発** ワークロードを含む)。  
.NET 分離関数プロジェクト テンプレートと発行は現在、Visual Studio では使用できません。
::: zone-end

## <a name="create-a-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

::: zone pivot="development-environment-vs"

>[!NOTE]  
> 現時点では、.NET 分離関数プロジェクトの作成をサポートする Visual Studio プロジェクト テンプレートはありません。 この記事では、Core Tools を使用して C# プロジェクトを作成する方法について説明します。このプロジェクトは、Visual Studio でローカル実行してデバッグできます。  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で **[新しいプロジェクトの作成]** アイコンを選択します。

    ![[新しいプロジェクトの作成] を選択する](./media/functions-create-first-function-vs-code/create-new-project.png)

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. プロンプトで、次の情報を入力します。

    + **Select a language for your function project (関数プロジェクトの言語を選択してください)** : [`C#`] を選択します。

    + **.NET ランタイムを選択してください**: [`.NET 5 isolated`] を選択します。

    + **Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください)** : [`HTTP trigger`] を選択します。

    + **Provide a function name (関数名を指定してください):** 「`HttpExample`.

    + **Provide a namespace (名前空間を指定してください)** : 「`My.Functions`.

    + **承認レベル**: `Anonymous` を選択します。この場合、すべてのユーザーが関数のエンドポイントを呼び出すことができます。 承認レベルについては、「[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)」を参照してください。

    + **Select how you would like to open your project (プロジェクトを開く方法を選択してください)** : [`Add to workspace`] を選択します。

1. Visual Studio Code は、この情報を使用して、HTTP トリガーによる Azure Functions プロジェクトを生成します。 ローカル プロジェクト ファイルは、エクスプローラーで表示できます。 作成されるファイルの詳細については、「[生成されるプロジェクト ファイル](functions-develop-vs-code.md#generated-project-files)」を参照してください。
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. 次のように `func init` コマンドを実行して、*LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    `dotnetisolated` を指定すると、.NET 5.0 で実行するプロジェクトが作成されます。


1. プロジェクト フォルダーに移動します。

    ```console
    cd LocalFunctionProj
    ```

    このフォルダーにはプロジェクト用の各種ファイルが格納されています。たとえば、[local.settings.json](functions-run-local.md#local-settings-file) や [host.json](functions-host-json.md) などの構成ファイルです。 *local.settings.json* には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で *.gitignore* ファイルによってソース管理から除外されます。

1. 次のコマンドを使用して、関数をプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前 (HttpExample) で、`--template` 引数は関数のトリガー (HTTP) を指定します。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` によって、HttpExample.cs コード ファイルが作成されます。
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>関数をローカルで実行する

この時点で、プロジェクト フォルダーのルートから `func start` コマンドを実行し、C# 分離関数プロジェクトをコンパイルして実行できます。 現在、Visual Studio でアウトプロセス関数コードをデバッグする場合は、次の手順を使用して、実行中の Functions ランタイム プロセスにデバッガーを手動でアタッチする必要があります。  

1. Visual Studio でプロジェクト ファイル (.csproj) を開きます。 プロジェクト コードをレビューして変更したり、コード内で必要なブレーク ポイントを設定したりすることができます。 

1. ルート プロジェクト フォルダーから、ターミナルまたはコマンド プロンプトで次のコマンドを使用して、ランタイム ホストを起動します。

    ```console
    func start --dotnet-isolated-debug
    ```

    `--dotnet-isolated-debug` オプションでは、デバッガーがアタッチされるのを待ってから続行するようにプロセスに指示します。 出力の最後に、次のような行が表示されます。 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    `PID: XXXXXX` は、実行中の Functions ホストである dotnet.exe プロセスのプロセス ID (PID) を示します。
 
1. Azure Functions ランタイムの出力で、デバッガーをアタッチするホスト プロセスのプロセス ID をメモしておきます。 また、ローカル関数の URL もメモしておきます。

1. Visual Studio の **[デバッグ]** メニューから、 **[プロセスにアタッチ]** を選択し、プロセス ID と一致するプロセスを見つけて、 **[アタッチ]** を選択します。 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Functions ホスト プロセスにデバッガーをアタッチする":::    

    デバッガーがアタッチされている状態で、関数コードを通常どおりデバッグできます。

1. ブラウザーのアドレス バーに、次のようなローカル関数の URL を入力し、要求を実行します。 

    <http://localhost:7071/api/HttpExample>

    要求からのトレース出力が実行中のターミナルに書き込まれることを確認できます。 コードの実行は、関数コードで設定したすべてのブレーク ポイントで停止します。

1. 完了したら、ターミナルに移動し、Ctrl + C キーを押してホスト プロセスを停止します。
 
関数がローカル コンピューター上で正常に動作することを確認したら、プロジェクトを Azure に発行します。

> [!NOTE]  
> 現在、Visual Studio 発行は .NET 分離プロセス アプリでは使用できません。 Visual Studio でのプロジェクト開発が完了したら、Azure CLI を使用して、リモートの Azure リソースを作成する必要があります。 その後、コマンド ラインからもう一度 Azure Functions Core Tools を使用して、プロジェクトを Azure に発行できます。
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイする前に、3 つのリソースを作成する必要があります。

- [リソース グループ](../azure-resource-manager/management/overview.md)。関連リソースの論理コンテナーです。
- [ストレージ アカウント](../storage/common/storage-account-create.md)。関数についての情報 (状態など) を維持する目的で使用されます。
- 関数アプリ。関数コードを実行するための環境となります。 関数アプリは、ローカルの関数プロジェクトと対応関係にあります。これを使用すると、リソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。

以下のコマンドを使用してこれらの項目を作成します。

1. まだ Azure にサインインしていない場合は、Azure にサインインします。

    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az_login) コマンドで Azure アカウントにサインインします。

1. `westeurope` リージョンに `AzureFunctionsQuickstart-rg` という名前のリソース グループを作成します。

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 リソース グループとリソースは通常、近くのリージョンに作成します。`az account list-locations` コマンドから返される利用可能なリージョンを使用してください。

1. リソース グループとリージョン内に汎用ストレージ アカウントを作成します。

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドでストレージ アカウントを作成します。 

    前の例の `<STORAGE_NAME>` は、適宜、Azure Storage 内で一意の名前に置き換えてください。 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` は汎用アカウントを指定します。これは [Functions でサポート](storage-considerations.md#storage-account-requirements)されています。

1. Azure に関数アプリを作成します。
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドで Azure に関数アプリを作成します。 
    
    前の例では、`<STORAGE_NAME>` を前の手順で使用したアカウントの名前に、`<APP_NAME>` を適宜グローバルに一意の名前に置き換えてください。 `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。 
    
    このコマンドでは、[Azure Functions 従量課金プラン](consumption-plan.md)で .NET 5.0 を実行する関数アプリが作成されます。 このプランでは、この記事で発生する使用が無料になります。 また、関連付けられた Azure Application Insights インスタンスも同じリソース グループ内にプロビジョニングされます。 このインスタンスを使用して関数アプリを監視し、ログを表示します。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

このセクションでは、ご利用の Azure サブスクリプションに関数アプリと関連リソースを作成し、コードをデプロイします。

> [!IMPORTANT]
> 既存の関数アプリに発行すると、Azure のそのアプリのコンテンツが上書きされます。


1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で、 **[Deploy to function app]\(関数アプリにデプロイ\)** ボタンを選択します。

    ![プロジェクトを Azure に発行する](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. プロンプトで、次の情報を入力します。

    - **Select folder (フォルダーを選択してください)** : ワークスペースのフォルダーを選択するか、関数アプリが格納されているフォルダーを参照します。 既に有効な関数アプリが開いているときは、このプロンプトは表示されません。

    - **Select subscription (サブスクリプションを選択してください)** : 使用するサブスクリプションを選択します。 サブスクリプションが 1 つのみのときは、このプロンプトは表示されません。

    - **Select function app in Azure (Azure で関数アプリを選択してください)** : [`- Create new Function App`] を選択します。 (`Advanced` オプションはこの記事では取り上げません。選択しないでください。)
      
    - **Enter a globally unique name for the function app (関数アプリのグローバルに一意の名前を入力します)** : URL パスに有効な名前を入力します。 入力した名前は、Azure Functions 内での一意性を確保するために検証されます。
    
    - **ランタイム スタックを選択してください**: [`.NET 5 (non-LTS)`] を選択します。 
    
    - **Select a location for new resources (新しいリソースの場所を選択してください)** : パフォーマンスを向上させるために、お近くの [リージョン](https://azure.microsoft.com/regions/)を選択してください。 
    
    通知領域には、Azure で作成された個々のリソースの状態が表示されます。

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure リソース作成の通知":::
    
1.  完了すると、関数アプリ名に基づく名前を使用して、次の Azure リソースがサブスクリプションに作成されます。
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. この通知の **[View Output]\(出力の表示\)** を選択すると、作成済みの Azure リソースなど、作成とデプロイの結果が表示されます。 通知を見逃した場合は、右下隅にあるベル アイコンを選択して、再度確認します。

    ![作成完了通知](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事を完了するためにリソースを作成しました。 これらのリソースには、[アカウントの状態](https://azure.microsoft.com/account/)と[サービスの価格](https://azure.microsoft.com/pricing/)に応じて課金される場合があります。 

::: zone pivot="development-environment-cli"  
追加コストの発生を避けるために、次のコマンドを使用して、リソース グループとそこに含まれるすべてのリソースを削除してください。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
追加コストの発生を避けるために、次の手順に従って関数アプリとその関連リソースを削除してください。

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
追加コストの発生を避けるために、次の手順に従って関数アプリとその関連リソースを削除してください。

1. Cloud Explorer で、自分のサブスクリプション > **[App Services]** の順に展開し、関数アプリを右クリックして、 **[ポータルで開く]** を選択します。 

1. 関数アプリのページで、 **[概要]** タブを選択してから、 **[リソース グループ]** の下にあるリンクを選択します。

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="関数アプリのページで削除するリソース グループを選択する":::

2. **[リソース グループ]** ページで、含まれているリソースの一覧を確認し、削除するものであることを確認します。
 
3. **[リソース グループの削除]** を選択し、指示に従います。

   削除には数分かかることがあります。 実行されると、通知が数秒間表示されます。 ページの上部にあるベルのアイコンを選択して、通知を表示することもできます。
::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [.NET 分離関数の詳細情報](dotnet-isolated-process-guide.md)

