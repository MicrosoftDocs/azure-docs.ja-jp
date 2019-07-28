---
title: スケール アウトされたクラウド データベース全体のレポート (行方向のパーティション分割) |Microsoft Docs
description: 複数のデータベース全体についてレポートするには、複数のデータベースにまたがるデータベース クエリを使用します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mladjoa
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a73938c98ebaea310875f0db8b665d0f1aed55e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556278"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>スケールアウトされたクラウド データベース全体のレポート (プレビュー)

1 つの接続ポイントから複数の Azure SQL データベースのレポートを作成するには、 [エラスティック クエリ](sql-database-elastic-query-overview.md)を使用します。 データベースは、行方向にパーティション分割する必要があります ("シャード化" とも呼ばれます)。

既存のデータベースがある場合は、 [スケール アウトされたデータベースへの既存データベースの移行](sql-database-elastic-convert-to-use-elastic-tools.md)に関するページをご覧ください。

クエリに必要な SQL オブジェクトについては、 [行方向にパーティション分割されたデータベース間のクエリ](sql-database-elastic-query-horizontal-partitioning.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

[「エラスティック データベース ツールの概要」に示されているサンプル](sql-database-elastic-scale-get-started.md)をダウンロードして実行します。

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>サンプル アプリケーションを使用してシャード マップ マネージャーを作成する
ここでは、シャード マップ マネージャーといくつかのシャードを作成し、シャードにデータを挿入します。 シャードがすでにセットアップされ、シャード データが取り込まれている場合は、以下の手順を省略して、次のセクションに進むことができます。

1. 「 **「エラスティック データベース ツールの概要** 」に示されているサンプル アプリケーションをビルドして実行します。 セクション「[サンプル アプリケーションのダウンロードと実行](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app)」の手順 7 まで実行します。 手順 7 を終了すると、次のコマンド プロンプトが表示されます。

    ![コマンド プロンプト][1]
2. コマンド ウィンドウで、「1」を入力し、**Enter** キーを押します。 シャード マップ マネージャーが作成され、2 つのシャードがサーバーに追加されます。 「3」を入力し、**Enter** キーを押します。この操作を 4 回を繰り返します。 これにより、サンプルのデータ行がシャードに挿入されます。
3. [Azure Portal](https://portal.azure.com) に、サーバー内の 3 つの新しいデータベースが表示されます。

   ![Visual Studio の確認][2]

   この時点で、複数のデータベースにまたがるクエリは、エラスティック データベース クライアント ライブラリを介してサポートされます。 たとえば、コマンド ウィンドウのオプション 4 を使用するとします。 マルチ シャード クエリの結果は常に、すべてのシャードからの結果に **UNION ALL** を適用したものとなります。

   次のセクションでは、複数のシャードにまたがって実行されるデータの高度なクエリをサポートするサンプル データベース エンドポイントを作成します。

## <a name="create-an-elastic-query-database"></a>エラスティック クエリ データベースを作成する
1. [Azure Portal](https://portal.azure.com) を開き、ログインします。
2. シャード セットアップと同じサーバーに新しい Azure SQL データベースを作成します。 このデータベースに "ElasticDBQuery" という名前を付けます。

    ![Azure ポータルと価格レベル][3]

    > [!NOTE]
    > 既存のデータベースを使用できます。 そうする場合、その既存のプレミアム データベースを、クエリの実行対象とするシャードに含めることはできません。 このデータベースは、エラスティック データベース クエリのメタデータ オブジェクトを作成するために使用されます。
    >

## <a name="create-database-objects"></a>データベース オブジェクトを作成する
### <a name="database-scoped-master-key-and-credentials"></a>データベース スコープのマスター キーと資格情報
これらを使用し、次の手順に従ってシャード マップ マネージャーとシャードに接続します。

1. SQL Server Management Studio または Visual Studio の SQL Server Data Tools を開きます。
2. ElasticDBQuery データベースに接続し、次の T-SQL コマンドを実行します。

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "username" と "password" は、「[Elastic Database ツールの概要](sql-database-elastic-scale-get-started.md)」の「[サンプル アプリケーションのダウンロードと実行](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app)」の手順 6 で使用するログイン情報と同じである必要があります。

### <a name="external-data-sources"></a>外部データ ソース
外部データ ソースを作成するには、ElasticDBQuery データベースで、次のコマンドを実行します。

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 エラスティック データベース ツールのサンプルを使用してシャード マップとシャード マップ マネージャーを作成した場合、"CustomerIDShardMap" はシャード マップの名前となります。 ただし、このサンプルでカスタム セットアップを使用した場合は、アプリケーションで選択するシャード マップ名となります。

### <a name="external-tables"></a>外部テーブル
シャード上の Customers テーブルと一致する外部テーブルを作成するには、ElasticDBQuery データベースに対して次のコマンドを実行します。

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>サンプルのエラスティック データベース T-SQL クエリを実行する
外部データ ソースと外部テーブルを定義すると、外部テーブルに対して完全に T-SQL を使用できるようになります。

ElasticDBQuery データベースでこのクエリを実行します。

    select count(CustomerId) from [dbo].[Customers]

クエリはすべてのシャードからの結果を集計し、次のように出力を表示します。

![出力の詳細][4]

## <a name="import-elastic-database-query-results-to-excel"></a>エラスティック データベース クエリの結果を Excel にインポートする
 クエリの結果は Excel ファイルにインポートすることができます。

1. Excel 2013 を起動します。
2. **[データ]** リボンに移動します。
3. **[その他のデータ ソース]** をクリックし、 **[SQL Server]** をクリックします。

   ![他のソースから Excel へのインポート][5]
4. **[データ接続ウィザード]** で、サーバー名とログイン時の資格情報を入力します。 その後、 **[次へ]** をクリックします。
5. **[使用するデータが含まれているデータベースを選択]** ダイアログ ボックスで、 **[ElasticDBQuery]** データベースを選択します。
6. リスト ビューで **[Customers]** テーブルを選択し、 **[次へ]** をクリックします。 **[完了]** をクリックします。
7. **[データのインポート]** フォームの **[このデータをブックでどのように表示するかを選択してください。]** で、 **[テーブル]** を選択し、 **[OK]** をクリックします。

さまざまなシャードに格納されている、 **[Customers]** テーブルからのすべての行が Excel シートに読み込まれます。

これで Excel の強力なデータ視覚化機能を使用できるようになりました。 サーバー名、データベース名、および資格情報が含まれる接続文字列を使用して、BI とデータ統合ツールをエラスティック クエリ データベースに接続することができます。 使用しているツールのデータ ソースとして SQL Server がサポートされていることを確認してください。 エラスティック クエリ データベースおよび外部テーブル (ツールを使用して接続するその他の SQL Server データベースおよび SQL Server テーブルのような) を参照できます。

### <a name="cost"></a>コスト
Elastic Database クエリ機能を使用する場合に追加の料金は発生しません。

料金情報については、「 [SQL Database の料金詳細](https://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。

## <a name="next-steps"></a>次の手順

* エラスティック クエリの概要については、「[Azure SQL Database エラスティック データベース クエリの概要 (プレビュー)](sql-database-elastic-query-overview.md)」をご覧ください。
* 列方向のパーティション分割のチュートリアルについては、「[クロスデータベース クエリの概要 (列方向のパーティション分割) (プレビュー)](sql-database-elastic-query-getting-started-vertical.md)」をご覧ください。
* 列方向にパーティション分割されたデータの構文とサンプル クエリについては、「[例: 列方向にパーティション分割されたデータベースのクエリ](sql-database-elastic-query-vertical-partitioning.md)」をご覧ください。
* 行方向にパーティション分割されたデータの構文とサンプル クエリについては、「[スケールアウトされたクラウド データベース全体をレポートする (プレビュー)](sql-database-elastic-query-horizontal-partitioning.md)」をご覧ください。
* 行方向のパーティション分割方式でシャードとして機能する単一のリモート Azure SQL Database またはデータベースのセットに対して Transact-SQL ステートメントを実行するストアド プロシージャについては、「[sp\_execute\_remote](https://msdn.microsoft.com/library/mt703714)」をご覧ください。


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
