---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 6fd8c3c5839d4cc897caa2dff70af87980e547eb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206724"
---
## <a name="local-settings-file"></a>ローカル設定ファイル

local.settings.json ファイルには、アプリの設定、接続文字列、およびローカルの開発ツールによって使用される設定が格納されます。 local.settings.json ファイル内の設定は、プロジェクトをローカルで実行している場合にのみ使用されます。 ローカル設定ファイルの構造は次のとおりです。

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

これらの設定は、プロジェクトをローカルで実行する場合にサポートされます。

| 設定      | 説明                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | この設定を `true` にすると、すべての値がローカル コンピューターのキーを使用して暗号化されます。 `func settings` コマンドと共に使用されます。 既定値は `false` です。 |
| **`Values`** | プロジェクトをローカルで実行するときに使用されるアプリケーション設定と接続文字列の配列です。 これらのキーと値 (文字列と文字列) のペアは、[`AzureWebJobsStorage`] など、Azure 内の関数アプリでのアプリケーション設定に対応します。 多くのトリガーおよびバインドには、[BLOB ストレージ トリガー](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)の `Connection` など、接続文字列アプリ設定を参照するプロパティがあります。 これらのプロパティでは、`Values` 配列にアプリケーション設定を定義する必要があります。 <br/>[`AzureWebJobsStorage`] は、HTTP 以外のトリガーに必要なアプリ設定です。 <br/>Functions ランタイムのバージョン 2.x 以降では、`FUNCTIONS_WORKER_RUNTIME` 設定が必要です。これは、Core Tools によってプロジェクトのために生成されます。 <br/> [Azure ストレージ エミュレーター](../articles/storage/common/storage-use-emulator.md)がローカルにインストールされ、[`AzureWebJobsStorage`] を `UseDevelopmentStorage=true` に設定すると、Core Tools ではエミュレーターが使用されます。 エミュレーターは開発中には便利ですが、デプロイする前に実際のストレージに接続してテストする必要があります。<br/> 値は、JSON オブジェクトまたは配列ではなく文字列である必要があります。 設定名には、コロン (`:`)、2 つの連続する下線 (`__`) を含めることはできません。 これらの文字はランタイムで予約されています。 <br/>ローカルで実行しているときに関数を無効にするには、`"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` をコレクションに追加します。`<FUNCTION_NAME>` は関数の名前です。 詳細については、[Azure Functions で関数を無効にする方法](../articles/azure-functions/disable-function.md#localsettingsjson)に関する記事を参照してください。  |
| **`Host`** | このセクションの設定により、ローカルでプロジェクトを実行するときの Functions ホスト プロセスをカスタマイズできます。 これらの設定は、Azure でプロジェクトを実行するときにも適用される、host.json 設定とは別のものです。 |
| **`LocalHttpPort`** | ローカルの Functions ホストの実行時に使用される既定のポートを設定します (`func host start`と`func run`)。 `--port` コマンド ライン オプションは、この設定より優先されます。 |
| **`CORS`** | [クロス オリジン リソース共有 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) で許可されるオリジンを定義します。 スペースなしのコンマ区切りのリストでオリジンを指定します。 ワイルドカード値 (\*) がサポートされており、これによって任意のオリジンからの要求を許可できます。 |
| **`CORSCredentials`** |  `true` に設定すると、`withCredentials` 要求が許可されます。 |
| **`ConnectionStrings`** | コレクション。 関数のバインディングで使用される接続文字列にこのコレクションを使用しないでください。 このコレクションは、[Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) など、構成ファイルの `ConnectionStrings` セクションから接続文字列を取得するのが一般的なフレームワークでのみ使用されます。 このオブジェクト内の接続文字列は、[System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) のプロバイダーの種類と共に、環境に追加されます。 他のアプリ設定では、このコレクション内の項目は Azure に発行されません。 ご自分の関数アプリの設定の `Connection strings` コレクションに、これらの値を明示的に追加する必要があります。 関数コードで [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) を作成する場合は、接続文字列の値を他の接続と共に、ポータル内の**アプリケーション設定**に格納する必要があります。 |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
