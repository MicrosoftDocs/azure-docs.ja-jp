---
title: Azure Functions の Python 開発者向けリファレンス
description: Python を使用して関数を開発する方法について説明します
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3eb3b3b015f401e872a879c46ec6f8c69df5f87f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455418"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions の Python 開発者向けガイド

この記事では、Python を使用した Azure Functions の開発について紹介します。 以下の内容は、「[Azure Functions の開発者向けガイド](functions-reference.md)」を既に読んでいることを前提としています。

Python 開発者は、次のいずれかの記事にも興味があるかもしれません。

| 作業の開始 | 概念| シナリオとサンプル |
| -- | -- | -- | 
| <ul><li>[Visual Studio Code を使用した Python 関数](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[ターミナルとコマンド プロンプトを使用した Python 関数](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[開発者ガイド](functions-reference.md)</li><li>[ホスティング オプション](functions-scale.md)</li><li>[パフォーマンスに関する考慮事項&nbsp;](functions-best-practices.md)</li></ul> | <ul><li>[PyTorch を使用した画像の分類](machine-learning-pytorch.md)</li><li>[Azure Automation サンプル](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[TensorFlow を使用した機械学習](functions-machine-learning-tensorflow.md)</li><li>[Python サンプルの参照](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> [Python ベースの Azure 関数を Windows 上のローカルで開発](create-first-function-vs-code-python.md#run-the-function-locally)できますが、Python は、Azure で実行されている場合、Linux ベースのホスティング プランでのみサポートされます。 サポートされている[オペレーティング システムとランタイム](functions-scale.md#operating-systemruntime)の組み合わせの一覧を参照してください。

## <a name="programming-model"></a>プログラミング モデル

Azure Functions では、関数は入力を処理して出力を生成する Python スクリプト内のステートレスなメソッドであることが求められます。 既定で、ランタイムでは、このメソッドは `main()` と呼ばれるグローバル メソッドとして `__init__.py` ファイル内に実装されると想定されます。 また、[代替エントリ ポイントを指定する](#alternate-entry-point)こともできます。

トリガーとバインディングからのデータをメソッド属性を介して関数にバインドするには、*function.json* ファイル内で定義されている `name` プロパティを使用します。 たとえば、次の _function.json_ には、`req` という名前の HTTP 要求によってトリガーされるシンプルな関数が記述されています。

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

この定義に基づき、関数コードを含む `__init__.py` ファイルは次の例のようになります。

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

[azure.functions.*](/python/api/azure-functions/azure.functions) パッケージに含まれる Python の注釈を使用すると、入力と出力がご利用のメソッドにバインドされます。

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

Python 関数プロジェクトの推奨フォルダー構造は、次の例のようになります。

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
メイン プロジェクト フォルダー (<project_root>) には、次のファイルを含めることができます。

* *local.settings.json*:ローカルで実行するときに、アプリの設定と接続文字列を格納するために使用されます。 このファイルは Azure に公開されません。 詳細については、「[local.settings.file](functions-run-local.md#local-settings-file)」に関するページを参照してください。
* *requirements.txt*:Azure に公開するときにシステムによってインストールされる Python パッケージの一覧が含まれます。
* *host.json*:関数アプリ内のすべての関数に影響するグローバル構成オプションが含まれます。 このファイルは Azure に公開されます。 ローカルで実行する場合は、すべてのオプションがサポートされるわけではありません。 詳細については、「[host.json](functions-host-json.md)」に関するページを参照してください。
* *.vscode/* :(省略可能) ストア VSCode 構成が含まれます。 詳細については、[VSCode 設定](https://code.visualstudio.com/docs/getstarted/settings)に関するページを参照してください。
* *.venv/* :(省略可能) ローカル開発で使用される Python 仮想環境が含まれます。
* *Dockerfile*:(省略可能) [カスタム コンテナー](functions-create-function-linux-custom-image.md)でプロジェクトを発行するときに使用されます。
* *tests/* :(省略可能) 関数アプリのテスト ケースが含まれます。
* *.funcignore*:(省略可能) Azure に発行しないファイルを宣言します。 通常、このファイルには、エディター設定を無視する場合は `.vscode/`、ローカルの Python 仮想環境を無視する場合は `.venv/`、テスト ケースを無視する場合は `tests/`、ローカル アプリの設定を発行しない場合は `local.settings.json` が含まれます。

各関数には、独自のコード ファイルとバインディング構成ファイル (function.json) があります。

Azure の関数アプリにプロジェクトをデプロイする場合は、メイン プロジェクト ( *<project_root>* ) フォルダーの内容全体をパッケージに含める必要がありますが、フォルダー自体は含めないでください。つまり、`host.json` はパッケージ ルートに存在する必要があります。 テストは、他の関数と一緒に 1 つのフォルダーに保存することをお勧めします (この例では `tests/`)。 詳細については、「[単体テスト](#unit-testing)」を参照してください。

## <a name="import-behavior"></a>インポートの動作

絶対と相対の各参照の両方を使用して、関数コードにモジュールをインポートすることができます。 次のインポートは、上記のフォルダー構造に基づいて、関数ファイル *<project_root>\my\_first\_function\\_\_init\_\_.py* 内から機能します。

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  *shared_code/* フォルダーには、絶対インポート構文を使用するときに Python パッケージとしてマークするための \_\_init\_\_.py ファイルが含まれている必要があります。

次の \_\_app\_\_ インポートおよび最上位を超える相対インポートは、静的な型チェッカーでサポートされておらず、Python テスト フレームワークでサポートされていないため非推奨です。

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

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

この関数が呼び出されると、HTTP 要求は `req` として関数に渡されます。 エントリは、ルート URL 内の _ID_ に基づいて Azure Blob Storage から取得され、関数の本体で `obj` として使用できるようになります。  ここで、指定されるストレージ アカウントは、AzureWebJobsStorage アプリ設定で見つかる接続文字列であり、関数アプリケーションで使用されるストレージ アカウントと同じものです。


## <a name="outputs"></a>出力

出力は、戻り値および出力パラメーターの両方で表現できます。 出力が 1 つのみの場合は、戻り値の使用をお勧めします。 出力が複数の場合は、出力パラメーターを使用する必要があります。

出力バインディングの値として関数の戻り値を使用するには、バインディングの `name` プロパティを `function.json` 内の `$return` に設定する必要があります。

複数の出力を生成するには、[`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) インターフェイスによって提供される `set()` メソッドを使用して、バインディングに値を割り当てます。 たとえば、次の関数を使用すると、キューにメッセージをプッシュすることに加え、HTTP 応答を返すこともできます。

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

## <a name="logging"></a>ログ記録

Azure Functions ランタイム ロガーへのアクセスは、ご利用の関数アプリ内のルート [`logging`](https://docs.python.org/3/library/logging.html#module-logging) ハンドラーを介して利用できます。 このロガーは Application Insights に関連付けられているため、関数の実行中に検出される警告とエラーにフラグを設定することができます。

次の例では、HTTP トリガーを介して関数が呼び出されるときに、情報メッセージが記録されます。

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

他にもログ記録メソッドが用意されています。これにより、さまざまなトレース レベルでコンソールへの書き込みが可能になります。

| Method                 | 説明                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | ルート ロガー上に CRITICAL レベルのメッセージを書き込みます。  |
| **`error(_message_)`**   | ルート ロガー上に ERROR レベルのメッセージを書き込みます。    |
| **`warning(_message_)`**    | ルート ロガー上に WARNING レベルのメッセージを書き込みます。  |
| **`info(_message_)`**    | ルート ロガー上に INFO レベルのメッセージを書き込みます。  |
| **`debug(_message_)`** | ルート ロガー上に DEBUG レベルのメッセージを書き込みます。  |

ログ記録の詳細については、「[Azure Functions を監視する](functions-monitoring.md)」を参照してください。

## <a name="http-trigger-and-bindings"></a>HTTP トリガーとバインディング

HTTP トリガーは function.json ファイルで定義されています。 バインディングの `name` は、関数の名前付きパラメーターと一致している必要があります。
前の例では、バインド名 `req` が使用されています。 このパラメーターは [HttpRequest] オブジェクトであり、[HttpResponse] オブジェクトが返されます。

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

## <a name="scaling-and-performance"></a>スケーリングとパフォーマンス

Python 関数アプリのスケーリングとパフォーマンスのベスト プラクティスについては、[Python のスケーリングとパフォーマンスに関する記事](python-scale-performance-reference.md)をご覧ください。

## <a name="context"></a>Context

実行中に関数の呼び出しコンテキストを取得するには、そのシグニチャに [`context`](/python/api/azure-functions/azure.functions.context) 引数を含めます。

次に例を示します。

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**コンテキスト**](/python/api/azure-functions/azure.functions.context) クラスには次の文字列属性が含まれています。

`function_directory` 関数が実行されるディレクトリです。

`function_name` 関数の名前です。

`invocation_id` 現在の関数呼び出しの ID です。

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

## <a name="python-version"></a>Python バージョン

Azure Functions では次の Python バージョンがサポートされています。

| Functions バージョン | Python<sup>*</sup> バージョン |
| ----- | ----- |
| 3.x | 3.9 (プレビュー) <br/> 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>公式 CPython ディストリビューション

Azure で関数アプリを作成するときに特定の Python バージョンを要求するには、[`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) コマンドの `--runtime-version` オプションを使用します。 Functions ランタイム バージョンは `--functions-version` オプションによって設定されます。 Python バージョンは関数アプリの作成時に設定され、変更できません。

ローカルで実行する場合、ランタイムは使用可能な Python バージョンを使用します。

## <a name="package-management"></a>パッケージの管理

Azure Functions Core Tools または Visual Studio Code を使用してローカルで開発を行う場合は、必要なパッケージの名前とバージョンを `requirements.txt` ファイルに追加し、`pip` を使用してそれらをインストールしてください。

たとえば、次の要件のファイルと pip コマンドを使用すれば、PyPI から `requests` パッケージをインストールすることができます。

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Azure への発行

発行する準備ができたら、一般公開されているすべての依存関係が、プロジェクト ディレクトリのルートにある requirements.txt ファイルに一覧表示されていることを確認します。

発行から除外されたプロジェクト ファイルとフォルダー (仮想環境フォルダーなど) は、.funcignore ファイルに一覧表示されます。

Python プロジェクトを Azure に発行するために、リモート ビルド、ローカル ビルド、およびカスタム依存関係を使用したビルドの 3 つのビルド アクションがサポートされています。

Azure Pipelines を使用して依存関係をビルドし、継続的デリバリー (CD) を使用して発行することもできます。 詳細については、「[Azure DevOps を使用した継続的デリバリー](functions-how-to-azure-devops.md)」を参照してください。

### <a name="remote-build"></a>リモート ビルド

リモート ビルドを使用する場合、サーバー上に復元された依存関係とネイティブの依存関係は運用環境と一致します。 これにより、アップロードするデプロイ パッケージが小さくなります。 Windows 上で Python アプリを開発する場合は、リモート ビルドを使用します。 プロジェクトにカスタム依存関係がある場合は、[追加のインデックスの URL を使用するリモート ビルドを使用する](#remote-build-with-extra-index-url)ことができます。

依存関係は、requirements.txt ファイルの内容に基づいてリモートで取得されます。 [リモート ビルド](functions-deployment-technologies.md#remote-build) は推奨されるビルド方法です。 次の [func azure functionapp publish](functions-run-local.md#publish) コマンドを使用して Python プロジェクトを Azure に発行すると、既定では Azure Functions Core Tools によってリモート ビルドが要求されます。

```bash
func azure functionapp publish <APP_NAME>
```

`<APP_NAME>` を、Azure 内のご自分の関数アプリの名前に置き換えることを忘れないでください。

[Visual Studio Code の Azure Functions 拡張機能](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)も、既定ではリモート ビルドを要求します。

### <a name="local-build"></a>ローカル ビルド

依存関係は、requirements.txt ファイルの内容に基づいてローカルで取得されます。 次の [func azure functionapp publish](functions-run-local.md#publish) コマンドを使用してローカル ビルドとして発行することで、リモート ビルドを実行しないようにすることができます。

```command
func azure functionapp publish <APP_NAME> --build local
```

`<APP_NAME>` を、Azure 内のご自分の関数アプリの名前に置き換えることを忘れないでください。

`--build local` オプションを使用すると、プロジェクトの依存関係が requirements.txt ファイルから読み取られ、これらの依存パッケージがローカルにダウンロードされ、インストールされます。 プロジェクト ファイルと依存関係は、ローカル コンピューターから Azure にデプロイされます。 これにより、大きいサイズのデプロイ パッケージが Azure にアップロードされます。 何らかの理由で、コア ツールが requirements.txt ファイルの依存関係を取得できない場合は、発行に際して [custom dependencies]\(カスタムの依存関係\) オプションを使用する必要があります。

Windows でローカルに開発する場合は、ローカル ビルドの使用をお勧めしません。

### <a name="custom-dependencies"></a>カスタムの依存関係

[Python パッケージ インデックス](https://pypi.org/)にない依存関係がプロジェクトにある場合、2 つの方法でプロジェクトをビルドすることができます。 ビルド方法は、プロジェクトのビルド方法によって異なります。

#### <a name="remote-build-with-extra-index-url"></a>追加のインデックスの URL を使用するリモート ビルド

アクセス可能なカスタム パッケージ インデックスからパッケージを取得できる場合は、リモート ビルドを使用します。 発行する前に、必ず、`PIP_EXTRA_INDEX_URL` という名前の[アプリ設定を作成](functions-how-to-use-azure-function-app-settings.md#settings)してください。 この設定の値は、カスタム パッケージ インデックスの URL です。 この設定は、リモート ビルドで `--extra-index-url` オプションを使用して `pip install` を実行することを指示するために使用します。 詳細については、[Python の pip install に関するドキュメント](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)を参照してください。

また、基本認証の資格情報を追加のパッケージ インデックスの URL と共に使用することもできます。 詳細については、Python ドキュメントの「[基本認証の資格情報](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials)」を参照してください。

#### <a name="install-local-packages"></a>ローカル パッケージをインストールする

ツールで公開されていないパッケージをプロジェクトに使用している場合は、それを \_\_app\_\_/.python_packages ディレクトリに配置することで、アプリで使用可能にすることができます。 発行する前に次のコマンドを実行して、依存関係をローカルでインストールします。

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

カスタムの依存関係を使用する場合は、既に依存関係がプロジェクト フォルダーにインストールされているため、`--no-build` 発行オプションを使用する必要があります。

```command
func azure functionapp publish <APP_NAME> --no-build
```

`<APP_NAME>` を、Azure 内のご自分の関数アプリの名前に置き換えることを忘れないでください。

## <a name="unit-testing"></a>単体テスト

Python で記述された関数は、標準的なテスト フレームワークを使用して、他の Python コードのようにテストできます。 ほとんどのバインドでは、`azure.functions` パッケージから適切なクラスのインスタンスを作成することにより、モック入力オブジェクトを作成できます。 [`azure.functions`](https://pypi.org/project/azure-functions/) パッケージはすぐには利用できないため、上記の「[パッケージ管理](#package-management)」セクションの説明に従って、`requirements.txt` ファイルを使用してインストールしてください。

*my_second_function* を例にとると、次に示すのは、HTTP によってトリガーされる関数のモック テストです。

最初に、 *<project_root>/my_second_function/function.json* ファイルを作成し、この関数を http トリガーとして定義する必要があります。

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
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

これで、*my_second_function* と *shared_code.my_second_helper_function* を実装できます。

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

http トリガーのテスト ケースの記述を開始できます。

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

`.venv` の Python 仮想環境内で、任意の Python テストフレームワーク (例: `pip install pytest`) をインストールします。 単純に `pytest tests` を実行して、テスト結果を確認します。

## <a name="temporary-files"></a>一時ファイル

`tempfile.gettempdir()` メソッドは一時フォルダーを返します。Linux では `/tmp` です。 アプリケーションは、実行中に関数が生成および使用する一時ファイルを格納するためにこのディレクトリを使用できます。

> [!IMPORTANT]
> 一時ディレクトリに書き込まれるファイルは、呼び出しをまたいで保持されることは保証されません。 スケールアウトの間、一時ファイルはインスタンス間で共有されません。

次の例では、名前付きの一時ファイルを一時ディレクトリ (`/tmp`) に作成します。

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

テストは、プロジェクト フォルダーとは別のフォルダーに保存することをお勧めします。 これにより、アプリでテスト コードをデプロイすることを防ぐことができます。

## <a name="preinstalled-libraries"></a>プレインストール済みライブラリ

Python Functions ランタイムには、いくつかのライブラリが付属しています。

### <a name="python-standard-library"></a>Python 標準ライブラリ

Python 標準ライブラリには、各 Python ディストリビューションに同梱されている組み込み Python モジュールの一覧が含まれています。 これらのライブラリのほとんどは、ファイル I/O などのシステム機能へのアクセスに役立ちます。 Windows システムでは、これらのライブラリは Python と共にインストールされます。 Unix ベースのシステムでは、これらはパッケージ コレクションによって提供されます。

これらのライブラリの一覧の完全な詳細については、次のリンク先を参照してください。

* [Python 3.6 標準ライブラリ](https://docs.python.org/3.6/library/)
* [Python 3.7 標準ライブラリ](https://docs.python.org/3.7/library/)
* [Python 3.8 標準ライブラリ](https://docs.python.org/3.8/library/)
* [Python 3.9 標準ライブラリ](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions Python worker の依存関係

Functions Python worker は、特定のライブラリ セットを必要とします。 これらのライブラリは、関数内で使用することもできますが、Python 標準の一部ではありません。 対象の関数がこれらのライブラリのいずれかに依存している場合、Azure Functions の外部で実行したときにコードでそれらのライブラリを使用できない場合があります。 依存関係の詳細な一覧は、[setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) ファイルの **install\_requires** セクションで確認できます。

> [!NOTE]
> 関数アプリの requirements.txt に `azure-functions-worker` エントリが含まれている場合は、それを削除します。 Functions worker は Azure Functions プラットフォームによって自動的に管理され、新しい機能とバグの修正のための更新が定期的に行われます。 古いバージョンの worker を requirements.txt に手動で組み込むと、予期しない問題が発生する可能性があります。

### <a name="azure-functions-python-library"></a>Azure Functions Python ライブラリ

すべての Python worker の更新プログラムには、新しいバージョンの [Azure Functions Python ライブラリ (azure.functions)](https://github.com/Azure/azure-functions-python-library) が含まれています。 各更新プログラムには下位互換性があるため、これで簡単に Python 関数アプリを継続的に更新できるようになります。 このライブラリのリリースの一覧は、[azure-functions PyPi](https://pypi.org/project/azure-functions/#history) で確認できます。

ランタイム ライブラリのバージョンは Azure によって修正され、requirements.txt で上書きすることはできません。 requirements.txt の `azure-functions` エントリは、リンティングと顧客意識のためだけに用意されています。

対象のランタイムの Python Functions ライブラリの実際のバージョンを追跡するには、次のコードを使用します。

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>ランタイム システム ライブラリ

Python worker の Docker イメージにプレインストールされているシステム ライブラリの一覧については、次のリンク先を参照してください。

|  Functions ランタイム  | Debian のバージョン | Python のバージョン |
|------------|------------|------------|
| バージョン 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| バージョン 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3.9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="cross-origin-resource-sharing"></a>クロス オリジン リソース共有

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS は、Python 関数アプリでは完全にサポートされています。

## <a name="known-issues-and-faq"></a>既知の問題とよくあるご質問

一般的な問題のトラブルシューティング ガイドの一覧を次に示します。

* [ModuleNotFoundError および ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* ['cygrpc' をインポートできない](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

既知の問題と機能に関する要望はすべて、[GitHub issues](https://github.com/Azure/azure-functions-python-worker/issues) リストを使用して追跡されます。 問題が発生してその問題が GitHub で見つからない場合は、新しい Issue を開き、その問題の詳細な説明を記載してお知らせください。

## <a name="next-steps"></a>次のステップ

詳細については、次のリソースを参照してください。

* [Azure Functions パッケージ API のドキュメント](/python/api/azure-functions/azure.functions)
* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
* [Blob Storage のバインド](functions-bindings-storage-blob.md)
* [HTTP と Webhook のバインド](functions-bindings-http-webhook.md)
* [Queue Storage のバインド](functions-bindings-storage-queue.md)
* [タイマー トリガー](functions-bindings-timer.md)

[問題がある場合は、お知らせください。](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse