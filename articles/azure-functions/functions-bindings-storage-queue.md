---
title: Azure Functions における Azure Queue storage のトリガーとバインドの概要
description: Azure Functions で Azure Queue Storage トリガーと出力バインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381479"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure Functions における Azure Queue storage のトリガーとバインドの概要

Azure Functions は、新しい Azure Queue storage メッセージが作成されたときに実行でき、関数内でキュー メッセージを書き込むことができます。

| アクション | 種類 |
|---------|---------|
| キュー ストレージのデータが変更されたときに関数を実行する | [トリガー](./functions-bindings-storage-queue-trigger.md) |
| キュー ストレージ メッセージを書き込む |[出力バインド](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 3.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

#### <a name="storage-extension-5x-and-higher"></a>ストレージ拡張機能 5.x 以降

Storage のバインド拡張機能の新しいバージョンは、[プレビュー NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2)として入手できます。 このプレビューでは、[シークレットではなく ID を使用して接続する](./functions-reference.md#configure-an-identity-based-connection)機能が導入されています。 .NET アプリケーションの場合は、バインドできる型も変更されます。これにより、 `WindowsAzure.Storage` と `Microsoft.Azure.Storage` の型が [Azure.Storage.Queues](/dotnet/api/azure.storage.queues) の新しい型に置き換えられます。

> [!NOTE]
> このプレビュー パッケージは拡張機能バンドルに含まれていないため、手動でインストールする必要があります。 .NET アプリの場合は、パッケージへの参照を追加します。 その他のすべてのアプリの種類については、[拡張機能の更新]に関する記事を参照してください。

[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x アプリでは、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ バージョン 2.x が自動的に参照されます。

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x 以降でこのバインドに使用可能なグローバル構成設定について説明します。 下の *host.json* ファイルの例には、このバインドのバージョン 2.x 以降の設定のみが含まれています。 バージョン 2.x 以降でのグローバル構成設定の詳細については、「[Azure Functions の host.json のリファレンス](functions-host-json.md)」を参照してください。

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](functions-host-json-v1.md)」を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|maxPollingInterval|00:00:01|キューのポーリングの最大間隔。 最小は 00:00:00.100 (100 ミリ秒) であり、最大 00:01:00 (1 分) まで増分されます。  Functions 2.x 以降では、データ型は `TimeSpan`であり、バージョン 1.x ではミリ秒単位です。|
|visibilityTimeout|00:00:00|メッセージの処理が失敗したときの再試行間隔。 |
|batchSize|16|Functions ランタイムが同時に取得して並列で処理するキュー メッセージの数。 処理中のメッセージの数が `newBatchThreshold` まで減少すると、ランタイムは は別のバッチを取得し、そのメッセージの処理を開始します。 そのため、1 つの関数につき同時に処理されるメッセージの最大数は、`batchSize` に `newBatchThreshold` を加えた値です。 この制限は、キューによってトリガーされる各関数に個別に適用されます。 <br><br>1 つのキューで受信した複数のメッセージの並列実行を回避したい場合は、`batchSize` を 1 に設定します。 ただし、この設定では、関数アプリが単一の仮想マシン (VM) で実行されている限り、コンカレンシーは実現しません。 この関数アプリを複数の VM にスケール アウトすると、各 VM では、キューによってトリガーされる関数ごとに 1 つのインスタンスを実行できます。<br><br>最大の `batchSize` は 32 です。 |
|maxDequeueCount|5|有害キューに移動する前に、メッセージの処理を試行する回数。|
|newBatchThreshold|batchSize/2|同時に処理されているメッセージの数がこの数まで減少すると、ランタイムは別のバッチを取得します。|
|messageEncoding|base64| この設定は、[バージョン 5.0.0 以降の拡張機能](#storage-extension-5x-and-higher)でのみ使用できます。 これは、メッセージのエンコード形式を表します。 有効値は `base64` または `none` です。|

## <a name="next-steps"></a>次のステップ

- [キュー ストレージのデータが変更されたときに関数を実行する (トリガー)](./functions-bindings-storage-queue-trigger.md)
- [キュー ストレージ メッセージを書き込む (出力バインド)](./functions-bindings-storage-queue-output.md)
