---
title: Azure SQL Database リリース ノート | Microsoft Docs
description: Azure SQL Database サービスの新機能と機能強化、および Azure SQL Database のドキュメントの改善について説明します
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: 232f8b13174aed19477ee0b139c83fba439049ac
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101541"
---
# <a name="sql-database-release-notes"></a>SQL Database リリース ノート

この記事では、現在パブリック プレビュー段階にある SQL Database の機能を示します。 SQL Database の更新情報や機能強化については、[SQL Database サービスの更新情報](https://azure.microsoft.com/updates/?product=sql-database)を参照してください。 他の Azure サービスの更新情報や機能強化については、[サービスの更新情報](https://azure.microsoft.com/updates)を参照してください。

## <a name="features-in-public-preview"></a>パブリック プレビュー段階の機能

### <a name="single-databasetabsingle-database"></a>[1 つのデータベース](#tab/single-database)

| 機能 | 詳細 |
| ---| --- |
| [Azure プライベート リンク](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Private Link を使用すると、ネットワーク アーキテクチャが簡素化されると共に、データが Azure ネットワーク上に保存されてインターネットへの公開が行われないことから、Azure 内でのエンドポイント間の接続がセキュリティ保護されます。 また、Private Link を利用すると、ご自身のサービスを Azure 上に作成してレンダリングすることもできます。 |
| 単一データベースとエラスティック プールでの高速データベース復旧 | 詳しくは、「[高速データベース復旧](sql-database-accelerated-database-recovery.md)」をご覧ください。|
|個別の概算数|詳しくは、[個別の概算数](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)に関する記事をご覧ください。|
|行ストアでのバッチ モード (互換性レベル 150)|詳しくは、「[行ストアでのバッチ モード](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)」をご覧ください。|
| データの検出と分類  |詳しくは、「[Azure SQL Database および SQL Data Warehouse のデータの検出と分類](sql-database-data-discovery-and-classification.md)」をご覧ください。|
| エラスティック データベース ジョブ | 詳しくは、「[エラスティック ジョブの作成、構成、および管理](elastic-jobs-overview.md)」をご覧ください。 |
| エラスティック クエリ | 詳しくは、[エラスティック クエリの概要](sql-database-elastic-query-overview.md)に関する記事をご覧ください。 |
| エラスティック トランザクション | [クラウド データベースにまたがる分散トランザクション](sql-database-elastic-transactions-overview.md)。 |
|メモリ許可フィードバック (行モード) (互換性レベル 150)|詳しくは、「[行モード メモリ許可フィードバック](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)」をご覧ください。|
| Azure portal のクエリ エディター |詳しくは、「[Azure portal の SQL クエリ エディターを使用した接続とデータの照会](sql-database-connect-query-portal.md)」をご覧ください。|
| 単一データベースとエラスティック プールでの R サービス/機械学習 |詳しくは、「[Machine Learning Services in Azure SQL Database (Azure SQL Database での Machine Learning Services)](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)」をご覧ください。|
| サーバーレス コンピューティング レベル | 詳細については、「[SQL Database serverless (preview) (SQL Database サーバーレス (プレビュー))](sql-database-serverless.md)」を参照してください。|
|SQL Analytics|詳細については、[Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) に関するページをご覧ください。|
|テーブル変数の遅延コンパイル (互換性レベル 150)|詳しくは、「[テーブル変数の遅延コンパイル](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)」をご覧ください。|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Managed Instance](#tab/managed-instance)

| 機能 | 詳細 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">インスタンス プール</a> | 比較的小規模な SQL インスタンスをクラウドに移行するための、便利かつ費用対効果に優れた方法です。 |
| <a href="https://aka.ms/managed-instance-tde-byok">Bring Your Own Key (BYOK) による透過的なデータ暗号化 (TDE)</a> |詳しくは、「[Azure Key Vault のユーザー管理キーを使用した Azure SQL Transparent Data Encryption: Bring Your Own Key のサポート](transparent-data-encryption-byok-azure-sql.md)」をご覧ください。|
| <a href="https://aka.ms/managed-instance-aadlogins">インスタンス レベルの Azure AD サーバー プリンシパル (ログイン)</a> | <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> ステートメントを使って、サーバーレベルのログインを作成します。 |
| [トランザクション レプリケーション](sql-database-managed-instance-transactional-replication.md) | テーブルからの変更をマネージド インスタンス、単一データベース、または SQL Server インスタンスに配置された他のデータベースにレプリケートするか、他のマネージド インスタンスまたは SQL Server インスタンス内で一部の行が変更されたときにテーブルを更新します。 詳しくは、「[Azure SQL Database マネージド インスタンス データベースにレプリケーションを構成する](replication-with-sql-database-managed-instance.md)」をご覧ください。 |
| 脅威の検出 |詳しくは、「[Azure SQL Database Managed Instance で脅威検出を構成する](sql-database-managed-instance-threat-detection.md)」をご覧ください。|
| マネージ インスタンスで削除されたデータベースを再作成する |詳しくは、「[Re-create dropped databases in Azure SQL Managed Instance (Azure SQL Managed Instance で削除されたデータベースを再作成する)](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)」をご覧ください。|
| &nbsp; |

---

## <a name="new-features"></a>新機能

### <a name="managed-instance-h2-2019-updates"></a>2019 年下期のマネージド インスタンスの更新プログラム

- [グローバル トレース フラグ](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)を使用して、マネージド インスタンスの動作を構成します。

### <a name="managed-instance-h1-2019-updates"></a>2019 年上期のマネージド インスタンスの更新プログラム

2019 年上期のマネージド インスタンスのデプロイ モデルでは、次の機能が有効になっています。
  - <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio サブスクライバー向けの Azure の月単位のクレジット</a>と強化された[リージョン制限](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)を備えたサブスクリプションのサポート
  - <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016、SharePoint 2019</a>、および <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a> のサポート
  - 選択した<a href="https://aka.ms/managed-instance-collation">サーバーレベルの照合順序</a>と<a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">タイム ゾーン</a>を使用して、インスタンスを作成します。
  - マネージド インスタンスは現在、<a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">組み込みのファイアウォール</a>によって保護されています。
  - より優れたネットワーク パフォーマンスと、<a href="https://aka.ms/four-cores-sql-mi-update">Gen5 ハードウェア世代における 4 vCores</a> またはポイント イン タイム リストアのための<a href="https://aka.ms/managed-instance-configurable-backup-retention">最大 35 日間のバックアップ保有期間の構成</a>を得るために、[パブリック エンドポイント](sql-database-managed-instance-public-endpoint-configure.md)と [Proxy Override](/sql-database-connectivity-architecture.md#connection-policy) 接続を使用するように、インスタンスを構成します。 長期のバックアップ保有期間 (最大 10 年) はまだ有効になっていないので、代わりに、<a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">コピーのみのバックアップ</a>を使用できます。
  - 新しい機能を利用すると、<a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell を使用してお使いのデータベースを別のデータ センターに geo リストアして</a>、[データベース名を変更し](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)、[仮想クラスターを削除する](sql-database-managed-instance-delete-virtual-cluster.md)ことができます。
  - 新しい組み込みの[インスタンス共同作成者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor)を使用すると、セキュリティ原則による職務の分離 (SoD) のコンプライアンスとエンタープライズ標準によるコンプライアンスを有効にできます。
  - マネージド インスタンスは、中国北部 2 および中国東部 2 と、GA (US Gov テキサス、US Gov アリゾナ) の Azure Government リージョン内で利用できます。 また、次のパブリック リージョンでも利用できます。オーストラリア中部、オーストラリア中部 2、ブラジル南部、フランス南部、アラブ首長国連邦中部、アラブ首長国連邦北部、南アフリカ北部、南アフリカ西部です。

## <a name="fixed-known-issues"></a>修正された既知の問題

- **2019 年 8 月** - 包含データベースは、マネージド インスタンス内で完全にサポートされています。

## <a name="updates"></a>更新プログラム

SQL Database の更新情報や機能強化の一覧については、[SQL Database サービスの更新情報](https://azure.microsoft.com/updates/?product=sql-database)を参照してください。

すべての Azure サービスの更新情報や機能強化については、[サービスの更新情報](https://azure.microsoft.com/updates)を参照してください。

## <a name="contribute-to-content"></a>コンテンツの改善への協力

Azure SQL Database のドキュメント コンテンツに協力するには、「[Microsoft Docs 共同作成者ガイド概要](https://docs.microsoft.com/contribute/)」を参照してください。
