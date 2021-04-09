---
title: メトリックの Container insights を更新する方法 | Microsoft Docs
description: この記事では、集計したメトリックの探索とアラートをサポートするカスタム メトリック機能を有効にするように Container insights を更新する方法について説明します。
ms.topic: conceptual
ms.date: 10/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: edca7e4e8f6a9ea8dd9efdaafab8c906efd671b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708273"
---
# <a name="how-to-update-container-insights-to-enable-metrics"></a>メトリックを有効にするように Container insights を更新する方法

Container insights により、Azure Kubernetes Services (AKS) および Azure Arc 対応の Kubernetes クラスター ノードおよびポッドからのメトリックの収集と、Azure Monitor メトリック ストアへのそれらの書き込みのサポートが導入されます。 この変更は、パフォーマンス グラフでの集計計算 (Avg、Count、Max、Min、Sum) を表示するときの適時性の改善、Azure portal ダッシュボードでのパフォーマンス グラフのピン留めのサポート、およびメトリック アラートのサポートを実現するためのものです。

>[!NOTE]
>現在、この機能は Azure Red Hat OpenShift クラスターをサポートしていません。
>

この機能の一部として、次のメトリックが有効にされています。

| メトリック名前空間 | メトリック | 説明 |
|------------------|--------|-------------|
| Insights.container/nodes | cpuUsageMillicores、cpuUsagePercentage、memoryRssBytes、memoryRssPercentage、memoryWorkingSetBytes、memoryWorkingSetPercentage、nodesCount、diskUsedPercentage | *ノード* メトリックとして、それらは *ホスト* をディメンションとして含みます。 また、<br> *ホスト* ディメンションの値として、ノードの名前も含みます。 |
| Insights.container/pods | podCount、completedJobsCount、restartingContainerCount、oomKilledContainerCount、podReadyPercentage | *ポッド* メトリックとして、それらは ControllerName、Kubernetes 名前空間、名前、フェーズをディメンションとして含みます。 |
| Insights.container/containers | cpuExceededPercentage、memoryRssExceededPercentage、memoryWorkingSetExceededPercentage | |
| Insights.container/persistentvolumes | pvUsageExceededPercentage | |

これらの新機能をサポートするために、新しいコンテナー化されたエージェントが、このリリースのバージョン **microsoft/oms:ciprod05262020** (AKS 用) およびバージョン **microsoft/oms:ciprod09252020** (Azure Arc 対応 Kubernetes クラスター用) に含まれています。 AKS の新しいデプロイでは、この構成の変更と機能が自動的に含まれます。 この機能をサポートするためのクラスターの更新は、Azure portal、Azure PowerShell、または Azure CLI で実行できます。 Azure PowerShell と Azure CLI を使用すると、 サブスクリプション内のクラスターごとに、またはすべてのクラスターでこれを実行できます。

エージェントが収集したデータをクラスター リソースにパブリッシュできるように、どちらのプロセスでも、クラスターのサービス プリンシパルまたは監視アドオン用のユーザー割り当て済み MSI に対して **メトリックの発行元の監視** ロールが割り当てられます。 メトリックの発行元の監視は、メトリックをリソースにプッシュする権限のみを持ち、状態の変更、リソースの更新、およびデータの読み取りはできません。 このロールの詳細については、[メトリックの発行元の監視ロール](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher)に関する記事を参照してください。 監視メトリック発行者ロールの要件は、Azure Arc 対応 Kubernetes クラスターには適用されません。

> [!IMPORTANT]
> Azure Arc 対応 Kubernetes クラスターには、必要な最小バージョンのエージェントが既に存在するため、アップグレードは必要ありません。

## <a name="prerequisites"></a>前提条件

クラスターを更新する前に、次のことを確認してください。

* カスタム メトリックは、一部の Azure リージョンでのみ利用できます。 サポートされているリージョンの一覧については、[こちら](../essentials/metrics-custom-overview.md#supported-regions)を参照してください。

* AKS クラスター リソースの **[所有者](../../role-based-access-control/built-in-roles.md#owner)** ロールのメンバーであることを確認してください。このロールでは、ノードとポッドのカスタム パフォーマンス メトリックを収集できます。 この要件は、Azure Arc 対応 Kubernetes クラスターには適用されません。

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.59 以降を実行している必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Azure portal からクラスターをアップグレードする

Container insights によって監視される既存の AKS クラスターの場合、クラスターを選択して Azure Monitor のマルチクラスター ビューから、または左側のペインで **[Insights]** を選択することでクラスターから直接その正常性を表示すると、ポータルの上部にバナーが表示されます。

![Azure portal の AKS クラスター バナーのアップグレード](./media/container-insights-update-metrics/portal-banner-enable-01.png)

**[有効化]** をクリックすると、クラスターをアップグレードするためのプロセスが開始されます。 このプロセスには終了するまでに数秒かかる場合があり、メニューの [通知] の下で進行状況を追跡できます。

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Azure コマンド シェルで Bash を使用してすべてのクラスターをアップグレードする

Azure コマンド シェルで Bash を使用してサブスクリプション内のすべてのクラスターを更新するには、次の手順を実行します。

1. Azure CLI を使用して、次のコマンドを実行します。  AKS クラスターの **[AKS の概要]** ページの値を使用して、**subscriptionId** の値を編集します。

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    構成の変更が完了するまでに、数秒かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Azure CLI を使用してクラスターごとにアップグレードする

Azure CLI を使用してサブスクリプション内の特定のクラスターを更新するには、次の手順を実行します。

1. Azure CLI を使用して、次のコマンドを実行します。 AKS クラスターの **[AKS の概要]** ページの値を使用して、**subscriptionId** **resourceGroupName** 、および **clusterName** の値を編集します。  **clientIdOfSPN** の値を取得する場合、次の例に示すように、コマンド `az aks show` を実行すると値が返されます。

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    **clientIdOfSPNOrMsi** の値を取得するには、次の例に示すように、コマンド `az aks show` を実行します。 **servicePrincipalProfile** オブジェクトに有効な *clientid* 値がある場合は、その値を使用できます。 そうではなく、*msi* に設定されている場合は、`addonProfiles.omsagent.identity.clientId` から clientid を渡す必要があります。

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Azure PowerShell を使用してすべてのクラスターをアップグレードする

Azure PowerShell を使用してサブスクリプション内のすべてのクラスターを更新するには、次の手順を実行します。

1. GitHub リポジトリから **mdm_onboarding_atscale.ps1** スクリプトを [ダウンロード](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1)し、ローカル フォルダーに保存します。
2. Azure PowerShell を使用して、次のコマンドを実行します。  AKS クラスターの **[AKS の概要]** ページの値を使用して、**subscriptionId** の値を編集します。

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    構成の変更が完了するまでに、数秒かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Azure PowerShell を使用してクラスターごとにアップグレードする

Azure PowerShell を使用して特定のクラスターを更新するには、次の手順を実行します。

1. GitHub リポジトリから **mdm_onboarding.ps1** スクリプトを [ダウンロード](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1)し、ローカル フォルダーに保存します。

2. Azure PowerShell を使用して、次のコマンドを実行します。 AKS クラスターの **[AKS の概要]** ページの値を使用して、**subscriptionId** **resourceGroupName** 、および **clusterName** の値を編集します。

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    構成の変更が完了するまでに、数秒かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>更新を確認する

前に説明した方法のいずれかを使用して更新を開始したら、Azure Monitor メトリックス エクスプローラーを使用できます。また、 **[メトリック名前空間]** から **[Insights]** が表示されていることを確認できます。 表示されている場合は、[メトリック アラート](../alerts/alerts-metric.md)の設定または[ダッシュボード](../../azure-portal/azure-portal-dashboards.md)へのグラフのピン留めを開始できます。  
