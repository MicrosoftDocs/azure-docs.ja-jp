---
title: クイック スタート:単一の T-SQL ステートメントを使用してデータを一括読み込みする
description: COPY ステートメントを使用してデータを一括読み込みする
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 06/18/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: f82bedc6ef638714b2641003e8274c2024a86c2e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213008"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>クイック スタート:COPY ステートメントを使用してデータを一括読み込みする

このクイックスタートでは、シンプルで柔軟性の高い [COPY ステートメント](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)による高スループットのデータ インジェストを使用して、SQL プールにデータを一括読み込みします。 COPY ステートメントは、次の機能を備えることによってシームレスかつフレキシブルにデータを読み込めることから推奨されている読み込みユーティリティです。

- さほど権限の高くないユーザーでもデータの読み込みを実行できます。データ ウェアハウスに対する厳格な CONTROL アクセス許可は不要です。
- 単一の T-SQL ステートメントのみを活用でき、別途データベース オブジェクトを作成する必要はありません。
- Shared Access Signature (SAS) を使用してストレージ アカウント キーを公開しなくても、より洗練されたアクセス許可モデルを活用できます。
- ERRORFILE の場所 (REJECTED_ROW_LOCATION) として異なるストレージ アカウントを指定できます。
- ターゲット列ごとに既定値をカスタマイズし、特定のターゲット列に読み込むソース データ フィールドを指定できます。
- CSV ファイルのカスタム行ターミネータを指定できます。
- CSV ファイルの文字列、フィールド、行の区切り記号をエスケープできます。
- CSV ファイルで SQL Server の日付形式を活用できます。
- 保存場所のパスにワイルドカードや複数のファイルを指定できます。

## <a name="prerequisites"></a>前提条件

このクイックスタートでは、SQL プールが既に用意されていることを前提とします。 SQL プールが未作成である場合は、[「作成と接続」の「ポータル」](create-data-warehouse-portal.md)のクイックスタートを参照してください。

## <a name="set-up-the-required-permissions"></a>必要なアクセス許可を設定する

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>ターゲット テーブルを作成する

この例では、ニューヨークのタクシー データセットからデータを読み込みます。 1 年以内に行われたタクシーの乗車を表す Trip というテーブルを読み込みます。 このテーブルを作成するには、次のコードを実行します。

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>COPY ステートメントを実行する

次の COPY ステートメントを実行します。このステートメントによって、Azure Blob Storage アカウントから Trip テーブルにデータが読み込まれます。

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>読み込みを監視する

次のクエリを定期的に実行して、読み込みが進行しているかどうかをチェックします。

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>次のステップ

- データ読み込みのベスト プラクティスについては、[データ読み込みのベスト プラクティス](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)に関するページを参照してください。
- データ読み込みのリソースを管理する方法については、[ワークロードの分離](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql)に関するページを参照してください。 
