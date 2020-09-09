---
title: チュートリアル:Microsoft Azure Maps でジオフェンスを作成してデバイスを追跡する
description: ジオフェンスを設定する方法について説明します。 Azure Maps 空間サービスを使用して、ジオフェンスを基準にデバイスを追跡する方法をご確認ください。
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3ea9923dd98a49b1533defa3e95616655b7ea78d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299305"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>チュートリアル:Azure Maps を使用してジオフェンスを設定する

このチュートリアルでは、Azure Maps ジオフェンス サービスの作成と使用の基礎について、次のシナリオを背景に説明します。

*建設現場の管理者が、建設区域の境界を出入りする機材を追跡する必要があります。それらの境界を機材が出入りするたびに、電子メールによる通知が作業の管理者に送信されます。*

上記のシナリオにおいて、建設区域を出入りする機材の追跡をサポートするさまざまなサービスが Azure Maps には用意されています。 このチュートリアルでは、次の方法について取り上げます。

> [!div class="checklist"]
> * 監視対象の建設現場区域を定義する[ジオフェンシング GeoJSON データ](geofence-geojson.md)をアップロードする。 [Data Upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) を使用して、ジオフェンスをポリゴン座標として Azure Maps アカウントにアップロードします。
> * 機材がジオフェンス領域を出入りしたときにトリガーされ、電子メールによる通知を建設現場の作業の管理者に送信する 2 つの[ロジック アプリ](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps)を設定する。
> * [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) を使用して Azure Maps ジオフェンスの enter イベントと exit イベントをサブスクライブする。 ここでは、2 つのロジック アプリに定義された HTTP エンドポイントを呼び出す Web hook イベントのサブスクリプションを 2 つ設定します。 これらのロジック アプリから、ジオフェンスを出入りする機材の適切な電子メール通知が送信されます。
> * 機材がジオフェンス領域を出入りしたときに、[Search Geofence Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) を使用して通知を受信する。

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

このチュートリアルでは [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境を選択することもできます。

## <a name="upload-geofencing-geojson-data"></a>ジオフェンシング GeoJSON データをアップロードする

このチュートリアルでは、`FeatureCollection` を含んだジオフェンシング GeoJSON データをアップロードします。 `FeatureCollection` には、建設現場内の多角形領域を定義する 2 つのジオフェンスが含まれています。 1 つ目のジオフェンスには、時間の有効期限や制限はありません。 2 つ目のジオフェンスは、照会できる期間が作業時間 (太平洋標準時の午前 9 時から午後 5 時) に限られ、また 2022 年 1 月 1 日を過ぎると無効となります。 PST), and will no longer be valid after January 1, 2022. GeoJSON 形式の詳細については、「[ジオフェンシング GeoJSON データ](geofence-geojson.md)」を参照してください。

>[!TIP]
>ジオフェンシング データはいつでも更新することができます。 データを更新する方法について詳しくは、[Data Upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) に関するページを参照してください。

1. Postman アプリを開きます。 Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[コレクション]** を選択します。  コレクションに名前を付け、 **[作成]** ボタンを選択します。

2. 要求を作成するには、 **[新規]** をもう一度選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 要求の **[要求名]** を入力します。 前の手順で作成したコレクションを選択し、 **[Save]\(保存\)** を選択します。

3. ビルダー タブで **POST** HTTP メソッドを選択し、次の URL を入力して、ジオフェンシング データを Azure Maps サービスにアップロードします。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    URL パス内の _geojson_ パラメーターは、アップロードするデータの形式を表します。

4. **[本文]** タブをクリックします。 **[raw]\(未加工\)** を選択し、入力形式として **[JSON]** を選択します。 次の GeoJSON データをコピーして **[本文]** テキスト領域に貼り付けます。

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
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
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

5. 青色の **[Send]\(送信\)** ボタンをクリックし、要求が処理されるまで待ちます。 要求が完了したら、応答の **[Headers]\(ヘッダー\)** タブに移動します。 **Location** キーの値である `status URL` をコピーします。

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. API 呼び出しの状態を確認するには、`status URL` に対して **GET** HTTP 要求を作成します。 認証のために、プライマリ サブスクリプション キーを URL に追加する必要があります。 **GET** 要求は次の URL のようになります。

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. **GET** HTTP 要求が正常に完了すると、`resourceLocation` が返されます。 `resourceLocation` には、アップロードされたコンテンツの一意の `udid` が格納されます。 このチュートリアルの最後のセクションで Get Geofence API に対するクエリを実行するには、この `udid` を保存する必要があります。 次の手順では任意で、`resourceLocation` URL を使用してこのリソースからメタデータを取得できます。

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. コンテンツ メタデータを取得するには、手順 7 で取得した `resourceLocation` URL で **GET** HTTP 要求を作成します。 認証のために、プライマリ サブスクリプション キーを URL に必ず追加します。 **GET** 要求は次の URL のようになります。

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. **GET** HTTP 要求が正常に完了すると、応答本文には、手順 7 の `resourceLocation` で指定された `udid`、今後のコンテンツのアクセスおよびダウンロード場所、作成または更新された日付、サイズなどのコンテンツに関するその他のメタデータが含まれます。 全体的な応答の例を次に示します。

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-logic-app-workflows"></a>ロジック アプリ ワークフローを作成する

このセクションでは、電子メールによる通知をトリガーする 2 つの[ロジック アプリ](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) エンドポイントを作成します。 ここでは 1 つ目のトリガーの作成方法について説明します。対応するエンドポイントが呼び出されるたび、電子メールによる通知が送信されます。

1. [Azure ポータル](https://portal.azure.com)

2. [Azure Portal](https://portal.azure.com) の左上隅にある **[リソースの作成]** をクリックします。

3. *[Marketplace を検索]* ボックスに「**Logic App**」と入力します。

4. "*結果*" から **[Logic App]** を選択します。 **[作成]** をクリックします。

5. **[Logic App]** ページで、次の値を入力します。
    * このロジック アプリに使用する "*サブスクリプション*"。
    * このロジック アプリの "*リソース グループ*" の名前。 *[新規作成]* を選んで新しく作成することも、 *[既存のものを使用]* を選んで既存のリソース グループを使うこともできます。
    * "*ロジック アプリ名*"。 この例では、名前として `Equipment-Enter` を使用します。

    このチュートリアルの目的上、他の値は、既定の設定のままにしてください。

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="ロジック アプリの作成":::

6. **[確認および作成]** ボタンをクリックします。 設定を確認し、 **[作成]** をクリックしてデプロイを送信します。 デプロイが正常に完了したら、 **[リソースに移動]** をクリックします。 **[ロジック アプリ デザイナー]** に誘導されます。

7. 今度は、トリガーの種類を選択します。 少し下へスクロールして、 *[一般的なトリガーで開始する]* * セクションに移動します。 **[HTTP 要求の受信時]** をクリックします。

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="ロジック アプリの HTTP トリガーを作成する":::

8. デザイナーの右上隅にある **[保存]** をクリックします。 **HTTP POST の URL** が自動的に生成されます。 この URL は保存しておいてください。次のセクションでイベントのエンドポイントを作成する際に必要となります。

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="ロジック アプリの HTTP 要求の URL と JSON":::

9. **[+ 新しいステップ]** を選択します。 次に、アクションを選択します。 検索ボックスに、「`outlook.com email`」と入力します。 **[アクション]** の一覧を下へスクロールし、 **[メールの送信 (V2)]** をクリックします。
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="ロジック アプリ デザイナーを作成する":::

10. Outlook.com アカウントにサインインします。 アカウントへのアクセスをロジック アプリに許可するために、必ず **[はい]** をクリックしてください。 メール送信に関するフィールドに値を入力します。

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="ロジック アプリのメールの送信ステップを作成する":::

    >[!TIP]
    > 電子メールによる通知に含まれる GeoJSON 応答データ (`geometryId`、`deviceId` など) は、Event Grid から送信されたデータを読み取るよう Logic App を構成することで取得できます。 イベント データを取り出して電子メールによる通知に渡すようロジック アプリを構成する方法については、「[チュートリアル: Event Grid および Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)」を参照してください。

11. Logic Apps デザイナーの左上隅の **[保存]** をクリックします。

12. 手順 3. から手順 11. を繰り返して、建設現場から機材が出されたときに管理者に通知する 2 つ目のロジック アプリを作成します。 このロジック アプリには `Equipment-Exit` という名前を付けます。

## <a name="create-azure-maps-events-subscriptions"></a>Azure Maps イベントのサブスクリプションを作成する

Azure Maps では、3 種類のイベントがサポートされています。 Azure Maps でサポートされるイベントの種類は、[こちら](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)で確認できます。  ここでは 2 つのイベント サブスクリプションを作成する必要があります。1 つはジオフェンスの enter イベント用、もう 1 つはジオフェンスの exit イベント用です。

以下の手順に従って、ジオフェンスの enter イベント用にサブスクリプションを作成します。 ジオフェンスの exit イベントも同様の手順を繰り返してサブスクライブできます。

1. Azure Maps アカウントに移動します。 ダッシュボードで **[サブスクリプション]** を選択します。 使用するサブスクリプション名をクリックし、設定メニューから **[イベント]** を選択します。

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Azure Maps アカウントのイベントに移動する":::

2. イベント サブスクリプションを作成するには、[イベント] ページの **[+ イベント サブスクリプション]** を選択します。

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Azure Maps イベントのサブスクリプションの作成":::

3. **[イベント サブスクリプションの作成]** ページで、次の値を入力します。
    * イベント サブスクリプションの "*名前*"。
    * *[イベント スキーマ]* は *[イベント グリッド スキーマ]* にする必要があります。
    * このイベント サブスクリプションの "*システム トピック名*"。 この例では、`Contoso-Construction` を使用します。
    * *[イベントの種類のフィルター]* で、イベントの種類として `Geofence Entered` を選択します。
    * *[エンドポイントのタイプ]* で `Web Hook` を選択します。
    * *[エンドポイント]* には、前のセクションで作成したロジック アプリの Enter エンドポイントの HTTP POST URL をコピーします。 保存し忘れた場合は、ロジック アプリ デザイナーに戻って HTTP トリガーの手順からコピーしてください。

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Azure Maps イベントのサブスクリプションの詳細":::

4. **Create** をクリックしてください。

5. 前のセクションで作成したロジック アプリの Exit エンドポイントについても、手順 1. から手順 4. を繰り返します。 手順 3. で、イベントの種類として必ず `Geofence Exited` を選択してください。

## <a name="use-spatial-geofence-get-api"></a>Spatial Geofence Get API を使用する

今度は、[Spatial Geofence Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) を使用して、機材がジオフェンスを出入りしたときに電子メールによる通知を作業の管理者に送信します。

機材にはそれぞれ `deviceId` があります。 このチュートリアルでは、一意の ID が `device_1` である単一の機材を追跡します。

わかりやすくするために、時間の経過に伴う 5 つの機材位置を次の図で示しました。ジオフェンス外側のどこかにある *Start* 位置が起点となります。 *Start* 位置は定義していません。このチュートリアルの目的上、その位置のデバイスに対してクエリを実行することはないためです。

ジオフェンスの最初の出入りを示す機材位置を指定して [Spatial Geofence Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) に対するクエリを実行すると、Event Grid によって適切なロジック アプリ エンドポイントが呼び出されて、電子メールによる通知が作業の管理者に送信されます。

以降の各セクションでは、5 つの異なる機材位置座標を使用して HTTP GET Geofencing API 要求を実行します。

![Azure Maps のジオフェンス マップ](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>機材位置 1 (47.638237,-122.132483)

1. Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。  要求の **[要求名]** を入力します。 ここでは、*Location 1* という名前を使用します。 「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで作成したコレクションを選択し、 **[保存]** を選択します。

2. [builder]\(ビルダー\) タブで **GET** HTTP メソッドを選択し、次の URL を入力します。`{Azure-Maps-Primary-Subscription-key}` は実際のプライマリ サブスクリプション キーに、また `{udid}` は、「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで保存した `udid` に置き換えてください。

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. **[送信]** ボタンをクリックします。 応答ウィンドウに次の GeoJSON が表示されます。

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

4. 上の GeoJSON 応答では、メイン サイト ジオフェンスからの距離が負数になっていますが、これは機材がメイン ジオフェンス内にあることを意味します。 サブサイトのジオフェンスからの距離は正数になっていることから、サブサイトのジオフェンスの外にあることがわかります。 このデバイスがメイン サイト ジオフェンス内に位置するのはこれが初回であるため、`isEventPublished` パラメーターが `true` に設定され、機材がジオフェンスに入ったという通知が作業の管理者に電子メールで送信されます。

### <a name="location-2-4763800-122132531"></a>位置 2 (47.63800,-122.132531)

1. Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。  要求の **[要求名]** を入力します。 ここでは、*Location 2* という名前を使用します。 「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで作成したコレクションを選択し、 **[保存]** を選択します。

2. [builder]\(ビルダー\) タブで **GET** HTTP メソッドを選択し、次の URL を入力します。`{Azure-Maps-Primary-Subscription-key}` は実際のプライマリ サブスクリプション キーに、また `{udid}` は、「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで保存した `udid` に置き換えてください。

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. **[送信]** ボタンをクリックします。 応答ウィンドウに次の GeoJSON が表示されます。

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. 上の GeoJSON 応答では、機材はメイン サイト ジオフェンス内に留まっています。サブサイト ジオフェンスには入っていません。 結果的に `isEventPublished` パラメーターは `false` に設定され、作業の管理者には、電子メールによる通知は送信されません。

### <a name="location-3-4763810783315048-12213336020708084"></a>位置 3 (47.63810783315048,-122.13336020708084)

1. Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。  要求の **[要求名]** を入力します。 ここでは、*Location 3* という名前を使用します。 「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで作成したコレクションを選択し、 **[保存]** を選択します。

2. [builder]\(ビルダー\) タブで **GET** HTTP メソッドを選択し、次の URL を入力します。`{Azure-Maps-Primary-Subscription-key}` は実際のプライマリ サブスクリプション キーに、また `{udid}` は、「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで保存した `udid` に置き換えてください。

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. **[送信]** ボタンをクリックします。 応答ウィンドウに次の GeoJSON が表示されます。

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. 上の GeoJSON 応答では、機材はメイン サイト ジオフェンス内に留まっており、なおかつサブサイト ジオフェンスにも入っています。 結果的に `isEventPublished` パラメーターは `true` に設定され、作業の管理者には、機材がジオフェンスに入ったことを示す通知が電子メールで送信されます。

    >[!NOTE]
    >作業時間を過ぎてから機材がサブサイトに移動した場合、イベントは発行されず、作業の管理者にも通知は一切送信されません。  

### <a name="location-4-47637988-1221338344"></a>場所 4 (47.637988,-122.1338344)

1. Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。  要求の **[要求名]** を入力します。 ここでは、*Location 4* という名前を使用します。 「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで作成したコレクションを選択し、 **[保存]** を選択します。

2. [builder]\(ビルダー\) タブで **GET** HTTP メソッドを選択し、次の URL を入力します。`{Azure-Maps-Primary-Subscription-key}` は実際のプライマリ サブスクリプション キーに、また `{udid}` は、「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで保存した `udid` に置き換えてください。

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. **[送信]** ボタンをクリックします。 応答ウィンドウに次の GeoJSON が表示されます。

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. 上の GeoJSON 応答では、機材はメイン サイト ジオフェンス内に留まっていますが、サブサイト ジオフェンスからは出ています。 ただし、お気付きかもしれませんが、`userTime` の値は、ジオフェンス データに定義されている `expiredTime` を超えています。 結果的に `isEventPublished` パラメーターは `false` に設定され、作業の管理者には、電子メールによる通知は送信されません。

### <a name="location-547637988-1221338344"></a>位置 5(47.637988,-122.1338344)

1. Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。  要求の **[要求名]** を入力します。 ここでは、*Location 4* という名前を使用します。 「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで作成したコレクションを選択し、 **[保存]** を選択します。

2. [builder]\(ビルダー\) タブで **GET** HTTP メソッドを選択し、次の URL を入力します。`{Azure-Maps-Primary-Subscription-key}` は実際のプライマリ サブスクリプション キーに、また `{udid}` は、「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで保存した `udid` に置き換えてください。

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. **[送信]** ボタンをクリックします。 応答ウィンドウに次の GeoJSON が表示されます。

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. 上の GeoJSON 応答では、機材はメイン サイト ジオフェンスから出ています。 結果的に `isEventPublished` パラメーターは `true` に設定され、作業の管理者には、機材がジオフェンスから出たことを示す通知が電子メールで送信されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Logic Apps における各種コンテンツの扱い](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)

> [!div class="nextstepaction"]
> [Event Grid と Logic Apps を使用して電子メール通知を送信する](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)

> [!div class="nextstepaction"]
> [Event Grid でサポートされるイベント ハンドラー](https://docs.microsoft.com/azure/event-grid/event-handlers)
