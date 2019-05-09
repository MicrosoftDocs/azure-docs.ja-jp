---
title: Azure SQL Data Warehouse リリース ノート 2018 年 9 月 | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: bc559a1224aace2ee599c24c8dce07a6d55173fd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863714"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Azure SQL Data Warehouse の新機能 2018 年 9 月
Azure SQL Data Warehouse では、継続的に機能強化を図っています。 この記事では、2018 年 9 月に導入された新しい機能と変更点について説明します。

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>SQL Data Warehouse Gen2 用の新しい下位エントリ ポイント
2018 年 4 月、5 倍のパフォーマンス、5 倍のコンピューティング スケール、4 倍のコンカレンシー、無制限のストレージを提供する Azure SQL Data Warehouse Gen2 が、[Microsoft から発表されました](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/)。 Gigaom による「[Data Warehouse in the cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)」(クラウドでのデータ ウェアハウスのベンチマーク) で示されているように、SQL Data Warehouse Gen2 は **Amazon Redshift より 42% 優れています**。

Gen2 は DWU500c の下位エントリ ポイントで一般に利用できるようになり、最新のサービス機能強化をすべて利用して、小規模データ ウェアハウスまたは開発/テスト環境を実行できます。 新しいエントリ ポイントは、[アダプティブ キャッシュ](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)、[高速データ シャッフル](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)、[リアルタイム データ ウェアハウス](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/)のサポートなど、Gen2 のすべての機能を備えています。

## <a name="sql-vulnerability-assessment"></a>SQL の脆弱性評価
[SQL の脆弱性評価 (VA)](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/25/sql-vulnerability-assessment-now-supports-azure-sql-data-warehouse-and-azure-sql-database-managed-instance/) は、データ ウェアハウスを継続的に監視する使いやすいサービスです。 これを利用して、高レベルのセキュリティを常に確保し、組織のポリシーが満たされている状態を維持できます。 また、見つかった問題ごとに、包括的なセキュリティ レポートと実用的な修復の手順が提供されます。 このレポートを利用すると、セキュリティの専門家ではない場合でも、データベースのセキュリティ状況を予防的に管理し、最も影響の大きいアクションに特に注意をすることが簡単にできます。 変更が頻繁に発生し、追跡が困難な動的環境では、攻撃に対してデータ ウェアハウスが脆弱になる可能性がある設定を検出する上で、VA は重要です。

## <a name="improved-availability-with-query-restartability"></a>クエリの再開可能性による可用性の向上
クエリの実行中に、問題が発生してクエリが失敗する可能性があります。 ネットワーク停止、ハードウェア障害、その他の切断で中断が発生する可能性があります。 SQL Data Warehouse では、ステップ レベルまたはステートメント レベルの SELECT クエリに対するクエリの再開可能性がサポートされるようになりました。 

クエリ再開可能性により、実行中にエラーのため中断したクエリが自動的に再開します。 ステップの数、クエリの形態、クエリが実行中に停止した場所などにより、SQL Data Warehouse エンジンは、クエリを最初から開始するか、または完了した最後のクエリ ステップから再開します。 エンド ユーザーからは、クエリは何もなく完了したように見えます。 

## <a name="maintenance-scheduling-preview"></a>メンテナンス スケジュール (プレビュー)
Azure SQL Data Warehouse のメンテナンスのスケジュール プレビューが開始されました。 この新しい機能は、サービス ヘルス計画メンテナンス通知、リソース ヘルス チェック モニター、および Azure SQL Data Warehouse メンテナンス スケジューリング サービスをシームレスに統合します。 メンテナンス スケジューリングでは、新しい機能、アップグレード、およびパッチを受信するのが便利な時間枠をスケジュールできます。

メンテナンス スケジュールによって、Azure Monitor を活用して、お客様は差し迫ったメンテナンス イベント発生したことの通知方法を決定でき、運用への影響を最小限に抑えるためにどの自動化されたフローを起動してダウンタイムを管理するかを決定できます。 通知には、メールまたはテキストを含めることができます。 

## <a name="wide-row-support-in-polybase"></a>Polybase での広い行のサポート
SQL Data Warehouse にデータを読み込むときの一般的なガイダンスでは、並列データ読み込みをサポートするには [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase) を使用します。 このリリースでは、サポートできる列の幅が 32 K から 1 MB に広がり、広い行サイズのテーブルを読み込むことができます。 広い行のサポートにより、広い行を含むテーブルのデータ読み込みプロセスが簡単になります。

> [!Note]
> 合計行サイズは 1 MB を超えることはできません。

## <a name="additional-language-support"></a>言語サポートの追加
このリリースでは、次の T-SQL 言語要素のサポートが導入されています。

### <a name="stringsplit"></a>STRING_SPLIT
[STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) 関数は、指定された区切り記号を使用して文字列を分割します。 STRING_SPLIT は、1 つの列に含まれる複数の値を解析して別のテーブルに挿入するデータ読み込みシナリオに便利です。

#### <a name="example"></a>例
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>COMPRESS/DECOMPRESS 関数
[COMPRESS](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) 関数では、GZIP アルゴリズムを使用して入力文字列を圧縮または圧縮解除することができます。

#### <a name="example"></a>例

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>ビューを削除する場合の IF EXISTS 句
[DROP VIEW](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) ステートメントに IF EXISTS 句が追加され、データ ウェアハウスからビューを削除するために必要な T-SQL コードが簡単になります。 DROP VIEW ステートメントに IF EXISTS 構文を適用すると、ビューが存在する場合は削除され、存在しない場合はステートメントが無視されます。

#### <a name="example"></a>例
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>単一挿入と DDL ステートメントのコンパイル時間の向上 
データ挿入に対する従来の抽出、変換、読み込み (ETL) モデルに従うと、データベース内のテーブルへの単一挿入 ([INSERT-VALUES](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) が実行されることがよくあります。 このリリースでは、この種のステートメントの実行に必要なコンパイル時間を減らすことで、単一挿入操作のパフォーマンスが向上しています。 場合によっては、コンパイルが最大で 3 倍速く感じます。 この向上により、単一挿入ステートメントの実行に必要な時間が短縮されます。 

この改善により、CREATE、ALTER、DELETE 操作を含むデータ定義言語 (DDL) ステートメントのクエリ コンパイル時間も同様に短縮されることで、多数のオブジェクトを含むデータ ウェアハウスにとってもメリットがあります。 

最後に、幅の広いテーブル (多数の列が含まれるテーブル) に対するステートメントの全体的な実行が減少します。 クエリ コンパイル ステップの時間短縮により、クエリの実行時間全体が短縮されます。

## <a name="bug-fixes"></a>バグの修正

| タイトル | 説明 |
|:---|:---|
| **一意制約の分布についての統計作成に関する修正** | この修正により、一意制約が定義されているテーブルだけを指定して UPDATE STATISTICS を実行したときに発生するエラーが対処されます。 |
| **外部テーブルに対するクエリのコンパイルに関する修正** | この修正により、外部テーブルが関係するクエリのコンパイル時間に影響を与えていた不具合が対処されます。|
| **大きい型を含むステートメントの実行に関する修正** | "*大きい*" 型 (nvarchar(max)、varchar(max)、varbinary(max)) のいずれかとして宣言されたパラメーターを含む準備されたステートメントのコンパイルでの不具合に対処します。 |
| **深く入れ子になったクエリでのエラー発生に関する修正** | 深く入れ子になったクエリがシステムの制限を超えたときに、明確なエラー メッセージを提供します。|
| **ステートメントに相関サブクエリと実行時定数が含まれる場合のコンパイル時エラーに関する修正** |相関サブクエリと実行時定数 (GETDATE() など) の特定の組み合わせを含むクエリのコンパイル時エラーに対処します。|
| **PDW オブジェクトのロックおよび autostats の同時実行スロットの取得に関するタイムアウトの対処** |この修正では、autostats 要求によって元の要求が長時間ブロックされるのを防ぐためにロック タイムアウトが追加されます。|

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
