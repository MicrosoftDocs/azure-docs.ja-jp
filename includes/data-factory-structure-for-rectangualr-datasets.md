---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572374"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>四角形のデータセットの構造定義を指定する
データセット JSON の structure セクションは、(行と列がある) 四角形のテーブルの**省略可能**なセクションです。テーブルの列のコレクションが含まれています。 structure セクションは、型変換のために型情報を提供したり、列マッピングを実行したりするために使用されます。 次のセクションでは、これらの機能の詳細について説明します。 

各列には次のプロパティが含まれます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| name |列の名前です。 |はい |
| type |列のデータ型です。 型情報を指定する必要がある場合の詳細については、以下の型変換のセクションを参照してください。 |いいえ  |
| culture |型を指定するときに使用される .NET ベースのカルチャ。.NET 型の Datetime または Datetimeoffset です。 既定値は “en-us” です。 |いいえ  |
| format |型を指定するときに使用される書式指定文字列。.NET 型の Datetime または Datetimeoffset です。 |いいえ  |

次に、userid、name、および lastlogindate という 3 つの列があるテーブルの structure セクション JSON の例を示します。

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

"structure" 情報を含める場合と、 **structure** セクションに含める内容については、次のガイドラインに従ってください。

* **構造化データ ソースの場合** (SQL Server、Oracle、Azure テーブルなどのソース)、"structure" セクションを指定する必要があるのは、特定のソース列をシンク内の特定の列とマップし、それらの列名が同じではない場合のみです (詳細については、後述する列マッピングのセクションを参照してください)。 
  
    前述のように、"structure" セクションの型情報は省略可能です。 構造化ソースの場合、型情報はデータ ストアのデータセット定義の一部として既に使用可能なので、"structure" セクションを含める場合に型情報を含めないでください。
* **読み取りデータ ソースのスキーマの場合 (具体的には Azure BLOB)**、データと共にスキーマや型情報を保存せずに、データを保存することができます。 このような種類のデータ ソースでは、次の 2 つの場合に "structure" を含める必要があります。
  * 列マッピングを実行する場合。
  * データセットがコピー アクティビティのソースの場合、"structure" で型情報を提供できます。Data Factory ではシンクのネイティブ型への変換にその型情報を使用します。 詳細については、[Azure BLOB との間のデータの移動](../articles/data-factory/v1/data-factory-azure-blob-connector.md)に関する記事を参照してください。

### <a name="supported-net-based-types"></a>サポートされる .NET ベースの型
Data Factory は、Azure BLOB などの読み取りデータ ソースでスキーマに "structure" で型情報を提供する場合、次の CLS に準拠している .NET ベースの型値をサポートしています。

* Int16
* Int32 
* Int64
* Single
* Double
* Decimal
* Byte[]
* ブール値
* String 
* Guid
* Datetime
* Datetimeoffset
* Timespan 

Datetime と Datetimeoffset の場合、必要に応じて “culture” と “format” 文字列を指定して、カスタムの Datetime 文字列の解析に利用することができます。 型変換については、以下のサンプルを参照してください。

