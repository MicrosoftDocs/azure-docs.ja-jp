---
title: Durable Functions のためのゼロダウンタイムのデプロイ
description: ゼロダウンタイムのデプロイのために Durable Functions オーケストレーションを有効にする方法について説明します。
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 707d624c47c536e00e98910a8902772703733515
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558765"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Durable Functions のためのゼロダウンタイムのデプロイ

Durable Functions の[信頼性の高い実行モデル](./durable-functions-orchestrations.md)には、オーケストレーションが決定論的であることが必要です。これにより、更新プログラムをデプロイするときに考慮する必要がある追加の課題が発生します。 アクティビティ関数のシグネチャまたはオーケストレーター ロジックに対する変更がデプロイに含まれている場合、実行中のオーケストレーション インスタンスが失敗します。 この状況は、数時間または数日間にわたる作業を表す可能性がある、長時間実行されているオーケストレーションのインスタンスの場合に特に問題があります。

このような障害は 2 とおりの方法で防止できます。 
- 実行中のオーケストレーション インスタンスがすべて完了するまでデプロイを遅らせます。
- 実行中のオーケストレーション インスタンスで既存バージョンの関数が使用されていることを確認します。 

次の表では、Durable Functions のゼロダウンタイム デプロイを実現するための 3 つの主な戦略を比較します。 

| 戦略 |  使用する場合 | 長所 | 短所 |
| -------- | ------------ | ---- | ---- |
| [バージョン管理](#versioning) |  [破壊的変更](durable-functions-versioning.md)が頻繁に発生しないアプリケーション。 | 実装が簡単。 |  メモリ内の関数アプリのサイズと関数の数が増える。<br/>コードの重複。 |
| [スロットでの状態チェック](#status-check-with-slot) | 24 時間以上続く長時間のオーケストレーションまたは頻繁に重複するオーケストレーションが存在しないシステム。 | シンプルなコード ベース。<br/>追加の関数アプリ管理が不要。 | 追加のストレージ アカウントまたはタスク ハブの管理が必要である。<br/>オーケストレーションが実行されていない時間帯が必要である。 |
| [アプリケーション ルーティング](#application-routing) | 24 時間以上続くオーケストレーションや、頻繁に重複するオーケストレーションの期間など、オーケストレーションが実行されていない期間がないシステム。 | 破壊的変更を伴うオーケストレーションが継続的に実行されている新しいバージョンのシステムを処理する。 | インテリジェントなアプリケーション ルーターが必要。<br/>サブスクリプションで許可されている関数アプリの最大数を超える可能性。 既定値は、100 です。 |

## <a name="versioning"></a>バージョン管理

関数の新しいバージョンを定義し、関数アプリでは古いバージョンのままにします。 図を見るとわかるように、関数のバージョンが名前の一部になります。 以前のバージョンの関数が保持されるため、実行中のオーケストレーション インスタンスは引き続きそれらを参照できます。 一方で、新しいオーケストレーション インスタンスに対する要求では最新バージョンが呼び出され、オーケストレーション クライアント関数はアプリ設定から参照できます。

![バージョン管理の戦略](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

この戦略では、すべての関数をコピーし、他の関数への参照を更新する必要があります。 スクリプトを記述することで簡単にできます。 次に示すのは、移行スクリプトを使用した[サンプル プロジェクト](https://github.com/TsuyoshiUshio/DurableVersioning)です。

>[!NOTE]
>この戦略では、デプロイ スロットを使用して、デプロイ時のダウンタイムが回避されます。 新しいデプロイ スロットを作成して使用する方法の詳細については、「[Azure Functions デプロイ スロット](../functions-deployment-slots.md)」を参照してください。

## <a name="status-check-with-slot"></a>スロットでの状態チェック

現在のバージョンの関数アプリが運用スロットで実行されている間に、関数アプリの新しいバージョンをステージング スロットにデプロイします。 運用スロットとステージング スロットをスワップする前に、実行中のオーケストレーション インスタンスがあるかどうかを確認します。 すべてのオーケストレーション インスタンスが完了したら、スワップを実行できます。 この戦略は、実行中のオーケストレーション インスタンスがなくなる期間を予測できる場合に有効です。 これは、オーケストレーションが長時間実行されない場合や、オーケストレーションの実行で重複が頻度に発生しない場合に、最適な方法です。

### <a name="function-app-configuration"></a>関数アプリの構成

次の手順でこのシナリオを設定します。

1. ステージングと運用のために、関数アプリに[デプロイ スロットを追加します](../functions-deployment-slots.md#add-a-slot)。

1. スロットごとに、共有ストレージ アカウントの接続文字列に [AzureWebJobsStorage アプリケーション設定](../functions-app-settings.md#azurewebjobsstorage)を設定します。 このストレージ アカウント接続文字列は Azure Functions ランタイムで使用されます。 このアカウントは Azure Functions ランタイムによって使用され、このアカウントで関数のキーを管理します。

1. スロットごとに、新しいアプリ設定を作成します (例: `DurableManagementStorage`)。 その値を異なるストレージ アカウントの接続文字列に設定します。 これらのストレージ アカウントは、[信頼性の高い実行](./durable-functions-orchestrations.md)のために Durable Functions 拡張機能によって使用されます。 スロットごとに個別のストレージ アカウントを使用します。 この設定をデプロイ スロットの設定としてマークしないでください。

1. 関数アプリの [host.json ファイルの durableTask セクション](durable-functions-bindings.md#hostjson-settings)で、ステップ 3 で作成したアプリ設定の名前として `connectionStringName` (Durable 2.x) または `azureStorageConnectionStringName` (Durable 1.x) を指定します。

次の図では、デプロイ スロットとストレージ アカウントの説明した構成を示します。 このような事前にデプロイされている可能性のあるシナリオでは、バージョン 2 の関数アプリが運用スロットで実行されている間、バージョン 1 はステージング スロットに残っています。

![デプロイ スロットとストレージ アカウント](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host.json の例

次の JSON フラグメントは、*host.json* ファイルでの接続文字列の設定の例です。

#### <a name="functions-20"></a>Functions 2.0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "storageProvider": {
        "connectionStringName": "DurableManagementStorage"
      }
    }
  }
}
```

#### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>CI/CD パイプラインの構成

関数アプリに保留中または実行中のオーケストレーション インスタンスがない場合にのみデプロイするように、CI/CD パイプラインを構成します。 Azure Pipelines を使用する場合は、次の例のように、これらの条件がチェックされる関数を作成できます。

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

次に、オーケストレーションが実行されなくなるまで待機するように、ステージング ゲートを構成します。 詳細については、「[ゲートを使用してデプロイの制御をリリースする](/azure/devops/pipelines/release/approvals/gates)」を参照してください

![デプロイ ゲート](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines では、デプロイ開始前に、関数アプリで実行中のオーケストレーション インスタンスが確認されます。

![デプロイ ゲート (実行中)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

ここで、新しいバージョンの関数アプリを、ステージング スロットにデプロイする必要があります。

![ステージング スロット](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

最後に、スロットをスワップします。 

デプロイ スロット設定としてマークされていないアプリケーション設定もスワップされるため、バージョン 2 のアプリではストレージ アカウント A への参照が維持されます。オーケストレーションの状態がストレージ アカウントで追跡されるため、バージョン 2 のアプリで実行中のすべてのオーケストレーションは、中断されることなく、新しいスロットで引き続き実行されます。

![デプロイ スロット](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

両方のスロットに同じストレージ アカウントを使用するには、タスク ハブの名前を変更します。 この場合は、ユーザーがスロットの状態とアプリの HubName の設定を管理する必要があります。 詳しくは、[Durable Functions におけるタスク ハブ](durable-functions-task-hubs.md)に関するページをご覧ください。

## <a name="application-routing"></a>アプリケーション ルーティング

この戦略は最も複雑です。 しかし、オーケストレーションの実行と実行の間に時間がない関数アプリに使用できます。

この戦略では、Durable Functions の外側に *アプリケーション ルーター* を作成する必要があります。 このルーターは Durable Functions で実装できます。 ルーターは次の役割を担います。

* 関数アプリをデプロイします。
* Durable Functions のバージョンを管理します。 
* オーケストレーション要求を関数アプリにルーティングします。

初めてオーケストレーション要求を受信したとき、ルーターでは次のタスクが実行されます。

1. Azure で新しい関数アプリを作成します。
2. 関数アプリのコードを Azure の新しい関数アプリにデプロイします。
3. オーケストレーション要求を新しいアプリに転送します。

ルーターでは、アプリのコードのどのバージョンが Azure のどの関数アプリにデプロイされているかという状態が管理されます。

![アプリケーションのルーティング (初回)](media/durable-functions-zero-downtime-deployment/application-routing.png)

ルーターでは、要求と共に送信されたバージョンに基づいて、デプロイとオーケストレーション要求が適切な関数アプリに転送されます。 パッチ バージョンは無視されます。

破壊的変更が含まれないアプリの新しいバージョンをデプロイするときは、パッチ バージョンをインクリメントできます。 ルーターでは、既存の関数アプリに対してデプロイが行われ、コードの古いバージョンと新しいバージョンに対する要求は、同じ関数アプリにルーティングされます。

![アプリケーションのルーティング (破壊的変更なし)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

破壊的変更が含まれるアプリの新しいバージョンをデプロイするときは、メジャー バージョンまたはマイナー バージョンをインクリメントできます。 次に、アプリケーション ルーターによって Azure に新しい関数アプリが作成され、それに対してデプロイが行われて、新しいバージョンのアプリに対する要求はそれにルーティングされます。 次の図では、アプリの 1.0.1 バージョンで実行中のオーケストレーションは実行し続けますが、1.1.0 バージョンに対する要求は新しい関数アプリにルーティングされます。

![アプリケーションのルーティング (破壊的変更あり)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

ルーターでは、1.0.1 バージョンでのオーケストレーションの状態が監視され、すべてのオーケストレーションが完了した後でアプリが削除されます。 

### <a name="tracking-store-settings"></a>追跡ストアの設定

各関数アプリでは、個別のスケジュール キュー (可能な場合は異なるストレージ アカウントの) を使用する必要があります。 アプリケーションのすべてのバージョンですべてのオーケストレーション インスタンスのクエリを実行したい場合は、関数アプリ間でインスタンス テーブルと履歴テーブルを共有できます。 [host.json settings](durable-functions-bindings.md#host-json) ファイルで `trackingStoreConnectionStringName` と `trackingStoreNamePrefix` の設定を構成し、すべてのテーブルで同じ値が使用されるようにすることで、テーブルを共有できます。

詳細については、「[Azure における Durable Functions でのインスタンスの管理](durable-functions-instance-management.md)」を参照してください。

![追跡ストアの設定](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Durable Functions のバージョン管理](durable-functions-versioning.md)
