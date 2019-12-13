---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: 2124b5241015ca74ff6507767396b1a27bd1191d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935811"
---
`deploymentconfig.json` ドキュメントのエントリは、[AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py) のパラメーターにマップされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

| JSON エンティティ | メソッド パラメーター | 説明 |
| ----- | ----- | ----- |
| `computeType` | NA | コンピューティング ターゲット。 AKS の場合、値は `aks` である必要があります。 |
| `autoScaler` | NA | 自動スケーリングの構成要素が含まれます。 自動スケーラー テーブルを参照してください。 |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Web サービスの自動スケールを有効にするかどうか。 `numReplicas` = `0` の場合、`True`。それ以外の場合、`False`。 |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | この Web サービスを自動スケールするときに使用するコンテナーの最小数。 既定値、`1`。 |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | この Web サービスを自動スケールするときに使用するコンテナーの最大数。 既定値、`10`。 |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 自動スケーラーがこの Web サービスのスケーリングを試行する頻度。 既定値、`1`。 |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | オートスケーラーがこの web サービスに対してメンテナンスを試行する目標使用率 (最大 100%)。 既定値、`70`。 |
| `dataCollection` | NA | データ収集の構成要素が含まれます。 |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Web サービスに対してモデル データ収集を有効にするかどうか。 既定値、`False`。 |
| `authEnabled` | `auth_enabled` | Web サービスに対してキー認証を有効にするかどうかを指定します。 `tokenAuthEnabled` と `authEnabled` の両方を `True` にすることはできません。 既定値、`True`。 |
| `tokenAuthEnabled` | `token_auth_enabled` | Web サービスに対してトークン認証を有効にするかどうかを指定します。 `tokenAuthEnabled` と `authEnabled` の両方を `True` にすることはできません。 既定値、`False`。 |
| `containerResourceRequirements` | NA | CPU およびメモリ エンティティのコンテナー。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | この Web サービスに割り当てる CPU コアの数。 既定値、`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | この Web サービスに割り当てるメモリの量 (GB 単位)。 既定値、`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Web サービスに対して Application Insights ログ記録を有効にするかどうか。 既定値、`False`。 |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Web サービス呼び出しのスコア付けに適用するタイムアウト。 既定値、`60000`。 |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | この Web サービスのノードあたり最大同時要求数。 既定値、`1`。 |
| `maxQueueWaitMs` | `max_request_wait_time` | 要求がキューに留まる最大時間 (ミリ秒単位)。この時間を過ぎると、503 エラーが返されます。 既定値、`500`。 |
| `numReplicas` | `num_replicas` | この Web サービスに割り当てるコンテナーの数。 既定値はありません。 このパラメーターが設定されていない場合、自動スケーラーは既定で有効になります。 |
| `keys` | NA | キーの構成要素が含まれます。 |
| &emsp;&emsp;`primaryKey` | `primary_key` | この Web サービスに使用するプライマリ認証キー |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | この Web サービスに使用するセカンダリ認証キー |
| `gpuCores` | `gpu_cores` | この Web サービスに割り当てる GPU コアの数。 既定値は 1 です。 整数のみがサポートされます。 |
| `livenessProbeRequirements` | NA | liveness probe 要件の構成要素が含まれます。 |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | liveness probe を実行する頻度 (秒単位)。 既定値は 10 秒です。 最大値は 1 です。 |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | コンテナーの起動後、liveness probe が開始するまでの秒数。 既定値は 310 です |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | liveness probe がタイムアウトするまでの秒数既定値は 2 秒です。 最小値は 1 です |
| &emsp;&emsp;`successThreshold` | `success_threshold` | 失敗後、liveness probe が成功と見なされるための最小連続成功数。 既定値は 1 です。 最大値は 1 です。 |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Pod が起動し、liveness probe が失敗したとき、Kubernetes では、failureThreshold 回数だけ試し、それからあきらめます。 既定値は 3 です。 最大値は 1 です。 |
| `namespace` | `namespace` | Web サービスのデプロイ先となる Kubernetes 名前空間。 最大 63 個の小文字の英数字 ('a'-'z'、'0'-'9') とハイフン ('-') 文字。 先頭と末尾の文字をハイフンにすることはできません。 |

次の JSON は、CLI で使用するデプロイ構成の例です。

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
