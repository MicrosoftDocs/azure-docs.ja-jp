---
title: Azure Data Lake の U-SQL ユーザー定義アウトプッター プログラミング ガイド
description: U-SQL UDO プログラミング ガイドのユーザー定義アウトプッターについて説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512463"
---
# <a name="use-user-defined-outputter"></a>ユーザー定義アウトプッターを使用する

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL UDO: ユーザー定義アウトプッター
ユーザー定義アウトプッターは、U-SQL の組み込み機能を拡張するもう 1 つの U-SQL UDO です。 エクストラクターと同じく、アウトプッターも組み込みのものがいくつか用意されています。

* *Outputters.Text()* : エンコードがさまざまに異なる区切りテキスト ファイルにデータを書き込みます。
* *Outputters.Csv()* : エンコードがさまざまに異なるコンマ区切り値 (CSV) ファイルにデータを書き込みます。
* *Outputters.Tsv()* : エンコードがさまざまに異なるタブ区切り値 (TSV) ファイルにデータを書き込みます。

カスタム アウトプッターでは、独自に定義した形式でデータを書き込むことができます。 これは、以下のタスクに役に立つ場合があります。

* 半構造化ファイルまたは非構造化ファイルにデータを書き込む。
* サポートされていないエンコーディングのデータを書き込む。
* 出力データを変更したり、カスタム属性を追加したりする。

## <a name="how-to-define-and-use-user-defined-outputter"></a>ユーザー定義アウトプッターを定義して使用する方法
ユーザー定義アウトプッターを定義するには、`IOutputter` インターフェイスを作成する必要があります。

基底クラスとなる `IOutputter` の実装を以下に示します。

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

列や行の区切り記号、エンコーディングなど、アウトプッターに対する入力パラメーターはすべて、クラスのコンストラクターで定義する必要があります。 `IOutputter` インターフェイスにはほかにも、`void Output` オーバーライドの定義が必要です。 アトミック ファイルの処理に関しては、オプションで属性 `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` を設定できます。 詳しくは、次の詳細をご覧ください。

```csharp
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

```csharp
row.Get<string>("column_name")
```

`row.Schema` を呼び出すと、個々の列の名前を決めることができます。

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

この方法を使えば、メタデータのスキーマを問わず対応できる柔軟なアウトプッターを作成することができます。

出力データは、`System.IO.StreamWriter` を使ってファイルに書き込まれます。 ストリームのパラメーターは `IUnstructuredWriter output` の一部として `output.BaseStream` に設定されます。

行の反復処理が 1 回終わるごとにデータ バッファーをファイルにフラッシュすることが重要です。 また、`StreamWriter` オブジェクトは、Disposable 属性を有効にした状態 (既定の設定) で、**using** キーワードと共に使う必要があります。

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

または、反復処理が 1 回終わるごとに Flush() メソッドを明示的に呼び出します。 これについては、次の例で示します。

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>ユーザー定義アウトプッターにヘッダーとフッターを設定する
ヘッダーを設定するには、1 回限りの反復実行フローを使用します。

```csharp
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

```csharp
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

```usql
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

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>次のステップ
* [U-SQL プログラミング ガイド - 概要](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL プログラミング ガイド - UDT および UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)