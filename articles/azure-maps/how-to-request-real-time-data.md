---
title: Azure Maps でリアルタイム データを要求する方法 | Microsoft Docs
description: Azure Maps Mobility Service を使用してリアルタイム データを要求します。
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4c53d1c1ffbc80e694a9a7b423b2aaf9c6d38b48
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914379"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Azure Maps Mobility Service を使用してリアルタイム データを要求する

この記事では、Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) を使用してリアルタイムの輸送データを要求する方法を説明します。

この記事では、次のことについて説明します。


 * 特定の停留所に到着するすべての路線について次のリアルタイムの到着を要求する
 * 特定の自転車ドック ステーションのリアルタイム情報を要求する。


## <a name="prerequisites"></a>前提条件

Azure Maps 公共輸送 API を呼び出すには、Maps アカウントとキーが必要です。 アカウント作成の詳細については、[アカウントの管理](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account)に関するページにある手順に従ってください。また、[主キーの取得](./tutorial-search-location.md#getkey)に関するページの手順に従い、アカウントのプライマリ サブスクリプション キーを取得します。

この記事では、[Postman アプリ](https://www.getpostman.com/apps)を使用して REST 呼び出しを構築します。 選択した任意の API 開発環境を使用できます。


## <a name="request-real-time-arrivals-for-a-stop"></a>停留所のリアルタイムの到着を要求する

特定の公共輸送機関の停留所に関するリアルタイムの到着データを要求するには、Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) の [Real-time Arrivals API](https://aka.ms/AzureMapsMobilityRealTimeArrivals) に要求を送信する必要があります。 要求を完了するには、**metroID** と **stopID** が必要です。 これらのパラメーターを要求する方法の詳細については、[公共輸送機関のルートの要求](https://aka.ms/AMapsHowToGuidePublicTransitRouting)に関する攻略ガイドを参照してください。 

ここでは都市圏 ID として "Seattle–Tacoma–Bellevue, WA" /地域の都市圏 ID である "522" を使用し、"Ne 24th St & 162nd Ave Ne, Bellevue WA" のバス停である停留所 ID "522---2060603" を使用します。 この停留所での次のすべてのライブ到着について、次の 5 つのリアルタイム到着データを要求するには、次の手順を実行します。

1. 要求を格納するコレクションを作成します。 Postman アプリ内で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[コレクション]** を選択します。 コレクションに名前を付け、 **[作成]** ボタンを選択します。

2. 要求を作成するには、 **[新規]** をもう一度選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 要求に**要求名**を入力し、前の手順で要求の保存場所として作成したコレクションを選択し、 **[保存]** を選択します。

    ![Postman での要求の作成](./media/how-to-request-transit-data/postman-new.png)

3. [builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、次の URL を入力して GET 要求を作成します。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. 要求が成功すると、次の応答が表示されます。  パラメーター 'scheduleType' では、到着予定時刻がリアルタイム データと静的データのどちらに基づいているかを定義する点に注目してください。

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>自転車ドック ステーションのリアルタイム データ

Azure Maps Mobility Service の [Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) を使用して、特定の自動車またはスクーターのドック ステーションの可用性や空き情報などの静的なリアルタイム情報を要求できます。 自転車のドッキング ステーションのリアルタイム データを取得する要求を行います。

Get Transit Dock Info API への要求を行うためには、そのステーションの **dockId** が必要です。 [Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit) への検索要求を行い、**objectType** パラメーターを "bikeDock" に設定することで、ドック ID を取得できます。 自転車のドッキング ステーションのリアルタイム データを取得するには、次の手順に従います。


### <a name="get-dock-id"></a>ドック ID を取得する

**dockID** を取得するには、次の手順に従って Get Nearby Transit API への要求を行います。

1. Postman で、 **[新しい要求]**  |  **[GET 要求]** をクリックし、「**Get dock ID (ドック ID の取得)** 」 という名前を付けます。

2.  [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、次の要求 URL を入力して **[Send]\(送信\)** をクリックします。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. 要求が成功すると、次の応答が表示されます。 応答に **id** が含まれていることに注目してください。これは、後で Get Transit Dock Info API への要求でクエリ パラメーターとして使用できます。

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>リアルタイムの自転車ドックの状態を取得する

以下の手順に従って、Get Transit Dock Info API に対する要求を行い、選択したドックのリアルタイム データを取得します。

1. Postman で、 **[New Request]\(新しい要求\)**  |  **[GET request]\(GET 要求\)** をクリックし、「**Get Route info**」という名前を付けます。

2.  [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、次の要求 URL を入力して **[Send]\(送信\)** をクリックします。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. 要求が成功すると、次の構造の応答を受け取ります。

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>次の手順

Mobility Service を使用して輸送データを要求する方法について説明します。

> [!div class="nextstepaction"]
> [輸送データを要求する方法](how-to-request-transit-data.md)

Azure Maps Mobility Service API のドキュメントを確認します。

> [!div class="nextstepaction"]
> [Mobility Service API ドキュメント](https://aka.ms/AzureMapsMobilityService)
