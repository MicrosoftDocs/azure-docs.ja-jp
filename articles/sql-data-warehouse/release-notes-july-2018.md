---
title: Azure SQL Data Warehouse リリース ノート 2018 年 7 月 | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 123198b21122a23d81794db0a5ca2051b15ee2e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834202"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Azure SQL Data Warehouse の新機能 2018 年 7 月
Azure SQL Data Warehouse では、継続的に機能強化を図っています。 この記事では、2018 年 7 月に導入された新しい機能と変更点について説明します。

## <a name="lightning-fast-query-performance"></a>きわめて高速なクエリ パフォーマンス
[Azure SQL Data Warehouse](https://aka.ms/sqldw) は、シャッフル操作を改善するインスタント データ アクセスの導入により、パフォーマンスを新たな水準へと進化させます。 インスタント データ アクセスとは、SQL Server から SQL Server へのダイレクトなネイティブ データ操作を使用することで、データ移動操作のオーバーヘッドを軽減するものです。 SQL Server エンジンをデータ移動用に直接統合する SQL Data Warehouse では、業界標準として知られる [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/) からのワークロードを使用した場合、**Amazon Redshift よりも 67% 高速**な動作を実現します。

![Azure SQL Data Warehouse は Amazon Redshift よりも高速で安価です](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>出典:[Gigaom Research Analyst Report:Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

[Gigaom Research](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) のレポートでは、実行時のパフォーマンスだけでなく、特定のワークロードに対するコスト (米国ドル) を定量化する価格性能比も測定されています。 30 TB のワークロードでは、SQL Data Warehouse は Redshift よりも **23% 以上低コスト**となっています。 SQL Data Warehouse では、処理性能を柔軟にスケーリングできるだけでなく、ワークロードの一時停止や再開も可能なので、お客様は従量課金でサービスを利用しながら、コストを最大限に削減することができます。
![Azure SQL Data Warehouse は Amazon Redshift よりも高速で安価です](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>出典:[Gigaom Research Analyst Report:Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

### <a name="query-concurrency"></a>クエリのコンカレンシー
SQL Data Warehouse は、組織内でのデータの可用性を確保するうえでも役立ちます。 サービスが強化され、128 件のクエリを同時実行できるようになったことで、他の要求によってブロックされることなく、同じデータベースに対してより多くのユーザーがクエリを実行できるようになりました。 これに対し、Amazon Redshift ではクエリの同時実行数が最大 50 件であるため、組織内でのデータ アクセスが大きく制限されます。

SQL Data Warehouse では、ストレージとコンピューティングを分離した独自のアーキテクチャにより、価格を一切引き上げることなく、これらのクエリ パフォーマンスとクエリのコンカレンシー数を実現しています。

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>リージョンやサーバーの垣根を越えた復元で、さらに細かい粒度を実現
24 時間おきに作成される geo 冗長バックアップを選択する代わりに、任意の復元ポイントを使い、リージョンやサーバーの垣根を越えて復元できるようになりました。 リージョンやサーバーの垣根を越えた復元は、ユーザー定義の復元ポイントと自動の復元ポイントのどちらでもサポートされるので、粒度が細かくなりデータ保護の強化につながります。 利用できる復元ポイントが増えることで、リージョンをまたいで復元する際にデータ ウェアハウスの論理的整合性が保たれることへの安心感が得られます。

![復元コマンド - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![復元オプション - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

詳細については、[高速で柔軟な復元ポイント](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)に関するブログ記事を参照してください。

## <a name="20-minute-restorations"></a>20 分での復元
SQL Data Warehouse であらゆるデータ ウェアハウスの復元にかかる時間が短縮され、同じリージョン内であれば、データベース サイズに関係なく **20 分未満**で復元できるようになりました。 同じリージョン内であれば、復元先の論理サーバーが同じであるかどうかに関係なく、この復元時間が当てはまります。 また、復元ポイントの作成にかかる時間を短縮するために、スナップショットの処理も改善されています。 低パフォーマンス レベル (低 DWU 設定) では、スナップショットの作成時間が "*1/2 に短縮*" されます。

詳細については、[高速で柔軟な復元ポイント](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)に関するブログ記事を参照してください。

## <a name="custom-restoration-configurations"></a>カスタム復元構成
復元時のパフォーマンス レベル (DWU) を Azure portal で変更できるようになりました。 アップグレードされた Gen2 データ ウェアハウスに復元することもできます。 Gen 2 インスタンスを復元先とすることによって、[Gen1 データ ウェアハウスをアップグレード](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation)する前に Gen2 の効果を評価できるようになりました。

![カスタム復元構成 - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
[sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) ストアド プロシージャは、Transact-SQL バッチ内のパラメーターに関するメタデータをツールで取得する際によく使用されます。 このプロシージャは、バッチ内の各パラメーターにつき 1 行を、その推定される型情報と共に返します。 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

この結果セットには、`@id` パラメーターに関するメタデータが含まれています。以下に示したのは結果の抜粋です。
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
[sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) ストアド プロシージャは、基になるオブジェクトが変更され、基になるメタデータが最新でなくなった場合に、データベース オブジェクトのメタデータを更新します。 この状況は、ビューのベース テーブルが変更された後、ビューが再作成されていない場合に発生する可能性があります。 このストアド プロシージャを使用すると、依存オブジェクトを削除して再作成する手順を省略できます。

次の例では、基になるテーブルが変更されたことにより、ビューが最新でなくなっています。 最初の列変更 (1 から Mollie へ) についてはデータが正しいですが、列名が無効になり、2 列目が存在しなくなっています。 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

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
