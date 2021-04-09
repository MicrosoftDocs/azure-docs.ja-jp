---
title: Container insights の Prometheus 統合を構成する | Microsoft Docs
description: この記事では、Kubernetes クラスターで Prometheus からメトリックをスクレイピングするために、Container insights エージェントを構成する方法について説明します。
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 8affeb472b9452e4d234e99e5ea6bb4509770fac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731733"
---
# <a name="configure-scraping-of-prometheus-metrics-with-container-insights"></a>Container insights で Prometheus メトリックのスクレイピングを構成する

[Prometheus](https://prometheus.io/) は、人気のあるオープン ソースのメトリック監視ソリューションであり、[Cloud Native Compute Foundation](https://www.cncf.io/) の一部です。 Container insights には、Prometheus メトリックを収集するためのシームレスなオンボード エクスペリエンスが用意されています。 通常、Prometheus を使用するには、ストアとともに Prometheus サーバーを設定し、管理する必要があります。 Azure Monitor と統合することにより、Prometheus サーバーは必要ありません。 エクスポーターまたはポッド (アプリケーション) を使用して Prometheus メトリック エンドポイントを公開するだけで、Container insights 向けのコンテナー化されたエージェントがメトリックを自動的にスクレイピングできます。 

![Prometheus のコンテナー監視アーキテクチャ](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Prometheus メトリックのスクレーピングでサポートされているエージェントの最小バージョンは ciprod07092019 以降であり、`KubeMonAgentEvents` テーブルへの構成エラーおよびエージェント エラーの書き込みでサポートされているエージェント バージョンは ciprod10112019 です。 Azure Red Hat OpenShift および Red Hat OpenShift v4 の場合は、エージェント バージョン ciprod04162020 以降です。 
>
>エージェント バージョンおよび各リリースに含まれている内容の詳細については、「[エージェントのリリース ノート](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)」を参照してください。 
>お使いのエージェントのバージョンを確認するには、 **[ノード]** タブでノードを選択し、プロパティ ウィンドウで **[エージェント イメージ タグ]** プロパティの値に注目します。

Prometheus メトリックのスクレーピングは、次にホストされている Kubernetes クラスターでサポートされています。

- Azure Kubernetes Service (AKS)
- Azure Stack またはオンプレミス
- Azure Red Hat OpenShift バージョン 3.x
- Azure Red Hat OpenShift と Red Hat OpenShift バージョン 4.x

### <a name="prometheus-scraping-settings"></a>Prometheus のスクレーピングの設定

Prometheus からのメトリックのアクティブなスクレーピングは、次の 2 つの観点から実行されます。

* クラスター全体 - リストされたサービスのエンドポイントからの HTTP URL と検出ターゲット。 たとえば、kube-dns や kube-state-metrics などの k8s サービスや、アプリケーションに固有のポッド注釈などです。 このコンテキストで収集されるメトリックは、ConfigMap のセクション *[Prometheus data_collection_settings.cluster]* で定義されています。
* ノード全体 - リストされたサービスのエンドポイントからの HTTP URL と検出ターゲット。 このコンテキストで収集されるメトリックは、ConfigMap のセクション *[Prometheus_data_collection_settings.node]* で定義されています。

| エンドポイント | Scope | 例 |
|----------|-------|---------|
| ポッド注釈 | クラスター全体 | 注釈: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes サービス | クラスター全体 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/エンドポイント | ノードごと、またはクラスター全体 (あるいは両方) | `http://myurl:9101/metrics` |

URL を指定すると、Container insights によりエンドポイントのみがススクレイピングされます。 Kubernetes サービスが指定されると、クラスター DNS サーバーを使用してサービス名が解決されて IP アドレスが取得された後で、解決されたサービスがスクレーピングされます。

|Scope | Key | データ型 | 値 | 説明 |
|------|-----|-----------|-------|-------------|
| クラスター全体 | | | | メトリックのエンドポイントを収集するには、次の 3 つの方法のいずれかを指定します。 |
| | `urls` | String | コンマ区切りの配列 | HTTP エンドポイント (指定された IP アドレスまたは有効な URL パス)。 (例: `urls=[$NODE_IP/metrics]`)。 $NODE_IP は、Container insights の特定のパラメーターであり、ノードの IP アドレスの代わりに使用できます。 すべて大文字である必要があります。) |
| | `kubernetes_services` | String | コンマ区切りの配列 | kube-state-metrics からメトリックを収集する Kubernetes サービスの配列。 例: `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | Boolean | true または false | クラスター全体の設定で `true` に設定すると、Container insights エージェントにより、次の Prometheus 注釈についてクラスター全体の Kubernetes ポッドが収集されます。<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true または false | ポッドの収集を有効にします。 `monitor_kubernetes_pods` が `true` に設定されていること。 |
| | `prometheus.io/scheme` | String | http または https | 既定値は HTTP 経由での収集です。 必要であれば、`https`に設定します。 | 
| | `prometheus.io/path` | String | コンマ区切りの配列 | メトリックのフェッチ元の HTTP リソース パス。 メトリック パスが `/metrics` ではない場合は、この注釈でそれを定義します。 |
| | `prometheus.io/port` | String | 9102 | 収集するポートを指定します。 ポートが設定されていない場合、既定値は 9102 になります。 |
| | `monitor_kubernetes_pods_namespaces` | String | コンマ区切りの配列 | Kubernetes ポッドからメトリックをスクレーピングするための名前空間の許可リスト。<br> たとえば、`monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` のように指定します。 |
| ノード全体 | `urls` | String | コンマ区切りの配列 | HTTP エンドポイント (指定された IP アドレスまたは有効な URL パス)。 (例: `urls=[$NODE_IP/metrics]`)。 $NODE_IP は、Container insights の特定のパラメーターであり、ノードの IP アドレスの代わりに使用できます。 すべて大文字である必要があります。) |
| ノード全体またはクラスター全体 | `interval` | String | 60s | 収集間隔の既定値は 1 分 (60 秒) です。 *[prometheus_data_collection_settings.node]* または *[prometheus_data_collection_settings.cluster]* に対する収集を、s、m、h などの時間単位に変更できます。 |
| ノード全体またはクラスター全体 | `fieldpass`<br> `fielddrop`| String | コンマ区切りの配列 | リスティングの許可 (`fieldpass`) および禁止 (`fielddrop`) を設定することにより、エンドポイントから特定のメトリックを収集する、または収集しないように指定できます。 許可リストを最初に設定する必要があります。 |

ConfigMaps はグローバル リストであり、エージェントに適用できる ConfigMap は 1 つだけです。 別の ConfigMaps でコレクションを上書きすることはできません。

## <a name="configure-and-deploy-configmaps"></a>ConfigMap を構成してデプロイする

次のクラスター用の ConfigMap 構成ファイルを構成するには、次の手順を行います。

* Azure Kubernetes Service (AKS)
* Azure Stack またはオンプレミス
* Azure Red Hat OpenShift バージョン 4.x と Red Hat OpenShift バージョン 4.x

1. テンプレート ConfigMap の yaml ファイルを[ダウンロード](https://aka.ms/container-azm-ms-agentconfig)し、container-azm-ms-agentconfig.yaml として保存します。

   >[!NOTE]
   >Azure Red Hat OpenShift を使用する場合は、ConfigMap テンプレートがクラスターに既に存在しているため、この手順は必要ありません。

2. Prometheus メトリックをスクレーピングするためにご自分のカスタマイズで ConfigMap yaml ファイルを編集します。

    >[!NOTE]
    >Azure Red Hat OpenShift の ConfigMap yaml ファイルを編集している場合は、最初にコマンド `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` を実行して、テキスト エディターでファイルを開きます。

    >[!NOTE]
    >調整が行われないようにするために、次の注釈 `openshift.io/reconcile-protect: "true"` を *container-azm-ms-agentconfig* ConfigMap のメタデータの下に追加する必要があります。 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Kubernetes サービス クラスター全体で収集するには、次の例を使用して ConfigMap ファイルを構成します。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - クラスター全体で特定の URL からの Prometheus メトリックの収集を構成するには、次の例を使用して ConfigMap ファイルを構成します。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - クラスター内のすべての個別ノードに対するエージェントの DaemonSet からの Prometheus メトリックの収集を構成するには、ConfigMap で次のように構成します。
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE_IP は、Container insights の特定のパラメーターであり、ノードの IP アドレスの代わりに使用できます。 すべて大文字にする必要があります。 

    - ポッドの注釈を指定して Prometheus メトリックの収集を構成するには、次の手順のようにします。

       1. ConfigMap で、次のように指定します。

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. ポッドの注釈に対して次の構成を指定します。

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          注釈があるポッドの特定の名前空間に監視を制限する (たとえば、運用ワークロード専用のポッドのみを含める) 場合は、ConfigMap で `monitor_kubernetes_pod` を `true` に設定し、スクレーピングする名前空間を指定して名前空間フィルター `monitor_kubernetes_pods_namespaces` を追加します。 たとえば、`monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` のように指定します。

3. kubectl コマンド `kubectl apply -f <configmap_yaml_file.yaml>` を実行します。
    
    例: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" created`。

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>ConfigMaps の構成とデプロイ - Azure Red Hat OpenShift v3

このセクションでは、Azure Red Hat OpenShift v3.x クラスター用に ConfigMap 構成ファイルを正しく構成するための要件と手順について説明します。

>[!NOTE]
>Azure Red Hat OpenShift v3.x では、テンプレートの ConfigMap ファイルが *openshift-azure-logging* 名前空間に作成されます。 これは、エージェントからアクティブにメトリックをスクレーピングしたり、データ収集したりするようには構成されていません。

### <a name="prerequisites"></a>前提条件

コンテナー化されたエージェントと Prometheus スクレーピング設定を構成するために、開始する前に Azure Red Hat OpenShift クラスターの顧客クラスター管理者ロールのメンバーであることを確認します。 *osa-customer-admins* グループのメンバーであることを確認するには、次のコマンドを実行します。

``` bash
  oc get groups
```

出力は次のようになります。

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

*osa-customer-admins* グループのメンバーである場合は、次のコマンドを使用して `container-azm-ms-agentconfig` ConfigMap を一覧表示できます。

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

出力は次のようになります。

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>監視を有効にする

Azure Red Hat OpenShift v3.x クラスター用の ConfigMap 構成ファイルを構成するには、次の手順を行います。

1. Prometheus メトリックをスクレーピングするためにご自分のカスタマイズで ConfigMap yaml ファイルを編集します。 ConfigMap テンプレートは、Red Hat OpenShift v3 クラスターに既に存在します。 コマンド `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` を実行して、ファイルをテキスト エディターで開きます。

    >[!NOTE]
    >調整が行われないようにするために、次の注釈 `openshift.io/reconcile-protect: "true"` を *container-azm-ms-agentconfig* ConfigMap のメタデータの下に追加する必要があります。 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Kubernetes サービス クラスター全体で収集するには、次の例を使用して ConfigMap ファイルを構成します。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - クラスター全体で特定の URL からの Prometheus メトリックの収集を構成するには、次の例を使用して ConfigMap ファイルを構成します。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - クラスター内のすべての個別ノードに対するエージェントの DaemonSet からの Prometheus メトリックの収集を構成するには、ConfigMap で次のように構成します。
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE_IP は、Container insights の特定のパラメーターであり、ノードの IP アドレスの代わりに使用できます。 すべて大文字にする必要があります。 

    - ポッドの注釈を指定して Prometheus メトリックの収集を構成するには、次の手順のようにします。

       1. ConfigMap で、次のように指定します。

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. ポッドの注釈に対して次の構成を指定します。

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          注釈があるポッドの特定の名前空間に監視を制限する (たとえば、運用ワークロード専用のポッドのみを含める) 場合は、ConfigMap で `monitor_kubernetes_pod` を `true` に設定し、スクレーピングする名前空間を指定して名前空間フィルター `monitor_kubernetes_pods_namespaces` を追加します。 たとえば、`monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` のように指定します。

2. エディターで変更内容を保存します。

構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" created`。

コマンド `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` を実行すると、更新された ConfigMap を表示できます。 

## <a name="applying-updated-configmap"></a>更新された ConfigMap を適用する

クラスターに ConfigMap を既にデプロイしていて、それを新しい構成で更新したい場合は、前に使用した ConfigMap ファイルを編集した後、同じコマンドを使って適用できます。

次の Kubernetes 環境の場合:

- Azure Kubernetes Service (AKS)
- Azure Stack またはオンプレミス
- Azure Red Hat OpenShift と Red Hat OpenShift バージョン 4.x

コマンド `kubectl apply -f <configmap_yaml_file.yaml` を実行します。 

Azure Red Hat OpenShift v3.x クラスターの場合は、コマンド `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` を実行して既定のエディターでファイルを開き、変更して保存します。

構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verify-configuration"></a>構成の確認

クラスターに構成が正常に適用されたことを検証するには、次のコマンドを使って、エージェント ポッドからのログを確認します: `kubectl logs omsagent-fdf58 -n=kube-system`。 

>[!NOTE]
>このコマンドは、Azure Red Hat OpenShift v3.x クラスターには使用できません。
> 

omsagent ポッドからの構成エラーがある場合は、出力で次のようなエラーが示されます。

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

構成変更の適用に関連するエラーも確認できます。 次のオプションを使用して、構成変更および Prometheus メトリックのスクレーピングに関する追加のトラブルシューティングを実行できます。

- エージェント ポッド ログから。同じ `kubectl logs` コマンドを使用 
    >[!NOTE]
    >このコマンドは、Azure Red Hat OpenShift クラスターには使用できません。
    > 

- ライブ データ (プレビュー) から。 ライブ データ (プレビュー) ログには、次のようなエラーが表示されます。

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Log Analytics ワークスペースの **KubeMonAgentEvents** テーブルから。 スクレーピング エラーの場合は "*警告*" の重大度、構成エラーの場合は "*エラー*" の重大度で 1 時間ごとにデータが送信されます。 エラーがない場合、テーブルのエントリには "*情報*" の重大度のデータが含まれ、エラーは報告されません。 **Tags** プロパティには、エラーが発生したポッドとコンテナー ID に関する詳細情報のほか、直近 1 時間の最初の発生、最後の発生、および発生回数も含まれます。

- Azure Red Hat OpenShift v3.x および v4.x の場合は、openshift-azure-logging のログ収集が有効になっているかどうかを確認するために、**ContainerLog** テーブルを検索して omsagent ログを確認します。

エラーがあると omsagent でファイルを解析できず、再起動されて、既定の構成が使用されます。 Azure Red Hat OpenShift v3.x 以外のクラスターでは、ConfigMap のエラーを修正した後で、次のコマンドを実行して yaml ファイルを保存し、更新された ConfigMap を適用します: `kubectl apply -f <configmap_yaml_file.yaml`。 

Azure Red Hat OpenShift v3.x の場合は、次のコマンドを実行して、更新された ConfigMap を編集して保存します: `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`。

## <a name="query-prometheus-metrics-data"></a>Prometheus メトリック データのクエリを実行する

Azure Monitor によってスクレーピングされた Prometheus メトリックと、エージェントによって報告された構成エラーまたはスクレーピング エラーを表示するには、「[Prometheus メトリック データのクエリを実行する](container-insights-log-search.md#query-prometheus-metrics-data)」と「[構成エラーまたはスクレーピング エラーのクエリを実行する](container-insights-log-search.md#query-config-or-scraping-errors)」を確認してください。

## <a name="view-prometheus-metrics-in-grafana"></a>Grafana で Prometheus メトリックを表示する

Container insights では、Grafana ダッシュボードの Log Analytics ワークスペースに格納されているメトリックの表示がサポートされています。 Grafana の[ダッシュボード リポジトリ](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)からダウンロードできるテンプレートが用意されています。これを使って作業を開始し、監視対象クラスターから追加データのクエリを実行して、カスタム Grafana ダッシュボードで視覚化する方法を学習できます。 

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

![データ インジェスト ボリュームのログ クエリの結果を示すスクリーンショット](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

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

![データ インジェスト ボリュームのログ クエリの結果](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

データの使用状況を監視する方法およびコストを分析する方法について詳しくは、「[Azure Monitor ログで使用量とコストを管理する](../logs/manage-cost-storage.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

コンテナー ワークロードからの stdout、stderr、および環境変数のエージェント収集設定を構成する方法の詳細については、[こちら](container-insights-agent-config.md)をご覧ください。