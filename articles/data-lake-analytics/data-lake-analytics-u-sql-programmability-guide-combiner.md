---
title: Azure Data Lake の U-SQL ユーザー定義コンバイナー プログラミング ガイド
description: U-SQL UDO プログラミング ガイド - ユーザー定義コンバイナーについて説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512479"
---
# <a name="use-user-defined-combiner"></a>ユーザー定義コンバイナーを使用する

## <a name="u-sql-udo-user-defined-combiner"></a>U-SQL UDO: ユーザー定義コンバイナー
ユーザー定義コンバイナー (UDC) を使うと、カスタム ロジックに基づいて左右の行セットの行を結合できます。 ユーザー定義コンバイナーは、COMBINE 式で使用します。

## <a name="how-to-define-and-use-user-defined-combiner"></a>ユーザー定義コンバイナーを定義して使用する方法

COMBINE 式でコンバイナーが呼び出されると、左右の入力行セット、グループ化列、予期される結果スキーマに関する必要な情報のほか、追加の情報が提供されます。

U-SQL のベース スクリプトでコンバイナーを呼び出すには、以下の構文を使います。

```usql
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

```csharp
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

```csharp
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

```csharp
mycolumn = row.Get<int>("mycolumn")
```

`IRow` のスキーマ メソッドを呼び出すと、個々の列の名前を決めることができます。

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

または、スキーマの列名を使います。

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

LINQ の一般的な列挙型は、以下のようになります。

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

両方の行セットを列挙した後、すべての行をループします。 左側の行セットにある行それぞれについて、コンバイナーの条件を満たす行をすべて探し出します。

出力値は、`IUpdatableRow` 出力で設定する必要があります。

```csharp
output.Set<int>("mycolumn", mycolumn)
```

実際の出力は、`yield return output.AsReadOnly();` を呼び出すとトリガーされます。

コンバイナーの例を以下に示します。

```csharp
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

```csharp
USING new MyNameSpace.MyCombiner();
```


また、ラッパー ファクトリ メソッドを呼び出すことによって呼び出すこともできます。

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>次のステップ
* [U-SQL プログラミング ガイド - 概要](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL プログラミング ガイド - UDT および UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)