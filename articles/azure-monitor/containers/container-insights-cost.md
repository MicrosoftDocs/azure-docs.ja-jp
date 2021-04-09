---
title: Container insights の監視コスト | Microsoft Docs
description: この記事では、Container insights によって収集されるメトリックとインベントリ データの監視コストについて説明します。これにより、お客様が使用量と関連コストを管理できるようになります。
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 78387e950d476126d7c2065a530844e44fd59b4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728911"
---
# <a name="understand-monitoring-costs-for-container-insights"></a>Container insights の監視コストについて

この記事では、次の内容を理解するための、Container insights に関する価格のガイダンスを提供します。

* この分析情報を有効にする前に事前にコストを見積もる方法

* 1 つまたは複数のコンテナーに対して Container insights を有効にした後に、コストを測定する方法

* データの収集を制御してコストを削減する方法

Azure Monitor ログは、Kubernetes クラスターによって生成されたデータを収集、インデックス作成、格納します。 

Azure Monitor の価格モデルは、主に、Log Analytics ワークスペースに取り込まれたデータ量 (1 日あたりギガバイト単位) に基づいています。 Log Analytics ワークスペースのコストは、収集されるデータのボリュームだけでなく、選択したプラン、およびクラスターから生成されたデータの保持期間にも依存します。

>[!NOTE]
>すべてのサイズと価格は、見積もり用のサンプル用です。 Azure Monitor Log Analytics の価格モデルと Azure リージョンに基づく最新の価格については、Azure Monitor の[価格](https://azure.microsoft.com/pricing/details/monitor/)ページを参照してください。

次に、Container insights を使用して Kubernetes クラスターから収集されるデータの種類について説明します。このデータは、コストに影響を与え、使用状況に応じてカスタマイズできます。

- クラスター内のすべての Kubernetes 名前空間で監視されているすべてのコンテナーの stdout、stderr コンテナーのログ

- クラスターで監視されているすべてのコンテナーからのコンテナー環境変数

- 監視を必要としないクラスターの完了した Kubernetes ジョブ/ポッド

- Prometheus メトリックのアクティブなスクレイピング

- *kube-apiserver* および *kube-controller-manager* マスター コンポーネントによって生成されるログ データを分析できる、AKS クラスター内の Kubernetes マスター ノード ログの [診断ログの収集](../../aks/view-control-plane-logs.md)。

## <a name="what-is-collected-from-kubernetes-clusters"></a>Kubernetes クラスターから収集されるもの

Container insights には、Log Analytics ワークスペースにログ データとして書き込まれる、一連の収集された定義済みのメトリックとインベントリ項目が含まれます。 以下に示すすべてのメトリックは、既定で 1 分ごとに収集されます。

### <a name="node-metrics-collected"></a>収集されるノード メトリック

次に、収集されるノードごとの 24 個のメトリックの一覧を示します。

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- used (disk)
- free (disk)
- used_percent (disk)
- io_time (diskio)
- writes (diskio)
- reads (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (net)
- err_out (net)
- bytes_recv (net)
- bytes_sent (net)
- Kubelet_docker_operations (kubelet)

### <a name="container-metrics"></a>コンテナー メトリック

次に、収集されるノードごとの 8 個のメトリックの一覧を示します。

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>クラスター インベントリ

次に、既定で収集されるクラスター インベントリ データの一覧を示します。

- KubePodInventory – コンテナーごとに 1 分あたり 1
- KubeNodeInventory – 1 分ごとにノードあたり 1
- KubeServices – 1 分ごとにサービスあたり 1
- ContainerInventory – 1 分ごとにコンテナーあたり 1

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>AKS クラスターを監視するためのコストの見積もり

以下の見積もりは、次のサイズの Azure Kubernetes Service (AKS) クラスターに基づいています。 また、見積もりは、収集されたメトリックとインベントリ データに対してのみ適用されます。 コンテナー ログ (stdout、stderr、および環境変数) の場合、ワークロードによって生成されるログのサイズに基づいて変化し、見積もりからは除外されます。

次のように構成された AKS クラスターの監視を有効にした場合:

- 3 つのノード
- ノードあたり 2 つのディスク
- ノードあたり 1 つのネットワーク インターフェイス
- 20 ポッド (各ポッドに 1 つのコンテナー = 合計で 20 のコンテナー)
- 2 つの Kubernetes 名前空間
- 5 つの Kubernetes サービス (kube-system ポッド、サービス、名前空間を含む)
- 収集頻度 = 60 秒 (既定値)

1 時間あたりに生成されるデータのテーブルとボリュームは、割り当てられた Log Analytics ワークスペースで確認できます。 これらの各テーブルの詳細については、「[コンテナー レコード](container-insights-log-search.md#container-records)」を参照してください。

|テーブル | 見積もりサイズ (MB/時間) |
|------|---------------|
|Perf | 12.9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0.75 |
|KubeServices | 0.13 |
|ContainerInventory | 3.6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0.005 |

合計 = 31 MB/時間 = 23.1 GB/月 (1 か月 = 31 日)

従量課金制モデルである Log Analytics の既定の[価格](https://azure.microsoft.com/pricing/details/monitor/)を使用すると、1 か月あたりの Azure Monitor のコストを見積もることができます。 容量予約を含めた後は、選択した予約に応じて、1 か月あたりの料金が高くなります。

## <a name="controlling-ingestion-to-reduce-cost"></a>コスト削減のためのインジェスト制御

組織の異なる部署が Kubernetes インフラストラクチャと Log Analytics ワークスペースを共有しているシナリオについて考えてみましょう。 各事業単位は、Kubernetes 名前空間で区切られています。 **[データ使用状況]** Runbook を使用して、各ワークスペースで取り込まれたデータの量を視覚化できます。この Runbook は **[ブックの表示]** ドロップダウンから利用できます。

[![[ブックの表示] ドロップダウン](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


このブックを使用すると、ドキュメントに記載されている内容から独自のクエリ ライブラリを作成しなくても、データのソースを視覚化することができます。 このブックには、次のような視点で課金対象データを表示できるグラフがあります。

- ソリューションごとの取り込まれた課金対象データの合計 (GB 単位)
- コンテナー ログ (アプリケーションログ) ごとの取り込まれた課金対象のデータ
- Kubernetes 名前空間ごとの取り込まれた課金対象のコンテナー ログ
- クラスター名別に分離された、取り込まれた課金対象のコンテナー ログ データ
- ログソース エントリ別の、取り込まれた課金対象コンテナー ログ データ
- 診断マスター ノード ログ別の、取り込まれた課金対象の診断データ

[![[データ使用状況] ブック](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

ブックの権限と権限を管理する方法の詳細については、「[アクセス制御](../visualize/workbooks-access-control.md)」を参照してください。

分析を完了して、最も多くのデータ、または必要以上のデータを生成しているソースを特定したら、データ収集を再構成できます。 stdout、stderr、および環境変数の収集を構成する方法の詳細については、「[コンテナーの Azure Monitor に対するエージェントのデータ収集を構成する](container-insights-agent-config.md)」を参照してください。

次に、コストを制御するために ConfigMap ファイルを変更して、クラスターに適用できる変更の例を示します。

1. ConfigMap ファイルで次の内容を変更して、クラスター内のすべての名前空間で stdout ログを無効にします。

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. 開発名前空間 (たとえば、**dev-test**) からの stderr ログ収集を無効にし、ConfigMap ファイルの次の内容を変更して、他の名前空間からの stderr ログの収集を続行します (たとえば、**prod** および **default**)。

    >[!NOTE]
    >kube-system ログ収集は、既定では無効になっています。 既定の設定は保持され、除外名前空間の一覧に **dev-test** 名前空間を追加すると、stderr ログの収集に適用されます。

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. ConfigMap ファイルで次の内容を変更して、クラスター全体で環境変数の収集を無効にします。 これは、すべての Kubernetes 名前空間のすべてのコンテナーに適用されます。 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. 完了したジョブをクリーンアップするには、ConfigMap ファイルの次の内容を変更して、ジョブ定義のクリーンアップ ポリシーを指定します。

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

これらの変更の 1 つ以上を ConfigMaps に適用したら、「[更新された ConfigMap を適用する](container-insights-prometheus-integration.md#applying-updated-configmap)」を参照してこれをクラスターに適用します。

### <a name="prometheus-metrics-scraping"></a>Prometheus メトリックのスクレ―ピング

[Prometheus メトリック スクレイピング](container-insights-prometheus-integration.md)を利用している場合は、クラスターから収集するメトリックの数を制限するために、次の点を考慮してください。

- スクレイピングの頻度が最適に設定されていることを確認します (既定値は 60 秒)。 頻度は 15 秒に上げることができますが、スクレイピングしているメトリックがその頻度で公開されていることを確認する必要があります。 そうしないと、多くの重複したメトリックがスクレイピングされ、その間隔で Log Analytics ワークスペースに送信されますが、データ インジェストとリテンションのコストが加算される半面、その価値は下がります。 

- Container insights では、メトリック名ごとの除外リストとインクルージョン リストがサポートされています。 たとえば、クラスターの **kubedns** メトリックをスクレイピングする場合、既定ではスクレイピング対象が数百にも上る可能性がありますが、ほとんどの場合、必要なのはサブセットのみです。 スクレイピングするメトリックの一覧を指定したことを確認するか、データの取り込み量を節約するために、少数を除いて除外することもできます。 スクレイピングを有効にし、Log Analytics の請求額の負担になるだけの、これらの多くのメトリックを使用しないようにすることは簡単です。

- ポッドの注釈を使用してスクラップするときは、使用しない名前空間からポッド メトリックのスクレイピングを除外するように、名前空間でフィルター処理する必要があります (たとえば、**dev-test** 名前空間)。

## <a name="next-steps"></a>次のステップ

Container insights で収集されたデータから最近の使用パターンに基づいてどのようなコストが発生する可能性があるかを理解する方法の詳細については、[使用量の管理とコストの見積もり](../logs/manage-cost-storage.md)に関する記事を参照してください。