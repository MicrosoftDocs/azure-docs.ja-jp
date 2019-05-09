---
title: Azure Functions の Python 開発者向けリファレンス
description: Python を使用して関数を開発する方法について説明します
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーレス アーキテクチャ, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 28f2b395c7f9be1b194b500ef20456be8ff405b0
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438699"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions の Python 開発者向けガイド

この記事では、Python を使用した Azure Functions の開発について紹介します。 以下の内容は、「[Azure Functions の開発者向けガイド](functions-reference.md)」を既に読んでいることを前提としています。

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>プログラミング モデル

Azure 関数は、入力を処理して出力を生成する Python スクリプト内でステートレスなメソッドである必要があります。 既定で、ランタイムでは、これは `main()` と呼ばれるグローバル メソッドとして `__init__.py` ファイル内に実装されると想定されます。

`function.json` ファイル内で `scriptFile` プロパティと `entryPoint` プロパティを指定すれば、既定の構成を変更することができます。 たとえば、以下の _function.json_ では、ご利用の Azure 関数のエントリ ポイントとして、_main.py_ ファイル内の _customentry()_ メソッドを使用するようにランタイムに指示が出されます。

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

トリガーとバインディングからのデータをメソッド属性を介して関数にバインドするには、`function.json` 構成ファイル内に定義されている `name` プロパティを使用します。 たとえば、次の _function.json_ には、`req` という名前の HTTP 要求によってトリガーされるシンプルな関数が記述されています。

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

`__init__.py` ファイルには、次の関数コードが含まれています。

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

必要があれば、Python の型の注釈を使用することで、関数内にパラメーターの型および戻り値の型を宣言することもできます。 たとえば、次のように注釈を使用して同じ関数を記述することができます。

```python
import azure.functions

def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```  

[azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) パッケージに含まれる Python の注釈を使用すると、入力と出力がご利用のメソッドにバインドされます。 

## <a name="folder-structure"></a>フォルダー構造

Python 関数プロジェクトのフォルダー構造は、次のようになります。

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 | - extensions.csproj
 | - bin
```

関数アプリの構成に使用できる共有 [host.json](functions-host-json.md) ファイルがあります。 各関数には、独自のコード ファイルとバインディング構成ファイル (function.json) があります。 

共有コードは、別のフォルダーに保存する必要があります。 SharedCode フォルダー内のモジュールを参照するには、次の構文を使用します。

```
from ..SharedCode import myFirstHelperFunction
```

Functions ランタイムで使用されるバインディング拡張機能は、`extensions.csproj` ファイル内に定義されており、実際のライブラリ ファイルは `bin` フォルダー内にあります。 ローカルで開発する場合は、Azure Functions Core Tools を使用して、[バインディング拡張機能を登録する](./functions-bindings-register.md#local-development-azure-functions-core-tools)必要があります。 

Functions プロジェクトを Azure 内のご利用の関数アプリにデプロイする場合は、フォルダー自体ではなく、パッケージに FunctionApp フォルダーの内容全体を含める必要があります。

## <a name="inputs"></a>入力

Azure Functions では、入力はトリガー入力と追加入力の 2 つのカテゴリに分けられます。 `function.json` ではこれらは同じではありませんが、Python コードでは同じように使用されます。 次のコード スニペットを例として使用します。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

この関数が呼び出されると、HTTP 要求は `req` として関数に渡されます。 エントリは、ルート URL 内の _id_ に基づいて Azure Blob Storage から取得され、関数の本体で `obj` として使用できるようになります。

## <a name="outputs"></a>出力

出力は、戻り値および出力パラメーターの両方で表現できます。 出力が 1 つのみの場合は、戻り値の使用をお勧めします。 出力が複数の場合は、出力パラメーターを使用する必要があります。

出力バインディングの値として関数の戻り値を使用するには、バインディングの `name` プロパティを `function.json` 内の `$return` に設定する必要があります。

複数の出力を生成するには、`azure.functions.Out` インターフェイスによって提供される `set()` メソッドを使用して、バインディングに値を割り当てます。 たとえば、次の関数を使用すると、キューにメッセージをプッシュすることに加え、HTTP 応答を返すこともできます。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>ログの記録

Azure Functions ランタイム ロガーへのアクセスは、ご利用の関数アプリ内のルート [ `logging`](https://docs.python.org/3/library/logging.html#module-logging) ハンドラーを介して利用できます。 このロガーは Application Insights に関連付けられているため、関数の実行中に検出される警告とエラーにフラグを設定することができます。

次の例では、HTTP トリガーを介して関数が呼び出されるときに、情報メッセージが記録されます。

```python
import logging

def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

他にもログ記録メソッドが用意されています。これにより、さまざまなトレース レベルでコンソールへの書き込みが可能になります。

| 方法                 | 説明                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_message_)**   | ルート ロガー上に CRITICAL レベルのメッセージを書き込みます。  |
| logging.**error(_message_)**   | ルート ロガー上に ERROR レベルのメッセージを書き込みます。    |
| logging.**warning(_message_)**    | ルート ロガー上に WARNING レベルのメッセージを書き込みます。  |
| logging.**info(_message_)**    | ルート ロガー上に INFO レベルのメッセージを書き込みます。  |
| logging.**debug(_message_)** | ルート ロガー上に DEBUG レベルのメッセージを書き込みます。  |

## <a name="importing-shared-code-into-a-function-module"></a>関数モジュールへの共有コードのインポート

関数モジュールと共に発行する Python モジュールは、相対インポート構文を使用してインポートする必要があります。

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

あるいは、共有コードをスタンドアロン パッケージに配置し、それをパブリックまたはプライベートの PyPI インスタンスに発行して、通常の依存関係として指定します。

## <a name="async"></a>非同期

1 つの関数アプリに付き、1 つの Python プロセスしか存在することができないので、`async def` ステートメントを使用してご自分の Azure 関数を非同期コルーチンとして実装することをお勧めします。

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

main() 関数が同期 (`async` 修飾子が付いていない) の場合、それは `asyncio` スレッド プール内で自動的に実行されます。

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Context

実行中に関数の呼び出しコンテキストを取得するには、そのシグニチャに `context` 引数を含めます。 

例: 

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

**コンテキスト** クラスには次のメソッドが含まれています。

`function_directory`  
関数が実行されるディレクトリです。

`function_name`  
関数の名前です。

`invocation_id`  
現在の関数呼び出しの ID です。

## <a name="python-version-and-package-management"></a>Python のバージョンとパッケージの管理

現在、Azure Functions では、Python 3.6.x (公式な CPython 配布) のみがサポートされています。

Azure Functions Core Tools または Visual Studio Code を使用してローカルで開発を行う場合は、必要なパッケージの名前とバージョンを `requirements.txt` ファイルに追加し、`pip` を使用してそれらをインストールしてください。

たとえば、次の要件のファイルと pip コマンドを使用すれば、PyPI から `requests` パッケージをインストールすることができます。

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

発行の準備ができたら、ご利用の依存関係がすべて、プロジェクト ディレクトリのルートにある `requirements.txt` ファイル内にリストされていることを確認します。 ご自分の Azure 関数を正常に実行するには、少なくとも次のパッケージが要件ファイルに含まれている必要があります。

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Azure への発行

使用しているパッケージにおいてコンパイラが必要とされるが、PyPI からの manylinux 対応のホイールのインストールがサポートされていない場合、Azure への発行は失敗し、次のエラーが返されます。 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

必要なバイナリを自動的にビルドして構成するには、[Azure Functions Core Tools](functions-run-local.md#v2) (func) を使用することにより、ご利用のローカル マシン上に [Docker をインストール](https://docs.docker.com/install/)し、次のコマンドを実行して発行します。 `<app name>` を、Azure 内のご自分の関数アプリの名前に置き換えることを忘れないでください。 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Core Tools では、ご利用のローカル マシン上で [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) イメージをコンテナーとして実行するためにバックグラウンドで Docker が使用されます。 この環境を使用する場合、必要なモジュールがソース配布からビルドおよびインストールされてから、Azure への最終的なデプロイに備えてそれらがパッケージ化されます。

> [!NOTE]
> Core Tools (func) では、PyInstaller プログラムを使用して、ユーザーのコードと依存関係が、Azure 内で実行される 1 つのスタンドアロン実行可能ファイルに固定されます。 この機能は現在プレビュー段階にあり、すべての種類の Python パッケージに展開されるとは限りません。 使用するモジュールをインポートできない場合は、`--no-bundler` オプションを使用してもう一度発行を試みてください。 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> それでもまだ問題が発生する場合は、Microsoft にお知らせください。そのためには、[issue を開き](https://github.com/Azure/azure-functions-core-tools/issues/new)、問題の説明を入力してください。 


依存関係をビルドし、継続的インテグレーション (CI) と継続的デリバリー (CD) システムを使用して発行するには、[Azure パイプライン](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts)または [Travis CI のカスタム スクリプト](https://docs.travis-ci.com/user/deployment/script/)を使用します。 

次に示すのは、ビルドおよび発行プロセスに関する `azure-pipelines.yml` スクリプトの例です。
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

次に示すのは、ビルドおよび発行プロセスに関する `.travis.yaml` スクリプトの例です。

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

```

## <a name="known-issues-and-faq"></a>既知の問題とよくあるご質問

既知の問題と機能に関する要望はすべて、[GitHub issues](https://github.com/Azure/azure-functions-python-worker/issues) リストを使用して追跡されます。 問題が発生してその問題が GitHub で見つからない場合は、新しい Issue を開き、その問題の詳細な説明を記載してお知らせください。

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
* [Blob Storage のバインド](functions-bindings-storage-blob.md)
* [HTTP と Webhook のバインド](functions-bindings-http-webhook.md)
* [Queue Storage のバインド](functions-bindings-storage-queue.md)
* [タイマー トリガー](functions-bindings-timer.md)
