---
title: アクティブ geo レプリケーション - Azure SQL Database | Microsoft Docs
description: アクティブ geo レプリケーションを使用して、同じデータ センターまたは異なるデータ センター (リージョン) に個々のデータベースの読み取り可能なセカンダリ データベースを作成します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 07/09/2019
ms.openlocfilehash: 4b525c3cbea600859106062ed34dc6df9622dec5
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807307"
---
# <a name="creating-and-using-active-geo-replication"></a>アクティブ geo レプリケーションの作成と使用

アクティブ geo レプリケーションは Azure SQL Database の機能です。これを使用すると、同じデータ センターまたは異なるデータ センター (リージョン) 内の SQL Database サーバー上に、個々のデータベースの読み取り可能なセカンダリ データベースを作成できます。

> [!NOTE]
> アクティブ geo レプリケーションは、マネージド インスタンスではサポートされていません。 マネージド インスタンスの地理的なフェールオーバーについては、[自動フェールオーバー グループ](sql-database-auto-failover-group.md)を使用します。

アクティブ geo レプリケーションはビジネス継続性ソリューションとして設計されています。このソリューションを使用すれば、地域災害または大規模な機能停止が発生した場合にディザスター リカバリーをアプリケーションで迅速に実行することができます。 geo レプリケーションを有効にすると、アプリケーションは別の Azure リージョンにあるセカンダリ データベースへのフェールオーバーを開始できます。 同じリージョン内または異なるリージョン内で最大 4 つのセカンダリがサポートされています。また、セカンダリを使用して読み取り専用アクセスのクエリを行うこともできます。 フェールオーバーはアプリケーションまたはユーザーによって手動で開始される必要があります。 フェールオーバー後、新しいプライマリには別の接続エンドポイントが設定されます。 次の図に、アクティブ geo レプリケーションを使用して行われる geo 冗長クラウド アプリケーションの一般的な構成を示します。

![アクティブ geo レプリケーション](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database でも自動フェールオーバー グループがサポートされています。 詳細については、[自動フェールオーバー グループ](sql-database-auto-failover-group.md)の使用に関するページを参照してください。 また、アクティブ geo レプリケーションは、Managed Instance 内で作成されたデータベースに対してはサポートされていません。 マネージド インスタンスでの[フェールオーバー グループ](sql-database-auto-failover-group.md)の使用を検討してください。

何らかの理由でご利用のプライマリ データベースにエラーが発生したか、または単にそのプライマリ データベースをオフラインにする必要がある場合、ご利用のセカンダリ データベースのいずれかへのフェールオーバーを開始することができます。 セカンダリ データベースの 1 つに対してフェールオーバーがアクティブな場合、その他すべてのセカンダリ データベースは新しいプライマリ データベースに自動的にリンクします。

サーバー上またはエラスティック プール内の個々のデータベースまたはデータベースのセットのレプリケーションとフェールオーバーは、アクティブ geo レプリケーションを使用して管理できます。 そのためには以下のものを使うことができます。

- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: 単一データベース](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: エラスティック プール](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: 単一のデータベースまたはエラスティック プール](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: 単一データベース](https://docs.microsoft.com/rest/api/sql/replicationlinks)


アクティブ geo レプリケーションは SQL Server の [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) テクノロジーを活用し、スナップショット分離を使用してプライマリ データベース上のコミットされたトランザクションを非同期的にレプリケートします。 自動フェールオーバー グループにはアクティブ geo レプリケーションに加えてグループ セマンティックが用意されていますが、同じ非同期レプリケーション メカニズムを使用します。 特定の時点におけるセカンダリ データベースは、プライマリ データベースよりもわずかに古い可能性がありますが、セカンダリ データには部分トランザクションが含まれないことが保証されます。 リージョン間で冗長性が確保されるため、自然災害、致命的なヒューマン エラー、または悪意のある行為によってデータセンター全体またはその一部の機能が完全に失われた場合でも、アプリケーションをすばやく復旧できます。 特定の RPO データについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)に関するページをご覧ください。

> [!NOTE]
> 2 つのリージョン間でネットワーク障害が発生すると、接続を再確立するために 10 秒ごとに再試行します。
> [!IMPORTANT]
> プライマリ データベースでの重要な変更が、フェールオーバー前にセカンダリに確実にレプリケートされるようにするには、強制同期を実行して、重要な変更 (パスワードの更新など) のレプリケーションを確実に行わせることができます。 強制同期を実行すると、コミットされたトランザクションがすべてレプリケートされるまで呼び出しスレッドがブロックされるため、パフォーマンスに影響します。 詳細については、[sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) に関するページをご覧ください。 プライマリ データベースと geo セカンダリの間のレプリケーションの遅延を監視するには、「[sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)」を参照してください。

次の図は、プライマリが米国中北部リージョン、セカンダリが米国中南部リージョンに構成されたアクティブ Geo レプリケーションの例を示しています。

![geo レプリケーションのリレーションシップ](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

セカンダリ データベースは読み取り可能であるため、ジョブを報告するなどの読み取り専用ワークロードの負荷を軽減するために使用できます。 アクティブ geo レプリケーションを使用している場合、セカンダリ データベースをプライマリと同じリージョンに作成できますが、致命的な障害に対するアプリケーションの復元力は改善されません。 自動フェールオーバー グループを使用している場合、セカンダリ データベースは常に異なるリージョンに作成されます。

アクティブ geo レプリケーションは、ディザスター リカバリーに加えて次のシナリオで使用できます。

- **データベースの移行**: アクティブ geo レプリケーションを使用して、最小限のダウンタイムでデータベースをあるサーバーから別のサーバーにオンラインで移行できます。
- **アプリケーションのアップグレード**: アプリケーションのアップグレード時に、セカンダリをフェールバック コピーとして余分に作成することができます。

ビジネスの継続性を実現するにあたって、データセンター間のデータベース冗長性を追加することは、ソリューションのほんの一部です。 致命的な障害の後にアプリケーション (サービス) をエンド ツー エンドで復旧するには、そのサービスと依存しているサービスを構成するすべてのコンポーネントを復旧する必要があります。 このようなコンポーネントの例には、クライアント ソフトウェア (カスタム JavaScript が設定されたブラウザーなど)、Web フロント エンド、ストレージ、DNS などがあります。 すべてのコンポーネントが同じ障害に対する復元性を備えており、アプリケーションの目標復旧時間 (RTO) 以内に利用可能になることが重要です。 そのため、依存するサービスをすべて特定し、これらのサービスが提供する保証と機能について把握しておく必要があります。 そのうえで、依存するサービスのフェールオーバー中もサービスが確実に機能するように対策を講じる必要があります。 ディザスター リカバリーのソリューション設計の詳細については、[アクティブ geo レプリケーションを使用したディザスター リカバリー用のクラウド ソリューションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)に関するページを参照してください。

## <a name="active-geo-replication-terminology-and-capabilities"></a>アクティブ geo レプリケーションの用語と機能

- **自動非同期レプリケーション**

  セカンダリ データベースは、既存のデータベースに追加することによってのみ作成できます。 セカンダリは任意の Azure SQL Database サーバーに作成できます。 作成した後、プライマリ データベースからコピーしたデータをセカンダリ データベースに設定できます。 このプロセスはシード処理と呼ばれます。 オンライン セカンダリ データベースが作成およびシードされた後、プライマリ データベースの更新がセカンダリ データベースに非同期で自動的にレプリケートされます。 非同期レプリケーションでは、トランザクションはプライマリ データベースでコミットされた後に、セカンダリ データベースにレプリケートされます。

- **読み取り可能なセカンダリ データベース**

  アプリケーションは、プライマリ データベースへのアクセスに使用されているのと同じ、または異なるセキュリティ プリンシパルを使用して、セカンダリ データベースにアクセスして読み取り専用操作を実行できます。 セカンダリ データベースは、セカンダリ上で実行されるクエリによってプライマリ (ログの再生) の更新プログラムのレプリケーションが遅延しないように、スナップショット分離モードで動作します。

> [!NOTE]
> プライマリ データベースでスキーマの更新がある場合、セカンダリ データベースのログ再生は遅延します。 後者の場合は、セカンダリ データベースでスキーマ ロックが必要です。
> [!IMPORTANT]
> geo レプリケーションを使用して、同じリージョンにプライマリとしてセカンダリ データベースを作成できます。 このセカンダリを使用して、同じリージョン内の読み取り専用ワークロードの負荷分散を行います。 ただし、同じリージョン内のセカンダリ データベースは、障害からの回復性を提供しないため、ディザスター リカバリーの適切なフェールオーバー ターゲットではありません。 また、可用性ゾーンの分離も保証されません。 [ゾーン冗長の構成](sql-database-high-availability.md#zone-redundant-configuration)を持つ Business Critical レベルまたは Premium サービス レベルを使用して、可用性ゾーンの分離を行ってください。   
>

- **計画されたフェールオーバー**

  計画されたフェールオーバーは、完全同期が完了した後に、プライマリ データベースとセカンダリ データベースのロールを切り替えます。 これはデータの損失を発生させないオンライン操作です。 操作の時間は、同期が必要なプライマリ上のトランザクション ログのサイズによって異なります。 計画されたフェールオーバーは次のシナリオ向けに設計されています。(a) データの損失が許容されない場合に、運用環境で DR ドリルを実行する (b) 異なるリージョンにデータベースを再配置する、(c) 機能停止に対処した後でプライマリ リージョンにデータベースを戻す (フェールバック)。

- **計画されていないフェールオーバー**

  計画されていないフェールオーバーや強制フェールオーバーでは、プライマリと同期せずに、セカンダリをすぐにプライマリ ロールに切り替えます。 プライマリにコミットされていてもセカンダリにレプリケートされていないトランザクションは失われます。 この操作は、停止中、プライマリにアクセスできないがデータベースの可用性を早急に回復しなければならない場合の復旧方法として設計されています。 元のプライマリは、オンラインに戻ると、自動的に再接続され、新しいセカンダリとなります。 フェールオーバー前の同期されていないトランザクションはすべてバックアップ ファイルに保持されますが、競合を避けるために新しいプライマリとは同期されません。 これらのトランザクションは、プライマリ データベースの最新バージョンに手動でマージする必要があります。
 
- **複数のセカンダリ データベース**

  プライマリごとに、最大で 4 つのセカンダリ データベースを作成できます。 セカンダリ データベースが 1 つしか存在しない場合に障害が発生すると、新しいセカンダリ データベースが作成されるまで、アプリケーションは高いリスクにさらされます。 セカンダリ データベースが複数存在する場合、セカンダリ データベースのいずれかに障害が発生しても、アプリケーションは保護された状態が維持されます。 読み取り専用のワークロードをスケール アウトするために、追加のセカンダリを使用することもできます。

  > [!NOTE]
  > グローバルに分散されたアプリケーションの構築にアクティブ geo レプリケーションを使用し、4 つを超えるリージョンにデータへの読み取り専用アクセスを提供する必要がある場合は、セカンダリのセカンダリ (チェーンと呼ばれるプロセス) を作成できます。 この方法により、データベースのレプリケーションを事実上無制限に拡張できます。 さらに、チェーンによりプライマリ データベースからのレプリケーションのオーバーヘッドが軽減されます。 トレードオフは、リーフの多いセカンダリ データベースでレプリケーションのラグが増えることです。

- **エラスティック プール内のデータベースの geo レプリケーション**

  各セカンダリ データベースは、個別にエラスティック プールに参加しても、どのエラスティック プールにもまったく参加しなくてもかまいません。 セカンダリ データベースごとのプールの選択は独立しており、他のどのセカンダリ データベースの構成にも依存しません (プライマリでもセカンダリでも)。 各エラスティック プールは 1 つのリージョンにのみ含まれるので、同じトポロジ内の複数のセカンダリ データベースがエラスティック プールを共有することはできません。


- **ユーザー制御のフェールオーバーとフェールバック**

  アプリケーションまたはユーザーによって、セカンダリ データベースをプライマリ ロールにいつでも明示的に切り替えることができます。 実際のシステム停止時には、"計画されていない" オプションを使用して、セカンダリをプライマリにすぐに昇格させる必要があります。 障害が発生したプライマリが回復して、再び使用可能になると、回復したプライマリは自動的にセカンダリとしてマークされ、新しいプライマリによって最新の状態になります。 レプリケーションは非同期であるため、最新の変更をセカンダリにレプリケートする前に、プライマリに障害が発生した場合、計画されていないフェールオーバー時に少量のデータの失われる可能性があります。 複数のセカンダリを持つプライマリがフェールオーバーすると、システムは自動的にレプリケーション関係を再構成して、いかなるユーザー介入も必要とすることなく、残りのセカンダリを新しく昇格されたプライマリにリンクします。 フェールオーバーの原因となった障害が解決されたら、アプリケーションをプライマリ リージョンに復帰させることが望ましい場合があります。 そうするには、"計画" オプションを使用してフェールオーバー コマンドを呼び出す必要があります。

## <a name="preparing-secondary-database-for-failover"></a>フェールオーバー用セカンダリ データベースの準備

フェールオーバー後にアプリケーションが新しいプライマリにすぐアクセスできることが確実になるよう、セカンダリ サーバーとデータベースの認証要件が正しく構成されていることを確認してください。 詳細については、 [障害復旧後の SQL Database のセキュリティ](sql-database-geo-replication-security-config.md)に関するページを参照してください。 フェールオーバー後のコンプライアンスを保証するために、セカンダリ データベースでのバックアップ保持ポリシーがプライマリのそれと一致していることを確認してください。 これらの設定はデータベースの一部ではなく、レプリケートされません。 既定では、セカンダリは 7 日間の既定の PITR 保持期間で構成されます。 詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)」を参照してください。

## <a name="configuring-secondary-database"></a>セカンダリ データベースの構成

プライマリとセカンダリ、両方のデータベースが同じサービス階層を持つ必要があります。 また、セカンダリ データベースは、プライマリと同じコンピューティング サイズ (DTU または仮想コア) で作成することを強くお勧めします。 プライマリ データベースで書き込みワークロードの負荷が高くなっている場合、コンピューティング サイズがより小さなセカンダリはそのワークロードを維持できない可能性があります。 これにより、セカンダリで再実行ラグが発生し、利用不能となる可能性があります。 プライマリ データベースより処理速度が遅いセカンダリ データベースでは、フェールオーバーを強制的に実施する必要がある場合に、大きなデータ損失が発生するリスクもあります。 このようなリスクを軽減するために、効果的なアクティブ geo レプリケーションは、プライマリのログ レートをスロットルしてセカンダリが追い付けるようにします。 バランスがとれていないセカンダリ構成の他の影響として、フェールオーバーの後、新しいプライマリのコンピューティング容量の不足のためにアプリケーションのパフォーマンスが低下することが挙げられます。 より大きなコンピューティング容量に、必要なレベルまでアップグレードすることが必要になりますが、それは停止が緩和されるまで不可能です。 


> [!IMPORTANT]
> セカンダリ データベースがプライマリと同じコンピューティング サイズで構成されていない場合、公表されている 5 秒の RPO は保証できません。 


小さいコンピューティング サイズでセカンダリを作成する場合は、Azure portal 上のログ IO の割合グラフを使用すると、レプリケーションの負荷を維持するために必要なセカンダリの最小コンピューティング サイズを適切に見積もることができます。 たとえば、プライマリ データベースが P6 (1000 DTU) で、ログ IO の割合が 50% の場合、セカンダリは P4 (500 DTU) 以上である必要があります。 ログ IO データは、[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) または [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) データベース ビューを使用しても取得できます。  スロットルは、[sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) および [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) の各データベース ビューで HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO 待機状態として報告されます。 

SQL Database のコンピューティング サイズの詳細については、[SQL Database サービス レベル](sql-database-purchase-models.md)に関するページをご覧ください。

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>資格情報とファイアウォール規則の同期を保つ

geo レプリケートされたデータベースには、[データベース レベルのファイアウォール規則](sql-database-firewall-configure.md)の使用をお勧めします。この規則は、データベースと共にレプリケートされ、すべてのセカンダリ データベースの IP ファイアウォール規則がプライマリと同じになります。 このアプローチにより、プライマリとセカンダリ データベースの両方をホストするサーバー上で、顧客がファイアウォール規則を手動で構成、管理する必要性がなくなります。 同様に、データのアクセスに[包含データベース ユーザー](sql-database-manage-logins.md)を使用することにより、プライマリとセカンダリの両方のデータベースが、確実かつ常に同じユーザー資格情報を持つようにして、フェールオーバー時に、ログインとパスワードの不一致による中断を防ぐことができます。 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) の顧客を追加すると、プライマリおよびセカンダリ データベースへのユーザー アクセスを管理でき、データベース内で資格情報を管理する必要が完全になくなります。

## <a name="upgrading-or-downgrading-primary-database"></a>プライマリ データベースのアップグレードまたはダウングレード

セカンダリ データベースの接続を解除することなく、プライマリ データベースを (General Purpose と Business Critical 間ではなく、同じサービス レベル内の) 異なるコンピューティング サイズにアップグレードまたはダウングレードできます。 アップグレードの場合は、最初にセカンダリ データベースをアップグレードしてからプライマリをアップグレードすることをお勧めします。 ダウングレードは逆の順序で行います。つまり、最初にプライマリをダウングレードしてからセカンダリをダウングレードします。 データベースを異なるサービス レベルにアップグレードまたはダウングレードすると、この推奨事項が強制されます。

> [!NOTE]
> セカンダリ データベースをフェールオーバー グループ構成の一部として作成した場合、セカンダリ データベースをダウングレードすることは推奨されません。 これは、フェールオーバーがアクティブ化された後にデータ層に通常のワークロードを処理するのに十分な容量を確保するためです。

> [!IMPORTANT]
> フェールオーバー グループのプライマリ データベースを上位のレベルにスケーリングするには、最初にセカンダリ データベースを上位のレベルにスケーリングする必要があります。 セカンダリ データベースがスケーリングされる前にプライマリ データベースをスケーリングしようとすると、次のエラーが表示される可能性があります。
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>重要なデータの損失の防止

ワイド エリア ネットワークの遅延は大きいため、連続コピーでは非同期のレプリケーション メカニズムが使用されます。 非同期レプリケーションでは、障害が発生した場合に部分的なデータ損失が避けられません。 しかし、データ損失が許されないアプリケーションもあります。 重要な更新情報を保護するには、アプリケーション開発者は、トランザクションがコミットされた直後に [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) ステム プロシージャを呼び出すことができます。 **sp_wait_for_database_copy_sync** を呼び出すと、最後にコミットされたトランザクションがセカンダリ データベースに転送されるまで、呼び出しスレッドがブロックされます。 ただし、転送されたトランザクションがセカンダリで再生およびコミットされるのを待つことはありません。 **sp_wait_for_database_copy_sync** の対象は、特定の連続コピー リンクです。 プライマリ データベースへの接続権限を持つユーザーが、このプロシージャを呼び出すことができます。

> [!NOTE]
> **sp_wait_for_database_copy_sync** は、フェールオーバー後のデータの損失を防ぎますが、読み取りアクセスに対して完全な同期を保証することはありません。 **sp_wait_for_database_copy_sync** プロシージャ呼び出しによる遅延は、長くなる場合があり、呼び出し時のトランザクション ログのサイズによって異なります。

## <a name="monitoring-geo-replication-lag"></a>geo レプリケーションの遅延の監視

RPO に関する遅延を監視するには、プライマリ データベースの [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) の *replication_lag_sec* 列を使用します。 ここには、プライマリでコミットされたトランザクションと、セカンダリで保持されているトランザクションとの間の遅延が秒単位で表示されます。 例: 遅延の値が 1 秒である場合は、この瞬間にプライマリが停止の影響を受け、フェールオーバーが開始されると、最新の移行の 1 秒間が保存されないことを示します。 

セカンダリに適用済みの (つまり、セカンダリから読み取り可能な) プライマリ データベースに対する変更に関する遅延を測定するには、セカンダリ データベースの *last_commit* 時間を、プライマリ データベースの同じ値と比較します。

> [!NOTE]
> プライマリ データベースの *replication_lag_sec* の値が NULL になっていることがありますが、これは、現時点でプライマリとセカンダリとの間の遅延時間が不明であることを意味します。   これは、プロセスが再起動した後に発生する一時的な状態であることがほとんどです。 *replication_lag_sec* から長時間にわたって NULL が返される場合は、アプリケーションにアラートを送ることを検討してください。 永続的な接続エラーにより、セカンダリ データベースがプライマリと通信できないことを示していると考えられます。 セカンダリとプライマリ データベース間の *last_commit* 時間に大きな差が生じる可能性のある状況は他にもあります。 例: 長時間変更がなかったプライマリでコミットが行われた場合、差異は非常に大きくなりますが、すぐに 0 に戻ります。 これらの 2 つの値の差が大きい状況が長時間続く場合は、エラーの状態にあると考えられます。


## <a name="programmatically-managing-active-geo-replication"></a>アクティブ geo レプリケーションのプログラムでの管理

前に説明したように、アクティブ geo レプリケーションは、Azure PowerShell および REST API を使用してプログラムによって管理することもできます。 次の表では、使用できるコマンド セットについて説明します。 アクティブ geo レプリケーションには、管理のための Azure Resource Manager API 一式 ([Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)、[Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview)など) が含まれています。 これらの API は、リソース グループの使用を必要とし、ロール ベース セキュリティ (RBAC) をサポートします。 アクセス ロールの実装方法の詳細については、[Azure のロール ベースのアクセス制御](../role-based-access-control/overview.md)に関するページをご覧ください。

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: 単一データベースおよびプールされたデータベースのフェールオーバーを管理します。

> [!IMPORTANT]
> これらの Transact-SQL コマンドは、アクティブ geo レプリケーションにのみ適用され、フェールオーバー グループには適用されません。 そのため、それらのコマンドは、フェールオーバー グループしかサポートしていない Managed Instance にも適用されません。

| command | 説明 |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |ADD SECONDARY ON SERVER 引数を使用して、既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。 |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |FAILOVER または FORCE_FAILOVER_ALLOW_DATA_LOSS を使用して、セカンダリ データベースをプライマリに切り替え、フェールオーバーを開始します |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |REMOVE SECONDARY ON SERVER を使用して、SQL Database と指定されたセカンダリ データベース間でのデータ レプリケーションを終了します。 |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Azure SQL Database サーバー上の各データベースの、既存のレプリケーション リンクの情報をすべて返します。 |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |最新のレプリケーション時刻、最後のレプリケーションの遅延、および指定された SQL データベースのレプリケーション リンクに関する他の情報を取得します。 |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |レプリケーション リンクの状態を含むすべてのデータベース操作の状態が表示されます。 |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |コミットされたすべてのトランザクションがレプリケートされ、アクティブ セカンダリ データベースによって認識されるまで、アプリケーションが待機状態になります。 |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell:単一データベースおよびプールされたデータベースのフェールオーバーを管理します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

| コマンドレット | 説明 |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |1 つまたは複数のデータベースを取得します。 |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。 |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |セカンダリ データベースをプライマリに切り替えて、フェールオーバーを開始します。 |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |SQL Database と指定されたセカンダリ データベース間でのデータ レプリケーションを終了します。 |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Azure SQL Database とリソース グループまたは SQL Server 間の geo レプリケーション リンクを取得します。 |
|  | |

> [!IMPORTANT]
> サンプル スクリプトについては、[アクティブ geo レプリケーションを使用した単一データベースの構成およびフェールオーバー](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)に関するページ、および[アクティブ geo レプリケーションを使用したプールされたデータベースの構成およびフェールオーバー](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)に関するページをご覧ください。

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API:単一データベースおよびプールされたデータベースのフェールオーバーを管理します。

| API | 説明 |
| --- | --- |
| [Create または Update Database (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |プライマリまたはセカンダリ データベースを作成、更新、または復元します。 |
| [Get Create or Update Database Status](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |復元操作中にステータスを返します。 |
| [Set Secondary Database as Primary (Planned Failover) (セカンダリ データベースをプライマリとして設定する (計画されたフェールオーバー))](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |現在のプライマリ データベースからフェールオーバーして、どのセカンダリ データベースがプライマリかを設定します。 **このオプションは、Managed Instance に対してはサポートされていません。**|
| [Set Secondary Database as Primary (計画されていないフェールオーバー)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |現在のプライマリ データベースからフェールオーバーして、どのセカンダリ データベースがプライマリかを設定します。 この操作を行うとデータが失われる可能性があります。 **このオプションは、Managed Instance に対してはサポートされていません。**|
| [Get Replication Link](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |geo レプリケーション パートナーシップで指定された SQL データベースの特定のレプリケーション リンクを取得します。 sys.geo_replication_links カタログ ビューで表示可能な情報を取得します。 **このオプションは、Managed Instance に対してはサポートされていません。**|
| [Replication Links - List By Database](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | geo レプリケーション パートナーシップで指定された SQL データベースのすべてのレプリケーション リンクを取得します。 sys.geo_replication_links カタログ ビューで表示可能な情報を取得します。 |
| [Delete Replication Link](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | データベース レプリケーション リンクを削除します。 フェールオーバー中には実行できません。 |
|  | |

## <a name="next-steps"></a>次の手順

- サンプル スクリプトは、以下を参照してください。
  - [アクティブ geo レプリケーションを使用して、単一のデータベースを構成およびフェールオーバーする](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [アクティブ geo レプリケーションを使用して、プールされたデータベースを構成およびフェールオーバーする](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database でも自動フェールオーバー グループがサポートされています。 詳細については、[自動フェールオーバー グループ](sql-database-auto-failover-group.md)の使用に関するページを参照してください。
- ビジネス継続性の概要およびシナリオについては、[ビジネス継続性の概要](sql-database-business-continuity.md)を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)」を参照してください
- 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
- 新しいプライマリ サーバーとデータベースの認証要件については、 [障害復旧後の SQL Database のセキュリティ](sql-database-geo-replication-security-config.md)に関する記事を参照してください。
