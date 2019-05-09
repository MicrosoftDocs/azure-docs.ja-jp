---
title: Azure Maps を使用してジオフェンスを作成する | Microsoft Docs
description: Azure Maps を使用してジオフェンスを設定します。
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 112d0bd4b6802179692d0d177775027e552d1170
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085322"
---
# <a name="set-up-a-geofence-by-using-azure-maps"></a>Azure Maps を使用してジオフェンスを設定する

このチュートリアルでは、Azure Maps を使用してジオフェンスを設定する基本的な手順について説明します。 建設現場の管理者は、危険を伴うおそれのある機材が、指定された建設領域から出ないよう監視します。このチュートリアルでは、そうした管理者を支援するシナリオについて取り上げます。 建設現場には高価な機材が存在し、規制も伴います。 そのような機材は通常、建設現場内に留め、許可なく持ち出されることのないようにする必要があります。

Azure Maps Data Upload API を使用してジオフェンスを保存し、Azure Maps の Geofence API を使用して、ジオフェンスを基準とした機材の位置をチェックします。 Azure Event Grid を使用してジオフェンスの結果をストリーム配信し、ジオフェンスの結果に基づく通知を設定します。
Event Grid の詳細については、[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) に関するページを参照してください。
このチュートリアルでは、次の方法を学習します。

> [!div class="checklist"]
> * Data Upload API を使用して Azure Maps のデータ サービスにジオフェンス領域をアップロードする。
> *   ジオフェンス イベントを処理する Event Grid を設定する。
> *   ジオフェンス イベントのハンドラーを設定する。
> *   ジオフェンス イベントに反応するアラートを Logic Apps を使用して設定する。
> *   Azure Maps のジオフェンス サービス API シリーズを使用して、建設用資産が建設現場内に存在するかどうかを追跡する。


## <a name="prerequisites"></a>前提条件

### <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する 

このチュートリアルの手順を実行する前に、[アカウントとキーの管理](how-to-manage-account-keys.md)に関するページを参照して、S1 価格レベルのアカウント サブスクリプションを作成、管理する必要があります。

## <a name="upload-geofences"></a>ジオフェンスのアップロード

Data Upload API を使用して建設現場のジオフェンスをアップロードするには、postman アプリケーションを使用します。 このチュートリアルの目的上、建設現場の全体的な領域が存在することを前提としており、この領域は、建設機材が確実に遵守すべきパラメーターです。 このフェンスを越えることは重大な違反であり、Operations Manager にレポートされます。 最適化した一連のフェンスを追加で使用すれば、全体的な建設領域内の異なる建設領域をスケジュールに従って追跡することができます。 メインのジオフェンスにはサブサイト 1 があるものとします。サブサイト 1 には設定された有効期限があり、それを過ぎると期限切れになります。 実際の要件に従って、入れ子になったジオフェンスをさらに作成することができます。 たとえば、サブサイト 1 はスケジュールの第 1 週から第 4 週にかけて作業が発生し、サブサイト 2 は第 5 週から第 7 週にかけて作業が発生するという具合です。 そのすべてのフェンスを単一のデータセットとしてプロジェクトの最初に読み込んでおき、時間と空間に基づくルールの追跡に使用することができます。 ジオフェンスのデータ形式の詳細については、[ジオフェンスの GeoJSON データ](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)に関するページを参照してください。 Azure Maps サービスへのデータのアップロードの詳細については、[Data Upload API のドキュメント](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)を参照してください。

Azure Maps の Data Upload API を使用して建設現場のジオフェンスをアップロードするには、Postman アプリを開いて次の手順に従います。

1. Postman アプリを開き、[new]\(新規\)、[Create new]\(新規作成\) の順にクリックして、[Request]\(要求\) を選択します。 ジオフェンス データのアップロードに対する要求名を入力して、これを保存するコレクションまたはフォルダーを選択し、[Save]\(保存\) をクリックします。

    ![Postman を使用してジオフェンスをアップロードする](./media/tutorial-geofence/postman-new.png)

2. [builder]\(ビルダー\) タブで POST HTTP メソッドを選択し、POST 要求を行うための次の URL を入力します。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL パス内の GEOJSON パラメーターは、アップロードするデータの形式を表します。

3. **[Params]\(パラメーター\)** をクリックして、POST 要求の URL に使用する次のキーと値のペアを入力します。 subscription-key の値は、実際の Azure Maps のサブスクリプション キーに置き換えてください。
   
    ![Postman のキーと値のペアから成るパラメーター](./media/tutorial-geofence/postman-key-vals.png)

4. **[Body]\(本文\)** をクリックして未加工入力形式を選択し、入力形式として JSON をドロップダウン リストから選択します。 アップロードするデータとして次の JSON を入力します。

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. [send]\(送信\) をクリックして、応答ヘッダーを確認します。 今後使用するためにデータをダウンロードしたりデータにアクセスしたりするための URI は、location ヘッダーに格納されます。 また、アップロードされたデータの一意の `udId` もそこに格納されます。

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

## <a name="set-up-an-event-handler"></a>イベント ハンドラーの設定

Operations Manager に enter イベントと exit イベントを通知するには、通知を受け取るイベント ハンドラーを作成する必要があります。

ここでは、enter イベントと exit イベントを処理するための 2 つの [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) サービスを作成します。 さらに、これらのイベントによってトリガーされるイベント トリガーを Logic Apps 内に作成します。 建設現場に対して機材が出し入れされたときに Operations Manager にアラート (この場合はメール) を送信することがそのねらいです。 次の図は、ジオフェンスの enter イベントに使用するロジック アプリの作成を示します。 同様に、exit イベント用にもう 1 つ作成できます。
詳細については、[サポートされているすべてのイベント ハンドラー](https://docs.microsoft.com/azure/event-grid/event-handlers)に関するページを参照してください。

1. Azure portal でロジック アプリを作成します。

   ![ロジック アプリを作成する](./media/tutorial-geofence/logic-app.png)

2. HTTP 要求トリガーを選択し、Outlook Connector のアクションとして [電子メールの送信] を選択します
  
   ![Logic Apps スキーマ](./media/tutorial-geofence/logic-app-schema.png)

3. ロジック アプリを保存して HTTP URL エンドポイントを生成し、HTTP URL をコピーします。

   ![Logic Apps エンドポイント](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Azure Maps イベントのサブスクリプションの作成

Azure Maps では、3 種類のイベントがサポートされています。 Azure Maps でサポートされるイベントの種類は、[こちら](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)で確認できます。 ここでは、サブスクリプションを 2 つ作成します。1 つは enter イベント用で、もう 1 つは exit イベント用です。

以下の手順に従って、ジオフェンスの enter イベント用にサブスクリプションを作成します。 ジオフェンスの exit イベントも同様の方法でサブスクライブできます。

1. [こちらのポータル リンク](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/)から Azure Maps アカウントに移動し、[イベント] タブを選択します。

   ![Azure Maps の [イベント]](./media/tutorial-geofence/events-tab.png)

2. イベント サブスクリプションを作成するには、[イベント] ページの [イベント サブスクリプション] を選択します。

   ![Azure Maps のイベント サブスクリプション](./media/tutorial-geofence/create-event-subscription.png)

3. イベント サブスクリプションに名前を付け、イベントの種類として Enter をサブスクライブします。 次に、[エンドポイントのタイプ] として [Web hook] を選択し、ロジック アプリの HTTP URL エンドポイントを [エンドポイント] にコピーします。

   ![イベント サブスクリプション](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Geofence API の使用

Geofence API を使用すると、**デバイス** (機材は状態の一部) がジオフェンスの内側にあるのか外側にあるのかを確認することができます。 ジオフェンスの GET API に対する理解を深めるために、 特定の機材が時間の経過に伴って移動される状況下で、さまざまな場所を照会します。 次の図は、一意の**デバイス ID** を持つ特定の建設機材が時系列で観察された 5 つの場所を示したものです。 5 つの場所はそれぞれ、フェンスに対する enter と exit の状態の変化を評価する目的で使用されます。 状態の変化が生じた場合、ジオフェンス サービスからイベントがトリガーされ、そのイベントが Event Grid によってロジック アプリに送信されます。 その結果、対応する enter または exit の通知が、作業の管理者にメールで送信されます。

> [!Note]
> 前述のシナリオと動作は、同じ**デバイス ID** に基づくものです。以下の図にある 5 つの場所が、この ID で示されます。

![ジオフェンス マップ](./media/tutorial-geofence/geofence.png)

Postman アプリで、先ほど作成したコレクションの新しいタブを開きます。 [builder]\(ビルダー\) タブで GET HTTP メソッドを選択します。

以下は、5 つの HTTP GET Geofencing API 要求です。それぞれ、機材が時系列で観察された場所の対応する座標を指定しています。 それぞれの要求に続けて応答本文を示しています。
 
1. 場所 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![ジオフェンス クエリ 1](./media/tutorial-geofence/geofence-query1.png)

   上の応答を見ると、メイン ジオフェンスからの距離が負数になっていますが、これは機材がメイン ジオフェンス内にあることを意味します。サブサイトのジオフェンスからの距離は正数になっていることから、サブサイトのジオフェンスの外にあることがわかります。 

2. 場所 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![ジオフェンス クエリ 2](./media/tutorial-geofence/geofence-query2.png)

   前の JSON 応答をよく見ると、機材がサブサイトの外側、かつメイン フェンスの内側にあります。 この場合は、イベントがトリガーされず、メールは送信されません。

3. 場所 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![ジオフェンス クエリ 3](./media/tutorial-geofence/geofence-query3.png)

   状態の変化が発生しました。機材はメイン ジオフェンスの内側、かつサブサイト ジオフェンスの内側にあります。 これによりイベントが発行され、Operations Manager には通知メールが送信されます。

4. 場所 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![ジオフェンス クエリ 4](./media/tutorial-geofence/geofence-query4.png)

   対応する応答をよく観察すると、機材がサブサイト ジオフェンスから出たにもかかわらず、イベントが発行されていません。 ユーザーが GET 要求で指定した時刻を見てみると、この時刻に対して、サブサイト ジオフェンスは有効期限が切れており、また、機材は依然としてメイン ジオフェンス内に存在することがわかります。 また、サブサイト ジオフェンスのジオメトリ ID は応答本文の `expiredGeofenceGeometryId` で確認できます。


5. 場所 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![ジオフェンス クエリ 5](./media/tutorial-geofence/geofence-query5.png)

   建設現場のメイン ジオフェンスから機材が移動されたことが確認できます。 これによってイベントが発行されます。これは重大な違反であるため、Operations Manager には、重要なアラート メールが送信されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Data Upload API を使用して Azure Maps のデータ サービスにジオフェンスをアップロードすることで、ジオフェンスを設定する方法を学習しました。 また、Azure Maps の Event Grid を使用してジオフェンス イベントをサブスクライブし、処理する方法も学習しました。 

* ロジック アプリを使用して JSON を解析し、より複雑なロジックを構築する方法については、「[Azure Logic Apps における各種コンテンツの扱い](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)」を参照してください。
* Event Grid のイベント ハンドラーの詳細については、[Event Grid でサポートされるイベント ハンドラー](https://docs.microsoft.com/azure/event-grid/event-handlers)に関するページを参照してください。
