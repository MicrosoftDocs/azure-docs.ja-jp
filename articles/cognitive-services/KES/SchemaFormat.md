---
title: スキーマ形式 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Knowledge Exploration Service (KES) API のスキーマ形式について説明します。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 51a812762659bcc67762b82e9c120772065aab53
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549689"
---
# <a name="schema-format"></a>スキーマ形式

スキーマは、インデックスの作成に使用されるデータ ファイル内のオブジェクトの属性構造を記述した JSON ファイルに指定します。  スキーマでは、各属性について名前、データ型、オプションの操作、およびオプションのシノニム リストを指定します。  オブジェクトは、各属性の値を 0 個または複数持つことができます。  学術的刊行物ドメインを簡略化した例を次に示します。

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

属性名は、大文字と小文字が区別される識別子です。英字で始まり、英字 (A から Z)、数字 (0 から 9)、およびアンダースコア (\_) のみで構成されます。  予約済みの "logprob" 属性は、オブジェクト間の相対的な自然対数確率を指定するために使用されます。

## <a name="attribute-type"></a>属性の種類

サポートされている属性のデータ型のリストを次に示します。

| Type | 説明 | Operations | 例 |
|------|-------------|------------|---------|
| `String` | 文字列 (1 から 1024 文字) | equals、starts_with | "hello world" |
| `Int32` | 符号付き 32 ビット整数 | equals、starts_with、is_between | 2016 |
| `Int64` | 符号付き 64 ビット整数 | equals、starts_with、is_between | 9876543210 |
| `Double` | 倍精度浮動小数点数値 | equals、starts_with、is_between | 1.602e-19 |
| `Date` | 日付 (1400-01-01 から 9999-12-31) | equals、is_between | '2016-03-14' |
| `Guid` | グローバル一意識別子 | equals | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| `Blob` | 内部的に圧縮された非インデックス データ | *なし* | "Empower every person and every organization on the planet to achieve more" |
| `Composite` | 複数のサブ属性の複合| *該当なし* | { "Name":"harry shum", "Affiliation":"microsoft" } |

String 属性は、ユーザー クエリの一部として表示することができる文字列値を表すために使用されます。  完全一致の *equals* 演算と、"micros" と "microsoft" の一致など、クエリ入力候補シナリオの *starts_with* 演算をサポートします。  誤植を処理するための大文字と小文字を区別しないファジー一致は、今後のリリースでサポートされる予定です。

Int32/Int64/Double 属性は、数値を表すために使用されます。  *is_between* 演算では、実行時に不等式のサポート (lt、le、gt、ge) が有効になります。  *starts_with* 演算は、クエリ入力候補シナリオをサポートします。たとえば、"20" と "2016" の一致や、"3." と "3.14" の一致などです。

Date 属性は、日付値を効率的にエンコードするために使用されます。  *is_between* 演算では、実行時に不等式のサポート (lt、le、gt、ge) が有効になります。
  
Guid 属性は、*equals* 演算の既定のサポートで GUID 値を効率的に表すために使用されます。

Blob 属性は、BLOB 値の内容に基づくインデックス付け操作をサポートせずに、対応するオブジェクトからの実行時ルックアップのために大きい可能性があるデータ BLOB を効率的にエンコードするために使用されます。

### <a name="composite-attributes"></a>複合属性

複合属性は、属性値のグループを表すために使用されます。  各サブ属性の名前は、複合属性の名前から始まり、" " が続きます。  複合属性の値は、入れ子にされた属性値を含む JSON オブジェクトとして指定されます。  複合属性は複数のオブジェクト値を持つことができます。  ただし、複合属性は、それ自体が複合属性であるサブ属性を持つことはできません。

前述の学術的刊行物の例では、複合属性をサービスに利用して、"harry shum" が "microsoft" に在籍中に執筆した論文のクエリを実行することができます。  複合属性を使用しない場合、著者の 1 人が "harry shum" で、著者の 1 人が "microsoft" に在籍している論文のクエリのみを実行できます。  詳細については、[クエリの作成](SemanticInterpretation.md#composite-function)に関する記事を参照してください。

## <a name="attribute-operations"></a>属性の演算

既定で各属性には、その属性のデータ型に使用できるすべての演算をサポートするインデックスが付けられています。  特定の演算が不要な場合は、インデックス付き演算のセットを明示的に指定してインデックスのサイズを減らすことができます。  前述のスキーマ例の次のスニペットでは、Author.Id 属性は、Int32 属性の追加の *starts_with* 演算と *is_between* 演算はサポートせず、*equals* 演算のみをサポートするようにインデックス付けされています。
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

属性が文法内で参照される場合、サービスが部分的なクエリから入力候補を生成するために、スキーマに *starts_with* 演算を指定する必要があります。  

## <a name="attribute-synonyms"></a>属性のシノニム

特定の文字列属性値をシノニムで参照する方が望ましい場合があります。  たとえば、ユーザーは "Microsoft" を "MSFT" や "MS" と参照することがあります。  このような場合、属性定義では、スキーマ ファイルと同じディレクトリにあるスキーマ ファイルの名前を指定できます。  シノニム ファイルの各行で、次の JSON 形式のシノニム エントリを表します。`["<canonical>", "<synonym>"]`  このスキーマ例では、"AuthorName.syn" は、Author.Name 属性のシノニム値を含む JSON ファイルです。

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


正規値には複数のシノニムが含まれる可能性があります。  複数の正規値が 1 つの共通シノニムを共有する可能性があります。  このような場合、サービスは複数の解釈を使用してあいまいさを解決します。  前述のスキーマに対応する AuthorName.syn シノニム ファイルの例を次に示します。
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
