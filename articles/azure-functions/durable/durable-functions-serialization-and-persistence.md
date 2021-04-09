---
title: Durable Functions でのデータの永続化とシリアル化 - Azure
description: Azure Functions の Durable Functions 拡張機能でデータを永続化する方法について説明します
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: ea4aaa1cdbe10e2db9cf619452558d104a2293ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449375"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Durable Functions でのデータの永続化とシリアル化 (Azure Functions)

Durable Functions は、信頼性の高い実行を実現するために、関数パラメーター、戻り値、およびその他の状態を永続的なバックエンドに自動的に永続化します。 ただし、永続的なストレージに永続化されるデータの量と頻度は、アプリケーションのパフォーマンスとストレージ トランザクションのコストに影響を与える可能性があります。 アプリケーションで格納されるデータの種類によっては、データ保有とプライバシー ポリシーも考慮する必要があります。

## <a name="azure-storage"></a>Azure Storage

既定では、Durable Functions では、指定した [Azure Storage](https://azure.microsoft.com/services/storage/) アカウントのキュー、テーブル、および BLOB にデータが永続化されます。

### <a name="queues"></a>キュー

Durable Functions では、Azure Storage キューを使用して、すべての関数の実行が確実にスケジュールされます。 これらのキュー メッセージには、メッセージが実行をスケジュールしたり呼び出し元の関数に値を返したりするために使用されているかどうかに応じて、関数の入力または出力が含まれます。 これらのキュー メッセージには、Durable Functions でルーティングやエンドツーエンドの相関関係などの内部目的で使用される追加のメタデータも含まれます。 受信したメッセージに応答して関数の実行が完了した後、そのメッセージは削除され、実行結果は Azure Storage テーブルまたは Azure Storage BLOB のいずれかに永続化される場合もあります。

1 つの [タスク ハブ](durable-functions-task-hubs.md)内では、Durable Functions でメッセージが作成され、アクティビティ関数をスケジュールするための `<taskhub>-workitem` という名前の "*作業項目*" キューと、オーケストレーターおよびエンティティ関数をスケジュールまたは再開するための `<taskhub>-control-##` という名前の 1 つ以上の *コントロール キュー* に追加されます。 コントロール キューの数は、アプリケーション用に構成されたパーティションの数と同じです。 キューとパーティションの詳細については、[パフォーマンスとスケーラビリティのドキュメント](durable-functions-perf-and-scale.md)を参照してください。

### <a name="tables"></a>テーブル

オーケストレーションでメッセージが正常に処理されると、結果として得られたアクションのレコードは、`<taskhub>History` という名前の "*履歴*" テーブルに永続化されます。 オーケストレーションの入力、出力、およびカスタムの状態データは、`<taskhub>Instances` という名前の "*インスタンス*" テーブルにも永続化されます。

### <a name="blobs"></a>BLOB

ほとんどの場合、Durable Functions ではデータを永続化するために Azure Storage Blob を使用しません。 ただし、キューとテーブルには、Durable Functions で必要なすべてのデータがストレージ行またはキュー メッセージに永続化されないようにすることができる[サイズ制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits)があります。 たとえば、シリアル化するときに、キューに永続化する必要があるデータが 45 KB を超える場合、Durable Functions では代わりにデータが圧縮されて BLOB に格納されます。 この方法で BLOB ストレージにデータを永続化する場合、Durable Function では、その BLOB への参照がテーブル行またはキュー メッセージに格納されます。 Durable Functions でデータを取得する必要がある場合は、BLOB から自動的に取り込まれます。 これらの BLOB は BLOB コンテナー `<taskhub>-largemessages` に格納されます。

> [!NOTE]
> 大きいメッセージに対する追加の圧縮と BLOB 操作の手順は、CPU および I/O 待機時間のコスト面でコストが高くなる可能性があります。 さらに、Durable Functions では、永続化されたデータをメモリに読み込む必要があり、多くの異なる関数を同時に実行する場合にこれを行うこともあります。 その結果、大きなデータ ペイロードを永続化すると、メモリの使用率も高くなる可能性があります。 メモリのオーバーヘッドを最小限に抑えるには、大きなデータ ペイロードを手動で (たとえば、BLOB ストレージに) 永続化し、代わりにそのデータへの参照を受け渡すことを検討してください。 これにより、コードで必要なときにのみデータを読み込んで、[オーケストレーター関数の再生](durable-functions-orchestrations.md#reliability)中の冗長な読み込みを回避することができます。 ただし、ペイロードをディスクに保存することは *お勧めしません*。関数はその有効期間を通じて異なる VM で実行される可能性があるため、ディスク上の状態が利用可能であることが保証されないためです。

### <a name="types-of-data-that-is-serialized-and-persisted"></a>シリアル化および永続化されるデータの種類
次に示すのは、Durable Functions の機能の使用時にシリアル化および永続化されるさまざまな種類のデータの一覧です。

- すべての ID とハンドルされない例外を含む、オーケストレーター、アクティビティ、およびエンティティ関数のすべての入力と出力
- オーケストレーター、アクティビティ、およびエンティティ関数の名前
- 外部イベントの名前とペイロード
- カスタム オーケストレーションの状態ペイロード
- オーケストレーションの終了メッセージ
- 持続的タイマー ペイロード
- 持続的な HTTP 要求と応答の URL、ヘッダー、およびペイロード
- エンティティの呼び出しとシグナル ペイロード
- エンティティ状態ペイロード

### <a name="working-with-sensitive-data"></a>機密性の高いデータの使用
Azure Storage の使用中は、すべてのデータが自動的に暗号化されます。 ただし、ストレージ アカウントへのアクセス権を持つユーザーは、暗号化されていない形式でデータを読み取ることができます。 機密データのより強力な保護が必要な場合は、最初に独自の暗号化キーを使用してデータを暗号化して、Durable Functions で事前に暗号化された形式でデータが永続化されるようにすることを検討してください。

また、.NET ユーザーは、自動暗号化を提供するカスタムのシリアル化プロバイダーを実装することもできます。 暗号化を使用したカスタムのシリアル化の例については、[この GitHub のサンプル](https://github.com/charleszipp/azure-durable-entities-encryption)を参照してください。

> [!NOTE]
> アプリケーション レベルの暗号化を実装する場合は、オーケストレーションとエンティティが無期限に存在する可能性があることに注意してください。 オーケストレーションまたはエンティティはキーのローテーション ポリシーよりも長く実行される可能性があるため、暗号化キーをローテーションするときにこれが重要になります。 キーのローテーションが行われると、データの暗号化に使用されたキーが、次にオーケストレーションまたはエンティティを実行したときに復号化できなくなる可能性があります。 このため、ユーザー暗号化は、オーケストレーションとエンティティの実行時間が比較的短い場合にのみ推奨されます。

## <a name="customizing-serialization-and-deserialization"></a>シリアル化と逆シリアル化のカスタマイズ

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>既定のシリアル化のロジック

Durable Functions では、内部で [Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) を使用して、オーケストレーションとエンティティのデータを JSON にシリアル化します。 Durable Functions で Json.NET 用に使用する既定の設定は、次のとおりです。

**入力、出力、および状態:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**例外:**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

`JsonSerializerSettings` に関する詳細なドキュメントについては、[こちら](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm)を参照してください。

## <a name="customizing-serialization-with-net-attributes"></a>.NET 属性を使用したシリアル化のカスタマイズ

データをシリアル化する場合、Json.NET では、データのシリアル化と JSON からの逆シリアル化の方法を制御するクラスおよびプロパティの[さまざまな属性](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm)を検索します。 Durable Functions API に渡されるデータ型のソース コードを所有している場合は、シリアル化と逆シリアル化をカスタマイズするために、これらの属性を型に追加することを検討してください。

## <a name="customizing-serialization-with-dependency-injection"></a>依存関係の挿入によるシリアル化のカスタマイズ

.NET を対象とする Functions 3 ランタイムで実行される関数アプリでは、[依存関係の挿入 (DI)](../functions-dotnet-dependency-injection.md) を使用して、データと例外をシリアル化する方法をカスタマイズできます。 次のサンプル コードは、DI を使用して、`IMessageSerializerSettingsFactory` および `IErrorSerializerSettingsFactory` サービス インターフェイスのカスタム実装を使用して既定の Json.NET シリアル化設定をオーバーライドする方法を示しています。

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>シリアル化と逆シリアル化のロジック

Azure Functions Node アプリケーションでは、シリアル化には [`JSON.stringify()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)、および逆シリアル化には [`JSON.Parse()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) を使用します。 ほとんどの型は、シームレスにシリアル化および逆シリアル化する必要があります。 既定のロジックが不十分な場合は、`toJSON()` オブジェクトに対してメソッドを定義すると、シリアル化ロジックがハイジャックされます。 ただし、オブジェクトの逆シリアル化にはアナログは存在しません。

シリアル化/逆シリアル化パイプラインを完全にカスタマイズする場合は、独自のコードでシリアル化と逆シリアル化を処理し、データを文字列として渡すことを検討してください。


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>シリアル化と逆シリアル化のロジック

Durable Functions でデータを確実に正しくシリアル化および逆シリアル化するには、型の注釈を使用することを強くお勧めします。 多くの組み込み型は自動的に処理されますが、一部の組み込みデータ型では、逆シリアル化中に型を保持するために型の注釈が必要です。

カスタム データ型の場合は、クラスから静的 `to_json` メソッドおよび `from_json` メソッドをエクスポートして、データ型の JSON シリアル化および逆シリアル化を定義する必要があります。

---
