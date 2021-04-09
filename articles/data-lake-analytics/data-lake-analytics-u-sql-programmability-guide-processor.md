---
title: Azure Data Lake の U-SQL ユーザー定義プロセッサ プログラミング ガイド
description: U-SQL UDO プログラミング ガイド - ユーザー定義プロセッサについて説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512478"
---
# <a name="use-user-defined-processor"></a>ユーザー定義プロセッサを使用する

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL UDO: ユーザー定義プロセッサ
ユーザー定義プロセッサ (UDP) は U-SQL の UDO の一種で、プログラミング機能を適用して入力行を処理できます。 UDP では、必要に応じて列の結合、値の変更、新しい列の追加が可能です。 UDP は基本的に、行セットを処理して必要なデータ要素を生成する際に役立つものです。

## <a name="how-to-define-and-use-user-defined-processor"></a>ユーザー定義プロセッサを定義して使用する方法
UDP を定義するには、`IProcessor` インターフェイスを作成する必要があります。UDP では、`SqlUserDefinedProcessor` 属性は省略可能です。

このインターフェイスには、次の例に示すように `IRow` インターフェイスの行セット オーバーライドの定義が必要です。

```csharp
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

SqlUserDefinedProcessor 属性は、UDP の定義では **省略可能** です。

主なプログラミング オブジェクトは、**input** と **output** です。 input オブジェクトは、入力列を列挙するために使うものです。これに対して、output はプロセッサのアクティビティの結果としての出力データを設定するために使います。

入力列の列挙には、`input.Get` メソッドを使います。

```csharp
string column_name = input.Get<string>("column_name");
```

`input.Get` メソッドに渡すパラメーターは、U-SQL のベース スクリプトの `PROCESS` ステートメントの `PRODUCE` 句で渡される列です。 ここでは正しいデータ型を使用する必要があります。

出力には、`output.Set` メソッドを使います。

カスタム プロデューサーでは、`output.Set` メソッドの呼び出しで定義された列および値のみ出力されるという点が重要です。

```csharp
output.Set<string>("mycolumn", mycolumn);
```

プロセッサの実際の出力は、`return output.AsReadOnly();` を呼び出すとトリガーされます。

プロセッサの例を以下に示します。

```csharp
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

```usql
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


## <a name="next-steps"></a>次のステップ
* [U-SQL プログラミング ガイド - 概要](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL プログラミング ガイド - UDT および UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)