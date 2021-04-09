---
title: Azure Service Fabric Mesh アプリでの監視と診断
description: Azure 上の Service Fabric Mesh にあるアプリケーションの監視および診断について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02de8ea5dd5c53192d2b8c7beba8bc36143beac6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626996"
---
# <a name="monitoring-and-diagnostics"></a>監視と診断

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)に関する記事を参照してください。

Azure Service Fabric Mesh は、仮想マシン、ストレージ、ネットワークを管理することなく開発者がマイクロサービス アプリケーションをデプロイできるフル マネージド サービスです。 Service Fabric Mesh の監視と診断は、主に次の 3 種類の診断データに分類されます。

- アプリケーション ログ - これらは、アプリケーションをインストルメント化した方法に基づいて、コンテナー化されたアプリケーションからのログと定義されます (例: Docker のログ)。
- プラットフォーム イベント - 現時点では、コンテナーのアクティブ化、非アクティブ化、および終了などのコンテナーの操作に関連するメッシュ プラットフォームからのイベント。
- コンテナーのメトリック - コンテナーのリソース使用率とパフォーマンス メトリック (docker の統計情報)。

この記事では、使用可能な最新のプレビュー バージョンの監視と診断のオプションについて説明します。

## <a name="application-logs"></a>アプリケーション ログ

デプロイされたコンテナーから、あるいはコンテナーごとに、Docker ログを表示できます。 Service Fabric Mesh アプリケーション モデルでは、各コンテナーは、アプリケーション内のコード パッケージです。 コード パッケージに関連付けられているログを表示するには、次のコマンドを使用します。

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> "az mesh service-replica" コマンドを使用して、レプリカ名を取得できます。 レプリカ名は、0 から増分する整数です。

この動作が、投票アプリケーションの VotingWeb.Code コンテナーからのログにどのように記録されるかを次に示します。

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>コンテナー メトリック 

Mesh 環境は、コンテナーのパフォーマンスを示す、いくつかのメトリックを公開します。 Azure portal および Azure Monitor の CLI を介して、次のメトリックを使用できます。

| メトリック | 説明 | Units|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu (パーセンテージ) | % |
| MemoryUtilization | ActualMem/AllocatedMem (パーセンテージ) | % |
| AllocatedCpu | Azure Resource Manager テンプレートに基づいて割り当てられる CPU | ミリコア |
| AllocatedMemory | Azure Resource Manager テンプレートに基づいて割り当てられるメモリ | MB |
| ActualCpu | CPU 使用率 | ミリコア |
| ActualMemory | メモリ使用量 | MB |
| ContainerStatus | 0 - 無効:コンテナーの状態が不明です <br> 1 - 保留中:コンテナーは開始するように設定されています <br> 2 - 開始中:コンテナーは開始中です <br> 3 - 開始済み:コンテナーは正常に開始されました <br> 4 - 停止中:コンテナーは停止中です <br> 5 - 停止済み:コンテナーは正常に停止しました | 該当なし |
| ApplicationStatus | 0 - 不明:状態を取得できません <br> 1 - 準備完了:アプリケーションは正常に実行中です <br> 2 - アップグレード中:アップグレードが進行中です <br> 3 - 作成中:アプリケーションを作成中です <br> 4 - 削除中:アプリケーションを削除中です <br> 5 - 失敗:アプリケーションのデプロイに失敗しました | 該当なし |
| ServiceStatus | 0 - 無効:サービスには現在、正常性状態がありません <br> 1 - OK:サービスは正常な状態です  <br> 2 - 警告:調査が必要な問題が生じている可能性があります <br> 3 - エラー:調査が必要な問題があります <br> 4 - 不明:状態を取得できません | 該当なし |
| ServiceReplicaStatus | 0 - 無効:レプリカには現在、正常性状態がありません <br> 1 - OK:サービスは正常な状態です  <br> 2 - 警告:調査が必要な問題が生じている可能性があります <br> 3 - エラー:調査が必要な問題があります <br> 4 - 不明:状態を取得できません | 該当なし | 
| RestartCount | コンテナーの再起動の回数 | 該当なし |

> [!NOTE]
> ServiceStatus と ServiceReplicaStatus の値は、Service Fabric の [HealthState](/dotnet/api/system.fabric.health.healthstate) と同じです。

各メトリックは、異なるディメンションで使用可能であるため、異なるレベルでの集計を表示できます。 ディメンションの現在の一覧は次のとおりです。

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> CodePackageName ディメンションは Linux アプリケーションでは使用できません。 

各ディメンションは、[Service Fabric アプリケーション モデル](service-fabric-mesh-service-fabric-resources.md#applications-and-services)の異なるコンポーネントに対応します

### <a name="azure-monitor-cli"></a>Azure Monitor の CLI

コマンドの完全な一覧が [Azure Monitor CLI のドキュメント](/cli/azure/monitor/metrics#az-monitor-metrics-list)にありますが、いくつかの役立つ例を以下に示します 

それぞれの例で、リソース ID は次のパターンに従います

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* アプリケーション内のコンテナーの CPU 使用率

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* 各サービス レプリカのメモリ使用量
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* 1 時間の時間枠で各コンテナーを再起動します 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* "VotingWeb" という名前のサービス全体における、1 時間の時間枠での平均 CPU 使用率
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>メトリックス エクスプローラー

メトリックス エクスプローラーは、Mesh アプリケーションのすべてのメトリックを視覚化できる、ポータル内のブレードです。 このブレードには、ポータル内のアプリケーションのページと Azure Monitor ブレードでアクセスできます。後者は、Azure Monitor をサポートするすべての Azure リソースのメトリックを表示するために使用できます。 

![メトリックス エクスプローラー](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>次のステップ
* Service Fabric Mesh の詳細については、[Service Fabric Mesh の概要](service-fabric-mesh-overview.md)に関するページを参照してください。
* Azure Monitor メトリック コマンドの詳細については、[Azure Monitor CLI のドキュメント](/cli/azure/monitor/metrics#az-monitor-metrics-list)をご確認ください。
