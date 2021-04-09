---
title: Synapse SQL 内のユーザー定義のスキーマ
description: 以下のセクションでは、T-SQL ユーザー定義スキーマを使用して、Azure Synapse Analytics の Synapse SQL 機能でソリューションを開発するためのさまざまなヒントについて説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6920d6cd190927747a69fc4dc0f0791278665abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451701"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Synapse SQL 内のユーザー定義のスキーマ

以下のセクションでは、T-SQL ユーザー定義スキーマを使用して Synapse SQL 内でソリューションを開発するためのさまざまなヒントについて説明します。

## <a name="schemas-for-application-boundaries"></a>アプリケーション境界のスキーマ

従来の分析アーキテクチャでは、ワークロード、ドメイン、またはセキュリティに基づいてアプリケーション境界を作成するために、個別のデータベースが使用されることがよくあります。 たとえば、従来の SQL Server 分析インフラストラクチャには、ステージング データベース、分析データベース、データ マート データベースなどが含まれている場合があります。 このトポロジでは、各データベースがアーキテクチャでワークロードとセキュリティの境界として機能します。

その代わりに、Synapse SQL では、分析ワークロード全体を 1 つのデータベース内で実行します。 データベース間の結合は許可されていません。 Synapse SQL では、ウェアハウスで使用されるすべてのテーブルが 1 つのデータベースに格納されていることが求められます。

> [!NOTE]
> 専用 SQL プールは、どのような種類の複数データベース間のクエリもサポートしません。 したがって、このパターンを利用する分析の実装は変更する必要があります。 サーバーレス SQL プールは、複数データベース間のクエリをサポートしています。

## <a name="user-defined-schema-recommendations"></a>ユーザー定義のスキーマに関する推奨事項

ユーザー定義のスキーマを使用して、ワークロード、セキュリティ、ドメイン、機能の境界を統合する場合の推奨事項を以下に示します。

- 1 つのデータベースを使用して、分析ワークロード全体を実行します。
- 既存の分析環境を統合することで、1 つのデータベースを使用します。
- **ユーザー定義のスキーマ** を活用して、これまでデータベースを使用して実装されていた境界を提供します。

ユーザー定義のスキーマをこれまで使用していなかった場合は白紙の状態です。 Synapse SQL データベースのユーザー定義のスキーマの基礎として、古いデータベース名を使用します。

スキーマを既に使用していた場合、次のオプションがあります。

- 従来のスキーマ名を削除し、最初からやり直す
- テーブル名の先頭に従来のスキーマ名を追加することで、従来のスキーマ名を保持する
- 追加のスキーマにテーブルのビューを実装する (これにより、古いスキーマ構造が再作成される) ことで、従来のスキーマ名を保持する

> [!NOTE]
> 一見すると、オプション 3 が最も魅力的な選択肢のように思えるかもしれません。 Synapse SQL では、ビューは読み取り専用です。 データまたはテーブルの変更は、すべてベース テーブルに対して実行する必要があります。 また、オプション 3 では、ビューのレイヤーがシステムに導入されます。 アーキテクチャでビューを既に使用している場合は、この点をさらに検討することをお勧めします。
> 
> 

### <a name="examples"></a>例

データベース名に基づくユーザー定義のスキーマを実装します。

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

テーブル名の先頭に従来のスキーマ名を追加することで、従来のスキーマ名を保持します。 ワークロードの境界にスキーマを使用します。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

ビューを使用して従来のスキーマ名を保持します。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> スキーマ戦略を変更する場合、データベースのセキュリティ モデルを見直す必要があります。 多くの場合、スキーマ レベルでアクセス許可を割り当てることで、セキュリティ モデルを簡素化できます。

きめ細かいアクセス許可が必要な場合は、データベース ロールを使用します。 データベース ロールについて詳しくは、「[データベース ロールとユーザーの管理](../../analysis-services/analysis-services-database-users.md)」の記事をご覧ください。

## <a name="next-steps"></a>次のステップ

開発に関するその他のヒントについては、[Synapse SQL の開発の概要](develop-overview.md)に関する記事をご覧ください。
