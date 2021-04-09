---
title: Azure Maps Search Service のベスト プラクティス | Microsoft Azure Maps
description: Microsoft Azure Maps から Search Service を使用する場合のベスト プラクティスを適用する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 11c1938c3c1ccba533f52336fad81ebeaae53b24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895479"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Azure Maps Search Service のベスト プラクティス

Azure Maps [Search Service](/rest/api/maps/search) には、開発者が住所、場所、名前またはカテゴリ別の事業の一覧、およびその他の地理情報を検索するために役立つさまざまな機能を提供する API が含まれています。 たとえば、[あいまい検索 API](/rest/api/maps/search/getsearchfuzzy) を使用して、住所または目的地 (POI) を検索できます。

この記事では、Azure Maps Search Service からデータを呼び出す際に、サウンド プラクティスを適用する方法を説明します。 学習内容は次のとおりです。
> [!div class="checklist"]
> * 関連する一致を返すクエリを作成する
> * 検索の結果を制限する
> * 結果の種類の違いを確認する
> * 住所検索の応答構造を確認する

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

この記事では、[Postman アプリ](https://www.postman.com/downloads/)を使用して REST 呼び出しを構築しますが、任意の API 開発環境を選択できます。

## <a name="best-practices-to-geocode-addresses"></a>ジオコード アドレスに関するベスト プラクティス

Azure Maps Search Service を使って住所の全部または一部を検索すると、API は検索クエリからキーワードを読み取ります。 次に、住所の経度と緯度の座標を返します。 このプロセスは *ジオコーディング* と呼ばれています。

ある国またはリージョンでジオコーディングする機能は、道路データの可用性とジオコーディング サービスの精度に左右されます。 国または地域による Azure Maps のジオコーディング機能の詳細については、[ジオコーディングの対象範囲](./geocoding-coverage.md)を参照してください。

### <a name="limit-search-results"></a>検索の結果を制限する

 Azure Maps Search API は、検索結果を適切に制限するのに役立ちます。 ユーザーに関連データを表示できるように、結果を制限します。

> [!NOTE]
> 検索 API は、この記事で説明されているものだけでなく、より多くのパラメーターをサポートしています。

#### <a name="geobiased-search-results"></a>ジオバイアスされた検索結果

ユーザーの関連領域に結果をジオバイアスするには、常にできるだけ多くの場所の詳細を追加します。 次の入力型を指定して、検索結果を制限できます。

* `countrySet`パラメーターを設定します。 たとえば、`US,FR`に設定できます。 デフォルトでは、API は全世界を検索するため、不要な結果が返される可能性があります。 クエリに`countrySet`パラメーターがない場合、検索で不正確な結果が返される可能性があります。 たとえば、"*ベルビュー*" という名前の都市を検索すると、米国とフランスの両方に "*ベルビュー*" という名前の都市があるため、両方の国またはリージョンの結果が返されます。

* `btmRight` パラメーターと `topleft` パラメーターを使用して、境界ボックスを設定できます。 これらのパラメーターは検索をマップ上の特定領域に制限します。

* 結果の関連性の領域に影響を与えるには、`lat` と `lon` 座標パラメーターを定義します。 `radius` パラメーターを使用して、検索領域の半径を設定します。


#### <a name="fuzzy-search-parameters"></a>あいまい検索パラメーター

検索クエリに対するユーザー入力がわからない場合は、Azure Maps[Search Fuzzy API](/rest/api/maps/search/getsearchfuzzy)を使用することをお勧めします。 たとえば、ユーザーからの入力が、住所だったり、目的地 (POI) (*ショッピングモール* など) だったりすることがあります。 この API は、POI 検索およびジオコーディングを正規の *一行検索* で組み合わせます。 

* `minFuzzyLevel` パラメーターと `maxFuzzyLevel` パラメーターを使用すると、クエリパラメーターがユーザーが必要とする情報と完全に一致しない場合でも、関連する一致を返すことができます。 パフォーマンスを最大化し、異常な結果を減らすには、検索クエリをデフォルトの`minFuzzyLevel=1`および`maxFuzzyLevel=2`に設定します。 

    たとえば、`maxFuzzyLevel`パラメーターが 2 に設定されている場合、検索語句 *restrant* は *restaurant* と一致します。 必要に応じて、デフォルトのあいまいレベルをオーバーライドできます。 

* `idxSet`パラメータを使用して、結果タイプの正確なセットに優先順位を付けます。 結果の正確なセットに優先順位を付けるには、コンマで区切られたインデックスのリストを送信します。 リストでは、アイテムの順序は関係ありません。 Azure Maps は以下のインデックスをサポートしています。

* `Addr` - **住所範囲**:道路の始点と終点から補間された住所ポイント。 これらの地点は、住所範囲として表されます。
* `Geo` - **地域**: 土地の行政区画です。 たとえば、地域は国またはリージョン、州、市などとなります。
* `PAD` - **ポイントアドレス**:街路の名前と番号を含む住所です。 ポイントアドレスはインデックスにあります。 例は *Soquel Dr 2501* です。 ポイントアドレスは、住所に利用できる最高レベルの精度を提供します。  
* `POI` - **目的地**:注目に値する、または興味深いと思われるマップ上のポイントです。 [Search Address API](/rest/api/maps/search/getsearchaddress)では POI は返されません。  
* `Str` - **街路**: マップ上の街路です。
* `XStr` - **交差点**:2 本の街路が交差するジャンクションまたはその場所です。


#### <a name="usage-examples"></a>使用例

* `idxSet=POI` - POIのみを検索します。 

* `idxSet=PAD,Addr` - アドレスのみを検索します。 `PAD`はポイントアドレス、`Addr`は住所範囲を示します。

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>geography エンティティ型の逆ジオコードとフィルター

[Search Address Reverse API](/rest/api/maps/search/getsearchaddressreverse)で逆ジオコード検索を実行すると、サービスは行政区分のポリゴンを返すことができます。 たとえば、都市の区分のポリゴンをフェッチできます。  特定の geography エンティティ型に検索を絞り込むには、要求に`entityType`パラメーターを含めます。 

結果の応答には、一致した geography IDとエンティティ型が含まれます。 複数のエンティティを指定すると、エンドポイントは *使用可能な最小のエンティティ* が返されます。 返された geometry ID を使用して、[Search Polygon サービス](/rest/api/maps/search/getsearchpolygon)を介して geography のジオメトリを取得できます。

#### <a name="sample-request"></a>要求のサンプル

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Response

```JSON
{
    "summary": {
        "queryTime": 14,
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

### <a name="set-the-results-language"></a>結果の言語を設定する

`language`パラメーターを使用して、返される検索結果の言語を設定します。 要求で言語が設定されていないと、Search Service では規定でその国または地域で最も一般的な言語が使用されます。 指定した言語で使用できるデータがない場合は、既定の言語が使用されます。 

詳細については、 [Azure Maps でサポートされている言語](./supported-languages.md) を参照してください。


### <a name="use-predictive-mode-automatic-suggestions"></a>予測モードの使用 (自動提案)

部分的なクエリに対して、より多くの一致を検索するには、`typeahead`パラメーターを`true`に設定します。 このクエリは部分的な入力として解釈され、検索は予測モードになります。 `typeahead`パラメーターを`true`に設定しない場合、サービスはすべての関連情報が渡されたと見なします。

次のサンプル クエリでは、*Microsoft* に対して Search Address サービスがクエリされます。 ここでは、`typeahead`パラメータが`true`に設定されています。 応答は、検索サービスがクエリを部分クエリとして解釈したことを示しています。 応答には、自動提案のクエリの結果が含まれています。

#### <a name="sample-query"></a>サンプル クエリ

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Response

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>URI をエンコードして特殊文字を処理する 

交差点の住所を検索するには、住所の特殊文字を処理するために URI をエンコードする必要があります。 次の住所の例について考えてみましょう。*1st Avenue & Union Street, Seattle*. ここでは、アンパサンド文字 (`&`) をエンコードしてから要求を送信します。 

文字データを URI にエンコードすることをお勧めします。 URI では、パーセント記号 (`%`) と、文字の UTF-8 コードに対応する二文字の十六進値を使用して、すべての文字をエンコードします。

#### <a name="usage-examples"></a>使用例

次のアドレスから開始します。

```
query=1st Avenue & E 111th St, New York
```

アドレスをエンコードします。

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

次の方法を使用できます。

JavaScript または TypeScript:
```javascript
encodeURIComponent(query)
```

C# または Visual Basic:
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


## <a name="best-practices-for-poi-searching"></a>POI 検索のベスト プラクティス

POI 検索では、POI 結果を名前で要求できます。 たとえば、名前でビジネスを検索できます。 

アプリケーションにカバレッジが必要な国またはリージョンを指定するには、`countrySet`パラメーターを使用することを強くお勧めします。 既定の動作では、世界全体を検索します。 この広範な検索では不要な結果が返され、検索に時間がかかる場合があります。

### <a name="brand-search"></a>ブランドの検索

応答の結果と情報の関連性を改善するために、POI 検索応答はブランド情報が含まれています。 この情報を使用して、応答をさらに解析できます。

要求では、コンマで区切られたブランド名のリストを送信できます。 リストを使用して`brandSet`パラメータを設定し、結果を特定のブランドに限定します。 リストでは、アイテムの順序は関係ありません。 複数のブランドリストを提供する場合、返される結果は少なくとも1つのリストに属している必要があります。

ブランド検索を調べるために、[POI カテゴリー検索](/rest/api/maps/search/getsearchpoicategory)要求を作成しましょう。 次の例では、ワシントン州レドモンドにある Microsoft キャンパス近くでガソリンスタンドを探しています。 応答には、返された各 POI のブランド情報が表示されます。

#### <a name="sample-query"></a>サンプル クエリ

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Response

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
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
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>空港の検索

Search POI API を使用すると、空港の公式コードを使用して空港を検索できます。 たとえば、*SEA* を使用してシアトル/タコマ国際空港を検索できます。 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>近隣検索

特定の場所に関する POI の結果を取得するには、[Search Nearby API](/rest/api/maps/search/getsearchnearby) を使用してみてください。 エンドポイントは POI の結果のみを返します。 検索クエリパラメーターでは使用されません。 

結果を制限するには、半径を設定することをお勧めします。

## <a name="understanding-the-responses"></a>応答について

Azure Maps Search Service に対してアドレス検索を要求して、シアトルの住所を検索してみましょう。 次の要求 URL では、`countrySet`パラメーターを`US`に設定して、米国内の住所を検索します。

### <a name="sample-query"></a>サンプル クエリ

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>サポートされている種類の結果

* **ポイントアドレス**:街路の名前と番号がある特定の住所を持つマップ上のポイントです。 ポイントアドレスは、住所に利用できる最高レベルの精度を提供します。 

* **アドレス範囲**:街路の始点と終点から補間されたアドレスポイントの範囲です。  

* **geography**: 国またはリージョン、州、市など、土地の行政区画を表すマップ上の領域です。 

* **POI**:注意する価値があり、興味を引く可能性があるマップ上のポイントです。

* **街路**:マップ上の街路です。 住所を含む街路の緯度および経度の座標に住所が解決されます。 家番号は処理されない可能性があります。 

* **交差道路**:交差部分。 交差道路は、2 つの街路が交わる場所であるジャンクションを表します。

### <a name="response"></a>Response

応答構造を見てみましょう。 次の応答では、結果オブジェクトの種類が異なります。 よく見ると、次の 3 種類の結果オブジェクトがあります。

* ポイント住所
* Street
* 交差道路

アドレス検索では POI が返されないことに注意してください。  

各応答オブジェクトの`Score`パラメーターは、一致スコアが同じ応答内の他のオブジェクトのスコアとどのように関連しているかを示します。 応答オブジェクトのパラメーターについて詳しくは、[Get Search Address](/rest/api/maps/search/getsearchaddress)を参照してください。

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>ジオメトリ

*Geometr* の応答型には、`geometry`および`id`の下の`dataSources`オブジェクトで返される geometry ID を含めることができます。 たとえば、[Search Polygon サービス](/rest/api/maps/search/getsearchpolygon)を使用して、GeoJSON 形式の geometry データを要求することができます。 この形式を使用すると、一連のエンティティに対して、都市または空港のアウトラインを取得できます。 次に、[ジオフェンスの設定](./tutorial-geofence.md) または [ジオメトリ内の POI 検索 ](/rest/api/maps/search/postsearchinsidegeometry)に対して、この境界データを使用できます。


[Search Address](/rest/api/maps/search/getsearchaddress)API または[Search Fuzzy](/rest/api/maps/search/getsearchfuzzy)API に対する応答には、`geometry`および`id`の下の`dataSources`オブジェクトで返されたジオメトリ ID を含めることができます。


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Azure Maps Search Service の要求を作成する方法](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Search Service API ドキュメント](/rest/api/maps/search)