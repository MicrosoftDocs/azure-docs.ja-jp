---
title: "Azure SQL Database の機能の概要 | Microsoft Docs"
description: "このページには、Azure SQL Database の論理サーバーとデータベースについての概要が記載され、各機能へのリンク付きの機能のサポート マトリックスが含まれています。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: e052109ff10f21c3a737f336479fe43fb54e500e
ms.lasthandoff: 03/14/2017


---
# <a name="azure-sql-database-features"></a>Azure SQL Database の機能
このトピックには、Azure SQL Database の論理サーバーとデータベースについての概要が記載され、各機能へのリンク付きの機能のサポート マトリックスが含まれています。 

## <a name="what-is-an-azure-sql-database-logical-server"></a>Azure SQL Database 論理サーバーとは
Azure SQL Database 論理サーバーは、複数のデータベースの中央管理ポイントとして機能します。 SQL Database におけるサーバーとは、オンプレミスでなじみのある SQL Server インスタンスとは別の論理コンストラクトです。 具体的には、SQL Database サービスは論理サーバーに関連したデータベースの場所について保証しません。また、インスタンス レベルのアクセスまたは機能を公開しません。 Azure SQL 論理サーバーの詳細については、[論理サーバー](sql-database-server-overview.md)に関する記事を参照してください。 

## <a name="what-is-an-azure-sql-database"></a>Azure SQL データベースとは
Azure SQL Database 内の各データベースは、論理サーバーに関連付けられています。 データベースには以下の種類があります。

- 単一のデータベースとその[独自のリソースのセット](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU)
- [リソースのセットを共有する](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus)[データベースのプール](sql-database-elastic-pool.md)の一部 (eDTU)
- [シャード化されたデータベースのスケール アウトされたセット](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling)の一部 (単一またはプールされたデータベースのいずれか)
- [マルチテナント SaaS デザイン パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)に参加しているデータベースのセットの一部 (これらのデータベースは、単一またはプールされたデータベースのいずれか (またはその両方)) 

Azure SQL Database の詳細については、[SQL Database](sql-database-overview.md) に関するページをご覧ください。

## <a name="what-features-are-supported"></a>どのような機能がサポートされていますか?

次の表では、Azure SQL Database と SQL Server の主要な機能の一覧が示され、そのサポートが指定され、各プラットフォームの機能に関する詳細情報へのリンクが提供されます。 Transact-SQL 機能については、機能のカテゴリの表にあるリンクを参照してください。 特定の種類の機能についてサポートが不足している理由の背景については、「[Azure SQL Database の Transact-SQL の相違点](sql-database-transact-sql-information.md)」を参照してください。

V12 には継続的に機能が追加されます。 したがって、Azure のサービス更新情報に関する Web ページにアクセスし、次のフィルターを使用することをお勧めします。

* サービスを [[SQL Database]](https://azure.microsoft.com/updates/?service=sql-database)でフィルター処理します。
* SQL Database の機能について、[General Availability] [(GA) のアナウンス](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) でフィルター処理します。

> [!TIP]
> 既存のデータベースと Azure SQL Database の互換性をテストするには、[SQL Server Database から Azure への移行](sql-database-cloud-migrate.md)に関するページをご覧ください。
>

| **機能** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| アクティブ geo レプリケーションを選択するとき | サポートされていません - [AlwaysOn 可用性グループ](https://msdn.microsoft.com/library/hh510230.aspx)に関する記事を参照してください | [サポートされています](sql-database-geo-replication-overview.md)
| Always Encrypted | [サポートされています](https://msdn.microsoft.com/library/mt163865.aspx) | [サポートされています](sql-database-always-encrypted.md) |
| AlwaysOn 可用性グループ | [サポートされています](https://msdn.microsoft.com/library/hh510230.aspx) | サポートされていません - [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください |
| データベースの接続 | [サポートされています](https://msdn.microsoft.com/library/ms190209.aspx) | サポートされていません |
| アプリケーション ロール | [サポートされています](https://msdn.microsoft.com/library/ms190998.aspx) | [サポートされています](https://msdn.microsoft.com/library/ms190998.aspx) |
| 自動スケール | サポートされていません | [サポートされています](sql-database-service-tiers.md) |
| Azure Active Directory | サポートされていません | [サポートされています](sql-database-aad-authentication.md) |
| Azure Data Factory | [サポートされています](../data-factory/data-factory-introduction.md) | [サポートされています](../data-factory/data-factory-introduction.md) |
| 監査 | [サポートされています](https://msdn.microsoft.com/library/cc280386.aspx) | [サポートされています](sql-database-auditing.md) |
| BACPAC ファイル (エクスポート) | [サポートされています](https://msdn.microsoft.com/library/hh213241.aspx) | [サポートされています](sql-database-export.md) |
| BACPAC ファイル (インポート) | [サポートされています](https://msdn.microsoft.com/library/hh710052.aspx) | [サポートされています](sql-database-import-portal.md) |
| BACKUP および RESTORE ステートメント | [サポートされています](https://msdn.microsoft.com/library/ff848768.aspx) | サポートされていません |
| 組み込み関数 | [サポートされています](https://msdn.microsoft.com/library/ms174318.aspx) | [ほとんど](https://msdn.microsoft.com/library/ms174318.aspx) |
| 変更データのキャプチャ | [サポートされています](https://msdn.microsoft.com/library/cc645937.aspx) | サポートされていません |
| 変更の追跡 | [サポートされています](https://msdn.microsoft.com/library/bb933875.aspx) | [サポートされています](https://msdn.microsoft.com/library/bb933875.aspx) |
| 照合順序ステートメント | [サポートされています](https://msdn.microsoft.com/library/ff848763.aspx) | [サポートされています](https://msdn.microsoft.com/library/ff848763.aspx) |
| 列ストア インデックス | [サポートされています](https://msdn.microsoft.com/library/gg492088.aspx) | [Premium Edition のみ](https://msdn.microsoft.com/library/gg492088.aspx) |
| 共通言語ランタイム (CLR) | [サポートされています](https://msdn.microsoft.com/library/ms131102.aspx) | サポートされていません |
| 包含データベース | [サポートされています](https://msdn.microsoft.com/library/ff929071.aspx) | ビルトイン |
| 包含ユーザー | [サポートされています](https://msdn.microsoft.com/library/ff929188.aspx) | [サポートされています](sql-database-manage-logins.md#non-administrator-users) |
| フロー制御言語のキーワード | [サポートされています](https://msdn.microsoft.com/library/ms174290.aspx) | [サポートされています](https://msdn.microsoft.com/library/ms174290.aspx) |
| 複数データベース間のクエリ | [サポートされています](https://msdn.microsoft.com/library/dn584627.aspx) | [エラスティック クエリ](sql-database-elastic-query-overview.md) |
| カーソル | [サポートされています](https://msdn.microsoft.com/library/ms181441.aspx) | [サポートされています](https://msdn.microsoft.com/library/ms181441.aspx) | 
| データ圧縮 | [サポートされています](https://msdn.microsoft.com/library/cc280449.aspx) | [サポートされています](https://msdn.microsoft.com/library/cc280449.aspx) |
| データベースのバックアップ | [ユーザーに公開](https://msdn.microsoft.com/library/ms187048.aspx) | [ビルトイン](sql-database-automated-backups.md) |
| データベース メール | [サポートされています](https://msdn.microsoft.com/library/ms189635.aspx) | サポートされていません |
| データベース ミラーリング | [サポートされています](https://msdn.microsoft.com/library/ms189852.aspx) | サポートされていません |
| データベース構成オプション | [サポートされています](https://msdn.microsoft.com/library/mt629158.aspx) | [サポートされています](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services (DQS) | [サポートされています](https://msdn.microsoft.com/library/ff877925.aspx) | サポートされていません |
| データベース スナップショット | [サポートされています](https://msdn.microsoft.com/library/ms175158.aspx) | サポートされていません |
| データの種類 | [サポートされています](https://msdn.microsoft.com/library/ms187752.aspx) | [サポートされています](https://msdn.microsoft.com/library/ms187752.aspx) |  
| DBCC ステートメント | [すべて](https://msdn.microsoft.com/library/ms188796.aspx) | [一部](https://msdn.microsoft.com/library/ms188796.aspx) |
| DDL ステートメント | [サポートされています](https://msdn.microsoft.com/library/ff848799.aspx) | [ほとんど](https://msdn.microsoft.com/library/ff848799.aspx)
| DDL トリガー | [サポートされています](https://msdn.microsoft.com/library/ms175941.aspx) | [データベースのみ](https://msdn.microsoft.com/library/ms175941.aspx) |
| 分散トランザクション | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | 限定された SQL Database 内のシナリオのみ |
| DML ステートメント | [サポートされています](https://msdn.microsoft.com/library/ff848766.aspx) | [ほとんど](https://msdn.microsoft.com/library/ff848766.aspx) |
| DML トリガー | [サポートされています](https://msdn.microsoft.com/library/ms178110.aspx) | [サポートされています](https://msdn.microsoft.com/library/ms178110.aspx) |
| DMV | [すべて](https://msdn.microsoft.com/library/ms188754.aspx) | [一部](https://msdn.microsoft.com/library/ms188754.aspx) |
| エラスティック プール | サポートされていません | [サポートされています](sql-database-elastic-pool.md) |
| エラスティック ジョブ | サポートされていません - 「[SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx)」を参照してください | [サポートされています](sql-database-elastic-jobs-getting-started.md) | 
| エラスティック クエリ | サポートされていません - 「[複数データベースにまたがるクエリ](https://msdn.microsoft.com/library/dn584627.aspx)」を参照してください | [サポートされています](sql-database-elastic-query-overview.md) |
| イベント通知 | [サポートされています](https://msdn.microsoft.com/library/ms186376.aspx) | [サポートされています](sql-database-insights-alerts-portal.md) |
| 式 | [サポートされています](https://msdn.microsoft.com/library/ms190286.aspx) | [サポートされています](https://msdn.microsoft.com/library/ms190286.aspx) |
| 拡張イベント | [サポートされています](https://msdn.microsoft.com/library/bb630282.aspx) | [一部](sql-database-xevent-db-diff-from-svr.md) |
| 拡張ストアド プロシージャ | [サポートされています](https://msdn.microsoft.com/library/ms164627.aspx) | サポートされていません |
| ファイル グループ | [サポートされています](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [プライマリのみ](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| Filestream | [サポートされています](https://msdn.microsoft.com/library/gg471497.aspx) | サポートされていません |
| フルテキスト検索 | [サポートされています](https://msdn.microsoft.com/library/ms142571.aspx) | [サード パーティのワード ブレーカーはサポートされていません](https://msdn.microsoft.com/library/ms142571.aspx) |
| 関数 | [サポートされています](https://msdn.microsoft.com/library/ms174318.aspx) | [ほとんど](https://msdn.microsoft.com/library/ms174318.aspx) |
| メモリ内の最適化 | [サポートされています](https://msdn.microsoft.com/library/dn133186.aspx) | [Premium Edition のみ](https://msdn.microsoft.com/library/dn133186.aspx) |
| [ジョブ] | [SQL Server エージェント](https://msdn.microsoft.com/library/ms189237.aspx) | [サポートされています](sql-database-elastic-jobs-getting-started.md) |
| JSON データのサポート | [サポートされています](https://msdn.microsoft.com/library/dn921897.aspx) | [サポートされています](sql-database-json-features.md) |
| 言語要素 | [サポートされています](https://msdn.microsoft.com/library/ff848807.aspx) | [ほとんど](https://msdn.microsoft.com/library/ff848807.aspx) |  
| リンク サーバー | [サポートされています](https://msdn.microsoft.com/library/ms188279.aspx) | サポートされていません - [エラスティック クエリ](sql-database-elastic-query-horizontal-partitioning.md)に関する記事を参照してください |
| ログ配布 | [サポートされています](https://msdn.microsoft.com/library/ms187103.aspx) | サポートされていません - [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください |
| 管理コマンド | [サポートされています](https://msdn.microsoft.com/library/ms190286.aspx)| [サポートされていません](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services (MDS) | [サポートされています](https://msdn.microsoft.com/library/ff487003.aspx) | サポートされていません |
| 一括インポートでの最小ログ記録 | [サポートされています](https://msdn.microsoft.com/library/ms190422.aspx) | サポートされていません |
| システム データの変更 | [サポートされています](https://msdn.microsoft.com/library/ms178028.aspx) | サポートされていません |
| オンライン インデックス操作 | [サポートされています](https://msdn.microsoft.com/library/ms177442.aspx) | [サービス層で制限されるトランザクションのサイズ](https://msdn.microsoft.com/library/ms177442.aspx) |
| 演算子 | [サポートされています](https://msdn.microsoft.com/library/ms174986.aspx) | [ほとんど](https://msdn.microsoft.com/library/ms174986.aspx) |
| データベースのポイントインタイム リストア | [サポートされています](https://msdn.microsoft.com/library/ms179451.aspx) | [サポートされています](sql-database-recovery-using-backups.md#point-in-time-restore) |
| PolyBase | [サポートされています](https://msdn.microsoft.com/library/mt143171.aspx) | [サポートされていません]
| ポリシー ベースの管理 | [サポートされています](https://msdn.microsoft.com/library/bb510667.aspx) | サポートされていません |
| 述語 | [サポートされています](https://msdn.microsoft.com/library/ms189523.aspx) | [ほとんど](https://msdn.microsoft.com/library/ms189523.aspx)
| R Services | [サポートされています](https://msdn.microsoft.com/library/mt604845.aspx)
| リソース ガバナー | [サポートされています](https://msdn.microsoft.com/library/bb933866.aspx) | サポートされていません |
| バックアップからデータベースを復元する | [サポートされています](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [ビルトイン バックアップからのみ](sql-database-recovery-using-backups.md) |
| 行レベルのセキュリティ | [サポートされています](https://msdn.microsoft.com/library/dn765131.aspx) | [サポートされています](https://msdn.microsoft.com/library/dn765131.aspx) |
| セキュリティ ステートメント | [サポートされています](https://msdn.microsoft.com/library/ff848791.aspx) | [一部](https://msdn.microsoft.com/library/ff848791.aspx) |
| セマンティック検索 | [サポートされています](https://msdn.microsoft.com/library/gg492075.aspx) | サポートされていません |
| シーケンス番号 | [サポートされています](https://msdn.microsoft.com/library/ff878058.aspx) | [サポートされています](https://msdn.microsoft.com/library/ff878058.aspx) |
| Service Broker | [サポートされています](https://msdn.microsoft.com/library/bb522893.aspx) | サポートされていません |
| サーバー構成オプション | [サポートされています](https://msdn.microsoft.com/library/ms189631.aspx) | サポートされていません - [データベース構成オプション](https://msdn.microsoft.com/library/mt629158.aspx)に関する記事を参照してください |
| SET ステートメント | [サポートされています](https://msdn.microsoft.com/library/ms190356.aspx) | [ほとんど](https://msdn.microsoft.com/library/ms190356.aspx) 
| Spatial | [サポートされています](https://msdn.microsoft.com/library/bb933790.aspx) | [サポートされています](https://msdn.microsoft.com/library/bb933790.aspx) |
| SQL Server エージェント | [サポートされています](https://msdn.microsoft.com/library/ms189237.aspx) | サポートされていません - [エラスティック ジョブ](sql-database-elastic-jobs-getting-started.md)に関する記事を参照してください |
| SQL Server Analysis Services (SSAS) | [サポートされています](https://msdn.microsoft.com/library/bb522607.aspx) | サポートされていません - 「[Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/)」を参照してください |
| SQL Server Integration Services (SSIS) | [サポートされています](https://msdn.microsoft.com/library/ms141026.aspx) | サポートされていません - [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) に関するページを参照してください |
| SQL Server PowerShell | [サポートされています](https://msdn.microsoft.com/library/hh245198.aspx) | [サポートされています](https://msdn.microsoft.com/library/hh245198.aspx) |
| SQL Server プロファイラー | [サポートされています](https://msdn.microsoft.com/library/ms181091.aspx) | サポートされていません - [拡張イベント](https://msdn.microsoft.com/library/ms181091.aspx)に関する記事を参照してください |
| SQL Server レプリケーション | [サポートされています](https://msdn.microsoft.com/library/ms151198.aspx) | [トランザクション レプリケーションとスナップショット レプリケーションのサブスクライバーのみ](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [サポートされています](https://msdn.microsoft.com/library/ms159106.aspx) | サポートされていません |
| ストアド プロシージャ | [サポートされています](https://msdn.microsoft.com/library/ms190782.aspx) | [サポートされています](https://msdn.microsoft.com/library/ms190782.aspx) |
| システム ストアド関数 | [サポートされています](https://msdn.microsoft.com/library/ff848780.aspx) | [一部](https://msdn.microsoft.com/library/ff848780.aspx) |
| システム ストアド プロシージャ | [サポートされています](https://msdn.microsoft.com/library/ms187961.aspx) | [一部](https://msdn.microsoft.com/library/ms187961.aspx) |
| システム テーブル | [サポートされています](https://msdn.microsoft.com/library/ms179932.aspx) | [一部](https://msdn.microsoft.com/library/ms179932.aspx) |
| システム ビュー | [サポートされています](https://msdn.microsoft.com/library/ms177862.aspx) | [一部](https://msdn.microsoft.com/library/ms177862.aspx)
| テーブル パーティション | [サポートされています](https://msdn.microsoft.com/library/ms190787.aspx) | [プライマリ ファイル グループのみ](https://msdn.microsoft.com/library/ms190787.aspx) |
| 一時テーブル | [ローカルおよびグローバル](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [ローカルのみ](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| テンポラル テーブル | [サポートされています](https://msdn.microsoft.com/library/dn935015.aspx) | [サポートされています](sql-database-temporal-tables.md) |
| トランザクション ステートメント | [サポートされています](https://msdn.microsoft.com/library/ms174377.aspx) | [サポートされています](https://msdn.microsoft.com/library/ms174377.aspx) |
| variables | [サポートされています](https://msdn.microsoft.com/library/ff848809.aspx) | | [サポートされています](https://msdn.microsoft.com/library/ff848809.aspx) | 
| Transparent Data Encryption (TDE)  | [サポートされています](https://msdn.microsoft.com/library/bb934049.aspx) | [サポートされています](https://msdn.microsoft.com/dn948096.aspx) |
| Windows Server フェールオーバー クラスタリング | [サポートされています](https://msdn.microsoft.com/library/hh270278.aspx) | サポートされていません - [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください |
| XML インデックス | [サポートされています](http://msdn.microsoft.com/library/bb934097.aspx) | [サポートされています](http://msdn.microsoft.com/library/bb934097.aspx) |
| XML ステートメント | [サポートされています](https://msdn.microsoft.com/library/ff848798.aspx) | [サポートされています](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>次のステップ

- Azure SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」を参照してください。
- Azure SQL 論理サーバーの概要については、[SQL Database 論理サーバーの概要](sql-database-server-overview.md)に関する記事を参照してください。
- Azure SQL Database の概要については、[SQL Database の概要](sql-database-overview.md)に関する記事を参照してください。
- Transact-SQL のサポートおよび違いについては、「[Azure SQL Database の Transact-SQL の相違点](sql-database-transact-sql-information.md)」を参照してください。
- サービス階層に基づく特定のリソース クォータと制限については、**サービス階層**に関する記事を参照してください。 サービス レベルの概要については、「 [SQL Database のサービス階層](sql-database-service-tiers.md)」を参照してください。
- セキュリティの概要については、[Azure SQL Database のセキュリティの概要](sql-database-security-overview.md)に関するページを参照してください。
- SQL Database の利用可能なドライバーとサポートの詳細については、「 [SQL Database および SQL Server の接続ライブラリ](sql-database-libraries.md)」を参照してください。

