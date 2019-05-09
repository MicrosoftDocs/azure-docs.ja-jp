---
title: クロスデータベース クエリの概要 (列方向のパーティション分割) | Microsoft Docs
description: 垂直にパーティション分割されたデータベースでエラスティック データベース クエリを使用する方法
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 116a465a0ddc913e342e0ffcc1fb29f5bf969419
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464163"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>クロスデータベース クエリの概要 (列方向のパーティション分割) (プレビュー)

Azure SQL Database 用の エラスティック データベース クエリ (プレビュー) を使用すると、1 つの接続ポイントで複数のデータベースにまたがる T-SQL クエリを実行することができます。 この記事は、[列方向にパーティション分割されたデータベース](sql-database-elastic-query-vertical-partitioning.md)に適用されます。  

終了すると、複数の関連するデータベースにまたがるクエリを実行するために Azure SQL Database を構成および使用する方法を習得できます。

エラスティック データベース クエリ機能の詳細については、[Azure SQL Database エラスティック データベース クエリの概要](sql-database-elastic-query-overview.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

ALTER ANY EXTERNAL DATA SOURCE アクセス許可が必要です。 このアクセス許可は、ALTER DATABASE アクセス許可に含まれています。 ALTER ANY EXTERNAL DATA SOURCE アクセス許可は、基になるデータ ソースを参照するために必要です。

## <a name="create-the-sample-databases"></a>サンプル データベースの作成

最初に、**Customers** と **Orders** という 2 つのデータベースを同じ SQL Database サーバーか異なる SQL Database サーバーに作成します。

「**Orders**」データベースで次のクエリを実行し、「**OrderInformation**」テーブルを作成し、サンプル データを入力します。

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

次に、**Customers** データベースで次のクエリを実行して、**CustomerInformation** テーブルを作成し、サンプル データを入力します。

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>データベース オブジェクトを作成する

### <a name="database-scoped-master-key-and-credentials"></a>データベース スコープのマスター キーと資格情報

1. SQL Server Management Studio または Visual Studio の SQL Server Data Tools を開きます。
2. Orders データベースに接続し、次の T-SQL コマンドを実行します。

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    "username" と "password" は Customers データベースのログインに使用するユーザー名とパスワードになります。
    Azure Active Directory とエラスティック クエリを使用した認証は、現時点ではサポートされていません。

### <a name="external-data-sources"></a>外部データ ソース

外部データ ソースを作成するには、Orders データベースで、次のコマンドを実行します。

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>外部テーブル

CustomerInformation テーブルの定義に一致する外部テーブルを Orders データベースで作成します。

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>サンプルのエラスティック データベース T-SQL クエリを実行する

外部データ ソースと外部テーブルを定義すると、T-SQL を使用して外部テーブルにクエリを実行できるようになります。 Orders データベースでこのクエリを実行します。

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>コスト

現在のところ、エラスティック データベース クエリ機能は Azure SQL Database のコストに含まれています。  

料金情報については、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database)」を参照してください。

## <a name="next-steps"></a>次の手順

* エラスティック クエリの概要については、「[Azure SQL Database エラスティック データベース クエリの概要 (プレビュー)](sql-database-elastic-query-overview.md)」をご覧ください。
* 列方向にパーティション分割されたデータの構文とサンプル クエリについては、「[例: 列方向にパーティション分割されたデータベースのクエリ](sql-database-elastic-query-vertical-partitioning.md)」をご覧ください。
* 行方向のパーティション分割 (シャード化) のチュートリアルについては、「[スケールアウトされたクラウド データベース全体のレポート (プレビュー)](sql-database-elastic-query-getting-started.md)」をご覧ください。
* 行方向にパーティション分割されたデータの構文とサンプル クエリについては、「[スケールアウトされたクラウド データベース全体をレポートする (プレビュー)](sql-database-elastic-query-horizontal-partitioning.md)」をご覧ください。
* 行方向のパーティション分割方式でシャードとして機能する単一のリモート Azure SQL Database またはデータベースのセットに対して Transact-SQL ステートメントを実行するストアド プロシージャについては、「[sp\_execute\_remote](https://msdn.microsoft.com/library/mt703714)」をご覧ください。
