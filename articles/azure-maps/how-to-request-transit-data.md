---
title: Azure Maps の公共輸送機関データを要求する方法 | Microsoft Docs
description: Azure Maps Mobility Service を使用して公共輸送機関のデータを要求します。
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b28788ac7b3ce4e1997b71c683f8e0445406a391
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915632"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Azure Maps Mobility Service を使用して公共輸送機関のデータを要求します 

この記事では、Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) を使用して、停留所、ルート情報、予想される移動時間などの公共輸送機関のデータを要求する方法を示します。

この記事では、次のことについて説明します。

* [Get Metro Area API](https://aka.ms/AzureMapsMobilityMetro) を使用して、都市圏 ID を取得します
* [Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) サービスを使用して、近くの停留所情報を要求します。
* [Get Transit Routes API](https://aka.ms/AzureMapsMobilityTransitRoute) にクエリを実行し、公共輸送機関を使用してルートを計画します。
* [Get Transit Itinerary API](https://aka.ms/https://azure.microsoft.com/services/azure-maps/) を使用して、輸送ルート ジオメトリおよびルートについての詳細なスケジュールを要求します。


## <a name="prerequisites"></a>前提条件

Azure Maps 公共輸送 API を呼び出すには、Maps アカウントとキーが必要です。 アカウント作成の詳細については、[アカウントの管理](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account)に関するページにある手順に従ってください。また、[主キーの取得](./tutorial-search-location.md#getkey)に関するページの手順に従い、アカウントのプライマリ サブスクリプション キーを取得します。

この記事では、[Postman アプリ](https://www.getpostman.com/apps)を使用して REST 呼び出しを構築します。 選択した任意の API 開発環境を使用できます。


## <a name="get-a-metro-area-id"></a>都市圏 ID を取得する

特定の都市圏の輸送情報を要求するには、輸送データを要求するエリアの `metroId` が必要になります。 [Get Metro Area API](https://aka.ms/AzureMapsMobilityMetro) によって、Azure Maps Mobility Service を利用可能な都市圏を要求できます。 応答には `metroId`、`metroName` などの詳細情報と、GeoJSON 形式での都市圏のジオメトリ表現が含まれます。

それでは、シアトル/タコマの都市圏 ID を取得するための要求を行ってみましょう。 都市圏 ID を要求するには、次の手順を実行します。

1. 要求を格納するコレクションを作成します。 Postman アプリ内で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[コレクション]** を選択します。 コレクションに名前を付け、 **[作成]** ボタンを選択します。

2. 要求を作成するには、 **[新規]** をもう一度選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 要求に**要求名**を入力し、前の手順で要求の保存場所として作成したコレクションを選択し、 **[保存]** を選択します。
    
    ![Postman での要求の作成](./media/how-to-request-transit-data/postman-new.png)

3. [builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、次の URL を入力して GET 要求を作成します。

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. 要求が成功すると、次の応答が表示されます。

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

5. 後で使用するために、`metroId` をコピーします。

## <a name="request-nearby-transit-stops"></a>近くの公共輸送機関の停留所を要求する

Azure Maps の [Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) サービスでは、指定された場所の周囲にある輸送オブジェクト (公共輸送機関の停留所やバイクシェアなど) を検索して、輸送オブジェクトの詳細情報を返すことができます。 次に、特定の場所から半径 300 m 以内にある近くの公共輸送機関の停留所を検索するようサービスに要求します。 要求には、先ほど取得した `metroId` を含める必要があります。

[Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) に対する要求を行うには、次の手順に従います。

1. Postman で、 **[新しい要求]**  |  **[GET request]\(GET 要求\)** をクリックして、「**Get Nearby stops**」 (近くの停留所の取得) という名前を付けます。

2. [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、API エンドポイントの次の要求 URL を入力して **[Send]\(送信\)** をクリックします。

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. 要求が成功すると、応答の構造は次のようになります。

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    }   
    ```

応答の構造を注意深く観察すると、各オブジェクトのパラメーター (`id`、`type`、`stopName`、`mainTransitType`、`mainAgencyName` など) およびオブジェクトの位置 (座標) が含まれていることが確認できます。

理解を深めるために、次のセクションでは、バスの停留所のうち 1 つの `id` をルートの起点として使用します。  


## <a name="request-a-transit-route"></a>輸送ルートを要求する

Azure Maps の [Get Transit Routes API](https://aka.ms/AzureMapsMobilityTransitRoute) を使用すると、起点と目的地の間で最良のルート オプションを返す移動計画が可能になります。 サービスでは、徒歩、自転車、公共輸送機関などのさまざまな移動手段が提供されます。 次に、最も近いバス停からシアトルの Space Needle へのルートを検索します。

### <a name="get-location-coordinates-for-destination"></a>目的地の位置座標を取得する

Space Needle の位置座標を取得するために、Azure Maps の[ファジー検索サービス](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)を使用します。

ファジー検索サービスを要求するには、次の手順に従います。

1. Postman で、 **[新しい要求]**  |  **[GET request]\(GET 要求\)** をクリックして、「**Get location coordinates**」 (位置座標の取得) という名前を付けます。

2.  [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、次の要求 URL を入力して **[Send]\(送信\)** をクリックします。
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. 応答を注意深く見てみると、Space Needle の結果に複数の場所が含まれており、それぞれの位置座標情報が **position** の下に含まれています。 最初の結果の position から `lat` および `lon` をコピーします。
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
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
            ...,
            ...,
            ...
        ]
    }
    ``` 
    

### <a name="request-route"></a>ルートを要求する

ルートの要求を行うには、次の手順を実行します。

1. Postman で、 **[新しい要求]**  |  **[GET request]\(GET 要求\)** をクリックして、「**Get Route info**」 (ルート情報の取得) という名前を付けます。

2. [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、API エンドポイントの次の要求 URL を入力して **[Send]\(送信\)** をクリックします。

    `modeType` と `transitType` のパラメーターを指定することによって、バスについての公共輸送機関のルートを要求します。 要求 URL には、前のセクションで取得した場所が含まれています。 `originType` として **stopId** が指定され、`destionationType` として **position** が指定されています。

    [Get Transit Routes API](https://aka.ms/AzureMapsMobilityTransitRoute) への要求で使用できる [URI パラメーターの一覧](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters)を参照してください。 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. 要求が成功すると、応答の構造は次のようになります。

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ]
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ]
            }
        ]
    }
    ```

4. 注意深く観察すると、応答には複数の **bus** ルートがあります。 各ルートには、一意の **itinerary ID** およびルートの各区間を記述した概要があります。 次に、応答の `itineraryId` を使用して、最も速いルートについての詳細を要求します。

## <a name="request-fastest-route-itinerary"></a>最も速いルートの移動プランを要求する

Azure Maps の [Get Transit Itinerary](https://aka.ms/AzureMapsMobilityTransitItinerary) サービスでは、[Get Transit Routes API](https://aka.ms/AzureMapsMobilityTransitRoute) サービスによって返されるルートの **itinerary ID** を使用して、特定のルートについてのデータを要求することができます。 要求を行うには、次の手順を実行します。

1. Postman で、 **[新しい要求]**  |  **[GET request]\(GET 要求\)** をクリックして、「**Get Transit info**」 (輸送情報の取得) という名前を付けます。

2. [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、API エンドポイントの次の要求 URL を入力して **[Send]\(送信\)** をクリックします。

    `detailType` パラメーターを **geometry** に設定することで、公共輸送機関の停留所情報と、ルートの徒歩および自転車区間の道案内ナビゲーションが応答に含まれるようにします。

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. 要求が成功すると、応答の構造は次のようになります。

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>次の手順

Mobility Service を使用してリアルタイム データを要求する方法について説明します。

> [!div class="nextstepaction"]
> [リアルタイム データを要求する方法](how-to-request-real-time-data.md)

Azure Maps Mobility Service API のドキュメントを確認します

> [!div class="nextstepaction"]
> [Mobility Service API ドキュメント](https://aka.ms/AzureMapsMobilityService)

