---
title: Kubernetes クラスター ノードへの PostgreSQL Hyperscale サーバー グループの配置
description: PostgreSQL Hyperscale サーバー グループを形成する PostgreSQL インスタンスが Kubernetes クラスター ノードにどのように配置されるかについて説明します
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b88b36ba8ec1d2d612adbbf19a6cf1e91fbb2cfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377756"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの配置

この記事では、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの PostgreSQL インスタンスが、それらがホストされている Kubernetes クラスターの物理ノードにどのように配置されるかを示す例を紹介します。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>構成

この例では、4 つの物理ノードを持つ Azure Kubernetes Service (AKS) クラスターを使用しています。 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="Azure portal の 4 ノード AKS クラスター":::

Kubernetes クラスターの物理ノードを一覧表示します。 次のコマンドを実行します。

```console
kubectl get nodes
```

`kubectl` では Kubernetes クラスター内の 4 つの物理ノードが返されます。

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

アーキテクチャは次のように表すことができます。

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Kubernetes クラスター内でグループ化された 4 つのノードの論理表現":::

Kubernetes クラスターでは、1 つの Azure Arc データ コントローラーと 1 つの Azure Arc 対応 PostgreSQL Hyperscale サーバー グループをホストしています。 このサーバー グループは、3 つの PostgreSQL インスタンス (1 つのコーディネーターと 2 つのワーカー) で構成されています。

コマンドを使用してポッドを一覧表示します。

```console
kubectl get pods -n arc3
```
`kubectl` が次のように返します。

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          9h
postgres01w-0         3/3     Running   0          9h
postgres01w-1         3/3     Running   0          9h
```
これらのポッドでは、それぞれ PostgreSQL インスタンスをホストしています。 ポッドは、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを共に形成しています。

```output
Pod name        Role in the server group
postgres01c-0 Coordinator
postgres01w-0   Worker
postgres01w-1   Worker
```

## <a name="placement"></a>配置
Kubernetes でサーバー グループのポッドがどのように配置されるかを見てみましょう。 各ポッドの詳細を示し、これらのポッドが Kubernetes クラスターのどの物理ノードに配置されているかを特定します。 たとえば、コーディネーターの場合は、次のコマンドを実行します。

```console
kubectl describe pod postgres01c-0 -n arc3
```

`kubectl` が次のように返します。

```output
Name:         postgres01c-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

ポッドごとにこのコマンドを実行すると、現在の配置を次のように要約できます。

| サーバー グループのロール|サーバー グループのポッド|ポッドがホストされている Kubernetes 物理ノード |
|--|--|--|
| ワーカー|postgres01-1|aks-agentpool-42715708-vmss000002 |
| ワーカー|postgres01-2|aks-agentpool-42715708-vmss000003 |

ポッドの説明で、各ポッドによってホストされているコンテナーの名前も確認します。 たとえば、2 つ目のワーカーの場合は、次のコマンドを実行します。

```console
kubectl describe pod postgres01w-1 -n arc3
```

`kubectl` が次のように返します。

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一部である各ポッドでは、次の 3 つのコンテナーをホストしています。

|コンテナー|説明
|----|----|
|`Fluentbit` |データ * ログ コレクター: https://fluentbit.io/
|`Postgres`|Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの PostgreSQL インスタンス部分
|`Telegraf` |メトリック コレクター: https://www.influxdata.com/time-series-platform/telegraf/

アーキテクチャは次のようになります。

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="それぞれが別々のノードに配置された 3 つのポッド":::

つまり、この時点で、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを構成する各 PostgreSQL インスタンスは、Kubernetes コンテナー内の特定の物理ホストでホストされています。 この構成では、各ロール (コーディネーターとワーカー) で各物理ノードのリソースが使用されるため、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのパフォーマンスが最大限に引き出されます。 これらのリソースが PostgreSQL の複数のロール間で共有されることはありません。

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale をスケール アウトする

次に、スケール アウトしてサーバー グループに 3 つ目のワーカー ノードを追加し、何が起こるかを観察します。 4 つ目のポッドでホストされる 4 つ目の PostgreSQL インスタンスが作成されます。
スケール アウトするには、次のコマンドを実行します。

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

これにより、次の出力が生成されます。

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Azure Arc データ コントローラーにデプロイされているサーバー グループを一覧表示し、サーバー グループが 3 つのワーカーで実行されていることを確認します。 次のコマンドを実行します。

```console
azdata arc postgres server list
```

次のように、2 つのワーカーから 3 つのワーカーにスケール アウトされたことを確認します。

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

前に行ったように、サーバー グループで合計 4 つのポッドが使用されていることを確認します。

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          11h
postgres01w-0         3/3     Running   0          11h
postgres01w-1         3/3     Running   0          11h
postgres01w-2         3/3     Running   0          5m2s
```

また、新しいポッドの詳細を示して、それがホストされている Kubernetes クラスターの物理ノードを特定します。
次のコマンドを実行します。

```console
kubectl describe pod postgres01w-2 -n arc3
```

ホスト ノードの名前を特定するには、次を実行します。

```output
Name:         postgres01w-2
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

この時点で、クラスターの物理ノード上の PostgreSQL インスタンスの配置は次のようになります。

|サーバー グループのロール|サーバー グループのポッド|ポッドがホストされている Kubernetes 物理ノード
|-----|-----|-----
|コーディネーター|postgres01-0|aks-agentpool-42715708-vmss000000
|ワーカー|postgres01-1|aks-agentpool-42715708-vmss000002
|ワーカー|postgres01-2|aks-agentpool-42715708-vmss000003
|ワーカー|postgres01-3|aks-agentpool-42715708-vmss000000

また、新しいワーカー (postgres01w-2) のポッドが、コーディネーターと同じノードに配置されていることがわかります。 

アーキテクチャは次のようになります。

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="コーディネーターと同じノード上の 4 つ目のポッド":::

Kubernetes クラスター aks-agentpool-42715708-vmss000003 の残りの物理ノードに新しいワーカーまたはポッドが配置されていないのはなぜでしょうか。

これは、Kubernetes クラスターの最後の物理ノードによって、Azure Arc 対応データ サービスを実行するために必要な追加のコンポーネントをホストする複数のポッドが実際にホストされているためです。 スケジュールの時点で、追加のワーカーをホストするのに最適な候補は aks-agentpool-42715708-vmss000000 物理ノードであると、Kubernetes によって評価されました。 

上記と同じコマンドを使用して、各物理ノードによってホストされているポッドを確認できます。

|その他のポッドの名前\* |使用法|ポッドがホストされている Kubernetes 物理ノード
|----|----|----
|bootstrapper-jh48b|SQL マネージド インスタンス、PostgreSQL Hyperscale サーバー グループ、データ コントローラーなどのカスタム リソースの作成、編集、および削除を行う受信要求を処理するサービス|aks-agentpool-42715708-vmss000003
|control-gwmbs||aks-agentpool-42715708-vmss000002
|controldb-0|データ コントローラーの構成と状態を格納するために使用されるコントローラー データ ストアです。|aks-agentpool-42715708-vmss000001
|controlwd-zzjp7|データ コントローラーの可用性を監視するコントローラー "ウォッチドッグ" サービスです。|aks-agentpool-42715708-vmss000000
|logsdb-0|すべての Arc Data Services ポッドで収集されたすべてのログを格納するために使用される Elastic Search インスタンスです。 Elasticsearch は、各ポッドの `Fluentbit` コンテナーからデータを受信する|aks-agentpool-42715708-vmss000003
|logsui-5fzv5|Elastic Search データベース上にあり、Log Analytics GUI を表示するための Kibana インスタンスです。|aks-agentpool-42715708-vmss000003
|metricsdb-0|すべての Arc Data Services ポッドで収集されたすべてのメトリックを格納するために使用される InfluxDB インスタンスです。 InfluxDB は、各ポッドの `Telegraf` コンテナーからデータを受信する|aks-agentpool-42715708-vmss000000
|metricsdc-47d47|ノードに関するノードレベルのメトリックを収集するために、クラスター内のすべての Kubernetes ノードにデプロイされたデーモンセットです。|aks-agentpool-42715708-vmss000002
|metricsdc-864kj|ノードに関するノードレベルのメトリックを収集するために、クラスター内のすべての Kubernetes ノードにデプロイされたデーモンセットです。|aks-agentpool-42715708-vmss000001
|metricsdc-l8jkf|ノードに関するノードレベルのメトリックを収集するために、クラスター内のすべての Kubernetes ノードにデプロイされたデーモンセットです。|aks-agentpool-42715708-vmss000003
|metricsdc-nxm4l|ノードに関するノードレベルのメトリックを収集するために、クラスター内のすべての Kubernetes ノードにデプロイされたデーモンセットです。|aks-agentpool-42715708-vmss000000
|metricsui-4fb7l|InfluxDB データベースの上に置かれ、監視ダッシュボード GUI を表示する Grafana インスタンスです。|aks-agentpool-42715708-vmss000003
|mgmtproxy-4qppp|Grafana および Kibana のインスタンスの前に置かれている Web アプリケーション プロキシ レイヤーです。|aks-agentpool-42715708-vmss000002

> \* ポッド名のサフィックスは、デプロイによって異なります。 また、ここでは、Azure Arc データ コントローラーの Kubernetes 名前空間内でホストされているポッドのみを一覧表示します。

アーキテクチャは次のようになります。

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="さまざまなノード上の名前空間内のすべてのポッド":::

前述のように、Azure Arc 対応 Postgres Hyperscale サーバー グループのコーディネーター ノード (ポッド 1) により、サーバー グループの 3 つ目のワーカー ノード (ポッド 4) と同じ物理リソースが共有されています。 これは、ワーカー ノードの場合と比較して、コーディネーター ノードで使用されているリソースが非常に少ないため、許容されます。 このため、次の項目を慎重に選択してください。
- Kubernetes クラスターのサイズと、その各物理ノードの特性 (メモリ、仮想コア)
- Kubernetes クラスター内の物理ノードの数
- Kubernetes クラスターでホストするアプリケーションまたはワークロード

Kubernetes クラスターでホストされているワークロードの数が多すぎると、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループで調整が行われる可能性があります。 その場合、水平方向にスケーリングできるというメリットはあまり得られません。 システムから実現できるパフォーマンスは、物理ノードやストレージ システムの配置や物理的特性のみに影響されるわけではありません。 実現できるパフォーマンスは、Kubernetes クラスター内で実行されている各リソース (Azure Arc 対応 PostgreSQL Hyperscale を含む) をどのように構成するか (たとえば、メモリと仮想コアに対して設定する要求や制限) によっても影響されます。 特定の Kubernetes クラスターでホストできるワークロードの量は、Kubernetes クラスターの特性、ワークロードの性質、ユーザー数、Kubernetes クラスターの操作の実行方法などに対応して変化します。

## <a name="scale-out-aks"></a>AKS をスケール アウトする

AKS クラスターと Azure Arc 対応 PostgreSQL Hyperscale サーバーの両方を水平方向にスケーリングすることで、Azure Arc 対応 PostgreSQL Hyperscale の高パフォーマンスを最大限に活用できることを説明します。
AKS クラスターに 5 つ目のノードを追加してみましょう。

:::row:::
    :::column:::
        以前
    :::column-end:::
    :::column:::
        これらの手順の完了後、
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="追加前の Azure portal のレイアウト":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="追加後の Azure portal のレイアウト":::
    :::column-end:::
:::row-end:::

アーキテクチャは次のようになります。

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="更新後の Kubernetes クラスターの論理レイアウト":::

次のコマンドを実行して、新しい AKS 物理ノードでホストされている Arc データ コントローラーの名前空間のポッドを見てみましょう。

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

次に、システムのアーキテクチャの表現を更新しましょう。

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="クラスターの論理図上のすべてのポッド":::

Kubernetes クラスターの新しい物理ノードによって、Azure Arc データ サービスに必要なメトリック ポッドのみがホストされていることを確認できます。 この例では、Arc データ コントローラーの名前空間のみに焦点を当てており、他のポッドは表していないことに注意してください。

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Azure Arc 対応 PostgreSQL Hyperscale を再度スケール アウトする

5 つ目の物理ノードでは、まだワークロードをホストしていません。 Azure Arc 対応 PostgreSQL Hyperscale をスケール アウトすると、Kubernetes では新しい PostgreSQL ポッドの配置が最適化されます。また、より多くのワークロードが既にホストされている物理ノード上にこれを併置することはできません。 次のコマンドを実行して、Azure Arc 対応 PostgreSQL Hyperscale を 3 つのワーカーから 4 つのワーカーにスケーリングします。 操作の最後に、サーバー グループが構成され、5 つの PostgreSQL インスタンス (1 つのコーディネーターと 4 つのワーカー) に分散されます。

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

これにより、次の出力が生成されます。

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

データ コントローラーにデプロイされているサーバー グループを一覧表示し、サーバー グループが 4 つのワーカーで実行されていることを確認します。

```console
azdata arc postgres server list
```

3 つのワーカーから 4 つのワーカーにスケール アウトされたことを確認します。 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

前に行ったように、サーバー グループで 4 つのポッドが使用されていることを確認します。

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          13h
postgres01w-0         3/3     Running   0          13h
postgres01w-1         3/3     Running   0          13h
postgres01w-2         3/3     Running   0          179m
postgres01w-3         3/3     Running   0          3m13s
```

サーバー グループの形状は次のようになります。

|サーバー グループのロール|サーバー グループのポッド
|----|-----
|コーディネーター|postgres01c-0
|ワーカー|postgres01w-0
|ワーカー|postgres01w-1
|ワーカー|postgres01w-2
|ワーカー|postgres01w-3

postgres01w-3 ポッドの詳細を示して、それがホストされている物理ノードを特定しましょう。

```console
kubectl describe pod postgres01w-3 -n arc3
```

次のように、実行されているポッドを観察します。

|サーバー グループのロール|サーバー グループのポッド| Pod
|----|-----|------
|コーディネーター|postgres01c-0|aks-agentpool-42715708-vmss000000
|ワーカー|postgres01w-0|aks-agentpool-42715708-vmss000002
|ワーカー|postgres01w-1|aks-agentpool-42715708-vmss000003
|ワーカー|postgres01w-2|aks-agentpool-42715708-vmss000000
|ワーカー|postgres01w-3|aks-agentpool-42715708-vmss000004

アーキテクチャは次のようになります。

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes では、最も低い使用率でノード内の最新のポッドがスケジュールされる":::

Kubernetes では、負荷が最も少ない Kubernetes クラスターの物理ノードで、新しい PostgreSQL ポッドがスケジュールされました。

## <a name="summary"></a>まとめ

スケーラビリティと、Azure Arc 対応サーバー グループを水平方向にスケーリングする場合のパフォーマンスを最大限に活用するには、Kubernetes クラスター内での次のリソースの競合を避ける必要があります。
- 同じ Kubernetes クラスターでホストされている Azure Arc 対応 PostgreSQL Hyperscale サーバー グループとその他のワークロードの間
- Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを構成するすべての PostgreSQL インスタンス間

これは、次のいくつかの方法で実現できます。
- Kubernetes と Azure Arc 対応 Postgres Hyperscale の両方をスケール アウトする: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループをスケーリングするのと同じ方法で、Kubernetes クラスターを水平方向にスケーリングすることを検討してください。 サーバー グループに追加する各ワーカーごとに、クラスターに物理ノードを追加します。
- Kubernetes をスケール アウトせずに Azure Arc 対応 Postgres Hyperscale をスケール アウトする: Kubernetes でホストされているワークロード (Azure Arc 対応 PostgreSQL Hyperscale を含む) に適切なリソースの制約 (メモリと仮想コアに対する要求および制限) を設定することにより、Kubernetes でのワークロードのコロケーションを可能にし、リソースの競合を軽減します。 Kubernetes クラスターの物理ノードの物理的な特性が、定義したリソースの制約に従うことができるようにする必要があります。 また、ワークロードが時間の経過と共に進化するか、Kubernetes クラスターに追加されるワークロードが増えるにつれて、均衡が維持されるようにする必要もあります。
- ポッドの配置に影響を与えるには、Kubernetes メカニズム (ポッド セレクター、アフィニティ、アンチアフィニティ) を使用します。

## <a name="next-steps"></a>次の手順

[ワーカー ノードを追加して Azure Arc 対応 PostgreSQL Hyperscale サーバー グループをスケールアウトする](scale-out-postgresql-hyperscale-server-group.md)
