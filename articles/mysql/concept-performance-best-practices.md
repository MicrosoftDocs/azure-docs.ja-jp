---
title: パフォーマンスのベスト プラクティス - Azure Database for MySQL
description: この記事では、Azure Database for MySQL のパフォーマンスを監視して調整するための推奨事項について説明します。
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 7b5223bc08c470a0e8722b76b80473aaa235b51a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727160"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Azure Database for MySQL のパフォーマンスを最適化するためのベスト プラクティス - 単一サーバー

Azure Database for MySQL の使用時に最高のパフォーマンスを得る方法について説明します (単一サーバー)。 Microsoft は、プラットフォームに新しい機能を追加していく中で、このセクションで詳しく説明する推奨事項を改善していきます。

## <a name="physical-proximity"></a>物理的な近接

 アプリケーションとデータベースを同じリージョンにデプロイしていることを確認します。 パフォーマンス ベンチマークの実行を開始する前のクイック チェックとして、簡単な SELECT 1 クエリを使用して、クライアントとデータベース間のネットワーク待機時間を確認します。 

## <a name="accelerated-networking"></a>高速ネットワーク

Azure 仮想マシン、Azure Kubernetes、または App Services を使用する場合は、アプリケーション サーバー用に高速ネットワークを使用します。 高速ネットワークは VM へのシングル ルート I/O 仮想化 (SR-IOV) を可能にします。これにより、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされる VM タイプの最も要件の厳しいネットワーク ワークロードで使用した場合でも、待ち時間、ジッター、CPU 使用率が軽減されます。

## <a name="connection-efficiency"></a>接続の効率

新しい接続を確立することは、常にコストが高く時間のかかる作業です。 アプリケーションは、データベース接続を要求するときに、新しい接続の作成よりも、既存のアイドル状態のデータベース接続の割り当てを優先します。  適切な接続を実践するためのいくつかのオプションを次に示します。

- **ProxySQL**:組み込みの接続プールを提供する [ProxySQL](https://proxysql.com/) を使用します。また、アプリケーション コードへの変更によって、必要に応じて、複数の読み取りレプリカに対して [ワークロードを負荷分散](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)させます。

- **Heimdall Data Proxy**:また、ベンダー中立の専用プロキシ ソリューションである Heimdall Data Proxy を利用することもできます。 クエリのキャッシュと、レプリケーションのラグ検出を使用した読み取りまたは書き込みの分割がサポートされています。 [Heimdall Proxy を使用して MySQL のパフォーマンスを向上させる](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349)方法も参照してください。  

- **永続的または有効期間が長い接続**:アプリケーションで、通常、実行時間が 5 ms から 10 ms 未満の短時間のトランザクションまたはクエリが使用される場合、短時間の接続を永続的な接続に置き換えます。 短時間の接続を永続的な接続に置き換えるには、コードをわずかに変更するだけで済みますが、多くの標準的なアプリケーション シナリオでのパフォーマンス向上の点から見ると大きな影響があります。 トランザクションが完了したときにタイムアウトするか接続を閉じるように設定してください。

- **レプリカ**:レプリカを使用する場合は、[ProxySQL](https://proxysql.com/) を使用して、プライマリ サーバーと読み取り可能なセカンダリ レプリカ サーバーの間で負荷を分散させます。 [ProxySQL の設定方法](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847)を参照してください。

## <a name="data-import-configurations"></a>データ インポートの構成

- データのインポート操作を開始する前に、インスタンスをより大きい SKU サイズに一時的にスケーリングし、その後、インポートが成功したらそれをスケールダウンできます。
- オンラインまたはオフラインの移行で [Azure Database Migration Service (DMS)](https://datamigration.microsoft.com/) を使用して、最小限のダウンタイムでデータをインポートできます。 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Azure Database for MySQL のメモリに関する推奨事項

Azure Database for MySQL のパフォーマンスのベスト プラクティスは、作業セットがほぼ完全にメモリ内に存在するように、十分な RAM を割り当てることです。 

- [MySQL サーバーのメトリック](./concepts-monitoring.md)を使用して、メモリの使用率が[制限](./concepts-pricing-tiers.md)に達しているかどうかを確認します。 
- このような数値に対してアラートを設定して、サーバーが制限に達したときに迅速に対処して修正できるようにします。 定義されている制限に基づいて、データベースの SKU をスケールアップして、より大きいコンピューティング サイズにするか、またはより適切な価格レベルにすることで、パフォーマンスが大幅に向上するかどうかを確認します。 
- スケーリング操作の後に、パフォーマンスの数値が大幅に低下しなくなるまでスケールアップします。 DB インスタンスのメトリックの監視の詳細については、[MySQL DB のメトリック](./concepts-monitoring.md#metrics)に関する記事を参照してください。
 
## <a name="use-innodb-buffer-pool-warmup"></a>InnoDB バッファー プールのウォームアップの使用

Azure Database for MySQL サーバーを再起動すると、テーブルがクエリされるときにストレージ内にあるデータ ページが読み込まれるため、クエリの初回実行時の待機時間が長くなり、パフォーマンスが低下します。 これは、待機時間の影響を受けやすいワークロードでは許容できない可能性があります。 

InnoDB バッファー プールのウォームアップを利用すると、DML または SELECT 操作が対応する行にアクセスするのを待機するのではなく、再起動の前にバッファー プール内にあったディスク ページを再読み込みするので、ウォームアップ期間を短縮できます。

Azure Database for MySQL サーバーを再起動した後のウォームアップ期間を短縮できます。これは、[InnoDB バッファー プール サーバー パラメーター](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)を構成すると得られるパフォーマンス上の利点です。 InnoDB は、サーバーのシャットダウン時に各バッファー プールで最近使用されたページを一定の割合で保存し、サーバーの起動時にこれらのページを復元します。

また、サーバーの起動時間が長くなる代償として、パフォーマンスが向上することにも注意する必要があります。 このパラメーターを有効にすると、サーバーにプロビジョニングされている IOPS に応じて、サーバーの起動と再起動にかかる時間が長くなることが予想されます。 

その間はサーバーが利用できないため、起動/再起動のパフォーマンスが許容できるように、再起動時間をテストし監視することをお勧めします。 プロビジョニングされた IOPS が 1000 未満である場合 (つまり、プロビジョニングされたストレージが 335 GB 未満の場合) は、このパラメーターを使用しないことをお勧めします。

サーバーのシャットダウン時にバッファー プールの状態を保存するには、サーバー パラメーター `innodb_buffer_pool_dump_at_shutdown` を `ON` に設定します。 同様に、サーバー起動時にバッファー プールの状態を復元するには、サーバー パラメーター `innodb_buffer_pool_load_at_startup` を `ON` に設定します。 サーバー パラメーター `innodb_buffer_pool_dump_pct` の値を下げて微調整すると、起動/再起動時間への影響を制御できます。 既定では、このパラメーターは `25` に設定されます。

> [!Note]
> InnoDB バッファー プールのウォームアップ パラメーターは、最大 16 TB のストレージを持つ汎用ストレージ サーバーでのみサポートされています。 [Azure Database for MySQL コンピューティングとストレージ](./concepts-pricing-tiers.md#storage)に関する詳細情報をご覧ください。

## <a name="next-steps"></a>次の手順

- [Azure Database for MySQL を使用したサーバー操作のベスト プラクティス](concept-operation-excellence-best-practices.md) <br/>
- [Azure Database for MySQL の監視のベスト プラクティス](concept-monitoring-best-practices.md)<br/>
- [Azure Database for MySQL の概要](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>