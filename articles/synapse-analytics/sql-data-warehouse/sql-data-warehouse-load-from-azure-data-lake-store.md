---
title: 'チュートリアル: Azure Data Lake Storage からデータを読み込む'
description: COPY ステートメントを使用して、専用 SQL プール用に Azure Data Lake Storage からデータを読み込む。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: ca57c6200cf7006a89be4b1fd621974559e5b514
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606125"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プールに Azure Data Lake Storage からデータを読み込む

このガイドでは、[COPY ステートメント](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)を使用して Azure Data Lake Storage からデータを読み込む方法について説明します。 すべての認証方法で COPY ステートメントを使用する簡単な例については、[専用 SQL プールを使用してデータを安全に読み込む](./quickstart-bulk-load-copy-tsql-examples.md)に関する記事を参照してください。

> [!NOTE]  
> COPY ステートメントに関するご意見や問題の報告は、配布リストの sqldwcopypreview@service.microsoft.com までメールでお寄せください。
>
> [!div class="checklist"]
>
> * Azure Data Lake Storage からデータを読み込む対象テーブルを作成します。
> * データ ウェアハウスにデータを読み込む COPY ステートメントを作成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを始める前に、最新バージョンの [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) をダウンロードしてインストールします。

このチュートリアルを実行するには、次のものが必要です。

* 専用 SQL プール。 [専用 SQL プールの作成とデータのクエリ](create-data-warehouse-portal.md)に関する記事をご覧ください。
* Data Lake Storage アカウント。 [Azure Data Lake Storage の使用開始](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関する記事を参照してください。 このストレージ アカウントでは、次のいずれかの資格情報を構成または指定して読み込む必要があります。ストレージ アカウントに対する適切な Azure ロールを持つストレージ アカウント キー、Shared Access Signature (SAS) キー、Azure Directory アプリケーション ユーザー、または AAD ユーザー。

## <a name="create-the-target-table"></a>ターゲット テーブルを作成する

専用 SQL プールに接続して、読み込み先のターゲット テーブルを作成します。 この例では、製品ディメンション テーブルを作成します。

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>COPY ステートメントを作成する

専用 SQL プールに接続して、COPY ステートメントを実行します。 例の完全な一覧については、[専用 SQL プールを使用してデータを安全に読み込む](./quickstart-bulk-load-copy-tsql-examples.md)に関する記事を参照してください。

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>列ストア圧縮の最適化

既定では、テーブルはクラスター化列ストア インデックスとして定義されます。 読み込みの完了時、一部のデータ行が、列ストアに圧縮されない可能性があります。  これにはさまざまな理由があります。 詳しくは、[列ストア インデックスの管理](sql-data-warehouse-tables-index.md)に関するページをご覧ください。

読み込み後のクエリのパフォーマンスと列ストア圧縮を最適化するには、列ストア インデックスですべての行が強制的に圧縮されるようにテーブルを再構築します。

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>統計の最適化

読み込みの直後に単一列の統計を作成することをお勧めします。 統計には選択肢がいくつかあります。 たとえば、すべての列に対して単一列の統計を作成する場合は、すべての統計の再構築に時間がかかる場合があります。 クエリ述語に含まれない列があることがわかっている場合は、その列に対する統計の作成はスキップできます。

すべてのテーブルのすべての列で単一列の統計を作成する場合は、[統計に関する記事](sql-data-warehouse-tables-statistics.md)のストアド プロシージャのコード サンプル `prc_sqldw_create_stats` を使うことができます。

次の例は、統計の作成の出発点として適しています。 これによりディメンション テーブルの各列と、ファクト テーブルの各結合列に、単一列の統計が作成されます。 他のファクト テーブルの列には、後で単一列または複数列の統計をいつでも追加できます。

## <a name="achievement-unlocked"></a>結果

データがデータ ウェアハウスに正常に読み込まれました。 すばらしい結果です。

## <a name="next-steps"></a>次のステップ
データの読み込みは、Azure Synapse Analytics を使ってデータ ウェアハウス ソリューションを開発する際の最初の手順です。 開発リソースを確認してください。

> [!div class="nextstepaction"]
> [データ ウェアハウス用のテーブルを開発する方法を学習する](sql-data-warehouse-tables-overview.md)

読み込みの例とリファレンスに関する詳細については、次のドキュメントを確認してください。
- [COPY ステートメントのリファレンス ドキュメント](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [各認証方法での COPY の例](./quickstart-bulk-load-copy-tsql-examples.md)
- [単一テーブルでの COPY に関するクイック スタート](./quickstart-bulk-load-copy-tsql.md)