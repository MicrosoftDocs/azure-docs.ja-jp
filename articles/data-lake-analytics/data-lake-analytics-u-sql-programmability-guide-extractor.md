---
title: Azure Data Lake の U-SQL ユーザー定義エクストラクター プログラミング ガイド
description: U-SQL UDO プログラミング ガイド - ユーザー定義エクストラクターについて説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: ad7f6336753903533771033de21aec8262425a61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608019"
---
# <a name="use-user-defined-extractor"></a>ユーザー定義エクストラクターを使用する

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL UDO: ユーザー定義エクストラクター
U-SQL では、EXTRACT ステートメントを使って外部データをインポートできます。 EXTRACT ステートメントでは、組み込みの UDO エクストラクターを使用できます。  

* *Extractors.Text()* : エンコードがさまざまに異なる区切りテキスト ファイルからの抽出に対応します。

* *Extractors.Csv()* : エンコードがさまざまに異なるコンマ区切り値 (CSV) ファイルからの抽出に対応します。

* *Extractors.Tsv()* : エンコードがさまざまに異なるタブ区切り値 (TSV) ファイルからの抽出に対応します。

カスタム エクストラクターを開発すると便利なことがあります。 具体的には、データのインポート中に以下のいずれかのタスクが必要な場合に便利です。

* 列を分割し、個々の値を変更する形で入力データに変更を加える。 列の結合については、プロセッサ機能の方が適しています。
* Web ページ、メールなどの非構造化データまたは XML、JSON などの半構造化データを解析する。
* サポート対象外のエンコーディングが行われたデータを解析する。

## <a name="how-to-define-and-use-user-defined-extractor"></a>ユーザー定義エクストラクターを定義して使用する方法
ユーザー定義エクストラクター (UDE) を定義するには、`IExtractor` インターフェイスを作成する必要があります。 列や行の区切り記号、エンコーディングなど、エクストラクターに対する入力パラメーターはすべて、クラスのコンストラクターで定義する必要があります。 `IExtractor` インターフェイスにはほかにも、次のような `IEnumerable<IRow>` オーバーライドの定義が必要です。

```csharp
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

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

次に、入力行を列の部分に分割します。

```csharp
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

```csharp
output.Set<string>(count, part);
```

エクストラクターの実際の出力は、`yield return output.AsReadOnly();` を呼び出すとトリガーされます。

エクストラクターの例を以下に示します。

```csharp
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

```usql
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


## <a name="next-steps"></a>次のステップ
* [U-SQL プログラミング ガイド - 概要](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL プログラミング ガイド - UDT および UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)