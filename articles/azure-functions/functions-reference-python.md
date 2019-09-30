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
ms.openlocfilehash: 7922f07cfe08d0bd58827b59337b86387c624778
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844687"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions の Python 開発者向けガイド

この記事では、Python を使用した Azure Functions の開発について紹介します。 以下の内容は、「[Azure Functions の開発者向けガイド](functions-reference.md)」を既に読んでいることを前提としています。 

Python でのスタンドアロンの関数のサンプル プロジェクトについては、[Python 関数のサンプル](/samples/browse/?products=azure-functions&languages=python)を参照してください。 

## <a name="programming-model"></a>プログラミング モデル

Azure Functions では、関数は入力を処理して出力を生成する Python スクリプト内のステートレスなメソッドであることが求められます。 既定で、ランタイムでは、このメソッドは `main()` と呼ばれるグローバル メソッドとして `__init__.py` ファイル内に実装されると想定されます。 また、[代替エントリ ポイントを指定する](#alternate-entry-point)こともできます。

トリガーとバインディングからのデータをメソッド属性を介して関数にバインドするには、*function.json* ファイル内で定義されている `name` プロパティを使用します。 たとえば、次の _function.json_ には、`req` という名前の HTTP 要求によってトリガーされるシンプルな関数が記述されています。

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

Python の型の注釈を使用することで、関数内に属性の種類および戻り値の型を明示的に宣言することもできます。 これは、多くの Python コード エディターによって提供される IntelliSense およびオートコンプリート機能を使用するのに役立ちます。

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

[azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) パッケージに含まれる Python の注釈を使用すると、入力と出力がご利用のメソッドにバインドされます。

## <a name="alternate-entry-point"></a>代替エントリ ポイント

*function.json* ファイル内で `scriptFile` プロパティと `entryPoint` プロパティをオプションで指定することによって、関数の既定の動作を変更できます。 たとえば、以下の _function.json_ では、ご利用の Azure 関数のエントリ ポイントとして、_main.py_ ファイル内の `customentry()` メソッドを使用するようにランタイムに指示が出されます。

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>フォルダー構造

Python 関数プロジェクトのフォルダー構造は、次の例のようになります。

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

関数アプリの構成に使用できる共有 [host.json](functions-host-json.md) ファイルがあります。 各関数には、独自のコード ファイルとバインディング構成ファイル (function.json) があります。 

共有コードは、別のフォルダーに保存する必要があります。 SharedCode フォルダー内のモジュールを参照するには、次の構文を使用します。

```
from __app__.SharedCode import myFirstHelperFunction
```

関数に対してローカルになるモジュールを参照するには、次のような相対インポート構文を使用できます。

```
from . import example
```

関数プロジェクトを Azure 内のご利用の関数アプリにデプロイする場合は、フォルダー自体ではなく、パッケージに *FunctionApp* フォルダーの内容全体を含める必要があります。

## <a name="triggers-and-inputs"></a>トリガーと入力

Azure Functions では、入力はトリガー入力と追加入力の 2 つのカテゴリに分けられます。 `function.json` ファイルではこれらは同じではありませんが、Python コードでは同じように使用されます。  トリガーの接続文字列またはシークレットと入力ソースは、ローカル実行時には `local.settings.json` ファイル内の値にマップし、Azure での実行時にはアプリケーションの設定にマップします。 

たとえば、次のコードでは 2 つの違いが示されています。

```json
// function.json
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

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

この関数が呼び出されると、HTTP 要求は `req` として関数に渡されます。 エントリは、ルート URL 内の _ID_ に基づいて Azure Blob Storage から取得され、関数の本体で `obj` として使用できるようになります。  ここで、指定されるストレージ アカウントは、`AzureWebJobsStorage`で見つかる接続文字列であり、関数アプリケーションで使用されるストレージ アカウントと同じものです。


## <a name="outputs"></a>出力

出力は、戻り値および出力パラメーターの両方で表現できます。 出力が 1 つのみの場合は、戻り値の使用をお勧めします。 出力が複数の場合は、出力パラメーターを使用する必要があります。

出力バインディングの値として関数の戻り値を使用するには、バインディングの `name` プロパティを `function.json` 内の `$return` に設定する必要があります。

複数の出力を生成するには、[`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) インターフェイスによって提供される `set()` メソッドを使用して、バインディングに値を割り当てます。 たとえば、次の関数を使用すると、キューにメッセージをプッシュすることに加え、HTTP 応答を返すこともできます。

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
| **`critical(_message_)`**   | ルート ロガー上に CRITICAL レベルのメッセージを書き込みます。  |
| **`error(_message_)`**   | ルート ロガー上に ERROR レベルのメッセージを書き込みます。    |
| **`warning(_message_)`**    | ルート ロガー上に WARNING レベルのメッセージを書き込みます。  |
| **`info(_message_)`**    | ルート ロガー上に INFO レベルのメッセージを書き込みます。  |
| **`debug(_message_)`** | ルート ロガー上に DEBUG レベルのメッセージを書き込みます。  |

ログ記録の詳細については、「[Azure Functions を監視する](functions-monitoring.md)」を参照してください。

## <a name="http-trigger-and-bindings"></a>HTTP トリガーとバインディング

HTTP トリガーは function.jon ファイルで定義されています。 バインディングの `name` は、関数の名前付きパラメーターと一致している必要があります。 前の例では、バインド名 `req` が使用されています。 このパラメーターは [HttpRequest] オブジェクトであり、[HttpResponse] オブジェクトが返されます。

[HttpRequest] オブジェクトからは、要求ヘッダー、クエリ パラメーター、ルート パラメーター、およびメッセージ本文を取得できます。 

次の例は、[Python 用の HTTP トリガー テンプレート](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)からのものです。 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

この関数では、`name` クエリ パラメーターの値は [HttpRequest] オブジェクトの `params` パラメーターから取得されます。 JSON でエンコードされたメッセージ本文は `get_json` メソッドを使用して読み取られます。 

同様に、返される [HttpResponse] オブジェクトに応答メッセージの `status_code` および `headers` を設定できます。
                                                              
## <a name="async"></a>非同期

`async def` ステートメントを使って非同期のコルーチンとして Azure 関数を記述することをお勧めします。

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

main() 関数が同期 (修飾子が付いていない) の場合、関数は `asyncio` スレッド プール内で自動的に実行されます。

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Context

実行中に関数の呼び出しコンテキストを取得するには、そのシグニチャに [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) 引数を含めます。 

例:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**コンテキスト**](/python/api/azure-functions/azure.functions.context?view=azure-python) クラスには次のメソッドが含まれています。

`function_directory`  
関数が実行されるディレクトリです。

`function_name`  
関数の名前です。

`invocation_id`  
現在の関数呼び出しの ID です。

## <a name="global-variables"></a>グローバル変数

将来の実行に対してアプリの状態が保持されることは保証されません。 ただし、Azure Functions ランタイムでは、多くの場合、同じアプリの複数の実行に対して同じプロセスが再利用されます。 高コストの計算の結果をキャッシュするには、グローバル変数として宣言します。 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>環境変数

Functions では、サービス接続文字列などの[アプリケーション設定](functions-app-settings.md)は、実行中に環境変数として公開されます。 これらの設定にアクセスするには、`import os` を宣言してから `setting = os.environ["setting-name"]` を使用します。

次の設定では、`myAppSetting` という名前のキーの[アプリケーション設定](functions-how-to-use-azure-function-app-settings.md#settings)が取得されます。

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

ローカル開発の場合、アプリケーション設定は [local.settings.json ファイルに保持されます](functions-run-local.md#local-settings-file)。  

## <a name="python-version-and-package-management"></a>Python のバージョンとパッケージの管理

現在、Azure Functions では、Python 3.6.x (公式な CPython 配布) のみがサポートされています。

Azure Functions Core Tools または Visual Studio Code を使用してローカルで開発を行う場合は、必要なパッケージの名前とバージョンを `requirements.txt` ファイルに追加し、`pip` を使用してそれらをインストールしてください。

たとえば、次の要件のファイルと pip コマンドを使用すれば、PyPI から `requests` パッケージをインストールすることができます。

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Azure への発行

発行の準備ができたら、依存関係がすべて、プロジェクト ディレクトリのルートにある *requirements.txt* ファイル内にリストされていることを確認します。 Azure Functions では、これらの依存関係を[リモート ビルド](functions-deployment-technologies.md#remote-build)できます。

発行から除外されたプロジェクト ファイルとフォルダー (仮想環境フォルダーなど) は、.funcignore ファイルに一覧表示されます。  

Azure にデプロイし、リモート ビルドを実行するには、次のコマンドを使用します。

```bash
func azure functionapp publish <app name> --build remote
```

リモート ビルドを使用しておらず、使用しているパッケージにおいてコンパイラが必要とされるが、PyPI からの manylinux 対応のホイールのインストールがサポートされていない場合、ローカルでビルドしない Azure への発行は失敗し、次のエラーが返されます。

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

必要なバイナリをローカルでビルドして構成するには、[Azure Functions Core Tools](functions-run-local.md#v2) (func) を使用することにより、ご利用のローカル マシン上に [Docker をインストール](https://docs.docker.com/install/)し、次のコマンドを実行して発行します。 `<app name>` を、Azure 内のご自分の関数アプリの名前に置き換えることを忘れないでください。 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Core Tools では、ご利用のローカル マシン上で [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) イメージをコンテナーとして実行するためにバックグラウンドで Docker が使用されます。 この環境を使用する場合、必要なモジュールがソース配布からビルドおよびインストールされてから、Azure への最終的なデプロイに備えてそれらがパッケージ化されます。

継続的デリバリー (CD) システムを使って依存関係のビルドと発行を行うには、[Azure パイプラインを使用](functions-how-to-azure-devops.md)します。 

## <a name="unit-testing"></a>単体テスト

Python で記述された関数は、標準的なテスト フレームワークを使用して、他の Python コードのようにテストできます。 ほとんどのバインドでは、`azure.functions` パッケージから適切なクラスのインスタンスを作成することにより、モック入力オブジェクトを作成できます。 [`azure.functions`](https://pypi.org/project/azure-functions/) パッケージはすぐには使用できないため、上記の「[Python のバージョンとパッケージの管理](#python-version-and-package-management)」セクションの説明に従って、`requirements.txt` ファイルを介してインストールするようにしてください。

たとえば、次に示すのは、HTTP によってトリガーされる関数のモック テストです。

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

キューによってトリガーされる関数の別の例を次に示します。

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```

## <a name="known-issues-and-faq"></a>既知の問題とよくあるご質問

既知の問題と機能に関する要望はすべて、[GitHub issues](https://github.com/Azure/azure-functions-python-worker/issues) リストを使用して追跡されます。 問題が発生してその問題が GitHub で見つからない場合は、新しい Issue を開き、その問題の詳細な説明を記載してお知らせください。

### <a name="cross-origin-resource-sharing"></a>クロス オリジン リソース共有

Azure Functions では、クロス オリジン リソース共有 (CORS) がサポートされています。 CORS は[ポータル内で](functions-how-to-use-azure-function-app-settings.md#cors)、[Azure CLI](/cli/azure/functionapp/cors) によって構成されます。 CORS の許可配信元一覧は、関数アプリ レベルで適用されます。 CORS を有効にすると、応答に `Access-Control-Allow-Origin` ヘッダーが含まれます。 詳細については、「 [クロス オリジン リソース共有](functions-how-to-use-azure-function-app-settings.md#cors)」を参照してください。

許可配信元一覧は、Python 関数アプリでは[現在サポートされていません](https://github.com/Azure/azure-functions-python-worker/issues/444)。 この制限のため、次の例に示すように、HTTP 関数で `Access-Control-Allow-Origin` ヘッダーを明示的に設定する必要があります。

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

OPTIONS HTTP メソッドをサポートするように、function.json も更新してください。

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

このメソッドは、許可配信元一覧をネゴシエートするために Chrome ブラウザーによって使用されます。 

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

* [Azure Functions パッケージ API のドキュメント](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
* [Blob Storage のバインド](functions-bindings-storage-blob.md)
* [HTTP と Webhook のバインド](functions-bindings-http-webhook.md)
* [Queue Storage のバインド](functions-bindings-storage-queue.md)
* [タイマー トリガー](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
