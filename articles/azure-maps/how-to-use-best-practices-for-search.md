---
title: Azure Maps Search Service を使用して効率よく検索する方法 | Microsoft Docs
description: Azure Maps Search Service を使用して検索するためのベスト プラクティスの使用方法について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 996a084fd653b2100d94313e8801d915b4bf2cf3
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348182"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Azure Maps Search Service を使用するためのベスト プラクティス

Azure Maps の [Search Service](https://docs.microsoft.com/rest/api/maps/search) には、住所検索から、特定の場所での目的地 (POI) データの検索まで、さまざまな機能を備えた API が含まれます。 この記事では、Azure Maps Search サービスを使用してデータを呼び出すためのベスト プラクティスについて説明します。 学習内容:

* 関連する一致を返すクエリを作成する
* 検索の結果を制限する
* さまざまな結果の種類の違いについて学習する
* アドレス検索の応答の構造を読む


## <a name="prerequisites"></a>前提条件

Maps サービス API を呼び出すには、Maps アカウントとキーが必要です。 アカウントを作成し、キーを取得する方法については、「[How to manage your Azure Maps account and keys](how-to-manage-account-keys.md)」(Azure Maps アカウントとキーを管理する方法) を参照してください。

> [!Tip]
> Search Service のクエリを実行するには、[Postman アプリ](https://www.getpostman.com/apps)を使用して REST 呼び出しを作成するか、または好みの API 開発環境を使用することができます。


## <a name="best-practices-for-geocoding"></a>ジオコーディングに関するベスト プラクティス

Azure Maps Search Service を使用して、完全な住所または部分的な住所を検索するとき、サービスは検索語句を受け取って、住所の経度と緯度の座標を返します。 このプロセスはジオコーディングと呼ばれます。 ある国でジオコーディングする機能は、ジオコーディング サービスによる道路データの網羅率とジオコーディングの精度に左右されます。

国/地域による Azure Maps のジオコーディング機能について詳しくは、[ジオコーディングの対象範囲](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)に関する記事をご覧ください。

### <a name="limit-search-results"></a>検索の結果を制限する

   このセクションでは、Azure Maps Search API を使用して検索結果を制限する方法を説明します。 

   > [!Note]
   > Search API によっては、以下で示すパラメーターを完全にサポートしていない場合があります

   **検索結果に geo バイアスを適用する**

   ユーザーに関連する領域になるよう結果に geo バイアスを適用するには、常に、可能な限り詳細な場所入力を追加する必要があります。 検索結果を制限するには、次の入力の種類の追加を検討します。

   1. `countrySet` パラメーターを設定します (例: US、FR)。 既定の検索動作では世界全体が検索されるので、必要のない結果が返される可能性があります。 クエリに `countrySet` パラメーターが含まれていない場合、検索で不正確な結果が返される可能性があります。 たとえば、**Bellevue** という名前の都市を検索すると、**Bellevue** という名前の都市はフランスと米国にあるので、米国とフランスからの結果が返されます。

   2. `btmRight` および `topleft` パラメーターを使用して境界ボックスを設定し、マップ上の特定の領域に検索を制限することができます。

   3. 結果の関連領域に影響を与えるには、`lat` と `lon` の座標パラメーターを定義し、`radius` パラメーターを使用して検索領域の半径を設定できます。


   **あいまい検索パラメーター**

   1. `minFuzzyLevel` と `maxFuzzyLevel` を使用すると、クエリ パラメーターが目的の情報に正確に対応していない場合でも、関連する一致を返すことができます。 ほとんどの検索クエリでは、パフォーマンスを高めて想定外の結果を減らすため、既定で `minFuzzyLevel=1` と `maxFuzzyLevel=2` に設定されます。 検索語句 "restrant" を例にすると、`maxFuzzyLevel` が 2 に設定されている場合は "restaurant" と一致します。 既定のあいまいレベルは、要求のニーズに応じてオーバーライドできます。 

   2. また、`idxSet` パラメーターを使用することで、正確な結果の種類のセットを返すように指定することもできます。 そのためには、インデックスのコンマ区切りリストを送信できます。項目の順序は関係ありません。 次のインデックスがサポートされています。

       * `Addr` - **住所範囲**: 街路によっては、街路の開始と終了から補間される住所ポイントが存在します。このようなポイントは、住所範囲として表されます。
       * `Geo` - **地域**: 土地の行政区分を表すマップ上の領域です (つまり、国、州、市)。
       * `PAD` - **ポイント住所**: 街路名と番号を持つ特定の住所がインデックスで見つかるマップ上のポイントです (例: Soquel Dr 2501)。 それは住所に利用できる最高レベルの精度です。  
       * `POI` - **目的地**: 注目する価値があり興味を引く可能性があるマップ上のポイントです。  [Get Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) では POI は返されません。  
       * `Str` - **街路**: マップ上の街路を表します。
       * `XStr` - **交差点**: 2 つの街路が交わる場所であるジャンクションを表します。


       **使用例** :

       * idxSet=POI (目的地のみを検索します) 

       * idxSet=PAD,Addr (住所のみを検索します、PAD = ポイント住所、Addr = 住所範囲)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>逆ジオコーディングと geography エンティティ型フィルター

[Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) で逆ジオコーディング検索を実行すると、サービスは行政区分に対する多角形を返すことができます。 要求で `entityType` パラメーターを指定することにより、指定した geography エンティティ型に検索を絞り込むことができます。 結果の応答には、geography ID および一致したエンティティ型が含まれます。 複数のエンティティを指定すると、エンドポイントからは**使用可能な最小のエンティティ**が返されます。 返されたジオメトリ ID を使用して、[Get Polygon サービス](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)によりその geography のジオメトリを取得することができます。

**要求のサンプル:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**応答:**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>検索結果の言語

`language` パラメーターを使用すると、返される検索結果の言語を設定できます。 要求で言語を設定しないと、Search Service では既定で自動的に国/地域で最も一般的な言語が使用されます。 また、指定した言語のデータが利用できない場合も、既定の言語が使用されます。 国/地域ごとに Azure Maps サービスでサポートされている言語の一覧については、[サポートされる言語](https://docs.microsoft.com/azure/azure-maps/supported-languages)に関する記事をご覧ください。


### <a name="predictive-mode-auto-suggest"></a>予測モード (自動提案)

部分的なクエリに対して、より多くの一致を検索するには、`typeahead` パラメーターを "true" に設定する必要があります。 クエリは部分的な入力として解釈され、検索は予測モードになります。 そうでない場合、サービスはすべての関連情報が渡されたものと想定します。

下のサンプル クエリでは、`typeahead` パラメーターを **true** に設定して、Search Address Service で "Microso" のクエリを実行しています。 応答を確認すると、Search Service でクエリが部分クエリとして解釈され、応答に自動提案されたクエリの結果が含まれていることがわかります。

**サンプル クエリ:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**応答:**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>特殊文字を処理するための URI エンコード 

交差点の住所を検索するには (つまり、"1st Avenue & Union Street, Seattle")、要求を送信する前に特殊文字 "&" をエンコードする必要があります。 URI で文字データをエンコードすることをお勧めします。その場合、すべての文字を、"%" 文字と、UTF-8 文字に対応する 2 文字の 16 進値を使用してエンコードします。

**使用例**:

Get Search Address:

```
query=1st Avenue & E 111th St, New York
```

 次のようにエンコードする必要があります。

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


さまざまな言語に使用する異なる方法を次に示します。 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Go:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>POI 検索のベスト プラクティス

目的地 (POI) 検索では、名前で POI の結果を要求できます (たとえば、名前による企業の検索)。 `countrySet` パラメーターを使用して、アプリケーションで対象にする必要がある国を指定することを強くお勧めします。既定の動作では全世界が検索されるため、必要のない結果が返されたり、検索間が長くなったりする可能性があります。

### <a name="brand-search"></a>ブランドの検索

結果と応答内の情報の関連性を向上させるため、目的地 (POI) 検索の応答には、応答をさらに解析するために使用できるブランド情報が含まれます。

Microsoft キャンパス (ワシントン州レドモンド) の近くのガソリン スタンドに関する [POI Category Search](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) 要求を行ってみましょう。 応答を確認すると、各 POI に対するブランド情報が返されることがわかります。

**サンプル クエリ:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**応答:**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>空港の検索

POI Search では、空港の公式の空港コードを使用して検索できます。 たとえば、**SEA** (シアトル タコマ国際空港) などです。 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>近隣検索

特定の場所の周囲の POI 結果のみを取得するには、[Nearby Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) が適した選択肢である場合があります。 このエンドポイントでは POI の結果のみが返され、検索クエリ パラメーターは使用されません。 結果を制限するには、半径を設定することをお勧めします。

## <a name="understanding-the-responses"></a>応答について

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) に対してシアトルの住所のを住所検索要求を行ってみましょう。 以下の要求 URL を慎重に確認すると、`countrySet` パラメーターを **US** に設定して、アメリカ合衆国の住所を検索していることがわかります。

**サンプル クエリ:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

さらに、次の応答の構造を見てみましょう。 応答に含まれる結果オブジェクトの結果の種類が異なります。 慎重に観察すると、3 種類の結果オブジェクト "Point Address"、"Street"、"Cross Street" が含まれることがわかります。 住所検索では POI が返されないことに注意してください。 各応答オブジェクトの `Score` パラメーターは、同じ応答内の他のオブジェクトのスコアに対する相対的な一致スコアを示します。 応答オブジェクトのパラメーターについて詳しくは、「[Get Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)」をご覧ください。

**サポートされる結果の種類:**

* **ポイント住所:** 街路名と番号による特定の住所を持つマップ上のポイント。 住所に利用できる最高レベルの精度です。 

* **住所範囲:** 街路によっては、街路の開始と終了から補間される住所ポイントが存在します。このようなポイントは、住所範囲として表されます。 

* **地理的な場所:** 土地の行政区分を表すマップ上の領域です (つまり、国、州、市)。 

* **POI - (目的地):** 注目する価値があり興味を引く可能性があるマップ上のポイントです。

* **街路:** マップ上の街路を表します。 住所を含む通りの緯度/経度座標に住所が解決されます。 家番号は処理されない場合があります。 

* **交差点:** 交差部分。 2 つの街路が交わる場所であるジャンクションを表します。

**応答:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>ジオメトリ

応答の種類が**ジオメトリ**のときは、"geometry" と "id" の下にある **dataSources** オブジェクトで返されたジオメトリ ID が含まれる場合があります。 たとえば、[Get Polygon サービス](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)では、エンティティのセットに対する都市や空港の輪郭など、GeoJSON 形式で geometry 型のデータを要求することができます。 [ジオフェンシング](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence)または[ジオメトリ内の POI 検索](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)に対して、この境界データを使用できます。


[Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) または [Search Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API の応答には、"geometry" および "id" の下の dataSources オブジェクトで返される**ジオメトリ ID** が含まれる場合があります。


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>次の手順

* [Azure Maps Search Service の要求を作成する方法](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)を学習する。
* Azure Maps [Search Service API のドキュメント](https://docs.microsoft.com/rest/api/maps/search)を確認する。 
