---
title: ユーザー定義スキーマの使用
description: Azure Synapse Analytics の専用 SQL プール用のソリューションを開発するための T-SQL ユーザー定義スキーマの使用に関するヒント。
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3204c77dd076d9aac6eb5a60b489280caefcbf4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460453"
---
# <a name="user-defined-schemas-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プールのユーザー定義スキーマ
この記事では、T-SQL ユーザー定義スキーマを使用して専用 SQL プールでソリューションを開発するためのいくつかのヒントに焦点を合わせています。

## <a name="schemas-for-application-boundaries"></a>アプリケーション境界のスキーマ

従来のデータ ウェアハウスでは、多くの場合、個別のデータベースを使用して、ワークロード、ドメイン、またはセキュリティに基づくアプリケーション境界を作成しています。 

たとえば、従来の SQL Server データ ウェアハウスは、ステージング データベース、データ ウェアハウス データベース、複数のデータ マート データベースで構成されていることがあります。 このトポロジでは、各データベースがアーキテクチャでワークロードとセキュリティの境界として機能します。

これに対して、専用 SQL プールは 1 つのデータベース内でデータ ウェアハウスの全ワークロードを実行します。 データベース間の結合は許可されていません。 専用 SQL プールでは、ウェアハウスで使用されるすべてのテーブルが 1 つのデータベースに格納されていることが求められます。

> [!NOTE]
> SQL プールでは、複数データベースにまたがるクエリはサポートされていません。 したがって、このパターンを利用するデータ ウェアハウスの実装を変更する必要があります。
> 
> 

## <a name="recommendations"></a>Recommendations
ユーザー定義のスキーマを使用して、ワークロード、セキュリティ、ドメイン、機能の境界を統合する際のレコメンデーションを以下に示します。

- 1 つの専用 SQL プール内の 1 つのデータベースを使用して、データ ウェアハウスの全ワークロードを実行します。
- 既存のデータ ウェアハウス環境を統合して、1 つの専用 SQL プール データベースを使用します。
- **ユーザー定義のスキーマ** を活用して、これまでデータベースを使用して実装されていた境界を提供します。

ユーザー定義のスキーマをこれまで使用していなかった場合は白紙の状態です。 専用 SQL プール データベースのユーザー定義スキーマの基礎として、古いデータベース名を使用します。

スキーマを既に使用していた場合、次のオプションがあります。

- 従来のスキーマ名を削除し、最初からやり直す。
- テーブル名の先頭に従来のスキーマ名を追加することで、従来のスキーマ名を保持する。
- 追加のスキーマにテーブルのビューを実装して、古いスキーマ構造を再作成することで、従来のスキーマ名を保持する

> [!NOTE]
> 一見すると、オプション 3 が最も魅力的なオプションのように思えるかもしれませんが、 細部に落とし穴があります。 専用 SQL プールでは、ビューは読み取り専用です。 データまたはテーブルの変更は、すべてベース テーブルに対して実行する必要があります。 また、オプション 3 では、ビューのレイヤーがシステムに導入されます。 アーキテクチャでビューを既に使用している場合は、この点をさらに検討することをお勧めします。
> 
> 

### <a name="examples"></a>例 :
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

ビューを使用してレガシのスキーマ名を保持します。

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

## <a name="next-steps"></a>次のステップ
開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。

