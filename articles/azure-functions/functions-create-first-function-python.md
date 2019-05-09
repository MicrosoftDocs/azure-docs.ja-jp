---
title: Azure で初めての Python 関数を作成する
description: Azure Functions Core Tools と Azure CLI を使用して、Azure で初めての Python 関数を作成する方法について説明します。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: af684a4fcc3a70326c1a57cb10a39204b4fd12dc
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438752"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Azure で初めての Python 関数を作成する (プレビュー)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

このクイック スタート記事では、Linux 上で動作する初めての[サーバーレス](https://azure.com/serverless) Python 関数アプリを Azure CLI を使用して作成する方法について説明します。 [Azure Functions Core Tools](functions-run-local.md) を使用して、関数のコードをローカルで作成し、その後 Azure にデプロイします。 関数アプリを Linux 上で実行するためのプレビューに関する考慮事項の詳細については、[Linux 上の関数に関するこちらの記事](https://aka.ms/funclinux)を参照してください。

次の手順は、Mac、Windows、または Linux コンピューターでサポートされます。

## <a name="prerequisites"></a>前提条件

ローカルでビルドしてテストするには、以下を行う必要があります。

+ [Python 3.6](https://www.python.org/downloads/) のインストール。

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 2.2.70 以降のインストール (.NET Core 2.x SDK が必要)。

Azure に発行して実行するには:

+ [Azure CLI]( /cli/azure/install-azure-cli) バージョン 2.x 以降をインストールします。

+ アクティブな Azure サブスクリプションが必要です。
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>仮想環境を作成してアクティブにする

関数プロジェクトを作成するには、Python 3.6 仮想環境で作業する必要があります。 次のコマンドを実行して、`.env` という名前の仮想環境を作成してアクティブにします。

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

次に、ローカル関数プロジェクトを作成できます。 このディレクトリは、Azure 上の関数アプリに相当します。 同じローカル構成とホスティング構成を共有する複数の関数を含めることができます。

コマンド プロンプトまたはターミナル ウィンドウで、次のコマンドを実行します。

```bash
func init MyFunctionProj
```

目的のランタイムとして **python** を選択します。

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

次のような出力が表示されます。

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

_MyFunctionProj_ という名前の新しいフォルダーが作成されます。 続行するには、このフォルダーにディレクトリを変更します。

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>関数を作成する

関数を作成するには、次のコマンドを実行します。

```bash
func new
```

テンプレートとして `HTTP Trigger` を選択し、**関数名**として `HttpTrigger` を指定します。

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

次のような出力が表示されます。

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

_HttpTrigger_ という名前のサブフォルダーが作成されます。 これには、プライマリ スクリプト ファイルである `__init__.py` と、トリガーの説明とこの関数によって使用されるバインドを記述する `function.json` ファイルが含まれています。 このプログラミング モデルの詳細については、「[Azure Functions Python developer guide](functions-reference-python.md)」(Azure Functions の Python 開発者向けガイド) を参照してください。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

次のコマンドを使用して、Functions ホストをローカルで実行します。

```bash
func host start
```

Functions ホストが起動すると、HTTP によってトリガーされる関数の URL が出力されます  (読みやすくするために出力の一部が省略されていることに注意してください)。

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %
...
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

出力からご自分の関数の URL をコピーしてブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<yourname>` を追加して、要求を実行します。

    http://localhost:7071/api/HttpTrigger?name=<yourname>

次のスクリーン ショットに、ブラウザーからトリガーされたときの関数の応答を示します。

![テスト](./media/functions-create-first-function-python/function-test-local-browser.png)

これで、関数アプリを作成して Azure に発行するために必要なその他のリソースの準備が整いました。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Azure に Linux の関数アプリを作成する

関数アプリには、関数コードを実行するための環境を指定します。 これにより、リソースの管理、デプロイ、および共有を容易にするための論理ユニットとして関数をグループ化できます。 [az functionapp create](/cli/azure/functionapp) コマンドを使用して、**Linux 上で動作する Python 関数アプリ**を作成します。

`<app_name>` プレースホルダーに一意の関数アプリ名を、ストレージ アカウント名として `<storage_name>` を使用して、次のコマンドを実行します。 `<app_name>` は、関数アプリの既定の DNS ドメインでもあります。 この名前は、Azure のすべてのアプリで一意である必要があります。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Linux 以外の App Service アプリを含んだ `myResourceGroup` という名前のリソース グループが既にある場合は、別のリソース グループを使用する必要があります。 Windows アプリと Linux アプリの両方を同じリソース グループでホストすることはできません。  

関数アプリが作成されると、次のメッセージが表示されます。

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

これで、ローカル関数プロジェクトを Azure 上の関数アプリに対して発行する準備が整いました。

## <a name="deploy-the-function-app-project-to-azure"></a>Azure に関数アプリのプロジェクトをデプロイする

Azure Functions Core Tools を使用して、次のコマンドを実行します。 `<app_name>` には、前の手順で作成したアプリの名前を指定します。

```bash
func azure functionapp publish <app_name>
```

次のような出力が表示されます (読みやすくするために一部が省略されています)。

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>次の手順

Python を使用した Azure Functions の開発の詳細を確認します。

> [!div class="nextstepaction"]
> [Azure Functions の Python 開発者ガイド](functions-reference-python.md)
> [Azure Functions のトリガーとバインド](functions-triggers-bindings.md)
