---
title: Azure で HTTP によってトリガーされる関数を作成する
description: Azure Functions Core Tools と Azure CLI を使用して、Azure で初めての Python 関数を作成する方法について説明します。
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 03b8e12d63ba84b4e20d7263f1c2ecb8d912936d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203158"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Azure で HTTP によってトリガーされる関数を作成する

この記事では、コマンドライン ツールを使用し、Azure Functions で実行される Python プロジェクトを作成する方法を紹介します。 また、HTTP 要求によってトリガーされる関数も作成します。 最後に、プロジェクトを公開し、Azure で[サーバーレス関数](functions-scale.md#consumption-plan)として実行します。

この記事は、Azure Functions 向けの 2 つの Python クイックスタートのうちの 1 つ目です。 このクイックスタートを終えたら、ご自身の関数に [Azure Storage キュー出力バインディングを追加](functions-add-output-binding-storage-queue-python.md)します。

## <a name="prerequisites"></a>前提条件

開始する前に、以下が必要になります。

+ [Python 3.6.x](https://www.python.org/downloads/) をインストールします。

+ [Azure Functions Core Tools](./functions-run-local.md#v2) バージョン 2.7.1575 以降をインストールします。

+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.x 以降のバージョンをインストールします。

+ 有効な Azure サブスクリプションを持っている。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>仮想環境を作成してアクティブにする (任意)

Python 関数をローカルで開発するには、Python 3.6.x 環境を使用する必要があります。 次のコマンドを実行して、`.venv` という名前の仮想環境を作成してアクティブにします。

> [!NOTE]
> お使いの Linux ディストリビューションに Python をインストールする際、venv がインストールされなかった場合は、次のコマンドを使用してインストールできます。
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash:

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell または Windows コマンド プロンプト:

```powershell
py -m venv .venv
.venv\scripts\activate
```

仮想環境をアクティブ化したので、そこで残りのコマンドを実行します。 仮想環境から出るには、`deactivate` を実行します。

## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

関数プロジェクトは、Azure での関数アプリに相当します。 同じローカル構成とホスティング構成を共有する複数の関数を使用できます。

1. 仮想環境で、次のコマンドを実行します。

    ```console
    func init MyFunctionProj
    ```

1. worker ランタイムとして、**python** を選択します。

    コマンドによって、_MyFunctionProj_ フォルダーが作成されます。 次の 3 つのファイルが含まれています。

    * *local.settings.json*: ローカルで実行するとき、アプリ設定と接続文字列を格納するために使用されます。 このファイルは Azure に公開されません。
    * *requirements.txt*: Azure に発行するときにシステムによってインストールされるパッケージの一覧が含まれています。
    * *host.json*: 関数アプリ内にあるすべての関数に影響するグローバル構成オプションが含まれます。 このファイルは Azure に公開されます。

1. 新しい *MyFunctionProj* フォルダーに移動します。

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>関数を作成する

新しいプロジェクトに関数を追加します。

1. プロジェクトに関数を追加するには、次のコマンドを実行します。

    ```console
    func new
    ```

1. 下矢印を使用して、 **[HTTP トリガー]** テンプレートを選択します。

1. 関数名の入力を求められたら、「*HttpTrigger*」と入力し、Enter キーを押します。

これらのコマンドでは、_HttpTrigger_ という名前のサブフォルダーが作成されます。 次のファイルが含まれます。

* *function.json*: 関数、トリガー、その他のバインディングを定義する構成ファイル。 このファイル内で、`scriptFile` の値が、関数を含むファイルを指していること、また、`bindings` 配列には呼び出しトリガーとバインディングが定義されていることがわかります。

    各バインディングは、方向、型、一意の名前を必要とします。 HTTP トリガーには、[`httpTrigger`](functions-bindings-http-webhook.md#trigger) 型の入力バインディングと、[`http`](functions-bindings-http-webhook.md#output) 型の出力バインディングが与えられます。

* *\_\_init\_\_.py*: HTTP でトリガーされる関数であるスクリプト ファイル。 このスクリプトには既定の `main()` があることがわかります。 トリガーからの HTTP データは、`binding parameter` という名前の `req` を使用して、関数に渡されます。 function.json に定義されている `req` は、[azure.functions.HttpRequest クラス](/python/api/azure-functions/azure.functions.httprequest)のインスタンスです。 

    *function.json* に `$return` として定義されているリターン オブジェクトは、[azure.functions.HttpResponse クラス](/python/api/azure-functions/azure.functions.httpresponse)のインスタンスです。 詳細については、「[Azure Functions の HTTP トリガーとバインド](functions-bindings-http-webhook.md)」を参照してください。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

関数は、Azure Functions ランタイムを使用してローカルで実行されます。

1. 次のコマンドでは、関数アプリが起動されます。

    ```console
    func host start
    ```

    Azure Functions ホストが起動されると、次のような出力が表示されます。 ここでは、読みやすくするために切り詰められています。

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
    
    Content root path: C:\functions\MyFunctionProj
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    ...
    
    Http Functions:
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. ランタイム出力から `HttpTrigger` 関数の URL をコピーして、それをブラウザーのアドレス バーに貼り付けます。

1. この URL にクエリ文字列 `?name=<yourname>` を追加して、要求を実行します。 次のスクリーンショットに、ローカル関数からブラウザーに返された GET 要求に対する応答を示します。

    ![ブラウザー上でローカルに検証する](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Ctrl + C キーを選択して、関数アプリをシャットダウンします。

ローカルで関数を実行したので、これで、Azure で関数アプリとその他の必要なリソースを作成できます。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Azure で関数アプリを作成する

関数アプリには、関数コードを実行するための環境を指定します。 これにより、リソースの管理、デプロイ、および共有を容易にするための論理ユニットとして関数をグループ化できます。

次のコマンドを実行します。 `<APP_NAME>` を一意の関数アプリ名に置き換えます。 `<STORAGE_NAME>` をストレージ アカウント名に置き換えます。 `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。 この名前は、Azure のすべてのアプリで一意である必要があります。

> [!NOTE]
> Linux と Windows のアプリを同じリソース グループ内でホストすることはできません。 Windows の関数アプリまたは Web アプリで `myResourceGroup` という名前のリソース グループが存在する場合、別のリソース グループを使用する必要があります。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

また、上記のコマンドでは、同じリソース グループ内に関連付けられている Azure Application Insights インスタンスがプロビジョニングされます。 このインスタンスを使用して関数アプリを監視し、ログを表示できます。

これで、Azure でローカル関数プロジェクトを関数アプリに公開できます。

## <a name="deploy-the-function-app-project-to-azure"></a>Azure に関数アプリのプロジェクトをデプロイする

Azure 上で関数アプリを作成した後、[func azure functionapp publish](functions-run-local.md#project-file-deployment) Core Tools コマンドを使用して、Azure にプロジェクト コードをデプロイできます。 この例では、`<APP_NAME>` をご自身のアプリの名前に置き換えてください。

```console
func azure functionapp publish <APP_NAME> --build remote
```

`--build remote` オプションは、デプロイ パッケージ内のファイルから Python プロジェクトを Azure にリモートでビルドします。 

次のメッセージとほぼ同じ出力が表示されます。 ここでは、読みやすくするために切り詰められています。

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

`HttpTrigger` に対応する `Invoke url` 値をコピーして、Azure 内で関数を検証するために使用できます。 URL には、ご自身の関数キーになっている `code` クエリ文字列の値が含まれ、他の人からは Azure 内の HTTP トリガー エンドポイントを呼び出すことが困難になります。

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> 公開された Python アプリのほぼリアルタイムのログを表示するには、[Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs) を使用します。

## <a name="next-steps"></a>次の手順

HTTP でトリガーされる関数で Python 関数プロジェクトを作成し、ローカル コンピューターでそれを実行し、Azure にデプロイしました。 次は以下の方法で関数を拡張します。

> [!div class="nextstepaction"]
> [Azure Storage キュー出力バインドを追加する](functions-add-output-binding-storage-queue-python.md)
