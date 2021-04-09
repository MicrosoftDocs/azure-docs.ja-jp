---
title: Azure Kubernetes ネットワーク ポリシー | Microsoft Docs
description: Kubernetes クラスターをセキュリティで保護するための Kubernetes ネットワーク ポリシーについて説明します。
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: a68e1a3f60930e290e97084ff2ec9350b18e2873
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594965"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes ネットワーク ポリシーの概要

ネットワーク ポリシーは、ネットワーク セキュリティ グループ (NSG) が VM にマイクロセグメンテーションを提供するのと同じように、ポッドにマイクロセグメンテーションを提供します。 Azure ネットワーク ポリシー マネージャー (Azure NPM とも呼ばれます) の実装では、標準の Kubernetes ネットワーク ポリシーの仕様がサポートされます。 ラベルを使用してポッドのグループを選択し、これらのポッドとの間のトラフィックをフィルター処理するイングレスとエグレスの規則の一覧を定義できます。 Kubernetes ネットワーク ポリシーの詳細については、[Kubernetes のドキュメント](https://kubernetes.io/docs/concepts/services-networking/network-policies/)を参照してください。

![Kubernetes ネットワーク ポリシーの概要](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure NPM 実装は、VNet 統合をコンテナーに提供する Azure CNI と連携して動作します。 NPM は現在 Linux でのみサポートされています。 実装では、定義されたポリシーに基づいて Linux IPTables で IP の許可と拒否のルールを構成することにより、トラフィックのフィルタリングを強制します。 これらのルールは、Linux IPSets を使用してグループ化されます。

## <a name="planning-security-for-your-kubernetes-cluster"></a>Kubernetes クラスターのセキュリティの計画
ご自身のクラスターにセキュリティを実装する場合は、ネットワーク セキュリティ グループ (NSG) を使用して、クラスター サブネットを出入りするトラフィック (南北のトラフィック) をフィルター処理します。 クラスター内のポッド間のトラフィック (東西のトラフィック) には Azure NPM を使用します。

## <a name="using-azure-npm"></a>Azure NPM の使用
次の方法で Azure NPM を使用して、ポッドにマイクロセグメンテーションを提供できます。

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
NPM は AKS でネイティブに使用でき、クラスターの作成時に有効にすることができます。 詳細については、「[Azure Kubernetes Service (AKS) のネットワーク ポリシーを使用して、ポッド間のトラフィックをセキュリティ保護する](../aks/use-network-policies.md)」を参照してください。

### <a name="aks-engine"></a>AKS エンジン
AKS エンジンは、Azure での Kubernetes クラスター デプロイ用 Azure Resource Manager テンプレートを生成するツールです。 クラスターの構成は、テンプレートを生成するときにツールに渡される JSON ファイルで指定されます。 サポートされているクラスター設定とその説明の完全な一覧については、Microsoft Azure Container Service エンジン - クラスター定義に関するページをご覧ください。

ACS エンジンを使用してデプロイされたクラスターでポリシーを有効にするには、クラスター定義ファイルで networkPolicy 設定の値が "azure" になるように指定します。

#### <a name="example-configuration"></a>構成例

次の JSON 構成の例では、新しい仮想ネットワークとサブネットを作成し、Azure CNI を使用して、そこに Kubernetes クラスターをデプロイします。 JSON ファイルの編集には "メモ帳" を使用することをお勧めします。 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Azure 内での自作 (DIY) Kubernetes クラスター
 DIY クラスターの場合は、最初に CNI プラグインをインストールし、クラスター内のすべての仮想マシンで有効にします。 詳細については、[自身でデプロイした Kubernetes クラスター用プラグインのデプロイ](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster)に関するページをご覧ください。

クラスターがデプロイされたら、次の `kubectl` コマンドを実行して、Azure NPM の "*デーモン セット*" をダウンロードして、クラスターに適用します。

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
また、ソリューションはオープン ソースで、コードは [Azure コンテナー ネットワーク リポジトリ](https://github.com/Azure/azure-container-networking/tree/master/npm)から入手できます。

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Azure NPM を使用したネットワーク構成の監視と視覚化
Azure NPM には、お客様の構成を監視して理解を深めることができる有益な Prometheus メトリックが含まれています。 Azure portal または Grafana Labs に組み込まれている視覚化を提供します。 これらのメトリックの収集を開始するには、Azure Monitor または Prometheus サーバーを使用します。

### <a name="benefits-of-azure-npm-metrics"></a>Azure NPM メトリックの利点
以前はユーザーがネットワーク構成について学習するには、クラスター ノード内で `iptables -L` コマンドを実行するしかなく、これによる出力は詳細で、理解が困難でした。 NPM メトリックは、ネットワーク ポリシー、IPTables ルール、および IPSets に関連する以下の利点を提供します。
- 構成をデバッグするための、これら 3 つと時間ディメンションとの関係に関する分析情報を提供します。
- すべての IPSets と各 IPSet 内のエントリの数。
- IPTable/IPSet レベルの粒度でポリシーを適用するために要する時間。
 
### <a name="supported-metrics"></a>サポートされるメトリック
サポート対象のメトリックの一覧は次のとおりです。

|メトリックの名前 |説明  |Prometheus のメトリックの種類  |ラベル  |
|---------|---------|---------|---------|
|`npm_num_policies`     |ネットワーク ポリシーの数          |ゲージ         |-         |
|`npm_num_iptables_rules`     | IPTables ルールの数     | ゲージ        |-         |         
|`npm_num_ipsets`     |IPSets の数         |ゲージ            |-         |
|`npm_num_ipset_entries`     |すべての IPSets 内の IP アドレス エントリの数         |ゲージ         |-         |
|`npm_add_policy_exec_time`     |ネットワーク ポリシーを追加するためのランタイム         |まとめ         |分位点 (0.5、0.9、または 0.99)         |
|`npm_add_iptables_rule_exec_time`     |IPTables ルールを追加するためのランタイム         |まとめ         |分位点 (0.5、0.9、または 0.99)         |
|`npm_add_ipset_exec_time`     |IPSet ルールを追加するためのランタイム         |まとめ         |分位点 (0.5、0.9、または 0.99)         |
|`npm_ipset_counts` (詳細)     |個々の IPSet 内のエントリの数         |GaugeVec         |名前とハッシュの設定         |

"exec_time" メトリックのさまざまな分位点レベルは、一般的なシナリオと最悪のシナリオを区別するのに役立ちます。

また、"exec_time" サマリー メトリックごとに "exec_time_count" と "exec_time_sum" のメトリックもあります。

メトリックは、Azure Monitor for Containers または Prometheus 全体でスクレイピングできます。

### <a name="setup-for-azure-monitor"></a>Azure Monitor のセットアップ
最初の手順は、Kubernetes クラスター用に Azure Monitor for containers を有効にします。 手順は「[Azure Monitor for containers の概要](../azure-monitor/containers/container-insights-overview.md)」から見つけることができます。 Azure Monitor for containers を有効にしたら、[Azure Monitor for containers ConfigMap](https://aka.ms/container-azm-ms-agentconfig) を構成して、NPM 統合と Prometheus NPM メトリックの収集を有効にします。 Azure monitor for containers ConfigMap には、NPM メトリックを収集するための設定を含む ```integrations``` セクションがあります。 これらの設定は、ConfigMap では既定で無効になっています。 基本設定 ```collect_basic_metrics = true``` を有効にすると、基本的な NPM メトリックが収集されます。 詳細設定 ```collect_advanced_metrics = true``` を有効にすると、基本メトリックに加えて高度なメトリックが収集されます。 

ConfigMap を編集したら、ローカルに保存し、次のように ConfigMap をクラスターに適用します。

```kubectl apply -f container-azm-ms-agentconfig.yaml``` 次に示すのは、高度なメトリックの収集が有効になっている NPM 統合を示す [Azure monitor for containers ConfigMap](https://aka.ms/container-azm-ms-agentconfig) のスニペットです。
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
高度なメトリックはオプションであり、オンにすると自動的に基本メトリックの収集が有効になります。 現在のところ、高度なメトリックには `npm_ipset_counts` のみが含まれています

[構成マップでの Azure monitor for containers のコレクション設定](../azure-monitor/containers/container-insights-agent-config.md)の詳細を参照してください。

### <a name="visualization-options-for-azure-monitor"></a>Azure Monitor の視覚化オプション
NPM メトリック コレクションを有効にすると、Azure portal で Container Insights を使用して、または Grafana 内でメトリックを表示できます。

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Azure portal の [分析情報] でのクラスターの表示
Azure portal を開きます。 クラスターの [分析情報] で、[ブック] に移動し、[ネットワーク ポリシー マネージャー (NPM) の構成] を開きます。

ブック (下の図) を表示するだけでなく、[分析情報] セクションの [ログ] で Prometheus メトリックを直接照会することもできます。 たとえば、次のクエリでは、収集されるすべてのメトリックが返されます。
| where TimeGenerated > ago(5h) | where Name contains "npm_"

また、Log Analytics でメトリックを直接照会することもできます。 詳細については、[Log Analytics クエリの使用方法](../azure-monitor/containers/container-insights-log-search.md)に関する記事を参照してください。 

#### <a name="viewing-in-grafana-dashboard"></a>Grafana ダッシュボードでの表示
[ここ](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource)に説明されているように、Grafana サーバーを設定し、Log Analytics データ ソースを構成します。 次に、[Log Analytics バックエンドを含む Grafana ダッシュボード](https://grafana.com/grafana/dashboards/10956)を Grafana Labs にインポートします。

ダッシュボードには、Azure ブックに似た視覚エフェクトがあります。 InsightsMetrics テーブルからグラフおよび NPM の視覚化メトリックにパネルを追加できます。

### <a name="setup-for-prometheus-server"></a>Prometheus サーバーのセットアップ
ユーザーによっては、Azure Monitor for containers ではなく、Prometheus サーバーを使用してメトリックを収集することも選択できます。 NPM メトリックを収集するには、スクレイプ構成に 2 つのジョブを追加する必要があります。

単純な Prometheus サーバーをインストールするには、クラスターにこのヘルム リポジトリを追加します。
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
次に、サーバーを追加します。
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
`prometheus-server-scrape-config.yaml` は次の要素で構成されます。
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


`azure-npm-node-metrics` ジョブを以下の内容に置き換えたり、Kubernetes ポッド用の既存のジョブに組み込むこともできます。
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Prometheus の視覚化オプション
Prometheus サーバーを使用する場合は、Grafana ダッシュボードのみがサポートされています。 

Grafana サーバーをまだ設定していない場合は、設定して Prometheus データ ソースを構成します。 次に、[Prometheus バックエンドを含む Grafana ダッシュボード](https://grafana.com/grafana/dashboards/13000)を Grafana Labs にインポートします。

このダッシュボードのビジュアルは、Container Insights/Log Analytics バックエンドを持つダッシュボードと同じです。

### <a name="sample-dashboards"></a>サンプル ダッシュボード
Container Insights (CI) と Grafana の NPM メトリック用のサンプル ダッシュボードを次に示します。

#### <a name="ci-summary-counts"></a>CI 集計カウント
![Azure ブック集計カウント](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>時間の経過に伴う CI カウント
[![時間の経過に伴う Azure ブックのカウント](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>CI IPSet エントリ
[![Azure ブックの IPSet エントリ](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>CI ランタイム分位点
![Azure ブック ランタイム分位点](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Grafana ダッシュボード集計カウント
![Grafana ダッシュボード集計カウント](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>時間の経過に伴う Grafana ダッシュボードのカウント
[![時間の経過に伴う Grafana ダッシュボードのカウント](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Grafana ダッシュボード IPSet エントリ
[![Grafana ダッシュボード IPSet エントリ](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana ダッシュボード ランタイム分位点
[![Grafana ダッシュボード ランタイム分位点](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>次のステップ
- [Azure Kubernetes Service](../aks/intro-kubernetes.md) について確認します。
-  [コンテナー ネットワーク](container-networking-overview.md)について確認します。
- Kubernetes クラスターまたは Docker コンテナー用の[プラグインをデプロイ](deploy-container-networking.md)します。

