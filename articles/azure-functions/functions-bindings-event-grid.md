---
title: Azure Functions における Azure Event Grid のバインド
description: Azure Functions で Event Grid イベントを処理する方法を説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 115ff1b59ecbe3f4fdb089c2bd61da955fba3984
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104548"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Functions における Azure Event Grid のバインド

このリファレンスでは、Azure Functions で [Event Grid](../event-grid/overview.md) イベントを処理する方法を説明します。 HTTP エンドポイントで Event Grid メッセージを処理する方法の詳細については、「[HTTP エンドポイントへのイベントの受信](../event-grid/receive-events.md)」を参照してください。

Event Grid は、"*パブリッシャー*" 内で発生したイベントについてユーザーに通知する HTTP 要求を送信する Azure サービスです。 パブリッシャーは、イベントを生成するサービスまたはリソースです。 たとえば、Azure Blob Storage アカウントはパブリッシャーであり、[BLOB のアップロードまたは削除がイベント](../storage/blobs/storage-blob-event-overview.md)です。 一部の [Azure サービスには、Event Grid にイベントを発行するサポートが組み込まれています](../event-grid/overview.md#event-sources)。

イベント "*ハンドラー*" は、イベントを受信して処理します。 Azure Functions は、[Event Grid イベントを処理する組み込みサポートを備えている Azure サービス](../event-grid/overview.md#event-handlers)の 1 つです。 このリファレンスでは、Event Grid トリガーを使用して Event Grid からイベントを受信したときに関数を呼び出し、出力バインディングを使用して [Event Grid カスタム トピック](../event-grid/post-to-custom-topic.md)にイベントを送信する方法について説明します。

必要に応じて、HTTP トリガーを使用して Event Grid イベントを処理できます。「[HTTP エンドポイントへのイベントの受信](../event-grid/receive-events.md)」を参照してください。 現時点では、[CloudEvents スキーマ](../event-grid/cloudevents-schema.md#azure-functions)でイベントを配信する際に、Azure Functions アプリの Event Grid トリガーを使用することはできません。 代わりに、HTTP トリガーを使用してください。

| アクション | Type |
|---------|---------|
| Event Grid イベントがディスパッチされたときに関数を実行する | [トリガー](./functions-bindings-event-grid-trigger.md) |
| Event Grid イベントを送信する |[出力バインド](./functions-bindings-event-grid-output.md) |

このリファレンスのコードは既定の .NET Core 構文です。これは、Functions バージョン 2.x 以降で使用されます。 1\.x 構文については、[1.x 関数テンプレート](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)を参照してください。

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 2.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x アプリでは、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ バージョン 2.x が自動的に参照されます。

## <a name="next-steps"></a>次のステップ
* [Event Grid イベントがディスパッチされたときに関数を実行する](./functions-bindings-event-grid-trigger.md)
* [Event Grid イベントをディスパッチする](./functions-bindings-event-grid-trigger.md)