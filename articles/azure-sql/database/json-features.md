---
title: JSON データの使用
description: Azure SQL Database と Azure SQL Managed Instance では、JavaScript Object Notation (JSON) 表記法でデータを解析、照会および書式設定できます。
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 4258c60335a13a6c0f27588aac1c6cdb185fcf6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92782802"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database と Azure SQL Managed Instance の JSON 機能の概要
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と Azure SQL Managed Instance では、JavaScript Object Notation [(JSON)](https://www.json.org/) 形式で表されたデータを解析およびクエリし、リレーショナル データを JSON テキストとしてエクスポートすることができます。 次の JSON シナリオを使用できます。

- `FOR JSON` 句を使用した [JSON 形式でのリレーショナル データの書式設定](#formatting-relational-data-in-json-format)。
- [JSON データの使用](#working-with-json-data)
- JSON スカラー関数を使用した [JSON データのクエリの実行](#querying-json-data)。
- `OPENJSON` 関数を使用した[表形式への JSON の変換](#transforming-json-into-tabular-format)。

## <a name="formatting-relational-data-in-json-format"></a>JSON 形式でのリレーショナル データの書式設定

データベース層からデータを取得し、JSON 形式で応答を提供する Web サービスがある場合、またはクライアント側の JavaScript フレームワークまたはライブラリが JSON 形式のデータを受け入れる場合、SQL クエリに直接 JSON としてデータベースの内容を書式設定することができます。 Azure SQL Database または Azure SQL Managed Instance からの結果を JSON 形式にするように、アプリケーション コードを書き込んだり、表形式のクエリの結果に変換して、オブジェクトを JSON 形式にシリアル化するために JSON シリアル化ライブラリを含めたりする必要がなくなりました。 代わりに、FOR JSON 句を使用して、SQL クエリの結果を JSON として書式設定し、アプリケーションで直接これを使用できます。

次の例では、FOR JSON 句を使用して、Sales.Customer テーブルの行を JSON 形式にしています。

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

FOR JSON PATH 句は、クエリの結果を JSON テキストとして書式設定します。 次のように、セルの値が JSON 値として生成される場合、列名はキーとして使用されます。

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

結果セットは、JSON 配列として書式設定され、各行は個別の JSON オブジェクトとして書式設定されます。

PATH は、列の別名にドット表記を使用して、JSON の結果の出力形式をカスタマイズできることを示します。 次のクエリでは、JSON 形式の出力で "CustomerName" キーの名前を変更し、電話番号と FAX 番号を "Contact" サブオブジェクトに入力します。

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

このクエリの出力は次のようになります。

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

この例では、[WITHOUT_ARRAY_WRAPPER](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option) オプションを指定することで、配列ではなく、1 つの JSON オブジェクトを返しています。 クエリの結果として 1 つのオブジェクトを返すことがわかっている場合は、このオプションを使用することができます。

FOR JSON 句の主な値を使用すると、入れ子になった JSON オブジェクトまたは配列として書式設定されたデータベースから複雑な階層データを返すことができます。 次の例では、`Orders` の入れ子になった配列として、`Customer` に属する `Orders` テーブルから行を含める方法を示します。

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Customer データを取得して、関連する Orders のリストを取得するために、個別のクエリを送信する代わりに、次の出力例のように、1 つのクエリで必要なデータをすべて取得することができます。

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>JSON データの使用

厳密に構造化されたデータがない場合、複雑なサブオブジェクト、配列、または階層データがある場合、またはデータ構造が時間と共に進化する場合、JSON 形式を使うと、すべての複雑なデータ構造を表すことができます。

JSON は、Azure SQL Database および Azure SQL Managed Instance の他の文字列型と同様に使用できるテキスト形式です。 JSON データは、標準の NVARCHAR として送信または格納することができます。

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

この例で使用される JSON データは、NVARCHAR(MAX) 型を使用して表されます。 JSON は、次の例のように標準の Transact-SQL 構文を使用して、このテーブルに挿入したり、格納されたプロシージャの引数として指定したりすることができます。

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

また、Azure SQL Database および Azure SQL Managed Instance の文字列データを操作する任意のクライアント側の言語またはライブラリも、JSON データを操作することができます。 JSON は、メモリ最適化テーブルやシステム バージョン管理されたテーブルなど、NVARCHAR 型をサポートする任意のテーブルに格納できます。 JSON は、クライアント側のコードまたはデータベース層のいずれにも、既存の制約を導入しません。

## <a name="querying-json-data"></a>JSON データをクエリする

Azure SQL テーブルに格納された JSON 形式のデータがある場合、JSON 関数では、このデータを任意の SQL クエリで使用できます。

Azure SQL Database および Azure SQL Managed Instance で使用可能な JSON 関数を使用すると、JSON 形式のデータを他の SQL データ型として処理できます。 JSON テキストから簡単に値を抽出し、任意のクエリで JSON データを使用できます。

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

JSON_VALUE 関数は、Data 列に格納された JSON テキストから値を抽出します。 この関数では、JavaScript のようなパスを使用して、抽出する JSON テキストの値を参照します。 抽出された値は、SQL クエリの任意の部分で使用できます。

JSON_QUERY 関数は、JSON_VALUE と同様です。 JSON_VALUE とは異なり、この関数は JSON テキストに配置されている配列やオブジェクトなどの複雑なサブオブジェクトを抽出します。

JSON_MODIFY 関数は、更新する必要がある JSON テキストの値だけでなく、古い値を上書きする新しい値のパスを指定することができます。 この方法では、構造全体を再解析することなく、JSON テキストを簡単に更新できます。

JSON は標準テキストで格納されるため、値が適切に書式設定されたテキスト列に格納される保証はありません。 標準の Azure SQL Database の CHECK 制約および ISJSON 関数を使用すると、JSON 列に格納されたテキストが適切に書式設定されていることを確認できます。

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

入力テキストが適切な JSON 形式である場合、ISJSON 関数は値 1 を返します。 JSON 列を挿入または更新するたびに、この制約は新しいテキスト値が無効な形式の JSON ではないことを確認します。

## <a name="transforming-json-into-tabular-format"></a>JSON を表形式に変換する

Azure SQL Database および Azure SQL Managed Instance では、JSON コレクションを表形式の書式設定に変換し、JSON データの読み込みまたはクエリを行うこともできます。

OPENJSON は、テーブル値関数です。この関数は、JSON テキストの解析、JSON オブジェクトの配列の検索、配列の要素の反復処理、および配列の各要素の出力結果に 1 行を返す操作を行うことができます。

![JSON 表形式](./media/json-features/image_2.png)

上記の例では、開く必要がある JSON 配列を検索する場所 ($.Orders パス)、結果として返される列、およびセルとして返される JSON 値を検索する場所を指定できます。

@orders 変数の JSON 配列を行セットに変換したり、この結果セットを分析したり、標準テーブルに行を挿入したりすることができます。

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

JSON 配列として書式設定され、ストアド プロシージャにパラメーターとして指定される orders のコレクションは、解析され、Orders テーブルに挿入することができます。

## <a name="next-steps"></a>次のステップ

アプリケーションに JSON を統合する方法については、次のリソースを確認してください。

JSON をアプリケーションに統合するためのさまざまなシナリオの詳細については、この [Channel 9 のビデオ](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds)のデモを参照するか、[JSON のブログの投稿](/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases)でお客様のユース ケースに一致するシナリオを見つけてください。