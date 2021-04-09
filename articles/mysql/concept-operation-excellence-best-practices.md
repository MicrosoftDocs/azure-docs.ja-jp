---
title: MySQL サーバーの運用上のベスト プラクティス - Azure Database for MySQL
description: この記事では、Azure で MySQL データベースを運用するためのベスト プラクティスについて説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96354910"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Azure Database for MySQL でのサーバー運用のベスト プラクティス - 単一サーバー

Azure Database for MySQL を使用する場合のベスト プラクティスについて説明します。 Microsoft は、プラットフォームに新しい機能を追加していく中で、このセクションで詳しく説明するベスト プラクティスを改善することに注力していきます。

## <a name="azure-database-for-mysql-operational-guidelines"></a>Azure Database for MySQL の運用ガイドライン 

Azure Database for MySQL を使用してデータベースのパフォーマンスを向上させるために従う必要がある運用ガイドラインを次に示します。 

* **コロケーション**:ネットワーク待ち時間を短縮するために、クライアントとデータベース サーバーを同じ Azure リージョンに配置します。

* **メモリ、CPU、ストレージの使用状況を監視する**:システムのパフォーマンスと可用性を維持できるようにするために、使用パターンが変化したとき、またはデプロイの容量に近づいたときに通知を受け取るように [アラートを設定](howto-alert-on-metric.md)できます。 

* **DB インスタンスをスケールアップする**:ストレージ容量の上限に近づいてきたら [スケールアップ](howto-create-manage-server-portal.md)できます。 アプリケーションからの予期しない需要増加に対応するために、ストレージとメモリにはある程度の余裕を持たせる必要があります。 また、ストレージの上限に近づいたときに、サービスによってストレージが自動的にスケーリングされるようにするために、[ストレージの自動拡張](howto-auto-grow-storage-portal.md)機能を "オン" にすることもできます。 

* **バックアップを構成する**:ビジネスの要件に基づいて、[ローカルまたは geo 冗長バックアップ](howto-restore-server-portal.md#set-backup-configuration)を有効にします。 また、バックアップがビジネス継続性のために利用できる期間に基づいて、保有期間を変更します。 

* **I/O 容量を増やす**:データベース ワークロードにより、プロビジョニングしたよりも多くの I/O が必要になる場合、データベースの復旧またはその他のトランザクション操作は低速になります。 サーバー インスタンスの I/O 容量を増やすには、次のいずれかまたはすべてを実行します。 

    * Azure Database for MySQL は、プロビジョニングされた GB ストレージあたり 3 IOPS の比率での IOPS スケーリングを提供します。 パフォーマンスを向上させるために、[プロビジョニングされたストレージを増やして](howto-create-manage-server-portal.md#scale-storage-up) IOPS をスケーリングします。 

    * プロビジョニングされた IOPS ストレージを既に使用している場合は、[追加のスループット容量](howto-create-manage-server-portal.md#scale-storage-up)をプロビジョニングします。 

* **コンピューティングをスケーリングする**:また、データベースのワークロードは、CPU またはメモリが原因で制限されることがあります。これにより、トランザクション処理に重大な影響が及ぶ可能性があります。 コンピューティング (価格レベル) は、[汎用またはメモリ最適化](concepts-pricing-tiers.md)サービス レベル間でのみスケールアップまたはスケールダウンできることに注意してください。 

* **フェールオーバーのテスト**:サーバー インスタンスのフェールオーバーを手動でテストして、自分のユース ケースに対してプロセスでどの程度時間がかかるか把握します。また、サーバー インスタンスにアクセスするアプリケーションがフェールオーバー後に新しいサーバー インスタンスに自動的に接続できることを確認します。

* **主キーを使用する**:Azure Database for MySQL で操作を行うときに、テーブルに主キーまたは一意キーがあることを確認します。 これにより、バックアップやレプリカなどの作成に大いに役立ち、パフォーマンスを向上させることができます。

* **TTL 値を構成する**:クライアント アプリケーションがサーバー インスタンスのドメイン ネーム サービス (DNS) データをキャッシュする場合は、有効期限 (TTL) の値を 30 秒未満に設定します。 フェールオーバー後にサーバー インスタンスの基になる IP アドレスが変更される可能性があります。このため、DNS データを長期間キャッシュすると、アプリケーションがサービスに存在しなくなった IP アドレスに接続しようとした場合に接続エラーが発生する可能性があります。

* 接続プールを使用して[接続数の上限](concepts-server-parameters.md#max_connections)に達しないようにします。また、再試行ロジックを使用して断続的な接続の問題を回避します。 

* レプリカを使用する場合は、プライマリ サーバーと読み取り可能なセカンダリ レプリカ サーバーの間で[負荷を分散させるために ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) を使用します。 セットアップ手順については、こちらを参照してください。 </br> 

* リソースをプロビジョニングする際に、Azure Database for MySQL の[自動拡張を有効にした](howto-auto-grow-storage-portal.md)ことを確認してください。 これによって、追加コストが発生することはなく、発生する可能性があるストレージのボトルネックからデータベースが保護されます。 </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Azure Database for MySQL で InnoDB を使用する

*   `ibdata1` 機能を使用する場合、システム テーブルスペースのデータ ファイルは、テーブルからデータを削除したり、file-per-table `tablespaces` にテーブルを移動したりしても、圧縮または消去することはできません。

* データベースのサイズが 1 TB を超える場合は、**innodb_file_per_table** `tablespace` でテーブルを作成する必要があります。 1 つのテーブル サイズが 1 TB を超える場合は、[パーティション](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) テーブルを使用する必要があります。

*   多数の `tablespace` があるサーバーでは、MySQL の起動時またはフェールオーバー時のシーケンシャル テーブルスペース スキャンが原因でエンジンの起動が非常に低速になります。 

* テーブルの合計数が 500 未満の場合は、テーブルを作成する前に innodb_file_per_table = ON を設定します。

* データベースに 500 を超えるテーブルがある場合は、個々のテーブルのテーブル サイズを確認します。 大規模なテーブルの場合でも、システム テーブルスペース ファイルが最大ストレージ制限に達しないようにするために、file-per-table テーブルスペースを使用することを検討してください。

> [!NOTE]
> サイズが 5 GB 未満のテーブルの場合は、システム テーブルスペースを使用することを検討してください。 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* 1 TB を超える可能性がある非常に大規模なテーブルがある場合は、テーブルの作成時にテーブルを[パーティション分割](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html)します。

* 複数の MySQL サーバーを使用して、それらのサーバーにテーブルを分散させます。 約 10000 以上のテーブルがある場合、1 つのサーバーにテーブルを配置しすぎないようにしてください。 

## <a name="next-steps"></a>次の手順
- [Azure Database for MySQL のパフォーマンスのベスト プラクティス](concept-performance-best-practices.md)
- [Azure Database for MySQL の監視のベスト プラクティス](concept-monitoring-best-practices.md)
