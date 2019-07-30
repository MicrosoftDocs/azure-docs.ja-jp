---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455161"
---
## <a name="local-settings-file"></a>ローカル設定ファイル

local.settings.json ファイルには、アプリの設定、接続文字列、およびローカルの開発ツールによって使用される設定が格納されます。 local.settings.json ファイル内の設定は、ローカルで実行している場合にのみ使用されます。 ローカル設定ファイルの構造は次のとおりです。

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
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

ローカルでの実行時は、次の設定がサポートされています。

| Setting      | 説明                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | `true` に設定すると、すべての値がローカル コンピューターのキーを使用して暗号化されます。 `func settings` コマンドと共に使用されます。 既定値は `false` です。 |
| **`Values`** | ローカルで実行するときに使用されるアプリケーション設定と接続文字列の配列です。 これらのキーと値 (文字列と文字列) のペアは、[`AzureWebJobsStorage`] など、Azure 内の関数アプリでのアプリケーション設定に対応します。 多くのトリガーおよびバインドには、[Blob Storage トリガー](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration)の `Connection` など、接続文字列アプリ設定を参照するプロパティがあります。 このようなプロパティでは、`Values` 配列にアプリケーション設定を定義する必要があります。 <br/>[`AzureWebJobsStorage`] は、HTTP 以外のトリガーに必要なアプリ設定です。 <br/>Functions ランタイムのバージョン 2.x には、[`FUNCTIONS_WORKER_RUNTIME`] 設定が必要です。これは、Core Tools によってご自分のプロジェクトのために生成されます。 <br/> [Azure ストレージ エミュレーター](../articles/storage/common/storage-use-emulator.md)がローカルにインストールされている場合は、[`AzureWebJobsStorage`] を `UseDevelopmentStorage=true` に設定できます。Core Tools はエミュレーターを使用します。 これは開発中には便利ですが、展開する前に実際のストレージに接続してテストする必要があります。<br/> 値は、JSON オブジェクトまたは配列ではなく文字列である必要があります。 設定名にコロン (`:`) または二重下線 (`__`) を含めることはできません。これらはランタイムによって予約されています。  |
| **`Host`** | このセクションの設定により、ローカルで実行時の Functions ホスト プロセスをカスタマイズできます。 これらは、Azure での実行時にも適用される host.json 設定とは別のものです。 |
| **`LocalHttpPort`** | ローカルの Functions ホストの実行時に使用される既定のポートを設定します (`func host start`と`func run`)。 `--port` コマンド ライン オプションは、この値に優先します。 |
| **`CORS`** | [クロス オリジン リソース共有 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) で許可されるオリジンを定義します。 スペースなしのコンマ区切りのリストでオリジンを指定します。 ワイルドカード値 (\*) がサポートされており、これによって任意のオリジンからの要求を許可できます。 |
| **`CORSCredentials`** |  `withCredentials` 要求を許可する場合は true に設定します。 |
| **`ConnectionStrings`** | 関数のバインディングで使用される接続文字列にこのコレクションを使用しないでください。 このコレクションは、[Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) など、構成ファイルの `ConnectionStrings` セクションから接続文字列を取得するのが一般的なフレームワークでのみ使用されます。 このオブジェクト内の接続文字列は、[System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) のプロバイダーの種類と共に、環境に追加されます。 他のアプリ設定では、このコレクション内の項目は Azure に発行されません。 ご自分の関数アプリの設定の `Connection strings` コレクションに、これらの値を明示的に追加する必要があります。 関数のコードで [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) を作成する場合は、他の接続と共に、接続文字列の値をポータル内の**アプリケーションの設定**に格納する必要があります。 |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
