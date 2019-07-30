---
title: Azure Data Explorer を使用して Azure Data Lake でデータのクエリを実行する
description: Azure Data Explorer を使用して Azure Data Lake でデータのクエリを実行する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454390"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Azure Data Explorer を使用して Azure Data Lake でデータのクエリを実行する (プレビュー)

Azure Data Lake Storage は、スケーラビリティが高く拡張性と費用対効果に優れた、ビッグ データ分析用のデータ レイク ソリューションです。 ハイパフォーマンス ファイル システムの能力に加えて、非常に高いスケーラビリティと効率性を兼ね備えており、お客様が分析情報を得るまでの時間を短縮するのに役立ちます。 Data Lake Storage Gen2 は Azure BLOB ストレージの機能を拡張するもので、分析ワークロード用に最適化されています。
 
Azure Data Explorer は、Azure BLOB ストレージおよび Azure Data Lake Storage Gen2 と統合され、レイク内のデータへの高速でキャッシュされたインデックス付きのアクセスを提供します。 レイク内のデータは、Azure Data Explorer に事前に取り込まずに分析およびクエリを実行できます。 また、取り込んだデータと取り込んでいないネイティブ　レイク データに対して同時にクエリを実行することもできます。  

> [!TIP]
> 最良のクエリ パフォーマンスを得るには、Azure Data Explorer へのデータの取り込みが必要です。 事前に取り込まずに Azure Data Lake Storage Gen2 内のデータのクエリを実行する機能は、履歴データか、ほとんどクエリが実行されないデータに対してのみ使用してください。
 
## <a name="optimize-query-performance-in-the-lake"></a>レイクでのクエリ パフォーマンスを最適化する 

* パフォーマンスを向上させ、クエリ時間を最適化するためにデータをパーティション分割します。
* パフォーマンス向上のためにデータを圧縮します (最適な圧縮のためには gzip、最良のパフォーマンスのためには lz4)。
* Azure BLOB ストレージまたは Azure Data Lake Storage Gen2 を、Azure Data Explorer クラスターと同じリージョンで使用します。 

## <a name="create-an-external-table"></a>外部テーブルの作成

1. `.create external table` コマンドを使用して、Azure Data Explorer に外部テーブルを作成します。 `.show`、`.drop`、および `.alter` などの追加の外部テーブル コマンドについては、「[外部テーブル コマンド](/azure/kusto/management/externaltables)」に記載されています。

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    このクエリでは、日単位のパーティション *container1/yyyy/MM/dd/all_exported_blobs.csv* が作成されます。 細かいパーティション分割を使用することでパフォーマンスの向上が期待されます。 たとえば、上記のような日単位のパーティションを使用する外部テーブルでのクエリは、月単位のパーティション テーブルを使用したクエリよりもパフォーマンスが良くなります。

    > [!NOTE]
    > 現在サポートされているストレージ アカウントは、Azure BLOB ストレージまたは Azure Data Lake Storage Gen2 です。 現在サポートされているデータ形式は、csv、tsv、および txt です。

1. 外部テーブルは Web UI の左側のウィンドウに表示されます

    ![Web UI の外部テーブル](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>外部テーブルのアクセス許可
 
* データベース ユーザーは外部テーブルを作成できます。 テーブルの作成者は、自動的にそのテーブルの管理者になります。
* クラスター、データベース、またはテーブルの管理者は、既存のテーブルを編集できます。
* すべてのデータベースのユーザーまたは閲覧者は、外部テーブルのクエリを実行できます。
 
## <a name="query-an-external-table"></a>外部テーブルのクエリを実行する
 
外部テーブルのクエリを実行するには、`external_table()` 関数を使用し、関数の引数としてテーブル名を指定します。 クエリの残りの部分は、標準的な Kusto クエリ言語です。

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense は、現在、外部テーブルのクエリではサポートされていません。

## <a name="query-external-and-ingested-data-together"></a>外部データと取り込んだデータのクエリをまとめて実行する

同じクエリ内で、外部テーブルと取り込んだデータ テーブルの両方のクエリを実行できます。 外部テーブルを、Azure Data Explorer、SQL サーバー、またはその他のソースからの追加データと [`join`](/azure/kusto/query/joinoperator) または [`union`](/azure/kusto/query/unionoperator) します。 [`let( ) statement`](/azure/kusto/query/letstatement) を使用して、外部テーブル参照に短縮名を割り当てます。

以下の例で、*Products* は取り込んだデータ テーブルで、*ArchivedProducts* は、Azure Data Lake Storage Gen2 のデータを含む外部テーブルです。

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>ヘルプ クラスターで *TaxiRides* 外部テーブルのクエリを実行する

*TaxiRides* サンプル データ セットには、[ニューヨーク市タクシーおよびリムジン委員会](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)からのニューヨーク市のタクシーのデータが含まれています。

### <a name="create-external-table-taxirides"></a>外部テーブル *TaxiRides* を作成する 

> [!NOTE]
> このセクションでは、*ヘルプ* クラスターに *TaxiRides* 外部テーブルを作成するために使用されるクエリを示します。 このテーブルは既に作成済みのため、このセクションをスキップして、[*TaxiRides* 外部テーブル データのクエリを実行](#query-taxirides-external-table-data)できます。 

1. ヘルプ クラスターに外部テーブル *TaxiRides* を作成するために、次のクエリが使用されました。 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. 結果のテーブルが *ヘルプ* クラスターに作成されました。

    ![TaxiRides 外部テーブル](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>*TaxiRides* 外部テーブル データのクエリを実行する 

[https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) にサインインして、*TaxiRides* 外部テーブルのクエリを実行します。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>パーティション分割を行わずに *TaxiRides* 外部テーブルのクエリを実行する

外部テーブル *TaxiRides* で[このクエリを実行](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=)して、データ セット全体で曜日別の乗車状況を示します。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

このクエリにより、一週間で最も忙しい曜日が示されます。 データがパーティション分割されていないため、このクエリでは、結果が返されるまでに長い時間がかかる可能性があります (最大 7 分)。

![パーティション分割されていないクエリのレンダリング](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>パーティション分割を行って TaxiRides 外部テーブルのクエリを実行する 

外部テーブル *TaxiRides* に対して[このクエリを実行](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==)すると、2017 年 1 月に使用されたタクシーの種類 (イエローまたはグリーン) が示されます。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

このクエリではパーティション分割を使用しています。それにより、クエリの時間とパフォーマンスが最適化されます。 クエリではパーティション分割された列 (pickup_datetime) でフィルター処理が実行されて、数秒で結果が返されます。

![パーティション分割されたクエリのレンダリング](media/data-lake-query-data/taxirides-with-partition.png)
  
外部テーブル *TaxiRides* で実行する追加のクエリを記述することで、このデータについてさらに詳しく知ることができます。 

## <a name="next-steps"></a>次の手順

Azure Data Explorer を使用して Azure Data Lake でデータのクエリを実行します。 [クエリを記述](write-queries.md)して、データから追加の分析情報を得る方法について学習します。