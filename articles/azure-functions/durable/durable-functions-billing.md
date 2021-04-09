---
title: Durable Functions の課金 - Azure Functions
description: Durable Functions の内部動作と、それらが Azure Functions の課金に与える影響について説明します。
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 2ec1b080c195a47caafd0120240b5fb61ede062b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97932284"
---
# <a name="durable-functions-billing"></a>Durable Functions の課金

[Durable Functions](durable-functions-overview.md) は Azure Functions と同じように課金されます。 詳細については、[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)に関するページを参照してください。

Azure Functions の[従量課金プラン](../consumption-plan.md)でオーケストレーター関数を実行する場合は、いくつかの課金動作に注意する必要があります。 以下のセクションでは、これらの動作とその影響について詳しく説明します。

## <a name="orchestrator-function-replay-billing"></a>オーケストレーター関数のリプレイの課金

[オーケストレーター関数](durable-functions-orchestrations.md)は、オーケストレーションの有効期間中に何度もリプレイされる場合があります。 各リプレイは、Azure Functions ランタイムによって個別の関数呼び出しとして表示されます。 このため、Azure Functions の従量課金プランでは、オーケストレーター関数のリプレイごとに課金されます。 他のプランの種類では、オーケストレーター関数のリプレイには課金されません。

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>オーケストレーター関数での待機と一時停止

オーケストレーター関数で **await** (C#) または **yield** (JavaScript) を使用して非同期アクションの完了が待機されると、ランタイムではそれが特定の実行の完了と見なされます。 オーケストレーター関数の課金はその時点で停止します。 次のオーケストレーター関数がリプレイされるまで課金は再開されません。 オーケストレーター関数の待機または一時停止に費やされた時間に対して課金されることはありません。

> [!NOTE]
> 関数による他の関数の呼び出しは、なんらかのアンチパターンと見なされます。 これは、"_二重請求_" と呼ばれる問題が原因です。 関数が別の関数を直接呼び出すときは、両方とも同時に実行されます。 呼び出し元の関数が応答を待機している間、呼び出し先の関数ではコードがアクティブに実行されています。 この場合は、呼び出し元の関数が呼び出し先の関数の実行を待機する時間に対して支払いを行う必要があります。
>
> オーケストレーター関数で二重請求は生じません。 オーケストレーター関数では、アクティビティ関数 (またはサブオーケストレーション) の結果を待機している間、オーケストレーター関数の課金が停止します。

## <a name="durable-http-polling"></a>持続的な HTTP ポーリング

[HTTP の機能に関する記事](durable-functions-http-features.md)で説明されているように、オーケストレーター関数では、外部エンドポイントに対して実行時間の長い HTTP 呼び出しを行うことができます。 **CallHttpAsync** メソッド (C#) および **callHttp** メソッド (JavaScript) では、[非同期 202 パターン](durable-functions-http-features.md#http-202-handling)に従うときに、HTTP エンドポイントに対するポーリングが内部的に行われる場合があります。

現在、内部 HTTP ポーリング操作に対する直接の課金はありません。 ただし、内部ポーリングによってオーケストレーター関数でリプレイが定期的に行われる場合があります。 これらの関数の内部リプレイに対しては標準料金が請求されます。

## <a name="azure-storage-transactions"></a>Azure Storage トランザクション

Durable Functions では、状態の保持、メッセージの処理、BLOB リースによるパーティションの管理に、Azure Storage が既定で使用されします。 このストレージ アカウントは自分で所有するので、すべてのトランザクション コストは、ご利用の Azure サブスクリプションに課金されます。 Durable Functions によって使用される Azure Storage の成果物について詳しくは、[タスク ハブに関する記事](durable-functions-task-hubs.md)を参照してください。

Durable Functions アプリによって発生する実際の Azure Storage コストに寄与する要因がいくつかあります。

* 1 つの関数アプリが 1 つのタスク ハブと関連付けられ、一連の Azure Storage リソースが共有されます。 これらのリソースは、関数アプリ内のすべての持続的関数によって使用されます。 関数アプリ内の関数の実際の数は、Azure Storage トランザクションのコストには影響しません。
* 各関数アプリ インスタンスでは、指数バックオフ ポーリング アルゴリズムを使用して、ストレージ アカウント内の複数のキューに対して内部的にポーリングが行われます。 アイドル状態のアプリ インスタンスでは、アクティブなアプリよりキューをポーリングする頻度が低下し、結果としてトランザクション コストが少なくなります。 Durable Functions でのキュー ポーリング動作について詳しくは、[パフォーマンスとスケーリングに関する記事のキューのポーリングに関するセクション](durable-functions-perf-and-scale.md#queue-polling)を参照してください。
* Azure Functions の従量課金プランまたは Premium プランで実行している場合、[Azure Functions のスケール コントローラー](../event-driven-scaling.md)では、バックグラウンドのすべてのタスク ハブ キューに対して定期的にポーリングが行われます。 関数アプリが小規模から中規模の場合は、1 つのスケール コントローラー インスタンスだけがこれらのキューのポーリングを行います。 関数アプリが多数のインスタンスにスケールアウトされると、より多くのスケール コントローラー インスタンスが追加される場合があります。 これらの追加のスケール コントローラー インスタンスによって、キュートランザクションの総コストが増加する可能性があります。
* 関数アプリの各インスタンスは、BLOB リースのセットに対して競合します。 これらのインスタンスでは、Azure Blob service が定期的に呼び出されて、保持されているリースの更新または新しいリースの取得が試みられます。 BLOB リースの数は、タスク ハブの構成済みパーティション数によって決まります。 多くの場合、多数の関数アプリ インスタンスにスケールアウトすると、これらのリース操作に関連する Azure Storage トランザクション コストが増加します。

Azure Storage の価格について詳しくは、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)に関するドキュメントを参照してください。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions の価格について学習する](https://azure.microsoft.com/pricing/details/functions/)
