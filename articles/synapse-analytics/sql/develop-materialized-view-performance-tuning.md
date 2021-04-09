---
title: 具体化されたビューを使用したパフォーマンス チューニング
description: クエリのパフォーマンスを向上させるための、具体化されたビューに関する推奨事項と考慮事項。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: e6c3987e2de7f9592a1f7f6086657592e1bf0c16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676588"
---
# <a name="performance-tuning-with-materialized-views-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics で専用 SQL プールを使用した具体化されたビューでのパフォーマンス チューニング

専用 SQL プールで具体化されたビューを使用すると、複雑な分析クエリのメンテナンスが少なくて済み、クエリを変更せずに高速なパフォーマンスを実現することができます。 この記事では、具体化されたビューを使用するうえでの一般的なガイダンスを説明します。

## <a name="materialized-views-vs-standard-views"></a>具体化されたビューと標準ビュー

SQL プールでは、標準ビューと具体化されたビューの両方がサポートされます。  どちらも SELECT 式を使って作成され、クエリからは論理テーブルとして認識される仮想テーブルです。  ビューによって、一般的なデータ計算に伴う複雑さが明らかになり、計算の変更に抽象化レイヤーが追加されます。これにより、クエリの書き換えが不要になります。  

標準ビューでは、ビューを使用するたびにそのデータが計算されます。  ディスクには一切データは格納されません。 通常、標準ビューは、データベース内の論理オブジェクトとクエリを体系化しやすくするツールとして使用されます。  標準ビューを使用するには、クエリでそれを直接参照する必要があります。

具体化されたビューによって、そのデータは事前に計算され、テーブルと同じように専用 SQL プールに格納されて管理されます。  具体化されたビューを使用するたびに再計算は必要ありません。  そのため、具体化されたビューにあるデータの一部または全部を使用するクエリでは、パフォーマンスを向上させることができます。  さらに、具体化されたビューは、クエリで直接参照することなく使用できるので、アプリケーション コードに変更を加える必要がありません。  

標準ビューの要件のほとんどは、具体化されたビューにも当てはまります。 具体化されたビューの構文とその他の要件の詳細については、[CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) に関するページを参照してください。

| 比較                     | 表示                                         | 具体化されたビュー
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|定義の表示                 | Azure データ ウェアハウスに格納されます。              | Azure データ ウェアハウスに格納されます。
|ビューの内容                    | ビューが使用されるたびに生成されます。   | ビューの作成中に前処理されて Azure データ ウェアハウスに格納されます。 基になるテーブルにデータが追加されると更新されます。
|データ更新                    | 常時更新                               | 常時更新
|複雑なクエリからビューのデータを取得する速度     | 遅い                                         | 速い  
|追加のストレージ                   | いいえ                                           | はい
|構文                          | CREATE VIEW                                  | CREATE MATERIALIZED VIEW AS SELECT

## <a name="benefits-of-materialized-views"></a>具体化されたビューの利点

具体化されたビューが適切に設計されていると、次のような利点が得られます。

- JOIN や集計関数を含んだ複雑なクエリの実行時間が短縮されます。 クエリが複雑であればあるほど、実行時間を短縮できる可能性が大きくなります。 利点が最大限に活かされるのは、クエリの計算コストが大きく、かつその結果得られるデータ セットが小さいケースです。  

- 専用 SQL プールのクエリ オプティマイザーを使用すると、デプロイされている具体化されたビューを自動的に使用して、クエリの実行プランを改善できます。  このプロセスは、ユーザーからは透過的に行われ、クエリの実行速度を向上させます。また、具体化されたビューを直接参照する必要もありません。

- ビューのメンテナンスが少なくて済みます。  具体化されたビューでは、データが 2 か所に格納されます (ビュー作成時の初期データはクラスター化列ストア インデックス、増分変更データはデルタ ストア)。  ベース テーブル以降のすべてのデータ変更は、同期的な方法で自動的にデルタ ストアに追加されます。  デルタ ストアにあるデータは、バックグラウンド プロセス (組ムーバー) によってビューの列ストア インデックスに定期的に移動されます。  この設計によって、具体化されたビューに対するクエリから、ベース テーブルに直接照会した場合と同じデータを得ることができます。
- ベース テーブルとは異なり、マテリアライズドビューのデータは分散させることができます。  
- 具体化されたビューのデータには、高可用性と回復性の点で、通常のテーブルのデータと同じ利点が確保されています。  

他のデータ ウェアハウス プロバイダーと比較して、専用 SQL プールに実装されている具体化されたビューには、さらに次の追加の利点があります。

- ベース テーブル内のデータ変更で自動的かつ同期的にデータが更新されます。 ユーザーによる操作は不要です。
- 幅広い集計関数がサポートされます。 [CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) に関するページを参照してください。
- クエリに固有のマテリアライズドビューに関する推奨情報が得られます。  「[EXPLAIN (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest&preserve-view=true)」を参照してください。

## <a name="common-scenarios"></a>一般的なシナリオ  

一般に、マテリアライズドビューは次のシナリオで使用されます。

**サイズの大きなデータに対する複雑な分析クエリのパフォーマンスを高める必要がある**

一般に、複雑な分析クエリでは、使用される集計関数やテーブルの結合が増えるため、クエリの実行中、計算負荷の大きい操作 (シャッフル、結合など) が多くなります。  そのため、そうしたクエリが完了するまでの時間は、特に大きなテーブルで長くなります。  

クエリでよく使われる計算から返されたデータについて、ユーザーは、具体化されたビューを作成することができます。そうすれば、そのデータがクエリで必要になったときの再計算が不要になるため、計算コストを抑制し、クエリの応答時間を短縮できます。

**クエリへの変更をゼロまたは最小限に抑えつつパフォーマンスを向上させる必要がある**

日常的な ETL 操作とレポート作成をサポートするために、データ ウェアハウスにおけるスキーマとクエリの変更は最小限に留められるのが一般的です。  具体化されたビューによって生じるコストがクエリのパフォーマンス向上で相殺されるのであれば、具体化されたビューをクエリのパフォーマンス チューニングに使用することができます。

具体化されたビューを作成して維持することは、スケーリングや統計管理といった他のチューニング方法と比べて、運用環境に影響を与える変更がはるかに少なくて済み、また、パフォーマンス向上の可能性も大きくなります。

- 具体化されたビューを作成したり維持したりすることによって、ベース テーブルに対して実行されるクエリに影響が及ぶことはありません。
- デプロイされているマテリアライズドビューは、クエリの中で直接参照しなくてもクエリ オプティマイザーによって自動的に使用されます。 この機能があるため、パフォーマンス チューニングでクエリに変更を加える必要がありません。

**クエリ パフォーマンスを向上させるために別のデータ分散方法が必要である**

Azure データ ウェアハウスは、分散型の超並列処理 (MPP) システムです。  

Synapse SQL は、データ エンジニアにとって使い慣れた標準の T-SQL エクスペリエンスを使用して、エンタープライズがデータ ウェアハウジングおよびデータ仮想化シナリオを実装できるようにする分散クエリ システムです。 また、SQL の機能を拡張して、ストリーミングや機械学習のシナリオに対応します。 データ ウェアハウス テーブル内のデータは、3 つの[分散方式](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (ハッシュ、ラウンド ロビン、レプリケート) のいずれかを使用して 60 のノードに分散されます。  

データ分散はテーブルの作成時に指定され、テーブルが削除されるまで変更されることなく維持されます。 具体化されたビューはディスク上の仮想テーブルであるため、ハッシュとラウンド ロビンによるデータ分散がサポートされます。  ユーザーは、ベース テーブルとは異なっているものの、頻繁にビューを使用するクエリのパフォーマンスに最適なデータ分散を選択できます。  

## <a name="design-guidance"></a>設計ガイダンス

次に示すのは、マテリアライズドビューを使用したクエリ パフォーマンスの改善についての一般的なガイダンスです。

**実際のワークロードに合わせて設計する**

マテリアライズドビューの作成を始める前に、クエリ パターン、重要性、頻度、得られるデータのサイズの観点から、実際のワークロードに対する理解を深めておくことが大切です。  

ユーザーは EXPLAIN WITH_RECOMMENDATIONS <SQL_statement> を実行することで、クエリ オプティマイザーによって推奨されるマテリアライズドビューを得ることができます。  それらの推奨情報はクエリに固有であるため、ある 1 つのクエリで役に立つマテリアライズドビューが、同じワークロードの他のクエリにとっては最適ではない可能性もあります。  

実際のワークロードのニーズを念頭に置きながら、それらの推奨情報を評価してください。  最適なマテリアライズドビューは、ワークロードのパフォーマンスを向上させるものです。  

**クエリの高速化とそのコストのトレードオフに注意する**

具体化されたビューにはそれぞれ、データ ストレージにかかるコストとビューのメンテナンス コストとがかかります。  ベース テーブルのデータが変更されると、具体化されたビューのサイズが増え、物理構造体も変化します。  

クエリのパフォーマンス低下を回避するため、具体化されたビューは、それぞれデータ ウェアハウス エンジンによって個別に維持されます。これには、デルタ ストアから列ストアのインデックス セグメントへの行の移動や、データ変更の統合が含まれます。  

具体化されたビューとベース テーブルへの変更数が増えると、メンテナンス ワークロードが増えます。   すべての具体化されたビューから発生するコストをクエリのパフォーマンス向上で相殺できるかどうかをユーザーがチェックする必要があります。  

データベース内の具体化されたビューのリストを得るには、このクエリを実行します。

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

具体化されたビューの数を減らす方法を次に示します。

- 実際のワークロードの複雑なクエリによってよく使用される一般的なデータ セットを特定します。  オプティマイザーが実行プランを作成するときにそれらのデータ セットをビルディング ブロックとして利用できるように、それらを格納するためのマテリアライズドビューを作成します。  

- 使われなくなったり不要になったりしたマテリアライズドビューは削除します。  マテリアライズドビューを無効にしても、メンテナンスは行われませんが、ストレージ コストは引き続き発生します。  

- 同じ (または同様の) ベース テーブルを基に作成されたマテリアライズドビューは、そのデータが重複していないとしても、まとめるようにします。  マテリアライズドビューをまとめると、ビューのサイズは個別のビューの合計より大きくなる可能性がありますが、ビューのメンテナンス コストは削減されます。  次に例を示します。

```sql
-- Query 1 would benefit from having a materialized view created with this SELECT statement
SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement
SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form
SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**クエリの変更を必要としないパフォーマンス チューニングもある**

データ ウェアハウス オプティマイザーでは、クエリ パフォーマンスを向上させるために、デプロイされている具体化されたビューを自動的に使用します。  この機能は、ビューを参照しないクエリや、具体化されたビューの作成ではサポートされない集計を使用したクエリに対して透過的に適用されます。  クエリの変更は必要ありません。 マテリアライズドビューが使用されているかどうかは、推定されるクエリの実行プランをチェックすることで確認できます。  

**具体化されたビューを監視する**

具体化されたビューは、クラスター化列ストア インデックス (CCI) を含んだテーブルとまったく同じようにデータ ウェアハウスに格納されます。  具体化されたビューからのデータの読み取りには、インデックスのスキャンとデルタ ストアからの変更の適用が含まれます。  デルタ ストア内の行数が多すぎると、具体化されたビューからのクエリの解決にかかる時間が、ベース テーブルに直接照会するよりも長くなってしまう場合があります。  

クエリ パフォーマンスの低下を防ぐために、[DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest&preserve-view=true) を実行して、ビューのオーバーヘッド比率 (合計行数/ベース ビューの行数) を監視することをお勧めします。  オーバーヘッド比率が高すぎる場合は、デルタ ストア内のすべての行が列ストア インデックスに移動されるよう、具体化されたビューを再構築することを検討してください。  

**具体化されたビューと結果セットのキャッシュ**

これらの 2 つの機能は、クエリ パフォーマンスのチューニングと同時期に専用 SQL プールに導入されました。 結果セットのキャッシュは、静的データに対する反復的なクエリから、高いコンカレンシーと高速な応答を得る目的で使用されます。  

キャッシュされた結果を使用するには、キャッシュを要求するクエリの形式が、そのキャッシュを生成したクエリと一致している必要があります。  加えて、キャッシュされた結果は、クエリ全体に当てはまるものでなければなりません。  

具体化されたビューでは、ベース テーブルへのデータの変更が許容されます。  マテリアライズドビューのデータは、クエリの一部に適用することができます。  そのため、一部の計算を共有する複数のクエリで、同じマテリアライズドビューを使用することにより、パフォーマンスを高めることができます。

## <a name="example"></a>例

この例では、店舗よりもカタログで多くのお金を費やす顧客を検索する、TPCDS に似たクエリを使用しています。 さらに、得意客とその出身国および地域も特定します。   このクエリには、SUM() と GROUP BY を含んだ 3 つのサブ SELECT ステートメントの UNION から上位 100 レコードを選択する操作が含まれています。

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

クエリの推定実行プランを確認します。  18 のシャッフル操作と 17 の結合操作があり、実行時間が長くなっています。 3 つのサブ SELECT ステートメントのそれぞれについて、マテリアライズドビューを 1 つ作成しましょう。

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

元のクエリの実行プランをもう一度確認してみます。  今度は、結合の数が 17 から 5 に変化しており、また、シャッフルはなくなっています。  プランのフィルター操作アイコンを選択します。 その出力リストに、ベース テーブルではなく具体化されたビューから読み取られたデータが表示されます。  

 ![Plan_Output_List_with_Materialized_Views](./media/develop-materialized-view-performance-tuning/output-list.png)

マテリアライズドビューがあれば、コードには一切変更を加えることなく、同じクエリの実行速度が大幅に改善されます。  

## <a name="next-steps"></a>次のステップ

開発に関するその他のヒントについては、[Synapse SQL の開発の概要](develop-overview.md)に関する記事をご覧ください。
 