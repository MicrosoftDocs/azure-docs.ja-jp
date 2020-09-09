---
title: チュートリアル:Microsoft Azure Maps を使用して IoT 空間分析を実装する
description: IoT ハブを Microsoft Azure Maps サービスの API と統合します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4150464b5c59b631afea0c788b1e351dee5185f9
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299416"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>チュートリアル:Azure Maps を使用した IoT 空間分析の実装

IoT シナリオでは、空間と時間に生じる関連イベントをキャプチャして追跡するのが一般的です。 たとえば、フリート管理、資産の追跡、モビリティ、スマート シティ アプリケーションのシナリオです。 このチュートリアルでは、Azure Maps API を使用して、使用されたレンタカーの移動を追跡するソリューションについて説明します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * 車の追跡データをログする Azure Storage アカウントを作成します。
> * Data Upload API を使用して Azure Maps Data Service にジオフェンスをアップロードします。
> * IoT Hub を作成してデバイスを登録します。
> * Azure Functions で関数を作成し、Azure Maps 空間分析に基づいてビジネス ロジックを実装します。
> * Event Grid 経由で Azure 関数から IoT デバイス テレメトリ イベントをサブスクライブします。
> * IoT Hub のメッセージ ルーティングを使用して、テレメトリ イベントをフィルター処理します。

## <a name="prerequisites"></a>前提条件

1. [Azure portal](https://portal.azure.com) にサインインします。

2. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)。

3. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。

4. [リソース グループを作成します](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups)。 このチュートリアルでは、リソース グループに *ContosoRental* という名前を付けますが、好きな名前を選択できます。

5. [C# プロジェクト rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) をダウンロードします。

このチュートリアルでは [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境を選択することもできます。

## <a name="use-case-rental-car-tracking"></a>ユース ケース: レンタカーの追跡

このチュートリアルでは、次のシナリオについて説明します。レンタカー会社は、レンタカーの位置情報、移動距離、運転状態を記録する必要があります。 また、この会社は、車が承認された適切な地理的領域から離れる場合は常にこの情報を保存したいと考えています。

このユース ケースでは、レンタカーには、利用統計情報を Azure IoT Hub に定期的に送信する IoT デバイスが搭載されています。 テレメトリには現在の場所が含まれ、車両のエンジンが作動中であるかどうかが示されます。 デバイスの場所スキーマは、[地理空間データ用の IoT プラグ アンド プレイ スキーマ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md)に準拠しています。 レンタカーのデバイス テレメトリ スキーマは、次の JSON コードのようになります。

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

このチュートリアルでは、1 台の車のみを追跡します。 Azure サービスを設定した後、車のシミュレーターを実行するには、[C# プロジェクト rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) をダウンロードする必要があります。 イベントから関数の実行までのプロセス全体が次の手順に要約されています。

1. 車載デバイスにより、テレメトリ データが IoT ハブに送信されます。

2. 車のエンジンが動作している場合、IoT ハブはテレメトリ データを Event Grid に発行します。

3. Azure 関数が、デバイス テレメトリ イベントに対するイベント サブスクリプションによってトリガーされます。

4. この関数は、車のデバイスの位置座標、イベント時間、デバイス ID をログします。 次に、[Spatial Geofence Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) を使用して、車がジオフェンスの外側に移動したかどうかを判断します。 ジオフェンス境界の外側で走行した場合、この関数により、イベントから受信した位置データが BLOB コンテナーに格納されます。 また、関数によって[住所の逆引き検索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)に対してクエリが実行されて、座標位置が住所に変換され、残りのデバイス位置データと共に格納されます。

次の図は、システムの大まかな概要を示しています。

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="システムの概要":::

次の図では、ジオフェンス領域が青で強調表示されています。 レンタカーのルートは、緑色の線で示されています。

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="ジオフェンス ルート":::

## <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

車の違反の追跡データを格納するため、リソース グループに[汎用 v2 ストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts)を作成します。 リソース グループを作成していない場合は、[リソース グループの作成](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups)に関するセクションの指示に従ってください。 このチュートリアルでは、リソース グループに *ContosoRental* という名前を付けます。

ストレージ アカウントを作成するには、「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)」の手順に従います。 このチュートリアルでは、ストレージ アカウントの名前を *contosorentalstorage* としていますが、好きな名前を付けることができます。

ストレージ アカウントが正常に作成されたら、ログ データを格納するコンテナーを作成する必要があります。

1. 新しく作成したストレージ アカウントに移動します。 [Essentials]\(要点\) セクションの **[コンテナー]** リンクをクリックします。

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="BLOB ストレージのコンテナー":::

2. 左上隅にある **[+ コンテナー]** ボタンをクリックします。 ブラウザーの右側にパネルが表示されます。 コンテナーに *contoso-rental-logs* という名前を付けて、 **[作成]** をクリックします。

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="BLOB コンテナーを作成する":::

3. 自分のストレージ アカウントの **[アクセス キー]** ブレードに移動して、**ストレージ アカウント名**と **[key1]** セクションの **[キー]** の値をコピーします。 「[Azure 関数を作成して Event Grid サブスクリプションを追加する](#create-an-azure-function-and-add-an-event-grid-subscription)」セクションで、両方の値が必要になります。

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="ストレージ アカウント名とキーをコピーする":::

## <a name="upload-a-geofence"></a>ジオフェンスをアップロードする

ここでは、[Postman アプリ](https://www.getpostman.com)を使用して、Azure Maps サービスに[ジオフェンスをアップロードします](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)。 ジオフェンスにより、レンタカーの承認された地理的領域が定義されます。  Azure 関数のジオフェンスを使用して、車がジオフェンス領域の外に移動したかどうかを判断します。

Azure Maps Data Upload API を使用してジオフェンスをアップロードするには、Postman アプリを開いて以下の手順に従います。  

1. Postman アプリを開きます。 Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[コレクション]** を選択します。  コレクションに名前を付け、 **[作成]** ボタンを選択します。

2. 要求を作成するには、 **[新規]** をもう一度選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 要求の **[要求名]** を入力します。 前の手順で作成したコレクションを選択し、 **[Save]\(保存\)** を選択します。

3. [Builder]\(ビルダー\) タブで **POST** HTTP メソッドを選択し、次の URL を入力して、ジオフェンスを Data Upload API にアップロードします。 `{subscription-key}` を実際のプライマリ サブスクリプション キーに必ず置き換えてください。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    URL パス内の `dataFormat` パラメーターに対する "geojson" 値は、アップロードされるデータの形式を表します。

4. **[Body]\(本文\)** をクリックして **[raw]\(未加工\)** 入力形式を選択し、入力形式として **[JSON]** をドロップダウン リストから選択します。 [こちら](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)の JSON データ ファイルを開き、JSON を body セクションにコピーします。 **[送信]** をクリックします。

5. 青色の **[Send]\(送信\)** ボタンをクリックし、要求が処理されるまで待ちます。 要求が完了したら、応答の **[Headers]\(ヘッダー\)** タブに移動します。 **Location** キーの値である `status URL` をコピーします。

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. API 呼び出しの状態を確認するには、`status URL` に対して **GET** HTTP 要求を作成します。 認証のために、プライマリ サブスクリプション キーを URL に追加する必要があります。 **GET** 要求は次の URL のようになります。

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>Azure IoT ハブを作成する

Azure IoT Hub を使用すると、IoT アプリケーションとそれが管理するデバイスの間でセキュリティで保護された信頼性の高い双方向通信を実現できます。  このシナリオでは、車載デバイスから情報を取得して、レンタカーの位置を特定する必要があります。 このセクションでは、*ContosoRental* リソース グループ内に IoT ハブを作成します。 IoT ハブは、デバイス テレメトリ イベントを発行する役割を担います。

> [!NOTE]
> Event Grid でデバイス テレメトリ イベントを発行する IoT ハブの機能は、パブリック プレビュー段階です。 パブリック プレビュー機能は、**米国東部、米国西部、西ヨーロッパ、Azure Government、Azure China 21Vianet**、および **Azure Germany** を除くすべてのリージョンで利用できます。

*ContosoRental* リソース グループに IoT ハブを作成するには、「[IoT Hub を作成する](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)」の手順に従います。

## <a name="register-a-device-in-iot-hub"></a>IoT ハブにデバイスを登録する

デバイスは、IoT ハブ ID レジストリに登録されていない限り、IoT ハブに接続できません。 このシナリオでは、*InVehicleDevice* という名前のデバイスを 1 つ作成します。 IoT ハブ内にデバイスを作成して登録するには、「[IoT ハブに新しいデバイスを登録する](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub)」の手順に従います。 後の手順で使用するため、デバイスの**プライマリ接続文字列**を必ずコピーしてください。

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Azure 関数を作成して Event Grid サブスクリプションを追加する

Azure Functions はサーバーレス コンピューティング サービスです。これを使用すると、コンピューティング インフラストラクチャの明示的なプロビジョニングや管理を行うことなく、小規模なコード ("関数") を実行できます。 Azure Functions の詳細については、[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) のドキュメントを参照してください。

関数は、特定のイベントによって "トリガー" されます。 このシナリオでは、Event Grid トリガーによってトリガーされる関数を作成します。 IoT ハブのデバイス テレメトリ イベント用のイベント サブスクリプションを作成して、トリガーと関数の間の関係を作成します。 デバイス テレメトリ イベントが発生すると、関数はエンドポイントとして呼び出され、[以前に IoT ハブに登録したデバイス](#register-a-device-in-iot-hub)の関連データを受け取ります。

関数に含まれる C# スクリプト コードは、[こちら](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)で確認できます。

ここでは、Azure 関数を設定します。

1. Azure portal ダッシュボードで、 **[リソースの作成]** をクリックします。 検索テキスト ボックスに「**関数アプリ**」と入力します。 **[関数アプリ]** をクリックします。 **Create** をクリックしてください。

2. **[関数アプリ]** 作成ページで、関数アプリに名前を付けます。 **[リソース グループ]** のドロップダウン リストから *[ContosoRental]* を選択します。  **[ランタイム スタック]** として *[.NET Core]* を選択します。 **[次へ: ホスティング >]** がページの下部にあるので、これをクリックします。

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="関数アプリの作成":::

3. **[ストレージ アカウント]** で、「[Azure のストレージ アカウントの作成](#create-an-azure-storage-account)」で作成したストレージ アカウントを選択します。 **[Review + create]\(レビュー + 作成\)** をクリックします。

4. 関数アプリの詳細を確認し、 **[作成]** をクリックします。

5. アプリが作成されたら、それに関数を追加します。 Function App に移動します。 **[関数]** ブレードをクリックします。 ページの上部にある **[+ 追加]** をクリックします。 関数テンプレート パネルが表示されます。 パネルを下にスクロールします。 **[Azure Event Grid trigger]\(Azure Event Grid トリガー\)** をクリックします。

     >[!WARNING]
    > **Azure Event Hub トリガー**と **Azure Event Grid トリガー**のテンプレートには似た名前が付いています。 必ず **Azure Event Grid トリガー** テンプレートをクリックしてください。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="関数を作成する":::

6. 関数に名前を付けます。 このチュートリアルでは、*GetGeoFunction* という名前を使用しますが、好きな名前を使用できます。 **[関数の作成]** をクリックします。

7. 左側のメニューの **[Code + Test]\(コードとテスト\)** ブレードをクリックします。 [C# スクリプト](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)をコピーし、コード ウィンドウに貼り付けます。

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="関数ウィンドウへのコードのコピー/貼り付け":::

8. C# コード内で、次のパラメーターを置き換えます。 **[保存]** をクリックします。 まだ **[Test/Run]\(テストと実行\)** をクリックしないでください。
    * **SUBSCRIPTION_KEY** を、自分の Azure Maps アカウントのプライマリ サブスクリプション キーに置き換えます。
    * **UDID** を、「[ジオフェンスをアップロードする](#upload-a-geofence)」でアップロードしたジオフェンスの `udid` に置き換えます。
    * スクリプト内の **CreateBlobAsync** 関数では、データ ストレージ アカウントでイベントごとに BLOB を作成します。 **ACCESS_KEY**、**ACCOUNT_NAME**、**STORAGE_CONTAINER_NAME** を、自分のストレージ アカウントのアクセス キー、アカウント名、データ ストレージ コンテナーに置き換えます。 これらの値は、「[Azure のストレージ アカウントの作成](#create-an-azure-storage-account)」でストレージ アカウントを作成したときに生成されました。

9. 左側のメニューの **[統合]** ブレードをクリックします。 ダイアグラムの **[Event Grid Trigger]\(Event Grid トリガー\)** をクリックします。 トリガーの名前 (*eventCarTelemetry* など) を入力し、 **[Event Grid サブスクリプションの作成]** をクリックします。

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="イベント サブスクリプションの追加":::

10. サブスクリプションの詳細を入力します。 イベント サブスクリプションに名前を付けます。 *[イベント スキーマ]* で *[Event Grid Schema]\(Event Grid スキーマ\)* を選択します。 **[トピックの種類]** で、 *[Azure IoT Hub Accounts]\(Azure IoT Hub アカウント\)* を選択します。 **[リソース グループ]** で、このチュートリアルの冒頭で作成したリソース グループを選択します。 **[リソース]** で、「[Azure IoT ハブを作成する](#create-an-azure-iot-hub)」で作成した IoT ハブを選択します。 **[イベントの種類のフィルター]** で、 *[Device Telemetry]\(デバイス テレメトリ\)* を選択します。 これらのオプションを選択すると、 **[トピックの種類]** が *[IoT Hub]* に変更されたことがわかります。 **[System Topic Name]\(システム トピック名\)** には、リソースと同じ名前を使用できます。  最後に、 **[エンドポイントの詳細]** セクションで **[エンドポイントの選択]** をクリックします。 すべての設定を受け入れ、 **[選択の確認]** をクリックします。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="イベント サブスクリプションの作成":::

11. 設定を確認します。 このセクションの冒頭で作成した関数がエンドポイントで指定されていることを確認します。 **Create** をクリックしてください。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="[イベント サブスクリプションの作成] の確認":::

12. ここで、 **[トリガーの編集]** パネルに戻ります。 **[保存]** をクリックします。

## <a name="filter-events-using-iot-hub-message-routing"></a>IoT Hub のメッセージ ルーティングを使用してイベントをフィルター処理する

Azure 関数に Event Grid サブスクリプションを追加すると、指定された IoT ハブにメッセージング ルートが自動的に作成されます。 メッセージ ルーティングを使用すると、各種データ型をさまざまなエンドポイントにルーティングできます。 たとえば、デバイス テレメトリのメッセージ、デバイスのライフサイクル イベント、デバイス ツインの変更イベントをルーティングできます。 IoT ハブのメッセージ ルーティングの詳細については、[IoT Hub メッセージ ルーティングの使用](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)に関するページを参照してください。

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="IoT ハブでのメッセージ ルーティング":::

このシナリオ例では、レンタカーの移動中にのみメッセージを受信する必要があります。 `Engine` プロパティが **"ON"** に等しいイベントをフィルター処理するルーティング クエリを作成します。 ルーティング クエリを作成するには、**RouteToEventGrid** ルートをクリックし、**ルーティング クエリ**を **"Engine='ON'"** に置き換えて、**[保存]** をクリックします。 これで、IoT ハブにより、エンジンがオンになっているデバイス テレメトリのみが発行されます。

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="ルーティング メッセージをフィルター処理する":::

>[!TIP]
>デバイスからクラウドへの IoT メッセージに対してクエリを実行するには、さまざまな方法があります。メッセージ ルーティング構文の詳細については、[IoT Hub メッセージ ルーティング](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)に関するページを参照してください。

## <a name="send-telemetry-data-to-iot-hub"></a>テレメトリ データを IoT Hub に送信する

Azure 関数が起動したら、テレメトリ データを IoT ハブに送信できます。これにより、そのデータが Event Grid にルーティングされます。 C# アプリケーションを使用して、レンタカーの車載デバイスの位置情報データをシミュレートしましょう。 アプリケーションを実行するには、自分の開発用マシン上に .NET Core SDK 2.1.0 以上が必要です。 以下の手順に従って、シミュレートされたテレメトリ データを IoT Hub に送信します。

1. まだ行っていない場合は、C# プロジェクト [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) をダウンロードします。

2. 任意のテキスト エディターで simulatedCar.cs ファイルを開き、`connectionString` の値を、デバイスを登録したときに保存したものに置き換えて、ファイルへの変更を保存します。

3. マシンに .NET Core がインストールされていることを確認します。 ローカル ターミナル ウィンドウで、C# プロジェクトのルート フォルダーに移動し、次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なパッケージをインストールします。

    ```cmd/sh
    dotnet restore
    ```

4. 同じターミナルで次のコマンドを実行して、レンタカー シミュレーション アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```

  ローカル ターミナルは次のようになります。

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="ターミナルの出力":::

ここで BLOB ストレージ コンテナーを開くと、車がジオフェンスの外側にあった位置の BLOB を 4 つ表示できます。

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="コンテナー内の BLOB を表示する":::

以下のマップでは、ジオフェンス外の車の位置ポイントを 4 か所示しています。 各位置は定期的にログされました。

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="違反のマップ":::

## <a name="explore-azure-maps-and-iot"></a>Azure Maps と IoT について調べる

このチュートリアルで使用した Azure Maps API シリーズの詳細については、以下を参照してください。

* [Get Search Address Reverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Get Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Azure Maps REST API シリーズの完全な一覧については、次を参照してください。

* [Azure Maps REST API シリーズ](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

IoT プラグ アンド プレイの詳細については、次を参照してください。

* [IoT プラグ アンド プレイ](https://docs.microsoft.com/azure/iot-pnp)

Azure で IoT の認定を受けたデバイスの一覧を取得するには、次のページにアクセスします。

* [Azure 認定デバイス](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>次の手順

デバイスからクラウドへのテレメトリを送信する方法、およびその逆の方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [デバイスから利用統計情報を送信する](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
