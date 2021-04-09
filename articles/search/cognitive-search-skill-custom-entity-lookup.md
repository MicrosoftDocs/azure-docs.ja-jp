---
title: カスタム エンティティの参照認知検索スキル
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の認知検索パイプラインで、テキストからさまざまなカスタム エンティティを抽出します。 このスキルは現在、パブリック プレビューの段階です。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 704763e8e6e7c5336d0ed3e1c28791fb96c77aba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97844942"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>カスタム エンティティの参照認知スキル (プレビュー)

> [!IMPORTANT] 
> このスキルは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 現時点では、ポータルと .NET SDK によるサポートはありません。

**カスタム エンティティの参照** スキルを使うと、ユーザーが定義したカスタムの単語と語句の一覧からテキストを検索できます。 この一覧を使用して、エンティティが一致するすべての文書がラベル付けされます。 このスキルは、ある程度のあいまい一致もサポートしており、類似しているが完全一致ではない一致を見つけるために適用できます。  

このスキルは Cognitive Services API にバインドされていないため、プレビュー期間中は無料で使用できます。 ただし、1 日の強化の制限を上書きするには、[Cognitive Services リソースを接続する](./cognitive-search-attach-cognitive-services.md)ようにします。 Azure Cognitive Search 経由でアクセスする場合、1 日あたりの制限が Cognitive Services への無料のアクセスに適用されます。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>データ制限
+ サポートされる入力レコードの最大サイズは 256 MB です。 カスタム エンティティの参照スキルにデータを送信する前にデータを分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)を使用することをお勧めします。
+ *entitiesDefinitionUri* パラメーターを使用して指定されている場合、サポートされるエンティティ定義テーブルの最大サイズは 10 MB です。 
+ エンティティが *inlineEntitiesDefinition* パラメーターを使用してインラインで定義されている場合、サポートされる最大サイズは 10 KB です。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | 説明 |
|--------------------|-------------|
| `entitiesDefinitionUri`    | 照合対象のすべてのターゲット テキストを含む JSON ファイルまたは CSV ファイルのパス。 このエンティティ定義は、インデクサー実行の開始時に読み取られます。このファイルの実行中に行われた更新は、後続の実行まで反映されません。 この構成には、HTTPS 経由でアクセスできる必要があります。 想定される CSV または JSON スキーマについては、後述する「[カスタム エンティティ定義の形式](#custom-entity-definition-format)」を参照してください。|
|`inlineEntitiesDefinition` | インライン JSON エンティティの定義。 このパラメーターは、entitiesDefinitionUri パラメーター (存在する場合) よりも優先されます。 10 KB を超える構成をインラインで指定することはできません。 想定される JSON スキーマについては、後述する「[カスタム エンティティ定義の形式](#custom-entity-definition-format)」を参照してください。 |
|`defaultLanguageCode` |    (省略可能) 入力テキストのトークン化と記述に使用される入力テキストの言語コード。 次の言語がサポートされます。`da, de, en, es, fi, fr, it, ko, pt` 既定値は英語 (`en`) です。 languagecode-countrycode 形式を渡す場合、形式の languagecode 部分のみが使用されます。  |
|`globalDefaultCaseSensitive` | (省略可能) スキルの大文字と小文字の区別に関する既定値。 エンティティの `defaultCaseSensitive` 値が指定されない場合、この値がそのエンティティの `defaultCaseSensitive` 値になります。 |
|`globalDefaultAccentSensitive` | (省略可能) スキルのアクセントの区別に関する既定値。 エンティティの `defaultAccentSensitive` 値が指定されない場合、この値がそのエンティティの `defaultAccentSensitive` 値になります。 |
|`globalDefaultFuzzyEditDistance` | (省略可能) スキルのあいまい編集距離の既定値。 エンティティの `defaultFuzzyEditDistance` 値が指定されない場合、この値がそのエンティティの `defaultFuzzyEditDistance` 値になります。 |

## <a name="skill-inputs"></a>スキルの入力

| 入力名      | 説明                   |
|---------------|-------------------------------|
| `text`          | 分析するテキスト。          |
| `languageCode`    | 省略可能。 既定値は `"en"` です。  |


## <a name="skill-outputs"></a>スキルの出力


| 出力名      | 説明                   |
|---------------|-------------------------------|
| `entities` | 検出された一致に関する情報と、関連するメタデータが格納されるオブジェクトの配列。 識別される各エンティティには、次のフィールドが含まれる場合があります。  <ul> <li> *name*:識別される最上位のエンティティ。 エンティティは、"正規化された" 形式を表します。 </li> <li> *id*:"カスタム エンティティ定義の形式" でユーザーによって定義されたエンティティの一意の識別子。</li> <li> *description*:"カスタム エンティティ定義の形式" でユーザーによって定義されたエンティティの説明。 </li> <li> *type:* "カスタム エンティティ定義の形式" でユーザーによって定義されたエンティティ型。</li> <li> *subtype:* "カスタム エンティティ定義形式" でユーザーによって定義されたサブ エンティティ型。</li>  <li> *matches*:ソース テキストのそのエンティティの各一致を記述するコレクション。 各一致には次のメンバーが含まれます。 </li> <ul> <li> *text*:ソース ドキュメントからの未加工のテキスト一致。 </li> <li> *offset*:一致が見つかったテキスト内の場所。 </li> <li> *length*:一致したテキストの長さ。 </li> <li> *matchDistance*:この一致と異なる文字数は、元のエンティティ名または別名に由来します。  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>カスタム エンティティ定義の形式

カスタム エンティティの一覧をカスタム エンティティの参照スキルに指定するには、3 種類の方法があります。 .CSV ファイル、.JSON ファイル、またはスキル定義の一部であるインライン定義として一覧を指定できます。  

定義ファイルが .CSV または .JSON ファイルである場合、ファイルのパスを *entitiesDefinitionUri* パラメーターの一部として指定する必要があります。 この場合、各インデクサーの実行の開始時に 1 度、ファイルがダウンロードされます。 インデクサーの実行を想定している限り、ファイルにアクセスできる必要があります。 また、ファイルは UTF-8 でエンコードされている必要があります。

定義をインラインで指定する場合、*inlineEntitiesDefinition* スキル パラメーターの内容としてインラインで指定する必要があります。 

### <a name="csv-format"></a>CSV 形式

検索するカスタム エンティティの定義をコンマ区切り値 (CSV) ファイルで指定するには、ファイルのパスを用意し、それを *entitiesDefinitionUri* スキル パラメーターに設定します。 このパスは、https の場所にある必要があります。 定義ファイルの最大サイズは 10 MB です。

CSV 形式は単純です。 次に示すように、各行は一意のエンティティを表します。

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

この場合、見つかったエンティティとして返される 3 つのエンティティ (Bill Gates、Satya Nadella、Microsoft) がありますが、テキストの行のいずれかの用語 (別名) と一致する場合、それらは識別されます。 たとえば、文字列 "William H. Gates" がドキュメントで見つかった場合、"Bill Gates" エンティティの一致が返されます。

### <a name="json-format"></a>JSON 形式

JSON ファイルで検索するカスタム エンティティの定義を指定することもできます。 JSON 形式では用語ごとに照合ルールを定義できるため、やや柔軟に利用できます。 たとえば、各用語のあいまい一致距離 (ダメラウレーベンシュタイン距離)、または照合で大文字と小文字を区別するかどうかを指定できます。 

 CSV ファイルと同様に、JSON ファイルへのパスを指定し、*entitiesDefinitionUri* スキル パラメーターで設定する必要があります。 このパスは、https の場所にある必要があります。 定義ファイルの最大サイズは 10 MB です。

最も基本的な JSON カスタム エンティティ一覧定義としては、照合するエンティティの一覧を使用できます。

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

JSON 定義のより複雑な例として、必要に応じて、各エンティティの ID、説明、型、サブ型だけでなく、その他の "*別名*" を指定できます。 別名の用語が一致した場合、そのエンティティも返されます。

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

照合するエンティティを定義するときに設定できるさまざまな構成パラメーターの詳細を次の表に示します。

|  フィールド名  |        説明  |
|--------------|----------------------|
| `name` | 最上位のエンティティ記述子。 スキル出力内の一致は、この名前でグループ化されます。これは、検出されるテキストの "正規化された" 形式を表します。  |
| `description`  | (省略可能) このフィールドは、一致したテキストに関するカスタム メタデータのパススルーとして使用できます。 このフィールドの値は、スキル出力内でエンティティが一致するたびに表示されます。 |
| `type` | (省略可能) このフィールドは、一致したテキストに関するカスタム メタデータのパススルーとして使用できます。 このフィールドの値は、スキル出力内でエンティティが一致するたびに表示されます。 |
| `subtype` | (省略可能) このフィールドは、一致したテキストに関するカスタム メタデータのパススルーとして使用できます。 このフィールドの値は、スキル出力内でエンティティが一致するたびに表示されます。 |
| `id` | (省略可能) このフィールドは、一致したテキストに関するカスタム メタデータのパススルーとして使用できます。 このフィールドの値は、スキル出力内でエンティティが一致するたびに表示されます。 |
| `caseSensitive` | (省略可能) 既定値は false です。 エンティティ名との比較で大文字と小文字を区別するかどうかを示すブール値。 "Microsoft" の大文字と小文字を区別しない一致の例として、microsoft、microSoft、MICROSOFT があります |
| `accentSensitive` | (省略可能) 既定値は false です。 "é" と "e" などアクセント記号が付いた文字と付いてない文字を同一と見なすかどうかを示すブール値。 |
| `fuzzyEditDistance` | (省略可能) 既定値は 0 です。 最大値は 5 です。 エンティティ名との一致を構成する上で、許容する不一致文字の数を指定します。 指定した一致に対して可能な限り最小のあいまいさが返されます。  たとえば、編集距離が 3 に設定されている場合、"Windows 10" は "Windows"、"Windows10"、"windows 7" と一致します。 <br/> 大文字と小文字の区別が false に設定されている場合、大文字と小文字の違いはあいまいさの許容値にはカウントされませんが、それ以外の場合はカウントされます。 |
| `defaultCaseSensitive` | (省略可能) このエンティティの既定の大文字と小文字の区別の値を変更します。 すべての別名の caseSensitive 値の既定値を変更するために使用できます。 |
| `defaultAccentSensitive` | (省略可能) このエンティティの既定のアクセントの区別の値を変更します。 すべての別名の accentSensitive 値の既定値を変更するために使用できます。|
| `defaultFuzzyEditDistance` | (省略可能) このエンティティの既定のあいまい編集距離値を変更します。 すべての別名の fuzzyEditDistance 値の既定値を変更するために使用できます。 |
| `aliases` | (省略可能) ルート エンティティ名の代替のスペルまたは同義語を指定するために使用できる複雑なオブジェクトの配列。 |

| 別名のプロパティ | 説明 |
|------------------|-------------|
| `text`  | 一部のターゲット エンティティ名の代替のスペルまたは表現。  |
| `caseSensitive` | (省略可能) 前述のルート エンティティ "caseSensitive" パラメーターと同じように機能しますが、この別名のみに適用されます。 |
| `accentSensitive` | (省略可能) 前述のルート エンティティ "accentSensitive" パラメーターと同じように機能しますが、この別名のみに適用されます。 |
| `fuzzyEditDistance` | (省略可能) 前述のルート エンティティ "fuzzyEditDistance" パラメーターと同じように機能しますが、この別名のみに適用されます。 |


### <a name="inline-format"></a>インライン形式

場合によっては、インラインでスキル定義に直接一致するカスタム エンティティの一覧を指定する方が便利です。 その場合は前述のような JSON 形式を使用できますが、スキル定義にインラインで指定されます。
サイズが 10 KB (シリアル化されたサイズ) 未満の構成のみをインラインで定義できます。 

##    <a name="sample-definition"></a>定義例

インライン形式を使用したスキル定義例を次に示します。

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
また、エンティティ定義ファイルへのポインターを指定する場合の、`entitiesDefinitionUri` 形式を使用したスキル定義例を次に示します。

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>サンプル入力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>サンプル出力

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>エラーと警告

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>警告:一致の最大容量に達したため、以降の重複する一致はすべてスキップされます。

この警告は、検出された一致の数が許容される最大値を超えた場合に出力されます。 この場合、重複する一致を含める処理は停止されます。 これを容認できない場合は、個別のユース ケースを支援できるように[サポート チケット](https://ms.portal.azure.com/#create/Microsoft.Support)をお送りください。

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [エンティティ認識スキル (既知のエンティティを検索する場合)](cognitive-search-skill-entity-recognition.md)
