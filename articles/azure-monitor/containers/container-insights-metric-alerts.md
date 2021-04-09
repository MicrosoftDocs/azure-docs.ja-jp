---
title: Container insights からのメトリック アラート
description: この記事では、Container insights から利用可能なパブリック プレビュー段階の推奨メトリック アラートを確認します。
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: f19959c76d31422a0bdf898a6fa41e6b168e2e61
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728894"
---
# <a name="recommended-metric-alerts-preview-from-container-insights"></a>Container insights からの推奨メトリック アラート (プレビュー)

需要がピークに達してほぼ容量の限度に近い状態で実行されているシステム リソースの問題についてアラートを生成するには、Container insights を使用して、Azure Monitor ログに格納されているパフォーマンス データに基づいてログ アラートを作成します。 Container insights に、AKS および Azure Arc 対応 Kubernetes クラスターの事前構成済みメトリック アラート ルールが含まれるようになりました。これは、現在パブリック プレビューの段階です。

この記事では、エクスペリエンスを確認し、これらのアラート ルールの構成と管理に関するガイダンスを提供します。

Azure Monitor のアラートに詳しくない場合は、事前に「[Microsoft Azure のアラートの概要](../alerts/alerts-overview.md)」を参照してください。 メトリック アラートの詳細については、「[Azure Monitor でのメトリック アラートの機能](../alerts/alerts-metric-overview.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

開始する前に、次のことを確認してください。

* カスタム メトリックは、一部の Azure リージョンでのみ利用できます。 サポートされているリージョンの一覧については、「[サポートされているリージョン](../essentials/metrics-custom-overview.md#supported-regions)」を参照してください。

* メトリック アラートおよび追加メトリックの導入をサポートするためのエージェントの必要最小限のバージョンは、AKS の場合は **mcr.microsoft.com/azuremonitor/containerinsights/ciprod:ciprod05262020**、Azure Arc 対応 Kubernetes クラスターの場合は **mcr.microsoft.com/azuremonitor/containerinsights/ciprod:ciprod09252020** です。

    お使いのクラスターで新しいバージョンのエージェントが実行されていることを確認するには、次のいずれかの手順を行います。

    * コマンド `kubectl describe <omsagent-pod-name> --namespace=kube-system`を実行します。 返された状態の、出力の *Containers* セクションにある omsagent の **Image** の下の値をメモします。 
    * **[ノード]** タブでクラスター ノードを選択し、右側の **プロパティ** ウィンドウにある **[エージェント イメージ タグ]** の下の値をメモします。

    AKS の場合、表示される値はバージョン **ciprod05262020** 以降である必要があります。 Azure Arc 対応 Kubernetes クラスターの場合、表示される値はバージョン **ciprod09252020** 以降である必要があります。 クラスターで古いバージョンを使用している場合は、[Container insights エージェントをアップグレードする方法](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster)に関する記事で、最新バージョンを取得する手順を参照してください。

    エージェントのリリースに関する詳細については、[エージェントのリリース履歴](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)を参照してください。 メトリックが収集されていることを確認するには、Azure Monitor のメトリックス エクスプローラーを使用して、**分析情報** が一覧表示されている **[メトリック名前空間]** から確認できます。 収集されている場合は、先に進んで、アラートの設定を開始できます。 収集されたメトリックが表示されない場合は、クラスターのサービス プリンシパルまたは MSI に必要なアクセス許可がありません。 SPN または MSI が **監視メトリック パブリッシャー** ロールのメンバーであることを確認するには、「[Azure CLI を使用してクラスターごとにアップグレードする](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli)」セクションの手順に従って、ロールの割り当てを確認して設定します。

## <a name="alert-rules-overview"></a>アラート ルールの概要

重要な問題についてアラートを生成するために、Container insights には、AKS および Azure Arc 対応 Kubernetes クラスターに関する次のメトリック アラートが含まれています。

|名前| [説明] |既定のしきい値 |
|----|-------------|------------------|
|Average container CPU % (コンテナーの平均 CPU %) |コンテナーあたりの使用された平均 CPU を計算します。|コンテナーあたりの平均 CPU 使用率が 95% を超えたとき。| 
|Average container working set memory % (コンテナーの平均ワーキング セット メモリ %) |コンテナーあたりの使用された平均ワーキング セット メモリを計算します。|コンテナーあたりの平均ワーキング セット メモリ使用率が 95% を超えたとき。 |
|Average CPU % (平均 CPU %) |ノードあたりの使用された平均 CPU を計算します。 |ノードの平均 CPU 使用率が 80% を超えたとき |
|Average Disk Usage % (平均ディスク使用率 %) |ノードの平均ディスク使用率を計算します。|ノードの平均ディスク使用率が 80% を超えたとき。 |
|Average Persistent Volume Usage % (永続ボリュームの平均使用率 %) |ポッドあたりの平均 PV 使用量を計算します。 |ポッドあたりの平均 PV 使用率が 80% を超えたとき。|
|Average Working set memory % (平均ワーキング セット メモリ %) |ノードの平均ワーキング セット メモリを計算します。 |ノードの平均ワーキング セット メモリが 80% を超えたとき。 |
|Restarting container count (コンテナーの再起動回数) |コンテナーの再起動回数を計算します。 | コンテナーの再起動回数が 0 を超えたとき。 |
|Failed Pod Counts (失敗したポッド数) |失敗した状態のポッドがあるかどうかを計算します。|失敗した状態のポッド数が 0 を超えたとき。 |
|Node NotReady status (NotReady 状態のノード) |NotReady 状態のノードがあるかどうかを計算します。|NotReady 状態のノード数が 0 を超えたとき。 |
|OOM Killed Containers (OOM により中止されたコンテナー) |OOM により中止されたコンテナーの数を計算します。 |OOM により中止されたコンテナーの数が 0 を超えたとき。 |
|Pods ready % (準備完了ポッドの割合 %) |準備完了状態の平均ポッド数を計算します。 |準備完了ポッドの割合が 80% を下回ったとき。|
|Completed job count (完了したジョブの数) |6 時間より前に完了したジョブの数を計算します。 |6 時間以上経過したジョブの数が 0 を超えたとき。|

これらのアラート ルールには、次のような共通プロパティがあります。

* すべてのアラート ツールはメトリックに基づきます。

* すべてのアラート ルールは、既定で無効になっています。

* すべてのアラート ルールは、1 分に 1 回評価され、5 分前にさかのぼってデータを調べます。

* 既定では、アラート ルールにアクション グループは割り当てられません。 アラート ルールの編集中に、既存のアクション グループを選択するか、新しいアクション グループを作成すると、アラートに[アクション グループ](../alerts/action-groups.md)を追加できます。

* アラート ルールのしきい値は、直接編集して変更することができます。 ただし、しきい値を変更する前に、各アラート ルールで提供されているガイダンスを参照してください。

次のアラートベースのメトリックには、他のメトリックと比較して動作に固有の特性があります。

* *completedJobsCount* メトリックは、6 時間より前に完了したジョブがある場合にのみ送信されます。

* *containerRestartCount* メトリックは、コンテナーの再起動がある場合にのみ送信されます。

* *oomKilledContainerCount* メトリックは、OOM により中止されたコンテナーがある場合にのみ送信されます。

* *cpuExceededPercentage*、*memoryRssExceededPercentage*、*memoryWorkingSetExceededPercentage* メトリックは、CPU、メモリ RSS、メモリ ワーキング セットの値が、構成されたしきい値 (既定のしきい値は 95%) を超えたときに送信されます。 これらのしきい値には、対応するアラート ルールに指定されたアラート条件のしきい値は含まれません。 つまり、これらのメトリックを収集し、[メトリック エクスプローラー](../essentials/metrics-getting-started.md) で分析する必要がある場合は、しきい値をアラートのしきい値より低い値に構成することをお勧めします。 コンテナーのリソース使用率のしきい値に関するコレクション設定関連の構成は、セクション `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` の下の ConfigMaps ファイルでオーバーライドすることができます。 ConfigMap 構成ファイルの構成の詳細については、セクション「[ConfigMaps で警告可能なメトリックを構成する](#configure-alertable-metrics-in-configmaps)」を参照してください。

* *pvUsageExceededPercentage* メトリックは、永続ボリューム使用率が、構成されているしきい値 (既定のしきい値は 60%) を超えたときに送信されます。 このしきい値には、対応するアラート ルールに指定されたアラート条件のしきい値は含まれません。 つまり、これらのメトリックを収集し、[メトリック エクスプローラー](../essentials/metrics-getting-started.md) で分析する必要がある場合は、しきい値をアラートのしきい値より低い値に構成することをお勧めします。 永続ボリューム使用率のしきい値に関する収集設定関連の構成は、セクション `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` の下の ConfigMaps ファイルでオーバーライドすることができます。 ConfigMap 構成ファイルの構成の詳細については、セクション「[ConfigMaps で警告可能なメトリックを構成する](#configure-alertable-metrics-in-configmaps)」を参照してください。 *kube-system* 名前空間の要求での永続ボリューム メトリックの収集は、既定では除外されます。 この名前空間の収集を有効にするには、ConfigMap ファイルの `[metric_collection_settings.collect_kube_system_pv_metrics]` セクションを使用します。 詳細については、「[メトリック収集の設定](./container-insights-agent-config.md#metric-collection-settings)」を参照してください。

## <a name="metrics-collected"></a>収集されるメトリック

特に指定しない限り、次のメトリックは、この機能の一部として有効化され、収集されます。

|メトリック名前空間 |メトリック |説明 |
|---------|----|------------|
|Insights.container/nodes |cpuUsageMillicores |ホスト別の CPU 使用率 (ミリコア単位)。|
|Insights.container/nodes |cpuUsagePercentage |ノード別の CPU 使用率 (%)。|
|Insights.container/nodes |memoryRssBytes |ホスト別のメモリ RSS 使用量 (バイト単位)。|
|Insights.container/nodes |memoryRssPercentage |ホスト別のメモリ RSS 使用率 (%)。|
|Insights.container/nodes |memoryWorkingSetBytes |ホスト別のメモリ ワーキング セット使用量 (バイト単位)。|
|Insights.container/nodes |memoryWorkingSetPercentage |ホスト別のメモリ ワーキング セット使用率 (%)。|
|Insights.container/nodes |nodesCount |状態別のノード数。|
|Insights.container/nodes |diskUsedPercentage |デバイス別のノードで使用されるディスクの割合 (%)。|
|Insights.container/pods |podCount |コントローラー、名前空間、ノード、フェーズ別のポッド数。|
|Insights.container/pods |completedJobsCount |ユーザーが構成可能なしきい値 (既定値は 6 時間) より前に完了したジョブのコントローラー、Kubernetes 名前空間別の数。 |
|Insights.container/pods |restartingContainerCount |コントローラー、Kubernetes 名前空間別のコンテナー再起動回数。|
|Insights.container/pods |oomKilledContainerCount |OOM により中止されたコンテナーのコントローラー、Kubernetes 名前空間別の数。|
|Insights.container/pods |podReadyPercentage |コントローラー、Kubernetes 名前空間別の準備完了状態のポッドの割合 (%)。|
|Insights.container/containers |cpuExceededPercentage |ユーザーが構成可能なしきい値 (既定値は 95.0) を超えるコンテナーの CPU 使用率 (%)。コンテナー名、コントローラー名、Kubernetes 名前空間、ポッド名別。<br> 収集済み  |
|Insights.container/containers |memoryRssExceededPercentage |ユーザーが構成可能なしきい値 (既定値は 95.0) を超えるコンテナーのメモリ RSS (%)。コンテナー名、コントローラー名、Kubernetes 名前空間、ポッド名別。|
|Insights.container/containers |memoryWorkingSetExceededPercentage |ユーザーが構成可能なしきい値 (既定値は 95.0) を超えるコンテナーのメモリ ワーキング セットの割合 (%)。コンテナー名、コントローラー名、Kubernetes 名前空間、ポッド名別。|
|Insights.container/persistentvolumes |pvUsageExceededPercentage |ユーザーが構成可能なしきい値 (既定値は 60.0) を超える永続ボリュームの PV 使用率 (%)。要求名、Kubernetes 名前空間、ボリューム名、ポッド名、ノード名別。

## <a name="enable-alert-rules"></a>アラート ルールを有効にする

Azure portal から Azure Monitor のメトリック アラートを有効にするには、次の手順に従います。 Resource Manager テンプレートを使用して有効にするには、「[Resource Manager テンプレートを使用して有効にする](#enable-with-a-resource-manager-template)」を参照してください。

### <a name="from-the-azure-portal"></a>Azure portal から

このセクションでは、Azure portal から Container insights のメトリック アラート (プレビュー) を有効にする手順について説明します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. Container insights のメトリック アラート (プレビュー) には、Azure portal の左側のペインから **[分析情報]** を選択して AKS クラスターから直接アクセスできます。

3. コマンド バーで、 **[推奨アラート]** を選択します。

    ![Container insights の推奨アラート オプション](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. ページの右側に **[推奨アラート]** プロパティ ペインが自動的に表示されます。 既定では、一覧内のすべてのアラート ルールは無効です。 **[有効]** を選択すると、アラート ルールが作成され、ルール名が更新されて、アラート リソースへのリンクが含まれるようになります。

    ![[推奨アラート] プロパティ ペイン](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    **[有効/無効]** トグルを選択して、アラートを有効にすると、アラート ルールが作成され、ルール名が更新されて、実際のアラート リソースへのリンクが含まれるようになります。

    ![警告ルールを有効にする](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. アラート ルールは[アクション グループ](../alerts/action-groups.md)に関連付けられないので、アラートがトリガーされてもユーザーに通知されません。 **[No action group assigned]\(アクション グループは割り当てられていません\)** を選択し、 **[アクション グループ]** ページで、 **[追加]** または **[作成]** を選択して、既存のアクション グループを指定するか、アクション グループを作成します。

    ![アクション グループを選択する](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用して有効にする

Azure Resource Manager テンプレートとパラメーター ファイルを使用して、Azure Monitor に含まれるメトリック アラートを作成できます。

基本的な手順は次のとおりです。

1. アラートの作成方法を説明している 1 つまたはすべての使用可能なテンプレートを [GitHub](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM)からダウンロードします。

2. [パラメーター ファイル](../../azure-resource-manager/templates/parameter-files.md)を JSON として作成し、それを使用して、アラート ルールの作成に必要な値を設定します。

3. Azure portal、PowerShell、または Azure CLI からテンプレートをデプロイします。

#### <a name="deploy-through-azure-portal"></a>Azure portal を使用してデプロイする

1. 次のコマンドを使用して、アラート ルールを作成するには、Azure Resource Manager テンプレートとパラメーター ファイルをダウンロードして、ローカル フォルダーに保存します。

2. カスタマイズされたテンプレートを portal からデプロイするには、[Azure portal](https://portal.azure.com)で **[リソースの作成]** を選択します。

3. **template** を検索して、 **[テンプレートのデプロイ]** を選択します。

4. **［作成］** を選択します

5. テンプレートを作成するためのいくつかのオプションが表示されます。 **[エディターで独自のテンプレートを作成する]** を選択します。

6. **[テンプレートの編集]** ページで、 **[ファイルの読み込み]** を選択し、テンプレート ファイルを選択します。

7. **[テンプレートの編集]** ページで、 **[保存]** を選択します。

8. **[カスタム デプロイ]** ページで、次のように指定し、完了したら、 **[購入]** を選択して、テンプレートをデプロイし、アラート ルールを作成します。

    * Resource group
    * 場所
    * アラート名
    * クラスターのリソース ID

#### <a name="deploy-with-azure-powershell-or-cli"></a>Azure PowerShell または CLI を使用してデプロイする

1. 次のコマンドを使用して、アラート ルールを作成するには、Azure Resource Manager テンプレートとパラメーター ファイルをダウンロードして、ローカル フォルダーに保存します。

2. メトリック アラートはテンプレート､パラメーター ファイルは PowerShell か Azure CLI を使用して作成できます｡

    Azure PowerShell の使用

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Azure CLI の使用

    ```azurecli
    az login

    az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >メトリック アラートはターゲット リソースと異なるリソース グループに作成することができますが､ターゲット リソースと同じリソース グループを使用することをお勧めします｡

## <a name="edit-alert-rules"></a>アラート ルールを編集する

Container insights のアラート ルールを表示および管理して、そのしきい値を編集したり、AKS クラスターの[アクション グループ](../alerts/action-groups.md)を構成したりすることができます。 これらのアクションは、Azure portal および Azure CLI から実行できますが、Container insights で AKS クラスターから直接実行することもできます。

1. コマンド バーで、 **[推奨アラート]** を選択します。

2. しきい値を変更するには、 **[推奨アラート]** ペインで、有効になっているアラートを選択します。 **[ルールの編集]** で、編集する **[アラートの条件]** を選択します。

    * アラート ルールのしきい値を変更するには、 **[条件]** を選択します。
    * 既存のアクション グループを指定するか、アクション グループを作成するには、 **[アクション グループ]** の下の **[追加]** または **[作成]** を選択します

有効になっているルール用に作成されたアラートを表示するには、 **[推奨アラート]** ペインで、 **[View in alerts]\(アラートを表示\)** を選択します。 AKS クラスターのアラート メニューにリダイレクトされます。そこに、ご利用のクラスター用に現在作成されているすべてのアラートが表示されます。

## <a name="configure-alertable-metrics-in-configmaps"></a>ConfigMaps で警告可能なメトリックを構成する

ConfigMap 構成ファイルを構成して、使用率の既定のしきい値をオーバーライドするには、次の手順を行います。 これらの手順は、次の警告可能なメトリックにのみ適用できます。

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*
* *pvUsageExceededPercentage*

1. セクション `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` または `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` の下の ConfigMap YAML ファイルを編集します。

   - *cpuExceededPercentage* のしきい値を 90% に変更して、このしきい値を超えたときにこのメトリックの収集を開始するには、次の例を使用して ConfigMap ファイルを構成します。

     ```
     [alertable_metrics_configuration_settings.container_resource_utilization_thresholds]
         # Threshold for container cpu, metric will be sent only when cpu utilization exceeds or becomes equal to the following percentage
         container_cpu_threshold_percentage = 90.0
         # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
         container_memory_rss_threshold_percentage = 95.0
         # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
         container_memory_working_set_threshold_percentage = 95.0
     ```

   - *pvUsageExceededPercentage* のしきい値を 80% に変更して、このしきい値を超えたときにこのメトリックの収集を開始するには、次の例を使用して ConfigMap ファイルを構成します。

     ```
     [alertable_metrics_configuration_settings.pv_utilization_thresholds]
         # Threshold for persistent volume usage bytes, metric will be sent only when persistent volume utilization exceeds or becomes equal to the following percentage
         pv_usage_threshold_percentage = 80.0
     ```

2. kubectl コマンド `kubectl apply -f <configmap_yaml_file.yaml>` を実行します。

    例: `kubectl apply -f container-azm-ms-agentconfig.yaml`.

構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドの再起動は、ローリング再起動です。すべてが同時に再起動されるわけではありません。 再起動が完了すると、`configmap "container-azm-ms-agentconfig" created` のようなメッセージが表示され、結果が含まれています。

## <a name="next-steps"></a>次のステップ

- [ログ クエリの例](container-insights-log-search.md#search-logs-to-analyze-data)を表示して、事前定義されたクエリや例を確認し、クラスターのアラート、視覚化、または分析のために評価やカスタマイズを行います。

- Azure Monitor と、Kubernetes クラスターの他のアスペクトを監視する方法の詳細については、[Kubernetes クラスターのパフォーマンスの監視](container-insights-analyze.md)に関するページをご覧ください。