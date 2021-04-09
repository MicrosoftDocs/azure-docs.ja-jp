---
title: Azure Functions でのイベント ドリブン スケーリング
description: 従量課金プランと Premium プランの関数アプリのスケーリング動作について説明します。
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937511"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Azure Functions でのイベント ドリブン スケーリング

従量課金プランと Premium プランでは、Azure Functions は、Functions ホストのインスタンスを追加することによって CPU およびメモリ リソースをスケーリングします。 インスタンスの数は、関数をトリガーするイベントの数に基づいて決定されます。 

従量課金プランの Functions ホストの各インスタンスは、1.5 GB のメモリと 1 個の CPU に制限されています。  ホストのインスタンスは関数アプリ全体です。つまり、関数アプリ内のすべての関数がインスタンス内のリソースを共有し、同時にスケールされます。 同じ従量課金プランを共有する関数アプリは、独立にスケーリングされます。  Premium プランでは、プランのサイズによって、そのインスタンス上のそのプランのすべてのアプリで使用可能なメモリと CPU が決定されます。  

関数コード ファイルは、関数のメイン ストレージ アカウントの Azure Files 共有に格納されます。 関数アプリのメイン ストレージ アカウントを削除すると、関数コード ファイルは削除され、復元できません。

## <a name="runtime-scaling"></a>実行時のスケーリング

Azure Functions は "*スケール コントローラー*" と呼ばれるコンポーネントを使用して、イベント レートを監視し、スケールアウトとスケールインのどちらを実行するかを決定します。 スケール コントローラーは、トリガーの種類ごとにヒューリスティックを使用します。 たとえば、Azure Queue Storage トリガーを使用した場合、拡大縮小はキューの長さや最も古いキュー メッセージの経過時間に基づいて実施されます。

Azure Functions のスケールの単位は関数アプリです。 関数アプリがスケールアウトするときは、Azure Functions ホストの複数のインスタンスを実行するための追加リソースが割り当てられます。 反対に、コンピューティングの需要が減ると、スケール コントローラーにより、関数ホストのインスタンスが削除されます。 関数アプリ内でどの関数も実行されていない場合、インスタンスの数は最終的に 0 に "スケールイン" されます。

![イベントを監視してインスタンスを作成しているスケール コントローラー](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>コールド スタート

関数アプリが数分の間アイドル状態になると、プラットフォームによって、アプリが実行されるインスタンスの数が 0 にスケール ダウンされる可能性があります。 次の要求には、0 から 1 へのスケーリングの待機時間が追加されています。 この待機時間は、_コールド スタート_ と呼ばれます。 関数アプリに必要な依存関係の数がコールド スタート時間に影響を与える場合があります。 コールド スタートは、応答を必ず返す必要がある HTTP トリガーなどの同期操作においては問題です。 コールド スタートが関数に影響を与えている場合は、 **[常時接続]** 設定を有効にして Premium プランまたは専用プランで実行することを検討してください。   

## <a name="understanding-scaling-behaviors"></a>スケーリングの動作について

スケーリングはさまざまな要因によって異なる可能性があり、選択したトリガーと言語に基づいて異なる方法でスケールします。 スケーリング動作には、注意が必要な複雑な作業がいくつかあります。

* **最大インスタンス数:** 1 つの関数アプリがスケールアウトされるのは、最大 200 インスタンスまでのみとなります。 1 つのインスタンスで一度に複数のメッセージや要求を処理できるので、同時実行の数に上限は設定されていません。  必要な場合は[最大値を下げて](#limit-scale-out)スケーリングを制限できます。
* **新しいインスタンスの頻度:** HTTP トリガーの場合、新しいインスタンスは最大で 1 秒間に 1 回割り当てられます。 HTTP 以外のトリガーの場合、新しいインスタンスは最大で 30 秒ごとに 1 回割り当てられます。 [Premium プラン](functions-premium-plan.md)で実行しているときは、スケーリングが速くなります。
* **スケーリングの効率:** Service Bus トリガーの場合、最も効率的なスケーリングを行うためには、リソースに対して "_管理_" 権限を使用します。 "_リッスン_" 権限では、スケーリングの決定を通知するためにキューの長さを使用できないため、スケーリングが正確ではありません。 Service Bus アクセス ポリシーで権限を設定する方法の詳細については、「[共有アクセス承認ポリシー](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)」を参照してください。 イベント ハブのトリガーについては、リファレンス記事の[スケーリングのガイダンス](functions-bindings-event-hubs-trigger.md#scaling)を参照してください。 

## <a name="limit-scale-out"></a>スケールアウトを制限する

スケールアウトするために使用されるアプリのインスタンスの最大数を制限したい場合があります。これは、データベースなどのダウンストリーム コンポーネントのスループットに制限があるケースで最も一般的です。  既定では、従量課金プランの関数は最大 200 インスタンスにスケールアウトし、Premium プランの関数は最大 100 インスタンスにスケールアウトします。  特定のアプリで最大値を下げるには、`functionAppScaleLimit` 値の指定を変更します。  `functionAppScaleLimit` は、`0` または `null` (無制限の場合)、あるいは `1` とアプリの最大値の間の有効な値に設定できます。

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>スケーラブルなアプリのベスト プラクティスとパターン

関数アプリには、ホスト構成、ランタイム フットプリント、リソース効率など、そのスケーリング方法に影響を与える多くの側面があります。  詳細については、[パフォーマンスの考慮事項に関する記事のスケーラビリティのセクション](functions-best-practices.md#scalability-best-practices)をご覧ください。 関数アプリがスケールするにつれて、接続がどのように変化するかを認識する必要もあります。 詳細については、「[How to manage connections in Azure Functions](manage-connections.md)」(Azure Functions で接続を管理する方法) を参照してください。

Python と Node.js のスケールインの詳細については、「[Azure Functions の Python 開発者向けガイド - スケーリングとコンカレンシー](functions-reference-python.md#scaling-and-performance)」および [Azure Functions Node.js 開発者ガイド - スケーリングとコンカレンシー](functions-reference-node.md#scaling-and-concurrency)に関するページを参照してください。

## <a name="billing-model"></a>課金モデル

各プランの課金の詳細については、[Azure Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)を参照してください。 使用量は Function App レベルで集計され、関数コードが実行されている期間のみカウントされます。 課金の単位は、次のとおりです。

* **ギガバイト/秒 (GB/秒) 単位でのリソース使用量**。 メモリ サイズと、関数アプリ内の全関数の実行時間の組み合わせとして計算されます。 
* **実行回数**。 イベント トリガーに応じて関数が実行されるたびにカウントされます。

従量課金の請求を理解する方法についての便利なクエリと情報については、[請求に関する FAQ](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) をご覧ください。

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>次のステップ

+ [Azure Functions のホスティング オプション](functions-scale.md)

