---
title: コンテナーの Azure Monitor エージェントのデータ収集を構成する | Microsoft Docs
description: この記事では、コンテナーの Azure Monitor エージェントによる stdout/stderr および環境変数のログ収集の制御を構成する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: magoedte
ms.openlocfilehash: 2b601825a58fe5739a43df607067acc8d629c5f4
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558902"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>コンテナーの Azure Monitor に対するエージェントのデータ収集を構成する

コンテナーの Azure Monitor では、コンテナー化されたエージェントにより、Azure Kubernetes Service (AKS) でホストされているマネージド Kubernetes クラスターにデプロイされたコンテナー ワークロードからの stdout、stderr、および環境変数が収集されます。 また、このエージェントでは、コンテナー化されたエージェントを使って Prometheus から時系列データ (メトリックとも呼ばれます) を収集することもできます。Prometheus サーバーとデータベースを設定して管理する必要はありません。 このエクスペリエンスを制御するためのカスタム Kubernetes ConfigMaps を作成することにより、エージェントのデータ収集の設定を構成できます。 

この記事では、ConfigMap を作成し、要件に基づいてデータの収集を構成する方法を示します。

>[!NOTE]
>現時点では、Prometheus のサポートはパブリック プレビューの機能です。
>

## <a name="configmap-file-settings-overview"></a>ConfigMap ファイル設定の概要

最初から作成する必要がなく、ご自分のカスタマイズで簡単に編集できる、テンプレート ConfigMap ファイルが提供されています。 始める前に、[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) についての Kubernetes のドキュメントを確認し、ConfigMaps を作成、構成、デプロイする方法をよく理解しておく必要があります。 そうすることで、名前空間ごとまたはクラスター全体の stderr と stdout、およびクラスター内のすべてのポッド/ノードで実行されている任意のコンテナーに対する環境変数を、フィルター処理できます。

>[!IMPORTANT]
>コンテナー ワークロードからの stdout、stderr、環境変数の収集をサポートされているエージェントの最小バージョンは、ciprod06142019 以降です。 Prometheus メトリックの収集をサポートされているエージェントの最小バージョンは、ciprod07092019 以降です。 お使いのエージェントのバージョンを確認するには、 **[ノード]** タブでノードを選択し、プロパティ ウィンドウで **[エージェント イメージ タグ]** プロパティの値に注目します。  

### <a name="data-collection-settings"></a>データ収集の設定

データ収集を制御するために構成できる設定を次に示します。

|キー |データ型 |値 |説明 |
|----|----------|------|------------|
|`schema-version` |String (大文字と小文字が区別されます) |v1 |これは、この ConfigMap を解析するときにエージェントで使われるスキーマのバージョンです。 現在サポートされているスキーマのバージョンは、v1 です。 この値に対する変更はサポートされておらず、ConfigMap の評価時に拒否されます。|
|`config-version` |string | | ソース管理システム/リポジトリでこの構成ファイルのバージョンを追跡する機能をサポートします。 許容される最大文字数は 10 で、他のすべての文字は切り捨てられます。 |
|`[log_collection_settings.stdout] enabled =` |Boolean | true または false | stdout コンテナーのログ収集が有効かどうかを制御します。 `true` に設定した場合、stdout のログ収集に対して名前空間を除外しないと (後の `log_collection_settings.stdout.exclude_namespaces` 設定)、クラスター内のすべてのポッド/ノードのすべてのコンテナーから、stdout ログが収集されます。 ConfigMap で指定しない場合、既定値は `enabled = true` です。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|string | コンマ区切りの配列 |stdout のログを収集しない Kubernetes 名前空間の配列。 この設定は、`log_collection_settings.stdout.enabled` を `true` に設定した場合にのみ有効です。 ConfigMap で指定しない場合、既定値は `exclude_namespaces = ["kube-system"]` です。|
|`[log_collection_settings.stderr] enabled =` |Boolean | true または false |stderr コンテナーのログ収集が有効かどうかを制御します。 `true` に設定した場合、stdout のログ収集に対して名前空間を除外しないと (`log_collection_settings.stderr.exclude_namespaces` 設定)、クラスター内のすべてのポッド/ノードのすべてのコンテナーから、stderr ログが収集されます。 ConfigMap で指定しない場合、既定値は `enabled = true` です。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |string |コンマ区切りの配列 |stderr のログを収集しない Kubernetes 名前空間の配列。 この設定は、`log_collection_settings.stdout.enabled` を `true` に設定した場合にのみ有効です。 ConfigMap で指定しない場合、既定値は `exclude_namespaces = ["kube-system"]` です。 |
| `[log_collection_settings.env_var] enabled =` |Boolean | true または false | これは、環境変数の収集が有効かどうかを制御します。 `false` に設定すると、クラスター内のすべてのポッド/ノードで実行されているすべてのコンテナーの環境変数が収集されません。 ConfigMap で指定しない場合、既定値は `enabled = true` です。 |

### <a name="prometheus-scraping-settings"></a>Prometheus のスクレーピングの設定

![Prometheus のコンテナー監視アーキテクチャ](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

コンテナー用の Azure Monitor では、この後の表に示すように、次のメカニズムを使用した複数のスクレーピングによる Prometheus メトリックの収集を有効にするシームレスなエクスペリエンスが提供されます。 メトリックは、1 つの ConfigMap ファイルに指定された一連の設定を介して収集されます。これは、stdout、stderr、およびコンテナー ワークロードの環境変数の構成に使用されるのと同じファイルです。 

Prometheus からのメトリックのアクティブなスクレーピングは、次の 2 つの観点から実行されます。

* クラスター全体 - リストされたサービスのエンドポイントからの HTTP URL と検出ターゲット、kube-dns や kube-state-metrics などの k8s サービス、アプリケーションに固有のポッド注釈。 このコンテキストで収集されるメトリックは、ConfigMap のセクション *[Prometheus data_collection_settings.cluster]* で定義されています。
* ノード全体 - リストされたサービスのエンドポイントからの HTTP URL と検出ターゲット。 このコンテキストで収集されるメトリックは、ConfigMap のセクション *[Prometheus_data_collection_settings.node]* で定義されています。

| エンドポイント | Scope (スコープ) | 例 |
|----------|-------|---------|
| ポッド注釈 | クラスター全体 | 注釈: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000" <br>prometheus.io/scheme: "http"` |
| Kubernetes サービス | クラスター全体 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/エンドポイント | ノードごと、またはクラスター全体 (あるいは両方) | `http://myurl:9101/metrics` |

URL が指定されると、コンテナー用 Azure Monitor はエンドポイントのみをスクレーピングします。 Kubernetes サービスが指定されると、クラスター DNS サーバーを使用してサービス名が解決されて IP アドレスが取得された後で、解決されたサービスがスクレーピングされます。

|Scope (スコープ) | キー | データ型 | 値 | 説明 |
|------|-----|-----------|-------|-------------|
| クラスター全体 | | | | メトリックのエンドポイントを収集するには、次の 3 つの方法のいずれかを指定します。 |
| | `urls` | string | コンマ区切りの配列 | HTTP エンドポイント (指定された IP アドレスまたは有効な URL パス)。 (例: `urls=[$NODE_IP/metrics]`)。 ($NODE_IP は、コンテナーの Azure Monitor の特定のパラメーターであり、ノードの IP アドレスの代わりに使用できます。 すべて大文字である必要があります。) |
| | `kubernetes_services` | string | コンマ区切りの配列 | kube-state-metrics からメトリックを収集する Kubernetes サービスの配列。 例: `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | Boolean | true または false | クラスター全体の設定で `true` に設定すると、コンテナーの Azure Monitor エージェントでは、次の Prometheus 注釈についてクラスター全体の Kubernetes ポッドが収集されます。<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true または false | ポッドの収集を有効にします。 `monitor_kubernetes_pods` は `true` に設定する必要があります。 |
| | `prometheus.io/scheme` | string | http または https | 既定値は HTTP 経由での収集です。 必要であれば、`https`に設定します。 | 
| | `prometheus.io/path` | string | コンマ区切りの配列 | メトリックのフェッチ元の HTTP リソース パス。 メトリック パスが `/metrics` ではない場合は、この注釈でそれを定義します。 |
| | `prometheus.io/port` | string | 9102 | リッスンするポートを指定します。 ポートが設定されていない場合、既定値は 9102 になります。 |
| ノード全体 | `urls` | string | コンマ区切りの配列 | HTTP エンドポイント (指定された IP アドレスまたは有効な URL パス)。 (例: `urls=[$NODE_IP/metrics]`)。 ($NODE_IP は、コンテナーの Azure Monitor の特定のパラメーターであり、ノードの IP アドレスの代わりに使用できます。 すべて大文字である必要があります。) |
| ノード全体またはクラスター全体 | `interval` | string | 60s | 収集間隔の既定値は 1 分 (60 秒) です。 *[prometheus_data_collection_settings.node]* または *[prometheus_data_collection_settings.cluster]* に対する収集を、ns、us (または Âµs)、ms、s、m、h などの時間単位に変更できます。 |
| ノード全体またはクラスター全体 | `fieldpass`<br> `fielddrop`| string | コンマ区切りの配列 | リスティングの許可 (`fieldpass`) および禁止 (`fielddrop`) を設定することにより、エンドポイントから特定のメトリックを収集する、または収集しないように指定できます。 許可リストを最初に設定する必要があります。 |

ConfigMap はグローバル リストであり、エージェントに適用できる ConfigMap は 1 つだけです。 別の ConfigMap で収集を上書きすることはできません。

## <a name="configure-and-deploy-configmaps"></a>ConfigMap を構成してデプロイする

ConfigMap 構成ファイルを構成してクラスターにデプロイするには、次の手順のようにします。

1. テンプレート ConfigMap の yaml ファイルを[ダウンロード](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)し、container-azm-ms-agentconfig.yaml として保存します。  
1. ご自分のカスタマイズで ConfigMap yaml ファイルを編集します。

    - stdout のログ収集から特定の名前空間を除外するには、次の例を使ってキー/値を構成します: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    
    - 特定のコンテナーからの環境変数の収集を無効にするには、キー/値 `[log_collection_settings.env_var] enabled = true` を設定して変数の収集をグローバルに有効にした後、[こちら](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)の手順に従って特定のコンテナーの構成を完了します。
    
    - クラスター全体で stderr のログ収集を無効にするには、次の例を使ってキー/値を構成します: `[log_collection_settings.stderr] enabled = false`。
    
    - 次の例では、ConfigMap ファイルのメトリックを構成する方法 (URL のクラスター全体、エージェントの DameonSet のノード全体、またはポッド注釈の指定) を示します。

        - クラスター全体で特定の URL から Prometheus のメトリックをスクレーピングします。

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h.
         fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
         fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
         urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

        - クラスターの各ノードで実行するエージェントの DaemonSet から Prometheus のメトリックをスクレーピングします。

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings 
         [prometheus_data_collection_settings.node] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h. 
         # Node level scrape endpoint(s). These metrics will be scraped from agent's DaemonSet running in every node in the cluster 
         urls = ["http://$NODE_IP:9103/metrics"] 
         fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
         fielddrop = ["metric_to_drop"] 
        ```

        - ポッド注釈を指定して Prometheus のメトリックをスクレーピングします。

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h
         monitor_kubernetes_pods = true #replicaset will scrape Kubernetes pods for the following prometheus annotations: 
          - prometheus.io/scrape:"true" #Enable scraping for this pod 
          - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
          - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
          - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

1. 次の kubectl コマンドを実行して、ConfigMap を作成します: `kubectl apply -f <configmap_yaml_file.yaml>`。
    
    例: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" created`。

構成が正常に適用されたことを検証するには、次のコマンドを使って、エージェント ポッドからのログを確認します: `kubectl logs omsagent-fdf58 -n=kube-system`。 omsagent ポッドからの構成エラーがある場合は、出力で次のようなエラーが示されます。

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Prometheus に対する構成変更の適用に関連するエラーは、確認することもできます。  同じ `kubectl logs` コマンドを使用するエージェント ポッドからのログから、またはライブ ログからです。 ライブ ログでは、次のようなエラーが示されます。

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

エラーがあると omsagent でファイルを解析できず、再起動されて、既定の構成が使用されます。 ConfigMap でエラーを修正した後、yaml ファイルを保存し、次のコマンドを実行して更新された ConfigMap を適用します: `kubectl apply -f <configmap_yaml_file.yaml`。

## <a name="applying-updated-configmap"></a>更新された ConfigMap を適用する

クラスターに ConfigMap を既にデプロイしてあり、それを新しい構成で更新したい場合は、前に使用した ConfigMap ファイルを編集した後、同じコマンド `kubectl apply -f <configmap_yaml_file.yaml` を使って適用できます。

構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>スキーマのバージョンを確認する

サポートされている構成スキーマのバージョンは、omsagent ポッドのポッド注釈 (schema-versions) として利用できます。 次の kubectl コマンドでそれらを確認できます: `kubectl describe pod omsagent-fdf58 -n=kube-system`

注釈 schema-versions を含む次のような出力が表示されます。

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="review-prometheus-data-usage"></a>Prometheus のデータ使用状況を確認する

各メトリック サイズのインジェスト ボリューム (1 日あたりの GB) を取得して、高いかどうかを把握できるよう、次のクエリが用意されています。

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
出力では、次のような結果が示されます。

![データ インジェスト ボリュームのログ クエリの結果](./media/container-insights-agent-config/log-query-example-usage-03.png)

1 か月間の各メトリック サイズ (GB 単位) を推定し、ワークスペースで受け取る取り込まれたデータの量が多いかどうかを把握するため、次のクエリが用意されています。

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

出力では、次のような結果が示されます。

![データ インジェスト ボリュームのログ クエリの結果](./media/container-insights-agent-config/log-query-example-usage-02.png)

データの使用状況を監視する方法およびコストを分析する方法について詳しくは、「[Azure Monitor ログで使用量とコストを管理する](../platform/manage-cost-storage.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

Azure Monitor for containers には、定義済みの一連のアラートは含まれません。 [Azure Monitor for containers を使用したパフォーマンス アラートの作成](container-insights-alerts.md)に関するページを読んで、CPU やメモリの使用率が高い場合に推奨アラートを作成し、DevOps や運用プロセスまたは手順をサポートする方法について学習します。

- Azure Monitor を使用して、AKS クラスターの他の側面を監視する方法を引き続き学習するには、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページをご覧ください。

- [ログ クエリの例](container-insights-log-search.md#search-logs-to-analyze-data)を表示して、事前定義されたクエリや例を確認し、クラスターのアラート、視覚化、または分析のために評価やカスタマイズを行います。