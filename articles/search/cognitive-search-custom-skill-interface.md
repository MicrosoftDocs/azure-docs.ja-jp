---
title: コグニティブ検索でのカスタム スキルのインターフェイス定義 - Azure Search
description: Azure Search のコグニティブ検索パイプラインでの web-api カスタム スキル用カスタム データ抽出インターフェイス。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: e181aab3d92d8111a0a7d1d41bbddac20687a547
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668874"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>コグニティブ検索パイプラインにカスタム スキルを追加する方法

Azure Search の [コグニティブ検索インデックス作成パイプライン](cognitive-search-concept-intro.md)は、[定義済みスキル](cognitive-search-predefined-skills.md)と、個人的に作成してパイプラインに追加する[カスタム スキル](cognitive-search-custom-skill-web-api.md)で組み立てることができます。 この記事では、コグニティブ検索パイプラインに含めることができるようにインターフェイスを公開するカスタム スキルの作成方法について説明します。 

カスタム スキルを構築すると、コンテンツに固有の変換を挿入することができます。 カスタム スキルは独立して実行され、必要なすべてのエンリッチメント ステップが適用されます。 たとえば、フィールド固有のカスタム エンティティを定義して、ビジネスおよび財務の契約やドキュメントを区別するためのカスタム分類モデルを作成したり、音声認識スキルを追加して、関連するコンテンツのオーディオ ファイルを細かく調べたりすることができます。 手順の例については、[例: コグニティブ検索用のカスタム スキルを作成する](cognitive-search-create-custom-skill-example.md)に関する記事を参照してください。

 どのようなカスタム機能が必要な場合でも、カスタム スキルをエンリッチメント パイプラインの他の部分に接続するための単純でわかりやすいインターフェイスがあります。 [スキルセット](cognitive-search-defining-skillset.md)に含めるための唯一の要件は、入力を受け入れて、スキルセット内で全体として使用できる方法で出力を行うことです。 この記事で焦点となっているのは、エンリッチメント パイプラインが必要とする入力と出力の形式です。

## <a name="web-api-custom-skill-interface"></a>Web API のカスタム スキル インターフェイス

Web API のカスタム スキル エンドポイントが、30 秒以内に応答を返さない場合、既定ではタイムアウトになります。 インデックス作成パイプラインは同期的であり、この時間内に応答が受信されない場合は、タイムアウト エラーが発生します。  タイムアウト パラメーターを設定することで、タイムアウトを最長で 90 秒に構成できます。

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 90 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT90S",
```

現在のところ、カスタム スキルとやり取りするための唯一のメカニズムは、Web API インターフェイスです。 Web API は、このセクションで説明する要件を満たしている必要があります。

### <a name="1--web-api-input-format"></a>1.Web API の入力形式

Web API は、処理されるレコードの配列を受け取る必要があります。 各レコードには、Web API に提供される入力である "プロパティ バッグ" が含まれていなければなりません。 

契約のテキストに記載されている最初の日付を識別する簡単なエンリッチャーを作成したいとします。 この例では、スキルは単一の入力 *contractText* を契約テキストとして受け取ります。 スキルには、契約の日付である 1 つの出力もあります。 エンリッチャーをより興味深いものにするために、この *contractDate* をマルチパートの複合型形式で返します。

Web API は、入力レコードのバッチを受け取る準備ができている必要があります。 *values* 配列の各メンバーは、特定のレコードの入力を表します。 各レコードには、次の要素が必要です。

+ 特定のレコードの一意の識別子である *recordId* メンバー。 エンリッチャーが結果を返すとき、呼び出し元がレコード結果を入力とマッチさせることができるように、この *recordId* を提供する必要があります。

+ *data* メンバー。基本的には各レコードの入力フィールドのバッグ。

具体的には、上の例では Web API が次のような要求を想定します。

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
現実には、ここに示した 3 つのレコードだけでなく、数百または数千のレコードでサービスが呼び出されることがあります。

### <a name="2-web-api-output-format"></a>2.Web API の出力形式

出力の形式は、*recordId* を含むレコードのセット、およびプロパティ バッグです 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

この特定の例では出力が 1 つしかありませんが、複数のプロパティを出力することができます。 

### <a name="errors-and-warning"></a>エラーと警告

前の例で示されていたように、レコードごとにエラー メッセージと警告メッセージを返すことができます。

## <a name="consuming-custom-skills-from-skillset"></a>スキルセットのカスタム スキルの使用

Web API エンリッチャーを作成すると、HTTP ヘッダーとパラメーターを要求の一部として記述できます。 次のスニペットは、要求パラメーターと HTTP ヘッダーをスキルセット定義の一部として記述する方法を示しています。

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>次の手順

+ [例:コグニティブ検索用のカスタム スキルを作成する](cognitive-search-create-custom-skill-example.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットを作成する (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [エンリッチされたフィールドをマップする方法](cognitive-search-output-field-mapping.md)
