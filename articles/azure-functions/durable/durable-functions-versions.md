---
title: Durable Functions のバージョンの概要 - Azure Functions
description: Durable Functions のバージョンについて説明します。
author: cgillum
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: azfuncdf
ms.openlocfilehash: c4d10bab06428295bbc8c5319bd47787d7b1fb34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763372"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions のバージョンの概要

*Durable Functions* は、サーバーレス環境でステートフル関数を記述できる、[Azure Functions](../functions-overview.md) と [Azure WebJobs](../../app-service/webjobs-create.md) の拡張機能です。 この拡張機能は状態、チェックポイント、再起動を管理します。 Durable Functions にまだ慣れていない場合は、[概要ドキュメント](durable-functions-overview.md)をご覧ください。

## <a name="new-features-in-2x"></a>2\.x の新機能

このセクションでは、バージョン 2.x で追加された Durable Functions の機能について説明します。

### <a name="durable-entities"></a>持続エンティティ

Durable Functions 2.x では、新しい[エンティティ関数](durable-functions-entities.md)の概念が導入されました。

エンティティ関数では、"*持続エンティティ*" と呼ばれる小さい状態の読み取りと更新のための操作が定義されています。 オーケストレーター関数と同様、エンティティ関数は特殊なトリガー型である "*エンティティ トリガー*" を含む関数です。 オーケストレーター関数とは異なり、エンティティ関数には特定のコードの制約はありません。 また、エンティティ関数では、制御フローを介して状態を表す暗黙的ではなく、明示的に状態が管理されます。

詳細については、「[持続エンティティ](durable-functions-entities.md)」の記事を参照してください。

### <a name="durable-http"></a>持続的 HTTP

Durable Functions 2.x では、次のことを実行できる新しい[持続的 HTTP](durable-functions-http-features.md#consuming-http-apis) 機能が導入されました。

* オーケストレーション関数から HTTP API シリーズを直接呼び出す (ドキュメントに記載されているいくつかの制限事項があります)。
* 自動クライアント側 HTTP 202 状態ポーリングを実装する。
* [Azure マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の組み込みサポート。

詳細については、[HTTP 機能](durable-functions-http-features.md#consuming-http-apis)に関する記事を参照してください。

## <a name="migrate-from-1x-to-2x"></a>1\.x から 2.x に移行する

このセクションでは、既存のバージョン 1.x の Durable Functions をバージョン 2.x に移行し、新機能を活用する方法について説明します。

### <a name="upgrade-the-extension"></a>拡張機能をアップグレードする

プロジェクトに Durable Functions バインド拡張機能の最新 2.x バージョンをインストールします。

#### <a name="javascript-python-and-powershell"></a>JavaScript、Python、PowerShell

Durable Functions 2.x は [Azure Functions 拡張機能バンドル](../functions-bindings-register.md#extension-bundles)のバージョン 2.x で使用できます。

Durable Functions での Python のサポートには Durable Functions 2.x が必要です。

プロジェクトで拡張機能バンドルのバージョンを更新するには、host.json を開き、バージョン 2.x (`[2.*, 3.0.0)`) を使用するように `extensionBundle` セクションを更新します。

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

> [!NOTE]
> 拡張機能バンドルのバージョンを変更しても Visual Studio Code に正しいテンプレートが表示されない場合は、*Developer:Reload Window* コマンド (Windows および Linux では <kbd>Ctrl + R</kbd>、macOS では <kbd>Command + R</kbd>) を実行して、ウィンドウを再度読み込みます。

#### <a name="net"></a>.NET

[Durable Functions バインド拡張機能](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)の最新 2.x バージョンを使用するように .NET プロジェクトを更新します。

詳細については、「[Azure Functions バインド拡張機能を登録する](../functions-bindings-register.md#local-csharp)」を参照してください。

### <a name="update-your-code"></a>コードを更新する

Durable Functions 2.x では、いくつかの重大な変更が導入されています。 コードを変更しない場合、Durable Functions 1.x アプリケーションは、Durable Functions 2.x と互換性がありません。 このセクションでは、バージョン 1.x の関数を 2.x にアップグレードするときに行う必要がある変更の一部を示します。

#### <a name="hostjson-schema"></a>Host.json スキーマ

Durable Functions 2.x では、新しいホストの json スキーマが使用されます。 1\.x からの主な変更点は次のとおりです。

* ストレージ固有の構成用の `"storageProvider"` (および `"azureStorage"` サブセクション)。
* 追跡とログ記録の構成用の `"tracing"`。
* イベント グリッド通知構成用の `"notifications"` (および `"eventGrid"` サブセクション)

詳細については、[Durable Functions host. json のリファレンス ドキュメント](durable-functions-bindings.md#durable-functions-2-0-host-json)を参照してください。

#### <a name="default-taskhub-name-changes"></a>既定のタスクハブ名の変更

バージョン 1.x では、タスクハブ名が host.json で指定されていない場合、既定で "DurableFunctionsHub" になっていました。 バージョン 2.x では、既定のタスク ハブ名は関数アプリの名前から派生するようになりました。 このため、2.x にアップグレードするときにタスク ハブ名を指定していない場合、コードは新しいタスク ハブで動作し、すべての実行中のオーケストレーションでは、アプリケーションがそれらを処理しなくなります。 この問題を回避するには、タスク ハブ名を v1.x の既定値 "DurableFunctionsHub" に明示的に設定するか、実行中のオーケストレーションの重大な変更を処理する方法についての詳細が記載されている、[ダウンタイムのないデプロイのガイダンス](durable-functions-zero-downtime-deployment.md)に関する記事に従うことができます。

#### <a name="public-interface-changes-net-only"></a>パブリック インターフェイスの変更 (.NET のみ)

バージョン 1.x では、Durable Functions によってサポートされるさまざまな _コンテキスト_ オブジェクトには、単体テストでの使用が意図される抽象基本クラスが含まれました。 Durable Functions 2.x の一部として、これらの抽象基本クラスはインターフェイスに置き換えられます。

次の表では、主な変更を示します。

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` または `IDurableClient` |
| `DurableOrchestrationContext` または `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` または `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

抽象基本クラスに仮想メソッドが含まれていた場合、これらの仮想メソッドは `DurableContextExtensions` で定義される拡張メソッドに置き換えられています。

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json の変更 (JavaScript および C# スクリプト)

Durable Functions 1.x では、オーケストレーション クライアントのバインドには `orchestrationClient` の `type` が使用されます。 バージョン 2.x では、代わりに `durableClient` が使用されます。

#### <a name="raise-event-changes"></a>イベント発生の変更

Durable Functions 1.x では、[イベント発生](durable-functions-external-events.md#send-events) API を呼び出して、存在しないインスタンスを指定すると、サイレント エラーが発生しました。 2\.x 以降では、存在しないオーケストレーションに対するイベント発生は例外になります。
