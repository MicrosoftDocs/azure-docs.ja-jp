---
title: アプリケーションとデータベースのパフォーマンス チューニング ガイダンス
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database および Azure SQL Managed Instance でデータベース アプリケーションとデータベースのパフォーマンスを調整する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/10/2020
ms.openlocfilehash: 40657ad2f3b69d62e0e0d9c7d9e0f0be7343547b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96490603"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database および Azure SQL Managed Instance でアプリケーションとデータベースのパフォーマンスを調整します
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database および Azure SQL Managed Instance で発生しているパフォーマンスの問題が明らかになったら、この記事を読むと次のことに役立ちます。

- アプリケーションを調整し、パフォーマンスを向上させるベスト プラクティスを適用します。
- データをより効率的に処理するようにインデックスとクエリを変更して、データベースをチューニングします。

この記事では、該当する場合は、Azure SQL Database の[データベース アドバイザーの推奨事項](database-advisor-implement-performance-recommendations.md)および Azure SQL Database の[自動チューニングの推奨事項](automatic-tuning-overview.md)に従った作業を既に行っているものとします。 また、[監視とチューニングの概要](monitor-tune-overview.md)に関する記事およびパフォーマンスの問題のトラブルシューティングに関するその関連記事を読んであるものとします。 さらに、この記事では、CPU リソースに、コンピューティング サイズまたはサービス レベルを上げてデータベースに対するリソースを増やすことで解決できる実行関連のパフォーマンスの問題がないことを前提としています。

## <a name="tune-your-application"></a>アプリケーションの調整

従来のオンプレミス SQL Server では多くの場合、初回の容量計画のプロセスは、運用環境でアプリケーションを実行するプロセスから分離されます。 最初にハードウェアと製品ライセンスが購入され、パフォーマンス調整は後で行われます。 Azure SQL を使用する場合、アプリケーションの実行と調整のプロセスを組み合わせることをお勧めします。 オンデマンド容量の支払いモデルでは、現在必要とされる最小のリソースを使用するようにアプリケーションを調整できます。(不正確なことが多い) アプリケーションの将来的な成長計画の推測に基づいて、ハードウェアに過剰プロビジョニングを行うことはしません。 アプリケーションを調整しないでハードウェア リソースを過剰にプロビジョニングすることを選ぶユーザーもいます。 この方法は、利用が集中する期間に重要なアプリケーションの変更を望まない場合に適していることがあります。 しかし、アプリケーションを調整することで、リソース要件を最小限に抑え、Azure SQL Database および Azure SQL Managed Instance のサービス レベルを使用する際に毎月の請求額を抑えることができます。

### <a name="application-characteristics"></a>アプリケーションの特性

Azure SQL Database および Azure SQL Managed Instance のサービス レベルは、アプリケーションのパフォーマンスの安定性と予測可能性を高めるように設計されています。一方で、いくつかのベスト プラクティスを実践することで、コンピューティング サイズ内でリソースを最大限に活用するようアプリケーションを調整できます。 多くのアプリケーションは、上位のコンピューティング サイズまたはサービス レベルに切り替えることでパフォーマンスが大幅に向上します。とはいえ、アプリケーションによっては、上位のサービス レベルの利点を活かすためにさらに調整が必要になります。 次のような特性を備えたアプリケーションでは、パフォーマンスを向上させるためにアプリケーションに調整を加えることを検討してください。

- **"煩雑な" 動作が原因でパフォーマンスの低いアプリケーション**

  煩雑なアプリケーションでは、ネットワークの待機時間が重要なデータ アクセス操作が過度に発生します。 この種のアプリケーションでは、データベースに対するデータ アクセス操作の数を減らすよう変更を施す必要があります。 たとえば、アドホック クエリを一括処理したり、ストアド プロシージャにクエリを移動したりするなどの手法を使って、アプリケーションのパフォーマンスを向上させることができます。 詳細については、「 [バッチ クエリ](#batch-queries)」を参照してください。

- **単一のマシンではサポートし切れないほどワークロードが集中するデータベース**

   最高の Premium コンピューティング サイズのリソースを超えるデータベースでは、ワークロードのスケールアウトを活用できる場合があります。 詳細については、「[データベース間のシャーディング](#cross-database-sharding)」と「[機能的パーティション分割](#functional-partitioning)」を参照してください。

- **最適でないクエリを含むアプリケーション**

  クエリが十分に調整されていない (特にデータ アクセス層の) アプリケーションの場合、上位のコンピューティング サイズの利点を活かせないことがあります。 たとえば、WHERE 句がない、インデックスが足りない、統計が古いクエリです。 これらのアプリケーションの場合、クエリ パフォーマンスの標準的な調整方法で効果が得られます。 詳細については、「[インデックスの不足](#identifying-and-adding-missing-indexes)」と「[クエリの調整とヒント](#query-tuning-and-hinting)」を参照してください。

- **データ アクセス設計が最適ではないアプリケーション**

   デッドロックなど、データ アクセスの同時性問題が内在するアプリケーションの場合、上位のコンピューティング サイズの利点を活かせないことがあります。 Azure キャッシュ サービスや他のキャッシング技術を利用し、クライアント側でデータをキャッシュすることで、データベースに対するラウンド トリップを減らすことを検討してください。 詳しくは、「 [アプリケーション層のキャッシュ](#application-tier-caching)」を参照してください。

## <a name="tune-your-database"></a>データベースの調整

このセクションでは、データベースを調整するいくつかの手法について説明します。これらの手法を使用すると、アプリケーションから最良のパフォーマンスを引き出し、可能な限り下位のコンピューティング サイズでアプリケーションを実行することができます。 これらの手法の一部は従来の SQL Server 調整のベスト プラクティスと同じですが、その他のものは Azure SQL Database および Azure SQL Managed Instance に固有です。 場合によっては、データベースで使用されるリソースを調べ、さらに調整すべき領域を見つけることができるほか、従来の SQL Server 手法を拡大し、Azure SQL Database および Azure SQL Managed Instance に応用することができます。

### <a name="identifying-and-adding-missing-indexes"></a>不足しているインデックスの識別と追加

OLTP データベースのパフォーマンスの一般的問題は物理的なデータベース設計に関連します。 多くの場合、データベース スキーマは (負荷またはデータ量の) 規模の面で試験することなく設計され、出荷されます。 残念ながら、クエリ プランのパフォーマンスは、規模が小さい場合には許容されることがあるものの、実稼働レベルのデータ量では大幅に低下する可能性があります。 この問題の最も一般的な原因は、適切なインデックスがなく、クエリのフィルターまたはその他の制約を満たせないことにあります。 多くの場合、インデックスがないと、インデックス シークで足りるときにテーブル スキャンが行われます。

次の例では、シークで足りるときに、選択したクエリ プランでスキャンが使用されます。

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![A query plan with missing indexes](./media/performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database および Azure SQL Managed Instance は、インデックス不足の一般的な状態を発見して修正するのに役立ちます。 Azure SQL Database および Azure SQL Managed Instance に組み込まれている DMV には、クエリ コンパイルが表示されます。クエリを実行するために見積もられたコストをインデックスで大幅に削減できる場合があります。 クエリの実行中、データベース エンジンによって、各クエリ プランが実行される頻度と、実行クエリ プランとそのインデックスが存在した想定クエリ プランの間で見積もられるギャップが追跡されます。 これらの DMV を使用し、データベースとその実際のワークロードに関してワークロード コストを全体的に改善できる物理データベース設計の変更をすばやく推測できます。

次のクエリは、潜在的なインデックス不足の評価に使用できます。

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

この例では、クエリの結果として次が推奨されました。

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

作成後、同じ SELECT ステートメントを実行すると、異なるプランが選択されます。スキャンではなくシークが使用され、プランがより効率的に実行されます。

![A query plan with corrected indexes](./media/performance-guidance/query_plan_corrected_indexes.png)

重要なことは、共有される汎用システムの IO 容量は専用サーバー コンピューターの IO 容量より限られているということです。 不必要な IO を最小限に抑え、サービス レベルの各コンピューティング サイズのリソース内でシステムを最大限に活用することが重要です。 適切な物理データベース設計を選択すると、個々のクエリの待機時間のほか、スケール ユニットごとに処理される同時要求のスループットを大幅に改善し、クエリを満たすために必要なコストを最小限に抑えることができます。 インデックス不足の DMV に関する詳細については、「[sys.dm_db_missing_index_details](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql)」を参照してください。

### <a name="query-tuning-and-hinting"></a>クエリの調整とヒント

Azure SQL Database および Azure SQL Managed Instance のクエリ オプティマイザーは、従来の SQL Server クエリ オプティマイザーと似ています。 クエリを調整し、クエリ オプティマイザーの推論モデル制約を理解するためのベスト プラクティスのほとんどは、Azure SQL Database および Azure SQL Managed Instance にも活かすことができます。 Azure SQL Database および Azure SQL Managed Instance のクエリを調整すると、総リソース要求を減らせる場合があります。 下位のコンピューティング サイズで実行できるため、クエリが調整されていない場合に比べて少ないコストでアプリケーションを実行できます。

SQL Server でよく見られ Azure SQL Database および Azure SQL Managed Instance にも適用される例は、クエリ オプティマイザーによるパラメーターの "スニッフィング" です。 コンパイル中、クエリ オプティマイザーによってパラメーターの現在の値が評価され、より最適なクエリ プランを生成できるかどうかが判断されます。 この戦略を使用すると多くの場合、既知のパラメーター値を使用せずにコンパイルされたプランよりもはるかに速いクエリ プランが生成されます。ただし現時点では、SQL Server、Azure SQL Database、および Azure SQL Managed Instance で動作が不完全です。 パラメーターがスニッフィングされなかったり、パラメーターがスニッフィングされたものの、生成されたプランがワークロードのすべてのパラメーター値に関して最適でなかったりする場合があります。 意図をより慎重に指定し、パラメーター スニッフィングの既定の動作をオーバーライドできるように、Microsoft はクエリ ヒント (ディレクティブ) を追加しています。 多くの場合、ヒントを使用すると、SQL Server、Azure SQL Database、および Azure SQL Managed Instance の既定の動作で特定のユーザーのワークロードに完全に対応できない問題を修正できます。

次の例は、パフォーマンスとリソースの両方の要件について最適でないプランがクエリ プロセッサによって生成されるようすを示しています。 この例から、クエリ ヒントを使用すると、データベースのクエリの実行時間とリソース要件を抑えることができることもわかります。

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

このセットアップ コードによって、傾斜データ分布が含まれたテーブルが作成されます。 最適なクエリ プランは、選択されたパラメーターによって異なります。 残念ながらプラン キャッシング動作では、常に最も一般的なパラメーター値に基づいてクエリが再コンパイルされるとは限りません。 そのため、平均すると別のプランの方がプランとしてより良い選択になる場合でも、最適でないプランがキャッシュされ、多くの値に使用される可能性があります。 次に、(一方に特殊なクエリ ヒントが含まれていることを除いて) 同一の 2 つのストアド プロシージャがクエリ プランによって作成されます。

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

例のパート 2 を開始する前に少なくとも 10 分待つことをお勧めします。これにより、生成されるテレメトリ データの結果の差異がはっきりします。

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

この例の各パートでは、(テスト データ セットとして使用するのに十分な負荷を生成するために) パラメーター化された挿入ステートメントが 1,000 回試行されます。 ストアド プロシージャを実行するとき、クエリ プロセッサは、その最初のコンパイル中にプロシージャに渡されるパラメーター値を調べます (パラメーターの "スニッフィング")。 結果として生成されたプランがプロセッサによってキャッシュされ、パラメーター値が異なる場合でも、後の呼び出しで使用されます。 最適なプランが使用されないことがあります。 クエリが最初にコンパイルされたときのケースではなく、平均的なケースに対して最適なプランを選択するように、オプティマイザーを調整する必要がある場合があります。 この例では、最初のプランは、パラメーターに一致する各値を見つけるためにすべての行を読み取る "スキャン" プランを生成します。

![Query tuning by using a scan plan](./media/performance-guidance/query_tuning_1.png)

値 1 を使用してプロシージャを実行したため、結果として生成されたプランは値 1 に対して最適ですが、テーブルにある他のすべての値に対しては最適ではありません。 各プランを無作為に選択した場合、結果は望んだものと異なることが予想されます。これは、プランの実行が遅く、より多くのリソースが使用されるためです。

`SET STATISTICS IO` を `ON` に設定してテストを実行すると、この例の論理スキャン作業がバックグラウンドで行われます。 このプランによって 1,148 件の読み取りが行われたことがわかります (平均的なケースで返される行がたった 1 つの場合、非効率的です)。

![Query tuning by using a logical scan](./media/performance-guidance/query_tuning_2.png)

例の 2 つ目の部分では、クエリ ヒントを利用し、コンパイル プロセス中に特定の値を使用するようにオプティマイザーに伝えます。 この場合、パラメーターとして渡される値を無視し、`UNKNOWN` を想定するようにクエリ プロセッサに強制します。 これはテーブル内での頻度が平均的な値を示します (傾斜を無視)。 結果として生成されるプランはシークベースのプランです。このプランはこの例のパート 1 のプランより全体的に高速であり、このプランで使用されるリソースもパート 1 のプランより全体的に少なくなっています。

![Query tuning by using a query hint](./media/performance-guidance/query_tuning_3.png)

**sys.resource_stats** テーブルで影響を確認できます (テストを実行してからデータがテーブルに入力されるまでの間に遅延があります)。 この例では、パート 1 は 22:25:00 の時間枠で実行され、パート 2 は 22:35:00 の時間枠で実行されています。 遅い方の時間枠と比べ、早い方の時間枠でその時間枠のリソースがより多く使用されます (プランの効率性改善が理由)。

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Query tuning example results](./media/performance-guidance/query_tuning_4.png)

> [!NOTE]
> この例では規模を意図的に小さくしてありますが、最適でないパラメーターの影響は特に大規模なデータベースで顕著になります。 極端なケースでは、速い場合は数秒単位、遅い場合は数時間単位の差異になります。

**sys.resource_stats** を調べると、あるテストで使用されるリソースが別のテストより多いか少ないか判断できます。 データを比較するとき、**sys.resource_stats** ビューで同じ 5 分の枠に入らないようにテストのタイミングを離します。 この演習の目標は、使用されるリソースの総量を最小限に抑えることであり、ピーク リソースを最小限に抑えることではありません。 一般的に、待ち時間のコードの一部を最適化すると、リソースの消費量も減ります。 アプリケーションに施す変更が必要なものであることと、アプリケーションでクエリ ヒントを使用している他のユーザーのカスタマー エクスペリエンスに対し変更による悪影響がないことを確認してください。

ワークロードに一連の反復的なクエリが含まれる場合は、データベースをホストするために必要な最小リソース サイズ単位を把握できるため、たいてい、プラン選択肢の最適性を理解して検証することは合理的です。 検証した後、プランのパフォーマンスが低くなっていないことを確認するために、ときどきプランを調べ直してください。 詳細については、「 [クエリ ヒント (Transact-SQL)](/sql/t-sql/queries/hints-transact-sql-query)」をご覧ください。

### <a name="very-large-database-architectures"></a>非常に大規模なデータベースのアーキテクチャ

Azure SQL Database で単一データベース向けに[ハイパースケール](service-tier-hyperscale.md) サービス レベルが始まる前は、顧客が個々のデータベースで容量上限に達することがよくありました。 そのような容量上限は、Azure SQL Database エラスティック プールのプールされたデータベースや Azure SQL Managed Instance のインスタンス データベースに依然として存在します。 次の 2 つのセクションでは、ハイパースケール サービス レベルを利用できないとき、Azure SQL Database および Azure SQL Managed Instance で非常に大規模なデータベースに発生する問題を解決するための 2 つのオプションについて説明します。

### <a name="cross-database-sharding"></a>データベース間のシャーディング

Azure SQL Database および Azure SQL Managed Instance は汎用ハードウェアで実行されるため、従来のオンプレミス SQL Server インストールと比べ、1 つのデータベースに対する容量制限が低くなります。 データベース操作が Azure SQL Database および Azure SQL Managed Instance の 1 つのデータベースの制限内に収まらないときにシャーディング手法を使用して、複数のデータベースに操作を分散しているユーザーもいます。 Azure SQL Database および Azure SQL Managed Instance でシャーディング手法を利用するほとんどのユーザーは、1 つのディメンションのデータを複数のデータベースで分割します。 この手法では、OLTP アプリケーションは多くの場合、スキーマ内の 1 行のみ、またはほんの数行から成るグループに適用されるトランザクションを実行することを理解しておく必要があります。

> [!NOTE]
> Azure SQL Database にシャーディングを支援するライブラリが追加されました。 詳細については、「 [エラスティック データベース クライアント ライブラリの概要](elastic-database-client-library.md)」をご覧ください。

たとえば、(SQL Server 付属の従来のサンプル Northwind データベースのように) あるデータベースに顧客名、注文、注文明細が含まれている場合、関連する注文と注文明細の情報を使って顧客をグループ化することで、このデータを複数のデータベースに分割できます。 顧客のデータは 1 つのデータベース内にとどめておくことができます。 アプリケーションはデータベース間で顧客を分割し、効果的に負荷を分散します。 シャーディングを使用すると、顧客がデータベース サイズの上限を回避できるだけでなく、個々のデータベースがその DTU に収まる限り、各コンピューティング サイズの制限を大幅に超えるワークロードを Azure SQL Database および Azure SQL Managed Instance で処理できます。

データベース シャーディングではソリューションの総リソース容量を減らすことはできませんが、複数のデータベースにまたがる非常に大規模なソリューションに対応する際に非常に効果的です。 各データベースを異なるコンピューティング サイズで実行し、リソース要件の高い、非常に大規模で "効果的な" データベースに対応できます。

#### <a name="functional-partitioning"></a>機能的パーティション分割

ユーザーは多くの場合、1 つのデータベースのさまざまな機能を組み合わせます。 たとえば、店舗の在庫を管理するロジックがアプリケーションに含まれている場合、そのデータベースには、在庫に関連付けられているロジック、購買発注の追跡、ストアド プロシージャ、月末報告を管理するインデックス付きビュー/マテリアライズドビューが含まれていることがあります。 この手法では、バックアップなどの操作に関するデータベースの管理が容易になりますが、アプリケーションの機能全体でピーク負荷を処理できるようにハードウェアのサイズを調整する必要もあります。

Azure SQL Database および Azure SQL Managed Instance 内でスケールアウト アーキテクチャを使用する場合、アプリケーションの異なる機能を異なるデータベースに分割することをお勧めします。 この手法を使用すると、各アプリケーションは独立してスケールされます。 管理者は、アプリケーションがビジー状態になった (データベースの負荷が増えた) ときに、アプリケーションの機能ごとにコンピューティング サイズを個別に選択できます。 制限はありますが、このアーキテクチャを使用して、1 台の汎用コンピューターで処理できる範囲を超えてアプリケーションの規模を大きくできます。これは、複数のコンピューター間で負荷が分散されるためです。

### <a name="batch-queries"></a>バッチ クエリ

大量のアドホック クエリを頻繁に実行してデータにアクセスするアプリケーションの場合、アプリケーション層とデータベース層の間で行われるネットワーク通信の応答に、相当な時間が費やされます。 アプリケーションとデータベースが両方同じデータ センターに存在する場合でも、データ アクセス操作の数が多ければ、この 2 つの間のネットワーク待機時間は長くなる可能性があります。 データ アクセス操作のネットワーク ラウンド トリップを減らすために、アドホック クエリを一括処理すること、またはストアド プロシージャとしてそれらをコンパイルすることを検討してください。 アドホック クエリを一括処理すると、複数のクエリを 1 つの大きなバッチとして 1 回のトリップでデータベースに送信できます。 アドホック クエリをストアド プロシージャにコンパイルすると、それらを一括処理した場合と同じ結果が得られます。 ストアド プロシージャを使用すると、クエリ プランがデータベースにキャッシュされる機会が増えるという利点もあるため、ストアド プロシージャを再度使用できます。

一部のアプリケーションでは、書き込みが集中します。 場合によっては、書き込みを一括処理する方法を検討することで、データベースの IO 総負荷を減らすことができます。 これは多くの場合、ストアド プロシージャとアドホック バッチ内で自動コミット トランザクションではなく明示的なトランザクションを使用するのと同じくらい単純です。 使用できるさまざまな手法の評価については、 [Azure でのデータベース アプリケーションのバッチ処理手法](../performance-improve-use-batching.md)に関するページをご覧ください。 独自のワークロードで実験を行って、一括処理に適したモデルを見つけてください。 モデルによってはトランザクションの整合性の保証がわずかに異なる場合があることを理解しておいてください。 リソース使用を最小限に抑える適切なワークロードを見つけるには、整合性とパフォーマンスの適度なバランスを見つける必要があります。

### <a name="application-tier-caching"></a>アプリケーション層のキャッシュ

一部のデータベース アプリケーションでは、ワークロードの大半が読み取りになります。 キャッシュ層を利用すれば、データベースの負荷を減らすことができます。また、Azure SQL Database および Azure SQL Managed Instance を使用してデータベースをサポートするために必要なコンピューティング サイズを下げられる可能性があります。 [Azure Cache for Redis](https://azure.microsoft.com/services/cache/) を利用すると、読み取りが多いワークロードがある場合に、データを 1 回 (または、構成方法に応じてアプリケーション層コンピューターごとに 1 回) 読み込んでから、データベースの外部にそのデータを格納することができます。 この方法は、データベースの負荷 (CPU と読み取り IO) を減らすことができるものの、トランザクションの整合性に影響があります。データがキャッシュから読み込まれると、データベースのデータとの同期が失われることがあるためです。 多くのアプリケーションではある程度の不整合が許容されますが、すべてのワークロードで許容されるとは限りません。 アプリケーション層のキャッシュ手法を実装する前に、あらゆるアプリケーション要件を完全に理解しておく必要があります。

## <a name="next-steps"></a>次のステップ

- DTU ベースのサービス レベルの詳細については、[DTU ベースの購入モデル](service-tiers-dtu.md)に関するページを参照してください。
- 仮想コアベースのサービス レベルの詳細については、[仮想コアベースの購入モデル](service-tiers-vcore.md)に関するページを参照してください。
- エラスティック プールの詳細については、「[Azure エラスティック プールの概要](elastic-pool-overview.md)」を参照してください。
- パフォーマンスとエラスティック プールの詳細については、[エラスティック プールの使用を検討する場合](elastic-pool-overview.md)に関するページを参照してください。