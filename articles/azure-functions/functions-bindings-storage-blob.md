---
title: Azure Functions における Azure Blob Storage のトリガーとバインド
description: Azure Functions で Azure Blob Storage のトリガーとバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: eead2d6ae2860bf06f1b878c1bb1692d74c06df6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87055973"
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

| 言語                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 3.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[拡張機能の更新]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x アプリでは、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ バージョン 2.x が自動的に参照されます。

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>次のステップ

- [Blob Storage データの変更時に関数を実行する](./functions-bindings-storage-blob-trigger.md)
- [関数が実行されたときに Blob Storage データを読み取る](./functions-bindings-storage-blob-input.md)
- [関数から Blob Storage データを書き込む](./functions-bindings-storage-blob-output.md)
