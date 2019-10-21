---
title: Azure Maps を使用した IoT 空間分析の実装 | Microsoft Docs
description: IoT Hub を Azure Maps サービス API シリーズと統合します。
author: walsehgal
ms.author: v-musehg
ms.date: 08/13/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 618931c3a45fcb25b2a9221ea3f6069e9ff11de5
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933196"
---
# <a name="implement-iot-spatial-analytics-using-azure-maps"></a>Azure Maps を使用した IoT 空間分析の実装

空間と時間に生じる関連イベントを追跡およびキャプチャすることは、一般的な IoT シナリオです。 たとえば、フリート管理、資産の追跡、モビリティ、スマート シティ アプリケーションなどです。 このチュートリアルでは、Event Grid によって提供されるイベント サブスクリプション モデルを使用して、IoT Hub によってキャプチャされた関連イベントに対して Azure Maps API シリーズを使用するためのソリューション パターンについて説明します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * IoT Hub を作成します。
> * Data Upload API を使用して Azure Maps のデータ サービスにジオフェンス領域をアップロードする。
> * Azure Functions で関数を作成し、Azure Maps 空間分析に基づいてビジネス ロジックを実装します。
> * Event Grid 経由で Azure 関数から IoT デバイス テレメトリ イベントをサブスクライブします。
> * IoT Hub のメッセージ ルーティングを使用して、テレメトリ イベントをフィルター処理します。
> * 関連イベント データを格納するストレージ アカウントを作成します。
> * 車載 IoT デバイスをシミュレートします。
    

## <a name="use-case"></a>ユース ケース

ここでは、レンタカー会社が貸し出した車両に関するイベントの監視と記録を計画しているというシナリオについて、ソリューションを例示します。 多くの場合、レンタカー会社は、特定の地域向けに車両を貸し出し、貸し出し中に車両の行方を追跡する必要があります。 指定された地域を離れる車両を含むすべてのインスタンスをログに記録して、ポリシー、料金、およびその他のビジネスの側面を適切に処理できるようにする必要があります。

このユース ケースでは、レンタカーには、テレメトリ データを Azure IoT Hub に定期的に送信する IoT デバイスが装備されています。 テレメトリには現在の場所が含まれ、車両のエンジンが作動中であるかどうかが示されます。 デバイスの場所スキーマは、[地理空間データ用の IoT プラグ アンド プレイ スキーマ](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md)に準拠しています。 レンタカーのデバイス テレメトリ スキーマは次のようになります。

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": { 
            "location": { 
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            } 
        } 
    }
}
```

車載デバイスのテレメトリを使用して、目標を達成できます。 私たちの目標は、ジオフェンシング ルールを実行し、車両が移動したことを示すイベントが受信されるたびに適切にフォローアップすることです。 これを行うには、Event Grid 経由で IoT Hub からデバイス テレメトリ イベントをサブスクライブして、適切な場合にのみ目的の顧客ビジネス ロジックを実行できるようにします。 Event Grid をサブスクライブするにはいくつかの方法があります。このチュートリアルでは、Azure Functions を使用します。 Azure Functions は、Event Grid で発行されたイベントに反応し、Azure Maps 空間分析に基づいてレンタカー ビジネス ロジックを実装します。 Azure 関数では、車両がジオフェンスを離れたかどうかを確認し、離れた場合は現在の場所に関連付けられている住所などの追加情報を収集します。 また、この関数では、レンタカー アナリストやレンタル利用者にイベントの状況を正確に説明するのに役立つ、意味のあるイベント データをデータ BLOB ストレージに格納するロジックも実装します。

次の図は、システムの大まかな概要を示しています。

 
  <center>

  ![システムの概要](./media/tutorial-iot-hub-maps/system-diagram.png)</center>

下の図では、ジオフェンス エリアが青色で強調表示され、レンタル車両のルートが緑色の線で示されています。

  ![ジオフェンス ルート](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>前提条件 

### <a name="create-a-resource-group"></a>リソース グループの作成

このチュートリアルの手順を完了するには、最初に Azure portal でリソース グループを作成する必要があります。 リソース グループを作成するには、以下の手順に従います。

1. [Azure Portal](https://portal.azure.com) にログインします。

2. **[リソース グループ]** を選択します。
    
   ![リソース グループ](./media/tutorial-iot-hub-maps/resource-group.png)

3. [リソース グループ] から **[追加]** を選択します。
    
   ![リソース グループの追加](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. 次のプロパティ値を入力します。
    * **サブスクリプション:** 自分の Azure サブスクリプションを選択します。
    * **[リソース グループ]:** リソース グループ名として「ContosoRental」と入力します。
    * **[リージョン]:** リソース グループのリージョンを選択します。  

    ![リソース グループの詳細](./media/tutorial-iot-hub-maps/resource-details.png)

    **[確認と作成]** をクリックし、次のページで **[作成]** を選択します。

### <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する 

Azure Maps 空間分析に基づいてビジネス ロジックを実装するには、作成したリソース グループに Azure Maps アカウントを作成する必要があります。 [アカウントの管理](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account)に関するページの手順に従って、S1 価格レベルで Azure Maps アカウントのサブスクリプションを作成します。さらに、[主キーの取得](./tutorial-search-location.md#getkey)に関するページの手順に従って、お使いのアカウントのプライマリ サブスクリプション キーを取得します。


### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

イベント データをログに記録するには、"ContosoRental" リソース グループに、データを BLOB として格納するための汎用 **v2storage** アカウントを作成します。 ストレージ アカウントを作成するには、「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)」の手順に従います。 次に、BLOB を格納するコンテナーを作成する必要があります。 これを行うには、以下の手順に従います。

1. 自分のストレージ アカウントで、BLOB に移動します。

    ![BLOB](./media/tutorial-iot-hub-maps/blobs.png)

2. 左上にある [コンテナー] ボタンをクリックします。コンテナーに「contoso-rental-logs」という名前を付け、[OK] をクリックします。

    ![blob-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. 自分のストレージ アカウントの **[アクセス キー]** ブレードに移動して、アカウント名とアクセス キーをコピーします。これらは後で使用します。

    ![access-keys](./media/tutorial-iot-hub-maps/access-keys.png)


これで、ストレージ アカウントと、イベント データをログに記録するためのコンテナーが準備できました。次は IoT ハブを作成します。

### <a name="create-an-iot-hub"></a>IoT Hub の作成

IoT ハブは、クラウド内のマネージド サービスであり、IoT アプリケーションとそれによって管理されるデバイスとの間で双方向通信を行うための中央メッセージ ハブとして機能します。 デバイス テレメトリ メッセージをイベント グリッドにルーティングするために、"ContosoRental" リソース グループ内に IoT ハブを作成し、メッセージ ルート統合を設定します。ここでは、車両のエンジン状態に基づいてメッセージをフィルター処理し、車両が移動しているときは常にデバイス テレメトリ メッセージをイベント グリッドに送信するようにします。 

> [!Note] 
> Event Grid でデバイス テレメトリ イベントを発行する IoT Hub の機能は、パブリック プレビュー段階です。 パブリック プレビュー機能は、**米国東部、米国西部、西ヨーロッパ、Azure Government、Azure China 21Vianet**、および **Azure Germany** を除くすべてのリージョンで利用できます。 

[「IoT ハブの作成」セクション](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)の手順に従って、IoT ハブを作成します。


### <a name="register-a-device"></a>デバイスの登録 

IoT ハブに接続するには、デバイスを登録する必要があります。 IoT ハブにデバイスを登録するには、以下の手順に従います。

1. 自分の IoT ハブで、[IoT デバイス] ブレードをクリックし、[新規] をクリックします。

    ![add-device](./media/tutorial-iot-hub-maps/add-device.png)

2. [デバイスの作成] ページで、自分の IoT デバイスに名前を付けて、[保存] をクリックします。
    
    ![register-device](./media/tutorial-iot-hub-maps/register-device.png)


## <a name="upload-geofence"></a>ジオフェンスをアップロードする

[Postman アプリケーション](https://www.getpostman.com)を使用して、Azure Maps Data Upload API を使用して Azure Maps サービスに[ジオフェンスをアップロード](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)します。 車両がこのジオフェンスの外側にあるときのイベントは、すべてログに記録されます。

Azure Maps の Data Upload API を使用してジオフェンスをアップロードするには、Postman アプリを開いて以下の手順に従います。  

1. Postman アプリで、[new]\(新規\)、[Create new]\(新規作成\) の順にクリックし、[Request]\(\要求\) を選択します。 ジオフェンス データのアップロードに対する要求名を入力して、これを保存するコレクションまたはフォルダーを選択し、[Save]\(保存\) をクリックします。

    ![Postman を使用してジオフェンスをアップロードする](./media/tutorial-iot-hub-maps/postman-new.png)

2. [builder]\(ビルダー\) タブで POST HTTP メソッドを選択し、POST 要求を行うための次の URL を入力します。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL パス内の `dataFormat` パラメーターに対する "geojson" 値は、アップロードされるデータの形式を表します。

3. **[Params]\(パラメーター\)** をクリックして、POST 要求の URL に使用する次のキーと値のペアを入力します。 subscription-key の値は、実際の Azure Maps のプライマリ サブスクリプション キーに置き換えてください。
   
    ![Postman のキーと値のペアから成るパラメーター](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. **[Body]\(本文\)** をクリックしてから、 **[raw]\(未加工\)** 入力形式を選択し、ドロップダウン リストから **[JSON (application/text)]\(JSON (アプリケーション/テキスト)\)** を入力形式として選択します。 [こちら](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)の JSON データ ファイルを開き、アップロードすべきデータとしてこの JSON を Postman の本文セクションにコピーし、 **[Send]\(送信\)** をクリックします。
    
    ![データの投稿](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. 応答ヘッダーを確認します。 要求が成功すると、**Location** ヘッダーには、アップロード要求の現在の状態を確認するための状態 URI が格納されます。 状態 URI は次の形式になります。 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. 自分の状態 URI をコピーし、自分の Azure Maps アカウントのサブスクリプション キーを値として備える `subscription-key` パラメーターを追加します。 状態 URI の形式は次のようになります。

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. `udId` を取得するには、Postman アプリで新しいタブを開き、[builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、状態 URI で GET 要求を行います。 データのアップロードが成功した場合は、応答本文で udId が返されます。 後で使用するために udId をコピーします。

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


次に、"ContosoRental" リソース グループ内に Azure 関数を作成し、デバイス テレメトリ メッセージをフィルター処理するためのメッセージ ルートを IoT Hub で設定します。


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Azure 関数を作成して Event Grid サブスクリプションを追加する

Azure Functions は、コンピューティング インフラストラクチャを明示的にプロビジョニングまたは管理することなく、オンデマンドでコードを実行できるサーバーレス コンピューティング サービスです。 Azure Functions の詳細については、[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) のドキュメントを参照してください。 関数で実装するロジックでは、車載デバイスのテレメトリから取得した位置情報データを使用して、ジオフェンスの状態を評価します。 指定の車両がジオフェンスの外に出た場合、この関数では、指定の位置座標を人間が理解できる住所に変換する [Get Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) を介して、場所の住所などの追加情報を収集します。 すべての関連イベント情報は、BLOB ストアに格納されます。 下の手順 5 は、そのようなロジックを実装する実行可能コードを示しています。 以下の手順に従って、ストレージ アカウントの BLOB コンテナーにデータ ログを送信する Azure 関数を作成し、それに Event Grid サブスクリプションを追加します。

1. Azure portal のダッシュボードで、[リソースの作成] を選択します。 使用可能なリソースの種類の一覧から **[Compute]** を選択し、 **[Function App]** を選択します。

    ![create-resource](./media/tutorial-iot-hub-maps/create-resource.png)

2. 関数アプリの作成ページで、関数アプリに名前を付け、 **[リソース グループ]** で **[既存のものを使用する]** を選択し、ドロップダウン リストから "ContosoRental" を選択します。 ランタイム スタックとして [.NET Core] を選択します。 **[ストレージ]** で **[既存のものを使用する]** を選択します。ドロップダウンから "contosorentaldata" を選択し、 **[作成]** をクリックします。
    
    ![create-app](./media/tutorial-iot-hub-maps/rental-app.png)

3. アプリが作成されたら、それに関数を追加する必要があります。 関数アプリに移動し、 **[新しい関数]** をクリックして関数を追加します。開発環境として **[ポータル内]** を選択し、 **[続行]** を選択します。

    ![create-function](./media/tutorial-iot-hub-maps/function.png)

4. **[その他のテンプレート]** を選択し、 **[テンプレートの完了と表示]** をクリックします。 

5. **Azure Event Grid トリガー**が含まれているテンプレートを選択します。 プロンプトが表示されたら拡張機能をインストールし、関数に名前を付け、 **[作成]** を押します。

    ![function-template](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. [C# コード](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)を自分の関数にコピーし、 **[保存]** をクリックします。
 
7. C# スクリプト内で、次のパラメーターを置き換えます。
    * **SUBSCRIPTION_KEY** を、自分の Azure Maps アカウントのプライマリ サブスクリプション キーに置き換えます。
    * **UDID** を、自分がアップロードしたジオフェンスの udId に置き換えます。 
    * スクリプト内の **CreateBlobAsync** 関数では、データ ストレージ アカウントでイベントごとに BLOB を作成します。 **ACCESS_KEY**、**ACCOUNT_NAME**、および **STORAGE_CONTAINER_NAME** を、自分のストレージ アカウントのアクセス キー、アカウント名、およびデータ ストレージ コンテナーに置き換えます。

10. **[Event Grid サブスクリプションの追加]** をクリックします。
    
    ![add-event-grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. サブスクリプションの詳細を入力します。 **[イベント サブスクリプションの詳細]** で自分のサブスクリプションに名前を付け、[イベント スキーマ] で [イベント グリッド スキーマ] を選択します。 **[トピックの詳細]** で、[トピックの種類] として [Azure IoT Hub Accounts]\(Azure IoT Hub アカウント\) を選択し、リソース グループの作成に使用したのと同じサブスクリプションを選択します。[リソース グループ] として "ContosoRental" を選択し、[リソース] として、作成した IoT ハブを選択します。 [イベントの種類] として **[Device Telemetry]\(デバイス テレメトリ\)** を選択します。 これらのオプションを選択すると、[トピックの種類] が [IoT Hub] に自動的に変更されます。

    ![event-grid-subscription](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>IoT Hub のメッセージ ルーティングを使用してイベントをフィルター処理する

Event Grid サブスクリプションを Azure 関数に追加すると、IoT Hub の **[メッセージ ルーティング]** ブレードに Event Grid への既定のメッセージ ルートを表示できるようになります。 メッセージ ルーティングを使用すると、種類の異なるデータ (デバイス テレメトリ メッセージ、デバイス ライフサイクル イベント、デバイス ツイン変更イベントなど) を、さまざまなエンドポイントにルーティングできます。 IoT ハブのメッセージ ルーティングの詳細については、[IoT Hub メッセージ ルーティングの使用](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)に関するページを参照してください。

![hub-EG-route](./media/tutorial-iot-hub-maps/hub-route.png)

この例のシナリオでは、レンタル車両が移動している場合のすべてのメッセージをフィルターで除外します。 このようなデバイス テレメトリ イベントを Event Grid に発行するために、ルーティング クエリを使用して、`Engine` プロパティが **"ON"** であるイベントをフィルター処理します。 デバイスからクラウドへの IoT メッセージに対してクエリを実行するには、さまざまな方法があります。メッセージ ルーティング構文の詳細については、[IoT Hub メッセージ ルーティング](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)に関するページを参照してください。 ルーティング クエリを作成するには、**RouteToEventGrid** ルートをクリックし、**ルーティング クエリ**を **"Engine='ON'"** に置き換えて、 **[保存]** をクリックします。 これで、IoT ハブにより、エンジンがオンになっているデバイス テレメトリのみが発行されます。

![hub-EG-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>テレメトリ データを IoT Hub に送信する

Azure 関数が起動したら、テレメトリ データを IoT Hub に送信します。それにより、テレメトリ データは Event Grid にルーティングされます。 C# アプリケーションを使用して、レンタカーの車載デバイスの位置情報データをシミュレートします。 アプリケーションを実行するには、自分の開発用マシン上に .NET Core SDK 2.1.0 以上が必要です。 以下の手順に従って、シミュレートされたテレメトリ データを IoT Hub に送信します。

1. [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# プロジェクトをダウンロードします。 

2. 任意のテキスト エディターで simulatedCar.cs ファイルを開き、`connectionString` の値を、デバイスを登録したときに保存したものに置き換えて、ファイルへの変更を保存します。
 
3. ローカル ターミナル ウィンドウで、C# プロジェクトのルート フォルダーに移動し、次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なパッケージをインストールします。
    
    ```cmd/sh
    dotnet restore
    ```

4. 同じターミナルで次のコマンドを実行して、レンタカー シミュレーション アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```

  ローカル ターミナルは次のようになります。

  ![ターミナルの出力](./media/tutorial-iot-hub-maps/terminal.png)

ここで BLOB ストレージ コンテナーを開くと、車両がジオフェンスの外側にあった場所の 4 つの BLOB を確認できます。

![BLOB の入力](./media/tutorial-iot-hub-maps/blob.png)

下のマップは、車両がジオフェンスの外側にあり、定期的にログに記録された 4 つの地点を示しています。

![違反マップ](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで使用した Azure Maps API シリーズの詳細については、以下を参照してください。

* [Get Search Address Reverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Get Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Azure Maps REST API シリーズの完全な一覧については、次を参照してください。

* [Azure Maps REST API シリーズ](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

IoT プラグ アンド プレイの詳細については、次を参照してください。

* [IoT プラグ アンド プレイ](https://docs.microsoft.com/azure/iot-pnp)

Azure で IoT の認定を受けたデバイスの一覧を取得するには、次のページにアクセスします。

* [Azure 認定デバイス](https://catalog.azureiotsolutions.com/)

デバイスからクラウドへのテレメトリを送信する方法、およびその逆の方法の詳細については、次を参照してください。

* [デバイスから利用統計情報を送信する](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
