---
title: Azure Machine Learning データの監視のリファレンス | Microsoft Docs
description: Azure Machine Learning の監視に関するリファレンス ドキュメント。 Azure Monitor で収集して利用できるデータとリソースについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/02/2020
ms.openlocfilehash: f130fc0c65c49c33c838812fc2758619e0d1bca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521341"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Azure Machine Learning データの監視のリファレンス

ご利用の Azure Machine Learning ワークスペースから Azure Monitor によって収集されるデータとリソースについて説明します。 監視データの収集と分析の詳細については、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="metrics"></a>メトリック

このセクションには、Azure Machine Learning 用に自動的に収集されたすべてのプラットフォーム メトリックが一覧表示されています。 これらのメトリックのリソース プロバイダーは、[Microsoft.MachineLearningServices/workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) です。

**Model**

| メトリック | ユニット | 説明 |
| ----- | ----- | ----- |
| モデルのデプロイが失敗しました | Count | 失敗したモデルのデプロイの数。 |
| 開始されたモデル デプロイ | Count | 開始されたモデル デプロイの数。 |
| モデルのデプロイが成功しました | Count | 成功したモデル デプロイの数。 |
| モデルの登録が失敗しました | Count | 失敗したモデル登録の数。 |
| モデルの登録が成功しました | Count | 成功したモデル登録の数。 |

**[クォータ]**

クォータ情報は Azure Machine Learning のコンピューティングのみを対象としています。

| メトリック | ユニット | 説明 |
| ----- | ----- | ----- |
| アクティブなコア | Count | アクティブなコンピューティング コアの数。 |
| アクティブなノード | Count | アクティブなノードの数。 |
| アイドル状態のコア | Count | アイドル状態のコンピューティング コアの数。 |
| アイドル状態のノード | Count | アイドル状態のコンピューティング ノードの数。 |
| 終了中のコア | Count | 終了中のコアの数。 |
| 終了中のノード | Count | 終了中のノードの数。 |
| 割り込まれたコア | Count | 割り込まれたコアの数。 |
| Preempted Node (割り込まれたノード) | Count | 割り込まれたノードの数。 |
| クォータ使用率 | Percent | 使用されているクォータの割合。 |
| コア総数 | Count | コア総数。 |
| ノード総数 | Count | ノード総数。 |
| 使用できないコア | Count | 使用できないコアの数。 |
| 使用できないノード | Count | 使用できないノードの数。 |

**リソース**

| メトリック | ユニット | 説明 |
| ----- | ----- | ----- |
| CpuUtilization | Percent | 実行/ジョブ中に特定のノードで使用された CPU の割合。 このメトリックは、ジョブがノード上で実行されている場合にのみ発行されます。 1 つのジョブで 1 つ以上のノードを使用できます。 このメトリックはノードごとに発行されます。 |
| GpuUtilization | Percent | 実行/ジョブ中に特定のノードで使用された GPU の割合。 1 つのノードが 1 つ以上の GPU を持つことができます。 このメトリックは、ノードごと、GPU ごとに発行されます。 |

**[実行]**

トレーニングの実行に関する情報。

| メトリック | ユニット | 説明 |
| ----- | ----- | ----- |
| 完了した実行 | Count | 完了した実行の数。 |
| 失敗した実行 | Count | 失敗した実行の数。 |
| 開始された実行 | Count | 開始された実行の数。 |

## <a name="metric-dimensions"></a>メトリック ディメンション

メトリック ディメンションの詳細については、「[多次元メトリック](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

Azure Machine Learning では、次のディメンションがメトリックに関連付けられています。

| Dimension | 説明 |
| ---- | ---- |
| クラスター名 | コンピューティング クラスター リソースの名前。 すべてのクォータ メトリックで使用できます。 |
| VM ファミリ名 | クラスターによって使用される VM ファミリの名前。 クォータ使用率で使用できます。 |
| VM 優先度 | VM の優先度。 クォータ使用率で使用できます。
| CreatedTime | CpuUtilization と GpuUtilization でのみ使用できます。 |
| deviceId | デバイスの ID (GPU)。 GpuUtilization でのみ使用できます。 |
| NodeId | ジョブが実行されている場所で作成されたノードの ID。 CpuUtilization と GpuUtilization でのみ使用できます。 |
| RunId | 実行/ジョブの ID。 CpuUtilization と GpuUtilization でのみ使用できます。 |
| ComputeType | 実行で使用されるコンピューティングの種類。 完了した実行、失敗した実行、および開始された実行に対してのみ使用できます。 |
| PipelineStepType | 実行で使用される [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) の種類。 完了した実行、失敗した実行、および開始された実行に対してのみ使用できます。 |
| PublishedPipelineId | 実行で使用される公開済みパイプラインの ID。 完了した実行、失敗した実行、および開始された実行に対してのみ使用できます。 |
| RunType | 実行の種類。 完了した実行、失敗した実行、および開始された実行に対してのみ使用できます。 |

RunType ディメンションの有効な値は次のとおりです。

| 値 | 説明 |
| ----- | ----- |
| 実験 | パイプライン以外の実行。 |
| PipelineRun | StepRun の親であるパイプラインの実行。 |
| StepRun | パイプライン ステップの実行。 |
| ReusedStepRun | 前回の実行を再利用するパイプライン ステップの実行。 |

## <a name="activity-log"></a>アクティビティ ログ

次の表は、アクティビティ ログで作成される可能性がある Azure Machine Learning に関連する操作を示しています。

| 操作 | 説明 |
|:---|:---|
| Machine Learning ワークスペースを作成または更新します | ワークスペースが作成または更新されました |
| CheckComputeNameAvailability | コンピューティング名が既に使用されているかどうかを確認します |
| コンピューティング リソースを作成または更新します | コンピューティング リソースが作成または更新されました |
| コンピューティング リソースを削除します | コンピューティング リソースが削除されました |
| シークレットのリスト | Machine Learning ワークスペースの操作のシークレットのリスト |

## <a name="resource-logs"></a>リソース ログ

このセクションでは、Azure Machine Learning ワークスペース用に収集できるリソース ログの種類を示します。

リソース プロバイダーと種類:[Microsoft.MachineLearningServices/workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces)。

| カテゴリ | 表示名 |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>スキーマ

次のスキーマは、Azure Machine Learning によって使用されています

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents テーブル

| プロパティ | 説明 |
|:--- |:---|
| TimeGenerated | ログ エントリが生成された時刻 |
| OperationName | ログ イベントに関連付けられた操作の名前 |
| カテゴリ | ログ イベントの名前、AmlComputeClusterNodeEvent |
| JobId | 送信されたジョブの ID |
| ExperimentId | 実験の ID |
| ExperimentName | 実験の名前 |
| CustomerSubscriptionId | 実験とジョブが送信された SubscriptionId |
| WorkspaceName | 機械学習ワークスペースの名前 |
| ClusterName | クラスターの名前 |
| ProvisioningState | ジョブの送信の状態 |
| ResourceGroupName | リソース グループの名前 |
| JobName | ジョブの名前 |
| ClusterId | クラスターの ID |
| EventType | ジョブ イベントの種類。 たとえば、JobSubmitted、JobRunning、JobFailed、JobSucceeded など。 |
| ExecutionState | ジョブ (実行) の状態。 たとえば、キューに登録済み、実行中、成功、失敗など |
| ErrorDetails | ジョブ エラーの詳細 |
| CreationApiVersion | ジョブの作成に使用される API バージョン |
| ClusterResourceGroupName | クラスターのリソース グループ名 |
| TFWorkerCount | TF worker の数 |
| TFParameterServerCount | TF パラメーター サーバーの数 |
| ToolType | 使用するツールの種類 |
| RunInContainer | ジョブをコンテナー内で実行する必要があるかどうかを示すフラグ |
| JobErrorMessage | ジョブ エラーの詳細なメッセージ |
| NodeId | ジョブが実行されている場所で作成されたノードの ID |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents テーブル

| プロパティ | 説明 |
|:--- |:--- |
| TimeGenerated | ログ エントリが生成された時刻 |
| OperationName | ログ イベントに関連付けられた操作の名前 |
| カテゴリ | ログ イベントの名前、AmlComputeClusterNodeEvent |
| ProvisioningState | クラスターのプロビジョニング状態 |
| ClusterName | クラスターの名前 |
| ClusterType | クラスターの種類 |
| CreatedBy | クラスターを作成したユーザー |
| CoreCount | クラスター内のコアの数 |
| VmSize | クラスターの VM サイズ |
| VmPriority | クラスターの内部で作成されたノードの優先度: Dedicated/LowPriority |
| ScalingType | クラスターのスケーリングの種類: 手動/自動 |
| InitialNodeCount | クラスターの初期のノード数 |
| MinimumNodeCount | クラスターの最小ノード数 |
| MaximumNodeCount | クラスターの最大ノード数 |
| NodeDeallocationOption | ノードの割り当てを解除する方法 |
| Publisher | クラスターの種類の発行元 |
| プラン | クラスターの作成に使用されるプラン |
| Sku | クラスター内に作成されたノード/VM の SKU |
| Version | ノード/VM の作成時に使用されるイメージのバージョン |
| SubnetId | クラスターの SubnetId |
| AllocationState | クラスター割り当ての状態 |
| CurrentNodeCount | クラスターの現在のノード数 |
| TargetNodeCount | スケール アップ/スケール ダウン中のクラスターのターゲット ノード数 |
| EventType | クラスター作成時のイベントの種類。 |
| NodeIdleTimeSecondsBeforeScaleDown | クラスターをスケール ダウン前のアイドル時間 (秒) |
| PreemptedNodeCount | クラスターにおける割り込まれたノード数 |
| IsResizeGrow | クラスターがスケール アップされていることを示すフラグ |
| VmFamilyName | クラスター内で作成できるノードの VM ファミリの名前 |
| LeavingNodeCount | クラスターの終了中のノード数 |
| UnusableNodeCount | クラスターの使用できないノードの数 |
| IdleNodeCount | クラスターのアイドル状態のノード数 |
| RunningNodeCount | クラスターの実行中のノード数 |
| PreparingNodeCount | クラスターの準備中のノード数 |
| QuotaAllocated | クラスターに割り当てられたクォータ |
| QuotaUtilized | クラスターの使用済みクォータ |
| AllocationStateTransitionTime | ある状態から別の状態への移行時間 |
| ClusterErrorCodes | クラスターの作成中またはスケーリング中に受信したエラー コード |
| CreationApiVersion | クラスターの作成時に使用される API バージョン |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents テーブル

| プロパティ | 説明 |
|:--- |:--- |
| TimeGenerated | ログ エントリが生成された時刻 |
| OperationName | ログ イベントに関連付けられた操作の名前 |
| カテゴリ | ログ イベントの名前、AmlComputeClusterNodeEvent |
| ClusterName | クラスターの名前 |
| NodeId | 作成されたクラスター ノードの ID |
| VmSize | ノードの VM サイズ |
| VmFamilyName | ノードが属している VM ファミリ |
| VmPriority | 作成されたノードの優先順位: Dedicated/LowPriority |
| Publisher | VM イメージの発行元。 たとえば、microsoft-dsvm など |
| プラン | VM 作成に関連付けられているプラン |
| Sku | 作成されたノード/VM の SKU |
| Version | ノード/VM の作成時に使用されるイメージのバージョン |
| ClusterCreationTime | クラスターが作成された時刻 |
| ResizeStartTime | クラスターのスケール アップ/スケール ダウンが開始された時刻 |
| ResizeEndTime | クラスターのスケール アップ/スケール ダウンが終了した時刻 |
| NodeAllocationTime | ノードが割り当てられた時刻 |
| NodeBootTime | ノードが起動された時刻 |
| StartTaskStartTime | タスクがノードに割り当てられて開始された時刻 |
| StartTaskEndTime | ノードに割り当てられたタスクが終了した時刻 |
| TotalE2ETimeInSeconds | ノードがアクティブだった合計時間 |


## <a name="see-also"></a>関連項目

- Azure Machine Learning の説明については、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
