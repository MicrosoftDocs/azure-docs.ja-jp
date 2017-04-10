---
title: "SQL Data Warehouse の容量制限 | Microsoft Docs"
description: "SQL Data Warehouse の接続、データベース、テーブル、およびクエリの最大値。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e1eac122-baee-4200-a2ed-f38bfa0f67ce
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 10/31/2016
ms.author: barbkess;jrj
translationtype: Human Translation
ms.sourcegitcommit: 3a9ea64c464a74c70e75634a3e5c1e49862a74e7
ms.openlocfilehash: c6b44392c0b3a241d41ae55bd6bb3f544d867e9e
ms.lasthandoff: 02/22/2017


---
# <a name="sql-data-warehouse-capacity-limits"></a>SQL Data Warehouse の容量制限
以下の表に、Azure SQL Data Warehouse のさまざまなコンポーネントで使用できる最大値を示します。

## <a name="workload-management"></a>ワークロード管理
| カテゴリ | 説明 | 最大値 |
|:--- |:--- |:--- |
| [Data Warehouse ユニット (DWU)][Data Warehouse Units (DWU)] |1 つの SQL Data Warehouse に対する 最大 DWU |6000 |
| [Data Warehouse ユニット (DWU)][Data Warehouse Units (DWU)] |1 つの SQL サーバーに対する最大 DWU |6000 (既定)<br/><br/> 既定では、各 SQL Server (myserver.database.windows.net など) の DTU クォータは 45,000 に設定されており、最大 6000 DWU が許可されます。 このクォータは単に安全上の制限です。 [サポート チケットを作成][creating a support ticket]し、要求の種類として *[クォータ]* を選択すれば、クォータを引き上げることができます。  実際に必要な DTU を計算するには、必要とされる DWU の合計に 7.5 を掛けます。 現在の DTU 消費量は、ポータルの [SQL Server] ブレードから確認できます。 DTU クォータには、一時停止しているデータベースと一時停止していないデータベースの両方が考慮されます。 |
| データベース接続 |同時に開かれるセッション数 |1024<br/><br/>最大 1024 個のアクティブな接続をサポートします。各接続が SQL Data Warehouse データベースに対して同時に要求を送信できます。 実際に同時実行できるクエリ数には制限があるので注意してください。 同時実行の制限を超えると、要求は内部キューに送られ、処理の順番が来るまで待機します。 |
| データベース接続 |準備されたステートメントに対する最大メモリ容量 |20 MB |
| [ワークロード管理][Workload management] |同時クエリの最大数 |32<br/><br/> 既定では、SQL Data Warehouse は、最大 32 個の同時実行クエリと、キューに残っているクエリを実行します。<br/><br/>ユーザーが上位リソース クラスに割り当てられている場合、または SQL Data Warehouse の DWU の数を少なく構成している場合は、同時実行レベルが低くなる可能性があります。 DMV クエリなど、クエリの中には必ず実行が許可されるものがあります。 |
| [Tempdb][Tempdb] |Tempdb の最大サイズ |DW100 あたり&399; GB です。 そのため、DWU1000 では、Tempdb のサイズは 3.99 TB になります。 |

## <a name="database-objects"></a>データベース オブジェクト
| カテゴリ | 説明 | 最大値 |
|:--- |:--- |:--- |
| データベース |最大サイズ |240 TB (ディスク上の圧縮)<br/><br/>この領域は tempdb またはログ領域から独立しています。そのため、この領域はパーマネント テーブル専用です。  クラスター化列ストアの圧縮率は約 5 倍になります。  このため、すべてのテーブルをクラスター化列ストア (既定のテーブルの種類) にした場合、データベースを約 1 PB まで拡大できるようになります。 |
| テーブル |最大サイズ |60 TB (ディスク上の圧縮) |
| テーブル |データベースあたりのテーブル数 |20 億 |
| テーブル |テーブルあたりの列数 |1,024 列 |
| テーブル |列あたりのバイト数 |列の[データ型][data type]によって決まります。  上限は、char データ型では 8000 GB、nvarchar データ型では 4000 GB、MAX データ型では 2 GB です。 |
| テーブル |行あたりのバイト数 (定義されたサイズ) |8060 バイト<br/><br/>行あたりのバイト数は、ページ圧縮を有効にした SQL Server の場合と同様に計算されます。 SQL Data Warehouse では SQL Server と同様に行オーバーフロー ストレージがサポートされており、 **可変長列** を行外にプッシュできます。 可変長行を行外にプッシュする場合、メイン レコードには 24 バイト ルートのみが格納されます。 詳細については、MSDNの「[8 KB を超える場合の行オーバーフロー データ][Row-Overflow Data Exceeding 8 KB]」を参照してください。 |
| テーブル |テーブルあたりのパーティション数 |15,000<br/><br/>高パフォーマンスを実現するには、ビジネス要件を満たしながら、必要なパーティション数を最小限に抑えることをお勧めします。 パーティションの数が増えるに従い、データ定義言語 (DDL) およびデータ操作言語 (DML) の操作のオーバーヘッドが拡大し、パフォーマンスの低下を引き起こします。 |
| テーブル |パーティション境界値あたりの文字数 |4000 |
| Index |テーブルあたりの非クラスター化インデックス数 |999<br/><br/>行ストア テーブルのみに適用されます。 |
| Index |テーブルあたりのクラスター化インデックス数 |1<br><br/>行ストア テーブルと列ストア テーブルの両方に適用されます。 |
| Index |インデックス キーのサイズ |900 バイト<br/><br/>行ストア インデックスにのみに適用されます。<br/><br/>インデックスの作成時には varchar 列の既存のデータが 900 バイトを超えていない場合でも、最大サイズが 900 バイトを超える、varchar 列のインデックスが作成されることがあります。 ただし、その後の列に対する INSERT または UPDATE 操作は、合計サイズが 900 バイトを超えてしまう場合、失敗します。 |
| Index |インデックスあたりのキー列数 |16<br/><br/>行ストア インデックスにのみに適用されます。 クラスター化列ストア インデックスには、すべての列が含まれます。 |
| 統計 |組み合わせ列値のサイズ |900 バイト |
| 統計 |統計オブジェクトあたりの列数 |32 |
| 統計 |テーブルごとに、列に対して作成される統計 |30,000 |
| ストアド プロシージャ |入れ子レベルの最大数 |8 |
| 表示 |ビューあたりの列数 |1,024 |

## <a name="loads"></a>読み込み
| カテゴリ | 説明 | 最大値 |
|:--- |:--- |:--- |
| Polybase 読み込み |行あたりの MB 数 |1<br/><br/>Polybase 読み込みは両方が 1 MB 未満の行の読み込みに制限され、VARCHR(MAX)、NVARCHAR(MAX)、VARBINARY(MAX) に読み込むことはできません。<br/><br/> |

## <a name="queries"></a>クエリ
| カテゴリ | 説明 | 最大値 |
|:--- |:--- |:--- |
| クエリ |ユーザー テーブルに対する、キューに置かれるクエリ数 |1,000 |
| クエリ |システム ビューに対する同時クエリ数 |100 |
| クエリ |システム ビューに対する、キューに置かれるクエリ数 |1,000 |
| クエリ |パラメーターの最大個数 |2098 |
| Batch |最大サイズ |65,536*4096 |
| SELECT の結果セット |行あたりの列数 |4096<br/><br/>SELECT の結果セットで、許容される行あたりの列数の上限は 4096 個です。 常に 4096 個が保証されるわけではありません。 クエリ プランで一時テーブルが必要な場合、テーブルあたりの列数の最大値として 1024 が適用される可能性があります。 |
| SELECT |入れ子になったサブクエリの数 |32<br/><br/>SELECT ステートメントで許容される入れ子になったサブクエリの数は 32 個までです。 常に 32 個が保証されるわけではありません。 たとえば、JOIN によって、クエリ プランにサブクエリが導入されることがあります。 サブクエリの数はまた、使用できるメモリによって制限される場合があります。 |
| SELECT |JOIN あたりの列数 |1,024 列<br/><br/>JOIN で許容される列数は 1,024 個までです。 常に 1024 列が保証されるわけではありません。 JOIN プランで、列数が JOIN の結果を上回る一時テーブルが必要な場合、一時テーブルには 1024 の制限が適用されます。 |
| SELECT |GROUP BY の列あたりのバイト数 |8060バイト<br/><br/>GROUP BY 句内の列に許容されるバイト数は、最大で 8,060 バイトです。 |
| SELECT |ORDER BY 列あたりのバイト数 |8060バイト<br/><br/>ORDER BY 句内の列に許容されるバイト数は、最大で 8,060 バイトです。 |
| ステートメントあたりの識別子と定数 |参照される識別子と定数の数 |65,535<br/><br/>SQL Data Warehouse では、1 つのクエリ式に含めることができる識別子と定数の数を制限しています。 上限は 65,535 個です。 この数を超えると、SQL Server エラー 8632 が発生します。 詳細については、[内部エラー: 式サービスの制限値に達しています][Internal error: An expression services limit has been reached]に関するページをご覧ください。 |

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

## <a name="next-steps"></a>次のステップ
詳細な参照情報については、[SQL Data Warehouse のリファレンス概要][SQL Data Warehouse reference overview]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md
[SQL Data Warehouse reference overview]: ./sql-data-warehouse-overview-reference.md
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[data type]: ./sql-data-warehouse-tables-data-types.md
[creating a support ticket]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Row-Overflow Data Exceeding 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Internal error: An expression services limit has been reached]: https://support.microsoft.com/kb/913050

