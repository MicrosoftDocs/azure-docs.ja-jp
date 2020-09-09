---
title: Azure Functions のベスト プラクティス
description: Azure Functions のベスト プラクティスとパターンについて説明します。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a41a5828a82d81c5e7e8749fee70cd15e17bb9d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84697692"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Azure Functions のパフォーマンスと信頼性を最適化する

この記事では、[サーバーレス](https://azure.microsoft.com/solutions/serverless/)関数アプリのパフォーマンスと信頼性を向上させるためのガイダンスを紹介しています。  

## <a name="general-best-practices"></a>一般的なベスト プラクティス

Azure Functions を使用して、サーバーレス ソリューションを構築および設計する際のベスト プラクティスを以下に示します。

### <a name="avoid-long-running-functions"></a>実行時間の長い関数を使用しない

サイズが大きく実行時間の長い関数では、予期しないタイムアウトの問題が発生する可能性があります。 特定のホスティング プランのタイムアウトの詳細については、「[Function App タイムアウト期間](functions-scale.md#timeout)」を参照してください。 

関数は、Node.js 依存関係の多さからそのサイズが大きくなることがあります。 また、依存関係をインポートすると、読み込み時間が長くなり、予期しないタイムアウトが発生する可能性もあります。 依存関係は明示的にも暗黙的にも読み込まれます。 コードによって読み込まれる単一のモジュールによって、独自の追加モジュールが読み込まれることがあります。 

可能な限り、大きな関数は、連携して高速な応答を返す、より小さな関数セットにリファクタリングしてください。 たとえば、webhook または HTTP トリガー関数では、一定の時間内に確認応答が必要になる場合があります。webhook は通常、即座に応答を必要とします。 この HTTP トリガー ペイロードは、キュー トリガー関数によって処理されるキューに渡すことができます。 このアプローチを使用すると、実際の作業を遅らせて、即座に応答を返すことができます。


### <a name="cross-function-communication"></a>関数間の通信

[Durable Functions](durable/durable-functions-overview.md) と [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、状態遷移と複数の関数間での通信を管理するように構築されています。

複数の関数と統合する際に Durable Functions も Logic Apps も使用しない場合、関数間通信にストレージ キューを使用することが推奨されます。 その主な理由は、他のストレージ オプションよりストレージ キューの方が安価であり、プロビジョニングがはるかに容易なためです。 

ストレージ キュー内では、個々のメッセージのサイズが 64 KB に制限されます。 関数間でこれよりも大きなメッセージを渡す必要がある場合は、Azure Service Bus キューを使用して、Standard レベルで最大 256 KB、Premium レベルで最大 1 MB のメッセージ サイズをサポートできます。

メッセージの処理の前にフィルター処理を必要とする場合は、Service Bus のトピックが役立ちます。

イベント ハブは、大量の通信をサポートするのに便利です。


### <a name="write-functions-to-be-stateless"></a>ステートレスな関数を記述する 

可能であれば、関数はステートレスかつべき等である必要があります。 すべての必要な状態情報をデータに関連付けます。 たとえば、処理する注文には、`state` メンバーが関連付けられている可能性があります。 関数は、それ自体がステートレスなまま、その状態に基づいて注文を処理できます。 

べき等性を持つ関数は、特にタイマー トリガーと共に使用することをお勧めします。 たとえば、1 日に 1 回必ず実行する必要のある操作がある場合は、1 日の中の任意の時間にその操作を実行して同じ結果が得られるように記述します。 特定の日に操作を実行しない場合、この関数は終了することができます。 また、前回の実行が完了しなかった場合は、次の実行では中断した場所から操作を実行する必要があります。


### <a name="write-defensive-functions"></a>防御的な関数を記述する

関数は時を選ばす例外に遭遇する可能性があることを想定する必要があります。 関数を設計する場合は、前回失敗した処理を、次回そのポイントから続行する機能を組み込みます。 次のアクションを必要とするシナリオを考えてみましょう。

1. データベース内の 10,000 行を照会する。
2. これらの行のそれぞれに対してキュー メッセージを作成して、最後まで処理を実行する。
 
システムの複雑さによっては、ダウンストリーム サービスが正しく動作しない、ネットワーキングが停止する、クォータ制限に達するなどの問題が発生する可能性があります。いずれも、時を選ばず関数に影響する可能性があります。 関数を設計する際は、このような状況を想定する必要があります。

これらのアイテムのうちの 5,000 個を処理のためにキューに入れた後で障害が発生する状況を想定した場合、どのように対応すればよいでしょうか。 完了したセット内のアイテムを追跡するようにしましょう。 そうでないと、次回同じアイテムを再び挿入することになります。 このように挿入が 2 回になると、ワークフローに深刻な影響が出ることがあります。そのため、[関数はべき等にして](functions-idempotent.md)ください。 

キュー アイテムが既に処理されている場合は、関数による操作をなしにします。

Azure Functions プラットフォームで使用するコンポーネントに対して既に提供されている防御策を活用してください。 一例として、[Azure Storage キューのトリガーとバインド](functions-bindings-storage-queue-trigger.md#poison-messages)に関するドキュメントの「**有害キュー メッセージの処理**」を参照してください。 

## <a name="scalability-best-practices"></a>スケーラビリティのベスト プラクティス

関数アプリのインスタンスのスケーリングに影響を及ぼす多数の要素があります。 詳細については、[関数のスケーリング](functions-scale.md)に関するドキュメントをご覧ください。  関数アプリの最適なスケーラビリティを確保するためのベスト プラクティスを以下に示します。

### <a name="share-and-manage-connections"></a>接続の共有と管理

可能であれば、外部リソースへの接続を再利用します。 「[Azure Functions で接続を管理する方法](./manage-connections.md)」をご覧ください。

### <a name="avoid-sharing-storage-accounts"></a>ストレージ アカウントの共有を回避する

関数アプリを作成する場合は、ストレージ アカウントに関連付ける必要があります。 ストレージ アカウント接続は、[AzureWebJobsStorage アプリケーション設定](./functions-app-settings.md#azurewebjobsstorage)の中で管理されます。 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>同じ関数アプリにテスト コードと運用環境のコードを混在させない

Function App 内の関数はリソースを共有します。 たとえば、メモリは共有されます。 運用環境で Function App を使用している場合は、テストに関連する関数およびリソースを追加しないでください。 これが原因で、運用環境のコードの実行中に予期しないオーバーヘッドが発生する可能性があります。

運用環境の Function App で読み込むものに注意してください。 メモリは、アプリ内の各関数間で平均化されます。

複数の .NET 関数で参照される共有アセンブリがある場合は、それを共通の共有フォルダーに配置します。 そうしないと、関数間で動作が異なる同じバイナリの複数のバージョンを誤ってデプロイする可能性があります。

実稼働コードでは、詳細ログを使用しないでください。パフォーマンスに悪影響を及ぼします。

### <a name="use-async-code-but-avoid-blocking-calls"></a>非同期コードを使用し、呼び出しのブロックは避ける

非同期プログラミングは、特に I/O 操作のブロックが関連している場合に、推奨されるベスト プラクティスです。

C# では、`Task` インスタンス上の `Result` プロパティを参照したり `Wait` メソッドを呼び出したりすることは、常に避けるようにします。 この手法により、スレッドが枯渇する可能性があります。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>複数のワーカー プロセスを使用する

既定では、Functions のどのホスト インスタンスでも、単一のワーカー プロセスが使用されます。 特に Python などのシングルスレッド ランタイムによってパフォーマンスを向上させるには、[FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) を使用して、ホストあたりのワーカープロセス数を増やします (最大 10 まで)。 次に、Azure Functions は、これらのワーカー間で同時関数呼び出しを均等に分散しようとします。 

FUNCTIONS_WORKER_PROCESS_COUNT は、需要に合わせてアプリケーションをスケールアウトするときに Functions によって作成される各ホストに適用されます。 

### <a name="receive-messages-in-batch-whenever-possible"></a>可能な限りメッセージをバッチで受信する

イベント ハブなどの一部のトリガーでは、1 つの呼び出しでメッセージのバッチを受信できます。  メッセージをバッチ処理すると、パフォーマンスが大幅に向上します。  [host.json のリファレンス ドキュメント](functions-host-json.md)で詳述するように、`host.json` ファイルで最大バッチ サイズを構成できます。

C# 関数の場合、型を厳密に型指定された配列に変更できます。  たとえば、メソッド シグネチャに、`EventData sensorEvent` ではなく `EventData[] sensorEvent` を指定できます。  他の言語の場合、バッチ処理を有効にするには、[こちら](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)に示すように、`function.json` のカーディナリティ プロパティを明示的に `many` に設定する必要があります。

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>コンカレンシーを適切に処理するようにホストの動作を構成する

関数アプリの `host.json` ファイルでは、ホストのランタイムとトリガーの動作を構成できます。  バッチ処理の動作に加え、多数のトリガーのコンカレンシーを管理できます。 多くの場合、これらのオプションの値を調整することで、呼び出された関数の要求に合わせて各インスタンスを適切にスケールできます。

host.json ファイルの設定は、アプリ内のすべての関数 (関数の "*1 つのインスタンス*") に適用されます。 たとえば、2 つの HTTP 関数が含まれ、[`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) 要求数が 25 に設定された関数アプリがある場合、一方の HTTP トリガーに対する要求は共有された 25 の同時要求にカウントされます。  その関数アプリが 10 インスタンスにスケールされた場合、2 つの関数は 250 個の同時要求に効果的に対応できます (10 インスタンス * インスタンスあたり 25 個の同時要求)。 

他のホスト構成オプションについては、[host.json 構成に関する記事](functions-host-json.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

詳細については、次のリソースを参照してください。

* [Azure Functions で接続を管理する方法](manage-connections.md)
* [Azure App Service のベスト プラクティス](../app-service/app-service-best-practices.md)
