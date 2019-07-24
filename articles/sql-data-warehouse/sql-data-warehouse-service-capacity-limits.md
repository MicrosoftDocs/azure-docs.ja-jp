---
title: 容量制限 - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse のさまざまなコンポーネントで使用できる最大値を示します。
services: sql-data-warehouse
author: sachinpMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 11/14/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 131a2102ec3ede930de3cad7516e486d793fec3d
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250566"
---
# <a name="sql-data-warehouse-capacity-limits"></a>SQL Data Warehouse の容量制限
Azure SQL Data Warehouse のさまざまなコンポーネントで使用できる最大値を示します。

## <a name="workload-management"></a>ワークロード管理
| Category | 説明 | 最大値 |
|:--- |:--- |:--- |
| [Data Warehouse ユニット (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |1 つの SQL Data Warehouse に対する 最大 DWU | Gen1:DW6000<br></br>Gen2:DW30000c |
| [Data Warehouse ユニット (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |サーバーあたりの既定の DTU |54,000<br></br>既定では、各 SQL Server (myserver.database.windows.net など) の DTU クォータは 54,000 に設定されており、最大 DW6000c が許可されます。 このクォータは単に安全上の制限です。 クォータを引き上げるには、[サポート チケットを作成](sql-data-warehouse-get-started-create-support-ticket.md)し、要求の種類として *[クォータ]* を選択します。  実際に必要な DTU を計算するには、必要とされる DWU の合計に 7.5 を掛けるか、必要とされる cDWU の合計に 9.0 を掛けます。 例: <br></br>DW6000 x 7.5 = 45,000 DTU<br></br>DW6000c x 9.0 = 54,000 DTU<br></br>現在の DTU 消費量は、ポータルで SQL Server オプションから確認できます。 DTU クォータには、一時停止しているデータベースと一時停止していないデータベースの両方が考慮されます。 |
| データベース接続 |同時に開かれるセッション数 |1024<br/><br/>1024 個のアクティブな各セッションが同時に、SQL Data Warehouse データベースに要求を送信できます。 同時に実行できるクエリ数については、制限があるので注意してください。 コンカレンシーの制限を超えると、要求は内部キューに送られ、処理の順番が来るまで待機します。 |
| データベース接続 |準備されたステートメントに対する最大メモリ容量 |20 MB |
| [ワークロード管理](resource-classes-for-workload-management.md) |同時クエリの最大数 |128<br/><br/> SQL Data Warehouse は、最大 128 個の同時実行クエリと、キューに残っているクエリを実行します。<br/><br/>ユーザーが割り当てられているリソース クラスが高いほど、または SQL Data Warehouse の [Data Warehouse ユニット](memory-and-concurrency-limits.md)の設定が低いほど、同時実行クエリの数が減る可能性があります。 一部のクエリ (DMV クエリなど) は、常に実行を許可され、同時実行クエリの制限に影響しません。 コンカレント クエリの詳細については、[コンカレンシーの最大値](memory-and-concurrency-limits.md#concurrency-maximums)に関する記事を参照してください。 |
| [tempdb](sql-data-warehouse-tables-temporary.md) |最大 GB |DW100 あたり 399 GB です。 そのため、DWU1000 では、tempdb のサイズは 3.99 TB になります。 |

## <a name="database-objects"></a>データベース オブジェクト
| Category | 説明 | 最大値 |
|:--- |:--- |:--- |
| Database |最大サイズ | Gen1:240 TB (ディスク上の圧縮)。 この領域は tempdb またはログ領域から独立しています。そのため、この領域はパーマネント テーブル専用です。  クラスター化列ストアの圧縮率は約 5 倍になります。  このため、すべてのテーブルをクラスター化列ストア (既定のテーブルの種類) にした場合、データベースを約 1 PB まで拡大できるようになります。 <br/><br/> Gen2:240 TB (行ストア) および無制限のストレージ (列ストア テーブル) |
| テーブル |最大サイズ |60 TB (ディスク上の圧縮) |
| テーブル |データベースあたりのテーブル数 | 100,000 |
| テーブル |テーブルあたりの列数 |1,024 列 |
| テーブル |列あたりのバイト数 |列の [データ型](sql-data-warehouse-tables-data-types.md)によって決まります。 上限は、char データ型では 8000 GB、nvarchar データ型では 4000 GB、MAX データ型では 2 GB です。 |
| テーブル |行あたりのバイト数 (定義されたサイズ) |8060 バイト<br/><br/>行あたりのバイト数は、ページ圧縮を有効にした SQL Server の場合と同様に計算されます。 SQL Data Warehouse では SQL Server と同様に行オーバーフロー ストレージがサポートされており、**可変長列**を行外にプッシュできます。 可変長行を行外にプッシュする場合、メイン レコードには 24 バイト ルートのみが格納されます。 詳細については、「[8 KB を超える場合の行オーバーフロー データ](https://msdn.microsoft.com/library/ms186981.aspx)」を参照してください。 |
| テーブル |テーブルあたりのパーティション数 |15,000<br/><br/>高パフォーマンスを実現するには、ビジネス要件を満たしながら、必要なパーティション数を最小限に抑えることをお勧めします。 パーティションの数が増えるに従い、データ定義言語 (DDL) およびデータ操作言語 (DML) の操作のオーバーヘッドが拡大し、パフォーマンスの低下を引き起こします。 |
| テーブル |パーティション境界値あたりの文字数 |4000 |
| Index |テーブルあたりの非クラスター化インデックス数 |50<br/><br/>行ストア テーブルのみに適用されます。 |
| Index |テーブルあたりのクラスター化インデックス数 |1<br><br/>行ストア テーブルと列ストア テーブルの両方に適用されます。 |
| Index |インデックス キーのサイズ |900 バイト<br/><br/>行ストア インデックスにのみに適用されます。<br/><br/>インデックスの作成時には varchar 列の既存のデータが 900 バイトを超えていない場合でも、最大サイズが 900 バイトを超える、varchar 列のインデックスが作成されることがあります。 ただし、その後の列に対する INSERT または UPDATE 操作は、合計サイズが 900 バイトを超えてしまう場合、失敗します。 |
| Index |インデックスあたりのキー列数 |16<br/><br/>行ストア インデックスにのみに適用されます。 クラスター化列ストア インデックスには、すべての列が含まれます。 |
| 統計 |組み合わせ列値のサイズ |900 バイト |
| 統計 |統計オブジェクトあたりの列数 |32 |
| 統計 |テーブルごとに、列に対して作成される統計 |30,000 |
| ストアド プロシージャ |入れ子レベルの最大数 |8 |
| 表示 |ビューあたりの列数 |1,024 |

## <a name="loads"></a>読み込み
| Category | 説明 | 最大値 |
|:--- |:--- |:--- |
| Polybase 読み込み |行あたりの MB 数 |1<br/><br/>Polybase は、1 MB 未満の行に対してのみ読み込みを行い、VARCHAR(MAX)、NVARCHAR(MAX)、VARBINARY(MAX) に読み込むことはできません。<br/><br/> |

## <a name="queries"></a>クエリ
| Category | 説明 | 最大値 |
|:--- |:--- |:--- |
| Query |ユーザー テーブルに対する、キューに置かれるクエリ数 |1,000 |
| Query |システム ビューに対する同時クエリ数 |100 |
| Query |システム ビューに対する、キューに置かれるクエリ数 |1,000 |
| Query |パラメーターの最大個数 |2098 |
| Batch |最大サイズ |65,536*4096 |
| SELECT の結果セット |行あたりの列数 |4096<br/><br/>SELECT の結果セットで、許容される行あたりの列数の上限は 4096 個です。 常に 4096 個が保証されるわけではありません。 クエリ プランで一時テーブルが必要な場合、テーブルあたりの列数の最大値として 1024 が適用される可能性があります。 |
| SELECT |入れ子になったサブクエリの数 |32<br/><br/>SELECT ステートメントで許容される入れ子になったサブクエリの数は 32 個までです。 常に 32 個が保証されるわけではありません。 たとえば、JOIN によって、クエリ プランにサブクエリが導入されることがあります。 サブクエリの数はまた、使用できるメモリによって制限される場合があります。 |
| SELECT |JOIN あたりの列数 |1,024 列<br/><br/>JOIN で許容される列数は 1,024 個までです。 常に 1024 列が保証されるわけではありません。 JOIN プランで、列数が JOIN の結果を上回る一時テーブルが必要な場合、一時テーブルには 1024 の制限が適用されます。 |
| SELECT |GROUP BY の列あたりのバイト数 |8060バイト<br/><br/>GROUP BY 句内の列に許容されるバイト数は、最大で 8,060 バイトです。 |
| SELECT |ORDER BY 列あたりのバイト数 |8060 バイト<br/><br/>ORDER BY 句内の列に許容されるバイト数は、最大で 8,060 バイトです。 |
| ステートメントあたりの識別子 |参照される識別子の数 |65,535<br/><br/>SQL Data Warehouse では、1 つのクエリ式に含めることができる識別子の数を制限しています。 この数を超えると、SQL Server エラー 8632 が発生します。 詳細については、[内部エラー: 式サービスの制限値に達しています](https://support.microsoft.com/en-us/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a)に関するページをご覧ください。 |
| 文字列リテラル | ステートメントの文字列リテラルの数 | 20,000 <br/><br/>SQL Data Warehouse では、1 つのクエリ式に含めることができる文字列定数の数を制限しています。 この数を超えると、SQL Server エラー 8632 が発生します。|

## <a name="metadata"></a>Metadata
| システム ビュー | 最大行数 |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |最新の 1000 個の SQL 要求に対する DMS ワーカーの総数 |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |sys.dm_pdw_exec_requests に格納された最新の 1000 個の SQL 要求に対するステップの総数 |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |sys.dm_pdw_exec_requests に格納された最新の 1000 個の SQL 要求 |

## <a name="next-steps"></a>次の手順
SQL Data Warehouse の使用に関する推奨事項については、[チート シート](cheat-sheet.md)に関するページを参照してください。
