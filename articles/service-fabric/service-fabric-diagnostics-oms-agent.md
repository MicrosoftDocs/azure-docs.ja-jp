---
title: Azure Monitor ログを使用したパフォーマンスの監視
description: Azure Service Fabric クラスターのコンテナーとパフォーマンス カウンターを監視するように Log Analytics Agent を設定する方法を説明します。
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 47017fdb5f1fbaba800e71dea21afe2c39bd91e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570144"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Azure Monitor ログを使用したパフォーマンスの監視

この記事では、Log Analytics エージェントを仮想マシン スケール セットの拡張機能としてクラスターに追加し、既存の Azure Log Analytics ワークスペースに接続する手順について説明します。 これにより、コンテナー、アプリケーション、およびパフォーマンスの監視に関する診断データを収集できます。 これを、仮想マシン スケール セット リソースの拡張機能として追加することで、クラスターをスケーリングしても Azure Resource Manager によってすべてのノードにインストールされるようになります。

> [!NOTE]
> この記事では、Azure Log Analytics ワークスペースが設定済みであることを前提としています。 設定していない場合は、[Azure Monitor ログの設定](service-fabric-diagnostics-oms-setup.md)に関するページに進んでください。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Azure CLI を使用して拡張機能を追加する

Log Analytics エージェントをクラスターに追加する最も良い方法は、Azure CLI で利用できる仮想マシン スケール セットの API を使用することです。 Azure CLI をまだ設定していない場合は、Azure portal に移動して [Cloud Shell](../cloud-shell/overview.md) インスタンスを開くか、[Azure CLI をインストール](/cli/azure/install-azure-cli)します。

1. Cloud Shell を要求されたら、お使いのリソースと同じサブスクリプションで作業していることを確認します。 確認には `az account show` を使用し、"name" (名前) の値がクラスターのサブスクリプション名と一致することを確認します。

2. Portal で、Log Analytics ワークスペースがあるリソース グループに移動します。 Log Analytics リソース (リソースの種類は Log Analytics ワークスペースです) をクリックします。 リソースの概要ページが表示されたら、左側のメニューの [設定] セクションの下の **[詳細設定]** をクリックします。

    ![Log Analytics のプロパティ ページ](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Windows クラスターを構築する場合は **[Windows サーバー]** を、Linux クラスターを作成する場合は **[Linux サーバー]** をクリックします。 このページには、`workspace ID` と `workspace key` (ポータルでは、主キーとして表示) が表示されます。 次の手順では、両方が必要です。

4. `vmss extension set` API を使用して、クラスターに Log Analytics エージェントをインストールするコマンドを実行します。

    Windows クラスターの場合

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Linux クラスターの場合

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Windows クラスターに Log Analytics エージェントを追加する例を次に示します。

    ![Log Analytics エージェントの CLI コマンド](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. 15 分以内にお使いのノードに正常にエージェントが追加されます。 `az vmss extension list` API を使用するとエージェントが追加されたことを確認できます。

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Resource Manager テンプレートを使用してエージェントを追加する

Azure Log Analytics ワークスペースをデプロイし、ご利用の各ノードにエージェントを追加する Resource Manager テンプレートのサンプルは、[Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) または [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS) で使用できます。

このテンプレートをダウンロードして、ニーズに最適なクラスターをデプロイするように変更することができます。

## <a name="view-performance-counters"></a>パフォーマンス カウンターの表示

Log Analytics エージェントを追加したら、Log Analytics ポータルに進み、どのパフォーマンス カウンターを収集するか選択します。

1. Azure Portal で Service Fabric Analytics ソリューションを作成したリソース グループに移動します。 **[ServiceFabric\<nameOfLog AnalyticsWorkspace\>]** を選択します。

2. **[Log Analytics]** をクリックします。

3. **[詳細設定]** をクリックします。

4. **[データ]** をクリックし、 **[Windows パフォーマンス カウンター] または [Linux パフォーマンス カウンター]** をクリックします。 選択して有効にできる既定のカウンターの一覧があり、収集の間隔を設定することもできます。 収集する[追加のパフォーマンス カウンター](service-fabric-diagnostics-event-generation-perf.md)を追加することもできます。 正しい形式については、この[記事](/windows/win32/perfctrs/specifying-a-counter-path)を参照してください。

5. **[保存]** をクリックし、 **[OK]** をクリックします。

6. [詳細設定] ブレードを閉じます。

7. [全般] という見出しの下の **[ワークスペースの概要]** をクリックします。

8. 有効なソリューションごとのグラフ形式のタイルが表示されます (Service Fabric のタイルも含まれています)。 **[Service Fabric]** グラフをクリックして、Service Fabric Analytics ソリューションを継続します。

9. 稼働チャネルと信頼性の高いサービス イベントのグラフがいくつかのタイルで表示されます。 選択したカウンターについて流れるデータのグラフィック表現が、[Node Metrics]\(ノード メトリック\) の下に表示されます。

10. [Container Metric]\(コンテナー メトリック\) グラフをクリックすると、詳細が追加表示されます。 また、Kusto クエリ言語を使用して、イベントをクラスタリングし、ノード、パフォーマンス カウンター名および値をフィルタリングするために、同様にパフォーマンス カウンター データに対してクエリを実行できます。

![Log Analytics パフォーマンス カウンターのクエリ](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>次のステップ

* 関連する[パフォーマンス カウンター](service-fabric-diagnostics-event-generation-perf.md)を収集する。 特定のパフォーマンス カウンターを収集するように Log Analytics エージェントを構成する場合は、「[データ ソースの構成](../azure-monitor/agents/agent-data-sources.md#configuring-data-sources)」を確認してください。
* Azure Monitor ログを構成して、検出と診断に役立つ[自動アラート](../azure-monitor/alerts/alerts-overview.md)を設定する
* 代わりに、パフォーマンス カウンターの収集は、[Microsoft Azure Diagnostics の拡張機能で行い、Application Insights に送信](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)することができる。
