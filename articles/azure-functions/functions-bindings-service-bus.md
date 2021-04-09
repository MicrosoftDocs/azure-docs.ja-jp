---
title: Azure Functions における Azure Service Bus のバインド
description: Azure Functions で Azure Service Bus トリガーとバインドを送信する方法を説明します。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: b32f16d170df9963960862bc82aef1a4baf13896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104446"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions における Azure Service Bus のバインド

Azure Functions は[トリガーとバインド](./functions-triggers-bindings.md)を使用して [Azure Service Bus](https://azure.microsoft.com/services/service-bus) と統合されます。 Service Bus と統合すると、キューまたはトピック メッセージに応答して送信する関数を構築できます。

| アクション | Type |
|---------|---------|
| Service Bus キューまたはトピック メッセージが作成されたときに関数を実行する | [トリガー](./functions-bindings-service-bus-trigger.md) |
| Azure Service Bus メッセージを送信する |[出力バインド](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

> [!NOTE]
> Service Bus バインドでは現在、マネージド ID を利用した認証がサポートされていません。 代わりに、[Service Bus の共有アクセス シグネチャ](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)を利用してください。

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 4.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能]をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x アプリでは、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ バージョン 2.x が自動的に参照されます。

## <a name="next-steps"></a>次のステップ

- [Service Bus キューまたはトピック メッセージが作成されたときに関数を実行する (トリガー)](./functions-bindings-service-bus-trigger.md)
- [Azure Functions から Azure Service Bus メッセージを送信する (出力バインド)](./functions-bindings-service-bus-output.md)