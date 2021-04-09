---
title: Azure Data Lake の U-SQL ユーザー定義レジューサー プログラミング ガイド
description: U-SQL UDO プログラミング ガイド - ユーザー定義レジューサーについて説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512460"
---
# <a name="use-user-defined-reducer"></a>ユーザー定義レジューサーを使用する

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL UDO: ユーザー定義レジューサー

U-SQL では、C# でユーザー定義演算子機能拡張フレームワークを使い、IReducer インターフェイスを実装することで、カスタム行セット レジューサーを作成できます。

ユーザー定義レジューサー (UDR) を使えば、データの抽出 (インポート) 時に不要な行を除去できます。 行や列を操作および評価するために使うこともできます。 プログラミング ロジックに基づいて、抽出が必要な行を定義することもできます。

## <a name="how-to-define-and-use-user-defined-reducer"></a>ユーザー定義レジューサーを定義して使用する方法
UDR クラスを定義するには、オプションで `SqlUserDefinedReducer` 属性を設定した `IReducer` インターフェイスを作成する必要があります。

このクラス インターフェイスには、`IEnumerable` インターフェイスの行セット オーバーライドの定義が必要です。

```csharp
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

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

`Row.Get` メソッドに渡すパラメーターは、U-SQL のベース スクリプトの `REDUCE` ステートメントの `PRODUCE` クラスで渡される列です。 ここでも、正しいデータ型を使用する必要があります。

出力には、`output.Set` メソッドを使います。

カスタム レジューサーでは、`output.Set` メソッドの呼び出しで定義された値のみ出力されるという点が重要です。

```csharp
output.Set<string>("mycolumn", guid);
```

レジューサーの実際の出力は、`yield return output.AsReadOnly();` を呼び出すとトリガーされます。

レジューサーの例を以下に示します。

```csharp
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

```usql
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

## <a name="next-steps"></a>次のステップ
* [U-SQL プログラミング ガイド - 概要](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL プログラミング ガイド - UDT および UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)