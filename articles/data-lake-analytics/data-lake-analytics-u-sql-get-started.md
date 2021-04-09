---
title: Azure Data Lake Analytics の U-SQL 言語を使用する
description: Azure Data Lake Analytics の U-SQL 言語の基本情報を説明します。 変数を利用して最初のクエリを記述し、ファイルからデータを抽出し、行セットを変換し、データを集計します。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/23/2017
ms.openlocfilehash: 5920f2b5823e568d56d13107200eb0025a654db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219923"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics の U-SQL を使用する

U-SQL は、あらゆる規模のデータの処理を可能にするために、宣言型の SQL と命令型の C# を組み合わせた言語です。 U-SQL のスケーラブルな分散クエリ機能を使用することで、Azure SQL Database などのリレーショナル ストアのデータを効率的に分析できます。 U-SQL では、読み取り時にスキーマを適用し、カスタム ロジックと UDF を挿入することで、非構造化データを処理できます。 また、U-SQL には、スケールを実行する方法をきめ細かく制御する拡張性があります。

## <a name="learning-resources"></a>学習用のリソース

* 「[U-SQL チュートリアル](https://aka.ms/usqltutorial)」では、ほとんどの U-SQL 言語のガイド付きチュートリアルを提供しています。 U-SQL について学習するすべての開発者には、このドキュメントをご覧になることをお勧めします。
* **U-SQL 言語構文** の詳細については、「[U-SQL Language Reference](/u-sql/)」(U-SQL 言語のリファレンス) を参照してください。
* **U-SQL のデザインの考え方** について理解するには、Visual Studio ブログの投稿「[Introducing U-SQL – A Language that makes Big Data Processing Easy](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)」(U-SQL 紹介 – ビッグ データ処理を容易にする言語)を参照してください。

## <a name="prerequisites"></a>前提条件

このドキュメントの U-SQL のサンプルをご覧になる前に、「[チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」を読み、完了してください。 このチュートリアルでは、Azure Data Lake Tools for Visual Studio での U-SQL の使用のメカニズムについて説明しています。

## <a name="your-first-u-sql-script"></a>最初の U-SQL スクリプト

以下の U-SQL スクリプトは単純で、U-SQL 言語のさまざまな側面を確認することができます。

```usql
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
```

このスクリプトに変換手順は含まれていません。 `SearchLog.tsv` というソース ファイルから読み取り、体系化して、SearchLog-first-u-sql.csv というファイルに行セットを書き込み直します。

`Duration` フィールドのデータ型の横にある疑問符に注目してください。 これは、`Duration` フィールドを null にできることを意味します。

### <a name="key-concepts"></a>主要な概念

* **行セットの変数**: 行セットを生成する各クエリ式を変数に割り当てることができます。 U-SQL は、スクリプトでは T-SQL 変数命名パターンに従います (例: `@searchlog`)。
* **EXTRACT** のキーワードは、データをファイルから読み取り、読み取り時にスキーマを定義します。 `Extractors.Tsv` はタブ区切り値ファイルのための、組み込みの U-SQL エクストラクターです。 カスタムのエクストラクターを開発することができます。
* **OUTPUT** は、行セットからファイルにデータを書き込みます。 `Outputters.Csv()` は、コンマ区切り値ファイルを作成するための、組み込みの U-SQL アウトプッターです。 カスタムのアウトプッターを作成することができます。

### <a name="file-paths"></a>ファイル パス

EXTRACT と OUTPUT のステートメントでは、ファイル パスを使用します。 ファイルは、絶対パスまたは相対パスで指定できます。

次の絶対ファイル パスは、`mystore` という名前の Data Lake Store のファイルを指しています。

```usql
adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv
```

次のファイル パスは、`"/"` で始まっています。 既定の Data Lake Store アカウントのファイルを指しています。

```usql
/output/SearchLog-first-u-sql.csv
```

## <a name="use-scalar-variables"></a>スカラー変数の使用

スカラー変数も使用して、スクリプトのメンテナンスを容易にすることができます。 次のように、前の U-SQL スクリプトを記述することもできます。

```usql
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
```

## <a name="transform-rowsets"></a>行セットの変換

以下のように、 **SELECT** を使用して行セットを変換します。

```usql
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
```

WHERE 句では [C# ブール式](/dotnet/csharp/language-reference/operators/index)を使用します。 C# 式言語を使用して、独自の式と関数を実行することができます。 論理積 (AND) および論理和 (OR) と組み合わせることによって、より複雑なフィルター処理を実行することもできます。

次のスクリプトでは、DateTime.Parse() メソッドと論理積を使用します。

```usql
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
    TO "/output/SearchLog-transform-datetime.csv"
    USING Outputters.Csv();
```

 >[!NOTE]
 >2 番目のクエリは最初の行セットの結果で動作します。結果は 2 つのフィルターを組み合わたものです。 また、変数名を再利用することもできます。その場合、名前は字句単位でスコープされます。

## <a name="aggregate-rowsets"></a>行セットの集計

U-SQL では、使い慣れた ORDER BY、GROUP BY および集計が提供されます。

次のクエリでは、リージョンごとの合計期間を検索してから、上位 5 つの期間を順に表示します。

次のクエリの場合、U-SQL 行セットの順序は保持されません。 そのため、出力を順序付けるには、次のように OUTPUT ステートメントに ORDER BY を追加する必要があります。

```usql
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
```

U-SQL の ORDER BY 句では、SELECT 式で FETCH 句と組み合わせる必要があります。

以下のように、U-SQL の HAVING 句を使用して、HAVING 条件を満たすグループに出力を制限することができます。

```usql
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
```

高度な集計シナリオについては、U-SQL リファレンス ドキュメントで[集計、分析、参照の各関数](/u-sql/built-in-functions)をご確認ください

## <a name="next-steps"></a>次のステップ

* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)