---
title: Azure Kubernetes Service (AKS) コントローラー ログの表示
description: Azure Kubernetes Service (AKS) で Kubernetes コントロール プレーンのログを有効にして表示する方法について説明します。
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 4027b2ca66b4d4319f7df347df6d671e6d48b772
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735133"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Kubernetes コントロール プレーンのログを有効にして表示する

Azure Kubernetes Service (AKS) では、*kube-apiserver* や *kube-controller-manager* などのコントロール プレーン コンポーネントが、マネージド サービスとして提供されます。 管理者は、*kubelet* とコンテナー ランタイムを実行するノードを作成して管理し、 マネージド Kubernetes API サーバーを通じてアプリケーションをデプロイします。 アプリケーションやサービスのトラブルシューティングに役立てるために、これらのコントロール プレーン コンポーネントによって生成されたログを表示する必要がある場合があります。 この記事では、Azure Monitor ログを使用して、Kubernetes コントロール プレーン コンポーネントからのログを有効にし、そのクエリを実行する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure アカウントで既存の AKS クラスターが実行されていることを前提条件としています。 AKS クラスターがまだない場合は、[Azure CLI][cli-quickstart] または [Azure portal][portal-quickstart] を使用して作成します。 Azure Monitor ログは、Kubernetes RBAC と、Azure RBAC 対応と非 RBAC 対応のどちらの AKS クラスターとでも動作します。

## <a name="enable-resource-logs"></a>リソース ログの有効化

複数のソースからデータを効率よく収集してレビューできるようにするため、Azure Monitor ログでは、環境に関するインサイトを取得するためのクエリ言語と分析エンジンが提供されています。 ワークスペースは、データの照合と分析のために使用されます。ワークスペースは、他の Azure サービス (Application Insights や Security Center など) と統合できます。 別のプラットフォームを使用してログを分析するには、Azure ストレージ アカウントやイベント ハブにリソース ログを送信することもできます。 詳しくは、[Azure Monitor ログの概要][log-analytics-overview]に関する記事をご覧ください。

Azure Monitor ログの有効化と管理は、Azure portal で行います。 AKS クラスターの Kubernetes コントロール プレーン コンポーネントのログ収集を有効にするには、Web ブラウザーで Azure portal を開き、次の手順を実行します。

1. AKS クラスターのリソース グループを選択します (*myResourceGroup* など)。 個別の AKS クラスター リソースを含んだリソース グループは選択しないでください (*MC_myResourceGroup_myAKSCluster_eastus* など)。

2. 左側で、 **[診断設定]** を選択します。

3. AKS クラスター (*myAKSCluster* など) を選択し、 **[診断設定を追加する]** を選択します。
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="診断設定を示すブラウザー ウィンドウ内の Azure portal のスクリーンショット ([診断設定の追加] を選択する必要があることを示しています)":::

4. 名前 (*myAKSClusterLogs* など) を入力し、**[Send to Log Analytics workspace]\(Log Analytics ワークスペースに送信\)** オプションを選択します。

5. 既存のワークスペースを選択するか、新しいワークスペースを作成します。 ワークスペースを作成する場合は、ワークスペースの名前、リソース グループ、および場所を指定します。

6. 使用可能なログの一覧から、有効にするログを選択します。 この例では、*kube-audit* および *kube-audit-admin* ログを有効にします。 一般的なログには、*kube-apiserver*、*kube-controller-manager*、および *kube-scheduler* が含まれます。 Log Analytics ワークスペースが有効になった後、構成画面に戻り、収集されるログを変更することもできます。

7. 準備ができたら **[保存]** を選択し、選択したログの収集を有効にします。
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Azure portal の [診断設定の追加] 画面のスクリーンショット。宛先として [Log Analytics ワークスペースに送信する]、ログとして &quot;kube-audit&quot; および &quot;kube-audit-admin&quot; が選択されています":::

## <a name="log-categories"></a>ログのカテゴリ

Kubernetes によって書き込まれたエントリに加えて、プロジェクトの監査ログには AKS からのエントリも含まれます。

監査ログは、*kube-audit*、*kube-audit-admin*、*guard* の 3 つのカテゴリに記録されます。

- *kube-audit* カテゴリには、すべての監査イベントのすべての監査ログ データが含まれており、これには *get*、*list*、*create*、*update*、*delete*、*patch*、および *post* が含まれます。
- *kube-audit-admin* カテゴリは、*kube* ログ カテゴリのサブセットです。 *kube-audit-admin* は、*get* および *list* の監査イベントをログから除外することで、ログの数を大幅に削減します。
- *guard* カテゴリはマネージド Azure AD および Azure RBAC 監査です。 マネージド Azure AD の場合: トークン入力、ユーザー情報出力。Azure RBAC の場合: アクセス レビューの入力と出力。

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>AKS クラスター上のテスト ポッドのスケジュール設定

ログを生成するには、AKS クラスター内に新しいポッドを作成します。 基本的な NGINX インスタンスを作成するには、次の YAML マニフェストの例を使用できます。 任意のエディターで `nginx.yaml` という名前のファイルを作成し、次の内容を貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

次の例のように、[kubectl create][kubectl-create] コマンドを使ってポッドを作成し、YAML ファイルを指定します。

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>収集したデータの表示

診断ログが有効になって表示されるまでには、最大 10 分かかる場合があります。

> [!NOTE]
> コンプライアンスまたはその他の目的ですべての監査ログ データが必要な場合は、データを収集して BLOB ストレージなどの安価なストレージに保存します。 *kube-audit-admin* ログ カテゴリを使用して、監視およびアラートの目的で意味のある監査ログ データのセットを収集して保存します。

Azure portal で、AKS クラスターに移動し、左側にある **[ログ]** を選択します。 *[クエリの例]* ウィンドウが表示されている場合は閉じます。

左側で、**[ログ]** を選択します。 *kube-audit* ログを表示するには、テキスト ボックスに次のクエリを入力します。

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

おそらく、多くのログが返されます。 クエリの対象を絞り込み、前の手順で作成した NGINX ポッドに関するログを表示するには、次の例のように *where* ステートメントを追加して、*nginx* を検索します。

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

*kube-audit-admin* ログを表示するには、テキスト ボックスに次のクエリを入力します。

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

この例では、クエリは *kube-audit-admin* のすべての作成ジョブを示しています。おそらく多くの結果が返されますが、クエリの対象を絞り込み、前の手順で作成した NGINX ポッドに関するログを表示するには、次の例のように *where* ステートメントを追加して、*nginx* を検索します。

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


クエリの実行やログ データの絞り込みの方法について詳しくは、「[Log Analytics のログ検索で収集されたデータの表示または分析][analyze-log-analytics]」をご覧ください。

## <a name="log-event-schema"></a>ログのイベント スキーマ

AKS では、次のイベントがログに記録されます。

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [ハートビート][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Perf][log-schema-perf]

## <a name="log-roles"></a>ログのロール

| Role                     | 説明 |
|--------------------------|-------------|
| *aksService*             | コントロール プレーン操作に対する監査ログの表示名 (hcpService から) |
| *masterclient*           | az aks get-credentials から取得する証明書 MasterClientCertificate に対する監査ログの表示名 |
| *nodeclient*             | エージェント ノードによって使用される ClientCertificate に対する表示名 |

## <a name="next-steps"></a>次のステップ

この記事では、AKS クラスターの Kubernetes コントロール プレーン コンポーネントのログを有効にして表示する方法について説明しました。 さらに詳細な監視方法やトラブルシューティング方法については、[Kubelet ログの表示][kubelet-logs]と [SSH ノード アクセスの有効化][aks-ssh]に関する記事をご覧ください。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/logs/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/logs/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf