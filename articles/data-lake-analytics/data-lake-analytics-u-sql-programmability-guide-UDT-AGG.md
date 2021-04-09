---
title: Azure Data Lake の U-SQL UDT および UDAGG プログラミング ガイド
description: 優れた USQL スクリプトを作成できるように、Azure Data Lake Analytics での U-SQL UDT および UDAGG プログラミングについて説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 10fcce9a667d9a08318f5adab804f482387052ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606659"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>U-SQL プログラミング ガイド - UDT および UDAGG



## <a name="use-user-defined-types-udt"></a>ユーザー定義型を使用する: UDT
ユーザー定義型 (UDT) は、U-SQL に用意されているもう 1 つのプログラミング機能です。 U-SQL の UDT の動作は、C# の一般的なユーザー定義型とよく似ています。 C# は、組み込みの型と独自のユーザー定義型のどちらも使用できる型付けの強固な言語です。

U-SQL では、UDT が行セットの頂点間で渡されるときに、任意の UDT を暗黙的にシリアル化または逆シリアル化することができません。 このため、ユーザーは IFormatter インターフェイスを使って明示的なフォーマッタを指定する必要があります。 これにより、U-SQL に UDT のシリアル化および逆シリアル化メソッドが提供されます。

> [!NOTE]
> U-SQL の組み込みのエクストラクターおよびアウトプッターは現在、IFormatter セットを使ってもファイルとの間で UDT データをシリアル化または逆シリアル化することができません。 このため、OUTPUT ステートメントで UDT データをファイルに書き込む場合、またはエクストラクターでデータを読み取る場合に、これを文字列またはバイト配列として渡す必要があります。 さらに、シリアル化および逆シリアル化コード (UDT の ToString() メソッド) を明示的に呼び出します。 一方、ユーザー定義のエクストラクターおよびアウトプッターでは、UDT の読み取り/書き込みを行えます。

エクストラクターまたはアウトプッター (前の SELECT の外部) で UDT を使う場合のコードは、以下のとおりです。

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

このコードでは、以下のエラーが発生します。

```output
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

```output
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

1. 次の名前空間を追加します。

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. UDTのインターフェイスに必要な `Microsoft.Analytics.Interfaces` を追加します。 また、IFormatter インターフェイスの定義には、`System.IO` が必要になることもあります。

3. SqlUserDefinedType 属性を使ってユーザー定義型を定義します。

**SqlUserDefinedType** は、アセンブリ内の型定義を U-SQL のユーザー定義型 (UDT) としてマークする際に使用する属性です。 この属性のプロパティは、UDT の物理的な特徴を反映したものになります。 このクラスは継承できません。

SqlUserDefinedType は、UDT を定義するうえで必須の属性です。

クラスのコンストラクター:  

* SqlUserDefinedTypeAttribute(型フォーマッタ)

* 型フォーマッタ: UDT フォーマッタを定義するうえで必須のパラメーターです。具体的には、`IFormatter` インターフェイスの型をここに渡す必要があります。

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* 次の例で示すように、典型的な UDT では IFormatter インターフェイスの定義も必要です。

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter` インターフェイスは、ルート型 \<typeparamref name="T"> を使ってオブジェクト グラフをシリアル化および逆シリアル化するものです。

\<typeparam name="T"> は、シリアル化および逆シリアル化の対象となるオブジェクト グラフのルート型です。

* **逆シリアル化**: 指定されたストリーム上のデータを逆シリアル化して、オブジェクトのグラフを再構成します。

* **シリアル化**: 指定されたルートのオブジェクトまたはオブジェクトのグラフをシリアル化し、指定されたストリームに送ります。

`MyType` インスタンス: 型のインスタンスです。  
`IColumnWriter` writer / `IColumnReader` reader: 基になる列ストリームです。  
`ISerializationContext` context: シリアル化の実行中にストリームのソースまたは宛先に関するコンテキストを指定するフラグのセットを定義する列挙型です。

* **中間**: ソースまたは宛先に関するコンテキストが永続的なストアでないことを指定します。

* **永続化**: ソースまたは宛先に関するコンテキストが永続的なストアであることを指定します。

U-SQL の UDT の定義には、C# の通常の型として +、==、!= などの演算子のオーバーライドを含めることができます。 また、静的メソッドを含めることもできます。 たとえば、この UDT を U-SQL の MIN 集計関数のパラメーターとして使う場合には、< 演算子のオーバーライドを定義する必要があります。

このガイドではこれまでに、特定の日付から会計期間を特定し、`Qn:Pn (Q1:P10)` という形式で返す例をお見せしました。 次の例では、会計期間の値のカスタム型を定義する方法を紹介します。

カスタム UDT と IFormatter インターフェイスを備えたコードビハインド セクションの例は、以下のとおりです。

```csharp
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

```csharp
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

```usql
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

```csharp
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
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
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

SqlUserDefinedType 属性は、UDAGG の定義では **省略可能** です。


この基底クラスでは、抽象パラメーターを入力に 2 つ、結果に 1 つの計 3 つ渡すことができるようになっています。 データ型は変数であり、クラスの継承時に定義する必要があります。

```csharp
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

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Accumulate の 2 つ目のパラメーター
* T2:Accumulate の 2 つ目のパラメーター
* TResult: Terminate の戻り値の型

次に例を示します。

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

or

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>U-SQL で UDAGG を使用する
UDAGG を使うにはまず、既に説明したようにコードビハインドで定義するか、既存のプログラミング DLL から参照する必要があります。

そのうえで、次の構文を使います。

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

次に UDAGG の例を示します。

```csharp
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

```usql
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

## <a name="next-steps"></a>次のステップ
* [U-SQL プログラミング ガイド - 概要](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL プログラミング ガイド - UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
