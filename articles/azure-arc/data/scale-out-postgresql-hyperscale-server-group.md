---
title: Azure Database for PostgreSQL Hyperscale サーバー グループをスケールアウトする
description: Azure Database for PostgreSQL Hyperscale サーバー グループをスケールアウトする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17bdae658c7095c44a7ae9f30fd85a6c45bf1546
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96779976"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>ワーカー ノードを追加して Azure Arc 対応 PostgreSQL Hyperscale サーバー グループをスケールアウトする
このドキュメントでは、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループをスケールアウトする方法について説明します。 シナリオを使用して説明します。 **シナリオを実行せずにスケールアウトする方法を確認したい場合は、「[スケール アウト](#scale-out)」の段落に進んでください**。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>作業開始
Azure Arc 対応 PostgreSQL Hyperscale または Azure Database for PostgreSQL Hyperscale (Citus) のスケーリング モデルについて既によく理解している場合は、この段落をスキップできます。 そうでない場合は、まず Azure Database for PostgreSQL Hyperscale (Citus) のドキュメント ページでこのスケーリング モデルについての説明を参照することが推奨されます。 Azure Database for PostgreSQL Hyperscale (Citus) は、Azure Arc 対応データ サービスの一部として提供されるのではなく、Azure でサービスとしてホストされるものと同じテクノロジです (Platform As A Service、PAAS とも呼ばれます)。
- [ノードとテーブル](../../postgresql/concepts-hyperscale-nodes.md)
- [アプリケーションの種類の決定](../../postgresql/concepts-hyperscale-app-type.md)
- [ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)
- [テーブルの配布と変更](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [マルチテナント データベースを設計する](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [リアルタイム分析ダッシュボードを設計する](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \*上記のドキュメントの「**Azure portal にサインインする**」と「**Azure Database for PostgreSQL - Hyperscale (Citus) を作成する**」セクションはスキップしてください。 Azure Arc デプロイの残りの手順を実装します。 これらのセクションは Azure クラウドで PaaS サービスとして提供される Azure Database for PostgreSQL Hyperscale (Citus) に固有のものですが、ドキュメントの他の部分は Azure Arc 対応 PostgreSQL Hyperscale に直接適用できます。

## <a name="scenario"></a>シナリオ
このシナリオでは、「[Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md)」ドキュメントの例として作成された PostgreSQL Hyperscale サーバー グループを参照します。

### <a name="load-test-data"></a>テスト データの読み込み
このシナリオでは、一般公開されている GitHub データのサンプルを使用します。これは、[Citus Data の Web サイト](https://www.citusdata.com/)から入手できます (Citus Data は Microsoft の一部です)。

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループに接続する

##### <a name="list-the-connection-information"></a>接続情報を一覧表示する
最初に接続情報を取得して、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループに接続します。このコマンドの一般的な形式は次のとおりです。
```console
azdata arc postgres endpoint list -n <server name>
```
次に例を示します。
```console
azdata arc postgres endpoint list -n postgres01
```

出力例:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>任意のクライアント ツールを使用して接続します。

次のクエリを実行して、現在、2 つ (またはそれ以上の Hyperscale ワーカー ノード) があり、それぞれが Kubernetes ポッドに対応していることを確認します。

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>サンプル スキーマを作成する
次のクエリを実行して、2 つのテーブルを作成します。

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

JSONB は PostgreSOL におけるバイナリ形式の JSON データ型です。 柔軟なスキーマが PostgreSOL を使用して 1 つの列に格納されます。 スキーマには、スキーマ内のすべてのキーと値をインデックス化するための GIN インデックスがあります。 GIN インデックスがあると、さまざまな条件でそのペイロードで直接簡単にクエリを実行できます。 それでは、データを読み込む前にインデックスをいくつか作成してみましょう。

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

標準テーブルをシャードするには、各テーブルに対してクエリを実行します。 シャードするテーブルと、それをシャードするキーを指定します。 user_id でイベントとユーザーのテーブルをシャード化します。

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>サンプル データを読み込む
COPYS を使用したデータの読み込み...プログラムから:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>データにクエリを実行する
次に、2 つのノードで単純なクエリにかかる時間を測定します。

```sql
SELECT COUNT(*) FROM github_events;
```
クエリの実行時間を書き留めておきます。


## <a name="scale-out"></a>スケール アウト
スケールアウト コマンドの一般的な形式は次のとおりです。
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> このプレビュー リリースでは、スケール バックはサポートされていません。 たとえば、ワーカー ノードの数を減らすことはまだできません。 これを行う必要がある場合は、データを抽出/バックアップし、サーバー グループをドロップし、少ないワーカー ノードで新しいサーバー グループを作成してから、データをインポートする必要があります。

この例では、次のコマンドを実行して、ワーカー ノードの数を 2 から 4 に増やします。

```console
azdata arc postgres server edit -n postgres01 -w 4
```

ノードを追加すると、サーバー グループが保留中状態であることが確認できます。 次に例を示します。
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

ノードが使用可能になると、Hyperscale のシャードリバランス機能が自動的に実行され、データが新しいノードに再分配されます。 スケールアウト操作は、オンライン操作です。 ノードが追加され、データがノード間で再分配されている間、データはクエリで使用可能なままになります。

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>サーバー グループの新しいシェイプを確認する (省略可能)
次のいずれかの方法を使用して、追加したワーカー ノードがサーバー グループで使用されていることを確認します。

#### <a name="with-azdata"></a>azdata を使用する:
次のコマンドを実行します。
```console
azdata arc postgres server list
```

名前空間に作成されているサーバー グループの一覧が返され、ワーカー ノードの数が示されます。 次に例を示します。
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>kubectl を使用する:
次のコマンドを実行します。
```console
kubectl get postgresql-12
```

名前空間に作成されているサーバー グループの一覧が返され、ワーカー ノードの数が示されます。 次に例を示します。
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> バージョン 12 ではなく 11 の PostgreSQL のサーバー グループを作成した場合は、代わりに次のコマンドを実行します。_kubectl get postgresql-11_

#### <a name="with-a-sql-query"></a>SQL クエリを使用する:
任意のクライアント ツールを使用してサーバー グループに接続し、次のクエリを実行します。

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>シナリオに戻る

select count クエリの実行時間をサンプル データ セットと比較する場合は、同じカウント クエリを使用します。 SQL ステートメントを変更することなく、4 つのワーカー ノードで使用できます。

```sql
SELECT COUNT(*) FROM github_events;
```
スクリプトの実行時間を書き留めておきます。


> [!NOTE]
> 環境によっては、たとえば単一ノード VM に `kubeadm` でテスト サーバー グループをデプロイした場合は、実行時間がわずかに向上することがあります。 Azure Arc 対応 PostgreSQL Hyperscale によって得られるパフォーマンス向上の種類を理解するには、次の短いビデオをご覧ください。
>* [Azure PostgreSQL Hyperscale (Citus) を使用したハイ パフォーマンスの HTAP](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Python と Azure PostgreSQL Hyperscale (Citus) を使用して HTAP アプリケーションを構築する](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>次のステップ

- [Azure Arc 対応 PostgreSQL Hyperscale サーバー グループをスケールアップおよびダウンする (メモリ、仮想コア)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) 方法を参照してください。
- Azure Arc 対応 PostgreSQL Hyperscale サーバー グループでサーバー パラメーターを設定する方法を参照してください。
- 複数の PostgreSQL Hyperscale ノードにデータを分散し、Azure Database for Postgres Hyperscale のすべての利点を活用するために、Azure Database for PostgreSQL Hyperscale の概念と使い方に関するガイドを参照してください。 :
    * [ノードとテーブル](../../postgresql/concepts-hyperscale-nodes.md)
    * [アプリケーションの種類の決定](../../postgresql/concepts-hyperscale-app-type.md)
    * [ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)
    * [テーブルの配布と変更](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [マルチテナント データベースを設計する](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [リアルタイム分析ダッシュボードを設計する](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \*上記のドキュメントの「**Azure portal にサインインする**」と「**Azure Database for PostgreSQL - Hyperscale (Citus) を作成する**」セクションはスキップしてください。 Azure Arc デプロイの残りの手順を実装します。 これらのセクションは Azure クラウドで PaaS サービスとして提供される Azure Database for PostgreSQL Hyperscale (Citus) に固有のものですが、ドキュメントの他の部分は Azure Arc 対応 PostgreSQL Hyperscale に直接適用できます。

- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
