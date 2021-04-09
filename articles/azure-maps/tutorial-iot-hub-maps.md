---
title: チュートリアル:IoT 空間分析を実装する | Microsoft Azure Maps
description: IoT Hub を Microsoft Azure Maps サービス API に統合する方法に関するチュートリアルです
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b5c65035f8b51b53f617d4562fe1982f53f0deec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678274"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>チュートリアル:Azure Maps を使用して IoT 空間分析を実装する

IoT シナリオでは、空間と時間に生じる関連イベントをキャプチャして追跡するのが一般的です。 たとえば、フリート管理、資産追跡、モビリティ、スマート シティといったアプリケーションが考えられます。 このチュートリアルでは、Azure Maps API シリーズを使用して、使用されたレンタカーの移動を追跡するソリューションについて説明します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * 車の追跡データをログに記録するための Azure ストレージ アカウントを作成します。
> * データ アップロード API を使用して Azure Maps Data Service (プレビュー) にジオフェンスをアップロードします。
> * Azure IoT Hub でハブを作成して、デバイスを登録します。
> * Azure Functions で関数を作成し、Azure Maps 空間分析に基づいてビジネス ロジックを実装します。
> * Azure Event Grid 経由で Azure 関数から IoT デバイス テレメトリ イベントをサブスクライブします。
> * IoT Hub のメッセージ ルーティングを使用して、テレメトリ イベントをフィルター処理します。

## <a name="prerequisites"></a>前提条件

1. [Azure portal](https://portal.azure.com) にサインインします。

2. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)。

3. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」をご覧ください。

4. [リソース グループを作成します](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)。 このチュートリアルでは、リソース グループに *ContosoRental* という名前を付けますが、好きな名前を選択できます。

5. [rentalCarSimulation C# プロジェクト](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)をダウンロードします。

このチュートリアルでは [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境を選択することもできます。

## <a name="use-case-rental-car-tracking"></a>ユース ケース: レンタカーの追跡

あるレンタカー会社で、レンタカーの位置情報、移動距離、運転状態を記録する必要があるとします。 また、この会社は、車が承認された適切な地理的領域から出た場合は常にこの情報を保存したいとも考えています。

レンタカーには、テレメトリ データを IoT ハブに定期的に送信する IoT デバイスが搭載されています。 テレメトリには現在の場所が含まれ、車両のエンジンが作動中であるかどうかが示されます。 デバイスの場所スキーマは、[地理空間データ用の IoT プラグ アンド プレイ スキーマ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md)に準拠しています。 レンタカーのデバイス テレメトリ スキーマは、次の JSON コードのようになります。

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

このチュートリアルで追跡する車は 1 台のみです。 Azure サービスを設定した後、車のシミュレーターを実行するには、[rentalCarSimulation C# プロジェクト](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)をダウンロードする必要があります。 イベントから関数の実行までのプロセス全体が次の手順に要約されています。

1. 車載デバイスにより、テレメトリ データが IoT ハブに送信されます。

2. 車のエンジンが動作している場合、ハブはテレメトリ データを Event Grid に発行します。

3. Azure 関数が、デバイス テレメトリ イベントに対するイベント サブスクリプションによってトリガーされます。

4. この関数は、車載デバイスの位置座標、イベント時刻、デバイス ID をログに記録します。 次に、[Spatial Geofence Get API](/rest/api/maps/spatial/getgeofence) を使用して、車がジオフェンスの外側に移動したかどうかを判断します。 ジオフェンス境界の外側で走行した場合、この関数により、イベントから受信した位置データが BLOB コンテナーに格納されます。 また、関数によって[住所の逆引き検索](/rest/api/maps/search/getsearchaddressreverse)に対してクエリが実行されて、座標位置が住所に変換され、残りのデバイス位置データと共に格納されます。

次の図は、このシステムの概要を示したものです。

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="システムの概要図。":::

次の図では、ジオフェンス領域が青で強調表示されています。 レンタカーのルートは、緑色の線で示されています。

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="ジオフェンス ルートを示す図。":::

## <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

車の違反の追跡データを格納するために、リソース グループに[汎用 v2 ストレージ アカウント](../storage/common/storage-account-overview.md#general-purpose-v2-accounts)を作成します。 リソース グループを作成していない場合は、[リソース グループの作成](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)に関するセクションの指示に従ってください。 このチュートリアルでは、リソース グループに *ContosoRental* という名前を付けます。

ストレージ アカウントを作成するには、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)」の手順に従います。 このチュートリアルでは、ストレージ アカウントの名前を *contosorentalstorage* としていますが、通常は好きな名前を付けることができます。

ストレージ アカウントを正常に作成したら、ログ データを格納するコンテナーを作成する必要があります。

1. 新しく作成したストレージ アカウントに移動します。 **[要点]** セクションの **[コンテナー]** リンクをクリックします。

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="BLOB ストレージのコンテナーのスクリーンショット。":::

2. 左上隅の **[+ Container]\(+ コンテナー\)** を選択します。 ブラウザーの右側にパネルが表示されます。 コンテナーに *contoso-rental-logs* という名前を付け、 **[作成]** を選択します。

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="BLOB コンテナーを作成する際のスクリーンショット。":::

3. 自分のストレージ アカウントの **[アクセス キー]** ペインに移動し、**ストレージ アカウント名** と **[key1]** セクションの **[キー]** の値をコピーします。 Azure 関数を作成して Event Grid サブスクリプションを追加する方法に関するセクションで、両方の値が必要になります。

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="ストレージ アカウント名とキーをコピーする際のスクリーンショット。":::

## <a name="upload-a-geofence"></a>ジオフェンスをアップロードする

次に、[Postman アプリ](https://www.getpostman.com)を使用して、Azure Maps に[ジオフェンスをアップロードします](./geofence-geojson.md)。 ジオフェンスにより、レンタカーの承認された地理的領域が定義されます。 Azure 関数でジオフェンスを使用して、車がジオフェンス領域の外に移動したかどうかを判断します。

Azure Maps Data Upload API を使用してジオフェンスをアップロードするには、次の手順に従います。 

1. Postman アプリを開き、 **[新規]** を選択します。 **[新規作成]** ウィンドウで **[コレクション]** を選択します。 コレクションに名前を付け、 **[作成]** を選択します。

2. 要求を作成するには、 **[新規]** をもう一度選択します。 **[新規作成]** ウィンドウで、 **[要求]** を選択し、要求の名前を入力します。 前の手順で作成したコレクションを選択し、 **[Save]\(保存\)** を選択します。

3. ビルダー タブで **POST** HTTP メソッドを選択し、次の URL を入力して、ジオフェンスを Data Upload API にアップロードします。 `{subscription-key}` を実際のプライマリ サブスクリプション キーに必ず置き換えてください。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    URL パス内の `dataFormat` パラメーターに対する `geojson` 値は、アップロードするデータの形式を表します。

4. 入力形式として **[本文]**  >  **[raw]\(未加工\)** を選択し、ドロップダウンリストから **[JSON]** を選択します。 [JSON データ ファイルを開き](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)、JSON を body セクションにコピーします。 **[Send]** を選択します。

5. **[Send]\(送信\)** を選択し、要求が処理されるまで待ちます。 要求が完了したら、応答の **[Headers]\(ヘッダー\)** タブに移動します。 **Location** キーの値である `status URL` をコピーします。

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. API 呼び出しの状態を確認するには、`status URL` に対して **GET** HTTP 要求を作成します。 認証のために、プライマリ サブスクリプション キーを URL に追加する必要があります。 **GET** 要求は次の URL のようになります。

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. **GET** HTTP 要求が正常に完了すると、`resourceLocation` が返されます。 `resourceLocation` には、アップロードされたコンテンツの一意の `udid` が格納されます。 このチュートリアルで後ほど使用するので、この `udid` をコピーしておきます。

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>IoT ハブを作成する

IoT ハブを使用すると、IoT アプリケーションとそれが管理するデバイスの間で、セキュリティで保護された信頼性の高い双方向通信を実現できます。 このチュートリアルでは、車載デバイスから情報を取得して、レンタカーの位置を特定する必要があります。 このセクションでは、*ContosoRental* リソース グループ内に IoT ハブを作成します。 このハブは、デバイス テレメトリ イベントを発行する役割を担います。

> [!NOTE]
> Event Grid でデバイス テレメトリ イベントを発行する機能は、現在、パブリック プレビュー段階です。 この機能は、以下を除くすべての Logic Apps リージョンで利用できます。米国東部、米国西部、西ヨーロッパ、Azure Government、Azure China 21Vianet、Azure Germany。

*ContosoRental* リソース グループに IoT ハブを作成するには、[IoT ハブの作成](../iot-hub/quickstart-send-telemetry-dotnet.md#create-an-iot-hub)に関するセクションの手順に従います。

## <a name="register-a-device-in-your-iot-hub"></a>IoT ハブにデバイスを登録する

デバイスは、IoT ハブ ID レジストリに登録されていない限り、IoT ハブに接続できません。 ここでは、*InVehicleDevice* という名前のデバイスを 1 つ作成します。 IoT ハブ内にデバイスを作成して登録するには、「[IoT ハブに新しいデバイスを登録する](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub)」の手順に従います。 デバイスのプライマリ接続文字列を必ずコピーしてください。 この情報は後で必要になります。

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>関数を作成して Event Grid サブスクリプションを追加する

Azure Functions はサーバーレス コンピューティング サービスです。これを使用すると、コンピューティング インフラストラクチャの明示的なプロビジョニングや管理を行うことなく、小規模なコード ("関数") を実行できます。 詳細については、[Azure Functions](../azure-functions/functions-overview.md) に関するページをご覧ください。

関数は、特定のイベントによってトリガーされます。 ここでは、Event Grid トリガーによってトリガーされる関数を作成します。 IoT ハブのデバイス テレメトリ イベント用のイベント サブスクリプションを作成して、トリガーと関数の間の関係を作成します。 デバイス テレメトリ イベントが発生すると、関数はエンドポイントとして呼び出され、前に IoT ハブに登録したデバイスの関連データを受け取ります。

[関数に含める C# スクリプト コード](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)をご確認ください。

次に、Azure 関数を設定します。

1. Azure portal のダッシュボードで、 **[リソースの作成]** を選択します。 検索テキスト ボックスに「**関数アプリ**」と入力します。 **[関数アプリ]**  >  **[作成]** を選択します。

1. **[関数アプリ]** 作成ページで、関数アプリに名前を付けます。 **[リソース グループ]** のドロップダウン リストから **[ContosoRental]** を選択します。 **[ランタイム スタック]** として **[.NET Core]** を選択します。 ページの下部にある **[Next: Hosting >]\(次へ: ホスティング\)** を選択します。

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="関数アプリの作成のスクリーンショット。":::

1. **[ストレージ アカウント]** で、「[Azure のストレージ アカウントの作成](#create-an-azure-storage-account)」で作成したストレージ アカウントを選択します。 **[Review + create]\(レビュー + 作成\)** を選択します。

1. 関数アプリの詳細を確認し、 **[作成]** を選択します。

1. アプリが作成されたら、それに関数を追加します。 Function App に移動します。 **[関数]** ペインを選択します。 ページの最上部で **[追加]** を選択します。 関数テンプレート パネルが表示されます。 パネルを下にスクロールし、 **[Azure Event Grid Trigger]\(Azure Event Grid トリガー\)** を選択します。

     >[!IMPORTANT]
    > **Azure Event Hub トリガー** と **Azure Event Grid トリガー** のテンプレートには似た名前が付いています。 **Azure Event Grid トリガー** テンプレートを選択していることを確認します。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="関数の作成のスクリーンショット。":::

1. 関数に名前を付けます。 このチュートリアルでは、*GetGeoFunction* という名前を使用しますが、通常は好きな名前を使用できます。 **[関数の作成]** を選択します。

1. 左側のメニューで、 **[コードとテスト]** ペインを選択します。 [C# スクリプト](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)をコピーし、コード ウィンドウに貼り付けます。

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="コードのコピーと関数ウィンドウへの貼り付けのスクリーンショット。":::

1. C# コード内で、次のパラメーターを置き換えます。
    * **SUBSCRIPTION_KEY** を、自分の Azure Maps アカウントのプライマリ サブスクリプション キーに置き換えます。
    * **UDID** を、「[ジオフェンスをアップロードする](#upload-a-geofence)」でアップロードしたジオフェンスの `udid` に置き換えます。
    * スクリプト内の `CreateBlobAsync` 関数では、データ ストレージ アカウントでイベントごとに BLOB を作成します。 **ACCESS_KEY**、**ACCOUNT_NAME**、**STORAGE_CONTAINER_NAME** を、自分のストレージ アカウントのアクセス キー、アカウント名、データ ストレージ コンテナーに置き換えます。 これらの値は、「[Azure のストレージ アカウントの作成](#create-an-azure-storage-account)」でストレージ アカウントを作成したときに生成されました。

1. 左側のメニューで、 **[統合]** ペインを選択します。 ダイアグラムの **[Event Grid Trigger]\(Event Grid トリガー\)** を選択します。 トリガーの名前 *eventGridEvent* を入力し、 **[Event Grid サブスクリプションの作成]** を選択します。

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="イベント サブスクリプションの追加のスクリーンショット。":::

1. サブスクリプションの詳細を入力します。 イベント サブスクリプションに名前を付けます。 **[イベント スキーマ]** で **[Event Grid Schema]\(Event Grid スキーマ\)** を選択します。 **[トピックの種類]** で、 **[Azure IoT Hub Accounts]\(Azure IoT Hub アカウント\)** を選択します。 **[リソース グループ]** で、このチュートリアルの冒頭で作成したリソース グループを選択します。 **[リソース]** で、「IoT ハブを作成する」で作成した IoT ハブを選択します。 **[イベントの種類のフィルター]** で、 **[Device Telemetry]\(デバイス テレメトリ\)** を選択します。

   これらのオプションを選択すると、 **[トピックの種類]** が **[IoT Hub]** に変更されたことがわかります。 **[System Topic Name]\(システム トピック名\)** には、リソースと同じ名前を使用できます。 最後に、 **[エンドポイントの詳細]** セクションで **[エンドポイントの選択]** を選択します。 すべての設定を受け入れ、 **[選択の確認]** を選択します。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="イベント サブスクリプションの作成のスクリーンショット。":::

1. 設定を確認します。 このセクションの冒頭で作成した関数がエンドポイントで指定されていることを確認します。 **［作成］** を選択します

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="イベント サブスクリプションの作成の確認のスクリーンショット。":::

1. ここで、 **[トリガーの編集]** パネルに戻ります。 **[保存]** を選択します。

## <a name="filter-events-by-using-iot-hub-message-routing"></a>IoT ハブのメッセージ ルーティングを使用してイベントをフィルター処理する

Azure 関数に Event Grid サブスクリプションを追加すると、指定された IoT ハブにメッセージング ルートが自動的に作成されます。 メッセージ ルーティングを使用すると、各種データ型をさまざまなエンドポイントにルーティングできます。 たとえば、デバイス テレメトリのメッセージ、デバイスのライフサイクル イベント、デバイス ツインの変更イベントをルーティングできます。 詳細については、[IoT ハブのメッセージ ルーティングの使用](../iot-hub/iot-hub-devguide-messages-d2c.md)に関するページをご覧ください。

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="IoT ハブでのメッセージ ルーティングのスクリーンショット。":::

このシナリオ例では、レンタカーの移動中にのみメッセージを受信する必要があります。 `Engine` プロパティが **"ON"** になっているイベントをフィルター処理するルーティング クエリを作成します。 ルーティング クエリを作成するには、**RouteToEventGrid** ルートをクリックし、 **[ルーティング クエリ]** の値を **"Engine='ON'"** で置き換えます。 次に、 **[保存]** を選択します。 これで、IoT ハブにより、エンジンがオンになっているデバイス テレメトリのみが発行されます。

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="メッセージ ルーティングのフィルター処理のスクリーンショット。":::

>[!TIP]
>IoT device-to-cloud メッセージを照会するさまざまな方法があります。 メッセージ ルーティング構文の詳細については、[IoT Hub のメッセージ ルーティング](../iot-hub/iot-hub-devguide-routing-query-syntax.md)に関するページを参照してください。

## <a name="send-telemetry-data-to-iot-hub"></a>テレメトリ データを IoT Hub に送信する

Azure 関数が実行されると、テレメトリ データを IoT ハブに送信できます。これにより、そのデータが Event Grid にルーティングされます。 C# アプリケーションを使用して、レンタカーの車載デバイスの位置情報データをシミュレートします。 アプリケーションを実行するには、自分の開発用コンピューター上に .NET Core SDK 2.1.0 以降が必要です。 以下の手順に従って、シミュレートされたテレメトリ データを IoT ハブに送信します。

1. まだ行っていない場合は、C# プロジェクト [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) をダウンロードします。

2. 任意のテキスト エディターで `simulatedCar.cs` ファイルを開き、`connectionString` の値を、デバイスを登録したときに保存したもので置き換えます。 変更をファイルに保存します。

3. マシンに .NET Core がインストールされていることを確認します。 ローカル ターミナル ウィンドウで、C# プロジェクトのルート フォルダーに移動し、次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なパッケージをインストールします。

    ```cmd/sh
    dotnet restore
    ```

4. 同じターミナルで次のコマンドを実行して、レンタカー シミュレーション アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```


  ローカル ターミナルは次のようになります。

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="ターミナル出力のスクリーンショット。":::

ここで BLOB ストレージ コンテナーを開くと、車がジオフェンスの外側にあった位置の BLOB を 4 つ表示できます。

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="コンテナー内の BLOB が表示されたスクリーンショット。":::

以下のマップには、ジオフェンス外の車の位置が 4 か所示されています。 各位置は定期的にログされました。

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="違反マップのスクリーンショット。":::

## <a name="explore-azure-maps-and-iot"></a>Azure Maps と IoT について調べる

このチュートリアルで使用した Azure Maps API シリーズの詳細については、以下を参照してください。

* [Get Search Address Reverse](/rest/api/maps/search/getsearchaddressreverse)
* [Get Geofence](/rest/api/maps/spatial/getgeofence)

Azure Maps REST API シリーズの完全な一覧については、次を参照してください。

* [Azure Maps REST API シリーズ](/rest/api/maps/spatial/getgeofence)

* [IoT プラグ アンド プレイ](../iot-pnp/index.yml)

Azure で IoT の認定を受けたデバイスの一覧を取得するには、次のページにアクセスします。

* [Azure 認定デバイス](https://catalog.azureiotsolutions.com/)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップが必要なリソースはありません。

## <a name="next-steps"></a>次の手順

device-to-cloud、およびその逆方向にテレメトリを送信する方法については、次を参照してください。


> [!div class="nextstepaction"]
> [デバイスから利用統計情報を送信する](../iot-hub/quickstart-send-telemetry-dotnet.md)