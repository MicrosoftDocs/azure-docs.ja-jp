---
title: SQL オンデマンド (プレビュー) のベスト プラクティス
description: Synapse SQL オンデマンド (プレビュー) を操作する場合に知っておくべき推奨事項とベスト プラクティス。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 7bebfeba6da1493557d51777ba8438747e160750
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85476276"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics の SQL オンデマンド (プレビュー) のベスト プラクティス

この記事では、SQL オンデマンド (プレビュー) の使用に関する一連のベスト プラクティスについて説明します。 SQL オンデマンドは Azure Synapse Analytics のリソースです。

## <a name="general-considerations"></a>一般的な考慮事項

SQL オンデマンドを使用すると、Azure ストレージ アカウント内のファイルに対してクエリを実行できます。 ローカル ストレージやインジェストの機能は備えていません。 したがって、クエリが対象とするすべてのファイルは SQL オンデマンドの外部にあります。 ストレージからのファイルの読み取りに関連するものはすべて、クエリのパフォーマンスに影響を与える可能性があります。

## <a name="colocate-your-azure-storage-account-and-sql-on-demand"></a>Azure ストレージ アカウントと SQL オンデマンドの併置

待機時間を最小限に抑えるには、Azure ストレージ アカウントと SQL オンデマンド エンドポイントを併置します。 ワークスペースの作成中にプロビジョニングされたストレージ アカウントとエンドポイントは同じリージョンに配置されます。

最適なパフォーマンスを得るために、SQL オンデマンドを使用して他のストレージ アカウントにアクセスする場合は、それらが同じリージョンにあることを確認してください。 同じリージョンにない場合、リモート リージョンとエンドポイントのリージョン間でデータをネットワーク転送するときの待機時間が長くなります。

## <a name="azure-storage-throttling"></a>Azure Storage の帯域幅調整

複数のアプリケーションとサービスがストレージ アカウントにアクセスする場合があります。 ストレージの帯域幅調整は、アプリケーション、サービス、および SQL オンデマンドのワークロードによって生成される IOPS またはスループットの合計がストレージ アカウントの制限を超えると発生します。 その結果、クエリのパフォーマンスに多大な悪影響が生じます。

帯域幅調整が検出された場合、SQL オンデマンドには、解決するための処理が組み込まれています。 SQL オンデマンドは、帯域幅調整が解決されるまで、より遅いペースでストレージへの要求を行います。

> [!TIP]
> 最適なクエリ実行のために、クエリの実行中は他のワークロードでストレージ アカウントに負荷をかけないでください。

## <a name="prepare-files-for-querying"></a>クエリ用のファイルを準備する

可能であれば、ファイルを準備してパフォーマンスを向上させることができます。

- CSV と JSON を Parquet に変換します。 Parquet は、列形式です。 圧縮されるため、同じデータを含む CSV または JSON ファイルよりもファイル サイズが小さくなります。 SQL オンデマンドで読み取るために必要な時間が短くなりストレージ要求も少なくなります。
- クエリが 1 つの大きなファイルを対象としている場合は、複数の小さなファイルに分割すると効果があります。
- CSV ファイルのサイズを 10 GB 未満にしてください。
- 1 つの OPENROWSET パスまたは外部テーブル LOCATION に対して、ファイルのサイズを同じにすることをお勧めします。
- パーティションを異なるフォルダーまたはファイル名に格納して、データをパーティション分割します。 「[filename および filepath 関数を使用して特定のパーティションを対象にする](#use-filename-and-filepath-functions-to-target-specific-partitions)」を参照してください。

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>パスの下位階層にワイルドカードを指定する

ご自分のパスでワイルドカードを使用して、[複数のファイルとフォルダー](query-data-storage.md#query-multiple-files-or-folders)にクエリを実行できます。 SQL オンデマンドは、ストレージ API を使用して、ストレージ アカウントのファイルを最初の * から一覧表示します。 指定したパスと一致しないファイルは除外します。 最初のワイルドカードまで、指定したパスに一致するファイルが多数ある場合は、ファイルの最初の一覧を減らすことで、パフォーマンスが向上する可能性があります。

## <a name="use-appropriate-data-types"></a>適切なデータ型を使用する

クエリで使用するデータ型は、パフォーマンスに影響します。 パフォーマンスは、次のガイドラインに従って向上させることができます。 

- 可能な最大値に対応する最小のデータ サイズを使用します。
  - 文字値の最大長が 30 文字の場合は、長さが 30 の文字データ型を使用します。
  - すべての文字列値が固定サイズの場合は、**char** または **nchar** を使用します。 それ以外の場合は、**varchar** または **nvarchar** を使用します。
  - 整数列の最大値が 500 の場合は、**smallint** を使用します。この値に対応する最小のデータ型であるためです。 整数のデータ型の範囲は、[この記事で](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)ご確認いただけます。
- 可能であれば、**nvarchar** と **nchar** ではなく、**varchar** と **char** を使用します。
- 可能であれば、整数ベースのデータ型を使用します。 SORT、JOIN、および GROUP BY 操作は、文字データよりも整数で速く完了します。
- スキーマ推論を使用している場合は、「[推論されたデータ型を確認する](#check-inferred-data-types)」をご確認ください。

## <a name="check-inferred-data-types"></a>推論されたデータ型を確認する

[スキーマ推論](query-parquet-files.md#automatic-schema-inference)を使用すると、ファイル スキーマを知らなくてもクエリをすばやく作成し、データを探索することができます。 これは便利ですが、その引き換えに、推論されたデータ型が実際のデータ型よりも大きくなります。 これは、適切なデータ型が使用されていることを確認できる十分な情報がソース ファイルにない場合に発生します。 たとえば、Parquet ファイルには最大文字列長のメタデータが含まれません。 そのため、SQL オンデマンドで varchar (8000) として推論されます。

ご自分のクエリの結果のデータ型は、[sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15) を使用して確認できます。

推論されたデータ型を最適化する方法を次の例に示します。 この手順は、推論されたデータ型を示すために使用しています。 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

結果セットは次のとおりです。

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|INT|4|

クエリの推論されるデータ型がわかれば、適切なデータ型を指定できます。

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>filename および filepath 関数を使用して特定のパーティションを対象にする

多くの場合、データはパーティションに編成されます。 特定のフォルダーやファイルに対してクエリを実行するよう、SQL オンデマンドに指示することができます。 こうすることで、クエリで読み取りおよび処理する必要があるファイルの数とデータの量が減ります。 副次的効果として、パフォーマンスが向上します。

詳細については、[filename](query-data-storage.md#filename-function) 関数、[filepath](query-data-storage.md#filepath-function) 関数、および[特定のファイルにクエリを実行する](query-specific-files.md)例を参照してください。

> [!TIP]
> filepath 関数と filename 関数の結果は、常に適切なデータ型にキャストしてください。 文字データ型を使用する場合は、適切な長さを確実に使用します。

> [!NOTE]
> 現在、パーティションの削除、filepath および filename 用に使用される関数は、Apache Spark for Azure Synapse Analytics で作成された各テーブルに対して自動的に作成されたもの以外の外部テーブルに対してはサポートされていません。

格納されているデータがパーティション分割されていない場合は、パーティション分割を検討してください。 そうすると、それらの関数を使用して、それらのファイルを対象とするクエリを最適化することができます。 SQL オンデマンドから、[パーティション分割された Apache Spark for Azure Synapse テーブルに対してクエリを実行](develop-storage-files-spark-tables.md)すると、必要なファイルだけが自動的にクエリの対象となります。

## <a name="use-parser_version-20-to-query-csv-files"></a>CSV ファイルにクエリを実行するために PARSER_VERSION 2.0 を使用する

CSV ファイルに対してクエリを実行するときに、パフォーマンス最適化パーサーを使用できます。 詳細については、[PARSER_VERSION](develop-openrowset.md) に関する記事を参照してください。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS を使用してクエリのパフォーマンスと結合を強化する

[CETAS](develop-tables-cetas.md) は、SQL オンデマンドで利用できる最も重要な機能の 1 つです。 CETAS は、外部テーブルのメタデータを作成し、SELECT クエリの結果をストレージ アカウント内の一連のファイルにエクスポートする並列操作です。

CETAS を使用して、結合された参照テーブルなど、クエリの頻繁に使用される部分を新しいファイル セットに格納できます。 次に、複数のクエリで共通の結合を繰り返す代わりに、この単一の外部テーブルに結合することができます。

CETAS によって Parquet ファイルが生成されると、最初のクエリがこの外部テーブルを対象とするときに統計が自動的に作成され、結果としてパフォーマンスが向上します。

## <a name="azure-ad-pass-through-performance"></a>Azure AD パススルー パフォーマンス

SQL オンデマンドを使用すると、Azure Active Directory (Azure AD) パススルーまたは SAS 資格情報を使用してストレージ内のファイルにアクセスできます。 Azure AD パススルーを使用する場合、SAS と比較してパフォーマンスが低下する可能性があります。

パフォーマンスを向上させる必要がある場合は、Azure AD パススルーのパフォーマンスが向上するまで、ストレージへのアクセスのために SAS 資格情報をお試しください。

## <a name="next-steps"></a>次のステップ

[トラブルシューティング](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関する記事で、一般的な問題と解決方法を確認します。 SQL オンデマンドではなく SQL プールを使用している場合の具体的なガイダンスについては、[SQL プール向けのベスト プラクティス](best-practices-sql-pool.md)に関する記事を参照してください。
