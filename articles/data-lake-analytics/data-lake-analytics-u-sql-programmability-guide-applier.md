---
title: Azure Data Lake の U-SQL ユーザー定義アプライヤー プログラミング ガイド
description: U-SQL UDO プログラミング ガイド - ユーザー定義アプライヤーについて説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512464"
---
# <a name="use-user-defined-applier"></a>ユーザー定義アプライヤーを使用する 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL UDO: ユーザー定義アプライヤー
U-SQL のユーザー定義アプライヤーを使うと、クエリの外部テーブル式によって返される行ごとにカスタム C# 関数を呼び出すことができます。 ユーザー定義アプライヤーは、左側の入力から得られた各行について右側の入力を評価し、生成された行を結合したうえで、最終的な出力とします。 APPLY 演算子により生成される列の一覧は、左側の入力と右側の入力にある列のセットを結合したものになります。


## <a name="how-to-define-and-use-user-defined-applier"></a>ユーザー定義アプライヤーを定義して使用する方法
ユーザー定義アプライヤーは、U-SQL の SELECT 式の一環として呼び出されます。

ユーザー定義アプライヤーの呼び出しの典型例を以下に示します。

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

SELECT 式でのアプライヤーの使用について詳しくは、「[U-SQL SELECT Selecting from CROSS APPLY and OUTER APPLY (U-SQL SELECT における CROSS APPLY と OUTER APPLY を使った選択)](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply)」をご覧ください。

ユーザー定義アプライヤーの基底クラスの定義は、以下のとおりです。

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

ユーザー定義アプライヤーを定義するには、`IApplier` インターフェイスを作成する必要があります。ユーザー定義アプライヤーの定義では、[`SqlUserDefinedApplier`] 属性は省略可能です。

```csharp
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

**SqlUserDefinedApplier** は、ユーザー定義アプライヤーの定義では **省略可能** です。


主なプログラミング オブジェクトは、以下のとおりです。

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

入力行セットは、`IRow` の入力として渡されます。 出力行は、`IUpdatableRow` 出力インターフェイスとして生成されます。

`IRow` のスキーマ メソッドを呼び出すと、個々の列の名前を決めることができます。

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

`IRow` から実際のデータ値を取得するには、`IRow` インターフェイスの Get() メソッドを使います。

```csharp
mycolumn = row.Get<int>("mycolumn")
```

または、スキーマの列名を使います。

```csharp
row.Get<int>(row.Schema[0].Name)
```

出力値は、`IUpdatableRow` 出力で設定する必要があります。

```csharp
output.Set<int>("mycolumn", mycolumn)
```

カスタム アプライヤーでは、`output.Set` メソッドの呼び出しで定義された列および値のみ出力されるという点が重要です。

実際の出力は、`yield return output.AsReadOnly();` を呼び出すとトリガーされます。

ユーザー定義アプライヤーのパラメーターは、コンストラクターに渡すことができます。 アプライヤーは、U-SQL のベース スクリプトでアプライヤーを呼び出すときに定義が必要な列をいくつも返すことができます。

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

ユーザー定義アプライヤーの例を以下に示します。

```csharp
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

```usql
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

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

これは、典型的なタブ区切り (TSV) ファイルです。プロパティ列には製造元、モデルなど、車のプロパティが格納されています。 これらのプロパティは解析のうえ、テーブルの列の形に並べられる必要があります。 ここに示したアプライヤーでは、渡されるパラメーターに基づいて結果の行セットに生成するプロパティの数を動的に変えることができます。 すべてのプロパティを生成することも、プロパティの特定のセットのみを生成することもできます。

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

ユーザー定義アプライヤーは、アプライヤー オブジェクトの新しいインスタンスとして呼び出すことができます。

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

また、ラッパー ファクトリ メソッドを呼び出すことによって呼び出すこともできます。

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>次のステップ
* [U-SQL プログラミング ガイド - 概要](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL プログラミング ガイド - UDT および UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)