---
title: Azure Data Lake の U-SQL プログラミング ガイド
description: Azure Data Lake Analytics での U-SQL の概要、および優れた USQL スクリプトを作成できる UDF のプログラミングについて説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96510864"
---
# <a name="u-sql-programmability-guide-overview"></a>U-SQL プログラミング ガイドの概要

U-SQL は、ビッグ データに該当するワークロードの処理を目的として設計されたクエリ言語です。 U-SQL 独自の特徴の 1 つに、SQL によく似た宣言型言語でありながら、C# の拡張性とプログラミング機能をも兼ね備えていることが挙げられます。 このガイドでは、C# によって実現される U-SQL 言語の拡張性とプログラミング機能について説明します。

## <a name="requirements"></a>必要条件

[Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) のダウンロードとインストール

## <a name="get-started-with-u-sql"></a>U-SQL を使ってみる  

次の U-SQL スクリプトを見てみましょう。

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

このスクリプトは、`@a` と `@results` の 2 つの行セットを定義します。 行セット `@results` は `@a` から定義されます。

## <a name="c-types-and-expressions-in-u-sql-script"></a>U-SQL スクリプトで C# の型と式を使用する

U-SQL 式は、`AND`、`OR`、`NOT` などの U-SQL 論理操作と組み合わされた C# の式です。 U-SQL 式は、SELECT、EXTRACT、WHERE、HAVING、GROUP BY、DECLARE と共に使用できます。 たとえば、次のスクリプトでは、文字列を DateTime 値として解析します。

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

次のスニペットでは、DECLARE ステートメントで文字列を DateTime 値として解析します。

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>C# の式を使ってデータ型を変換する

以下の例は、C# の式を使って日時のデータを変換する方法を示しています。 このシナリオでは、文字列の日時データを、時間の表記が真夜中の 00:00:00 となっている標準的な日時データに変換しています。

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>C# の式を使って今日の日付を取得する

今日の日付を取得するには、C# の式 `DateTime.Now.ToString("M/d/yyyy")` を使用します。

この式をスクリプトで使う方法の例を以下に示します。

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>.NET アセンブリの使用

U-SQL の拡張モデルは、.NET アセンブリからカスタム コードをどの程度追加できるかに大きく左右されます。 

### <a name="register-a-net-assembly"></a>.NET アセンブリの登録

U-SQL データベースに .NET アセンブリを配置するには、`CREATE ASSEMBLY` ステートメントを使用します。 後ほど、U-SQL スクリプトは `REFERENCE ASSEMBLY` ステートメントを使用してそれらのアセンブリを使用できます。 

次のコードは、アセンブリの登録方法を示しています。

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

次のコードは、アセンブリの参照方法を示しています。

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

[アセンブリの登録手順](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog)に関する記事をご覧ください。このトピックについて詳しく説明しています。


### <a name="use-assembly-versioning"></a>アセンブリのバージョン管理を使用する
U-SQL では現在、.NET Framework 4.7.2 を使っています。 このため、独自のアセンブリがこのバージョンのランタイムと互換性があることをご確認ください。

前に説明したとおり、U-SQL ではコードを 64 ビット (x64) 形式で実行します。 このため、コードは x64 形式で実行されるようにコンパイルしてください。 そうしないと、前に示したフォーマットの誤りに関するエラーが表示されます。

アセンブリ DLL とリソース ファイル (別のランタイム、ネイティブ アセンブリ、構成ファイルなど) は、それぞれ最大 400 MB までアップロードできます。 また、DEPLOY RESOURCE またはアセンブリとその追加ファイルの参照によりデプロイしたリソースのサイズは、合計 3 GB を超えることはできません。

最後に、各 U-SQL データベースに格納できるアセンブリのバージョンは 1 つだけであるという点にご注意ください。 たとえば、NewtonSoft Json.NET ライブラリのバージョン 7 とバージョン 8 の両方が必要な場合には、それぞれを異なる 2 つのデータベースに登録する必要があります。 さらに、各スクリプトで参照できるアセンブリ DLL のバージョンも 1 つのみです。 U-SQL はこの点で、C# のアセンブリ管理とバージョン管理の規則に従っています。

## <a name="use-user-defined-functions-udf"></a>ユーザー定義関数を使用する: UDF
U-SQL のユーザー定義関数 (UDF) は、パラメーターを受け取り、複雑な計算などのアクションを実行し、そのアクションの結果を値として返すプログラミング ルーチンです。 UDF の戻り値とすることができるのは、単一のスカラー値のみです。 U-SQL の UDF は、C# の他のスカラー関数と同じく、U-SQL のベース スクリプトから呼び出すことができます。

U-SQL のユーザー定義関数は、**public** と **static** で初期化することをお勧めします。

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

まずは、簡単な UDF を作成する例を見てみましょう。

このユース ケース シナリオでは、特定のユーザーが初めてサインインした会計期間 (会計上の四半期と月を含む) を特定する必要があります。 このシナリオでは、会計年度の最初の月を 6 月とします。

会計期間を計算するにあたっては、以下の C# の関数を導入します。

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

この関数は会計上の四半期と月を計算し、文字列の値として返します。 6 月であれば、第 1 四半期の第 1 月に当たるため、"Q1:P1" を使用します。 同様に、7 月であれば "Q1:P2" を使用します。

これが、U-SQL プロジェクトで使う通常の C# 関数です。

このシナリオでは、コードビハインドのセクションを以下のようにします。

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

それでは、U-SQL のベース スクリプトからこの関数を呼び出してみましょう。 そのためには、名前空間も含め、関数の完全修飾名を指定する必要があります。この場合は、NameSpace.Class.Function(parameter) となります。
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

以下は、U-SQL の実際のベース スクリプトを示したものです。
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

スクリプトを実行した結果として出力されるファイルは、以下のとおりです。

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

この例では、U-SQL でインライン UDF を使用する方法を簡単に紹介しました。

### <a name="keep-state-between-udf-invocations"></a>各 UDF 呼び出しの間で状態を維持する
U-SQL の C# プログラミング オブジェクトは、コードビハインドのグローバル変数を使えば、対話機能を備えたさらに高度なものにすることができます。 ここでは、ビジネスの場でのユース ケース シナリオを見てみましょう。

大規模な組織ではさまざまなアプリケーション (Microsoft Dynamics CRM、Power BI など) が採用されており、ユーザーはそれらを切り替えながら使います。 このため、顧客企業ではユーザーがどのような状況でアプリケーションを切り替えているかや、使用状況の傾向などについて、テレメトリ分析を実施したいという要望が生じる場合があります。 ビジネスの目標は、アプリケーションの使用状況を最適化することにあります。 そのためには、さまざまなアプリケーションや特定のサインオン ルーチンを組み合わせることも必要になる可能性があります。

この目標を達成するには、セッション ID と、前回発生したセッションとの間のラグ タイム (時間差) を決める必要があります。

前回のサインインを探し、同じアプリケーションに対して生成されるセッションすべてに、このサインインを割り当てます。 最初の問題は、U-SQL のベース スクリプトでは LAG 関数で既に計算が済んでいる列に対してもう一度計算を適用することができないという点です。 2 番目の問題は、同一の期間内に発生するセッションすべてについて、一定のセッションを保持する必要があるという点です。

この問題を解決するために、コードビハインド セクションにグローバル変数 `static public string globalSession;` を使います。

このグローバル変数は、スクリプトの実行中に行セット全体に適用されます。

U-SQL プログラムのコードビハインド セクションを以下に示します。

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

この例は、`getStampUserSession` 関数で使っているグローバル変数 `static public string globalSession;` を示しており、この変数は Session パラメーターが変わるごとに初期化されます。

U-SQL のベース スクリプトは次のとおりです。

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

関数 `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` は、2 番目のメモリ行セットの計算中に呼び出されます。 この関数は `UserSessionTimestamp` 列を渡し、`UserSessionTimestamp` が変わるまでの間、値を返します。

出力ファイルは次のようになります。

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

この例では一段階複雑なユース ケース シナリオとして、コードビハインド セクションにメモリ行セット全体に適用されるグローバル変数を使うというシナリオを紹介しました。

## <a name="next-steps"></a>次のステップ
* [U-SQL プログラミング ガイド - UDT および UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [U-SQL プログラミング ガイド - UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)