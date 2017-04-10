---
title: "Azure SQL Data Warehouse のトラブルシューティング | Microsoft Docs"
description: "Azure SQL Data Warehouse のトラブルシューティングを行います。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 03/03/2017
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 73f10984b7fe2636f5b9f664b831adc910e7ac7a
ms.lasthandoff: 03/04/2017


---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse のトラブルシューティング
このトピックでは、お客様から寄せられる一般的なトラブルシューティングに関する質問の一部を一覧で紹介します。

## <a name="connecting"></a>接続
| 問題 | 解決策 |
|:--- |:--- |
| ユーザー ' NT AUTHORITY\ANONYMOUS LOGON' はログインできませんでした。 (Microsoft SQL Server、エラー: 18456) |このエラーは、AAD ユーザーが master データベースに接続しようとするが、マスターにユーザーがいない場合に発生します。  この問題を解決するには、接続時に接続する SQL Data Warehouse を指定するか、master データベースにユーザーを追加します。  詳細については、[セキュリティの概要][Security overview]に関する記事をご覧ください。 |
| サーバー プリンシパル"MyUserName" が、現在のセキュリティ コンテキストでデータベース "master" にアクセスできません。 ユーザー既定のデータベースを開けません。 ログインできませんでした。 ユーザー 'MyUserName' はログインできませんでした。 (Microsoft SQL Server、エラー: 916) |このエラーは、AAD ユーザーが master データベースに接続しようとするが、マスターにユーザーがいない場合に発生します。  この問題を解決するには、接続時に接続する SQL Data Warehouse を指定するか、master データベースにユーザーを追加します。  詳細については、[セキュリティの概要][Security overview]に関する記事をご覧ください。 |
| CTAIP エラー |このエラーは、ログインが SQL Data Warehouse データベースではなく、SQL Server マスター データベース上で作成された場合に発生する可能性があります。  このエラーが発生した場合は、[セキュリティの概要][Security overview]に関する記事をご覧ください。  この記事では、マスター上にログインとユーザーを作成する方法と、SQL Data Warehouse データベースにユーザーを作成する方法について説明しています。 |
| ファイアウォールによってブロックされる |Azure SQL Database は、既知の IP アドレスのみがデータベースにアクセスできるように、サーバーとデータベース レベルのファイアウォールによって保護されています。 ファイアウォールは、既定でセキュリティ保護されています。つまり、接続する前に、IP アドレスまたはアドレス範囲を明示的に有効にする必要があります。  アクセスできるようにファイアウォールを構成するには、[プロビジョニングの手順][Provisioning instructions]に関する記事の、[クライアント IP 用のサーバー ファイアウォール アクセスの構成][Configure server firewall access for your client IP]に関するセクションの手順に従ってください。 |
| ツールまたはドライバーで接続できない |SQL Data Warehouse では、[SSMS][SSMS]、[SSDT for Visual Studio 2015][SSDT for Visual Studio 2015]、または [sqlcmd][sqlcmd] を使用してデータを照会することをお勧めします。 ドライバーおよび SQL Data Warehouse への接続の詳細については、「[Azure SQL Data Warehouse のドライバー][Drivers for Azure SQL Data Warehouse]」および「[Azure SQL Data Warehouse への接続][Connect to Azure SQL Data Warehouse]」をご覧ください。 |

## <a name="tools"></a>ツール
| 問題 | 解決策 |
|:--- |:--- |
| Visual Studio オブジェクト エクスプローラーに AAD ユーザーが表示されない |これは既知の問題です。  回避策として、ユーザーを [sys.database_principals][sys.database_principals] で表示します。  SQL Data Warehouse での Azure Active Directory の使用方法の詳細については、「[Azure SQL Data Warehouse への認証][Authentication to Azure SQL Data Warehouse]」をご覧ください。 |

## <a name="performance"></a>パフォーマンス
| 問題 | 解決策 |
|:--- |:--- |
| クエリ パフォーマンスのトラブルシューティング |特定のクエリのトラブルシューティングを行う場合は、まず、[クエリを監視する方法][Learning how to monitor your queries]に関する記事をご覧ください。 |
| 統計情報の不足を原因とする、クエリのパフォーマンス低下と不適切なプラン |パフォーマンスの低下の最も一般的な原因は、テーブルの統計情報の不足です。  統計を作成する方法と、統計情報がパフォーマンスにとって重要な理由の詳細については、[テーブルの統計の管理][Statistics]に関する記事をご覧ください。 |
| 低い同時実行性とキューに置かれたクエリ |同時実行でのメモリの割り当てのバランスを調整する方法を理解するには、[ワークロード管理][Workload management]を理解することが重要です。 |
| ベスト プラクティスを実装する方法 |クエリのパフォーマンスを向上させる方法については、[SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse best practices]に関する記事をご覧ください。 |
| スケーリングでパフォーマンスを向上させる方法 |[SQL Data Warehouse をスケーリング][Scaling your SQL Data Warehouse]して、クエリのコンピューティング能力を強化するだけで、パフォーマンスを改善できる場合があります。 |
| インデックスの品質が低いことによるクエリ パフォーマンスの低下 |[列ストア インデックスの品質の低さ][Poor columnstore index quality]が原因で、クエリの処理速度が低下する場合があります。  詳細と、インデックスを再構築してセグメントの品質を向上させる方法については、[こちら][Rebuild indexes to improve segment quality]の記事をご覧ください。 |

## <a name="system-management"></a>システム管理
| 問題 | 解決策 |
|:--- |:--- |
| メッセージ 40847: サーバーが許容データベース トランザクション単位クォータ 45000 を超えることになるため、操作を実行できませんでした。 |作成しようとしているデータベースの [DWU][DWU] を減らすか、[クォータの引き上げを要求][request a quota increase]してください。 |
| 領域使用率の調査 |システムの領域使用率の詳細については、[テーブルのサイズ][Table sizes]に関するセクションをご覧ください。 |
| テーブルの管理に関するヘルプ |テーブルの管理については、[テーブルの概要][Overview]に関する記事をご覧ください。  この記事には、[テーブルのデータ型][Data types]、[テーブルの分散][Distribute]、[テーブルのインデックス作成][Index]、[テーブルのパーティション分割][Partition]、[テーブルの統計の管理][Statistics]、[一時テーブル][Temporary]などのより詳細なトピックへのリンクも含まれています。 |

## <a name="polybase"></a>PolyBase
| 問題 | 解決策 |
|:--- |:--- |
| サイズの大きい行を原因とする読み込みの失敗 |サイズの大きい行は、現在 PolyBase でサポートされていません。  つまり、テーブルに VARCHAR(MAX)、NVARCHAR(MAX)、または VARBINARY(MAX) が含まれる場合は、データの読み込みに外部テーブルを使用できません。  サイズの大きい行の読み込みは現在、Azure Data Factory (BCP を使用)、Azure Stream Analytics、SSIS、BCP、.NET の SQLBulkCopy クラスのみでサポートされています。 PolyBase でのサイズの大きい行のサポートは、将来のリリースで追加されます。 |
| MAX データ型を持つテーブルの bcp 読み込みの失敗 |VARCHAR(MAX)、NVARCHAR(MAX)、または VARBINARY(MAX) は、既知の問題により、一部のシナリオでテーブルの最後に配置する必要があります。  データ型が MAX の列をテーブルの末尾に移動してみてください。 |

## <a name="differences-from-sql-database"></a>SQL Database との違い
| 問題 | 解決策 |
|:--- |:--- |
| サポートされていない SQL Database の機能 |「[サポートされていないテーブルの機能][Unsupported table features]」をご覧ください。 |
| サポートされていない SQL Database のデータ型 |「[サポートされていないデータ型][Unsupported data types]」をご覧ください。 |
| DELETE と UPDATE の制限事項 |[UPDATE の回避策][UPDATE workarounds]、[DELETE の回避策][DELETE workarounds]、[サポートされていない UPDATE と DELETE の構文を回避するための CTAS の使用][Using CTAS to work around unsupported UPDATE and DELETE syntax]に関する各セクションをご覧ください。 |
| MERGE ステートメントがサポートされていない |[MERGE の対処方法][MERGE workarounds]に関するセクションをご覧ください。 |
| ストアド プロシージャの制限事項 |ストアド プロシージャの制限事項を理解するには、[ストアド プロシージャの制限事項][Stored procedure limitations]に関するセクションをご覧ください。 |
| UDF が SELECT ステートメントをサポートしていない |これは、UDF の現在の制限です。  サポートされている構文については、[CREATE FUNCTION][CREATE FUNCTION] に関するページをご覧ください。 |

## <a name="next-steps"></a>次のステップ
上記の方法で問題を解決できなかった場合は、次に示すその他のリソースを参照してください。

* [ブログ]
* [機能に関する要求]
* [ビデオ]
* [CAT チームのブログ]
* [サポート チケットを作成する]
* [MSDN フォーラム]
* [Stack Overflow フォーラム]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[サポート チケットを作成する]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md
[request a quota increase]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Learning how to monitor your queries]: ./sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: ./sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: ./sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[Table sizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Working around the PolyBase UTF-8 requirement]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT チームのブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN フォーラム]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow フォーラム]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

