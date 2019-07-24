---
title: Azure Data Lake の U-SQL プログラミング ガイド
description: クラウドベースのビッグ データ プラットフォームを作成するための Azure Data Lake Analytics のサービスのセットについて説明します。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: d1b230b40d1f880787334ebfd39e704e3a650baa
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489658"
---
# <a name="u-sql-programmability-guide"></a>U-SQL プログラミング ガイド

U-SQL は、ビッグ データに該当するワークロードの処理を目的として設計されたクエリ言語です。 U-SQL 独自の特徴の 1 つに、SQL によく似た宣言型言語でありながら、C# の拡張性とプログラミング機能をも兼ね備えていることが挙げられます。 このガイドでは、C# によって実現される U-SQL 言語の拡張性とプログラミング機能について説明します。

## <a name="requirements"></a>必要条件

[Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) のダウンロードとインストール

## <a name="get-started-with-u-sql"></a>U-SQL を使ってみる  

次の U-SQL スクリプトを見てみましょう。

```
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

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

次のスニペットでは、DECLARE ステートメントで文字列を DateTime 値として解析します。

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>C# の式を使ってデータ型を変換する

以下の例は、C# の式を使って日時のデータを変換する方法を示しています。 このシナリオでは、文字列の日時データを、時間の表記が真夜中の 00:00:00 となっている標準的な日時データに変換しています。

```
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

```
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

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

次のコードは、アセンブリの参照方法を示しています。

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

[アセンブリの登録手順](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/)に関する記事をご覧ください。このトピックについて詳しく説明しています。


### <a name="use-assembly-versioning"></a>アセンブリのバージョン管理を使用する
U-SQL では現在、.NET Framework 4.5 を使っています。 このため、独自のアセンブリがこのバージョンのランタイムと互換性があることをご確認ください。

前に説明したとおり、U-SQL ではコードを 64 ビット (x64) 形式で実行します。 このため、コードは x64 形式で実行されるようにコンパイルしてください。 そうしないと、前に示したフォーマットの誤りに関するエラーが表示されます。

アセンブリ DLL とリソース ファイル (別のランタイム、ネイティブ アセンブリ、構成ファイルなど) は、それぞれ最大 400 MB までアップロードできます。 また、DEPLOY RESOURCE またはアセンブリとその追加ファイルの参照によりデプロイしたリソースのサイズは、合計 3 GB を超えることはできません。

最後に、各 U-SQL データベースに格納できるアセンブリのバージョンは 1 つだけであるという点にご注意ください。 たとえば、NewtonSoft Json.NET ライブラリのバージョン 7 とバージョン 8 の両方が必要な場合には、それぞれを異なる 2 つのデータベースに登録する必要があります。 さらに、各スクリプトで参照できるアセンブリ DLL のバージョンも 1 つのみです。 U-SQL はこの点で、C# のアセンブリ管理とバージョン管理の規則に従っています。

## <a name="use-user-defined-functions-udf"></a>ユーザー定義関数を使用する: UDF
U-SQL のユーザー定義関数 (UDF) は、パラメーターを受け取り、複雑な計算などのアクションを実行し、そのアクションの結果を値として返すプログラミング ルーチンです。 UDF の戻り値とすることができるのは、単一のスカラー値のみです。 U-SQL の UDF は、C# の他のスカラー関数と同じく、U-SQL のベース スクリプトから呼び出すことができます。

U-SQL のユーザー定義関数は、**public** と **static** で初期化することをお勧めします。

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

まずは、簡単な UDF を作成する例を見てみましょう。

このユース ケース シナリオでは、特定のユーザーが初めてサインインした会計期間 (会計上の四半期と月を含む) を特定する必要があります。 このシナリオでは、会計年度の最初の月を 6 月とします。

会計期間を計算するにあたっては、以下の C# の関数を導入します。

```
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

```
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

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

以下は、U-SQL の実際のベース スクリプトを示したものです。

```
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

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

この例では、U-SQL でインライン UDF を使用する方法を簡単に紹介しました。

### <a name="keep-state-between-udf-invocations"></a>各 UDF 呼び出しの間で状態を維持する
U-SQL の C# プログラミング オブジェクトは、コードビハインドのグローバル変数を使えば、対話機能を備えたさらに高度なものにすることができます。 ここでは、ビジネスの場でのユース ケース シナリオを見てみましょう。

大規模な組織ではさまざまなアプリケーション  (Microsoft Dynamics CRM、PowerBI など) が採用されており、ユーザーはそれらを切り替えながら使います。 このため、顧客企業ではユーザーがどのような状況でアプリケーションを切り替えているかや、使用状況の傾向などについて、テレメトリ分析を実施したいという要望が生じる場合があります。 ビジネスの目標は、アプリケーションの使用状況を最適化することにあります。 そのためには、さまざまなアプリケーションや特定のサインオン ルーチンを組み合わせることも必要になる可能性があります。

この目標を達成するには、セッション ID と、前回発生したセッションとの間のラグ タイム (時間差) を決める必要があります。

前回のサインインを探し、同じアプリケーションに対して生成されるセッションすべてに、このサインインを割り当てます。 最初の問題は、U-SQL のベース スクリプトでは LAG 関数で既に計算が済んでいる列に対してもう一度計算を適用することができないという点です。 2 番目の問題は、同一の期間内に発生するセッションすべてについて、一定のセッションを保持する必要があるという点です。

この問題を解決するために、コードビハインド セクションにグローバル変数 `static public string globalSession;` を使います。

このグローバル変数は、スクリプトの実行中に行セット全体に適用されます。

U-SQL プログラムのコードビハインド セクションを以下に示します。

```
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

```
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

```
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

## <a name="use-user-defined-types-udt"></a>ユーザー定義型を使用する: UDT
ユーザー定義型 (UDT) は、U-SQL に用意されているもう 1 つのプログラミング機能です。 U-SQL の UDT の動作は、C# の一般的なユーザー定義型とよく似ています。 C# は、組み込みの型と独自のユーザー定義型のどちらも使用できる型付けの強固な言語です。

U-SQL では、UDT が行セットの頂点間で渡されるときに、任意の UDT を暗黙的にシリアル化または逆シリアル化することができません。 このため、ユーザーは IFormatter インターフェイスを使って明示的なフォーマッタを指定する必要があります。 これにより、U-SQL に UDT のシリアル化および逆シリアル化メソッドが提供されます。

> [!NOTE]
> U-SQL の組み込みのエクストラクターおよびアウトプッターは現在、IFormatter セットを使ってもファイルとの間で UDT データをシリアル化または逆シリアル化することができません。 このため、OUTPUT ステートメントで UDT データをファイルに書き込む場合、またはエクストラクターでデータを読み取る場合に、これを文字列またはバイト配列として渡す必要があります。 さらに、シリアル化および逆シリアル化コード (UDT の ToString() メソッド) を明示的に呼び出します。 一方、ユーザー定義のエクストラクターおよびアウトプッターでは、UDT の読み取り/書き込みを行えます。

エクストラクターまたはアウトプッター (前の SELECT の外部) で UDT を使う場合のコードは、以下のとおりです。

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

このコードでは、以下のエラーが発生します。

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

アウトプッターで UDT を使うには、ToString() メソッドを使って文字列にシリアル化するか、カスタム アウトプッターを作成する必要があります。

現時点では、GROUP BY で UDT を使用することはできません。 GROUP BY で UDT を使用した場合には、以下のエラーが表示されます。

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

UDT を定義する手順は、以下のとおりです。

* 次の名前空間を追加します。

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* UDTのインターフェイスに必要な `Microsoft.Analytics.Interfaces` を追加します。 また、IFormatter インターフェイスの定義には、`System.IO` が必要になることもあります。

* SqlUserDefinedType 属性を使ってユーザー定義型を定義します。

**SqlUserDefinedType** は、アセンブリ内の型定義を U-SQL のユーザー定義型 (UDT) としてマークする際に使用する属性です。 この属性のプロパティは、UDT の物理的な特徴を反映したものになります。 このクラスは継承できません。

SqlUserDefinedType は、UDT を定義するうえで必須の属性です。

クラスのコンストラクター:  

* SqlUserDefinedTypeAttribute(型フォーマッタ)

* 型フォーマッタ: UDT フォーマッタを定義するうえで必須のパラメーターです。具体的には、`IFormatter` インターフェイスの型をここに渡す必要があります。

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* 次の例で示すように、典型的な UDT では IFormatter インターフェイスの定義も必要です。

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter` インターフェイスは、ルート型 \<typeparamref name="T"> を使ってオブジェクト グラフをシリアル化および逆シリアル化するものです。

\<typeparam 名前 ="T"> は、シリアル化および逆シリアル化の対象となるオブジェクト グラフのルート型です。

* **逆シリアル化**: 指定されたストリーム上のデータを逆シリアル化して、オブジェクトのグラフを再構成します。

* **シリアル化**: 指定されたルートのオブジェクトまたはオブジェクトのグラフをシリアル化し、指定されたストリームに送ります。

`MyType` instance: 型のインスタンスです。  
`IColumnWriter` writer / `IColumnReader` reader: 基になる列ストリームです。  
`ISerializationContext` context: シリアル化の実行中にストリームのソースまたは宛先に関するコンテキストを指定するフラグのセットを定義する列挙型です。

* **中間**: ソースまたは宛先に関するコンテキストが永続的なストアでないことを指定します。

* **永続化**: ソースまたは宛先に関するコンテキストが永続的なストアであることを指定します。

U-SQL の UDT の定義には、C# の通常の型として +、==、!= などの演算子のオーバーライドを含めることができます。 また、静的メソッドを含めることもできます。 たとえば、この UDT を U-SQL の MIN 集計関数のパラメーターとして使う場合には、< 演算子のオーバーライドを定義する必要があります。

このガイドではこれまでに、特定の日付から会計期間を特定し、`Qn:Pn (Q1:P10)` という形式で返す例をお見せしました。 次の例では、会計期間の値のカスタム型を定義する方法を紹介します。

カスタム UDT と IFormatter インターフェイスを備えたコードビハインド セクションの例は、以下のとおりです。

```
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

新たに定義された型には、四半期と月の 2 つの数字が含まれています。 演算子 `==/!=/>/<` と静的メソッド `ToString()` も、ここで定義しています。

既に説明したとおり、SELECT 式なら特別な処理をしなくても UDT を使用できるのに対して、アウトプッターとエクストラクターで UDT を使う場合には、カスタム シリアル化が必要になります。 このため、UDT は `ToString()` を使って文字列としてシリアル化するか、カスタマイズしたアウトプッターまたはエクストラクターで使う必要があります。

それでは、UDT の使用方法を見ていきましょう。 コードビハインド セクションで、GetFiscalPeriod 関数を次のように変更しました。

```
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

ご覧のとおり、FiscalPeriod 型の値が返されます。

これを U-SQL のベース スクリプトで使う方法を以下の例に示します。 この例では、U-SQL スクリプトから UDT を呼び出すさまざまな方法を示しています。

```
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

完全なコードビハインド セクションの例は以下のとおりです。

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>ユーザー定義集計を使用する: UDAGG
ユーザー定義集計とは、U-SQL であらかじめ用意されていない集計関連の関数のことを指します。 たとえば、カスタムの算術計算、文字列の連結、文字列の操作などを実行する集計関数が、ユーザー定義集計に該当します。

ユーザー定義集計の基底クラスの定義は、以下のとおりです。

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** は、この型をユーザー定義集計として登録する必要があることを示しています。 このクラスは継承できません。

SqlUserDefinedType 属性は、UDAGG の定義では**省略可能**です。


この基底クラスでは、抽象パラメーターを入力に 2 つ、結果に 1 つの計 3 つ渡すことができるようになっています。 データ型は変数であり、クラスの継承時に定義する必要があります。

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** は、計算中にグループ 1 つにつき 1 回呼び出されます。 これは、各集計グループの初期化ルーチンを提供するものです。  
* **Accumulate** は、各値に対して 1 回ずつ実行されます。 これが、集計アルゴリズムのメインの機能です。 クラスの継承中にさまざまなデータ型が定義された値を集計する際に使うものであり、 データ型が変数のパラメーターを 2 つ受け取ることができます。
* **Terminate** は、処理の最後に各集計グループに対して 1 回ずつ実行され、各グループの結果を出力します。

入力と出力の正しいデータ型を宣言するには、次のようにクラス定義を使います。

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Accumulate の 1 つ目のパラメーター
* T2:Accumulate の 2 つ目のパラメーター
* TResult: Terminate の戻り値の型

例: 

```
public class GuidAggregate : IAggregate<string, int, int>
```

or

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>U-SQL で UDAGG を使用する
UDAGG を使うにはまず、既に説明したようにコードビハインドで定義するか、既存のプログラミング DLL から参照する必要があります。

そのうえで、次の構文を使います。

```
AGG<UDAGG_functionname>(param1,param2)
```

次に UDAGG の例を示します。

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

U-SQL のベース スクリプトは、以下のとおりです。

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

このユース ケース シナリオでは、特定のユーザーのクラスの GUID を連結しています。

## <a name="use-user-defined-objects-udo"></a>ユーザー定義オブジェクトを使用する: UDO
U-SQL を使うと、ユーザー定義オブジェクト (UDO) と呼ばれるカスタム プログラミング オブジェクトを定義できます。

以下は、U-SQL の UDO の一覧です。

* ユーザー定義エクストラクター
    * 行ごとに抽出
    * カスタム構造化ファイルからのデータ抽出の実装に使用

* ユーザー定義アウトプッター
    * 行ごとに出力
    * カスタム データ型またはカスタム ファイル形式への出力に使用

* ユーザー定義プロセッサ
    * 1 つの行を取得し、1 つの行を生成
    * 列の数を減らしたり、既存の列セットから派生した値を含む列を新しく生成したりする際に使用

* ユーザー定義アプライヤー
    * 1 つの行を取得し、0 ～ n 個の行を生成
    * OUTER/CROSS APPLY と併用

* ユーザー定義コンバイナー
    * 行セットを結合 (ユーザー定義の JOIN)

* ユーザー定義レジューサー
    * n 個の行を取得し、1 つの行を生成
    * 行の数を減らす際に使用

UDO は通常、U-SQL スクリプトの以下の U-SQL ステートメントの中で明示的に呼び出されます。

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> UDO の消費メモリは 0.5Gb に制限されています。  このメモリ制限は、ローカルの実行には適用されません。

## <a name="use-user-defined-extractors"></a>ユーザー定義エクストラクターを使用する
U-SQL では、EXTRACT ステートメントを使って外部データをインポートできます。 EXTRACT ステートメントでは、組み込みの UDO エクストラクターを使用できます。  

* *Extractors.Text()*: エンコードがさまざまに異なる区切りテキスト ファイルからの抽出に対応します。

* *Extractors.Csv()*: エンコードがさまざまに異なるコンマ区切り値 (CSV) ファイルからの抽出に対応します。

* *Extractors.Tsv()*: エンコードがさまざまに異なるタブ区切り値 (TSV) ファイルからの抽出に対応します。

カスタム エクストラクターを開発すると便利なことがあります。 具体的には、データのインポート中に以下のいずれかのタスクが必要な場合に便利です。

* 列を分割し、個々の値を変更する形で入力データに変更を加える。 列の結合については、プロセッサ機能の方が適しています。
* Web ページ、メールなどの非構造化データまたは XML、JSON などの半構造化データを解析する。
* サポート対象外のエンコーディングが行われたデータを解析する。

ユーザー定義エクストラクター (UDE) を定義するには、`IExtractor` インターフェイスを作成する必要があります。 列や行の区切り記号、エンコーディングなど、エクストラクターに対する入力パラメーターはすべて、クラスのコンストラクターで定義する必要があります。 `IExtractor` インターフェイスにはほかにも、次のような `IEnumerable<IRow>` オーバーライドの定義が必要です。

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

**SqlUserDefinedExtractor** 属性は、型をユーザー定義エクストラクターとして登録する必要があることを示しています。 このクラスは継承できません。

SqlUserDefinedExtractor は、UDE の定義のオプション属性です。 この属性は、UDE オブジェクトの AtomicFileProcessing プロパティを定義する場合に使用します。

* bool     AtomicFileProcessing   

* **true** = このエクストラクターにはアトミック入力ファイル (JSON、XML など) が必要であることを示します。
* **false** = このエクストラクターが分割または分散されたファイル (CSV、SEQ など) を扱うことができることを示します。

主な UDE プログラミング オブジェクトは、**input** と **output** です。 Input オブジェクトは、入力データを `IUnstructuredReader` として列挙するために使うものです。 output オブジェクトはエクストラクターのアクティビティの結果としての出力データを設定するために使います。

入力データには、`System.IO.Stream` および `System.IO.StreamReader` を使ってアクセスできます。

入力列を列挙するには、最初に行の区切り記号を使って入力ストリームを分割します。

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

次に、入力行を列の部分に分割します。

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

出力データを設定するには、`output.Set` メソッドを使います。

カスタム エクストラクターでは、output で定義された列と値のみ出力されるという点が重要です。 Set メソッドの呼び出しは以下のとおりです。

```
output.Set<string>(count, part);
```

エクストラクターの実際の出力は、`yield return output.AsReadOnly();` を呼び出すとトリガーされます。

エクストラクターの例を以下に示します。

```
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

このユース ケース シナリオでは、エクストラクターによって "guid" 列の GUID が再生成され、"user" 列の値が大文字に変換されます。 カスタム エクストラクターを使えば、入力データを解析して操作し、いっそう複雑な結果を生成できます。

カスタム エクストラクターを使う U-SQLのベース スクリプトは、以下のとおりです。

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>ユーザー定義アウトプッターを使用する
ユーザー定義アウトプッターは、U-SQL の組み込み機能を拡張するもう 1 つの U-SQL UDO です。 エクストラクターと同じく、アウトプッターも組み込みのものがいくつか用意されています。

* *Outputters.Text()*: エンコードがさまざまに異なる区切りテキスト ファイルにデータを書き込みます。
* *Outputters.Csv()*: エンコードがさまざまに異なるコンマ区切り値 (CSV) ファイルにデータを書き込みます。
* *Outputters.Tsv()*: エンコードがさまざまに異なるタブ区切り値 (TSV) ファイルにデータを書き込みます。

カスタム アウトプッターでは、独自に定義した形式でデータを書き込むことができます。 これは、以下のタスクに役に立つ場合があります。

* 半構造化ファイルまたは非構造化ファイルにデータを書き込む。
* サポートされていないエンコーディングのデータを書き込む。
* 出力データを変更したり、カスタム属性を追加したりする。

ユーザー定義アウトプッターを定義するには、`IOutputter` インターフェイスを作成する必要があります。

基底クラスとなる `IOutputter` の実装を以下に示します。

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

列や行の区切り記号、エンコーディングなど、アウトプッターに対する入力パラメーターはすべて、クラスのコンストラクターで定義する必要があります。 `IOutputter` インターフェイスにはほかにも、`void Output` オーバーライドの定義が必要です。 アトミック ファイルの処理に関しては、オプションで属性 `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` を設定できます。 詳しくは、次の詳細をご覧ください。

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` は、各入力行について呼び出されます。 戻り値は `IUnstructuredWriter output` 行セットです。
* ユーザー定義アウトプッターにパラメーターを渡すときには、コンストラクター クラスを使います。
* `Close` は、負荷の大きな状態のリリースや最後の行が書き込まれた時点の判断のためにオプションでオーバーライドするために使われます。

**SqlUserDefinedOutputter** 属性は、型をユーザー定義アウトプッターとして登録する必要があることを示しています。 このクラスは継承できません。

SqlUserDefinedOutputter は、ユーザー定義アウトプッターの定義のオプション属性です。 この属性は、AtomicFileProcessing プロパティを定義する場合に使います。

* bool     AtomicFileProcessing   

* **true** = このアウトプッターにはアトミック出力ファイル (JSON、XML など) が必要であることを示します。
* **false** = このアウトプッターが分割または分散されたファイル (CSV、SEQ など) を扱うことができることを示します。

主なプログラミング オブジェクトは、**row** と **output** です。 **row** オブジェクトは、出力データを `IRow` インターフェイスとして列挙するために使うものです。 **output** はターゲット ファイルに対する出力データを設定するために使います。

出力データには、`IRow` インターフェイスを使ってアクセスできます。 また、出力データには行が一度に 1 つずつ渡されます。

個々の値は、IRow インターフェイスの Get メソッドを呼び出すと列挙されます。

```
row.Get<string>("column_name")
```

`row.Schema` を呼び出すと、個々の列の名前を決めることができます。

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

この方法を使えば、メタデータのスキーマを問わず対応できる柔軟なアウトプッターを作成することができます。

出力データは、`System.IO.StreamWriter` を使ってファイルに書き込まれます。 ストリームのパラメーターは `IUnstructuredWriter output` の一部として `output.BaseStream` に設定されます。

行の反復処理が 1 回終わるごとにデータ バッファーをファイルにフラッシュすることが重要です。 また、`StreamWriter` オブジェクトは、Disposable 属性を有効にした状態 (既定の設定) で、**using** キーワードと共に使う必要があります。

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

または、反復処理が 1 回終わるごとに Flush() メソッドを明示的に呼び出します。 これについては、次の例で示します。

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>ユーザー定義アウトプッターにヘッダーとフッターを設定する
ヘッダーを設定するには、1 回限りの反復実行フローを使用します。

```
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

最初の `if (isHeaderRow)` ブロックのコードは、1 回のみ実行されます。

フッターについては、`System.IO.Stream` オブジェクトのインスタンス (`output.BaseStream`) に対する参照を使います。 `IOutputter` インターフェイスの Close() メソッドにフッターを記述します。  (詳しくは、次の例をご覧ください。)

ユーザー定義アウトプッターの例は以下のとおりです。

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

U-SQL のベース スクリプトは、以下のようになります。

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

これが、テーブル データを備えた HTML ファイルを作成する HTML アウトプッターです。

### <a name="call-outputter-from-u-sql-base-script"></a>U-SQL のベース スクリプトからアウトプッターを呼び出す
U-SQL のベース スクリプトからカスタム アウトプッターを呼び出すには、アウトプッター オブジェクトの新しいインスタンスを作成する必要があります。

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

ベース スクリプトでオブジェクトのインスタンスを作成しないようにするには、前の例に示したように関数ラッパーを作成します。

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

このとき、元の呼び出しは以下のようになります。

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>ユーザー定義プロセッサを使用する
ユーザー定義プロセッサ (UDP) は U-SQL の UDO の一種で、プログラミング機能を適用して入力行を処理できます。 UDP では、必要に応じて列の結合、値の変更、新しい列の追加が可能です。 UDP は基本的に、行セットを処理して必要なデータ要素を生成する際に役立つものです。

UDP を定義するには、`IProcessor` インターフェイスを作成する必要があります。UDP では、`SqlUserDefinedProcessor` 属性は省略可能です。

このインターフェイスには、次の例に示すように `IRow` インターフェイスの行セット オーバーライドの定義が必要です。

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** は、この型をユーザー定義プロセッサとして登録する必要があることを示しています。 このクラスは継承できません。

SqlUserDefinedProcessor 属性は、UDP の定義では**省略可能**です。

主なプログラミング オブジェクトは、**input** と **output** です。 input オブジェクトは、入力列を列挙するために使うものです。これに対して、output はプロセッサのアクティビティの結果としての出力データを設定するために使います。

入力列の列挙には、`input.Get` メソッドを使います。

```
string column_name = input.Get<string>("column_name");
```

`input.Get` メソッドに渡すパラメーターは、U-SQL のベース スクリプトの `PROCESS` ステートメントの `PRODUCE` 句で渡される列です。 ここでは正しいデータ型を使用する必要があります。

出力には、`output.Set` メソッドを使います。

カスタム プロデューサーでは、`output.Set` メソッドの呼び出しで定義された列および値のみ出力されるという点が重要です。

```
output.Set<string>("mycolumn", mycolumn);
```

プロセッサの実際の出力は、`return output.AsReadOnly();` を呼び出すとトリガーされます。

プロセッサの例を以下に示します。

```
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

このユース ケース シナリオでは、プロセッサにより既存の列 (この場合は "user" (大文字) と "des") を結合することで "full_description" という新しい列が生成されます。 また、GUID が再生成され、元の GUID の値と新しい GUID の値が返されます。

前の例からわかるように、`output.Set` メソッドの呼び出し中に C# メソッドを呼び出すことができます。

カスタム プロセッサを使う U-SQL のベース スクリプトの例は、以下のとおりです。

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>ユーザー定義アプライヤーを使用する
U-SQL のユーザー定義アプライヤーを使うと、クエリの外部テーブル式によって返される行ごとにカスタム C# 関数を呼び出すことができます。 ユーザー定義アプライヤーは、左側の入力から得られた各行について右側の入力を評価し、生成された行を結合したうえで、最終的な出力とします。 APPLY 演算子により生成される列の一覧は、左側の入力と右側の入力にある列のセットを結合したものになります。

ユーザー定義アプライヤーは、U-SQL の SELECT 式の一環として呼び出されます。

ユーザー定義アプライヤーの呼び出しの典型例を以下に示します。

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

SELECT 式でのアプライヤーの使用について詳しくは、「[U-SQL SELECT Selecting from CROSS APPLY and OUTER APPLY (U-SQL SELECT における CROSS APPLY と OUTER APPLY を使った選択)](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply)」をご覧ください。

ユーザー定義アプライヤーの基底クラスの定義は、以下のとおりです。

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

ユーザー定義アプライヤーを定義するには、`IApplier` インターフェイスを作成する必要があります。ユーザー定義アプライヤーの定義では、[`SqlUserDefinedApplier`] 属性は省略可能です。

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Apply は、外部テーブルの行ごとに呼び出され、 `IUpdatableRow` の出力行セットを返します。
* ユーザー定義アプライヤーにパラメーターを渡すときには、コンストラクター クラスを使います。

**SqlUserDefinedApplier** は、この型をユーザー定義アプライヤーとして登録する必要があることを示しています。 このクラスは継承できません。

**SqlUserDefinedApplier** は、ユーザー定義アプライヤーの定義では**省略可能**です。


主なプログラミング オブジェクトは、以下のとおりです。

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

入力行セットは、`IRow` の入力として渡されます。 出力行は、`IUpdatableRow` 出力インターフェイスとして生成されます。

`IRow` のスキーマ メソッドを呼び出すと、個々の列の名前を決めることができます。

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

`IRow` から実際のデータ値を取得するには、`IRow` インターフェイスの Get() メソッドを使います。

```
mycolumn = row.Get<int>("mycolumn")
```

または、スキーマの列名を使います。

```
row.Get<int>(row.Schema[0].Name)
```

出力値は、`IUpdatableRow` 出力で設定する必要があります。

```
output.Set<int>("mycolumn", mycolumn)
```

カスタム アプライヤーでは、`output.Set` メソッドの呼び出しで定義された列および値のみ出力されるという点が重要です。

実際の出力は、`yield return output.AsReadOnly();` を呼び出すとトリガーされます。

ユーザー定義アプライヤーのパラメーターは、コンストラクターに渡すことができます。 アプライヤーは、U-SQL のベース スクリプトでアプライヤーを呼び出すときに定義が必要な列をいくつも返すことができます。

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

ユーザー定義アプライヤーの例を以下に示します。

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

このユーザー定義アプライヤーの U-SQL のベース スクリプトは以下のとおりです。

```
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

このユース ケース シナリオでは、ユーザー定義アプライヤーが車のプロパティのコンマ区切り値のパーサーとしての役割を果たしています。 入力ファイルの行は、以下のようになります。

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

これは、典型的なタブ区切り (TSV) ファイルです。プロパティ列には製造元、モデルなど、車のプロパティが格納されています。 これらのプロパティは解析のうえ、テーブルの列の形に並べられる必要があります。 ここに示したアプライヤーでは、渡されるパラメーターに基づいて結果の行セットに生成するプロパティの数を動的に変えることができます。 すべてのプロパティを生成することも、プロパティの特定のセットのみを生成することもできます。

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

ユーザー定義アプライヤーは、アプライヤー オブジェクトの新しいインスタンスとして呼び出すことができます。

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

また、ラッパー ファクトリ メソッドを呼び出すことによって呼び出すこともできます。

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>ユーザー定義コンバイナーを使用する
ユーザー定義コンバイナー (UDC) を使うと、カスタム ロジックに基づいて左右の行セットの行を結合できます。 ユーザー定義コンバイナーは、COMBINE 式で使用します。

COMBINE 式でコンバイナーが呼び出されると、左右の入力行セット、グループ化列、予期される結果スキーマに関する必要な情報のほか、追加の情報が提供されます。

U-SQL のベース スクリプトでコンバイナーを呼び出すには、以下の構文を使います。

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

さらに詳しい情報については、「[COMBINE Expression (U-SQL) (COMBINE 式 (U-SQL))](/u-sql/statements-and-expressions/combine-expression)」を参照してください。

ユーザー定義コンバイナーを定義するには、`ICombiner` インターフェイスを作成する必要があります。ユーザー定義コンバイナーの定義では、[`SqlUserDefinedCombiner`] 属性は省略可能です。

基底クラスとなる `ICombiner` クラスの定義は以下のとおりです。

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

`ICombiner` インターフェイスのカスタム実装には、`IEnumerable<IRow>` の結合オーバーライドの定義が必要です。

```
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

**SqlUserDefinedCombiner** 属性は、型をユーザー定義コンバイナーとして登録する必要があることを示しています。 このクラスは継承できません。

**SqlUserDefinedCombiner** は、コンバイナー モード プロパティを定義するために使います。 これは、ユーザー定義コンバイナーの定義のオプション属性です。

CombinerMode のモード

CombinerMode 列挙型は、以下の値を取ることができます。

* Full (0): 出力行がいずれも、キー値が同じ左右の入力行すべてに依存する可能性があります。

* Left (1): 出力行がいずれも、左側の単一の入力行に依存します (また、キー値が同じ右側の行すべてにも依存する可能性があります)。

* Right (2): 出力行がいずれも、右側の単一の入力行に依存します (また、キー値が同じ左側の行すべてにも依存する可能性があります)。

* Inner (3): 出力行がいずれも、キー値が同じ左右の単一の入力行に依存します。

例: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


主なプログラミング オブジェクトは、以下のとおりです。

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

入力行セットは、**left** および **right** の 2 つの `IRowset` 型インターフェイスとして渡されます。 行セットはどちらも、処理のために列挙される必要があります。 列挙は各インターフェイスにつき 1 回のみ許可されているため、列挙およびキャッシュは必要なときに実施する必要があります。

キャッシュのために、LINQ クエリの実行の結果として List\<T\> 型のメモリ構造体を作成できます。具体的には List<`IRow`> です。 列挙では、匿名データ型も使用できます。

LINQ クエリについて詳しくは「[LINQ クエリの概要 (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries)」を、IEnumerable\<T\> インターフェイスについて詳しくは「[IEnumerable\<T\> インターフェイス](/dotnet/api/system.collections.generic.ienumerable-1)」を、それぞれご覧ください。

`IRowset` から実際のデータ値を取得するには、`IRow` インターフェイスの Get() メソッドを使います。

```
mycolumn = row.Get<int>("mycolumn")
```

`IRow` のスキーマ メソッドを呼び出すと、個々の列の名前を決めることができます。

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

または、スキーマの列名を使います。

```
c# row.Get<int>(row.Schema[0].Name)
```

LINQ の一般的な列挙型は、以下のようになります。

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

両方の行セットを列挙した後、すべての行をループします。 左側の行セットにある行それぞれについて、コンバイナーの条件を満たす行をすべて探し出します。

出力値は、`IUpdatableRow` 出力で設定する必要があります。

```
output.Set<int>("mycolumn", mycolumn)
```

実際の出力は、`yield return output.AsReadOnly();` を呼び出すとトリガーされます。

コンバイナーの例を以下に示します。

```
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

このユース ケース シナリオでは、小売業者向けの分析レポートを作成します。 目的は、価格が $20,000 を超える製品のなかで、Web サイトでの一定期間内の販売量が通常の小売店を上回ったものを明らかにすることにあります。

U-SQL のベース スクリプトは、以下のとおりです。 通常の JOIN とコンバイナーのロジックを比較してみてください。

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

ユーザー定義コンバイナーは、アプライヤー オブジェクトの新しいインスタンスとして呼び出すことができます。

```
USING new MyNameSpace.MyCombiner();
```


また、ラッパー ファクトリ メソッドを呼び出すことによって呼び出すこともできます。

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>ユーザー定義レジューサーを使用する

U-SQL では、C# でユーザー定義演算子機能拡張フレームワークを使い、IReducer インターフェイスを実装することで、カスタム行セット レジューサーを作成できます。

ユーザー定義レジューサー (UDR) を使えば、データの抽出 (インポート) 時に不要な行を除去できます。 行や列を操作および評価するために使うこともできます。 プログラミング ロジックに基づいて、抽出が必要な行を定義することもできます。

UDR クラスを定義するには、オプションで `SqlUserDefinedReducer` 属性を設定した `IReducer` インターフェイスを作成する必要があります。

このクラス インターフェイスには、`IEnumerable` インターフェイスの行セット オーバーライドの定義が必要です。

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

**SqlUserDefinedReducer** 属性は、型をユーザー定義レジューサーとして登録する必要があることを示しています。 このクラスは継承できません。
**SqlUserDefinedReducer** は、ユーザー定義レジューサーの定義のオプション属性です。 この属性は、IsRecursive プロパティを定義する場合に使います。

* bool     IsRecursive    
* **true**  = このレジューサーが結合的で可換かどうかを示します

主なプログラミング オブジェクトは、**input** と **output** です。 Input オブジェクトは、入力行を列挙するために使うものです。 output はレジューサーのアクティビティの結果としての出力行を設定するために使います。

入力行の列挙には、`Row.Get` メソッドを使います。

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

`Row.Get` メソッドに渡すパラメーターは、U-SQL のベース スクリプトの `REDUCE` ステートメントの `PRODUCE` クラスで渡される列です。 ここでも、正しいデータ型を使用する必要があります。

出力には、`output.Set` メソッドを使います。

カスタム レジューサーでは、`output.Set` メソッドの呼び出しで定義された値のみ出力されるという点が重要です。

```
output.Set<string>("mycolumn", guid);
```

レジューサーの実際の出力は、`yield return output.AsReadOnly();` を呼び出すとトリガーされます。

レジューサーの例を以下に示します。

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

このユース ケース シナリオでは、ユーザー名が空の行をレジューサーがスキップしています。 レジューサーは、行セットの行それぞれについて必要な列を読み取ってユーザー名の長さを評価します。 ユーザー名が 0 文字よりも長い場合に限り、実際に行を出力します。

カスタム レジューサーを使う U-SQL のベース スクリプトは、以下のとおりです。

```
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
