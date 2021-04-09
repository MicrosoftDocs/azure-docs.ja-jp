---
title: サイズ設定のガイダンス
description: Azure Arc 対応データ サービスのデプロイのサイズを計画します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e7f2e445c3e4e8df7420c0587e156968f3a2c92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542679"
---
# <a name="sizing-guidance"></a>サイズ設定のガイダンス

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>サイズ設定のガイダンスの概要

Azure Arc データ サービスのデプロイを計画する場合は、Azure Arc データ コントローラーを実行するために必要なコンピューティング、メモリ、ストレージの適切な量、およびデプロイする SQL Managed Instance と PostgreSQL Hyperscale サーバー グループの数を計画する必要があります。  Azure Arc 対応データ サービスは Kubernetes にデプロイされるため、コンピューティング ノードまたはストレージを追加することにより、時間の経過に伴って Kubernetes クラスターに容量を追加する柔軟性が得られます。  このガイドでは、最小要件に関するガイダンスのほか、いくつかの一般的な要件のために推奨されるサイズに関するガイダンスを提供します。

## <a name="general-sizing-requirements"></a>サイズ設定の一般的な要件

> [!NOTE]
> この記事で説明する概念になじみがない場合は、[Kubernetes リソースのガバナンス](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)および [Kubernetes のサイズ表記](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)の詳細を参照できます。

コア数の値は 1 以上の整数値である必要があります。

デプロイに azdata を使用する場合、メモリの値は 2 つの数値の積で、つまり Ki、Mi、または Gi のサフィックスを使用して指定する必要があります。

制限値は常に、要求値 (指定されている場合) を超えている必要があります。

コアの制限値は、SQL Managed Instance と PostgreSQL Hyperscale サーバー グループに対する課金可能なメトリックです。

## <a name="minimum-deployment-requirements"></a>最小のデプロイ要件

Azure Arc 対応データ サービスの最小デプロイ サイズは、Azure Arc データ コントローラーに加えて、2 つのワーカー ノードを使用した 1 つの SQL Managed Instance と 1 つの PostgreSQL Hyperscale サーバー グループと考えることができます。  この構成では、Kubernetes クラスター上の _使用可能な_ 容量である少なくとも 16 GB RAM と 4 コアが必要です。  8 GB RAM と 4 コアという最小の Kubernetes ノード サイズと、すべての Kubernetes ノードにわたって使用可能な 16 GB RAM の合計容量があることを確認する必要があります。  たとえば、32 GB RAM と 4 コアのノードを 1 つ使用することも、それぞれが 16 GB RAM と 4 コアのノードを 2 つ使用することもできます。

ストレージのサイズ設定の詳細については、[ストレージ構成](storage-configuration.md)に関する記事を参照してください。

## <a name="data-controller-sizing-details"></a>データ コントローラーのサイズ設定の詳細

データ コントローラーは、API、コントローラー サービス、ブートストラップ、監視データベースおよびダッシュボードを提供するために Kubernetes クラスターにデプロイされるポッドのコレクションです。  次の表は、メモリおよび CPU 要求の既定値と制限を示しています。

|ポッド名|CPU 要求|メモリ要求|CPU の制限|メモリの制限|メモ|
|---|---|---|---|---|---|
|**bootstrapper**|100m|100Mi|200m|200Mi||
|**control**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10m|100Mi|100m|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100m|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100m|200Mi|200m|300Mi|metricsdc は、クラスター内の各 Kubernetes ノード上に作成されるデーモンセットです。  この表に示されている数値は _ノードあたり_ です。 データ コントローラーを作成する前にデプロイ プロファイル ファイルで allowNodeMetricsCollection = false を設定した場合、metricsdc デーモンセットは作成されません。|
|**metricsui**|20m|200Mi|500m|200Mi||
|**mgmtproxy**|200m|250Mi|500m|500Mi||

デプロイ プロファイル ファイルまたはデータ コントローラー YAML ファイルで controldb の既定の設定をオーバーライドし、ポッドを制御できます。  例:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

ストレージのサイズ設定の詳細については、[ストレージ構成](storage-configuration.md)に関する記事を参照してください。

## <a name="sql-managed-instance-sizing-details"></a>SQL Managed Instance のサイズ設定の詳細

各 SQL Managed Instance には、次の最小リソース要求が必要です。
- メモリ:2Gi
- コア:1

作成される各 SQL Managed Instance ポッドには、次の 3 つのコンテナーがあります。

|コンテナー名|CPU 要求|メモリ要求|CPU の制限|メモリの制限|メモ|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|指定なし|指定なし|fluentbit コンテナー リソース要求は、SQL Managed Instance に対して指定された要求 _に追加_ されます。||
|arc-sqlmi|ユーザー指定または指定なし。|ユーザー指定または指定なし。|ユーザー指定または指定なし。|ユーザー指定または指定なし。||
|collectd|指定なし|指定なし|指定なし|指定なし||

すべての永続ボリュームの既定のボリューム サイズは 5Gi です。

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>PostgreSQL Hyperscale サーバー グループのサイズ設定の詳細

各 PostgreSQL Hyperscale サーバー グループ ノードには、次の最小リソース要求が必要です。
- メモリ:256Mi
- コア:1

作成される各 PostgreSQL Hyperscale サーバー グループ コーディネーターまたはワーカー ポッドには、次の 3 つのコンテナーがあります。

|コンテナー名|CPU 要求|メモリ要求|CPU の制限|メモリの制限|メモ|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|指定なし|指定なし|fluentbit コンテナー リソース要求は、PostgreSQL Hyperscale サーバー グループ ノードに対して指定された要求 _に追加_ されます。|
|postgres|ユーザー指定または指定なし。|ユーザー指定または 256Mi (既定値)。|ユーザー指定または指定なし。|ユーザー指定または指定なし。||
|telegraf|指定なし|指定なし|指定なし|指定なし||

## <a name="cumulative-sizing"></a>累積的なサイズ設定

Azure Arc 対応データ サービスに必要な環境の全体的なサイズは、主に、作成されるデータベース インスタンスの数とサイズの関数です。  インスタンスの数が増えたり減ったりするほか、各データベース インスタンスに必要なリソースの量も変化することがわかっているため、全体的なサイズを事前に予測することは困難な場合があります。

特定の Azure Arc 対応データ サービス環境のベースラインのサイズは、4 コアと 16 GB RAM を必要とするデータ コントローラーのサイズです。  そこから、データベース インスタンスに必要なコアとメモリの累積合計を追加していくことができます。  SQL Managed Instance の場合、ポッドの数は、作成される SQL Managed Instance の数と同じです。  PostgreSQL Hyperscale サーバー グループの場合、ポッドの数は、ワーカー ノードの数にコーディネーター ノード用の 1 を加えた数と同じです。  たとえば、3 つのワーカー ノードを含む PostgreSQL サーバー グループがある場合、ポッドの合計数は 4 になります。

各データベース インスタンスに対して要求するコアとメモリに加えて、エージェント コンテナー用に 250m のコアと 250Mi の RAM を追加する必要があります。

サイズ計算の例を次に示します。

要件:

- **"SQL1"** : 1 つの SQL Managed Instance、16 GB RAM、4 コア
- **"SQL2"** : 1 つの SQL Managed Instance、256 GB RAM、16 コア
- **"Postgres1"** : 1 つの PostgreSQL Hyperscale サーバー グループ、12 GB RAM と 4 コアの 4 つのワーカー

サイズ計算:

- "SQL1" のサイズは次のとおりです。1 つのポッド * ポッドごとにエージェント用の ([16 Gi RAM、4 コア] + [250Mi RAM、250m コア]) = 16.25 Gi RAM、4.25 コア。
- "SQL2" のサイズは次のとおりです。1 つのポッド * ポッドごとにエージェント用の ([256 Gi RAM、16 コア] + + [250Mi RAM、250m コア]) = 256.25 Gi RAM、16.25 コア。
- SQL 1 と SQL 2 の合計サイズは次のとおりです。(16.25 GB + 256.25 Gi) = 272.5 GB RAM、(4.25 コア + 16.25 コア) = 20.5 コア。

- "Postgres1" のサイズは次のとおりです。(4 つのワーカー ポッド + 1 つのコーディネーター ポッド) * ポッドごとにエージェント用の ([12 GB RAM、4 コア] + [250Mi RAM、250m コア]) = 61.25 GB RAM、21.25 コア。

- データベース インスタンスに必要な合計容量は次のとおりです。SQL 用の 272.5 GB RAM、20.5 コア + PostgreSQL Hyperscale 用の 61.25 GB RAM、21.25 コア = 333.75 GB RAM、42.5 コア。

- データベース インスタンスとデータ コントローラーに必要な合計容量は次のとおりです。データベース インスタンス用の 333.75 GB RAM、42.5 コア + データ コントローラー用の 16 GB RAM、4 コア = 349.75 GB RAM、46.5 コア。

ストレージのサイズ設定の詳細については、[ストレージ構成](storage-configuration.md)に関する記事を参照してください。

## <a name="other-considerations"></a>その他の考慮事項

特定のデータベース インスタンスのコアまたは RAM に対するサイズ要求は、クラスター内の Kubernetes ノードの使用可能な容量を超えることができない点に注意してください。  たとえば、Kubernetes クラスター内にある最大の Kubernetes ノードが 256 GB RAM と 24 コアである場合は、512 GB RAM と 48 コアの要求でデータベース インスタンスを作成することができません。  

作成されるポッドを Kubernetes が効率的にスケジュールできるようにしたり、必要に応じてエラスティック スケーリングやより長期間の拡張を可能にしたりするために、Kubernetes ノードにわたって使用可能な容量の少なくとも 25% を保持することをお勧めします。  

サイズ計算では、Kubernetes システム ポッドのリソース要件や、同じ Kubernetes クラスター上で Azure Arc 対応データ サービスと容量を共有している可能性があるその他のすべてのワークロードを忘れずに追加してください。

計画メンテナンス中や障害が継続している間も高可用性を維持するには、特定の時点でクラスター内の Kubernetes ノードの少なくとも 1 つが使用不可になることを計画する必要があります。  Kubernetes は、メンテナンスまたは障害のために停止された、特定のノードで実行されていたポッドを再スケジュールしようとします。  残りのノードに使用可能な容量が存在しない場合は、使用可能な容量が再び存在するようになるまで、これらのポッドは作成のために再スケジュールされません。  大規模なデータベース インスタンスの場合は、特に注意してください。  たとえば、大規模なデータベース インスタンスのリソース要件を満たすだけの十分な大きさを持つ Kubernetes ノードが 1 つしか存在しない場合、そのノードに障害が発生すると、Kubernetes はそのデータベース インスタンス ポッドを別の Kubernetes ノードにスケジュールできません。

常に [Kubernetes クラスター サイズの最大制限](https://kubernetes.io/docs/setup/best-practices/cluster-large/)を念頭においてください。

Kubernetes 管理者が名前空間/プロジェクトに[リソース クォータ](https://kubernetes.io/docs/concepts/policy/resource-quotas/)を設定している可能性があります。  データベース インスタンスのサイズを計画する場合は、これらを念頭においてください。
