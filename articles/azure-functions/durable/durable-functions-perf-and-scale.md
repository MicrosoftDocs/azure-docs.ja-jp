---
title: Durable Functions のパフォーマンスとスケーリング - Azure
description: Azure Functions の Durable Functions 拡張機能の紹介。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: e6ae4cc527ae0828f530ab7f3904d2b3c64c910b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895751"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Durable Functions のパフォーマンスとスケーリング (Azure Functions)

パフォーマンスとスケーラビリティを最適化するには、[Durable Functions](durable-functions-overview.md) 固有のスケーリング特性を理解しておくことが重要です。

スケーリング動作を理解するには、基になる Azure Storage プロバイダーの詳細を理解しておく必要があります。

## <a name="history-table"></a>履歴テーブル

**履歴**テーブルは、タスク ハブ内のすべてのオーケストレーション インスタンスの履歴イベントを含む Azure Storage テーブルです。 このテーブルの名前は、*TaskHubName*History の形式になります。 インスタンスが実行されると、新しい行がこのテーブルに追加されます。 このテーブルのパーティション キーは、オーケストレーションのインスタンス ID から派生します。 ほとんどの場合、インスタンス ID はランダムであり、Azure Storage での内部パーティションの最適な分散が確保されます。

オーケストレーション インスタンスを実行する必要があるときには、履歴テーブルの適切な行がメモリに読み込まれます。 その後、これらの "*履歴イベント*" がオーケストレーター関数コードに再生され、以前にチェックポイントされた状態に戻されます。 このように実行履歴を使用した状態の再構築は、[イベント ソーシング パターン](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)の影響を受けます。

## <a name="instances-table"></a>インスタンス テーブル

**インスタンス** テーブルは、タスク ハブ内のすべてのオーケストレーション インスタンスの状態を含む別の Azure Storage テーブルです。 インスタンスが作成されると、このテーブルに新しい行が追加されます。 このテーブルのパーティション キーはオーケストレーション インスタンス ID であり、行キーは固定定数です。 オーケストレーション インスタンスごとに 1 つの行があります。

このテーブルは、[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET) API と `getStatus` (JavaScript) API の他に、[status query HTTP API](durable-functions-http-api.md#get-instance-status) からのインスタンス クエリ要求を満たすために使用されます。 最終的には、前述の**履歴**テーブルの内容との整合性が維持されます。 このように別の Azure Storage テーブルを使用したインスタンス クエリ操作への効率的な対応は、[コマンド クエリ責務分離 (CQRS) パターン](https://docs.microsoft.com/azure/architecture/patterns/cqrs)の影響を受けます。

## <a name="internal-queue-triggers"></a>内部キュー トリガー

オーケストレーター関数とアクティビティ関数は、どちらも関数アプリのタスク ハブ内の内部キューによってトリガーされます。 このようにキューを使用することによって、信頼性のある "少なくとも 1 回" のメッセージ配信保証が実現されます。 Durable Functions には、**コントロール キュー**と**作業項目キュー**という 2 種類のキューがあります。

### <a name="the-work-item-queue"></a>作業項目キュー

Durable Functions では、タスク ハブあたり 1 つの作業項目のキューがあります。 これは基本的なキューであり、Azure Functions の他の `queueTrigger` キューと同様に動作します。 このキューは、一度に 1 つのメッセージをデキューして、ステートレスな "*アクティビティ関数*" をトリガーするために使用されます。 これらの各メッセージには、アクティビティ関数の入力と追加のメタデータ (実行する関数など) が含まれています。 Durable Functions アプリケーションが複数の VM にスケールアウトされた場合、作業項目キューの作業を取得するためにすべての VM が競合します。

### <a name="control-queues"></a>コントロール キュー

Durable Functions では、タスク ハブごとに複数の "*コントロール キュー*" があります。 "*コントロール キュー*" は、単純な作業項目キューよりも高度なキューです。 コントロール キューは、ステートフルなオーケストレーター関数をトリガーするために使用されます。 オーケストレーター関数のインスタンスは、ステートフル シングルトンであるため、競合コンシューマー モデルを使用して VM 間に負荷を分散させることはできません。 代わりに、オーケストレーター メッセージが複数のコントロール キューに負荷分散されます。 この動作の詳細については、以降のセクションをご覧ください。

コントロール キューには、さまざまな種類のオーケストレーション ライフサイクル メッセージが含まれます。 たとえば、[オーケストレーター コントロール メッセージ](durable-functions-instance-management.md)、アクティビティ関数の "*応答*" メッセージ、タイマー メッセージがあります。 1 回のポーリングで 32 個のメッセージがコントロール キューからデキューされます。 これらのメッセージには、ペイロード データと、対象となるオーケストレーション インスタンスなどのメタデータが含まれています。 デキューされた複数のメッセージが同じオーケストレーション インスタンスを対象としている場合、それらはバッチとして処理されます。

### <a name="queue-polling"></a>キューのポーリング

Durable Task 拡張機能は、アイドル状態のキューのポーリングがストレージ トランザクション コストに与える影響を軽減するために、ランダムな指数バックオフ アルゴリズムを実装します。 メッセージが見つかると、このランタイムはすぐに別のメッセージがないか確認します。メッセージが見つからないときは、一定期間待ってから再試行します。 その後の試行でもキュー メッセージを取得できなかった場合は、最大待ち時間 (既定で 30 秒間) に達するまで待ち時間は増加し続けます。

最大ポーリング遅延は、[host.json ファイル](../functions-host-json.md#durabletask)内の `maxQueuePollingInterval` プロパティを使用して構成できます。 この値を高く設定するほど、メッセージ処理の待機時間が長くなる可能性があります。 長い処理時間が予期されるのは、非アクティブ期間の後のみになります。 低い値に設定すると、ストレージ トランザクションの増加により、ストレージ コストが上昇する可能性があります。

> [!NOTE]
> Azure Functions Consumption プランおよび Premium プランで実行しているとき、[Azure Functions Scale Controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) は、それぞれのコントロールと作業項目キューを 10 秒に 1 回ポーリングします。 この追加のポーリングは、関数アプリをアクティブにしてスケーリングの決定を行うタイミングを判別するために必要です。 この記事の執筆時点では、この 10 秒の間隔は定数であり、構成することはできません。

## <a name="storage-account-selection"></a>ストレージ アカウントの選択

Durable Functions で使用されるキュー、テーブル、BLOB は、構成済みの Azure Storage アカウントに作成されます。 使用するアカウントは、**host.json** ファイルの `durableTask/azureStorageConnectionStringName` 設定を使用して指定できます。

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

アカウントが指定されていない場合、既定の `AzureWebJobsStorage` ストレージ アカウントが使用されます。 ただし、パフォーマンスが重視されるワークロードの場合、既定以外のストレージ アカウントを構成することをお勧めします。 Durable Functions では Azure Storage を頻繁に使用するので、専用のストレージ アカウントを使用することで、Durable Functions によるストレージの使用を、Azure Functions ホストによる内部的な使用から分離します。

## <a name="orchestrator-scale-out"></a>オーケストレーターのスケールアウト

アクティビティ関数はステートレスであり、VM の追加によって自動的にスケールアウトされます。 一方、オーケストレーター関数は、1 つまたは複数のコントロール キューに*パーティション化*されます。 コントロール キューの数は、**host.json** ファイルで定義されています。 次の host.json スニペットの例では、`durableTask/partitionCount` プロパティを `3` に設定しています。

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

タスク ハブは、1 ～ 16 個のパーティションで構成できます。 パーティション数が指定されていない場合、既定値は **4** です。

複数の関数ホスト インスタンスにスケールアウトする (通常は異なる VM で実行されます) と、各インスタンスは、いずれかのコントロール キューのロックを取得します。 これらのロックは、内部的に Blob Storage リースとして実装され、オーケストレーション インスタンスが確実に一度に 1 つのホスト インスタンスでのみ実行されるようにします。 タスク ハブが 3 つのコントロール キューで構成されている場合、オーケストレーション インスタンスを 3 台の VM に負荷分散できます。 VM を追加することで、アクティビティ関数を実行するための容量を増やすことができます。

次の図は、Azure Functions ホストがスケールアウトされた環境で ストレージ エンティティと対話する方法を示しています。

![スケール図](./media/durable-functions-perf-and-scale/scale-diagram.png)

前の図に示すように、作業項目キューのメッセージを取得するためにすべての VM が競合します。 ただし、コントロール キューからメッセージを取得できるのは 3 台の VM のみであり、各 VM が 1 つのコントロール キューをロックします。

オーケストレーション インスタンスは、すべてのコントロール キュー インスタンスに配分されます。 配分は、オーケストレーションのインスタンス ID をハッシュすることによって実行されます。 インスタンス ID は既定ではランダムな GUID であり、インスタンスはすべてのコントロール キューに均等に配分されます。

一般に、オーケストレーター関数は軽量であることを目的としているので、多くの処理能力を必要としません。 そのため、スループットを向上させるためにコントロール キューの多数のパーティションを作成する必要はありません。 高負荷の処理の大半をステートレスなアクティビティ関数で実行することで、無限にスケールアウトできます。

## <a name="auto-scale"></a>自動スケール

従量課金プランで実行されるすべての Azure Functions と同様に、Durable Functions では、[Azure Functions のスケール コントローラー](../functions-scale.md#runtime-scaling)による自動スケールをサポートしています。 スケール コントローラーは、_peek_ コマンドを定期的に発行してすべてのキューの待ち時間を監視します。 スケール コントローラーは、ピークされたメッセージの待ち時間に基づいて、VM を追加するか削除するかを決定します。

コントロール キューのメッセージ待ち時間が長すぎると判断された場合、メッセージ待ち時間が許容レベルまで低減されるか、コントロール キューのパーティション数に達するまで、VM インスタンスが追加されます。 同様に、作業項目キューの待ち時間が長い場合は、パーティション数に関係なく、VM インスタンスが継続的に追加されます。

## <a name="thread-usage"></a>スレッドの使用

オーケストレーター関数は、実行が多数の再生で決定論的であることを保証するために 1 つのスレッドで実行されます。 このシングル スレッド実行のため、オーケストレーター関数のスレッドでは、CPU 集約型タスクや I/O を実行したり、何らかの理由でブロックしたりしないことが重要です。 I/O、ブロック、または複数のスレッドが必要になる可能性がある作業は、アクティビティ関数に移動する必要があります。

アクティビティ関数は、通常のキューによってトリガーされる関数とまったく同じように動作します。 アクティビティ関数では、I/O を安全に実行し、CPU 集約型操作を実行できます。また、複数のスレッドを使用することもできます。 アクティビティ トリガーはステートレスであるため、無制限の VM に対して自由にスケールアウトできます。

## <a name="concurrency-throttles"></a>コンカレンシーのスロットル

Azure Functions では、1 つのアプリ インスタンス内での複数の関数の同時実行をサポートしています。 この同時実行により、並列処理を増やすことが可能になり、標準的なアプリで経時的に発生する "コールド スタート" の回数を最小限に抑えることができます。 ただし、コンカレンシーが高いと、VM あたりのメモリ使用量が増える可能性があります。 関数アプリのニーズによっては、高負荷の状況でメモリ不足になる可能性を防ぐために、インスタンスごとのコンカレンシーを調整することが必要になる場合があります。

アクティビティ関数とオーケストレーター関数のコンカレンシーの制限は、どちらも **host.json** ファイルで構成できます。 該当する設定は、それぞれ `durableTask/maxConcurrentActivityFunctions`、`durableTask/maxConcurrentOrchestratorFunctions` です。

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

上記の例では、1 台の VM 上で最大 10 個のオーケストレーター関数と 10 個のアクティビティ関数を同時に実行できます。 最大数が指定されていない場合、アクティビティ関数とオーケストレーター関数の同時実行数の上限は、VM のコア数の 10 倍に設定されます。

> [!NOTE]
> これらの設定は、1 台の VM のメモリ使用量と CPU 使用率を管理するのに役立ちます。 ただし、複数の VM にスケールアウトした場合、VM ごとに独自の一連の制限が適用されます。 これらの設定を使用して、グローバル レベルでコンカレンシーを制御することはできません。

## <a name="orchestrator-function-replay"></a>オーケストレーター関数の再生

前述のように、オーケストレーター関数は**履歴**テーブルの内容を使用して再生されます。 既定では、メッセージのバッチがコントロール キューからデキューされるたびに、オーケストレーター関数コードが再生されます。

このアグレッシブな再生動作を無効にするには、**延長セッション**を有効にします。 延長セッションを有効にすると、オーケストレーター関数インスタンスがメモリに保持される期間が長くなり、フル再生なしで新しいメッセージを処理できます。 延長セッションを有効にするには、**host.json** ファイルで `durableTask/extendedSessionsEnabled` を `true` に設定します。 `durableTask/extendedSessionIdleTimeoutInSeconds` 設定は、アイドル セッションがメモリに保持される期間を制御するために使用します。

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

一般に、延長セッションを有効にすると、Azure ストレージ アカウントに対する I/O が減少し、全体的なスループットが向上します。

ただし、この機能の潜在的な欠点の 1 つは、アイドル状態のオーケストレーター関数インスタンスがメモリに保持される期間が長くなることです。 注意すべき 2 つの影響は次のとおりです。

1. 関数アプリのメモリ使用量が全体的に増加します。
2. 実行時間の短いオーケストレーター関数の同時実行数が多い場合、スループットが全体的に低下します。

たとえば、`durableTask/extendedSessionIdleTimeoutInSeconds` を 30 秒に設定した場合、実行時間の短いオーケストレーター関数の 1 回の実行時間が 1 秒未満であっても、30 秒間メモリが占有されることになります。 また、前述の `durableTask/maxConcurrentOrchestratorFunctions` クォータにもカウントされるので、他のオーケストレーター関数の実行を妨げる可能性があります。

> [!NOTE]
> これらの設定は、オーケストレーター関数が十分に開発され、テストされた後にのみ使用してください。 既定のアグレッシブな再生動作は、開発時にオーケストレーター関数のべき等エラーを検出するのに役立ちます。

## <a name="performance-targets"></a>パフォーマンスの目標

運用アプリケーションに Durable Functions を使用する場合は、計画プロセスの早期にパフォーマンス要件を検討することが重要です。 このセクションでは、一部の基本的な使用シナリオと予想される最大スループットの数値について説明します。

* **アクティビティの順次実行**: このシナリオでは、一連のアクティビティ関数を順次実行するオーケストレーター関数を記述します。 これは、[関数チェーン](durable-functions-sequence.md)のサンプルに最も似ています。
* **アクティビティの並列実行**: このシナリオでは、[ファンアウト/ファンイン](durable-functions-cloud-backup.md) パターンを使用して多数のアクティビティ関数を並列実行するオーケストレーター関数を記述します。
* **並列応答処理**: このシナリオは、[ファンアウト/ファンイン](durable-functions-cloud-backup.md) パターンの後半部分です。 ファンインのパフォーマンスが重視されます。 ファンアウトとは異なり、ファンインは 1 つのオーケストレーター関数インスタンスによって実行されるため、ファンインを実行できる VM は 1 台だけであることに注意してください。
* **外部イベント処理**: このシナリオは、一度に 1 つの[外部イベント](durable-functions-external-events.md)を待機する単一のオーケストレーター関数インスタンスを表します。

> [!TIP]
> ファンアウトとは異なり、ファンイン操作は 1 台の VM に制限されます。 アプリケーションでファンアウト/ファンイン パターンを使用しており、ファンインのパフォーマンスを懸念している場合は、アクティビティ関数のファンアウトを複数の[サブオーケストレーション](durable-functions-sub-orchestrations.md)に分けることを検討してください。

次の表に、前述のシナリオで予想される "*最大*" スループットの数値を示します。 "インスタンス" は、Azure App Service の単一のサイズの小さい ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) VM で実行されるオーケストレーター関数の単一のインスタンスを指します。 どの場合も、[延長セッション](#orchestrator-function-replay)が有効になっていることを前提としています。 実際の結果は、関数コードで実行される CPU または I/O の処理によって異なる可能性があります。

| シナリオ | 最大スループット |
|-|-|
| アクティビティの順次実行 | インスタンスあたり、5 アクティビティ/秒 |
| アクティビティの並列実行 (ファンアウト) | インスタンスあたり、100 アクティビティ/秒 |
| 並列応答処理 (ファンイン) | インスタンスあたり、150 応答/秒 |
| 外部イベント処理 | インスタンスあたり、50 イベント/秒 |

> [!NOTE]
> これらの数値は、Durable Functions 拡張機能の v1.4.0 (GA) リリース時点のものです。 機能が成熟し、最適化が行われるにつれて、これらの数値は経時的に変わる可能性があります。

予想されるスループットの数値が得られず、CPU 使用率とメモリ使用量が正常と思われる場合は、原因が[ストレージ アカウントの正常性](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)に関係していないかどうかを確認してください。 Durable Functions 拡張機能は Azure ストレージ アカウントに大きな負荷をかける可能性があり、負荷が非常に高くなると、ストレージ アカウントのスロットルが発生する場合があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [C# で最初の永続関数を作成する](durable-functions-create-first-csharp.md)
