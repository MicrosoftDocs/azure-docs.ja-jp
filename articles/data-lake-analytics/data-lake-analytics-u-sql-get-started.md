---
title: "チュートリアル: Azure Data Lake Analytics U-SQL 言語の使用 | Microsoft Docs"
description: "このチュートリアルを使用して、Azure Data Lake Analytics U-SQL 言語について説明します。"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: dc8c7beaf5b8e8d4f5467ffe22390c41f446d787
ms.lasthandoff: 03/04/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>チュートリアル: Azure Data Lake Analytics U-SQL 言語の使用
U-SQL は、SQL のメリットと、任意の規模でデータを処理する独自のコードの表現力を組み合わせた言語です。 U-SQL のスケーラブルな分散クエリ機能を使用することで、Azure SQL Database などのリレーショナル ストアのデータを効率的に分析できます。 U-SQL では、読み取り時にスキーマを適用し、カスタム ロジックと UDF を挿入することで、非構造化データを処理できます。 また、U-SQL には、スケールを実行する方法をきめ細かく制御する拡張性があります。 U-SQL の背後にあるデザインの考え方の詳細については、Visual Studio ブログの投稿「[Introducing U-SQL – A Language that makes Big Data Processing Easy (U-SQL 紹介 – ビッグ データ処理を容易にする言語)](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)」を参照してください。

U-SQL は、ANSI SQL や T-SQL とはいくつかの点で異なります。 たとえば、SELECT などのキーワードは、すべて大文字でなければなりません。

 SELECT 句、WHERE 述語などの型システムと式言語は C# です。 これは、データ型が C# 型であり、C# NULL セマンティクスを使用し、述語内の比較演算子が C# 構文に従うことを意味します (たとえば、a == "foo")。 また、値が完全な .NET オブジェクトであるため、任意のメソッドを使用してオブジェクトを簡単に操作できることも意味します (たとえば、"f o o o".Split(' '))。

U-SQL に関する詳しい情報については、「[U-SQL Language Reference (U-SQL 言語のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=691348)」を参照してください。

### <a name="prerequisites"></a>前提条件
「[チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をまだ完了していない場合は、こちらを先に完了してください。 チュートリアルを完了した後で、この記事に戻ってください。

このチュートリアルでは、次の U-SQL スクリプトを使用して Azure Data Lake Analytics ジョブを実行しました。

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

このスクリプトに変換手順は含まれていません。 SearchLog.tsv というソース ファイルから読み取り、体系化して、SearchLog-first-u-sql.csv というファイルに行セットを書き込み直します。

**Duration** フィールドのデータ型の横にある疑問符に注目してください。 これは、**Duration** フィールドを null にできることを意味します。

スクリプトでは、次の概念とキーワードを確認できます。

* 行セットの変数: 行セットを生成する各クエリ式を変数に割り当てることができます。 U-SQL は、スクリプトでは T-SQL 変数命名パターンに従います (例: @searchlog)。

 >[!NOTE]
 >この割り当ては実行を強制するものではありません。 単に式に名前を付けるだけなので、より複雑な式を構築することができます。
* EXTRACT: このキーワードを使用すると、読み取り時のスキーマを定義できます。 スキーマは、列名と列ごとの C# 型名のペアで指定できます。 スキーマは、いわゆるエクストラクター (たとえば、Extractors.Tsv()) を使用して、.tsv ファイルを抽出します。 カスタムのエクストラクターを開発することができます。
* OUTPUT: このキーワードでは行セットを使用し、それをシリアル化します。 Outputters.Csv() は、指定した場所にコンマ区切りファイルを書き込みます。 また、カスタムのアウトプッターを作成することもできます。

 >[!NOTE]
 >2 つのパスは相対パスです。 絶対パスを使用することもできます。 次に例を示します。    
 >     adl://\<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
 >
 >リンクされたストレージ アカウント内のファイルへのアクセスには、絶対パスを使用する必要があります。  リンクされた Azure ストレージ アカウントに格納されたファイルの構文は次のとおりです。wasb://\<BlobContainerName>@\<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

 >[!NOTE]
 >パブリック BLOB またはパブリック コンテナーのアクセス許可を持つ Azure BLOB ストレージ コンテナーは、現在サポートされていません。

## <a name="use-scalar-variables"></a>スカラー変数の使用
スカラー変数も使用して、スクリプトのメンテナンスを容易にすることができます。 次のように、前の U-SQL スクリプトを記述することもできます。

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>行セットの変換
以下のように、 **SELECT** を使用して行セットを変換します。

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

WHERE 句では [C# ブール式](https://msdn.microsoft.com/library/6a71f45d.aspx)を使用します。 C# 式言語を使用して、独自の式と関数を実行することができます。 論理積 (AND) および論理和 (OR) と組み合わせることによって、より複雑なフィルター処理を実行することもできます。

次のスクリプトでは、DateTime.Parse() メソッドと論理積を使用します。

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >2 番目のクエリは最初の行セットの結果で動作します。結果は&2; つのフィルターを組み合わたものです。 また、変数名を再利用することもできます。その場合、名前は字句単位でスコープされます。

## <a name="aggregate-rowsets"></a>行セットの集計
U-SQL では、使い慣れた ORDER BY、GROUP BY および集計が提供されます。

次のクエリでは、リージョンごとの合計期間を検索してから、上位&5; つの期間を順に表示します。

次のクエリの場合、U-SQL 行セットの順序は保持されません。 そのため、出力を順序付けるには、次のように OUTPUT ステートメントに ORDER BY を追加する必要があります。

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

U-SQL の ORDER BY 句は、SELECT 式では FETCH 句と組み合わせる必要があります。

以下のように、U-SQL の HAVING 句を使用して、HAVING 条件を満たすグループに出力を制限することができます。

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>データの結合
U-SQL ではテーブルだけでなく、すべての行セット (ファイルから生成されたものでも) を結合するために、INNER JOIN、LEFT/RIGHT/FULL OUTER JOIN、SEMI JOIN などの一般的な結合演算子が提供されます。

次のスクリプトでは、検索ログと広告インプレッション ログを結合し、指定日のクエリ文字列の広告を示します。

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL でサポートされるのは、ANSI 準拠の結合構文である Rowset1 JOIN Rowset2 ON 述語のみです。 FROM Rowset1, Rowset2 WHERE 述語の古い構文はサポートされて_いません_。
JOIN の述語は、式のない等価結合である必要があります。 式を使用する場合は、前述の行セットの select 句に追加します。 別の比較を実行する場合は、WHERE 句に移動できます。

## <a name="create-databases-table-valued-functions-views-and-tables"></a>データベース、テーブル値関数、ビュー、およびテーブルの作成
U-SQL では、データベースとスキーマのコンテキストでデータを使用することができ、常にファイルから読み取りまたはファイルに書き込む必要はありません。

すべての U-SQL スクリプトが、その既定のコンテキストとして、既定のデータベース (master) と既定のスキーマ (DBO) で実行されます。 独自のデータベースやスキーマを作成することができます。 コンテキストを変更する場合は、USE ステートメントを使用します。

### <a name="create-a-tvf"></a>TVF の作成
前述の U-SQL スクリプトでは、同じソース ファイルからの読み取りで EXTRACT の使用を繰り返しました。 U-SQL のテーブル値関数 (TVF) では、後で再利用するためにデータをカプセル化することができます。  

次のスクリプトでは、既定のデータベースとスキーマで *Searchlog()* という TVF を作成します。

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

次のスクリプトは、前述のスクリプトで定義された TVF を使用する方法を示しています。

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>ビューの作成
抽象化するものの、パラメーターを作成しないクエリ式が&1; つだけある場合は、テーブル値関数ではなく、ビューを作成できます。

次のスクリプトでは、既定のデータベースとスキーマで *SearchlogView* というビューを作成します。

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

次のスクリプトは定義されたビューの使用方法を示しています。

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>テーブルの作成
リレーショナル データベースのテーブルと同様に、U-SQL では、定義済みのスキーマでテーブルを作成したり、テーブルを作成して、そのテーブルを設定するクエリからスキーマを推測したりすることができます (CREATE TABLE AS SELECT または CTAS ともいう)。

次のスクリプトを使用して、1 つのデータベースと&2; つのテーブルを作成します。

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SearchLogDb;
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    DISTRIBUTED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>テーブルの照会
データ ファイルの照会と同じ方法で、前述のスクリプトで作成されたテーブルのようなテーブルを照会できます。 EXTRACT を使用して行セットを作成する代わりに、テーブル名を参照できるようになりました。

テーブルから読み取る場合、前に使用した変換スクリプトは以下のように変更されます。

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

 >[!NOTE]
 >現時点では、テーブルを作成する場合と同じスクリプトで、テーブルに対して SELECT を実行することはできません。

## <a name="conclusion"></a>まとめ
このチュートリアルでは、U-SQL のほんの一部を説明しました。 スコープが限られているため、チュートリアルでは、その他の U-SQL の多くの利点は説明されていません。 たとえば、次のようなことができます。

* CROSS APPLY を使用して、文字列、配列、およびマップの部分を行にアンパックする。
* データのパーティション セット (ファイル セットおよびパーティション テーブル) を操作する。
* エクストラクター、アウトプッター、プロセッサー、ユーザー定義のアグリゲーターなどのユーザー定義演算子を C# で開発する。
* U-SQL ウィンドウ化関数を使用する。
* U-SQL コードをビュー、テーブル値関数およびストアド プロシージャで管理する。
* 処理ノードで任意のカスタム コードを実行する。
* SQL Database に接続し、それらと U-SQL および Azure Data Lake データ間のクエリを統合する。

## <a name="see-also"></a>関連項目
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics ジョブに U-SQL ウインドウ関数を使用する](data-lake-analytics-use-window-functions.md)
* [Azure ポータルを使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>意見の投稿
* [機能要求の送信](http://aka.ms/adlafeedback)
* [フォーラムでサポートを受ける](http://aka.ms/adlaforums)
* [U-SQL に関するフィードバックの提供](http://aka.ms/usqldiscuss)

