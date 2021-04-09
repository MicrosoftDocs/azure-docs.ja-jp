---
title: 専用 SQL プール (旧称 SQL DW) のトラブルシューティング
description: Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) のトラブルシューティング。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 949a89dd4e84bdb44ad15eaff9ca160fdd21b395
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602130"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) のトラブルシューティング

この記事では、Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) における一般的な問題のトラブルシューティングについて説明します。

## <a name="connecting"></a>接続

| 問題                                                        | 解決方法                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| ユーザー ' NT AUTHORITY\ANONYMOUS LOGON' はログインできませんでした。 (Microsoft SQL Server、エラー:18456) | このエラーは、Azure AD ユーザーが master データベースに接続しようとするが、マスターにユーザーがいない場合に発生します。  この問題を解決するには、接続時に接続する専用 SQL プール (旧称 SQL DW) を指定するか、マスター データベースにユーザーを追加します。  詳細については、 [セキュリティの概要](sql-data-warehouse-overview-manage-security.md) に関する記事を参照してください。 |
| サーバー プリンシパル"MyUserName" が、現在のセキュリティ コンテキストでデータベース "master" にアクセスできません。 ユーザーの既定データベースを開けません。 ログインできませんでした。 ユーザー 'MyUserName' はログインできませんでした。 (Microsoft SQL Server、エラー:916) | このエラーは、Azure AD ユーザーが master データベースに接続しようとするが、マスターにユーザーがいない場合に発生します。  この問題を解決するには、接続時に接続する専用 SQL プール (旧称 SQL DW) を指定するか、マスター データベースにユーザーを追加します。  詳細については、 [セキュリティの概要](sql-data-warehouse-overview-manage-security.md) に関する記事を参照してください。 |
| CTAIP エラー                                                  | このエラーは、ログインが特定の SQL データベースではなく、SQL Database マスター データベース上で作成された場合に発生する可能性があります。  このエラーが発生した場合は、 [セキュリティの概要](sql-data-warehouse-overview-manage-security.md) に関する記事を参照してください。  この記事では、マスター データベースにログインとユーザーを作成する方法、および SQL データベースにユーザーを作成する方法について説明します。 |
| ファイアウォールによってブロックされる                                          | 専用 SQL プール (旧称 SQL DW) は、既知の IP アドレスのみがデータベースにアクセスできるように、ファイアウォールによって保護されます。 ファイアウォールは、既定でセキュリティ保護されています。つまり、接続する前に、IP アドレスまたはアドレス範囲を明示的に有効にする必要があります。  ファイアウォールでアクセスを構成するには、[プロビジョニングの手順](create-data-warehouse-portal.md)の[クライアント IP 用のサーバー ファイアウォール アクセスの構成](create-data-warehouse-portal.md)に関するセクションの手順に従ってください。 |
| ツールまたはドライバーで接続できない                           | 専用 SQL プール (旧称 SQL DW) では、[SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)、[SSDT for Visual Studio](sql-data-warehouse-install-visual-studio.md)、または [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) を使用してデータのクエリを実行することをお勧めします。 ドライバーの詳細や Azure Synapse への接続の詳細については、[Azure SQL Data Warehouse のドライバー](sql-data-warehouse-connection-strings.md)に関する記事および [Azure Synapse への接続](sql-data-warehouse-connect-overview.md)に関する記事をご覧ください。 |

## <a name="tools"></a>ツール

| 問題                                                        | 解決方法                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio オブジェクト エクスプローラーに Azure AD ユーザーが表示されない           | これは既知の問題です。  回避策として、ユーザーを [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) で表示してください。  専用 SQL プール (旧称 SQL DW) での Azure Active Directory の使用方法の詳細については、[Azure Synapse に対する認証](sql-data-warehouse-authentication.md)に関する記事をご覧ください。 |
| 手動でのスクリプト作成、スクリプト作成ウィザードの使用、または SSMS を介した接続が、遅かったり、応答しなかったり、エラーが発生したりする | ユーザーがマスター データベース内に作成されているかどうかを確認してください。 また、スクリプト作成オプションで、エンジンのエディションが "Microsoft Azure Synapse Analytics Edition" と設定されており、エンジンの種類が "Microsoft Azure SQL Database" となっていることも確認してください。 |
| SSMS でスクリプト生成に失敗する                               | [依存オブジェクトのスクリプトを生成] オプションが [True] に設定されている場合、専用 SQL プール (旧称 SQL DW) のスクリプト生成が失敗します。 回避策として、ユーザーは手動で **[ツール]、[オプション]、[SQL Server オブジェクト エクスプローラー] の順に選択し、[Generate script for dependent objects]\(依存オブジェクトのスクリプトを生成する\) オプションを [false] に設定する** 必要があります。 |

## <a name="data-ingestion-and-preparation"></a>データの取り込みと準備

| 問題                                                        | 解決方法                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| CETAS を使用して空の文字列をエクスポートすると、Parquet と ORC のファイルの値が NULL になります。 NOT NULL 制約のある列から空の文字列をエクスポートする場合は、CETAS によってレコードが拒否され、エクスポートが失敗する可能性があることに注意してください。 | CETAS の SELECT ステートメントで、空の文字列または問題のある列を削除します。 |
| Parquet および ORC ファイル形式の tinyint 型の列に 0 ～ 127 の範囲外の値を読み込むことはサポートされていません。 | ターゲット列に対してより大きなデータ型を指定してください。           |

## <a name="performance"></a>パフォーマンス

| 問題                                                        | 解決方法                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| クエリ パフォーマンスのトラブルシューティング                            | 特定のクエリのトラブルシューティングを行う必要がある場合は、 [クエリを監視する方法](sql-data-warehouse-manage-monitor.md#monitor-query-execution)に関する記事を参照してください。 |
| TempDB の領域に関する問題 | [TempDB の領域の使用状況を監視](sql-data-warehouse-manage-monitor.md#monitor-tempdb)します。  TempDB の領域が不足している一般的な原因は次のとおりです。<br>- クエリに割り当てられたリソースが不足しているため、データが TempDB に書き込まれます。  [ワークロード管理](resource-classes-for-workload-management.md)に関する記事を参照してください。 <br>- 統計が不足しているか、期限切れのため、データ移動が過剰になっています。  統計を作成する方法の詳細については、[テーブルの統計の管理](sql-data-warehouse-tables-statistics.md)に関する記事を参照してください。<br>- TempDB の領域はサービス レベルごとに割り当てられます。  [専用 SQL プール (旧称 SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) をより大きな DWU 設定にスケーリングすると、TempDB の領域がさらに割り当てられます。|
| 統計情報の不足を原因とする、クエリのパフォーマンス低下と不適切なプラン | パフォーマンスの低下の最も一般的な原因は、テーブルの統計情報の不足です。  統計を作成する方法と、それらがパフォーマンスにとって重要な理由の詳細については、[テーブルの統計の管理](sql-data-warehouse-tables-statistics.md)に関する記事をご覧ください。 |
| 低いコンカレンシーとキューに置かれたクエリ                             | [ワークロード管理](resource-classes-for-workload-management.md) を理解することは、コンカレンシーでのメモリの割り当てのバランスを調整する方法を理解するために重要です。 |
| ベスト プラクティスを実装する方法                              | クエリのパフォーマンスを向上させる方法については、[専用 SQL プール (旧称 SQL DW) のベスト プラクティス](sql-data-warehouse-best-practices.md)に関する記事をご覧ください。 |
| スケーリングでパフォーマンスを向上させる方法                      | [専用 SQL プール (旧称 SQL DW) をスケーリング](sql-data-warehouse-manage-compute-overview.md)して、クエリのコンピューティング能力を強化するだけで、パフォーマンスを改善できる場合があります。 |
| インデックスの品質が低いことによるクエリ パフォーマンスの低下     | [列ストア インデックスの品質の低さ](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)が原因で、クエリの処理速度が低下する場合があります。  詳細と [インデックスを再構築してセグメントの品質を向上する](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)方法に関する記事を参照してください。 |

## <a name="system-management"></a>システム管理

| 問題                                                        | 解決方法                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| メッセージ 40847:サーバーが許容データベース トランザクション単位クォータ 45000 を超えることになるため、操作を実行できませんでした。 | 作成しようとしているデータベースの [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) を減らすか、[クォータの引き上げを要求](sql-data-warehouse-get-started-create-support-ticket.md)してください。 |
| 領域使用率の調査                              | システムの領域使用率の詳細については、 [テーブルのサイズ](sql-data-warehouse-tables-overview.md#table-size-queries) に関するトピックをご覧ください。 |
| テーブルの管理に関するヘルプ                                    | テーブルの管理については、[テーブルの概要](sql-data-warehouse-tables-overview.md)に関する記事をご覧ください。  この記事には、[テーブルのデータ型](sql-data-warehouse-tables-data-types.md)、[テーブルの分散](sql-data-warehouse-tables-distribute.md)、[テーブルのインデックス作成](sql-data-warehouse-tables-index.md)、[テーブルのパーティション分割](sql-data-warehouse-tables-partition.md)、[テーブルの統計の管理](sql-data-warehouse-tables-statistics.md)、[一時テーブル](sql-data-warehouse-tables-temporary.md)などのより詳細なトピックへのリンクも含まれています。 |
| Azure portal で、透過的なデータ暗号化 (TDE) の進行状況バーが更新されない | [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) を使用すると、TDE の状態を表示できます。 |

## <a name="differences-from-sql-database"></a>SQL Database との違い

| 問題                                 | 解決方法                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| サポートされていない SQL Database の機能     | 「 [サポートされていないテーブルの機能](sql-data-warehouse-tables-overview.md#unsupported-table-features)」をご覧ください。 |
| サポートされていない SQL Database のデータ型   | 「 [サポートされていないデータ型](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types)」をご覧ください。        |
| ストアド プロシージャの制限事項          | ストアド プロシージャの制限事項のいくつかを理解するには、 [ストアド プロシージャの制限事項](sql-data-warehouse-develop-stored-procedures.md#limitations) に関するページをご覧ください。 |
| UDF が SELECT ステートメントをサポートしていない | これは、UDF の現在の制限です。  サポートされている構文については、 [CREATE FUNCTION](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) に関するページをご覧ください。 |
| 列の sp_rename (プレビュー) が *dbo* 以外のスキーマでは機能しない | これは、Synapse の[列の sp_rename (プレビュー)](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) に関する現在の制限です。  *dbo* スキーマの一部ではないオブジェクトの列は、CTAS を使用して新しいテーブルに名前を変更できます。 |

## <a name="next-steps"></a>次のステップ

問題の解決策は、以下のその他のリソースで探してみることができます。

* [ブログ](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [機能に関する要求](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [サポート チケットを作成する](sql-data-warehouse-get-started-create-support-ticket.md)
* [Microsoft Q&A 質問ページ](/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)