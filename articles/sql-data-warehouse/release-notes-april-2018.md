---
title: Azure SQL Data Warehouse リリース ノート 2018 年 4 月 | Microsoft Docs
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
ms.openlocfilehash: a9fae37acd653b05502737756baf463188ef631d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861708"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Azure SQL Data Warehouse の新機能 2018 年 4 月
Azure SQL Data Warehouse では、継続的に機能強化を図っています。 この記事では、2018 年 4 月に導入された新しい機能と変更点について説明します。

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>切り替えの前にパーティションを切り詰める機能
お客様は、`ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` 構文を使用してテーブルのメタデータを変更することにより、あるテーブルから別のテーブルにデータを読み込む 1 つのパターンとしてパーティション切り替えを使用することがよくあります。 SQL Data Warehouse では、切り替え先パーティションにデータが含まれている場合、パーティション切り替えはサポートされません。 切り替え先パーティションにデータが既に含まれている場合、お客様は切り替え先パーティションを切り詰めてから、切り替えを実行する必要があります。

SQL Data Warehouse では、1 つの T-SQL ステートメントでこの操作をサポートするようになりました。

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
詳しくは、「[ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)」の記事をご覧ください。

## <a name="improved-query-compilation-performance"></a>クエリ コンパイルのパフォーマンスの向上
SQL Data Warehouse に、分散クエリのクエリ コンパイル手順を向上させるための一連の変更が導入されました。 これらの変更により、クエリ コンパイル時間が最大 **10 倍**向上し、クエリ実行の全体的な実行時間が短縮されます。 これらの変更は、オブジェクト (テーブル、関数、ビュー、プロシージャ) の数が多いデータ ウェアハウスで顕著です。

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>DBCC コマンドはコンカレンシー スロットを消費しない (動作変更)
SQL Data Warehouse では、[DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql) など、T-SQL [DBCC コマンド](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql)のサブセットがサポートされます。 以前は、これらのコマンドは[コンカレンシー スロット](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots)を消費したので、実行できるユーザー負荷/クエリの数が減少しました。 `DBCC` コマンドは、リソース スロットを消費しないローカル キューで実行されるようになったため、全体的なクエリ実行パフォーマンスが向上します。

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>過剰なリテラルに対するエラー メッセージの更新 (動作変更)
以前は、SQL Data Warehouse には、クエリに含まれるリテラルが多すぎる場合に "*おおよそ*" の数が含まれました。
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

リテラルの制限に達したことのみを示すようにエラー メッセージが更新されました。
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

最大制限について詳しくは、[容量制限](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits)に関する記事の「[クエリ](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries)」セクションをご覧ください。

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>SYS.PDW_DATABASE_MAPPINGS ビューの削除 (動作変更)
この `sys.pdw_database_mappings` ビューは、SQL Data Warehouse では使用されません。 以前は、このビューに対する SELECT では結果が返されませんでした。 このビューは削除されました。 

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
