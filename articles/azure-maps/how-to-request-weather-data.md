---
title: Azure Maps Weather Service (プレビュー) を使用して、リアルタイムと予測の気象データを要求する
description: Microsoft Azure Maps Weather Service (プレビュー) を使用して、リアルタイム (現在) と予測 (分ごと、時間ごと、日ごと) の気象データを要求する方法について説明します
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fe1b337fe3e1dcf499f9a7428f66543108d0c050
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680408"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services-preview"></a>Azure Maps Weather Service (プレビュー) を使用して、リアルタイムと予測の気象データを要求する 

> [!IMPORTANT]
> Azure Maps Weather Service は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Maps [Weather Service](/rest/api/maps/weather) は、開発者が高度に動的な履歴、リアルタイム、予測の気象データと視覚化を自身のソリューションに統合できるようにする RESTful API のセットです。 この記事では、リアルタイムと予測の両方の気象データを要求する方法を説明します。

この記事では、以下を行う方法について説明します。

* [Get Current Conditions API](/rest/api/maps/weather/getcurrentconditionspreview) を使用して、リアルタイム (現在) の気象データを要求します。
* [Get Severe Weather Alerts API](/rest/api/maps/weather/getsevereweatheralertspreview) を使用して、悪天候のアラートを要求します。
* [Get Daily Forecast API](/rest/api/maps/weather/getdailyforecastpreview) を使用して、日ごとの予測を要求します。
* [Get Hourly Forecast API](/rest/api/maps/weather/gethourlyforecastpreview) を使用して、時間ごとの予測を要求します。
* [Get Minute Forecast API](/rest/api/maps/weather/getminuteforecastpreview) を使用して、分ごとの予測を要求します。

このビデオでは、Azure Maps Weather Service の REST 呼び出しを行う例を示します。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」を参照してください。

    >[!IMPORTANT]
    >[Get Minute Forecast API](/rest/api/maps/weather/getminuteforecastpreview) には、S1 価格レベルのキーが必要です。 その他すべての API には S0 価格レベルのキーが必要です。

このチュートリアルでは [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境を選択することもできます。

## <a name="request-real-time-weather-data"></a>リアルタイムの気象データを要求する

[Get Current Conditions API](/rest/api/maps/weather/getcurrentconditionspreview) では、特定の座標位置での降水量、気温、風などの詳細な気象条件が返されます。 また、特定の場所での過去 6 時間または 24 時間の観測値も取得できます。 この応答には、観測日時、気象条件の簡単な説明、気象アイコン、降水量インジケーター フラグ、気温などの詳細が含まれます。 RealFeel™ の気温と紫外線 (UV) 指数も返されます。

この例では、[Get Current Conditions API](/rest/api/maps/weather/getcurrentconditionspreview) を使用して、ワシントン州シアトルに位置する座標における現在の気象条件を取得します。

1. Postman アプリを開きます。 Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[コレクション]** を選択します。  コレクションに名前を付け、 **[作成]** ボタンを選択します。 このコレクションは、このドキュメントの残りの例で使用します。

2. 要求を作成するには、 **[新規]** をもう一度選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 要求の **[要求名]** を入力します。 前の手順で作成したコレクションを選択し、 **[Save]\(保存\)** を選択します。

3. ビルダー タブで **GET** HTTP メソッドを選択し、次の URL を入力します。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. 青い **[送信]** ボタンをクリックします。 この応答本文には、現在の気象情報が含まれます。

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>悪天候のアラートを要求する

[Azure Maps Get Severe Weather Alerts API](/rest/api/maps/weather/getsevereweatheralertspreview) では、公式の政府系気象機関および大手のグローバル、または地域的な気象アラート発行元の両方から、世界中で入手できる悪天候のアラートが返されます。 このサービスでは、要求された場所での実際の重大なアラート (ハリケーン、激しい雷雨、落雷、熱波、森林火災など) に関する、アラートの種類、カテゴリ、レベル、詳しい説明などの詳細を返すことができます。 たとえば、物流管理担当者は、重大な気象条件を事業拠点や計画されたルートと共に地図上で視覚化し、ドライバーや現場作業者と詳細に調整できます。

この例では、[Get Severe Weather Alerts API](/rest/api/maps/weather/getsevereweatheralertspreview) を使用して、ワイオミング州シャイアンに位置する座標における現在の気象条件を取得します。

>[!NOTE]
>この例では、この記事の執筆時点での悪天候のアラートを取得します。 要求された場所では悪天候のアラートが解除されている可能性があります。 この例の実行時点での実際の重大なアラート データを取得するには、別の座標位置のデータを取得する必要があります。

1. Postman アプリで、 **[new]\(新規\)** をクリックし、 **[Request]\(\要求\)** を選択します。 要求の **[要求名]** を入力します。 前のセクションで作成したコレクションを選択するか、新しいコレクションを作成して、 **[Save]\(保存\)** を選択します。

2. ビルダー タブで **GET** HTTP メソッドを選択し、次の URL を入力します。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 青い **[送信]** ボタンをクリックします。 悪天候のアラートがない場合、応答本文には空の `results[]` 配列が含まれます。 悪天候のアラートがある場合、応答本文には次のような JSON 応答が含まれます。

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>日ごとの気象予測データを要求する

[Get Daily Forecast API](/rest/api/maps/weather/getdailyforecastpreview) では、気温や風などの日ごとの詳細な気象予測が返されます。 要求では、返される対象の日数を指定できます。特定の座標位置に対して 1 日、5 日、10 日、15 日、25 日、または 45 日です。 この応答には、気温、風、降水量、大気質、UV 指数などの詳細が含まれます。  この例では、`duration=5` を設定して 5 日間を要求します。

>[!IMPORTANT]
>S0 価格レベルでは、今後 1 日、5 日、10 日、15 日間の日ごとの予測を要求できます。 S1 価格レベルでは、今後 25 日間および 45 日間の日ごとの予測も要求できます。

この例では、[Get Daily Forecast API](/rest/api/maps/weather/getdailyforecastpreview) を使用して、ワシントン州シアトルに位置する座標における 5 日間の気象予測を取得します。

1. Postman アプリで、 **[new]\(新規\)** をクリックし、 **[Request]\(\要求\)** を選択します。 要求の **[要求名]** を入力します。 前のセクションで作成したコレクションを選択するか、新しいコレクションを作成して、 **[Save]\(保存\)** を選択します。

2. ビルダー タブで **GET** HTTP メソッドを選択し、次の URL を入力します。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 青い **[送信]** ボタンをクリックします。 この応答本文には、5 日間の気象予測データが含まれます。 簡潔にするために、下の JSON 応答は最初の日の予測を示しています。
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>時間ごとの気象予測データを要求する

[Get Hourly Forecast API](/rest/api/maps/weather/gethourlyforecastpreview) では、特定の座標位置における、今後 1 時間、12 時間、24 時間 (1 日間)、72 時間 (3 日間)、120 時間 (5 日間)、240 時間 (10 日間) の時間ごとの詳しい気象予測が返されます。 この API では、気温、湿度、風、降水量、UV 指数などの詳細が返されます。

>[!IMPORTANT]
>S0 価格レベルでは、今後 1 時間、12 時間、24 時間 (1 日間)、および 72 時間 (3 日間) の時間ごとの予測を要求できます。 S1 価格レベルでは、今後 120 時間 (5 日間) および240 時間 (10 日間) の時間ごとの予測も要求できます。

この例では、[Get Hourly Forecast API](/rest/api/maps/weather/gethourlyforecastpreview) を使用して、ワシントン州シアトルに位置する座標における今後 12 時間の時間ごとの気象予測を取得します。

1. Postman アプリで、 **[new]\(新規\)** をクリックし、 **[Request]\(\要求\)** を選択します。 要求の **[要求名]** を入力します。 前のセクションで作成したコレクションを選択するか、新しいコレクションを作成して、 **[Save]\(保存\)** を選択します。

2. ビルダー タブで **GET** HTTP メソッドを選択し、次の URL を入力します。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 青い **[送信]** ボタンをクリックします。 この応答本文には、今後 12 時間の気象予測データが含まれます。 簡潔にするために、下の JSON 応答は最初の 1 時間の予測を示しています。

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>分ごとの気象予測データを要求する

 [Get Minute Forecast API](/rest/api/maps/weather/getminuteforecastpreview) では、特定の場所における今後 120 分間の分ごとの予測が返されます。 ユーザーは、1 分、5 分、15 分の間隔で気象予測を要求できます。 この応答には、降水の種類 (雨、雪、または両方の組み合わせを含む)、開始時刻、降水強度値 (dBZ) などの詳細が含まれます。

この例では、[Get Minute Forecast API](/rest/api/maps/weather/getminuteforecastpreview) を使用して、ワシントン州シアトルに位置する座標における分ごとの気象予測を取得します。 気象予測は今後 120 分間について表示されます。 このクエリでは 15 分間隔で予測が表示されるように要求していますが、このパラメーターを 1 分または 5 分に調整できます。

1. Postman アプリで、 **[new]\(新規\)** をクリックし、 **[Request]\(\要求\)** を選択します。 要求の **[要求名]** を入力します。 前のセクションで作成したコレクションを選択するか、新しいコレクションを作成して、 **[Save]\(保存\)** を選択します。

2. ビルダー タブで **GET** HTTP メソッドを選択し、次の URL を入力します。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 青い **[送信]** ボタンをクリックします。 この応答本文には、15 分間隔での、今後 120 分間の気象予測データが含まれます。

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Maps Weather Service (プレビュー) の概念](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [Azure Maps Weather Service (プレビュー) の REST API](/rest/api/maps/weather)