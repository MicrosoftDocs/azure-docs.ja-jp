---
title: Project Answer Search とは
titlesuffix: Azure Cognitive Services
description: Project Answer Search の概要。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: ac1717a8e8a08fcfedc3bc21bb0f03b3e3ca2511
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548083"
---
# <a name="what-is-project-answer-search"></a>Project Answer Search とは
Project Answer Search API は、疑問文を使ったクエリに対する回答の取得に Bing v7 エンドポイントを使用します。 "地球の円周はどのくらいですか?" などの質問には、 事実情報を記載した回答を返します。  人物、場所、または物に関するクエリの場合には、そのクエリで特定されているエンティティに関する情報を返します。 会話ボット、メッセージング アプリ、リーダーなどのアプリケーションでは、このようなシナリオが有用になります。  

クエリでは、そのシナリオに応じた応答が返されます。Web ページは常に返されますが、[事実](fact-queries.md)と[エンティティ](entity-queries.md)については、該当するクエリの場合に限り返されます。

## <a name="endpoint"></a>エンドポイント
人、場所、または物に関する質問の回答または情報を取得するには、Answer Search API エンドポイントに要求を送信する必要があります。 ヘッダーと URL パラメーターは、さまざまな仕様に応じたものを使用します。  有効なトークンを指定した *Ocp-Apim-Subscription-Key* ヘッダーを含めるようにしてください。  マーケットに関するパラメーターは必須です。 現時点では、`en-us` マーケットのみサポートされています。

次のクエリは、質問に対する回答を取得します。質問は、"地球の円周はどのくらいですか?" というものです。

GET:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

検索の対象を指定するには、URL パラメーター `q=` が必要です。

## <a name="response-object"></a>応答オブジェクト

応答には、HTTP ヘッダー、Web ページ、事実、および/またはエンティティが含まれます。

```
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "https://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "https://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "https://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "https://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "https://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

```

## <a name="terms-of-use"></a>使用条件
Project Answer Search と Project Video Trends には、[Bing Search の使用と表示に関する要件](use-display-requirements.md)が適用されます。

お客様およびお客様の代理を務める第三者のいずれも、URL Preview API から取得したデータを Microsoft 以外のサービスまたは機能のテスト、開発、トレーニング、頒布、または提供を目的として利用、保持、保存、キャッシュ、共有、または配布することは認められません。 

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>データの帰属表示  

Project Answer Search の応答には、第三者が所有する情報が含まれます。 お客様は、ユーザー エクスペリエンスで利用するクリエイティブ コモンズ ライセンスがあればそれを遵守するなど、情報を適切に利用する責任を負います。  
  
回答または結果に `contractualRules`、`attributions`、`provider` のいずれかのフィールドが含まれている場合、データをそれらの帰属を示す必要があります。 回答にこれらのいずれのフィールドも含まれていない場合は、属性は必要ありません。 回答に `contractualRules` フィールドと、`attributions` フィールドまたは `provider` フィールド (またはその両方) が含まれている場合は、契約上の規則を使用してデータがそれらの帰属を示す必要があります。  
  
次の例では、MediaAttribution タイプの契約規則を含むエンティティと、`provider` フィールドを含む画像を示しています。 この MediaAttribution 規則では、規則の対象として画像を指定しています。このため、帰属の表示にあたっては画像の `provider` フィールドが無視され、代わりに MediaAttribution 規則が使用されることになります。  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
契約規則に `targetPropertyName` フィールドが含まれている場合、規則は対象のフィールドにのみ適用されます。 含まれていない場合は、規則は `contractualRules` フィールドを含む親オブジェクトに適用されます。  
  
  
次の例では、`LinkAttribution` 規則に `targetPropertyName` フィールドが含まれているため、`description` フィールドに規則が適用されます。 特定のフィールドに適用される規則の場合、対象となるデータの直後に提供元の Web サイトへのハイパーリンクを含めた 1 行を追加する必要があります。 たとえば、説明の帰属を表示する場合には、説明テキストの直後にデータの提供元の Web サイト上のデータへのハイパーリンクを含めた 1 行を追加します。このケースでは、en.wikipedia.org へのリンクを作成します。  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>ライセンスの帰属表示  

契約規則のリストに [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) 規則が含まれている場合、ライセンスが適用されるコンテンツの直後の行に通知を表示する必要があります。 `LicenseAttribution` 規則では、ライセンスが適用されるプロパティを特定するために `targetPropertyName` フィールドを使用します。  
  
`LicenseAttribution` 規則を含む例を次に示します。  
  
![ライセンスの帰属表示](./media/licenseattribution.png)  
  
表示するライセンス通知には、ライセンスに関する情報を掲載している Web サイトへのハイパーリンクを含める必要があります。 通常は、ライセンスの名前をハイパーリンクにします。 たとえば、通知の文言が "**Text under CC-BY-SA license (CC-BY-SA ライセンスが適用されるテキスト)**" であって、ライセンスの名前が CC-BY-SA の場合、CC-BY-SA の部分をハイパーリンクにします。  
  
### <a name="link-and-text-attribution"></a>リンクとテキストの帰属表示  

[LinkAttribution](reference.md#linkattribution) と [TextAttribution](reference.md#textattribution) の規則は通常、データの提供元を明らかにする場合に使用します。 `targetPropertyName` フィールドは、規則が適用されるフィールドを特定するためのものです。  
  
プロバイダーの帰属を示すには属性を適用するコンテンツ (対象となるフィールドなど) の直後に 1 行含めます。 追加した行には、提供元がデータの出典であることを明確に示したラベルを付ける必要があります。 たとえば、"データの出典: en.wikipedia.org" のようにします。 `LinkAttribution` 規則の場合、提供元の Web サイトへのハイパーリンクを作成する必要があります。  
  
`LinkAttribution` および `TextAttribution` の規則を含む例を次に示します。  
  
![リンクとテキストの帰属表示](./media/linktextattribution.png)  

### <a name="media-attribution"></a>メディアの帰属表示  

エンティティに画像が含まれており、その画像を表示する場合には、提供元の Web サイトへのクリックスルー リンクを提供する必要があります。 エンティティに [MediaAttribution](reference.md#mediaattribution) 規則が含まれている場合、その規則の URL を使用してクリックスルー リンクを作成します。 含まれていない場合は、画像の `provider` フィールドに含まれる URL を使用してクリックスルー リンクを作成します。  
  
画像の `provider` フィールドと契約規則が含まれる例を次に示します。 この例には契約規則が含まれているため、画像の `provider` フィールドを無視して `MediaAttribution` 規則を適用することになります。  
  
![メディアの帰属表示](./media/mediaattribution.png)  

## <a name="next-steps"></a>次の手順
- [C# のクイック スタート](c-sharp-quickstart.md)
- [Java のクイック スタート](java-quickstart.md)
- [Node のクイック スタート](node-quickstart.md)
- [Python のクイック スタート](python-quickstart.md)
