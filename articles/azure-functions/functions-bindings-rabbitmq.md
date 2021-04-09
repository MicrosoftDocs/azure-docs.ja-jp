---
title: Azure Functions における Azure RabbitMQ バインド
description: Azure Functions で Azure RabbitMQ のトリガーとバインドを送信する方法について説明します。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 2a480f25821f5022295b18ca24abfd2c0fb8a50c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746526"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Azure Functions における RabbitMQ バインドの概要

> [!NOTE]
> RabbitMQ バインドは、**Premium および Dedicated** プランでのみ完全にサポートされています。 従量課金はサポートされていません。

Azure Functions は[トリガーとバインド](./functions-triggers-bindings.md)を使用して [RabbitMQ](https://www.rabbitmq.com/) と統合されます。 Azure Functions の RabbitMQ 拡張機能を使用すると、Functions で RabbitMQ API を使用してメッセージを送受信できます。

| アクション | Type |
|---------|---------|
| RabbitMQ メッセージがキューから届いたら、関数を実行する | [トリガー](./functions-bindings-rabbitmq-trigger.md) |
| RabbitMQ メッセージを送信する |[出力バインド](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

この拡張機能を使用して開発を開始するには、まず、[RabbitMQ エンドポイントを設定](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint)します。 RabbitMQ の詳細については、[作業の開始に関するページ](https://www.rabbitmq.com/getstarted.html)をご覧ください。

### <a name="functions-3x-and-higher"></a>Functions 3.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 4.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能]をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>Functions 1.x および 2.x

RabbitMQ バインド拡張機能は、Functions 1.x および 2.x ではサポートされていません。 Functions 3.x 以降を使用してください。

## <a name="next-steps"></a>次の手順

- [RabbitMQ メッセージの作成時に関数を実行する (トリガー)](./functions-bindings-rabbitmq-trigger.md)
- [Azure Functions から RabbitMQ メッセージを送信する (出力バインド)](./functions-bindings-rabbitmq-output.md)