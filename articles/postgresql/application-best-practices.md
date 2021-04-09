---
title: アプリ開発のベスト プラクティス - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL を使用してアプリケーションを構築する場合のベスト プラクティスについて説明します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97364546"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Azure Database for PostgreSQL を使用してアプリケーションを構築するためのベスト プラクティス

ここでは、Azure Database for PostgreSQL を使用してクラウド対応アプリケーションを構築する際に役立つベスト プラクティスをいくつか紹介します。 これらのベスト プラクティスにより、アプリの開発時間を短縮できます。

## <a name="configuration-of-application-and-database-resources"></a>アプリケーションとデータベースのリソースの構成

### <a name="keep-the-application-and-database-in-the-same-region"></a>同一リージョン内にアプリケーションとデータベースを維持する
アプリケーションを Azure にデプロイするときに、すべての依存関係が同一のリージョン内にあることを確認します。 リージョン間または可用性ゾーン間にインスタンスを分散すると、ネットワーク待機時間が発生し、アプリケーションの全体的なパフォーマンスに影響を及ぼす可能性があります。

### <a name="keep-your-postgresql-server-secure"></a>PostgreSQL サーバーのセキュリティ保護を維持する
PostgreSQL サーバーを、[セキュリティで保護](./concepts-security.md)してパブリック アクセスできないように構成します。 サーバーをセキュリティで保護するには、次のいずれかのオプションを使用します。
- [ファイアウォール規則](./concepts-firewall-rules.md)
- [仮想ネットワーク](./concepts-data-access-and-security-vnet.md)
- [Azure Private Link](./concepts-data-access-and-security-private-link.md)

セキュリティ確保のために、必ず、PostgreSQL サーバーを SSL 経由で接続し、TLS 1.2 を使用するように PostgreSQL サーバーとアプリケーションを構成する必要があります。 [SSL/TLS の構成方法](./concepts-ssl-connection-security.md)に関する記事を参照してください。

### <a name="tune-your-server-parameters"></a>サーバー パラメーターを調整する
読み取り負荷の高いワークロードのチューニングのサーバー パラメーターについては、`tmp_table_size` と `max_heap_table_size` を使用することでパフォーマンスを向上させることができます。 これらの変数に必要な値を計算するには、接続ごとのメモリ値の合計とベース メモリを調べます。 `tmp_table_size` を除く接続ごとのメモリ パラメーターの合計とベース メモリを組み合わせたものが、サーバーの合計メモリに相当します。

### <a name="use-environment-variables-for-connection-information"></a>接続情報に環境変数を使用する
自分のデータベース資格情報は、アプリケーション コードに保存しないでください。 フロント エンド アプリケーションに応じて、環境変数を設定するためのガイダンスに従ってください。App Service の使用については、[アプリ設定を構成する方法](../app-service/configure-common.md#configure-app-settings)に関するページを、Azure Kuberentes Service については、[Kubernetes シークレットを使用する方法](https://kubernetes.io/docs/concepts/configuration/secret/)に関するページを参照してください。

## <a name="performance-and-resiliency"></a>パフォーマンスと回復性
アプリケーションのパフォーマンス問題をデバッグするために役立ついくつかのツールと手法を次に示します。

### <a name="use-connection-pooling"></a>接続プールの使用
接続プールを使用すると、固定された接続のセットが起動時に確立され、維持されます。 これはまた、データベース サーバー上で確立される新しい動的な接続が原因でサーバーにおいて発生するメモリの断片化を削減するのにも役立ちます。 アプリ フレームワークまたはデータベース ドライバーでそれがサポートされている場合、アプリケーション側で接続プールを構成できます。 それがサポートされていない場合は、もう 1 つのオプションとして、アプリケーションの外部で実行されデータベース サーバーに接続される [PgBouncer](https://pgbouncer.github.io/) や [Pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) などのプロキシ接続プーラー サービスを利用することをお勧めします。 PgBouncer と Pgpool はどちらも、Azure Database for PostgreSQL と連携するコミュニティ ベースのツールです。

### <a name="retry-logic-to-handle-transient-errors"></a>一時的エラーを処理する再試行ロジック
アプリケーションで、データベースへの接続が断続的に切断されるか、または失われる一時的エラーが発生する可能性があります。 このような状況では、5 から 10 秒で 1、2 回再試行した後、サーバーは起動して稼働します。 最初に再試行する前に、5 秒間待つことをお勧めします。 その後、待機時間を 60 秒まで段階的に増やして、各再試行を行います。 再試行の最大回数を制限します。その時点で、アプリケーションによって操作が失敗したと見なされ、さらに調査できるようになります。 詳細については、[接続エラーをトラブルシューティングする方法](./concepts-connectivity.md)に関する記事を参照してください。

### <a name="enable-read-replication-to-mitigate-failovers"></a>フェールオーバーを軽減するために読み取りレプリケーションを有効にする
フェールオーバー シナリオには、[データイン レプリケーション](./concepts-read-replicas.md)を使用できます。 読み取りレプリカを使用している場合、ソースおよびレプリカのサーバー間での自動フェールオーバーは行われません。レプリケーションは非同期で行われるため、ソースとレプリカの間に遅延が生じることに注意してください。 ネットワーク ラグは、ソース サーバーで実行されているワークロードのサイズやデータセンター間の待機時間など、さまざまな要因によって影響を受ける可能性があります。 ほとんどの場合、レプリカの遅延は数秒から数分の範囲になります。


## <a name="database-deployment"></a>データベースのデプロイ

### <a name="configure-cicd-deployment-pipeline"></a>CI/CD デプロイ パイプラインを構成する
場合によっては、変更をデータベースにデプロイする必要があります。 このような場合、PostgreSQL サーバーの [GitHub アクション](https://github.com/Azure/postgresql/blob/master/README.md)を介して継続的インテグレーション (CI) を使用すれば、データベースに対してカスタム スクリプトを実行することでその更新を行うことができます。

### <a name="define-manual-database-deployment-process"></a>手動によるデータベースのデプロイのプロセスを定義する
手動でデータベースをデプロイする場合、ダウンタイムを最小限に抑えるか、デプロイが失敗するリスクを低減するために、次の手順に従ってください。

- pg_dump を使用して、新しいデータベース上に運用データベースのコピーを作成します。
- データベースに必要な新しいスキーマの変更または更新で新しいデータベースを更新します。
- 運用データベースを読み取り専用状態にします。 デプロイが完了するまで、運用データベースに対して書き込み操作を行わないでください。
- 手順 1 で新しく更新したデータベースを使用してアプリケーションをテストします。
- アプリケーションの変更をデプロイし、アプリケーションが最新の更新を含む新しいデータベースを使用するようになったことを確認します。
- 変更をロールバックできるように、古い運用データベースを保持します。 その後、古い運用データベースを削除するか、必要に応じて Azure Storage にエクスポートするかを評価できます。

>  [!NOTE]
> アプリケーションが e コマース アプリのようなもので、読み取り専用状態にできない場合、バックアップを作成した後、変更を運用データベースに直接デプロイします。 一部のユーザーで要求の失敗が発生する可能性があるので、影響を最小限に抑えるために、これらの変更は、アプリへのトラフィックが少ないピーク時以外に行う必要があります。 アプリケーション コードで、失敗した要求も処理されることを確認してください。

## <a name="database-schema-and-queries"></a>データベース スキーマとクエリ
データベース スキーマとクエリを作成する場合に留意するいくつかのヒントを次に示します。

### <a name="use-bigint-or-uuid-for-primary-keys"></a>主キーに BIGINT または UUID を使用する
カスタム アプリケーションまたは一部のフレームワークを構築する場合は、主キーに `BIGINT` ではなく `INT` が使用される可能性があります。 ```INT``` を使用すると、データベースの値が ```INT``` データ型のストレージ容量を超える可能性があるというリスクが発生します。 運用アプリケーションにこの変更を加えるには時間がかかり、より長い開発時間が必要になります。 別のオプションとして、主キーに [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) を使用することもできます。この識別子には、```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` など、自動生成された 128 ビット文字列が使用されます。 [PostgreSQL データ型](https://www.postgresql.org/docs/8.1/datatype.html)の詳細を確認してください。

### <a name="use-indexes"></a>インデックスを使用する

Postgres には、さまざまな方法で使用できるさまざまな[インデックス](https://www.postgresql.org/docs/9.1/indexes.html)があります。 インデックスを使用すると、インデックスなしの場合よりもはるかに高速に、サーバーで特定の行を検索して取得することができます。 ただし、インデックスによってもデータベース サーバーにオーバーヘッドが追加されるため、インデックスが多くなりすぎないようにしてください。

### <a name="use-autovacuum"></a>自動バキュームを使用する
Azure Database for PostgreSQL サーバー上の自動バキュームを使用してご利用のサーバーを最適化することができます。 PostgreSQL を使用すると、データベースのコンカレンシーは向上しますが、更新のたびに挿入と削除が行われます。 削除の場合、レコードには、後で消去されるソフト マークが付けられます。 このようなタスクを実行するために、PostgreSQL ではバキューム ジョブを実行します。 バキュームを随時実行しないと、使用不能タプルの蓄積によって、以下のような結果が生じる可能性があります。

- データベースやテーブルの拡大など、データの肥大化。
- 最適とはいえないインデックスの増大。
- I/O の増加。

[自動バキュームを使用して最適化する方法](howto-optimize-autovacuum.md)の詳細を確認してください。

### <a name="use-pg_stats_statements"></a>pg_stats_statements の使用
pg_stat_statements は、Azure Database for PostgreSQL で既定で有効になる PostgreSQL 拡張機能です。 この拡張機能では、サーバーで実行されるすべての SQL ステートメントの実行統計を追跡する手段が提供されます。 [pg_statement の使用方法](howto-optimize-query-stats-collection.md)に関するページを参照してください。


### <a name="use-the-query-store"></a>クエリ ストアの使用
Azure Database for PostgreSQL の[クエリ ストア](./concepts-query-store.md)機能では、クエリ統計を追跡するより効果的な方法が提供されます。 pg_stats_statements を使用する代わりに、この機能を使用することをお勧めします。

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>一括挿入を最適化し、一時的なデータを使用する
一時データが関係するか、大きなデータセットを一括で挿入するワークロード操作を行う場合は、ログ記録されないテーブルの使用を検討してください。 既定で、原子性と持続性が提供されます。 原子性、一貫性、分離性、持続性で ACID プロパティが構成されます。 [一括挿入を最適化する方法](howto-optimize-bulk-inserts.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
[Postgres ガイド](http://postgresguide.com/)
