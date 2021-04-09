---
title: アプリ開発のベスト プラクティス - Azure Database for MySQL
description: Azure Database for MySQL を使用してアプリケーションを構築する場合のベスト プラクティスについて説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 7f98e14c2eae133941f3fc87247a427198ebcdc2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611965"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Azure Database for MySQL を使用してアプリケーションを構築するためのベスト プラクティス 

ここでは、Azure Database for MySQL を使用してクラウド対応アプリケーションを構築する際に役立つベスト プラクティスをいくつか紹介します。 これらのベスト プラクティスにより、アプリの開発時間を短縮できます。 

## <a name="configuration-of-application-and-database-resources"></a>アプリケーションとデータベースのリソースの構成

### <a name="keep-the-application-and-database-in-the-same-region"></a>同一リージョン内にアプリケーションとデータベースを維持する
アプリケーションを Azure にデプロイするときに、すべての依存関係が同一のリージョン内にあることを確認します。 リージョン間または可用性ゾーン間にインスタンスを分散すると、ネットワーク待機時間が発生し、アプリケーションの全体的なパフォーマンスに影響を及ぼす可能性があります。 

### <a name="keep-your-mysql-server-secure"></a>MySQL サーバーの安全を維持する
MySQL サーバーを、[セキュリティで保護](./concepts-security.md)してパブリック アクセスできないように構成します。 サーバーをセキュリティで保護するには、次のいずれかのオプションを使用します。 
- [ファイアウォール規則](./concepts-firewall-rules.md)
- [仮想ネットワーク](./concepts-data-access-and-security-vnet.md) 
- [Azure Private Link](./concepts-data-access-security-private-link.md)

セキュリティ確保のために、必ず、MySQL サーバーを SSL 経由で接続し、TLS1.2 を使用するように MySQL サーバーとアプリケーションを構成する必要があります。 [SSL/TLS の構成方法](./concepts-ssl-connection-security.md)に関する記事を参照してください。 

### <a name="tune-your-server-parameters"></a>サーバー パラメーターを調整する
読み取り負荷の高いワークロードのチューニングのサーバー パラメーターについては、`tmp_table_size` と `max_heap_table_size` を使用することでパフォーマンスを向上させることができます。 これらの変数に必要な値を計算するには、接続ごとのメモリ値の合計とベース メモリを調べます。 `tmp_table_size` を除く接続ごとのメモリ パラメーターの合計とベース メモリを組み合わせたものが、サーバーの合計メモリに相当します。

`tmp_table_size` と `max_heap_table_size` の考えられる最大サイズを計算するには、次の数式を使用します。

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> 合計メモリは、プロビジョニングされた仮想コア全体でサーバーによって使用されるメモリの総量を示します。  たとえば、General Purpose 2 仮想コア Azure Database for MySQL サーバーでは、合計メモリは、5 GB * 2 になります。 各レベルのメモリの詳細については、[価格レベル](./concepts-pricing-tiers.md)のドキュメントを参照してください。
>
> ベース メモリは、サーバーの起動時に MySQL によって初期化され、割り当てられるメモリ変数 (`query_cache_size` や `innodb_buffer_pool_size` など) を示します。 接続ごとのメモリ (`sort_buffer_size` や `join_buffer_size` など) は、クエリで必要な場合にのみ割り当てられるメモリです。

### <a name="create-non-admin-users"></a>管理者以外のユーザーを作成する 
各データベースの[管理者以外のユーザーを作成](./howto-create-users.md)します。 通常、ユーザー名は、データベース名として識別されます。

### <a name="reset-your-password"></a>パスワードをリセットする
Azure portal を使用して、MySQL サーバーの[パスワードをリセットする](./howto-create-manage-server-portal.md#update-admin-password)ことができます。 

運用データベースのサーバー パスワードをリセットすると、アプリケーションが停止する可能性があります。 アプリケーションのユーザーに対する影響を最小限に抑えるために、ピーク時以外に運用ワークロードのパスワードをリセットすることをお勧めします。

## <a name="performance-and-resiliency"></a>パフォーマンスと回復性 
アプリケーションのパフォーマンス問題をデバッグするために役立ついくつかのツールと手法を次に示します。

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>パフォーマンス問題を特定する低速クエリ ログを有効にする
サーバーで[低速クエリ ログ](./concepts-server-logs.md)と[監査ログ](./concepts-audit-logs.md)を有効にできます。 低速クエリ ログを分析すると、トラブルシューティングの目的でパフォーマンスのボトルネックを特定するのに役立ちます。 

監査ログは、Azure Monitor ログ、Azure Event Hubs、ストレージ アカウントでの Azure Diagnostics ログを通じて入手することもできます。 [クエリのパフォーマンス問題をトラブルシューティングする方法](./howto-troubleshoot-query-performance.md)に関する記事を参照してください。

### <a name="use-connection-pooling"></a>接続プールの使用
データベース接続の管理が、全体としてアプリケーションのパフォーマンスに大きな影響を与える場合があります。 パフォーマンスを最適化するには、接続を確立する回数を削減し、キー コード パスで接続を確立する時間を短縮する必要があります。 回復性とパフォーマンスを向上するには、[接続プール](./concepts-connectivity.md#access-databases-by-using-connection-pooling-recommended)を使用して Azure Database for MySQL に接続します。 

[ProxySQL](https://proxysql.com/) 接続プーラーを使用して、接続を効率的に管理できます。 接続プーラーを使用すると、アイドル状態の接続を削減でき、既存の接続を再利用することで問題を回避できます。 詳細については、[ProxySQL のセットアップ方法](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842)に関する記事を参照してください。 

### <a name="retry-logic-to-handle-transient-errors"></a>一時的エラーを処理する再試行ロジック
アプリケーションで、データベースへの接続が断続的に切断されるか、または失われる[一時的エラー](./concepts-connectivity.md#handling-transient-errors)が発生する可能性があります。 このような状況では、5 から 10 秒で 1、2 回再試行した後、サーバーは起動して稼働します。 

最初に再試行する前に、5 秒間待つことをお勧めします。 その後、待機時間を 60 秒まで段階的に増やして、各再試行を行います。 再試行の最大回数を制限します。その時点で、アプリケーションによって操作が失敗したと見なされ、さらに調査できるようになります。 詳細については、[接続エラーをトラブルシューティングする方法](./howto-troubleshoot-common-connection-issues.md)に関する記事を参照してください。 

### <a name="enable-read-replication-to-mitigate-failovers"></a>フェールオーバーを軽減するために読み取りレプリケーションを有効にする
フェールオーバー シナリオには、[データイン レプリケーション](./howto-data-in-replication.md)を使用できます。 読み取りレプリカを使用している場合、ソースとレプリカのサーバー間で自動フェールオーバーは発生しません。 

レプリケーションは非同期であるため、ソースとレプリカの間にラグがあります。 ネットワーク ラグは、ソース サーバーで実行されているワークロードのサイズやデータセンター間の待機時間など、さまざまな要因によって影響を受ける可能性があります。 ほとんどの場合、レプリカの遅延は数秒から数分の範囲になります。

## <a name="database-deployment"></a>データベースのデプロイ 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>CI/CD デプロイ パイプラインで MySQL タスク用に Azure データベースを構成する
場合によっては、変更をデータベースにデプロイする必要があります。 このような場合、[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) を使用して、継続的インテグレーション (CI) および継続的デリバリー (CD) を使用でき、[MySQL サーバー](/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment)のタスクを使用して、データベースに対してカスタム スクリプトを実行すると、これを更新することができます。

### <a name="use-an-effective-process-for-manual-database-deployment"></a>データベースの手動デプロイに効果的なプロセスを使用する 
手動でデータベースをデプロイする場合、ダウンタイムを最小限に抑えるか、デプロイが失敗するリスクを低減するために、次の手順に従ってください。 

1. [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) または [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html) を使用して、新しいデータベースに運用データベースのコピーを作成します。 
2. データベースに必要な新しいスキーマの変更または更新で新しいデータベースを更新します。 
3. 運用データベースを読み取り専用状態にします。 デプロイが完了するまで、運用データベースに対して書き込み操作を行わないでください。 
4. 手順 1 で新しく更新したデータベースを使用してアプリケーションをテストします。
5. アプリケーションの変更をデプロイし、アプリケーションが最新の更新を含む新しいデータベースを使用するようになったことを確認します。 
6. 変更をロールバックできるように、古い運用データベースを保持します。 その後、古い運用データベースを削除するか、必要に応じて Azure Storage にエクスポートするかを評価できます。 

>[!NOTE]
>アプリケーションが e コマース アプリのようなもので、読み取り専用状態にできない場合、バックアップを作成した後、変更を運用データベースに直接デプロイします。 一部のユーザーで要求の失敗が発生する可能性があるので、影響を最小限に抑えるために、これらの変更は、アプリへのトラフィックが少ないピーク時以外に行う必要があります。 
>
>アプリケーション コードで、失敗した要求も処理されることを確認してください。

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>MySQL ネイティブ メトリックを使用して、ワークロードがメモリ内の一時テーブルのサイズを超えているかどうかを確認する
読み取り負荷の高いワークロードの場合、MySQL サーバーに対して実行されるクエリは、メモリ内の一時テーブルのサイズを超える可能性があります。 読み取り負荷の高いワークロードにより、サーバーが、一時テーブルをディスクに書き込むように切り替えられ、アプリケーションのパフォーマンスに影響を与える可能性があります。 一時テーブルのサイズを超えた結果としてサーバーがディスクに書き込んでいるかどうかを判断するには、次のメトリックを調べます。

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
`created_tmp_disk_tables` メトリックは、ディスク上に作成されたテーブルの数を示します。 `created_tmp_table` メトリックは、ワークロードが与えられたときにメモリ内に形成する必要がある一時テーブルの数を示します。 特定のクエリの実行で一時テーブルを使用するかどうかを判断するには、クエリで [EXPLAIN](https://dev.mysql.com/doc/refman/8.0/en/explain.html) ステートメントを実行します。 一時テーブルを使用してクエリが実行される場合、`extra` 列の詳細には、`Using temporary` が示されます。

クエリがディスクへの書き込みを行うワークロードの割合を計算するには、次の数式でメトリック値を使用します。

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

理想的には、この割合は 25% 未満である必要があります。 割合が 25% 以上であることがわかった場合は、tmp_table_size と max_heap_table_size の 2 つのサーバー パラメーターを変更することをお勧めします。

## <a name="database-schema-and-queries"></a>データベース スキーマとクエリ

データベース スキーマとクエリを作成する場合に留意するいくつかのヒントを次に示します。

### <a name="use-the-right-datatype-for-your-table-columns"></a>テーブル列に適したデータ型を使用する
格納するデータの種類に基づいて適切なデータ型を使用すると、ストレージを最適化し、不正なデータ型が原因で発生する可能性のあるエラーを削減することができます。

### <a name="use-indexes"></a>インデックスを使用する
クエリの速度の低下を回避するには、インデックスを使用します。 インデックスを使用すると、特定の列を含む行をすばやく見つけることができます。 [MySQL でのインデックスの使用方法](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)に関する記事を参照してください。

### <a name="use-explain-for-your-select-queries"></a>SELECT クエリに EXPLAIN を使用する
クエリを実行するための MySQL の実行内容について分析情報を取得するには、`EXPLAIN` ステートメントを使用します。 これは、クエリのボトルネックまたは問題を検出するのに役立ちます。 [EXPLAIN を使用してクエリのパフォーマンスをプロファイリングする方法](./howto-troubleshoot-query-performance.md)に関する記事を参照してください。