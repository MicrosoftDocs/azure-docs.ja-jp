---
title: Azure Functions のトリガーとバインド
description: Azure Functions で HTTP トリガーとバインドを使用する方法を説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 5236a3be9f8908d886274764dfc0c0da5b3565bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104480"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure Functions の HTTP トリガーとバインドの概要

Azure Functions は、HTTP 要求から呼び出して、サーバーレス API を構築し、[webhook](https://en.wikipedia.org/wiki/Webhook) に応答することができます。

| アクション | Type |
|---------|---------|
| HTTP 要求から関数を実行する | [トリガー](./functions-bindings-http-webhook-trigger.md) |
| 関数から HTTP 応答を返す |[出力バインド](./functions-bindings-http-webhook-output.md) |

この記事のコードは既定の .NET Core 構文です。これは、Functions バージョン 2.x 以降で使用されます。 1\.x 構文については、[1.x 関数テンプレート](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)を参照してください。

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 3.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x アプリでは、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ バージョン 2.x が自動的に参照されます。

## <a name="next-steps"></a>次のステップ

- [HTTP 要求から関数を実行する](./functions-bindings-http-webhook-trigger.md)
- [関数から HTTP 応答を返す](./functions-bindings-http-webhook-output.md)