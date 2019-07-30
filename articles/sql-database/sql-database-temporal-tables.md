---
title: Azure SQL Database のテンポラル テーブルの概要 | Microsoft Docs
description: Azure SQL Database のテンポラル テーブルの使い方について基本的な事柄を説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: ac57e873a6948e85e78849bd4e22db85491c4bfb
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441540"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Azure SQL Database のテンポラル テーブルの概要

テンポラル テーブルは、Azure SQL Database の新しいプログラミング機能です。テンポラル テーブルを使用することで、データに対する詳細な変更履歴を追跡、分析することができ、独自にコーディングする必要がありません。 テンポラル テーブルは、保存されている情報が特定の期間に限り有効と解釈されるように、データを常に時間のコンテキストと密接に関連付けます。 テンポラル テーブルが持つこの特性によって、時間に基づいた効率的な分析が可能となり、データの経時的変化に隠れた本質を見極めることができます。

## <a name="temporal-scenario"></a>テンポラル テーブルのシナリオ

この記事では、アプリケーションのシナリオでテンポラル テーブルを活用する手順を解説しています。 ゼロから開発した新しい Web サイトか、ユーザー アクティビティの分析機能を追加して拡張した既存の Web サイトでユーザー アクティビティを追跡することになったとします。 この例で分析対象となるインジケーターは、特定の期間にアクセスのあった Web ページの数です。Azure SQL Database にホストされている Web サイトのデータベースでこれを収集し、監視するものとします。 ユーザー アクティビティの履歴を分析することで、Web サイトのデザインを変更するうえで役立つデータを得ると共に、訪問者の使用感を高めるのがねらいです。

このシナリオのデータベース モデルはごくシンプルです。ユーザー アクティビティのメトリックは **PageVisited** という単一の整数フィールドで表され、該当するユーザー プロファイルの基本的な情報と共に収集されます。 また、時間に基づく分析ということで、ユーザーごとに一連の行を保持することになるでしょう。ひとつひとつの行で、特定の期間に特定のユーザーが訪問したページ数を表します。

![スキーマ](./media/sql-database-temporal-tables/AzureTemporal1.png)

さいわい、このアクティビティ情報を保持するために、アプリに手を加える必要はありません。 この処理は、テンポラル テーブルによって自動的に行われるため、Web サイトの設計の幅が広がり、より多くの時間をデータの分析そのものに費やすことができます。 必要な作業は **WebSiteInfo** テーブルを " [システム バージョン管理のテンポラル テーブル](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0)" として構成するだけです。 以降、このシナリオのテンポラル テーブルを利用するための詳しい手順を説明します。

## <a name="step-1-configure-tables-as-temporal"></a>手順 1:テーブルをテンポラルとして構成する
アプリケーションをゼロから開発するか、既にあるアプリケーションをアップグレードするかに応じて、テンポラル テーブルを作成するか、または既存のテーブルに経時的な属性を追加します。 実際には、その両方の作業が必要になることも少なからずあるでしょう。 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS) や [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) など、任意の Transact-SQL 開発ツールを使用して以下の作業を実行してください。

> [!IMPORTANT]
> 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

### <a name="create-new-table"></a>新しいテーブルの作成
SSMS オブジェクト エクスプローラーの "システム バージョン管理テーブルの新規作成" というコンテキスト メニュー項目を使用して、クエリ エディターを開き、テンポラル テーブル テンプレート スクリプトを表示した後、[テンプレート パラメーターの値の指定] \(Ctrl + Shift + M) を使用してテンプレートに値を入力します。

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

SSDT では、データベース プロジェクトに新しい項目を追加するときに "テンポラル テーブル (システム バージョン管理)" テンプレートを選択します。 これでテーブル デザイナーが開いて、テーブルのレイアウトが簡単に指定できます。

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

以下の例のように Transact-SQL ステートメントを直接指定してテンポラル テーブルを作成することもできます。 PERIOD の定義と SYSTEM_VERSIONING 句は、すべてのテンポラル テーブルに必須の要素であることに注意してください。SYSTEM_VERSIONING 句では、行バージョンの履歴を格納する別のユーザー テーブルへの参照を指定します。

```
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

システム バージョン管理のテンポラル テーブルを作成すると、それに付随する履歴テーブルが既定の構成で自動的に作成されます。 既定の履歴テーブルには、期間列 (end、start) に対するクラスター化された B ツリー インデックスが、ページ圧縮が有効な状態で存在します。 これは、テンポラル テーブルを使った大半のシナリオ (特に [データの監査](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0)) に最適な構成です。 

しかしここで扱うのは特殊なケースであり、もっと長期的なデータ履歴と大きなデータ セットで、時間に基づいた傾向分析を行うのが目的です。履歴テーブルのストレージには、クラスター化 columnstore インデックスを選択します。 クラスター化 columnstore が持つきわめて優れた圧縮とパフォーマンスを分析クエリで活かすことができます。 テンポラル テーブルに対するインデックスは、現在のテーブルに対するインデックスと完全に分けて構成できるようになっています。 

> [!NOTE]
> 列ストア インデックスは、Premium レベルと Standard レベル、S3 以上で使用できます。
>

次のスクリプトは、履歴テーブルの既定のインデックスをクラスター化 columnstore に変更する方法を示したものです。

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

オブジェクト エクスプローラーには、テンポラル テーブルが識別しやすいよう固有のアイコンで表示され、その子ノードとして対応する履歴テーブルが表示されます。

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>既存のテーブルをテンポラルに変更する
もうひとつの例として、WebsiteUserInfo テーブルは既に存在するものの、変更の履歴を保持するようには設計されていなかった場合のシナリオを取り上げます。 このケースは、既にあるテーブルをテンポラル テーブルに拡張するだけで対応できます。その例を次に示します。

```
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

## <a name="step-2-run-your-workload-regularly"></a>手順 2:ワークロードを定期的に実行する
テンポラル テーブルの大きな利点は、変更を追跡するために、既存の Web サイトになんら変更や調整を加える必要がないことです。 テンポラル テーブルは一度作成すれば、その後はユーザーに意識させることなく、データに変更が行われるたびに、以前の行バージョンを保存します。 

このシナリオで変更の自動追跡を利用するために、ユーザーが Web サイト セッションを終了するたびに、**PagesVisited** 列を更新してみましょう。

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

実際の操作が厳密にいつ実行されたかや、将来の分析に使う履歴データをどのように保持するかについて、更新クエリ側は一切、関知する必要がない点に注目してください。 どちらの側面も、Azure SQL Database によって自動的に処理されます。 次の図は、更新のたびに履歴データが生成されていくようすを表したものです。

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>手順 3:履歴データの分析を実行する
テンポラル テーブルのシステム バージョン管理を有効にしたらクエリを実行するだけです。それだけで履歴データの分析ができます。 この記事では、一般的な分析のニーズに対応する例をいくつか紹介します。細かな点も余さずお伝えするために、[FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) 句で導入されたさまざまなオプションについて詳しく見ていきます。

1 時間前の時点における訪問 Web ページ数の順に上位 10 ユーザーを表示するには、次のクエリを実行します。

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

このクエリにほんの少し変更を加えれば、1 日前や 1 か月前など過去の任意の時点におけるサイトのアクセス数を分析することができます。

前日の基本的な統計分析を実行するには、次の例を使用します。

```
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

特定のユーザーの一定期間におけるアクティビティを検索するには、CONTAINED IN 句を使用します。

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

グラフィック表現は、傾向や使用パターンをごく簡単な方法で直感的に示すことができるため、経時的なクエリにはうってつけです。

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>テーブル スキーマの刷新
通常、テンポラル テーブルのスキーマは、アプリの開発中に変更する必要があります。 この場合、通常の ALTER TABLE ステートメントを実行すれば、Azure SQL Database によって適切に変更が履歴テーブルに伝達されます。 次のスクリプトでは、追跡に使用する属性を追加しています。

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

同様に、ワークロードがアクティブな状態でも、列の定義を変更することができます。

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

また、不要になった列は削除することができます。

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

同じことは、最新の [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) を使用して行うこともできます。データベースに接続した状態で (オンライン モード) またはデータベース プロジェクトの一部として (オフライン モード)、テンポラル テーブルのスキーマを変更することができます。

## <a name="controlling-retention-of-historical-data"></a>履歴データのリテンション期間を制御する
システム バージョン管理のテンポラル テーブルでは、履歴テーブルによるデータベース サイズの増大が、通常のテーブルよりも顕著である場合があります。 サイズが大きいうえに絶えず増大する履歴テーブルは、純粋にストレージ コストの面だけでなく、テンポラル クエリのパフォーマンスの足かせとなるという点でも、問題となる可能性があります。 したがって、データの保持ポリシーを作成して履歴テーブルのデータを管理することが、あらゆるテンポラル テーブルのライフサイクルの計画と管理において重要な要素となります。 Azure SQL Database では、テンポラル テーブル内の履歴データを次の方法で管理できます。

* [テーブル パーティション](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [カスタム クリーンアップ スクリプト](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>次の手順

- テンポラル テーブルの詳細については、「チェック アウト[テンポラル テーブル](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)」をご覧ください。
- Channel 9 にアクセスして、[テンポラル テーブル導入による実際の成功事例](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions)や[テンポラル技術のライブ デモンストレーション](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)をご覧ください。

