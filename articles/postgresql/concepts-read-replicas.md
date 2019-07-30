---
title: Azure Database for PostgreSQL (単一サーバー) の読み取りレプリカ
description: この記事では、Azure Database for PostgreSQL (単一サーバー) の読み取りレプリカ機能について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/14/2019
ms.openlocfilehash: c98247b0ba8b670a59dec9aa3ec87e949f1dda78
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147929"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL (単一サーバー) の読み取りレプリカ

読み取りレプリカ機能を使用すると、Azure Database for PostgreSQL サーバーから、読み取り専用サーバーにデータをレプリケートできます。 マスター サーバーから最大 5 つのレプリカにレプリケートできます。 レプリカは、PostgreSQL エンジンのネイティブ レプリケーション テクノロジを使用して非同期的に更新されます。

> [!IMPORTANT]
> マスター サーバーと同じ Azure リージョン内、または選択した他の任意の Azure リージョン内に読み取りレプリカを作成できます。 リージョン間レプリケーションは、現在パブリック プレビュー段階です。

レプリカは、通常の Azure Database for PostgreSQL サーバーと同様に管理する新しいサーバーです。 読み取りレプリカごとに、仮想コアおよびストレージのプロビジョニング済みコンピューティング (GB/月) に対して課金されます。

[レプリカを作成して管理する](howto-read-replicas-portal.md)方法を参照してください。

## <a name="when-to-use-a-read-replica"></a>読み取りレプリカを使用する場合
読み取りレプリカ機能は、読み取り集中型ワークロードのパフォーマンスとスケールの向上に役立ちます。 書き込みワークロードをマスターに振り向けながら、読み取りワークロードはレプリカに分離することができます。

BI ワークロードおよび分析ワークロードでレポート用のデータ ソースとして使用するのが、読み取りレプリカの一般的なシナリオです。

レプリカは読み取り専用であるため、マスターへの書き込み容量の負担を直接減らすことにはなりません。 この機能は、書き込み集中型ワークロードを対象としていません。

読み取りレプリカ機能は、PostgreSQL の非同期レプリケーションを使用します。 機能は、同期レプリケーション シナリオを目的としていません。 マスターとレプリカの間には測定可能な遅延が発生します。 レプリカ上のデータは、最終的にマスター上のデータと整合します。 この機能は、この遅延に対応できるワークロードに使用してください。

読み取りレプリカを使用すると、ディザスター リカバリー計画を強化できます。 まずマスターとは異なる Azure リージョンにレプリカを作成する必要があります。 リージョンの災害が発生した場合は、そのレプリカへのレプリケーションを停止して、ワークロードをそこにリダイレクトできます。 レプリケーションを停止すると、レプリカで読み取りだけでなく書き込みの受け入れも開始できます。 詳細については、[レプリケーションの停止](#stop-replication)に関するセクションを参照してください。 

## <a name="create-a-replica"></a>レプリカの作成
マスター サーバーでは、`azure.replication_support` パラメーターが **[REPLICA]** に設定されている必要があります。 このパラメーターが変更された場合、その変更を有効にするにはサーバーの再起動が必要です。 (`azure.replication_support` パラメーターは、汎用レベルおよびメモリ最適化レベルのみに適用されます)。

レプリカ作成ワークフローを開始すると、空の Azure Database for PostgreSQL サーバーが作成されます。 新しいサーバーには、マスター サーバー上にあったデータが設定されます。 作成時間は、マスター上のデータ量と、最後の週次完全バックアップからの経過時間に依存します。 時間の範囲は、数分から数時間になる可能性があります。

すべてのレプリカでストレージの[自動拡張](concepts-pricing-tiers.md#storage-auto-grow)が有効になっています。 自動拡張機能によって、レプリカにレプリケートされるデータをレプリカで保持し続けることができ、ストレージ不足エラーを原因とするレプリケーションの中断を防ぐことができます。

読み取りレプリカ機能では、PostgreSQL の (論理レプリケーションではなく) 物理レプリケーションが使用されます。 レプリケーション スロットを使用したストリーミング レプリケーションが、既定の動作モードです。 必要に応じて、遅れを取り戻すためにログ配布が使用されます。

[Azure portal で読み取りレプリカを作成する](howto-read-replicas-portal.md)方法を確認してください。

## <a name="connect-to-a-replica"></a>レプリカへの接続
作成されたレプリカでは、マスター サーバーのファイアウォール規則または VNet サービス エンドポイントは継承されません。 これらの規則は、レプリカに対して個別に設定する必要があります。

レプリカの管理者アカウントは、マスター サーバーから継承されます。 マスター サーバー上のすべてのユーザー アカウントが、読み取りレプリカにレプリケートされます。 マスター サーバー上で使用可能なユーザー アカウントを使って読み取りレプリカにのみ接続できます。

通常の Azure Database for PostgreSQL サーバーの場合と同じように、レプリカのホスト名と有効なユーザー アカウントを使用して、レプリカに接続できます。 管理ユーザー名が **myadmin** で **my replica** という名前のサーバーの場合は、次の psql を使用してレプリカに接続できます。

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

メッセージが表示されたら、ユーザー アカウントのパスワードを入力します。

## <a name="monitor-replication"></a>レプリケーションを監視します
Azure Database for PostgreSQL には、レプリケーションを監視するための 2 つのメトリックがあります。 2 つのメトリックは **[Max Lag Across Replicas]\(レプリカ間の最大ラグ\)** と **[Replica Lag]\(レプリカ間のラグ\)** です。 これらのメソッドを表示する方法については、[読み取りレプリカのハウツー記事](howto-read-replicas-portal.md)の「**レプリカの監視**」セクションを参照してください。

**[Max lag across replicas]\(レプリカ間の最大ラグ\)** メトリックは、マスターと最も遅れているレプリカの間のラグをバイト単位で示します。 このメトリックは、マスター サーバーのみで使用できます。

**[Replica Lag]\(レプリカのラグ\)** メトリックでは最後に再生されたトランザクションからの時間が表示されます。 マスター サーバーでトランザクションが発生していない場合、メトリックにはこのタイム ラグが反映されます。 このメトリックは、レプリカ サーバーのみで使用できます。 レプリカのラグは、`pg_stat_wal_receiver` ビューから計算されます。

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

レプリカのラグがワークロードに対して許容できない値に達したときに通知されるアラートを設定します。 

さらに分析情報を得るには、マスター サーバーに対して直接クエリを実行して、すべてのレプリカでのバイト単位のレプリケーション ラグを取得します。

PostgreSQL バージョン 10 の場合:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

PostgreSQL バージョン 9.6 以前の場合:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> マスター サーバーまたは読み取りレプリカを再起動した場合、再起動して追いつくまでにかかった時間が、Replica Lag (レプリカ ラグ) メトリックに反映されます。

## <a name="stop-replication"></a>レプリケーションの停止
マスターとレプリカの間のレプリケーションを停止できます。 停止操作により、レプリカは再起動され、そのレプリケーション設定が削除されます。 マスター サーバーと読み取りレプリカの間のレプリケーションを停止すると、レプリカがスタンドアロン サーバーになります。 スタンドアロン サーバー内のデータは、レプリケーション停止コマンドが起動された時点でレプリカで使用可能だったデータです。 スタンドアロン サーバーにはマスター サーバーへの変更が反映されなくなっています。

> [!IMPORTANT]
> スタンドアロン サーバーをもう一度レプリカにすることはできません。
> 読み取りレプリカでレプリケーションを停止する前に、レプリカに必要なすべてのデータがあることを確認してください。

レプリケーションを停止すると、レプリカでは以前のマスターと他のレプリカへのリンクがすべて失われます。 マスターとレプリカの間に自動フェールオーバーはありません。 

[レプリカへのレプリケーションを停止する](howto-read-replicas-portal.md)方法を確認します。


## <a name="considerations"></a>考慮事項

このセクションでは、読み取りレプリカ機能に関する考慮事項をまとめています。

### <a name="prerequisites"></a>前提条件
読み取りレプリカを作成する前に、マスター サーバーで、`azure.replication_support` パラメーターを **REPLICA** に設定する必要があります。 このパラメーターが変更された場合、その変更を有効にするにはサーバーの再起動が必要です。 `azure.replication_support` パラメーターは、汎用レベルおよびメモリ最適化レベルのみに適用されます。

### <a name="new-replicas"></a>新しいレプリカ
読み取りレプリカは、新しい Azure Database for PostgreSQL サーバーとして作成されます。 既存のサーバーをレプリカにすることはできません。 別の読み取りレプリカのレプリカを作成することはできません。

### <a name="replica-configuration"></a>レプリカ構成
レプリカは、マスターと同じサーバー構成を使用して作成されます。 レプリカが作成されたら、マスター サーバーとは独立していくつかの設定 (コンピューティング世代、仮想コア、ストレージ、およびバックアップ保持期間) を変更できます。 価格レベルも独立して変更できます (Basic レベルへの変更や Basic レベルからの変更を除く)。

> [!IMPORTANT]
> マスター サーバー構成が新しい値に更新される前に、レプリカ構成をそれと同等以上の値に更新してください。 このアクションにより、レプリカがマスターのどのような変更にも追従できるようになります。

PostgreSQL では、読み取りレプリカの `max_connections` パラメーターの値をマスターの値以上にする必要があります。そうしないと、レプリカが起動しません。 Azure Database for PostgreSQL で、`max_connections` パラメーター値は、SKU に基づきます。 詳しくは、「[Azure Database for PostgreSQL の制限事項](concepts-limits.md)」をご覧ください。 

サーバーの値を更新しようとしていて、制限に従っていない場合、エラーが表示されます。

### <a name="maxpreparedtransactions"></a>max_prepared_transactions
読み取りレプリカの `max_prepared_transactions` パラメーターの値をマスターの値以上にすることが [PostgreSQL では必要](https://www.postgresql.org/docs/10/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS)です。そうしないと、レプリカが起動しません。 マスターで `max_prepared_transactions` を変更する場合、まずレプリカで変更します。

### <a name="stopped-replicas"></a>停止されたレプリカ
マスター サーバーと読み取りレプリカの間のレプリケーションを停止した場合、変更を適用するためにレプリカが再起動されます。 停止したレプリカは、読み取りと書き込みの両方を受け入れるスタンドアロン サーバーになります。 スタンドアロン サーバーをもう一度レプリカにすることはできません。

### <a name="deleted-master-and-standalone-servers"></a>削除されたマスターおよびスタンドアロン サーバー
マスター サーバーを削除すると、そのすべての読み取りレプリカがスタンドアロン サーバーになります。 この変更を反映するため、レプリカは再起動されます。

## <a name="next-steps"></a>次の手順
[Azure portal で読み取りレプリカを作成および管理する](howto-read-replicas-portal.md)方法を確認する。
