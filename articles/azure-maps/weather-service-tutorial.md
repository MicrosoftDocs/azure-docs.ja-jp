---
title: チュートリアル:Azure Notebooks (Python) を使用して天気予報データにセンサー データを結合する | Microsoft Docs
description: チュートリアル:このチュートリアルでは、Azure Notebooks (Python) を使用して、Azure Maps Weather Service の天気予報データにセンサー データを結合する方法について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 8f641640ff6cf4174e2e1374404d47fc0760f79f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979578"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>チュートリアル:Azure Notebooks (Python) を使用して天気予報データにセンサー データを結合する

風力は、気候変動への対策として化石燃料に代わるエネルギー源です。 風というのはもともと一定のものではないため、風力のオペレーターは ML (機械学習) モデルを作成して、電力需要を満たす風力発電能力を予測し、送電網の安定性を確保する必要があります。 このチュートリアルでは、センサーの場所と気象測定値を含んだデモ データ セットに Azure Maps の天気予報データを結合する方法について取り上げます。 天気予報データを要求するには、Azure Maps Weather Service を呼び出します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * クラウド内の [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) でデータ ファイルを操作する。
> * ファイルからデモ データを読み込む。
> * Python で Azure Maps REST API シリーズを呼び出す。
> * マップ上に場所データをレンダリングする。
> * Azure Maps の気象データ ([毎日の予報](https://aka.ms/AzureMapsWeatherDailyForecast)) でデモ データをエンリッチする。
> * 予測データをグラフにプロットする。


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、まず以下を実行する必要があります。

1. [Azure Maps アカウントの管理](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account)に関するページの手順に従い、S0 価格レベルで Azure Maps アカウントのサブスクリプションを作成します。
2. [アカウントのプライマリ キーの取得](./tutorial-search-location.md#getkey)に関するページの手順に従い、アカウントのプライマリ サブスクリプション キーを取得します。

Azure Notebooks の概要と基本的な使用方法については、「[Azure ノートブックを作成する](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook)」の手順に従ってください。

> [!Note]
> このプロジェクトの Jupyter Notebook ファイルは、[Weather Maps Jupyter Notebook リポジトリ](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data)からダウンロードできます。

## <a name="load-the-required-modules-and-frameworks"></a>必要なモジュールとフレームワークを読み込む

必要なモジュールとフレームワークをすべて読み込むには、次のスクリプトを実行します。

```python
import aiohttp
import pandas as pd
import datetime
from IPython.display import Image, display
```

## <a name="import-weather-data"></a>気象データをインポートする

このチュートリアルのために、ここでは、4 つの異なる風力タービンに設置されたセンサーからの気象データの測定値を利用します。 サンプル データは、各タービンに近接する気象データ センターから収集された 30 日間分の気象データ測定値から成ります。 デモ データには、気温、風速、風向の各データの測定値が含まれています。 デモ データは、[こちら](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data)からダウンロードできます。 次のスクリプトでは、デモ データを Azure Notebook にインポートします。

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>毎日の予報データを要求する

この例のシナリオでは、センサーの設置場所ごとの毎日の予報を要求します。 以下のスクリプトでは、Azure Maps Weather Service の [Daily Forecast API](https://aka.ms/AzureMapsWeatherDailyForecast) を呼び出して、現在の日付から 15 日間の日単位の天気予報を、それぞれの風力タービンについて取得します。


```python
subscription_key = "Your Azure Maps primary subscription key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

次のスクリプトでは、Azure Maps の [Get Map Image サービス](https://docs.microsoft.com/rest/api/maps/render/getmapimage)を呼び出してタービンの場所をマップ上にレンダリングします。

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![タービンの場所](./media/weather-service-tutorial/location-map.png)


デモ データを予測データで強化するために、ここでは気象データ センターのステーション ID に基づいて、予測データとデモ データをグループ化します。

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

次の表は、各地に設置されたタービンの 1 つについて、履歴データと予測データを併せて表示したものです。

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![グループ化されたデータ](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>予測データをプロットする

今後 15 日間に風速と風向がどのように変化するかを見るために、それぞれの日付の予測値をプロットします。

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

次のグラフは、風速 (左側のグラフ) と風向 (右側のグラフ) の変化についての予測データを現在の日付から 15 日間にわたって視覚化したものです。

<center>

![風速のプロット](./media/weather-service-tutorial/speed-date-plot.png) ![風向のプロット](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Maps の REST API を呼び出して天気予報データを取得し、そのデータをグラフで視覚化する方法について説明しました。

Azure Maps の REST API を Azure Notebooks 内で呼び出す方法については、[Azure Notebooks を使用した電気自動車のルート案内](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing)に関するページを参照してください。

このチュートリアルで使用した Azure Maps API シリーズの詳細については、以下を参照してください。

* [毎日の予報](https://aka.ms/AzureMapsWeatherDailyForecast)
* [レンダリング - Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Azure Maps REST API シリーズの完全な一覧については、[Azure Maps REST API](https://docs.microsoft.com/azure/azure-maps/#reference) に関する記事を参照してください。

Azure Notebooks の詳細については、[Azure Notebooks](https://docs.microsoft.com/azure/notebooks) に関する記事を参照してください。
