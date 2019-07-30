---
title: Azure で HTTP によってトリガーされる関数を作成する
description: Azure Functions Core Tools と Azure CLI を使用して、Azure で初めての Python 関数を作成する方法について説明します。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 5ef30fbf647492f79c64508d8306868aa1f6b278
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444574"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Azure で HTTP によってトリガーされる関数を作成する

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

この記事では、コマンドライン ツールを使用し、Azure Functions で実行される Python プロジェクトを作成する方法を紹介します。 作成する関数は、HTTP 要求によってトリガーされます。 最後に、プロジェクトを公開し、Azure で[サーバーレス関数](functions-scale.md#consumption-plan)として実行します。

この記事は、Azure Functions の 2 つのクイックスタートの 1 つ目です。 この記事を終えたら、自分の関数に [Azure Storage キュー出力バインディングを追加します](functions-add-output-binding-storage-queue-python.md)。

## <a name="prerequisites"></a>前提条件

始める前に、以下のものを用意する必要があります。

+ [Python 3.6](https://www.python.org/downloads/) のインストール。

+ [Azure Functions Core Tools](./functions-run-local.md#v2) バージョン 2.6.1071 以降をインストールします。

+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.x 以降のバージョンをインストールします。

+ 有効な Azure サブスクリプション

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>仮想環境を作成してアクティブにする

Python 関数をローカルで開発し、テストするには、Python 3.6 環境で作業する必要があります。 次のコマンドを実行して、`.env` という名前の仮想環境を作成してアクティブにします。

### <a name="bash"></a>Bash:

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell または Windows コマンド プロンプト:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

残りのコマンドは仮想環境内で実行されます。

## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

Functions プロジェクトは、Azure の関数アプリに相当します。 同じローカル構成とホスティング構成を共有する複数の関数を使用できます。

仮想環境で次のコマンドを実行します。worker ランタイムとして **python** を選択します。

```console
func init MyFunctionProj
```

_MyFunctionProj_ という名前のフォルダーが作成されます。これには次の 3 つのファイルが含まれています。

* `local.settings.json` は、ローカルで実行するとき、アプリ設定と接続文字列を格納するために使用されます。 このファイルは Azure に公開されません。
* `requirements.txt` には、Azure に公開するときにインストールされるパッケージの一覧が含まれます。
* `host.json` には、関数アプリのすべての関数に影響するグローバル構成オプションが含まれます。 このファイルは Azure に公開されます。

新しい MyFunctionProj フォルダーに移動します。

```console
cd MyFunctionProj
```

次に、host.json ファイルを更新し、拡張バンドルを有効にします。  

## <a name="create-a-function"></a>関数を作成する

プロジェクトに関数を追加するには、次のコマンドを実行します。

```console
func new
```

**[HTTP トリガー]** テンプレートを選択し、関数の名前として「`HttpTrigger`」と入力し、Enter キーを押します。

_HttpTrigger_ という名前のサブフォルダーが作成されます。これには次のファイルが含まれます。

* **function.json**: 関数、トリガー、その他のバインディングを定義する構成ファイル。 このファイルを見て、`scriptFile` の値が、関数が含まれるファイルを指していることを確認します。呼び出しのトリガーとバインディングは `bindings` 配列に定義されています。

  各バインディングは、方向、型、一意の名前を必要とします。 HTTP トリガーには、[`httpTrigger`](functions-bindings-http-webhook.md#trigger) 型の入力バインディングと、[`http`](functions-bindings-http-webhook.md#output) 型の出力バインディングが与えられます。

* **\_\_init\_\_.py**: HTTP でトリガーされる関数であるスクリプト ファイル。 このスクリプトを見て、それに既定の `main()` が含まれていることを確認します。 トリガーからの HTTP データは、バインディング パラメーターという名前の `req` を利用し、この関数に渡されます。 function.json に定義されている `req` は、[azure.functions.HttpRequest クラス](/python/api/azure-functions/azure.functions.httprequest)のインスタンスです。 

    function.json に `$return` として定義されているリターン オブジェクトは、[azure.functions.HttpResponse クラス](/python/api/azure-functions/azure.functions.httpresponse)のインスタンスです。 詳細については、「[Azure Functions の HTTP トリガーとバインド](functions-bindings-http-webhook.md)」を参照してください。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

次のコマンドでは、Azure にある同じ Azure Functions ランタイムを使用してローカル実行される関数アプリが起動されます。

```bash
func host start
```

Functions ホストが起動すると、次のような出力が表示されます。この出力は、読みやすいように切り詰められています。

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

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

ランタイム出力から `HttpTrigger` 関数の URL をコピーして、それをブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<yourname>` を追加して、要求を実行します。 ローカルの関数によって返された GET 要求に対するブラウザーでの応答を次に示します。

![ブラウザーでローカルにテストする](./media/functions-create-first-function-python/function-test-local-browser.png)

ローカルで関数を実行したので、これで、Azure で関数アプリとその他の必要なリソースを作成できます。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Azure で関数アプリを作成する

関数アプリには、関数コードを実行するための環境を指定します。 これにより、リソースの管理、デプロイ、および共有を容易にするための論理ユニットとして関数をグループ化できます。

`<APP_NAME>` プレースホルダーに一意の関数アプリ名を、ストレージ アカウント名として `<STORAGE_NAME>` を使用して、次のコマンドを実行します。 `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。 この名前は、Azure のすべてのアプリで一意である必要があります。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

> [!NOTE]
> Azure Functions の Linux 向け従量課金プランは現在、プレビュー段階であり、使用できるのは次のリージョンだけです: 米国西部、米国東部、西ヨーロッパ、東アジア。 さらに、Linux アプリと Windows アプリは同じリソース グループ内でホストすることができません。 Windows の関数アプリまたは Web アプリで `myResourceGroup` という名前のリソース グループが存在する場合、別のリソース グループを使用する必要があります。

これで、Azure でローカル関数プロジェクトを関数アプリに公開できます。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="next-steps"></a>次の手順

HTTP でトリガーされる関数で Python 関数プロジェクトを作成し、ローカル コンピューターでそれを実行し、Azure にデプロイしました。 次は以下の方法で関数を拡張します。

> [!div class="nextstepaction"]
> [Azure Storage キュー出力バインドを追加する](functions-add-output-binding-storage-queue-python.md)
