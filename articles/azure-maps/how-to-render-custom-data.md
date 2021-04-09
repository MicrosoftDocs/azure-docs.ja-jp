---
title: ラスター マップ上にカスタム データをレンダリングする | Microsoft Azure Maps
description: ラスター マップにプッシュピン、ラベル、幾何学図形を追加する方法について説明します。 この目的のために Azure Maps で静的画像サービスを使用する方法を確認してください。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5c70835c11bafb3fd06645ba51099b33d1eb6149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906082"
---
# <a name="render-custom-data-on-a-raster-map"></a>ラスター マップ上にカスタム データをレンダリングする

この記事では、[静的画像サービス](/rest/api/maps/render/getmapimage)と画像コンポジション機能を使用して、ラスター マップの上にオーバーレイを作成する方法について説明します。 画像コンポジションには、カスタムのプッシュピン、ラベル、ジオメトリのオーバーレイなどのデータが追加されたラスター タイルを戻す機能があります。

カスタムのプッシュピン、ラベル、ジオメトリをレンダリングするには、Postman アプリケーションを使用できます。 オーバーレイの格納とレンダリングには、Azure Maps [Data Service API](/rest/api/maps/data) を使用できます。

> [!Tip]
> 多くの場合、静的イメージ サービスを使用するよりも、Azure Maps Web SDK を使用して Web ページ上に単純なマップを表示した方が、ずっとコスト効率に優れています。 Web SDK では、マップタイルを使用します。ユーザーがマップをパンしたりズームしたりしない限り、多くの場合、マップの読み込みごとにトランザクションのごく一部のみが生成されます。 Azure Maps Web SDK には、パンとズームを無効にするオプションがあることに注意してください。 さらに、Azure Maps Web SDK には、静的なマップの Web サービスよりも豊富なデータ視覚化オプションが用意されています。  

## <a name="prerequisites"></a>前提条件

### <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する

この記事の手順を完了するには、まず Azure Maps アカウントを作成し、マップのアカウント キーを取得する必要があります。 [アカウントの作成](quick-demo-map-app.md#create-an-azure-maps-account)に関するページの手順に従って、Azure Maps アカウントのサブスクリプションを作成します。さらに、[主キーの取得](quick-demo-map-app.md#get-the-primary-key-for-your-account)に関するページの手順に従って、お使いのアカウントの主キーを取得します。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」を参照してください。


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>ラベルとカスタム画像を使ってプッシュピンをレンダリングする

> [!Note]
> このセクションの手順には、S0 または S1 価格レベルの Azure Maps アカウントが必要です。

Azure Maps アカウント S0 レベルでは、`pins` パラメーターのインスタンスが 1 つだけサポートされます。 カスタム画像を利用し、URL 要求で指定されている最大 5 つのプッシュピンをレンダリングできます。

ラベルとカスタム画像を使ってプッシュピンをレンダリングするには、次の手順を実行します。

1. 要求を格納するコレクションを作成します。 Postman アプリ内で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[コレクション]** を選択します。 コレクションに名前を付け、 **[作成]** ボタンを選択します。 

2. 要求を作成するには、 **[新規]** をもう一度選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 プッシュピンの **[要求名]** を入力します。 前の手順で要求の保存場所として作成したコレクションを選択します。 次に、 **[保存]** を選択します。
    
    ![Postman での要求の作成](./media/how-to-render-custom-data/postman-new.png)

3. [builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、次の URL を入力して GET 要求を作成します。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    結果的に生成される画像:

    ![ラベル付きのカスタム プッシュピン](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Azure Maps データ ストレージからデータを取得する

> [!Note]
> このセクションの手順には、S1 価格レベルの Azure Maps アカウントが必要です。

[Data Upload API](/rest/api/maps/data/uploadpreview) を使用し、パスとピンの場所情報を取得することもできます。 パスとピンのデータをアップロードするには、次の手順を実行します。

1. Postman アプリで、前のセクションで作成したコレクションの新しいタブを開きます。 [builder]\(ビルダー\) タブで POST HTTP メソッドを選択し、次の URL を入力して POST 要求を行います。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. **[パラメーター]** タブに、POST 要求の URL に使用する次のキーと値のペアを入力します。 `subscription-key` 値を自分の Azure Maps サブスクリプション キーに変更します。
    
    ![Postman のキーと値のペアから成るパラメーター](./media/how-to-render-custom-data/postman-key-vals.png)

3. **[本文]** タブで未加工入力形式を選択し、入力形式として JSON をドロップダウン リストから選択します。 アップロードするデータとしてこの JSON を入力します。
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. **[送信]** を選択し、応答ヘッダーを確認します。 要求が成功すると、Location ヘッダーには、アップロード要求の現在の状態を確認するための状態 URI が格納されます。 状態 URI は次の形式になります。  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. 自分の状態 URI をコピーし、Azure Maps アカウントのサブスクリプション キーの値を使用してサブスクリプション キー パラメーターを追加します。 データのアップロードに使用したものと同じアカウント サブスクリプション キーを使用します。 状態 URI の形式は次のようになります。

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. udId を取得するには、Postman アプリで新しいタブを開きます。 [builder]\(ビルダー\) タブで GET HTTP メソッドを選択します。状態 URI で GET 要求を行います。 データのアップロードが成功した場合は、応答本文で udId が返されます。 udId をコピーします。

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. マップ上にフィーチャーをレンダリングするには、Data Upload API から受け取った `udId` 値を使用します。 これを行うには、前のセクションで作成したコレクションの新しいタブを開きます。 [ビルダー] タブで GET HTTP メソッドを選択し、{subscription-key} と {udId} を実際の値に置き換えて、次の URL を入力して GET 要求を行います。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    応答イメージを次に示します。

    ![Azure Maps データ ストレージからデータを取得する](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>色と不透明度が指定された多角形をレンダリングする

> [!Note]
> このセクションの手順には、S1 価格レベルの Azure Maps アカウントが必要です。


多角形の外観は、[path パラメーター](/rest/api/maps/render/getmapimage#uri-parameters)のスタイル修飾子を使用して変更できます。

1. Postman アプリで、先ほど作成したコレクションの新しいタブを開きます。 [builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、GET 要求を構成する次の URL を入力し、色と不透明度を指定した多角形をレンダリングします。
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    応答イメージを次に示します。

    ![不透明な多角形をレンダリングする](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>カスタム ラベルが付いた円とプッシュピンをレンダリングする

> [!Note]
> このセクションの手順には、S1 価格レベルの Azure Maps アカウントが必要です。


ピンの外観を変更するには、スタイル修飾子を追加します。 たとえば、プッシュピンとそのラベルを大きくまたは小さくするには、`sc` "スケール スタイル" 修飾子を使用します。 この修飾子は 0 より大きい値を受け取ります。 1 の値が標準のスケールです。 1 より大きい値にするとピンが大きくなり、1 より小さい値にすると小さくなります。 スタイル修飾子の詳細については、[静的画像サービス パスのパラメーター](/rest/api/maps/render/getmapimage#uri-parameters)に関するページを参照してください。


次の手順でカスタム ラベルが付いた円とプッシュピンをレンダリングします。

1. Postman アプリで、先ほど作成したコレクションの新しいタブを開きます。 [builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、この URL を入力して GET 要求を行います。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    応答イメージを次に示します。

    ![カスタム プッシュピンが付いた円をレンダリングする](./media/how-to-render-custom-data/circle-custom-pins.png)

2. 前の手順のプッシュピンの色を変更するには、"co" スタイル修飾子を変更します。 `pins=default|la15+50|al0.66|lc003C62|co002D62|` を確認すると、現在の色が CSS で #002D62 として指定されています。 たとえば、#41d42a に変更するとします。 次のように、"co" 指定子の後に新しい色の値を書き込みます (例: `pins=default|la15+50|al0.66|lc003C62|co41D42A|`)。 新しい GET 要求を作成します。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    ピンの色を変更した後の応答の画像を次に示します。

    ![更新されたプッシュピンで円をレンダリングする](./media/how-to-render-custom-data/circle-updated-pins.png)

同様に、他のスタイル修飾子を変更、追加、および削除することもできます。

## <a name="next-steps"></a>次の手順


* [Azure Maps Get Map Image API](/rest/api/maps/render/getmapimage) のドキュメントを確認します。
* Azure Maps Data Service (プレビュー) の詳細については、[サービス ドキュメント](/rest/api/maps/data)を参照してください。