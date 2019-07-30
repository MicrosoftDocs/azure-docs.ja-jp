---
title: SQL Data Warehouse でのユーザー定義スキーマの使用 |Microsoft Docs
description: Azure SQL Data Warehouse でソリューションを開発するための T-SQL ユーザー定義スキーマの使用に関するヒント。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e0ae00e0fca5ed4c6fba04444e5c50424462d297
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479578"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>SQL Data Warehouse でのユーザー定義スキーマの使用
Azure SQL Data Warehouse でソリューションを開発するための T-SQL ユーザー定義スキーマの使用に関するヒント。

## <a name="schemas-for-application-boundaries"></a>アプリケーション境界のスキーマ

従来のデータ ウェアハウスでは、多くの場合、個別のデータベースを使用して、ワークロード、ドメイン、またはセキュリティに基づくアプリケーション境界を作成しています。 たとえば、従来の SQL Server データ ウェアハウスは、ステージング データベース、データ ウェアハウス データベース、複数のデータ マート データベースで構成されていることがあります。 このトポロジでは、各データベースがアーキテクチャでワークロードとセキュリティの境界として機能します。

これに対して、SQL Data Warehouse では、データ ウェアハウスの全ワークロードを 1 つのデータベース内で実行します。 データベース間の結合は許可されていません。 そのため、SQL Data Warehouse では、ウェアハウスで使用されるすべてのテーブルが 1 つのデータベースに格納されていることが求められます。

> [!NOTE]
> SQL Data Warehouse では、複数データベースにまたがるクエリはサポートされていません。 したがって、このパターンを利用するデータ ウェアハウスの実装を変更する必要があります。
> 
> 

## <a name="recommendations"></a>Recommendations
ユーザー定義のスキーマを使用して、ワークロード、セキュリティ、ドメイン、機能の境界を統合する際の推奨事項を以下に示します。

1. 1 つの SQL Data Warehouse データベースを使用して、データ ウェアハウスの全ワークロードを実行します。
2. 既存のデータ ウェアハウス環境を統合して、1 つの SQL Data Warehouse データベースを使用します。
3. **ユーザー定義のスキーマ** を活用して、これまでデータベースを使用して実装されていた境界を提供します。

ユーザー定義のスキーマをこれまで使用していなかった場合は白紙の状態です。 SQL Data Warehouse データベースのユーザー定義のスキーマの基礎として、古いデータベース名を使用します。

スキーマを既に使用していた場合、次のオプションがあります。

1. 従来のスキーマ名を削除し、最初からやり直す
2. テーブル名の先頭に従来のスキーマ名を追加することで、従来のスキーマ名を保持する
3. 追加のスキーマにテーブルのビューを実装して、古いスキーマ構造を再作成することで、従来のスキーマ名を保持する

> [!NOTE]
> 一見すると、オプション 3 が最も魅力的なオプションのように思えるかもしれませんが、 細部に落とし穴があります。 SQL Data Warehouse では、ビューは読み取り専用です。 データまたはテーブルの変更は、すべてベース テーブルに対して実行する必要があります。 また、オプション 3 では、ビューのレイヤーがシステムに導入されます。 アーキテクチャでビューを既に使用している場合は、この点をさらに検討することをお勧めします。
> 
> 

### <a name="examples"></a>次に例を示します。
データベース名に基づくユーザー定義のスキーマを実装します。

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

テーブル名の先頭に従来のスキーマ名を追加することで、従来のスキーマ名を保持します。 ワークロードの境界にスキーマを使用します。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

ビューを使用して従来のスキーマ名を保持します。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> スキーマ戦略を変更する場合、データベースのセキュリティ モデルを見直す必要があります。 多くの場合、スキーマ レベルでアクセス許可を割り当てることで、セキュリティ モデルを簡素化できます。 きめ細かなアクセス許可が必要な場合は、データベース ロールを使用します。
> 
> 

## <a name="next-steps"></a>次の手順
開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。

