---
title: Python 関数に Azure Storage キュー バインドを追加する
description: Azure CLI と Functions Core Tools を使用して、Python 関数に Azure Storage キュー出力バインドを追加する方法について説明します。
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
ms.openlocfilehash: c2565a5549cbca08b987883e5905f09070b5ab2c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443197"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Python 関数に Azure Storage キュー バインドを追加する

Azure Functions を使用すると、独自の統合コードを記述しなくても、Azure サービスやその他のリソースを関数に接続できます。 これらの*バインド*は、入力と出力の両方を表し、関数定義内で宣言されます。 バインドからのデータは、パラメーターとして関数に提供されます。 トリガーは、特殊な種類の入力バインドです。 関数はトリガーを 1 つしか持てませんが、複数の入力および出力バインドを持つことができます。 詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

この記事では、[前のクイック スタートの記事](functions-create-first-function-python.md)で作成した関数を Azure Storage キューと統合する方法を説明します。 この関数に追加する出力バインドは、HTTP 要求のデータをキュー内のメッセージに書き込みます。 

ほとんどのバインドでは、バインドされているサービスにアクセスするために関数が使用する、保存されている接続文字列が必要です。 作業を簡単にするために、関数アプリで作成したストレージ アカウントを使用します。 このアカウントへの接続は、既に `AzureWebJobsStorage` という名前のアプリ設定に保存されています。  

## <a name="prerequisites"></a>前提条件

この記事の作業を始める前に、[Python クイック スタートのパート 1](functions-create-first-function-python.md) の手順を完了しておきます。

## <a name="download-the-function-app-settings"></a>関数アプリの設定をダウンロードする

前のクイックスタートの記事では、必要なストレージ アカウントと、Azure での関数アプリを作成しました。 このアカウントの接続文字列は、Azure のアプリ設定に安全に格納されています。 この記事では、同じアカウントのストレージ キューにメッセージを書き込みます。 関数をローカルで実行しているときにストレージ アカウントに接続するには、アプリ設定を local.settings.json ファイルにダウンロードする必要があります。 次の Azure Functions Core Tools コマンドを実行して、設定を local.settings.json にダウンロードし、`<APP_NAME>` を前の記事の関数アプリの名前に置き換えます。

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Azure アカウントへのサインインを求められる場合があります。

> [!IMPORTANT]  
> local.settings.json ファイルは、機密情報が含まれているため、決して公開されず、ソース管理から除外される必要があります。

ストレージ アカウントの接続文字列である、値 `AzureWebJobsStorage` が必要になります。 この接続を使用して、出力バインドが期待どおりに動作することを確認します。

## <a name="enable-extension-bundles"></a>拡張バンドルを有効にする

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

これで、プロジェクトに Storage 出力バインディングを追加できるようになります。

## <a name="add-an-output-binding"></a>出力バインディングを追加する

Functions では、各種のバインドで、`direction`、`type`、および固有の `name` が function.json ファイル内で定義される必要があります。 バインドの種類によっては、追加のプロパティが必要になることもあります。 [キュー出力構成](functions-bindings-storage-queue.md#output---configuration)では、Azure Storage キュー バインドに必要なフィールドについて説明されています。

バインドを作成するには、バインド構成オブジェクトを `function.json` ファイルに追加します。 HttpTrigger フォルダー内の function.json ファイルを編集して、以下のプロパティを持つオブジェクトを `bindings` 配列に追加します。

| プロパティ | 値 | 説明 |
| -------- | ----- | ----------- |
| **`name`** | `msg` | コードで参照されているバインド パラメーターを識別する名前。 |
| **`type`** | `queue` | バインドは Azure Storage キュー バインドです。 |
| **`direction`** | `out` | バインドは出力バインドです。 |
| **`queueName`** | `outqueue` | バインドが書き込むキューの名前。 *queueName* が存在しない場合は、バインドによって最初に使用されるときに作成されます。 |
| **`connection`** | `AzureWebJobsStorage` | ストレージ アカウントの接続文字列を含むアプリ設定の名前。 `AzureWebJobsStorage` 設定には、関数アプリで作成したストレージ アカウントの接続文字列が含まれています。 |

function.json ファイルは、次の例のようになっているはずです。

```json
{
  "scriptFile": "__init__.py",
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
    },
  {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

## <a name="add-code-that-uses-the-output-binding"></a>出力バインディングを使用するコードを追加する

構成が済んだら、バインドの `name` を関数シグネチャのメソッド属性として使用して、バインドにアクセスできるようになります。 次の例では、`msg` は [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) のインスタンスです。

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

出力バインドを使用すると、認証、キュー参照の取得、またはデータの書き込みに、Azure Storage SDK のコードを使用する必要がなくなります。 Functions ランタイムおよびキューの出力バインドが、ユーザーに代わってこれらのタスクを処理します。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

以前と同様に、次のコマンドを実行して、Functions ランタイムをローカルで起動します。

```bash
func host start
```

> [!NOTE]  
> 前の記事では host.json で拡張バンドルを有効にしていたため、スタートアップ時に[ストレージ バインド拡張機能](functions-bindings-storage-blob.md#packages---functions-2x)が他の Microsoft バインディング拡張機能と共に自動的にダウンロードされ、インストールされました。

ランタイム出力から `HttpTrigger` 関数の URL をコピーして、それをブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<yourname>` を追加して、要求を実行します。 前の記事のときと同じ応答がブラウザーに表示されるはずです。

今回は、出力バインドはさらにストレージ アカウントに `outqueue` という名前のキューを作成し、この同じ文字列のメッセージを追加します。

次に、Azure CLI を使用して新しいキューを表示し、メッセージが追加されたことを確認します。 また、[Microsoft Azure Storage Explorer][Azure Storage Explorer] または [Azure portal](https://portal.azure.com) を使用してキューを表示することもできます。

### <a name="set-the-storage-account-connection"></a>ストレージ アカウントの接続を設定する

local.settings.json ファイルを開き、ストレージ アカウントの接続文字列である `AzureWebJobsStorage` の値をコピーします。 次の Bash コマンドを使用して、`AZURE_STORAGE_CONNECTION_STRING` 環境変数に接続文字列を設定します。

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

`AZURE_STORAGE_CONNECTION_STRING` 環境変数に接続文字列を設定すると、毎回認証情報を指定しなくても、ストレージ アカウントにアクセスできます。

### <a name="query-the-storage-queue"></a>ストレージ キューに対するクエリを実行する

次の例のように、[`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) コマンドを使用して、アカウントのストレージ キューを表示できます。

```azurecli-interactive
az storage queue list --output tsv
```

このコマンドの出力には、`outqueue` という名前のキューが含まれています。これは、関数の実行時に作成されたキューです。

次に、[`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) コマンドを使用して、このキュー内のメッセージを表示します (以下の例を参照)。

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

返される文字列は、関数をテストするために送信したメッセージと同じものであるはずです。

> [!NOTE]  
> 前の例では、返される文字列を base64 からデコードしています。 これは、Queue ストレージ バインドが [base64 文字列](functions-bindings-storage-queue.md#encoding)として Azure Storage との間で書き込みおよび読み取りを行うためです。

ここで、更新された関数アプリを Azure に再発行します。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

デプロイした関数をテストするために、ここでも cURL またはブラウザーを使用できます。 次の例のように、前の手順と同様に、クエリ文字列 `&name=<yourname>` を URL に追加します。

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

[ストレージ キュー メッセージ](#query-the-storage-queue)を調べて、出力バインドがキューに新しいメッセージを再度生成することを確認できます。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>次の手順

HTTP によってトリガーされる関数を、ストレージ キューにデータを書き込むように更新しました。 Python を使用して Azure Functions を開発する方法の詳細については、「[Azure Functions の Python 開発者向けガイド](functions-reference-python.md)」と、[Azure Functions のトリガーとバインド](functions-triggers-bindings.md)に関するページを参照してください。

次に、関数アプリに対して Application Insights の監視を有効にする必要があります。

> [!div class="nextstepaction"]
> [Application Insights との統合を有効にする](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/