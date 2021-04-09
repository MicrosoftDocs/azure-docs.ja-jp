---
title: スコアリング プロファイルを使用して検索順位を上げる
titleSuffix: Azure Cognitive Search
description: スコアリング プロファイルを追加することにより、Azure Cognitive Search の検索結果での検索順位スコアを上げます。
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 97797e309c32c6ea996d5ae1901b9a266a683173
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537635"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>スコアリング プロファイルを Azure Cognitive Search のインデックスに追加する

"*スコアリング*" では、順位に従って並べられる結果セット内の各項目の検索スコアが計算されます。 検索結果セット内のすべての項目にそれぞれ検索スコアが割り当てられ、最上位から最下位まで順位が付けられます。

 Azure Cognitive Search では、既定のスコアリングを使用して初期スコアを計算しますが、"*スコアリング プロファイル*" を介して計算をカスタマイズすることができます。 スコアリング プロファイルでは、検索結果の項目の順位付けをより細かく制御できます。 たとえば、収益の可能性に基づいて項目をブーストしたり、より新しい項目を昇格させたり、場合によっては在庫期間が長すぎる項目をブーストしたりできます。  

 次のビデオ セグメントは、Azure Cognitive Search でのスコアリング プロファイルの動作についての部分まで早送りされます。
 
> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="scoring-profile-definitions"></a>スコアリング プロファイルの定義

 スコアリング プロファイルは、インデックス定義の一部であり、重み付けされたフィールド、関数、およびパラメーターで構成されます。  

 スコアリング プロファイルとはどのようなものかを説明するために、次の例に 'geo' という名前の簡単なプロファイルを示します。 このプロファイルは、**hotelName** フィールド内の検索語句を含む項目をブーストします。 また、 `distance` 関数を使用して、現在の場所から 10 キロメートル以内にある項目を優先します。 他のユーザーが語句 "inn" の検索を実行し、"inn" がたまたまホテル名の一部であった場合、現在位置の半径 10 Km 以内にあって "inn" を含むホテルが記載されているドキュメントが検索結果で上位に表示されます。  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 このスコアリング プロファイルを使用するには、クエリを作成して、クエリ文字列にプロファイルを指定します。 次のクエリで、要求内のクエリ パラメーター `scoringProfile=geo` に注目してください。  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2020-06-30 
```  

 このクエリは、語句 'inn' を検索し、現在の場所を渡します。 このクエリには、`scoringParameter` などの他のパラメーターが含まれていることがわかります。 クエリ パラメーターについては、「[ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)」を参照してください。  

 スコアリング プロファイルの詳細な例を確認するには、「 [例](#bkmk_ex) 」をクリックします。  

## <a name="what-is-default-scoring"></a>既定のスコアリングとは  
 スコアリングでは、順位に従って並べられる結果セット内の各項目の検索スコアを計算します。 検索結果セット内のすべての項目にそれぞれ検索スコアが割り当てられ、最上位から最下位まで順位が付けられます。 アプリケーションには、より高いスコアを持つ項目が返されます。 既定では、上位 50 個が返されますが、 `$top` パラメーターを使用することで、返される項目数を減らしたり増やしたりできます (1 回の応答で 1000 個まで)。  

検索スコアは、データとクエリの統計プロパティに基づいて計算されます。 Azure Cognitive Search はクエリ文字列内の検索語句 (`searchMode`に応じて一部またはすべて) を含むドキュメントを検索し、検索語句のインスタンスを多く含むドキュメントを優先します。 データ インデックス全体での語句の出現頻度は低いがドキュメント内ではよく使用されている場合、検索スコアはより高くなります。 関連性を計算するこのアプローチの基礎となる手法は、[TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) (単語の出現頻度 - 逆文書頻度) と呼ばれています。  

 カスタムの並べ替えが行われないと仮定した場合、結果は検索スコアによって順位付けされてから、呼び出し元のアプリケーションに返されます。 $top が指定されていない場合、最も高い検索スコアを持つ 50 個の項目が返されます。  

 検索スコアの値は、結果セット全体で繰り返すことができます。 たとえば、スコアが 1.2 である項目が 10 個、スコアが 1.0 である項目が 20 個、スコアが 0.5 である項目が 20 個あるとします。 同じ検索スコアを持つ項目が複数ヒットした場合、同じスコアを持つ項目の順序付けは定義されていないので安定しません。 もう一度クエリを実行すると、項目の位置が変わる可能性があります。 同一スコアの項目が 2 つ存在する場合、最初に表示される項目を特定することはできません。  

## <a name="when-to-add-scoring-logic"></a>スコアリング ロジックを追加する場合 
 既定の順位付けの動作ではビジネス目標を達成するに至らない場合、1 つ以上のスコアリング プロファイルを作成する必要があります。 たとえば、検索の関連性で、新しく追加された項目を優先するとします。 同様に、利益率を含んでいるフィールド、または他に収益の可能性を示す何らかのフィールドがあるとします。 ビジネスに利点をもたらすブースティング ヒットは、スコアリング プロファイルの使用を決定する上で重要な要因になる場合があります。  

 関連性に基づく順序付けも、スコアリング プロファイルを通して実装されます。 価格、日付、評価、または関連性での並べ替えが可能な、以前に使用した検索結果ページを検討してしてください。 Azure Cognitive Search では、スコアリング プロファイルによって「関連性」オプションが開始されます。 関連性の定義は、ビジネス目標および提供する検索機能の種類に基づいて、ユーザーによって制御されます。  

##  <a name="example"></a><a name="bkmk_ex"></a> 例  
 前述のように、カスタマイズされたスコアリングは、インデックス スキーマで定義された 1 つ以上のスコアリング プロファイルを介して実装されます。  

 次の例では、2 つのスコアリング プロファイル (`boostGenre`、`newAndHighlyRated`) を使用するインデックス スキーマを示します。 いずれかのプロファイルをクエリ パラメーターとして指定したクエリを、このインデックスに対して実行すると、該当するプロファイルを使用して結果セットのスコアリングが行われます。  

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>ワークフロー  
 カスタムのスコアリング動作を実装するには、インデックスを定義するスキーマにスコアリング プロファイルを追加します。 インデックスには最大 100 個のスコアリング プロファイルを含めることができます ([サービスの制限](search-limits-quotas-capacity.md)に関する記事を参照) が、クエリには一度に 1 つのプロファイルのみ指定できます。  

 このトピックで提供される [テンプレート](#bkmk_template) を使用して開始します。  

 名前を指定します。 スコアリング プロファイルは省略できますが、追加する場合は、名前が必要です フィールドの名前付け規則に従ってください (文字で始め、特殊文字や予約語の使用は避ける)。 完全な一覧については、[名前付け規則 &#40;Azure Cognitive Search&#41;](/rest/api/searchservice/naming-rules) に関する記事を参照してください。  

 スコアリング プロファイルの本文は、重み付けされたフィールドと関数によって構成されます。  

|||  
|-|-|  
|**Weights**|相対的な重みをフィールドに割り当てる名前と値のペアを指定します。 [例](#bkmk_ex)では、albumTitle、genre、および artistName のフィールドはそれぞれ 1.5、5、および 2 でブーストされます。 なぜ genre は他のものよりも大幅に高くブーストされるのでしょうか? ある程度同じようなデータに対して検索を実行する場合 (`musicstoreindex` での "genre" の場合のように)、相対的な重みをより分散することが必要になる可能性があります。 たとえば、 `musicstoreindex`では、「ロック」はジャンルとして表示されると共に、言葉で表現されるジャンルの説明の中にも表示されます。 ジャンルがジャンルの説明を上回るようにする場合は、genre フィールドの相対的な重みをより高くする必要があります。|  
|**関数**|特定のコンテキストに対して追加の計算が必要な場合に使用されます。 有効な値は `freshness`、`magnitude`、`distance`、および `tag` です。 各関数には、固有のパラメーターがあります。<br /><br /> -   `freshness` を使用してください。 この関数は、`datetime` フィールドでのみ使用できます (edm.DataTimeOffset)。 `boostingDuration` 属性は `freshness` 関数のみで使用されることに注意してください。<br />-   `magnitude` を使用してください。 この関数を呼び出すシナリオとしては、利益率、最高価格、最低価格、またはダウンロード回数に基づくブーストがあります。 この関数は、倍精度浮動小数点フィールドと整数フィールドでのみ使用できます。<br />     `magnitude` 関数では、逆のパターンが必要な場合 (たとえば、価格がより高い項目より価格がより低い項目をブーストする場合) に、高範囲から低範囲に反転することができます。 たとえば、価格の範囲が 100 ～ 1 ドルである場合、`boostingRangeStart` を 100 に、`boostingRangeEnd` を 1 に設定して、より低い価格の項目をブーストします。<br />-   `distance` を使用してください。 この関数は、 `Edm.GeographyPoint` フィールドでのみ使用できます。<br />-   `tag` を使用してください。 この関数は、`Edm.String` フィールドと `Collection(Edm.String)` フィールドでのみ使用できます。<br /><br /> **関数の使用に関する規則**<br /><br /> 関数の型 (`freshness`、`magnitude`、`distance`、`tag`) は、小文字にする必要があります。<br /><br /> 関数に null または空の値を含めることはできません。 具体的には、フィールド名を含めた場合、それを何かに設定する必要があります。<br /><br /> 関数はフィルターの適用が可能なフィールドにのみ適用できます。 フィルター可能フィールドについて詳しくは、[インデックスの作成 &#40;Azure Cognitive Search Service REST API&#41;](/rest/api/searchservice/create-index) をご覧ください。<br /><br /> 関数は、インデックスのフィールド コレクションで定義されているフィールドにのみ適用できます。|  

 インデックスが定義されたら、インデックス スキーマをアップロードし、ドキュメントをアップロードして、インデックスを構築します。 これらの操作の手順については、「[インデックスの作成 &#40;Azure Cognitive Search Service REST API&#41;](/rest/api/searchservice/create-index)」と[ドキュメントの追加、更新、削除 &#40;Azure Search Service REST API&#41;](/rest/api/searchservice/addupdate-or-delete-documents) に関する記事をご覧ください。 インデックスが構築されると、検索データと連携する機能的なスコアリング プロファイルが使用できるようになります。  

##  <a name="template"></a><a name="bkmk_template"></a> テンプレート  
 このセクションでは、スコアリング プロファイルの構文とテンプレートを示します。 属性の説明については、次のセクションの「[インデックスの属性のリファレンス](#bkmk_indexref)」をご覧ください。  

```  
. . .   
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "...",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a> インデックスの属性のリファレンス  

> [!NOTE]  
>  スコアリング関数はフィルターの適用が可能なフィールドにのみ適用できます。  

|属性|説明|  
|---------------|-----------------|  
|`name`|必須。 これは、スコアリング プロファイルの名前です。 これは、フィールドの場合と同じ名前付け規則に従います。 先頭は英文字にする必要があります。ドット、コロン、または @ 記号を含めることはできません。さらに、"azureSearch" (大文字小文字の区別あり) という句で開始することはできません。|  
|`text`|重みプロパティが含まれます。|  
|`weights`|省略可能。 フィールド名と相対的な重みをそれぞれ指定する名前と値のペアが含まれます。 相対的な重みは、正の整数または浮動小数点数にする必要があります。<br /><br /> 検索可能フィールド間の相対的な重要度を示すには、重みを使用します。|  
|`functions`|省略可能。 スコアリング関数はフィルターの適用が可能なフィールドにのみ適用できます。|  
|`type`|スコアリング関数の場合は必須です。 使用する関数の型を示します。 有効な値は、magnitude、freshness、distance、tag です。 各スコアリング プロファイルには複数の関数を含めることができます。 関数名は小文字にする必要があります。|  
|`boost`|スコアリング関数の場合は必須です。 生のスコアの乗数として使用される正の数値。 1 にすることはできません。|  
|`fieldname`|スコアリング関数の場合は必須です。 スコアリング関数は、インデックスのフィールド コレクションに属し、フィルターの適用が可能なフィールドにのみ適用できます。 さらに、各関数型には追加の制限 (freshness は日時フィールドで使用され、magnitude は整数または倍精度浮動小数点フィールドで使用され、distance は場所フィールドで使用される) が導入されています。 関数の定義ごとに指定できるフィールドは 1 つだけです。 たとえば、同じプロファイルで magnitude を 2 回使用するには、magnitude の定義を 2 つ含める必要があります (フィールドごとに 1 つ)。|  
|`interpolation`|スコアリング関数の場合は必須です。 スコア ブースティングが範囲の始点から範囲の終点に向かって高くなる場合の傾斜を定義します。 有効な値は、Linear (既定値)、Constant、Quadratic、Logarithmic です。 詳細については、「 [補間の設定](#bkmk_interpolation) 」を参照してください。|  
|`magnitude`|数値フィールドの値の範囲に基づいて順位付けを変更するには、magnitude スコアリング関数を使用します。 最も一般的な使用例には、次のようなものがあります。<br /><br /> -   **星評価:** "星評価" フィールド内の値に基づいて、スコアリングを変更します。 2 つの項目が該当する場合は、評価の高い方の項目が最初に表示されます。<br />-   **利益:** 2 つのドキュメントが該当する場合、小売業者は利益が高い方のドキュメントを最初にブーストできます。<br />-   **クリック回数:** 製品やページに対するクリック操作を追跡するアプリケーションの場合は、magnitude を使用することで、最大トラフィックを示す傾向にある項目をブーストすることが可能です。<br />-   **ダウンロード回数:** ダウンロードを追跡するアプリケーションの場合は、magnitude 関数を使用することで、ダウンロード回数が最も多い項目をブーストすることができます。|  
|`magnitude` &#124; `boostingRangeStart`|magnitude をスコアリングする範囲の開始値を設定します。 値は整数または浮動小数点数にする必要があります。 1 ～ 4 の星評価を対象とする場合、これは 1 になります。 50% を超える利益を対象とする場合、これは 50 になります。|  
|`magnitude` &#124; `boostingRangeEnd`|magnitude をスコアリングする範囲の終了値を設定します。 値は整数または浮動小数点数にする必要があります。 1 ～ 4 の星評価を対象とする場合、これは 4 になります。|  
|`magnitude` &#124; `constantBoostBeyondRange`|有効な値は、true または false (既定値) です。 true に設定すると、対象フィールドの値が範囲の上限を超えているドキュメントに、フル ブーストが継続的に適用されます。 false に設定すると、対象フィールドの値が範囲外にあるドキュメントに対して、この関数のブーストは適用されません。|  
|`freshness`|`DateTimeOffset` フィールドの値に基づいて項目の順位付けのスコアを変更するには、freshness スコアリング関数を使用します。 たとえば、最近の日付を持つ項目に、より古い日付を持つ項目よりも高い順位を付けることができます。<br /><br /> 将来の日付を使用して、カレンダー イベントなどの項目に順位を付けることもできます。たとえば、現在に近い日付の項目を遠い日付の項目よりも高い順位にすることができます。<br /><br /> 現在のサービス リリースでは、範囲の一端が現在の時刻に固定されます。 もう一端は、 `boostingDuration`に基づく過去の時刻です。 将来の時刻の範囲をブーストするには、負の値の `boostingDuration` を使用します。<br /><br /> 最大および最小の範囲からのブースティングの変化率は、スコアリング プロファイルに適用した補間によって決定されます (下図参照)。 適用されるブースティング係数を反転するには、ブースト係数として 1 未満を選択します。|  
|`freshness` &#124; `boostingDuration`|特定のドキュメントに対して有効期限を設定します。この期限を過ぎるとブースティングは停止します。 構文と例については、以下のセクション「[boostingDuration の設定](#bkmk_boostdur)」を参照してください。|  
|`distance`|地理的な参照場所との間の距離に基づいてドキュメントのスコアに影響を与えるには、distance スコアリング関数を使用します。 参照場所は、クエリの一部としてパラメーター (`scoringParameterquery` 文字列オプションを使用) 内に lon,lat 引数で指定します。|  
|`distance` &#124; `referencePointParameter`|参照場所として使用するためにクエリに渡されるパラメーター。 `scoringParameter` はクエリ パラメーターです。 クエリ パラメーターの説明については、「[ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)」を参照してください。|  
|`distance` &#124; `boostingDistance`|ブースティング範囲が終了する、参照場所からの距離をキロメートルで示す数値。|  
|`tag`|ドキュメントと検索クエリのタグに基づいてドキュメントのスコアに影響を与えるには、tag スコアリング関数を使用します。 検索クエリと共通のタグを持つドキュメントがブーストされます。 検索クエリのタグは、(`scoringParameterquery` 文字列オプションを使用して) 各検索要求でスコアリング パラメーターとして提供されます。|  
|`tag` &#124; `tagsParameter`|特定の要求のタグを指定するためにクエリに渡されるパラメーター。 `scoringParameter` はクエリ パラメーターです。 クエリ パラメーターの説明については、「[ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)」を参照してください。|  
|`functionAggregation`|省略可能。 関数を指定した場合にのみ適用されます。 有効な値は、sum (既定値)、average、minimum、maximum、firstMatching です。 検索スコアは、複数の変数 (複数の関数など) から計算される単一の値です。 この属性では、すべての関数のブーストを、基本ドキュメント スコアに適用される 1 つの集計ブーストに集約する方法が示されます。 基本スコアは、ドキュメントと検索クエリから算出される [tf-idf](http://www.tfidf.com/) 値に基づいています。|  
|`defaultScoringProfile`|検索要求の実行時に、スコアリング プロファイルが指定されていない場合は、既定のスコアリングが使用されます ([tf-idf](http://www.tfidf.com/) のみ)。<br /><br /> ここでは既定のスコアリング プロファイル名を設定できます。Azure Cognitive Search は検索要求に特定のプロファイルが指定されていない場合、そのプロファイルを使用します。|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a> 補間の設定  
 補間を使用することにより、スコアリングに使用される傾きの形状を設定できます。 スコアリングは高から低なので、傾きは常に減少ですが、補間により下向きの傾きの曲線が決まります。 次の補間を使用できます。  

| 補間 | 説明 |  
|-|-|  
|`linear`|最大および最小の範囲内にある項目の場合、項目に適用されるブーストは一定の減少量で行われます。 線形はスコアリング プロファイルの既定の補間です。|  
|`constant`|開始と終了の範囲内にある項目については、一定のブーストが順位の結果に適用されます。|  
|`quadratic`|ブーストが一定の割合で低下する線形補間と比較して、二次方程式の場合は、最初は遅いペースで低下しますが、終了範囲に近づくにつれて低下するペースが速くなります。 tag スコアリング関数では、この補間オプションは使用できません。|  
|`logarithmic`|ブーストが一定の割合で低下する線形補間と比較して、対数の場合は、最初は速いペースで低下しますが、終了範囲に近づくにつれて低下するペースが遅くなります。 tag スコアリング関数では、この補間オプションは使用できません。|  

 ![グラフでの定数、線形、二次方程式、log10 の線](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a> boostingDuration の設定  
 `boostingDuration` は `freshness` 関数の属性です。 これを使用して特定のドキュメントに対して有効期限を設定します。この期限が過ぎるとブースティングは停止します。 たとえば、10 日のプロモーション期間中に製品ラインまたはブランドをブーストするには、該当するドキュメントに対して 10 日の期間を "P10D" で指定します。  

 `boostingDuration` は、XSD "dayTimeDuration" 値 (ISO 8601 期間値の制限されたサブセット) としてフォーマットする必要があります。 これのパターンは次のとおりです:"P[nD][T[nH][nM][nS]]"。  

 次の表に、いくつかの例を示します。  

|Duration|boostingDuration|  
|--------------|----------------------|  
|1 日|"P1D"|  
|2 日と 12 時間|"P2DT12H"|  
|約 15 分|"PT15M"|  
|30 日 と 5 時間 10 分 6.334 秒|"P30DT5H10M6.334S"|  

 他の例については、「[XML Schema:Datatypes (XML スキーマ: データ型)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)」(W3.org の Web サイト) をご覧ください。  

## <a name="see-also"></a>関連項目  

+ [REST API リファレンス](/rest/api/searchservice/)
+ [インデックスの作成 API](/rest/api/searchservice/create-index)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search?)