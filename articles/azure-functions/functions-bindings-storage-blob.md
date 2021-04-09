---
title: Azure Functions における Azure Blob Storage のトリガーとバインド
description: Azure Functions で Azure Blob Storage のトリガーとバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381530"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Functions における Azure Blob Storage のバインドの概要

Azure Functions は[トリガーとバインド](./functions-triggers-bindings.md)を使用して [Azure Storage](../storage/index.yml) と統合されます。 Blob Storage との統合により、BLOB データの変更に対応し、値の読み取りと書き込も行う関数を作成することができます。

| アクション | 種類 |
|---------|---------|
| Blob Storage データが変更されたときに関数を実行する | [トリガー](./functions-bindings-storage-blob-trigger.md) |
| 関数で Blob Storage データを読み取る | [入力バインド](./functions-bindings-storage-blob-input.md) |
| 関数で Blob Storage データを書き込めるようにする |[出力バインド](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 3.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

#### <a name="storage-extension-5x-and-higher"></a>ストレージ拡張機能 5.x 以降

Storage のバインド拡張機能の新しいバージョンは、[プレビュー NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2)として入手できます。 このプレビューでは、[シークレットではなく ID を使用して接続する](./functions-reference.md#configure-an-identity-based-connection)機能が導入されています。 .NET アプリケーションの場合は、バインドできる型も変更されます。これにより、`WindowsAzure.Storage` と `Microsoft.Azure.Storage` の型が [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs) の新しい型に置き換えられます。

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

## <a name="hostjson-settings"></a>host.json 設定

> [!NOTE]
> このセクションは、5.0.0 より前のバージョンの拡張機能を使用する場合には適用されません。 それらのバージョンには、BLOB のグローバル構成設定はありません。

このセクションでは、[バージョン 5.0.0 以降の拡張機能](#storage-extension-5x-and-higher)を使用する場合に、このバインディングで使用できるグローバル構成設定について説明します。 下の *host.json* ファイルの例には、このバインドのバージョン 2.x 以降の設定のみが含まれています。 バージョン 2.x 以降の Functions でのグローバル構成設定の詳細については、「[Azure Functions の host.json のリファレンス](functions-host-json.md)」を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (使用可能なコアの数)|BLOB によってトリガーされる関数ごとに許可される、同時呼び出しの整数の値。 許容される最小値は 1 です。|

## <a name="next-steps"></a>次のステップ

- [Blob Storage データの変更時に関数を実行する](./functions-bindings-storage-blob-trigger.md)
- [関数が実行されたときに Blob Storage データを読み取る](./functions-bindings-storage-blob-input.md)
- [関数から Blob Storage データを書き込む](./functions-bindings-storage-blob-output.md)
