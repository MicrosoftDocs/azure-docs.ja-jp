---
title: Azure Database for MySQL の読み取りレプリカ。
description: この記事では、Azure Database for MySQL の読み取りレプリカについて説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6e33c7571dc735ce9984a0ce1b37275a6c4c7eca
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888473"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Azure Database for MySQL の読み取りレプリカ

読み取りレプリカ機能を使用すると、Azure Database for MySQL サーバー (マスター) から、同じ Azure リージョン内にある最大 5 つの読み取り専用サーバー (レプリカ) にデータをレプリケートできます。 読み取り専用レプリカは、MySQL エンジンのネイティブなバイナリ ログ (binlog) ファイルの位置ベースのレプリケーション テクノロジを使用して、非同期で更新されます。 binlog レプリケーションの詳細については、[MySQL binlog レプリケーションの概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)に関する記事を参照してください。

Azure Database for MySQL サービスで作成されるレプリカは、標準またはスタンドアロンの MySQL サーバーと同様に管理できる新しいサーバーです。 読み取りレプリカごとに、仮想コア内のプロビジョニング済みコンピューティングとプロビジョニング済みストレージ (GB/月) に対して課金されます。

MySQL レプリケーションの機能と問題の詳細については、[MySQL レプリケーションに関するドキュメント](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)を参照してください。

## <a name="when-to-use-read-replicas"></a>読み取りレプリカを使用する場合

読み取りの負荷が高いアプリケーションとワークロードの処理には、読み取り専用レプリカを使用できます。 読み取りレプリカは、読み取りと書き込みの両方に 1 台のサーバーのみを使用した場合よりも、使用できる読み取り能力を高めるのに役立ちます。 書き込みワークロードをマスターに振り向けながら、読み取りワークロードはレプリカに分離することができます。

BI ワークロードおよび分析ワークロードでレポート用のデータ ソースとして使用するのが、読み取りレプリカの一般的なシナリオです。

## <a name="considerations-and-limitations"></a>考慮事項と制限事項

### <a name="pricing-tiers"></a>価格レベル

読み取りレプリカは現在、General Purpose 価格レベルとメモリ最適化価格レベルでのみ使用できます。

### <a name="master-server-restart"></a>マスター サーバーの再起動

既存のレプリカがないマスターのレプリカを作成すると、マスターは最初に、レプリケーションの準備をするために再起動します。 これを考慮して、これらの操作はオフピーク時に実行してください。

### <a name="stopping-replication"></a>レプリケーションの停止

マスターとレプリカ サーバーの間のレプリケーションは停止することができます。 レプリケーションを停止すると、マスターとレプリカ サーバーの間にあるレプリケーション関係がなくなります。

レプリケーションが停止されると、レプリカ サーバーはスタンドアロン サーバーになります。 スタンドアロン サーバー内のデータは、"レプリケーションの停止" コマンドが開始された時点でレプリカで使用可能だったデータです。 スタンドアロン サーバーにはマスター サーバーへの変更が反映されなくなっています。 このサーバーをもう一度レプリカにすることはできません。

### <a name="replicas-are-new-servers"></a>レプリカは新規サーバー

レプリカは、新しい Azure Database for MySQL サーバーとして作成されます。 既存のサーバーをレプリカに変更することはできません。

### <a name="replica-server-configuration"></a>レプリカ サーバーの構成

レプリカ サーバーは、マスターと同じサーバー構成を使用して作成されます。たとえば、以下の構成が含まれます。

- 価格レベル 
- コンピューティング世代
- 仮想コア
- Storage
- バックアップのリテンション期間
- バックアップ冗長オプション
- MySQL エンジンのバージョン
- ファイアウォール規則

レプリカの作成の完了後、マスター サーバーとは別に、価格レベル (Basic への変更および Basic からの変更を除く)、コンピューティング世代、仮想コア、ストレージ、バックアップ保有期間を変更できます。

### <a name="master-server-configuration"></a>マスター サーバーの構成

マスターのサーバー構成 (例:  仮想コアやストレージ) が更新されると、レプリカの構成も、同じかそれ以上の値に更新する必要があります。 これを行わないと、レプリカ サーバーはマスターに加えられた変更を継続的に反映することができず、結果的にクラッシュする可能性があります。

レプリカ サーバーの作成後にマスター サーバーに追加された新しいファイアウォール規則は、レプリカにレプリケートされません。 この新しいファイアウォール規則を使用してレプリカを更新する必要があります。

### <a name="deleting-the-master-server"></a>マスター サーバーの削除

マスター サーバーが削除されると、すべての読み取りレプリカへのレプリケーションが停止されます。 これらのレプリカはスタンドアロン サーバーになります。 マスター サーバー自体は削除されます。

### <a name="user-accounts"></a>ユーザー アカウント

マスター サーバー上のユーザーは、読み取りレプリカにレプリケートされます。 マスター サーバー上で使用可能なユーザー アカウントを使って読み取りレプリカにのみ接続できます。

### <a name="other"></a>その他

- グローバル トランザクション ID (GTID) はサポートされていません。
- レプリカのレプリカの作成はサポートされていません。
- インメモリ テーブルを使用すると、レプリカの同期が解除される可能性があります。これは、MySQL レプリケーション テクノロジの制限事項です。 詳細については、[MySQL のリファレンス ドキュメント](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html)をお読みください。
- レプリカ サーバーの作成後にマスター サーバー上の [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) パラメーターをチューニングすると、レプリカの同期が解除される可能性があります。レプリカ サーバーでは異なるテーブル スペースが認識されません。
- マスター サーバーのテーブルに主キーがあることを確認します。 主キーがないと、マスターとレプリカ間でレプリケーションの待機時間が発生する可能性があります。
- MySQL レプリケーションの制限事項の完全な一覧については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)をご確認ください。


## <a name="next-steps"></a>次の手順

- [Azure portal を使用して読み取りレプリカの作成と管理](howto-read-replicas-portal.md)を行う方法について確認する。
- [Azure CLI を使用して読み取りレプリカの作成と管理](howto-read-replicas-cli.md)を行う方法について確認する。
