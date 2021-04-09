---
title: よく寄せられる質問 (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance に関してよく寄せられる質問 (FAQ)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: 17e97503996b53868f12e74ed5a38dba63285426
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503200"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL Managed Instance に関してよく寄せられる質問 (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) について特によく寄せられる質問を取り上げます。

## <a name="supported-features"></a>サポートされている機能

**SQL Managed Instance でサポートされている機能の一覧はどこで確認できますか?**

SQL Managed Instance でサポートされている機能の一覧については、[Azure SQL Managed Instance の機能](../database/features-comparison.md)に関するページを参照してください。

Azure SQL Managed Instance と SQL Server 間での構文と動作の違いについては、[T-SQL と SQL Server の相違点](transact-sql-tsql-differences-sql-server.md)に関するページを参照してください。


## <a name="technical-specification-resource-limits-and-other-limitations"></a>技術仕様、リソース制限、およびその他の制限事項
 
**SQL Managed Instance の技術的特性とリソース制限はどこで確認できますか?**

使用可能なハードウェアの世代の特性については、[ハードウェアの世代間の技術的相違点](resource-limits.md#hardware-generation-characteristics)に関するページを参照してください。
使用可能なサービス レベルとその特性については、[サービス レベル間の技術的相違点](resource-limits.md#service-tier-characteristics)に関するページを参照してください。

**対象となるサービス レベルはどれですか?**

すべてのお客様は、任意のサービス レベルを利用できます。 ただし、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、既存のライセンスを Azure SQL Managed Instance の割引料金で交換する場合は、ソフトウェア アシュアランスをお持ちの SQL Server Enterprise Edition のお客様は [General Purpose](../database/service-tier-general-purpose.md) または [Business Critical](../database/service-tier-business-critical.md) パフォーマンス レベルの対象で、ソフトウェア アシュアランスをお持ちの SQL Server Standard Edition のお客様は General Purpose パフォーマンス レベルのみ対象になることに注意してください。 詳細については、[AHB の具体的な権利](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)に関する記事を参照してください。

**SQL Managed Instance ではどのような種類のサブスクリプションがサポートされていますか?**

サポートされているサブスクリプションの種類の一覧については、「[サポートされているサブスクリプションの種類](resource-limits.md#supported-subscription-types)」を参照してください。 

**どの Azure リージョンがサポートされていますか?**

マネージド インスタンスは、ほとんどの Azure リージョンで作成できます。詳細については、[SQL Managed Instance でサポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)に関する記事を参照してください。 現在サポートされていないリージョンでマネージド インスタンスが必要な場合には、[Azure portal 経由でサポート リクエストを送信](../database/quota-increase-request.md)してください。

**SQL Managed Instance のデプロイにクォータの制限はありますか?**

マネージド インスタンスには、使用できるサブネットの数と、プロビジョニングできる仮想コアの数という 2 つの規定の制限があります。 制限は、サブスクリプションの種類とリージョンによって異なります。 サブスクリプションの種類ごとの、リージョン別のリソースの制限の一覧については、[リージョン別のリソースの制限](resource-limits.md#regional-resource-limitations)に関する記事を参照してください。 これらは、要求に応じて増やすことができるソフト制限です。 現在のリージョンでより多くのマネージド インスタンスをプロビジョニングする必要がある場合、Azure portal を使用してクォータを増やすためのサポート リクエストを送信します。 詳細については、「[Azure SQL Database のクォータの増加を要求する](../database/quota-increase-request.md)」を参照してください。

**必要に応じて、マネージド インスタンスのデータベース数の上限 (100) を増やすことはできますか?**

いいえ。現時点では、SQL Managed Instance 上のデータベースの数を増やすための専用プランはありません。 

**8TB を超えるデータがある場合、どこへ移行できますか?**
ワークロードに合う、以下のような他の Azure フレーバーに移行することをご検討ください。[Azure SQL Database Hyperscale](../database/service-tier-hyperscale.md) または [Azure Virtual Machines 上の SQL Server](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) があります。

**よい大きい RAM と仮想コアの比率や、より多くの CPU など、特定のハードウェア要件がある場合は、どこに移行できますか?**
[Azure Virtual Machines 上の SQL Server](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) またはメモリおよび CPU 最適化された [Azure SQL Database](../database/sql-database-paas-overview.md) への移行をご検討ください。

## <a name="known-issues-and-defects"></a>既知の問題と欠陥

**既知の問題や欠陥はどこで確認できますか?**

製品の欠陥や既知の問題については、「[既知の問題](../database/doc-changes-updates-release-notes.md#known-issues)」を参照してください。

## <a name="new-features"></a>新機能

**最新の機能とパブリック プレビュー段階の機能はどこにありますか?**

新機能とプレビュー機能については、[リリース ノート](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)を参照してください。

## <a name="create-update-delete-or-move-sql-managed-instance"></a>SQL Managed Instance の作成、更新、削除、または移動

**SQL Managed Instance をプロビジョニングするにはどうすればよいですか?**

インスタンスは、[Azure portal](instance-create-quickstart.md)、[PowerShell](scripts/create-configure-managed-instance-powershell.md)、[Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281)、および [ARM テンプレート](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)からプロビジョニングできます。

**既存のサブスクリプションで Managed Instance をプロビジョニングすることはできますか?**

はい。サブスクリプションが[サポートされているサブスクリプションの種類](resource-limits.md#supported-subscription-types)に属している場合、既存のサブスクリプションで Managed Instance をプロビジョニングできます。

**名前が数字で始まるサブネットで Managed Instance をプロビジョニングできないのはなぜですか?**

これは、サブネット名を正規表現 ^[a-zA-Z_][^\\\/\:\*\?\"\<\>\|\`\'\^]*(?<![\.\s])$ と照合して検証する、基になるコンポーネントによる現在の制限です。 正規表現および有効なサブネット名を渡すすべての名前は、現在サポートされています。

**マネージド インスタンスをスケーリングするにはどうすればよいですか?**

マネージド インスタンスは、[Azure portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation)、[PowerShell](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell)、[Azure CLI](/cli/azure/sql/mi#az-sql-mi-update) または [ARM テンプレート](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)からスケーリングできます。

**Managed Instance をあるリージョンから別のリージョンに移動することはできますか?**

はい、できます。 手順については、[リージョン間でリソースを移動する](../database/move-resources-across-regions.md)方法に関する記事を参照してください。

**Managed Instance を削除するにはどうすればよいですか?**

Managed Instance は、Azure portal、[PowerShell](/powershell/module/az.sql/remove-azsqlinstance)、[Azure CLI](/cli/azure/sql/mi#az-sql-mi-delete) または [Resource Manager REST API](/rest/api/sql/managedinstances/delete) を使用して削除できます。

**インスタンスを作成または更新したり、データベースを復元したりするのにどれくらいの時間がかかりますか?**

新しいマネージド インスタンスの作成やサービス レベル (仮想コア、ストレージ) の変更にかかると予想される時間は、いくつかの要因に左右されます。 詳細については、「[管理操作](sql-managed-instance-paas-overview.md#management-operations)」を参照してください。
 
## <a name="naming-conventions"></a>名前付け規則

**マネージド インスタンスに SQL Server のオンプレミス インスタンスと同じ名前を付けることはできますか?**

マネージド インスタンス名の変更はサポートされていません。

**DNS ゾーンのプレフィックスは変更できますか?**

はい。Managed Instance の既定の DNS ゾーン *.database.windows.net* は変更できます。 

既定値の代わりに別の DNS ゾーン、たとえば *.contoso.com* などを使用するには、次のようにします。 
- CliConfig を使用して別名を定義する。 このツールは単なるレジストリ設定ラッパーなので、グループ ポリシーやスクリプトを使用して行うことも可能です。
- *TrustServerCertificate=true* オプションを指定した *CNAME* を使用する。

## <a name="migration-options"></a>移行オプション

**Azure SQL Database の単体またはエラスティック プールから SQL Managed Instance に移行するには、どうすればよいですか?**

マネージド インスタンスで提供されるコンピューティングおよびストレージ サイズあたりのパフォーマンス レベルは、Azure SQL Database の他のデプロイ オプションと同じです。 単一インスタンスでデータを統合する場合または単にマネージド インスタンスでのみサポートされている機能が必要な場合は、エクスポートおよびインポート (BACPAC) 機能を使用してデータを移行できます。 SQL Database の SQL Managed Instance への移行に関して検討可能な他の方法を、次に示します。 
- [Data Source External](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210) を使用する
- [SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182) を使用する
- [BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7) を使用する

**インスタンス データベースを単一の Azure SQL Database に移行するには、どうすればよいですか?**

1 つの方法として、[データベースを BACPAC にエクスポート](../database/database-export.md)し、その [BACPAC ファイルをインポート](../database/database-import.md)します。 データベースが 100 GB 未満の場合は、これが推奨される方法です。

データベース内のすべてのテーブルに *主* キーがあり、データベース内にインメモリ OLTP オブジェクトが一切ない場合は、[トランザクション レプリケーション](replication-two-instances-and-sql-server-configure-tutorial.md?view=sql-server-2017&preserve-view=true)を使用できます。

マネージド インスタンスから取得したネイティブの COPY_ONLY バックアップを SQL Server に復元することはできません。マネージド インスタンスには、SQL Server と比較して新しいバージョンのデータベースがあるためです。 詳細については、「[コピーのみのバックアップ](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15)」を参照してください。

**自分の SQL Server インスタンスを SQL Managed Instance に移行するには、どうすればよいですか?**

SQL Server インスタンスを移行するには、「[Azure SQL Managed Instance への SQL Server インスタンスの移行](migrate-to-instance-from-sql-server.md)」を参照してください。

**他のプラットフォームから SQL Managed Instance に移行するには、どうすればよいですか?**

他のプラットフォームからの移行に関する移行の情報については、[Azure データベース移行ガイド](https://datamigration.microsoft.com/)を参照してください。

## <a name="switch-hardware-generation"></a>ハードウェアの世代の切り替え 

**マネージド インスタンスのハードウェア世代の Gen 4 と Gen 5 をオンラインで切り替えることはできますか?**

マネージド インスタンスがプロビジョニングされているリージョンで Gen5 ハードウェアが利用可能になっている場合は、Gen4 から Gen5 へのオンライン切り替えを自動で行うことができます。 この場合は、「[仮想コア モデルの概要](../database/service-tiers-vcore.md)」ページでハードウェアの世代間を切り替える方法を参照してください。

これは、新しいマネージド インスタンスがバックグラウンドでプロビジョニングされ、データベースがプロセスの最後の迅速なフェールオーバーによって古いインスタンスと新しいインスタンスの間で自動的に転送されるため、時間のかかる操作です。

注:Gen4 ハードウェアは段階的に廃止中であり、新しいデプロイでは利用できなくなりました。 すべての新しいデータベースを Gen5 ハードウェアにデプロイする必要があります。 Gen5 から Gen4 への切り替えもできません。

## <a name="performance"></a>パフォーマンス 

**Managed Instance のパフォーマンスを SQL Server のパフォーマンスと比較するにはどうすればよいですか?**

マネージド インスタンスと SQL Server のパフォーマンスを比較するために、まずは「[Azure SQL マネージド インスタンスと SQL Server のパフォーマンス比較に関するベスト プラクティス](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210)」の記事をご覧ください。

**Managed Instance と SQL Server の間でパフォーマンスの差が生じる原因は何ですか?**

「[SQL Managed Instance と SQL Server の間でパフォーマンスの差が生じる主な原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)」を参照してください。 General Purpose Managed Instance のパフォーマンスに対するログ ファイルのサイズの影響の詳細については、「[General Purpose のログ ファイルのサイズの影響](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)」を参照してください。

**マネージド インスタンスのパフォーマンスを調整するには、どうすればよいですか?**

次の方法で、マネージド インスタンスのパフォーマンスを最適化することができます。
- [自動チューニング](../database/automatic-tuning-overview.md)では、AI と機械学習に基づく継続的なパフォーマンス チューニングによって、最大限のパフォーマンスと安定したワークロードが実現されます。
-   [インメモリ OLTP](../in-memory-oltp-overview.md) では、トランザクション処理ワークロードのスループットと待機時間が改善され、ビジネスの分析情報がより迅速に提供されます。 

パフォーマンスをさらにチューニングするには、[アプリケーションとデータベースのチューニング](../database/performance-guidance.md#tune-your-database)の *ベスト プラクティス* をいくつか適用することを検討してください。
ワークロードが多数の小さなトランザクションで構成されている場合は、待機時間を短縮しスループットを向上させるために[接続の種類をプロキシからリダイレクト モードに切り替える](connection-types-overview.md#changing-connection-type)ことを検討してください。

## <a name="monitoring-metrics-and-alerts"></a>監視、メトリック、およびアラート

**マネージ インスタンスの監視とアラートにはどのようなオプションがありますか?**

SQL Managed Instance の使用状況とパフォーマンスを監視してアラートを生成するためのすべてのオプションについては、「[Azure SQL Managed Instance の監視オプションに関するブログ投稿](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416)」を参照してください。 SQL MI のリアルタイムのパフォーマンス監視については、「[Azure SQL DB Managed Instance のリアルタイムによるパフォーマンスの監視](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)」を参照してください。

**パフォーマンスの追跡に SQL Profiler を使用できますか?**

はい。SQL Profiler は SQL Managed Instance でサポートされています。 詳細については、[SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) に関する記事を参照してください。

**Database Advisor と Query Performance Insight は Managed Instance データベースでサポートされていますか?**

いいえ。サポートされていません。 [DMV](../database/monitoring-with-dmvs.md) と [クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15)を [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) と [XEvent](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) と共に使用して、データベースを監視できます。

**SQL Managed Instance でメトリック アラートを作成できますか?**

はい。 詳細については、[SQL Managed Instance のアラートの作成](alerts-create.md)に関する記事を参照してください。

**マネージド インスタンス内のデータベースでメトリック アラートを作成できますか?**

できません。アラート メトリックは、マネージド インスタンスでのみ使用できます。 マネージド インスタンス内の個々のデータベースのアラート メトリックは使用できません。

## <a name="storage-size"></a>ストレージ サイズ

**SQL Managed Instance の最大ストレージ サイズはどうなっていますか?**

SQL Managed Instance のストレージ サイズは選択したサービス レベル (General Purpose または Business Critical) に応じて異なります。 これらのサービス レベルのストレージ制限については、[サービス レベルの特性](../database/service-tiers-general-purpose-business-critical.md)に関するページを参照してください。

**マネージド インスタンスで使用可能な最小ストレージ サイズは?**

インスタンスで使用可能なストレージの最小量は 32 GB です。 ストレージは、最大ストレージ サイズになるまで、32 GB ずつ追加できます。 最初の 32 GB は無料です。

**インスタンスに割り当てられたストレージ スペースは、コンピューティング リソースとは別に増やすことができますか?**

はい。コンピューティングとは別に、アドオン ストレージをある程度まで購入できます。 この *表* にある "[インスタンスの予約済み最大ストレージ](resource-limits.md#hardware-generation-characteristics)" を参照してください。

**General Purpose サービス レベルでストレージのパフォーマンスを最適化するにはどうすればよいですか?**

ストレージのパフォーマンスを最適化するには、「[General Purpose でのストレージのベスト プラクティス](https://techcommunity.microsoft.com)」を参照してください。

## <a name="backup-and-restore"></a>バックアップと復元

**バックアップ ストレージは、マネージド インスタンス ストレージから差し引かれますか?**

いいえ。バックアップ ストレージがマネージド インスタンス ストレージ領域から差し引かれることはありません。 バックアップ ストレージはインスタンス ストレージ領域から独立しており、サイズは制限されていません。 バックアップ ストレージは、インスタンス データベースのバックアップを保持する期間に制限があり、35 日まで構成可能です。 詳細については、「[自動バックアップ](../database/automated-backups-overview.md)」を参照してください。

**マネージド インスタンスで自動バックアップが行われたタイミングを確認するにはどうすればよいですか?**

Managed Instance で自動バックアップが実行された時間を追跡するには、「[Azure SQL Managed Instance の自動バックアップの追跡方法](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355)」を参照してください。

**オンデマンド バックアップはサポートされていますか?**

はい。Azure Blob Storage にコピーのみの完全バックアップを作成できます。ただし、これは Managed Instance でのみ復元できます。 詳細については、「[コピーのみのバックアップ](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15)」を参照してください。 ただし、暗号化に使用される証明書にアクセスできないため、データベースがサービス マネージド TDE によって暗号化されている場合、コピーのみのバックアップは不可能です。 このような場合、ポイントインタイム リストア機能を使用して、データベースを別の SQL Managed Instance に移動するか、カスタマー マネージド キーに切り替えてください。

**Managed Instance への (.bak ファイルからの) ネイティブ復元はサポートされていますか?**

はい。SQL Server 2005 以降のバージョンでサポートされており、使用可能です。  ネイティブ復元を使用するには、.bak ファイルを Azure Blob Storage にアップロードし、T-SQL コマンドを実行します。 詳細については、「[URL からのネイティブ復元](./migrate-to-instance-from-sql-server.md#native-restore-from-url)」を参照してください。

## <a name="business-continuity"></a>ビジネス継続性

**システム データベースは、フェールオーバー グループのセカンダリ インスタンスにレプリケートされますか?**

システム データベースは、フェールオーバー グループのセカンダリ インスタンスにはレプリケートされません。 そのため、オブジェクトがセカンダリに手動で作成されていない限り、セカンダリ インスタンスではシステム データベースのオブジェクトに依存するシナリオは実現できません。 回避策については、「[システム データベースのオブジェクトに依存するシナリオを実現させる](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases)」を参照してください。
 
## <a name="networking-requirements"></a>ネットワーク要件 

**Managed Instance サブネットでの現在の受信/送信 NSG の制約はどのようなものですか?**

必要な NSG と UDR のルールは[こちら](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)に記載されており、サービスによって自動的に設定されます。
これらのルールは、あくまでもサービスを維持するために必要なものであることに注意してください。 マネージド インスタンスに接続してさまざまな機能を使用するには、管理が必要な、機能固有のルールを追加で設定する必要があります。

**管理ポートでインバウンド NSG 規則を設定するには、どうすればよいですか?**

SQL Managed Instance は、管理ポートにルールを設定する役割を担います。 これは、[サービス支援サブネット構成](connectivity-architecture-overview.md#service-aided-subnet-configuration)という機能によって実現されます。
これは、SLA を満たすために、管理トラフィックが中断されないようにするためです。

**受信管理トラフィックに使用される発信元 IP 範囲は取得できますか?**

はい。 [Network Watcher フロー ログを構成する](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group)ことで、ネットワーク セキュリティ グループを介して送信されるトラフィックを分析できます。

**データ エンドポイント (ポート 1433) へのアクセスを制御するように NSG を設定できますか?**

はい。 Managed Instance がプロビジョニングされた後、ポート 1433 への受信アクセスを制御する NSG を設定できます。 IP 範囲をできるだけ絞り込むことをお勧めします。

**NVA またはオンプレミスのファイアウォールを設定して、FQDN に基づいて送信管理トラフィックをフィルター処理できますか?**

いいえ。 これは、いくつかの理由によりサポートされていません。
-   受信した管理要求への応答を表すトラフィックのルーティングは非対称であり、機能しない可能性があります。
-   ストレージにルーティングされるトラフィックは、スループットの制約と待機時間の影響を受けるため、期待するサービスの品質と可用性を提供することはできません。
-   経験則に基づくと、これらの構成はエラーが発生しやすく、サポートすることは困難です。

**送信する管理以外のトラフィック用に NVA またはファイアウォールを設定できますか。**

はい。 これを実現する最も簡単な方法は、NVA 経由でトラフィックをルーティングするために、マネージド インスタンス サブネットに関連付けられている UDR に 0/0 ルールを追加することです。
 
**Managed Instance に必要な IP アドレスの数はいくつですか?**

サブネットには、十分な数の利用可能な [IP アドレス](connectivity-architecture-overview.md#network-requirements)が含まれている必要があります。 SQL Managed Instance の VNet サブネットのサイズを決定するには、[Managed Instance に必要なサブネットのサイズと範囲の決定](./vnet-subnet-determine-size.md)に関する記事を参照してください。 

**インスタンス更新操作を実行できるだけの十分な IP アドレスが存在しない場合はどうなりますか?**

マネージド インスタンスのプロビジョニング先となるサブネットに十分な [IP アドレス](connectivity-architecture-overview.md#network-requirements)が存在しない場合は、新しいサブネットを作成したうえで、その中に新しいマネージド インスタンスを作成する必要があります。 また、新しいサブネットを作成するにあたっては、将来の更新操作で同様の問題が生じないよう、より多くの IP アドレスを確保することをお勧めします。 新しいインスタンスのプロビジョニング後、古いインスタンスと新しいインスタンスとの間で、データのバックアップと復元を手動で行うことができるほか、インスタンス間の[ポイントインタイム リストア](point-in-time-restore.md?tabs=azure-powershell)を実行することができます。

**Managed Instance を作成するには、空のサブネットが必要ですか?**

いいえ。 空のサブネット、または既に Managed Instance が含まれているサブネットのいずれかを使用できます。 

**サブネットのアドレス範囲を変更できますか?**

Managed Instances が含まれている場合は、できません。 これは、Azure のネットワーク インフラストラクチャの制限です。 アドレス空間は、[空のサブネットにしか追加できません](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings)。 

**マネージド インスタンスを別のサブネットに移動できますか?**

いいえ。 これは、現在の Managed Instance の設計上の制限です。 ただし、他のサブネットで新しいインスタンスをプロビジョニングし、古いインスタンスと新しいインスタンスとの間で、データのバックアップと復元を手動で行うことができるほか、インスタンス間の[ポイントインタイム リストア](point-in-time-restore.md?tabs=azure-powershell)を実行することができます。

**Managed Instance を作成するには、空の仮想ネットワークが必要ですか?**

これは必要ありません。 [Azure SQL Managed Instance 用の仮想ネットワークを作成する](./virtual-network-subnet-create-arm-template.md)か、または [Azure SQL Managed Instance の既存の仮想ネットワークを構成する](./vnet-existing-add-subnet.md)ことができます。

**Managed Instance を他のサービスと一緒にサブネットに配置することはできますか?**

いいえ。 現時点では、他のリソースの種類が既に含まれているサブネットに Managed Instance を配置することはサポートされていません。

## <a name="connectivity"></a>接続 

**IP アドレスを使用してマネージド インスタンスに接続できますか?**

いいえ、これはサポートされていません。 Managed Instance のホスト名は、Managed Instance の仮想クラスターの前にあるロード バランサーにマップされます。 1 つの仮想クラスターが複数の Managed Instance をホストすることが可能なため、名前を指定しないで適切な Managed Instance に接続をルーティングすることはできません。
SQL Managed Instance 仮想クラスターのアーキテクチャの詳細については、「[仮想クラスターの接続アーキテクチャ](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)」を参照してください。

**マネージド インスタンスに静的 IP アドレスを付与することはできますか?**

現在これはサポートされていません。

まれではあるものの不可欠な状況として、マネージド インスタンスを新しい仮想クラスターにオンラインで移行することが必要になる場合があります。 必要な場合、この移行を行うのは、サービスのセキュリティおよび信頼性の向上を目指してテクノロジ スタックに変更を加えるためです。 新しい仮想クラスターに移行すると、マネージド インスタンスのホスト名にマップされている IP アドレスが変更されます。 マネージド インスタンス サービスは、静的 IP アドレスのサポートを要求することはなく、定期的なメンテナンス サイクルの一環として、そのアドレスを予告なしに変更する権限を有しています。

このような理由から、IP アドレスの不変性を当てにすると不要なダウンタイムの発生につながるので、変更を想定しておくことを強くお勧めします。

**Managed Instance にはパブリック エンドポイントがありますか?**

はい。 Managed Instance には、既定でサービス管理にのみ使用されるパブリック エンドポイントがありますが、お客様はデータ アクセスに対してもそれを有効にすることができます。 詳細については、[パブリック エンドポイントを使用した SQL Managed Instance の使用](./public-endpoint-overview.md)に関する記事を参照してください。 パブリック エンドポイントを構成するには、[SQL Managed Instance のパブリック エンドポイントの構成](public-endpoint-configure.md)に関する記事を参照してください。

**パブリック エンドポイントは Managed Instance コントロールからどのようにアクセスされるのですか?**

Managed Instance コントロールからパブリック エンドポイントへのアクセスは、ネットワークとアプリケーションの両方のレベルで行われます。

管理およびデプロイ サービスでは、外部ロード バランサーにマッピングされる[管理エンドポイント](./connectivity-architecture-overview.md#management-endpoint)を使用して、マネージド インスタンスに接続します。 トラフィックは、マネージド インスタンスの管理コンポーネントだけが使用する定義済みの一連のポートで受信された場合のみ、ノードにルーティングされます。 ノード上の組み込みのファイアウォールは、Microsoft の IP 範囲からのトラフィックのみを許可するように設定されています。 証明書によって、管理コンポーネントと管理プレーンとの間のすべての通信が互いに認証されます。 詳細については、[SQL Managed Instance の接続アーキテクチャ](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)に関する記事を参照してください。

**パブリック エンドポイントを使用して Managed Instance データベースのデータにアクセスすることはできますか?**

はい。 お客様は [Azure portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal) / [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell)、または ARM からパブリック エンドポイント データ アクセスを有効にして、データ ポート (ポート番号 3342) へのアクセスをロック ダウンするよう NSG を構成する必要があります。 詳細については、「[Azure SQL Managed Instance のパブリック エンドポイントを構成する](public-endpoint-configure.md)」と「[パブリック エンドポイントで安全に Azure SQL Managed Instance を使用する](public-endpoint-overview.md)」を参照してください。 

**SQL データ エンドポイントのカスタム ポートを指定できますか?**

いいえ。このオプションは使用できません。  プライベート データ エンドポイントの場合、Managed Instance では既定のポート番号 1433 が使用され、パブリック データ エンドポイントの場合、Managed Instance では既定のポート番号 3342 が使用されます。

**異なるリージョンに配置されている Managed Instance に接続するには、どのような方法がお勧めですか?**

これを行うには、Express Route 回線のピアリングがお勧めの方法です。 グローバル仮想ネットワーク ピアリングはサポート対象ですが、次の注記で説明する制限事項があります。  

> [!IMPORTANT]
> [2020 年 9 月 22 日、Microsoft は、新しく作成された仮想クラスターのグローバル仮想ネットワーク ピアリングを発表しました](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)。 これは、グローバル仮想ネットワーク ピアリングが、発表日以降に空のサブネットに作成された SQL Managed Instance に加え、それらのサブネットに作成された後続のすべてのマネージド インスタンスに対してサポートされることを意味します。 SQL Managed Instance のその他すべてのピアリングについては、[グローバル仮想ネットワーク ピアリングの制約](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)により、同じリージョン内のネットワークに制限されます。 詳細については、[Azure Virtual Networks のよく寄せられる質問](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)に関する記事の関連セクションも参照してください。 

Express Route 回線のピアリングおよびグローバル仮想ネットワーク ピアリングが不可能な場合、サイト間 VPN 接続 ([Azure portal](../../vpn-gateway/tutorial-site-to-site-portal.md)、[PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)、[Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) を作成することが、他の唯一のオプションとなります。

## <a name="mitigate-data-exfiltration-risks"></a>データ窃盗リスクを軽減する  

**データ窃盗リスクを軽減するにはどうすればいいですか?**

データ窃盗リスクを軽減するために、以下に示す一連のセキュリティ設定および制御を適用することをお勧めします。

- すべてのデータベースで [Transparent Data Encryption (TDE)](../database/transparent-data-encryption-tde-overview.md) を有効にする。
- 共通言語ランタイム (CLR) を無効にする。 これは、オンプレミスでも推奨される設定です。
- Azure Active Directory (Azure AD) 認証のみを使用する。
- 低特権の DBA アカウントでインスタンスにアクセスする。
- sysadmin アカウント用に Jumpbox への JIT アクセスを構成する。
- [SQL 監査](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)を有効にしてアラート メカニズムと統合する。
- [Azure Defender for SQL](../database/azure-defender-for-sql.md) スイートから[脅威検出](../database/threat-detection-configure.md)を有効にする。

## <a name="dns"></a>DNS

**SQL Managed Instance 用にカスタム DNS を構成できますか?**

はい。 詳細については、[Azure SQL Managed Instance 用にカスタム DNS を構成する](./custom-dns-configure.md)方法に関する記事を参照してください。

**DNS の更新はできますか?**

はい。 「[SQL Managed Instance 仮想クラスター上で仮想ネットワーク DNS サーバーの設定を同期する](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md)」を参照してください。

## <a name="change-time-zone"></a>タイムゾーンの変更

**既存のマネージド インスタンスのタイム ゾーンを変更できますか?**

タイム ゾーン構成は、マネージド インスタンスの初回プロビジョニング時に設定できます。 既存のマネージド インスタンスのタイム ゾーンの変更はサポートされていません。 詳細については、[タイム ゾーンの制限](timezones-overview.md#limitations)に関する記事を参照してください。

対処方法としては、適切なタイム ゾーンで新しいマネージド インスタンスを作成し、手動バックアップおよび復元を実行するか、[クロスインスタンスのポイントインタイム リストア](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance) (推奨) を実行します。


## <a name="security-and-database-encryption"></a>セキュリティとデータベースの暗号化

**Sysadmin サーバー ロールは SQL Managed Instance で使用できますか?**

はい。お客様は、sysadmin ロールのメンバーであるログインを作成できます。  sysadmin 権限を持つお客様は、インスタンスを運用する責任を負うことになり、これは SLA コミットメントに悪影響を与える可能性があります。 sysadmin サーバー ロールにログインを追加するには、「[Azure AD 認証](./aad-security-configure-tutorial.md#azure-ad-authentication)」を参照してください。

**SQL Managed Instance では Transparent Data Encryption サポートされていますか?**

はい。SQL Managed Instance では Transparent Data Encryption がサポートされています。 詳細については、[SQL Managed Instance の Transparent Data Encryption](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal) に関する記事を参照してください。

**TDE の "Bring Your Own Key" モデルを活用することはできますか?**

はい。BYOK シナリオの Azure Key Vault は、Azure SQL Managed Instance で使用できます。 詳細については、[カスタマー マネージド キーを使用した Transparent Data Encryption](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key) に関する記事を参照してください。

**暗号化された SQL Server データベースを移行できますか?**

はい、できます。 暗号化された SQL Server データベースを移行するには、既存の証明書をエクスポートして Managed Instance にインポートした後、データベースの完全バックアップを実行して Managed Instance に復元する必要があります。 

また、[Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) を使用して、TDE で暗号化されたデータベースを移行することもできます。

**SQL Managed Instance の TDE プロテクターのローテーションを構成する方法について教えてください。**

Azure Cloud Shell を使用して Managed Instance の TDE プロテクターをローテーションできます。 手順については、「[Azure Key Vault の独自のキーを使用した SQL Managed Instance での Transparent Data Encryption](scripts/transparent-data-encryption-byok-powershell.md)」を参照してください。

**暗号化されたデータベースを SQL Managed Instance に復元できますか?**

はい。SQL Managed Instance に復元するために、データベースを復号化する必要はありません。 暗号化されたバックアップ ファイルからデータを読み取れるように、ソース システム上で暗号化キーの保護機能として使用される証明書またはキーを SQL Managed Instance に提供する必要があります。 その方法は次の 2 つです。

- *SQL Managed Instance に証明書の保護機能をアップロードする。* これを実行できるのは、PowerShell を使用した場合のみです。 この[サンプル スクリプト](./tde-certificate-migrate.md)にプロセス全体が記述されています。
- *非対称キーの保護機能を Azure Key Vault にアップロードし、SQL Managed Instance がその保護機能を指すように指定する。* この方法は、暗号化キーの格納にも Key Vault 統合を使用する Bring Your Own Key (BYOK) TDE のユース ケースに似ています。 キーを暗号化キーの保護機能として使用せず、SQL Managed Instance によって暗号化されたデータベースが復元される際にキーを利用できるようにするだけの場合は、[BYOK TDE の設定](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)手順を実行し、 **[選択したキーを既定の TDE 保護機能にします]** チェック ボックスはオフにしてください。

暗号化保護機能を SQL Managed Instance で利用できるようにしたら、標準のデータベース復元手順を進めることができます。

## <a name="purchasing-models-and-benefits"></a>購入モデルと特典

**SQL Managed Instance では、どのような購入モデルを使用できますか?**

SQL Managed Instance には、[仮想コアベースの購入モデル](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)が用意されています。

**SQL Managed Instance では、どのようなコスト面での利点を受けられますか?**

Azure SQL の特典を活用して、次の方法でコストを削減できます。
-   オンプレミスのライセンスに対する既存の投資を最大化し、[Azure ハイブリッド特典](../azure-hybrid-benefit.md?tabs=azure-powershell)で最大 55% 節約できます。 
-   コンピューティング リソースを予約して、[予約インスタンス特典](../database/reserved-capacity-overview.md)で最大 33% 節約できます。 これと Azure ハイブリッド特典を組み合わせて、最大 82% の節約を実現できます。 
-   [Azure Dev/Test の価格特典](https://azure.microsoft.com/pricing/dev-test/)を利用して、進行中の開発とテストのワークロードを割引料金により、定価よりも最大 55% お得になります。

**予約インスタンスの特典の対象となるのはだれですか?**

予約インスタンス特典の利用資格を得るには、お客様のサブスクリプションの種類がエンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従量課金制料金の個々の契約 (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。 予約の詳細については、[予約インスタンスの特典](../database/reserved-capacity-overview.md)に関する記事を参照してください。 

**予約を取り消したり、交換したり、返金したりすることはできますか?**

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="billing-for-managed-instance-and-backup-storage"></a>Managed Instance およびバックアップ ストレージの課金

**SQL Managed Instance の価格オプションについて教えてください。**

Managed Instance の価格オプションの詳細については、「[価格ページ](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)」を参照してください。

**マネージド インスタンスの課金コストを追跡するにはどうすればよいですか?**

これを行うには、[Azure Cost Management ソリューション](../../cost-management-billing/index.yml)を使用します。 [Azure portal](https://portal.azure.com) で **[サブスクリプション]** に移動し、 **[コスト分析]** を選択します。 

**[累積コスト]** オプションを使用し、その後 **[リソースの種類]** を `microsoft.sql/managedinstances` としてフィルター処理します。

**自動バックアップのコストはどれくらいですか?**

設定されているバックアップの保有期間にかかわらず、購入した予約済みのデータ ストレージ スペースと同じ量のバックアップ ストレージが無料で確保されます。 バックアップ ストレージの使用量が、割り当てられている無料のバックアップ ストレージ スペース以内であれば、マネージド インスタンスの自動バックアップには追加のコストはかかりません。そのため、無料で利用できます。 無料の容量を超えてバックアップ ストレージを使用すると、米国リージョンでは 1 GB あたり約 $0.20 から $0.24 のコストがかかります。お客様のリージョンでの詳細については、価格ページを参照してください。 詳細については、[バックアップ ストレージの使用量の説明](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)に関する記事を参照してください。

**バックアップ ストレージの使用量の課金コストを監視するにはどうすればよいですか?**

バックアップ ストレージのコストは、Azure portal を使用して監視できます。 手順については、[自動バックアップのコストを監視する](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs)方法に関する記事を参照してください。 

**マネージド インスタンスのバックアップ ストレージのコストを最適化するにはどうすればよいですか?**

バックアップ ストレージのコストを最適化するには、[SQL Managed Instance でのバックアップの微調整](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)に関する記事を参照してください。

## <a name="cost-saving-use-cases"></a>コストを節約するユース ケース

**SQL Managed Instance のユース ケースやコスト削減の成果はどこで確認できますか?**

SQL Managed Instance のケース スタディ:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Azure SQL Managed Instance のデプロイに関連する利点、コスト、およびリスクについて理解を深めるには、次の Forrester 社による調査も参考になります。[Microsoft Azure SQL Database Managed Instance の Total Economic Impact](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)

## <a name="password-policy"></a>パスワード ポリシー 

**SQL Managed Instance の SQL ログインには、どのようなパスワード ポリシーが適用されますか?**

SQL Managed Instance の SQL ログイン用のパスワード ポリシーは、マネージド インスタンスを保持する仮想クラスターを形成している VM に適用された Azure プラットフォーム ポリシーを継承します。 現時点では、これらの設定は Azure によって定義され、マネージド インスタンスによって継承されるため、変更することはできません。

 > [!IMPORTANT]
 > Azure プラットフォームでは、そのポリシーに依存するサービスに通知することなく、ポリシー要件を変更することが可能です。

**現在の Azure プラットフォーム ポリシーはどうなっていますか?**

各ログインでは、ログイン時にパスワードを設定し、有効期間の上限に達したらそのパスワードを変更する必要があります。

| **ポリシー** | **セキュリティ設定** |
| --- | --- |
| パスワードの有効期間 | 42 日 |
| パスワードの変更禁止期間 | 1 日 |
| パスワードの最小文字数 | 10 文字 |
| パスワードでは、複雑さの要件を満たす必要がある | Enabled |

**ログイン レベルで SQL Managed Instance のパスワードの複雑さと有効期限を無効にすることはできますか?**

はい。CHECK_POLICY および CHECK_EXPIRATION フィールドをログイン レベルで制御することが可能です。 現在の設定を確認するには、以下の T-SQL コマンドを実行します。

```sql
SELECT *
FROM sys.sql_logins
```

その後、以下を実行して、指定されているログイン設定を変更できます。

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(' test ' を目的のログイン名に置き換え、ポリシーと有効期限の値を調整してください)


## <a name="service-updates"></a>サービスの更新情報

**Azure SQL Database と SQL Managed Instance に対するルート CA とは何ですか?**

[Azure SQL Database と SQL Managed Instance のための証明書ローテーション](../updates/ssl-root-certificate-expiring.md)に関する記事を参照してください。 

**SQL Managed Instance の計画メンテナンス イベントとは何ですか。**

[SQL Managed Instance での Azure メンテナンス イベントの計画](../database/planned-maintenance.md)に関するページを参照してください。 


## <a name="azure-feedback-and-support"></a>Azure のフィードバックとサポート

**SQL Managed Instance の改善案はどこに提出すればよいですか?**

新しい Managed Instance 機能に投票したり、[SQL Managed Instance フィードバック フォーラム](https://feedback.azure.com/forums/915676-sql-managed-instance)で新しい改善案を投票にかけることができます。 これにより、製品開発に貢献いただくことができ、これは今後の改善の優先順位を決める上で役立ちます。

**Azure サポート リクエストを作成するにはどうすればよいですか?**

Azure サポート リクエストを作成する方法については、[Azure サポート リクエストを作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)に関する記事を参照してください。
