---
title: チュートリアル:Microsoft Azure Maps Creator (プレビュー) を使用して屋内マップを作成する
description: Microsoft Azure Maps Creator (プレビュー) を使用して屋内マップを作成する方法に関するチュートリアル
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9ac53dab29feddd36a95b8b2b041caaf5c3598d5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720139"
---
# <a name="tutorial-use-creator-preview-to-create-indoor-maps"></a>チュートリアル:Creator (プレビュー) を使用して屋内マップを作成する

> [!IMPORTANT]
> Azure Maps Creator サービスは、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このチュートリアルでは、屋内マップを作成する方法を示します。 このチュートリアルでは、API を使用して以下を行う方法を学習します。

> [!div class="checklist"]
> * 屋内マップの Drawing パッケージをアップロードする
> * Drawing パッケージをマップ データに変換する
> * マップ データからデータセットを作成する
> * データセット内のデータからのタイルセットを作成する
> * Azure Maps Web Feature Service (WFS) API のクエリを実行して、マップ地物について学習する
> * マップ地物とデータセット内のデータを使用して地物状態セットを作成する
> * 地物状態セットを更新する

## <a name="prerequisites"></a>前提条件

屋内マップを作成するには:

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。
3. [Creator (プレビュー) リソースを作成します](how-to-manage-creator.md)
4. [サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip)をダウンロードします。

このチュートリアルでは [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境を選択することもできます。

>[!IMPORTANT]
> このドキュメントの API URL は、場合によっては、Creator リソースの場所に合わせて調整する必要があります。 「[Creator サービスにアクセスする](how-to-manage-creator.md#access-to-creator-services)」を参照してください。

## <a name="upload-a-drawing-package"></a>Drawing パッケージをアップロードする

Drawing パッケージを Azure Maps リソースにアップロードするには、[Data Upload API](/rest/api/maps/data/uploadpreview) を使用します。

Data Upload API は、ここで定義されたパターンを実装する長時間トランザクションです。 操作が完了したら、`udid` を使用してアップロードされたパッケージにアクセスし、変換します。 次の手順で `udid` を取得します。

1. Postman アプリを開きます。 Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[コレクション]** を選択します。  コレクションに名前を付け、 **[作成]** ボタンを選択します。

2. 要求を作成するには、 **[新規]** をもう一度選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 要求の **[要求名]** を入力します。 前の手順で作成したコレクションを選択し、 **[Save]\(保存\)** を選択します。

3. ビルダー タブで **POST** HTTP メソッドを選択し、次の URL を入力して、Drawing パッケージを Azure Maps サービスにアップロードします。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. **[Headers]\(ヘッダー\)** タブで、`Content-Type` キーの値を指定します。 Drawing パッケージは zip 形式圧縮されたフォルダーなので、`application/octet-stream` 値を使用します。 **[Body]\(本文\)** タブで、 **[binary]\(バイナリ\)** を選択します。 **[Select File]\(ファイルの選択\)** をクリックし、Drawing パッケージを選択します。

     ![データ管理](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. 青色の **[Send]\(送信\)** ボタンをクリックし、要求が処理されるまで待ちます。 要求が完了したら、応答の **[Headers]\(ヘッダー\)** タブに移動します。 **Location** キーの値である `status URL` をコピーします。

6. API 呼び出しの状態を確認するには、`status URL` に対して **GET** HTTP 要求を作成します。 認証のために、プライマリ サブスクリプション キーを URL に追加する必要があります。 **GET** 要求は次の URL のようになります。

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. **GET** HTTP 要求が正常に完了すると、`resourceLocation` が返されます。 `resourceLocation` には、アップロードされたコンテンツの一意の `udid` が格納されます。 次の手順では任意で、`resourceLocation` URL を使用してこのリソースからメタデータを取得できます。

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
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Drawing パッケージを変換する

 Drawing パッケージがアップロードされたので、アップロードされたパッケージに `udid` を使用して、パッケージをマップ データに変換します。 Conversion API には、[ここ](creator-long-running-operation.md)で定義されたパターンを実装する長時間トランザクションが使用されます。 操作が完了したら、`conversionId` を使用して、変換されたデータにアクセスします。 次の手順で `conversionId` を取得します。

1. **[新規]** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 **[Request name]\(要求名\)** を入力し、コレクションを選択します。 **[保存]** をクリックします。

2. ビルダー タブで **POST** HTTP メソッドを選択し、次の URL を入力して、アップロードした Drawing パッケージをマップ データに変換します。 アップロードしたパッケージには `udid` を使用します。

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > このドキュメントの API URL は、場合によっては、Creator リソースの場所に合わせて調整する必要があります。 詳細については、[Creator サービス (プレビュー) へのアクセス](how-to-manage-creator.md#access-to-creator-services)に関するセクションを参照してください。
    > `"RequiresCreatorResource"` というコードを含むエラーが発生した場合は、自分の Azure Maps アカウントで [Azure Maps Creator リソースをプロビジョニング](how-to-manage-creator.md)していることを確認してください。

3. **[Send]\(送信\)** ボタンをクリックし、要求が処理されるまで待ちます。 要求が完了したら、応答の **[Headers]\(ヘッダー\)** タブに移動し、**Location** キーを探します。 **Location** キーの値をコピーします。これは、変換要求の `status URL` です。 これは次の手順で使用します。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="Location キーの値をコピーする":::

4. ビルダー タブで新しい **GET** HTTP メソッドを開始します。Azure Maps のプライマリ サブスクリプション キーを `status URL` に追加します。 手順 3 でコピーした `status URL` で **GET** 要求を行います。 `status URL` は次の URL のようになります。

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    変換プロセスがまだ完了していない場合、次の JSON 応答のようなものが表示されることがあります。

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. 要求が正常に完了すると、応答本文に成功状態メッセージが表示されます。  変換されたパッケージの `resourceLocation` URL から `conversionId` をコピーします。 `conversionId` は、他の API から変換されたマップ データにアクセスするために使用されます。

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>Postman アプリケーションは、HTTP 長時間実行要求をネイティブでサポートしていません。 その結果、状態 URL で **GET** 要求を実行すると、長い遅延が発生する場合があります。  30 秒ほど待ってから、応答で成功または失敗が示されるまで、 **[Send]\(送信\)** ボタンをもう一度クリックします。

サンプル Drawing パッケージは、エラーや警告なしに変換されます。 ただし、独自の Drawing パッケージからエラーまたは警告を受け取った場合、JSON 応答には [Drawing エラー ビジュアライザー](drawing-error-visualizer.md)へのリンクが示されます。 Drawing Error ビジュアライザーを使用すると、エラーと警告の詳細を調べることができます。 変換エラーと警告の解決方法の推奨事項を受け取る方法については、「[Drawing Conversion のエラーと警告](drawing-conversion-error-codes.md)」を参照してください。

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>データセットを作成する

データセットは、建物、階層、部屋などのマップ地物のコレクションです。 データセットを作成するには、[Dataset Create API](/rest/api/maps/dataset/createpreview) を使用します。 Dataset Create API は、変換された Drawing パッケージの `conversionId` を受け取り、作成されたデータセットの `datasetId` を返します。 次の手順は、データセットを作成する方法を示します。

1. Postman アプリケーションで、 **[New]\(新規\)** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 **[Request name]\(要求名\)** を入力し、コレクションを選択します。 **[保存]**

2. [Dataset Create API](/rest/api/maps/dataset/createpreview) に **POST** 要求を行い、新しいデータセットを作成します。 要求を送信する前に、サブスクリプション キーと `conversionId` の両方に、手順 5 の変換プロセスで取得した `conversionId` を追加します。  要求は次の URL のようになります。

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 応答の **Headers** の **Location** キーで `statusURL` を取得します。

4. `statusURL` で **GET** 要求を行い、`datasetId` を取得します。 認証のために Azure Maps のプライマリ サブスクリプション キーを追加します。 要求は次の URL のようになります。

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. **GET** HTTP 要求が正常に完了すると、作成されたデータセットの `datasetId` が応答ヘッダーに含まれます。 `datasetId` をコピーします。 タイルセットを作成するには、`datasetId` を使用する必要があります。

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>タイルセットを作成する

タイルセットは、マップ上にレンダリングされる一連のベクター タイルです。 タイルセットは既存のデータセットから作成されます。 ただし、タイルセットは、そのソースとなったデータセットから独立しています。 データセットが削除された後も、タイルセットは存在します。 タイルセットを作成するには、次の手順を実行します。

1. Postman アプリケーションで、 **[New]\(新規\)** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 **[Request name]\(要求名\)** を入力し、コレクションを選択します。 **[保存]**

2. ビルダー タブで **POST** 要求を行います。要求 URL は次の URL のようになります。

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. タイルセットの `statusURL` で **GET** 要求を行います。 認証のために Azure Maps のプライマリ サブスクリプション キーを追加します。 要求は次の URL のようになります。

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. **GET** HTTP 要求が正常に完了すると、作成されたタイルセットの `tilesetId` が応答ヘッダーに含まれます。 `tilesetId` をコピーします。

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>WFS API を使用してデータセットのクエリを実行する

 データセットには、[WFS API](/rest/api/maps/wfs) を使用してクエリを実行できます。 WFS API を使用すると、地物コレクション、特定のコレクション、または地物 **ID** を指定して特定の地物のクエリを実行できます。 地物 **ID** を使用すると、データセット内の地物が一意に識別されます。 たとえば、特定の状態セットで更新する必要がある地物状態を識別するために使用されます。

1. Postman アプリケーションで、 **[New]\(新規\)** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 **[Request name]\(要求名\)** を入力し、コレクションを選択します。 **[保存]**

2. **GET** 要求を行い、データセット内のコレクションの一覧を表示します。 `<dataset-id>` は実際の `datasetId` に置き換えます。 プレースホルダーの代わりに Azure Maps の主キーを使用します。 要求は次の URL のようになります。

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. 応答本文は GeoJSON 形式で配信され、データセット内のすべてのコレクションが含まれます。 わかりやすくするために、この例では `unit` コレクションのみを示しています。 すべてのコレクションを含む例を参照するには、[WFS Describe Collections API](/rest/api/maps/wfs/collectiondescriptionpreview) に関するページを参照してください。 コレクションの詳細については、`link` 要素内の任意の URL をクリックしてください。

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. `unit` 地物コレクションに対して **GET** 要求を行います。  `{datasetId}` は実際の `datasetId` に置き換えます。 プレースホルダーの代わりに Azure Maps の主キーを使用します。 応答本文には、`unit` コレクションのすべての地物が含まれています。 要求は次の URL のようになります。

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. 動的に変更できるスタイルのプロパティを持つユニット地物の地物 `id` をコピーします。  ユニットの占有状態と温度は動的に更新できるため、次のセクションでこの地物 `id` を使用します。 次の例では、地物 `id` は "UNIT26" です。 この地物のスタイルのプロパティを状態と呼び、この地物を使用して状態セットを作成します。

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>地物状態セットを作成する

1. Postman アプリケーションで、 **[New]\(新規\)** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 **[Request name]\(要求名\)** を入力し、コレクションを選択します。 **[保存]**

2. [Create Stateset API](/rest/api/maps/featurestate/createstatesetpreview) に対して **POST** 要求を行います。 変更する状態を含むデータセットの `datasetId` を使用します。 要求は次の URL のようになります。

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. **POST** 要求の **[Headers]\(ヘッダー\)** で、`Content-Type` を `application/json` に設定します。 **[Body]\(本文\)** で、次の未加工の json スタイルを指定して `occupied` および `temperature` "*状態*" への変更を反映します。 設定が完了したら、 **[Send]\(送信\)** をクリックします。

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. 応答本文から `statesetId` をコピーします。

5. 状態を更新する **POST** 要求を作成します。Azure Maps サブスクリプション キーを使用して statesetId と地物 `ID` を渡します。 要求は次の URL のようになります。

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **POST** 要求の **[Headers]\(ヘッダー\)** で、`Content-Type` を `application/json` に設定します。 **POST** 要求の **[BODY]\(本文\)** で、次のサンプルの JSON をコピーして貼り付けます。

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > この更新は、POST されたタイム スタンプが前の要求のタイム スタンプより後の場合にのみ保存されます。 以前に構成した任意のキー名を作成時に渡すことができます。

7. 正常に更新されると、`200 OK` HTTP 状態コードを受け取ります。 屋内マップに[動的スタイル設定を実装](indoor-map-dynamic-styling.md)している場合、指定されたタイム スタンプでレンダリングされたマップに更新が表示されます。

[Feature Get States API](/rest/api/maps/featurestate/getstatespreview) を使用すると、地物 `ID` を使用して地物状態を取得できます。 [Feature State Delete API](/rest/api/maps/featurestate/deletestatesetpreview) を使用して、状態セットとそのリソースを削除することもできます。

この記事で説明するさまざまな Azure Maps Creator サービス (プレビュー) の詳細については、[屋内マップ用の Creator](creator-indoor-maps.md) に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップが必要なリソースはありません。

## <a name="next-steps"></a>次の手順

屋内マップ モジュールの使用方法については、次を参照してください。

> [!div class="nextstepaction"]
> [Indoor Maps モジュールを使用する](how-to-use-indoor-module.md)