---
title: Azure Notebooks を使用して電気自動車のルートを案内する (Python) | Microsoft Docs
description: Azure Maps のルート案内 API シリーズと Azure Notebooks を使用して、電気自動車のルートを案内します。
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9cc7fc1ba8c7f55700505ea8fca0dea4f836e333
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243301"
---
# <a name="route-electric-vehicles-by-using-azure-notebooks-python"></a>Azure Notebooks を使用して電気自動車のルートを案内する (Python)

Azure Maps は、Azure にネイティブに統合された地理空間サービス API シリーズのポートフォリオです。 これらの API シリーズを使用すると、開発者、企業、ISV は、場所を認識するアプリと、IoT、モビリティ、物流、資産追跡のソリューションを作成できます。 

Azure Maps REST API シリーズを Python や R などの言語で呼び出して、地理空間データ解析や機械学習のシナリオを実現できます。 Azure Maps に用意された堅牢な[ルート案内 API シリーズ](https://docs.microsoft.com/rest/api/maps/route)のセットを使用することで、ユーザーは、複数のデータ ポイント間のルートを計算できます。 計算は、車両の種類や到達可能な範囲などのさまざまな条件に基づいています。 

このチュートリアルでは、バッテリ残量が低下している電気自動車のドライバーに対して、自動車の場所からの走行時間が最も近い充電スタンドを案内するシナリオを見ていきます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * クラウドの [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) 上で Jupyter ノートブックを作成して実行する。
> * Python で Azure Maps REST API シリーズを呼び出す。
> * 電気自動車の消費モデルに基づいて到達可能範囲を調べる。
> * 到達可能範囲 (つまり等時線) 内にある電気自動車充電スタンドを検索する。
> * 到達可能範囲の境界線と充電スタンドをマップ上にレンダリングする。
> * 走行時間に基づいて最も近い電気自動車充電スタンドまでのルートを特定し、視覚化する。


## <a name="prerequisites"></a>前提条件 

このチュートリアルを完了するには、まず Azure Maps アカウントを作成して、プライマリ キー (サブスクリプション キー) を取得する必要があります。 

S1 価格レベルで Azure Maps アカウントのサブスクリプションを作成するには、「[Azure Maps アカウントを管理する](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account)」の手順に従います。 

アカウントのプライマリ サブスクリプション キーを取得するには、「[Azure Maps を使用して近くの目的地を検索する](./tutorial-search-location.md#getkey)」の手順に従います。

## <a name="create-an-azure-notebook"></a>Azure ノートブックを作成する

このチュートリアルに沿って作業を進めるには、Azure ノートブック プロジェクトを作成し、Jupyter ノートブック ファイルをダウンロードして、実行する必要があります。 ノートブック ファイルには、本チュートリアルのシナリオを実装する Python コードが含まれています。 Azure ノートブック プロジェクトを作成し、Jupyter ノートブック ドキュメントをそれにアップロードするには、次のようにします。

1. [Azure Notebooks](https://notebooks.azure.com) に移動してサインインします 詳細については、「[クイック スタート: サインインとユーザー ID の設定](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)」を参照してください。
1. パブリック プロファイル ページの上部にある **[マイ プロジェクト]** を選択します。

    ![[マイ プロジェクト] ボタン](./media/tutorial-ev-routing/myproject.png)

1. **[マイ プロジェクト]** ページで **[新しいプロジェクト]** を選択します。
 
   ![[新しいプロジェクト] ボタン](./media/tutorial-ev-routing/create-project.png)

1. **[新しいプロジェクトの作成]** ウィンドウで、プロジェクト名とプロジェクト ID を入力します。
 
    ![[新しいプロジェクトの作成] ウィンドウ](./media/tutorial-ev-routing/create-project-window.png)

1. **作成** を選択します。

1. プロジェクトが作成されたら、[Azure Maps の Jupyter ノートブック リポジトリ](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)から [Jupyter ノートブック ドキュメント ファイル](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb)をダウンロードします。 

1. **[マイ プロジェクト]** ページのプロジェクト一覧で、自分のプロジェクトを選択し、 **[アップロード]** を選択して Jupyter ノートブック ドキュメント ファイルをアップロードします。 

    ![ノートブックのアップロード](./media/tutorial-ev-routing/upload-notebook.png)

1. お使いのコンピューターからファイルをアップロードし、 **[完了]** を選択します。

1. アップロードが正常に完了すると、プロジェクト ページにファイルが表示されます。 ファイルを選択し、Jupyter ノートブックとして開きます。

ノートブック ファイルで実装される機能についての理解を深めるため、ノートブックのコードを一度に 1 セルずつ実行することをお勧めします。 Notebook アプリの上部にある **[実行]** ボタンを選択して、各セルのコードを実行できます。

  ![[実行] ボタン](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>プロジェクト レベルのパッケージをインストールする

ノートブックのコードを実行するには、次のようにして、プロジェクト レベルでパッケージをインストールします。

1. [Azure Maps の Jupyter ノートブック リポジトリ](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)から [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) ファイルをダウンロードして、自分のプロジェクトにアップロードします。
1. プロジェクト ダッシュボードで、 **[Project Settings]\(プロジェクトの設定\)** を選択します。 
1. **[プロジェクトの設定]** ウィンドウで、 **[環境]** タブを選択し、 **[追加]** を選択します。
1. **[Environment Setup Steps]\(環境セットアップ手順\)** で、次のようにします。   
    a. 1 つ目のドロップダウン リストで、**Requirements.txt** を選択します。  
    b. 2 つ目のドロップダウン リストで、*requirements.txt* ファイルを選択します。  
    c. 3 つ目のドロップダウン リストで、バージョンとして **[Python Version 3.6]** を選択します。
1. **[保存]** を選択します。

    ![パッケージをインストールする](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>必要なモジュールとフレームワークを読み込む

必要なモジュールとフレームワークをすべて読み込むには、次のスクリプトを実行します。

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>到達可能範囲の境界線を要求する

このシナリオの配送会社には、電気自動車が何台か導入されています。 日中は、倉庫に戻ることなく電気自動車を再充電しなければなりません。 現在の充電残量が 1 時間分を下回る (つまり、バッテリ残量が低下する) たびに、到達可能範囲内にある充電スタンドを検索して、その範囲の境界情報を取得する必要があります。 

会社は経済性とスピードのバランスがとれたルートを使いたいと考えているので、要求する routeType は *eco* になります。 次のスクリプトでは、車両の消費モデルのパラメーターを使用して、Azure Maps ルート案内サービスの [Get Route Range API](https://docs.microsoft.com/rest/api/maps/route/getrouterange) を呼び出します。 その後、スクリプトでは、応答が解析されて GeoJSON 形式の polygon オブジェクトが作成されます。これは、自動車の最大到達可能範囲を表します。

電気自動車の到達可能範囲の境界を特定するには、次のセルのスクリプトを実行します。

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>到達可能範囲内にある電気自動車充電スタンドを検索する

電気自動車の到達可能範囲 (等時線) を特定した後は、その範囲内にある充電スタンドを検索できます。 

次のスクリプトでは、Azure Maps の [Post Search Inside Geometry API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) が呼び出されます。 それでは、自動車の最大到達可能範囲の境界内にある電気自動車充電スタンドが検索され、応答が解析されて、到達可能な場所の配列が取得されます。

到達可能範囲内にある電気自動車充電スタンドを検索するには、次のスクリプトを実行します。

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Azure Maps Data Service に到達可能範囲と充電ポイントをアップロードする

電気自動車の最大到達可能範囲に対する充電スタンドと境界を、地図上に視覚化する必要があります。 これを行うには、[Data Upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) を使用して、境界のデータと充電スタンドのデータを GeoJSON オブジェクトとして Azure Maps Data Service にアップロードします。 

境界と充電ポイントのデータを Azure Maps Data Service にアップロードするには、次の 2 つのセルを実行します。

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data Service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data Service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>マップ上に充電スタンドと到達可能範囲をレンダリングする

データ サービスにデータをアップロードした後は、次のスクリプトを実行して、Azure Maps の [Get Map Image Service](https://docs.microsoft.com/rest/api/maps/render/getmapimage) を呼び出し、静的マップ画像の上に充電ポイントと最大到達可能範囲の境界をレンダリングします。

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![場所の範囲を示すマップ](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>最適な充電スタンドを探す

到達可能範囲内にある充電スタンド候補をすべて特定した後は、最も短い時間で到達できるスタンドを調べる必要があります。 

次のスクリプトで呼び出している Azure Maps の [Matrix Routing API](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) では、指定した自動車の位置に対し、各充電スタンドまでの走行時間と距離が返されます。 次のセルのスクリプトでは、応答が解析されて、時間的に最も近くにある到達可能な充電スタンドの場所が特定されます。

最も近くにあり最短時間で到達可能な充電スタンドを探すには、次のセルのスクリプトを実行します。

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>最も近い充電スタンドへのルートを計算する

最も近い充電スタンドがわかったので、[Get Route Directions API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) を呼び出して、電気自動車の現在位置から充電スタンドまでの詳細なルートを要求できます。

充電スタンドまでのルートを取得し、応答を解析してルートを表す GeoJSON オブジェクトを作成するには、次のセルのスクリプトを実行します。

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>ルートを視覚化する

ルートを視覚化するには、まず Azure Maps の [Data Upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) を使用して、Azure Maps Data Service にルートのデータを GeoJSON オブジェクトとしてアップロードします。 次に、レンダリング サービス [Get Map Image API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) を呼び出して、マップ上にルートをレンダリングして視覚化します。

マップ上にルートがレンダリングされた画像を取得するには、次のスクリプトを実行します。

```python
# Upload the route data to Azure Maps Data Service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![ルートを示すマップ](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Python を使用して Azure Maps REST API シリーズを直接呼び出し、Azure Maps のデータを視覚化する方法について説明しました。

このチュートリアルで使用した Azure Maps API シリーズの詳細については、以下を参照してください。

* [Get Route Range](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Post Search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Data Upload](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [レンダリング - Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Post Route Matrix](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Azure Maps REST API シリーズの完全な一覧については、[Azure Maps REST API](https://docs.microsoft.com/azure/azure-maps/#reference) に関する記事を参照してください。

Azure Notebooks の詳細については、[Azure Notebooks](https://docs.microsoft.com/azure/notebooks) に関する記事を参照してください。
