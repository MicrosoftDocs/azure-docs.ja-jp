---
title: Azure Data Factory の式と関数 | Microsoft Docs
description: この記事では、Data Factory エンティティの作成で使用できる式と関数に関する情報を提供します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: d48d9e89085e08ac4da9db15458e3a3aa8152bb5
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541217"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Azure Data Factory の式と関数
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [現在のバージョン](control-flow-expression-language-functions.md)

この記事では、Azure Data Factory によってサポートされている式と関数に関する詳細情報を提供します。 

## <a name="introduction"></a>はじめに
定義内の JSON 値には、実行時に評価されるリテラルまたは式を指定できます。 例:  
  
```json
"name": "value"
```

 (または)  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>式  
式は、JSON string 値内の任意の場所で使うことができ、常に別の JSON 値になります。 JSON 値が式である場合は、アットマーク (\@) を削除することによって式の本体が抽出されます。 \@ で始まるリテラル文字列が必要な場合は、\@\@ を使用してその文字列をエスケープする必要があります。 式の評価方法の例を次に示します。  
  
|JSON 値|結果|  
|----------------|------------|  
|"parameters"|文字 "parameters" が返されます。|  
|"parameters [1]"|文字 "parameters[1]" が返されます。|  
|"\@\@"|'\@' を含む 1 文字の文字列が返されます。|  
|" \@"|' \@' を含む 2 文字の文字列が返されます。|  
  
 また、式が `@{ ... }` にラップされる*文字列補間*と呼ばれる機能を使用して、式を文字列の内部に指定することもできます。 次に例を示します。`"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 文字列補間を使用すると、結果は常に文字列になります。 `myNumber` を `42` として、`myString` を `foo` として定義したとします。  
  
|JSON 値|結果|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| `foo` が文字列として返されます。|  
|"\@{pipeline().parameters.myString}"| `foo` が文字列として返されます。|  
|"\@pipeline().parameters.myNumber"| `42` が "*数値*" として返されます。|  
|"\@{pipeline().parameters.myNumber}"| `42` が "*string*" として返されます。|  
|"Answer is: @{pipeline().parameters.myNumber}"| string `Answer is: 42` が返されます。|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| string `Answer is: 42` が返されます。|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| string `Answer is: @{pipeline().parameters.myNumber}` が返されます。|  
  
### <a name="examples"></a>例

#### <a name="a-dataset-with-a-parameter"></a>パラメーターを備えたデータセット
次の例では、BlobDataset は **path** という名前のパラメーターを受け取ります。 その値は、`dataset().path` という式を使用して **folderPath** プロパティの値を設定するために使用されます。 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>パラメーターを備えたパイプライン
次の例では、パイプラインは **inputPath** パラメーターと **outputPath** パラメーターを受け取ります。 パラメーター化された BLOB データセットの **path** は、これらのパラメーターの値を使用して設定されます。 ここで使用する構文は `pipeline().parameters.parametername` です。 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
#### <a name="tutorial"></a>チュートリアル
この[チュートリアル](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf)では、パイプラインとアクティビティ間、およびアクティビティ間でパラメーターを渡す方法について説明します。

  
## <a name="functions"></a>Functions  
 式の中で関数を呼び出すことができます。 以降のセクションでは、式で使用できる関数に関する情報を提供します。  

## <a name="string-functions"></a>文字列関数  
 次の関数は、文字列にのみ適用されます。 文字列では、いくつかのコレクション関数を使用することもできます。  
  
|関数名|説明|  
|-------------------|-----------------|  
|concat|任意の数の文字列を結合します。 たとえば、パラメーター 1 が `foo,` である場合、次の式は `somevalue-foo-somevalue` を返します: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **パラメーター番号**: 1 ... *n*<br /><br /> **[名前]** :String *n*<br /><br /> **説明**:必須。 1 つの文字列に結合する文字列です。|  
|substring|文字列から文字のサブセットを返します。 たとえば、次の式<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> は次を返します。<br /><br /> `foo`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 部分文字列を取得する文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Start index<br /><br /> **説明**:必須。 パラメーター 1 で取得する部分文字列の開始位置のインデックスです。<br /><br /> **パラメーター番号**: 3<br /><br /> **[名前]** :Length<br /><br /> **説明**:必須。 部分文字列の長さです。|  
|replace|文字列を指定された文字列に置き換えます。 たとえば、式<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> は次を返します。<br /><br /> `the new string`<br /><br /> **パラメーター番号**: 1<br /><br /> **名前**: string<br /><br /> **説明**:必須。  パラメーター 1 の中にパラメーター 2 が見つかった場合、パラメーター 2 が検索された文字列であり、パラメーター 3 で更新されます。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Old string<br /><br /> **説明**:必須。 パラメーター 1 の中に一致が見つかったときに、パラメーター 3 に置き換える文字列<br /><br /> **パラメーター番号**: 3<br /><br /> **[名前]** :New string<br /><br /> **説明**:必須。 パラメーター 1 でパラメーター 2 と一致する文字列が見つかった場合、この文字列に置き換えます。|  
|guid| グローバルに一意の文字列 (GUID とも呼ばれます) を生成します。 たとえば、次の出力が生成されることがあります: `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`<br /><br /> `guid()`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :形式<br /><br /> **説明**:省略可能。 [この GUID の値の書式設定方法](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx)を示す単一の書式設定指定子です。 指定できる書式設定パラメーターは、"N"、"D"、"B"、"P"、"X" です。 指定しないと、"D" が使われます。|  
|toLower|文字列を小文字に変換します。 たとえば、次は `two by two is four` を返します: `toLower('Two by Two is Four')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 小文字に変換する文字列です。 文字列内の文字に小文字の対応する文字がない場合、その文字は返される文字列に変更されずに含まれます。|  
|toUpper|文字列を大文字に変換します。 たとえば、次の式は `TWO BY TWO IS FOUR` を返します: `toUpper('Two by Two is Four')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 大文字に変換する文字列です。 文字列内の文字に大文字の対応する文字がない場合、その文字は返される文字列に変更されずに含まれます。|  
|indexof|文字列内で値のインデックスを探します。大文字と小文字は区別されません。 たとえば、次の式は `7` を返します: `indexof('hello, world.', 'world')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 値を含む可能性のある文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 インデックスを探す値です。|  
|lastindexof|文字列内で値の最後のインデックスを探します。大文字と小文字は区別されません。 たとえば、次の式は `3` を返します: `lastindexof('foofoo', 'foo')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 値を含む可能性のある文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 インデックスを探す値です。|  
|startswith|文字列が値で始まっているかどうかを調べます。大文字と小文字は区別されません。 たとえば、次の式は `true` を返します: `startswith('hello, world', 'hello')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 値を含む可能性のある文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 文字列が始まっている可能性のある値です。|  
|endswith|文字列が値で終わっているかどうかを調べます。大文字と小文字は区別されません。 たとえば、次の式は `true` を返します: `endswith('hello, world', 'world')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 値を含む可能性のある文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 文字列が終わっている可能性のある値です。|  
|split|区切り記号を使って文字列を分割します。 たとえば、次の式は `["a", "b", "c"]` を返します: `split('a;b;c',';')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 分割する文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 区切り記号です。|  
  
  
## <a name="collection-functions"></a>コレクション関数  
 これらの関数は、配列、文字列、場合によっては辞書などのコレクションに対して動作します。  
  
|関数名|説明|  
|-------------------|-----------------|  
|contains|ディクショナリにキーが含まれる場合、リストに値が含まれる場合、または文字列に部分文字列が含まれる場合、true を返します。 たとえば、次の式は `true:``contains('abacaba','aca')` を返します<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Within collection<br /><br /> **説明**:必須。 その中で検索を行うコレクションです。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Find object<br /><br /> **説明**:必須。 **Within collection** 内で検索するオブジェクトです。|  
|length|配列または文字列内の要素の数を返します。 たとえば、次の式は `3` を返します: `length('abc')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :コレクション<br /><br /> **説明**:必須。 次の長さを取得するコレクション。|  
|empty|オブジェクト、配列、または文字列が空の場合は true を返します。 たとえば、次の式は `true` を返します:<br /><br /> `empty('')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :コレクション<br /><br /> **説明**:必須。 空かどうかを確認するコレクションです。|  
|intersection|渡された配列またはオブジェクトの間の共通要素を含む 1 つの配列またはオブジェクトを返します。 たとえば、次の関数は `[1, 2]` を返します。<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> 関数のパラメーターは、オブジェクトのセットまたは配列のセットです (両方を混ぜることはできません)。 同じ名前のオブジェクトが複数ある場合は、その名前を持つ最後のオブジェクトが最終的なオブジェクトに含まれます。<br /><br /> **パラメーター番号**: 1 ... *n*<br /><br /> **[名前]** :Collection *n*<br /><br /> **説明**:必須。 評価対象のコレクションです。 結果にオブジェクトが含まれるには、渡されるすべてのコレクションにオブジェクトが存在する必要があります。|  
|union|渡された配列またはオブジェクトにあるすべての要素を含む 1 つの配列またはオブジェクトを返します。 たとえば、この関数は `[1, 2, 3, 10, 101]:` を返します<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> 関数のパラメーターは、オブジェクトのセットまたは配列のセットです (両方を混ぜることはできません)。 最終的な出力に同じ名前のオブジェクトが複数ある場合は、その名前を持つ最後のオブジェクトが最終的なオブジェクトに含まれます。<br /><br /> **パラメーター番号**: 1 ... *n*<br /><br /> **[名前]** :Collection *n*<br /><br /> **説明**:必須。 評価対象のコレクションです。 いずれかのコレクションに現れるオブジェクトは、結果に含まれます。|  
|first|渡された配列または文字列の最初の要素を返します。 たとえば、次の関数は `0` を返します。<br /><br /> `first([0,2,3])`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :コレクション<br /><br /> **説明**:必須。 最初のオブジェクトを取得するコレクションです。|  
|last|渡された配列または文字列の最後の要素を返します。 たとえば、次の関数は `3` を返します。<br /><br /> `last('0123')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :コレクション<br /><br /> **説明**:必須。 最後のオブジェクトを取得するコレクションです。|  
|take|渡された配列または文字列の最初の **Count** 個の要素を返します。たとえば、この関数は `[1, 2]` を返します: `take([1, 2, 3, 4], 2)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :コレクション<br /><br /> **説明**:必須。 最初の **Count** 個のオブジェクトを取得するコレクション。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Count<br /><br /> **説明**:必須。 **Collection** から取得するオブジェクトの数です。 正の整数にする必要があります。|  
|skip|インデックス **Count** から始まる配列内の要素を返します。たとえば、この関数は `[3, 4]` を返します。<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :コレクション<br /><br /> **説明**:必須。 最初の **Count** 個のオブジェクトをスキップするコレクションです。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Count<br /><br /> **説明**:必須。 **Collection** の先頭から除去するオブジェクトの数です。 正の整数にする必要があります。|  
  
## <a name="logical-functions"></a>論理関数  
 これらの関数は条件の内部で役立ち、任意の種類のロジックを評価するために使用できます。  
  
|関数名|説明|  
|-------------------|-----------------|  
|equals|2 つの値が等しい場合、true を返します。 たとえば、パラメーター 1 が foo である場合、次の式は `true` を返します: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Object 1<br /><br /> **説明**:必須。 **Object 2** と比較するオブジェクトです。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Object 2<br /><br /> **説明**:必須。 **Object 1** と比較するオブジェクトです。|  
|less|1 番目の引数が 2 番目の引数より小さい場合、true を返します。 値として指定できる型は integer、float、string だけであることに注意してください。 たとえば、次の式は `true` を返します: `less(10,100)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Object 1<br /><br /> **説明**:必須。 **Object 2** より小さいかどうかを調べるオブジェクトです。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Object 2<br /><br /> **説明**:必須。 **Object 1** より大きいかどうかを調べるオブジェクトです。|  
|lessOrEquals|1 番目の引数が 2 番目の引数より小さいか等しい場合、true を返します。 値として指定できる型は integer、float、string だけであることに注意してください。 たとえば、次の式は `true` を返します: `lessOrEquals(10,10)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Object 1<br /><br /> **説明**:必須。 **Object 2** 以下かどうかを調べるオブジェクトです。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Object 2<br /><br /> **説明**:必須。 **Object 1** 以上かどうかを調べるオブジェクトです。|  
|greater|1 番目の引数が 2 番目の引数より大きい場合、true を返します。 値として指定できる型は integer、float、string だけであることに注意してください。 たとえば、次の式は `false` を返します: `greater(10,10)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Object 1<br /><br /> **説明**:必須。 **Object 2** より大きいかどうかを調べるオブジェクトです。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Object 2<br /><br /> **説明**:必須。 **Object 1** より小さいかどうかを調べるオブジェクトです。|  
|greaterOrEquals|1 番目の引数が 2 番目の引数より大きいか等しい場合、true を返します。 値として指定できる型は integer、float、string だけであることに注意してください。 たとえば、次の式は `false` を返します: `greaterOrEquals(10,100)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Object 1<br /><br /> **説明**:必須。 **Object 2** 以上かどうかを調べるオブジェクトです。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Object 2<br /><br /> **説明**:必須。 **Object 1** 以下かどうかを調べるオブジェクトです。|  
|and|両方のパラメーターが true である場合は、true を返します。 両方の引数は、ブール値である必要があります。 次は `false` を返します: `and(greater(1,10),equals(0,0))`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Boolean 1<br /><br /> **説明**:必須。 `true` でなければならない 1 番目の引数です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Boolean 2<br /><br /> **説明**:必須。 `true` でなければならない 2 番目の引数です。|  
|or|どちらかのパラメーターが true である場合は、true を返します。 両方の引数は、ブール値である必要があります。 次は `true` を返します: `or(greater(1,10),equals(0,0))`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Boolean 1<br /><br /> **説明**:必須。 `true` になることができる 1 番目の引数です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Boolean 2<br /><br /> **説明**:必須。 `true` になることができる 2 番目の引数です。|  
|not|パラメーターが `false` である場合は、true を返します。 両方の引数は、ブール値である必要があります。 次は `true` を返します: `not(contains('200 Success','Fail'))`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Boolean<br /><br /> **説明**:パラメーターが `false` である場合は、true を返します。 両方の引数は、ブール値である必要があります。 次は `true` を返します: `not(contains('200 Success','Fail'))`|  
|if|指定された式が `true` または `false` のどちらになるかに基づいて、指定された値を返します。  たとえば、次は `"yes"` を返します: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :式<br /><br /> **説明**:必須。 式によってどの値が返されるかを決定するブール値。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :True<br /><br /> **説明**:必須。 式が `true` の場合に返す値です。<br /><br /> **パラメーター番号**: 3<br /><br /> **[名前]** :False<br /><br /> **説明**:必須。 式が `false` の場合に返す値です。|  
  
## <a name="conversion-functions"></a>変換関数  
 これらの関数は、言語の各ネイティブ型の間の変換に使われます。  
  
-   string  
  
-   integer  
  
-   float  
  
-   ブール値  
  
-   arrays  
  
-   dictionaries  
  
|関数名|説明|  
|-------------------|-----------------|  
|int|パラメーターを整数に変換します。 たとえば、次の式は文字列ではなく、数値として 100 を返します: `int('100')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :値<br /><br /> **説明**:必須。 整数に変換する値です。|  
|string|パラメーターを文字列に変換します。 たとえば、次の式は `'10'` を返します: `string(10)`。また、オブジェクトを文字列に変換することもできます。たとえば、**foo** パラメーターが 1 つのプロパティ `bar : baz` を備えたオブジェクトである場合、次は `{"bar" : "baz"}` `string(pipeline().parameters.foo)` を返します<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :値<br /><br /> **説明**:必須。 文字列に変換する値です。|  
|json|パラメーターを JSON 型の値に変換します。 これは、string() の反対です。 たとえば、次の式は文字列ではなく、配列として `[1,2,3]` を返します。<br /><br /> `json('[1,2,3]')`<br /><br /> 同様に、文字列をオブジェクトに変換することもできます。 たとえば、`json('{"bar" : "baz"}')` は次を返します。<br /><br /> `{ "bar" : "baz" }`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 ネイティブな型の値に変換する文字列です。<br /><br /> json 関数は、xml 入力もサポートします。 たとえば、次のようなパラメーター値があるものとします。<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> 次の json に変換されます。<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|パラメーター引数を浮動小数点数に変換します。 たとえば、次の式は `10.333` を返します: `float('10.333')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :値<br /><br /> **説明**:必須。 浮動小数点数に変換する値です。|  
|bool|パラメーターをブール値に変換します。 たとえば、次の式は `false` を返します: `bool(0)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :値<br /><br /> **説明**:必須。 ブール値に変換する値です。|  
|coalesce|渡された引数で最初の null 以外のオブジェクトを返します。 注: 空の文字列は null ではありません。 たとえば、パラメーター 1 と 2 が定義されていない場合、これは `fallback` を返します: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **パラメーター番号**: 1 ... *n*<br /><br /> **[名前]** :Object*n*<br /><br /> **説明**:必須。 `null` をチェックするオブジェクト。|  
|base64|入力文字列の base64 表現を返します。 たとえば、次の式は `c29tZSBzdHJpbmc=` を返します: `base64('some string')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :String 1<br /><br /> **説明**:必須。 base 64 表現にエンコードする文字列です。|  
|base64ToBinary|base64 でエンコードされた文字列のバイナリ表現を返します。 たとえば、次の式は一部の文字列のバイナリ表現を返します: `base64ToBinary('c29tZSBzdHJpbmc=')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 base64 でエンコードされた文字列です。|  
|base64ToString|base64 でエンコードされた文字列の文字列表現を返します。 たとえば、次の式は一部の文字列を返します: `base64ToString('c29tZSBzdHJpbmc=')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 base64 でエンコードされた文字列です。|  
|Binary|値のバイナリ表現を返します。  たとえば、次の式は一部の文字列のバイナリ表現を返します: `binary(‘some string’).`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :値<br /><br /> **説明**:必須。 バイナリに変換する値です。|  
|dataUriToBinary|データの URI のバイナリ表現を返します。 たとえば、次の式は一部の文字列のバイナリ表現を返します: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 バイナリ表現に変換するデータ URI です。|  
|dataUriToString|データの URI の文字列表現を返します。 たとえば、次の式は一部の文字列を返します: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br />**説明**:必須。 文字列表現に変換するデータ URI です。|  
|dataUri|値のデータ URI を返します。 たとえば、次の式はデータを返します: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **パラメーター番号**: 1<br /><br />**[名前]** :値<br /><br />**説明**:必須。 データ URI に変換する値です。|  
|decodeBase64|入力の base64 文字列の文字列表現を返します。 たとえば、次の式は `some string` を返します: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:入力の base64 文字列の文字列表現を返します。|  
|encodeUriComponent|渡される URL でエスケープされた文字列です。 たとえば、次の式は `You+Are%3ACool%2FAwesome` を返します: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 URL の安全でない文字をエスケープする文字列です。|  
|decodeUriComponent|渡された文字列の URL エスケープを解除します。 たとえば、次の式は `You Are:Cool/Awesome` を返します: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 URL の安全でない文字をデコードする文字列です。|  
|decodeDataUri|入力データの URI 文字列のバイナリ表現を返します。 たとえば、次の式は `some string` のバイナリ表現を返します: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br /> **説明**:必須。 バイナリ表現にデコードする dataURI です。|  
|uriComponent|値の URI でエンコードされた表現を返します。 たとえば、次の式は `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')` を返します<br /><br /> パラメーターの詳細: 番号: 1、名前: String、説明: 必須。 URI でエンコードする文字列です。|  
|uriComponentToBinary|URI でエンコードされた文字列のバイナリ表現を返します。 たとえば、次の式は `You Are:Cool/Awesome` のバイナリ表現を返します: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :string<br /><br />**説明**:必須。 URI でエンコードされた文字列です。|  
|uriComponentToString|URI でエンコードされた文字列の文字列表現を返します。 たとえば、次の式は `You Are:Cool/Awesome` を返します: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **パラメーター番号**: 1<br /><br />**[名前]** :string<br /><br />**説明**:必須。 URI でエンコードされた文字列です。|  
|Xml|値の xml 表現を返します。 たとえば、次の式は `'\<name>Alan\</name>'` によって表される xml コンテンツを返します: `xml('\<name>Alan\</name>')` xml 関数は、JSON オブジェクトの入力もサポートします。 たとえば、パラメーター `{ "abc": "xyz" }` は xml コンテンツ `\<abc>xyz\</abc>` に変換されます<br /><br /> **パラメーター番号**: 1<br /><br />**[名前]** :値<br /><br />**説明**:必須。 XML に変換する値です。|  
|xpath|xpath 式が評価された値の xpath 式に一致する xml ノードの配列を返します。<br /><br />  **例 1**<br /><br /> パラメーター 'p1' の値が次の XML の文字列表現であるとします。<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1.コード `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> は次を返します<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> これに対して<br /><br /> 2.コード `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> は次を返します<br /><br /> `13`<br /><br /> <br /><br /> **例 2**<br /><br /> 次のような XML コンテンツがあるものとします。<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.コード `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> or<br /><br /> 2.コード `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> は、次の値は返します。<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> and<br /><br /> 手順 3.コード `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> は、次の値は返します。<br /><br /> ``bar``<br /><br /> **パラメーター番号**: 1<br /><br />**[名前]** :Xml<br /><br />**説明**:必須。 XPath 式を評価する XML です。<br /><br /> **パラメーター番号**: 2<br /><br />**[名前]** :XPath<br /><br />**説明**:必須。 評価する XPath 式です。|  
|array|パラメーターを配列に変換します。  たとえば、次の式は `["abc"]` を返します: `array('abc')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :値<br /><br /> **説明**:必須。 配列に変換する値です。|
|createArray|パラメーターから配列を作成します。  たとえば、次の式は `["a", "c"]` を返します: `createArray('a', 'c')`<br /><br /> **パラメーター番号**: 1 ... n<br /><br /> **[名前]** :Any n<br /><br /> **説明**:必須。 配列に結合する値です。|

## <a name="math-functions"></a>算術関数  
 これらの関数は、**integer** 型および **float** 型の値に使うことができます。  
  
|関数名|説明|  
|-------------------|-----------------|  
|add|2 つの数値の加算の結果を返します。 たとえば、この関数は `20.333` を返します: `add(10,10.333)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Summand 1<br /><br /> **説明**:必須。 **Summand 2** に加算する値です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Summand 2<br /><br /> **説明**:必須。 **Summand 1** に加算する値です。|  
|sub|2 つの数値の減算の結果を返します。 たとえば、この関数は `-0.333` を返します。<br /><br /> `sub(10,10.333)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Minuend<br /><br /> **説明**:必須。 **Subtrahend** を減算する値です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Subtrahend<br /><br /> **説明**:必須。 **Minuend** から減算する値です。|  
|mul|2 つの数値の乗算の結果を返します。 たとえば、次は `103.33` を返します。<br /><br /> `mul(10,10.333)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Multiplicand 1<br /><br /> **説明**:必須。 **Multiplicand 2** と乗算する値です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Multiplicand 2<br /><br /> **説明**:必須。 **Multiplicand 1** と乗算する値です。|  
|div|2 つの数値の除算の結果を返します。 たとえば、次は `1.0333` を返します。<br /><br /> `div(10.333,10)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Dividend<br /><br /> **説明**:必須。 **Divisor** によって除算される値です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Divisor<br /><br /> **説明**:必須。 **Dividend** を除算する値です。|  
|mod|2 つの数値の除算の後の残りの結果 (モジュロ) を返します。 たとえば、次の式は `2` を返します:<br /><br /> `mod(10,4)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Dividend<br /><br /> **説明**:必須。 **Divisor** によって除算される値です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Divisor<br /><br /> **説明**:必須。 **Dividend** を除算する値です。 除算の後、残りの部分が取得されます。|  
|min|この関数を呼び出すには、2 つの異なるパターンがあります: `min([0,1,2])`。ここで、min は配列を受け取ります。 この式は `0` を返します。 または、次の関数は、値のコンマ区切りリストを受け取ります: `min(0,1,2)`。この関数も 0 を返します。 注: すべての値が数値である必要があるため、パラメーターが配列である場合は、その中に数値だけが含まれている必要があります。<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Collection または Value<br /><br /> **説明**:必須。 最小値を見つけるための値の配列、またはセットの最初の値のどちらかを指定できます。<br /><br /> **パラメーター番号**: 2 ... *n*<br /><br /> **[名前]** :Value *n*<br /><br /> **説明**:省略可能。 最初のパラメーターが Value である場合は、追加の値を渡すことができ、渡されたすべての値の最小値が返されます。|  
|max|この関数を呼び出すには、2 つの異なるパターンがあります: `max([0,1,2])`<br /><br /> ここで、max は配列を受け取ります。 この式は `2` を返します。 または、次の関数は、値のコンマ区切りリストを受け取ります: `max(0,1,2)`。次の関数は 2 を返します。 注: すべての値が数値である必要があるため、パラメーターが配列である場合は、その中に数値だけが含まれている必要があります。<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Collection または Value<br /><br /> **説明**:必須。 最大値を見つけるための値の配列、またはセットの最初の値のどちらかを指定できます。<br /><br /> **パラメーター番号**: 2 ... *n*<br /><br /> **[名前]** :Value *n*<br /><br /> **説明**:省略可能。 最初のパラメーターが Value である場合は、追加の値を渡すことができ、渡されたすべての値の最大値が返されます。|  
|range| 特定の数値から始まる整数の配列を生成し、返される配列の長さを定義します。 たとえば、次の関数は `[3,4,5,6]` を返します。<br /><br /> `range(3,4)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Start index<br /><br /> **説明**:必須。 これは、配列内の最初の整数です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Count<br /><br /> **説明**:必須。 配列内にある整数の数。|  
|rand| 指定された範囲 (両端を包む) 内のランダムな整数を生成します。 たとえば、これは `42` を返す可能性があります。<br /><br /> `rand(-1000,1000)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :最小値<br /><br /> **説明**:必須。 返される可能性のある最小の整数。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :最大値<br /><br /> **説明**:必須。 返される可能性のある最大の整数。|  
  
## <a name="date-functions"></a>データ関数  
  
|関数名|説明|  
|-------------------|-----------------|  
|utcnow|現在のタイムスタンプを文字列として返します。 例: `2015-03-15T13:27:36Z`<br /><br /> `utcnow()`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :形式<br /><br /> **説明**:省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|addseconds|渡されたタイムスタンプ文字列に秒数を表す整数を追加します。 秒数は正でも負でもかまいません。 書式指定子が指定されない限り、既定では、結果は ISO 8601 形式 ("o") の文字列です。 例: `2015-03-15T13:27:00Z`<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Timestamp<br /><br /> **説明**:必須。 時刻を表す文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :Seconds<br /><br /> **説明**:必須。 追加する秒の値です。 負の値にして秒数を減算できます。<br /><br /> **パラメーター番号**: 3<br /><br /> **[名前]** :形式<br /><br /> **説明**:省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|addminutes|渡されたタイムスタンプ文字列に分の値を表す整数を追加します。 分の値は正でも負でもかまいません。 書式指定子が指定されない限り、既定では、結果は ISO 8601 形式 ("o") の文字列です。 例: `2015-03-15T14:00:36Z`<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Timestamp<br /><br /> **説明**:必須。 時刻を表す文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :分<br /><br /> **説明**:必須。 追加する分の値です。 負の値にして分数を減算できます。<br /><br /> **パラメーター番号**: 3<br /><br /> **[名前]** :形式<br /><br /> **説明**:省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|addhours|渡されたタイムスタンプ文字列に時間数を表す整数を追加します。 時間の値は正でも負でもかまいません。 書式指定子が指定されない限り、既定では、結果は ISO 8601 形式 ("o") の文字列です。 例: `2015-03-16T01:27:36Z`<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Timestamp<br /><br /> **説明**:必須。 時刻を表す文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :時間<br /><br /> **説明**:必須。 追加する時間数。 負の値にして時間数を減算できます。<br /><br /> **パラメーター番号**: 3<br /><br /> **[名前]** :形式<br /><br /> **説明**:省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|adddays|渡されたタイムスタンプ文字列に日数を表す整数を追加します。 日の値は正でも負でもかまいません。 書式指定子が指定されない限り、既定では、結果は ISO 8601 形式 ("o") の文字列です。 例: `2015-02-23T13:27:36Z`<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Timestamp<br /><br /> **説明**:必須。 時刻を表す文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :日数<br /><br /> **説明**:必須。 追加する日数です。 負の値にして日数を減算できます。<br /><br /> **パラメーター番号**: 3<br /><br /> **[名前]** :形式<br /><br /> **説明**:省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|formatDateTime|日付形式の文字列を返します。 書式指定子が指定されない限り、既定では、結果は ISO 8601 形式 ("o") の文字列です。 例: `2015-02-23T13:27:36Z`<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **パラメーター番号**: 1<br /><br /> **[名前]** :Date<br /><br /> **説明**:必須。 日付を表す文字列です。<br /><br /> **パラメーター番号**: 2<br /><br /> **[名前]** :形式<br /><br /> **説明**:省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  

## <a name="next-steps"></a>次の手順
式で使用できるシステム変数の一覧については、「[システム変数](control-flow-system-variables.md)」を参照してください。
