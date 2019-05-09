---
title: Azure SQL Data Warehouse リリース ノート 2018 年 5 月 | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: c17cb13bff0ea9eb3b0bb2caf5bb527fa3958428
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852612"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Azure SQL Data Warehouse の新機能 2018 年 5 月 
Azure SQL Data Warehouse では、継続的に機能強化を図っています。 この記事では、2018 年 5 月に導入された新しい機能と変更点について説明します。 

## <a name="gen-2-instances"></a>Gen 2 インスタンス
![alt](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Azure SQL Data Warehouse コンピューティング最適化 Gen2 層は、クラウド データ ウェアハウスの新しいパフォーマンス基準を設定します。 現在の世代と比較して、クエリ パフォーマンスが最大 5 倍、コンカレンシーが最大 4 倍、コンピューティング パワーが最大 5 倍に向上しました。 これにより、クラウド データ ウェアハウス サービスの中で最多である 128 の同時クエリを 1 つのクラスターから処理できるようになりました。

Azure Data のコーポレート バイス プレジデント、Rohan Kumar からのブログでの発表「[Azure SQL Data Warehouse を使用してクラウド分析の質を高める](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/)」を参照してください。

## <a name="auto-statistics"></a>自動統計
統計情報は、SQL Data Warehouse のエンジンなどの先進的なコストベースのオプティマイザーでクエリ プランの生成を最適化するために重要です。 すべてのクエリが事前にわかっている場合、どの統計オブジェクトを作成する必要があるかを判断することができます。 ただし、システムがデータ ウェアハウスのワークロードに特有のアドホック クエリやランダム クエリに直面した場合、システム管理者は作成する必要のある統計情報を予測することが難しいため、クエリ実行計画が最適化されず、クエリ応答時間が長くなる可能性があります。 この問題を軽減する 1 つの方法として、事前にすべてのテーブル列に統計オブジェクトを作成する方法があります。 ただし、このプロセスにはテーブルの読み込み処理中に統計オブジェクトを維持する必要があるため、読み込み時間が長くなるというデメリットがあります。

SQL Data Warehouse では、統計オブジェクトの自動作成がサポートされ、システム管理者や開発者に対して向上した柔軟性、生産性、使いやすさを提供すると同時に、システムが質の高い実行計画とベストな応答時間を継続的に提供できるようになりました。

SQL Data Warehouse で自動統計の作成を有効または無効にするには、次のステートメントを実行します。
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

ベスト プラクティスとベスト ガイダンスとして、`AUTO_CREATE_STATISTICS` オプションを `ON` に設定することをお勧めします。

> [!NOTE]
> 自動統計の作成は、すべての新しいデータ ウェアハウスで*既定で有効*になっています。
>  

詳細については、「[ALTER DATABASE の SET オプション](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options)」の記事を参照してください。

## <a name="rejected-row-support"></a>拒否された行のサポート
データ読み込みのパフォーマンスと並列性が高いため、SQL Data Warehouse への[データ読み込みに PolyBase (外部テーブル)](design-elt-data-loading.md) を使用することがよくあります。 PolyBase は、[Azure Data Factory](https://azure.com/adf) を介してデータを読み込む際の既定の読み込みモデルでもあります。 

SQL Data Warehouse では、[CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) ステートメントを使用した `REJECTED_ROW_LOCATION` パラメーターを介して、拒否された行の場所を定義することができます。 外部テーブルから [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) を実行した後、読み込むことができなかった行はすべて、さらなる調査のためにソースに近いファイルに保存されます。 

拒否された行の動作の詳細については、ブログ「[SQL Data Warehouse での PolyBase を使用した拒否された行の場所の確実な読み込み](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/)」を参照してください。

次の例は、拒否された行を指定する新しい構文を示しています。

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>ALTER VIEW
[ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) を使用すると、ビューの削除/作成や権限の再適用をすることなく、以前に作成したビューを変更することができます。 

次の例では、以前に作成したビューを変更します。
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
[The CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) 関数は、エンド ツー エンドで 2 つ以上の値を連結した文字列を返します。 最初の引数で指定された区切り記号で、連結された値を区切ります。 `CONCAT_WS` 関数は、コンマ区切り値 (CSV) 出力を生成するのに便利です。

次の例は、int 値のセットをコンマで連結する例を示しています。
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
ステートメントは次の結果を返します。
```
result
---------
1,2,3
```
次の例は、さまざまなデータ型の値のセットをコンマで連結する例を示しています。
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
ステートメントは次の結果を返します。
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
[sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) システム ストアド プロシージャは、現在の環境でサポートされているデータ型に関する情報を返します。 これはデータ型の調査のために ODBC 接続を介して接続するツールでよく使用されます。

次の例では、SQL Data Warehouse でサポートされているすべてのデータ型の詳細を取得します。

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>ORDER BY を使用した SELECT INTO の動作変更
SQL Data Warehouse では、`ORDER BY` 句を含む `SELECT INTO` クエリがブロックされるようになります。 以前、この操作は、最初にメモリ内のデータを順序付けてから、テーブルの形状と一致するようにデータを再度順序付けてターゲット テーブルに挿入することで成功していました。

### <a name="previous-behavior"></a>以前の動作
次のステートメントは、処理オーバーヘッドを追加すると成功します。
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>現在の動作
次のステートメントは、`ORDER BY` 句が `SELECT INTO` ステートメントでサポートされていないことを示すエラーをスローします。
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
返されたエラー ステートメント:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>SET PARSEONLY ON のクエリ状態 (動作変更)
`SET PARSEONLY ON` 構文を使用すると、SQL Data Warehouse エンジンで各 T-SQL ステートメントの構文を調べ、ステートメントをコンパイルまたは実行せずにエラー メッセージを返すことができます。 以前は、[sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) システム ビューで、これらのステートメントのステータスは `Running` 状態のままでした。 `sys.dm_pdw_exec_requests` ビューは、ステータスを `Complete` として返します。

## <a name="next-steps"></a>次の手順
SQL Data Warehouse の概要について学習したので、次は[SQL Data Warehouse を簡単に作成する][create a SQL Data Warehouse]方法について学習してください。 Azure に慣れていない場合に新しい用語を調べるには、[Azure 用語集][Azure glossary]が役立ちます。 または、次の SQL Data Warehouse リソースも確認できます。  

* [顧客の成功事例]
* [ブログ]
* [機能に関する要求]
* [ビデオ]
* [Customer Advisory Team のブログ]
* [Stack Overflow フォーラム]
* [Twitter]


[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team のブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[顧客の成功事例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow フォーラム]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
