---
title: Azure IoT Hub から Azure Cosmos DB を使用してデバイス接続イベントを順序付ける | Microsoft Docs
description: この記事では、Azure IoT Hub から Azure Cosmos DB を使用してデバイス接続イベントの順序付けと記録を行って、最新の接続状態を管理する方法について説明します
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: a020221d841682d1e18d2b728a732ec4dfc35ef3
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67988283"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Azure IoT Hub から Azure Cosmos DB を使用してデバイス接続イベントを順序付ける

Azure Event Grid を使用すると、イベント ベースのアプリケーションを構築するのに役立ち、IoT イベントをビジネス ソリューションに簡単に統合できます。 この記事では、最新のデバイス接続状態を追跡して Cosmos DB に保存するために使用できる設定について説明します。 ここでは、デバイス接続イベントとデバイス切断イベントで使用可能なシーケンス番号を使用し、Cosmos DB に最新の状態を保存します。 Cosmos DB 内のコレクションに対して実行されるアプリケーション ロジックであるストアド プロシージャを使用します。

シーケンス番号は、16 進数の文字列表現です。 文字列比較を使用して、より大きな数値を識別できます。 文字列を 16 進数に変換すると、数値は 256 ビットの数値になります。 シーケンス番号は狭義に増加し、最新のイベントには他のイベントよりも大きな番号が与えられます。 これは、デバイスの接続と切断が頻繁に発生するときに最新のイベントのみを使用してダウンストリームのアクションをトリガーする場合に便利です。それは、Azure Event Grid ではイベントの順序付けがサポートされないからです。

## <a name="prerequisites"></a>前提条件

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、[無料アカウントを作成する](https://azure.microsoft.com/pricing/free-trial/)ことができます。

* アクティブな Azure Cosmos DB SQL API アカウント。 まだ作成していない場合は、「[データベース アカウントの作成](../cosmos-db/create-sql-api-java.md#create-a-database-account)」の手順を参照してください。

* データベース内のコレクション。 「[コレクションの追加](../cosmos-db/create-sql-api-java.md#add-a-container)」の手順を参照してください。 コレクションを作成するときに、パーティション キーに `/id` を使用します。

* Azure の IoT Hub。 まだ作成していない場合は、「[IoT Hub の概要](iot-hub-csharp-csharp-getstarted.md)」のチュートリアルをご覧ください。

## <a name="create-a-stored-procedure"></a>ストアド プロシージャを作成する

最初に、ストアド プロシージャを作成します。このストアド プロシージャで、受信イベントのシーケンス番号を比較し、デバイスごとに最新のイベントをデータベースに記録するロジックを実行するように設定します。

1. Cosmos DB SQL API で、 **[データ エクスプローラー]**  >  **[アイテム]**  >  **[新しいストアド プロシージャ]** を選択します。

   ![ストアド プロシージャを作成する](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. ストアド プロシージャ ID の **LatestDeviceConnectionState** を入力し、**ストアド プロシージャの本文**に次のコードを貼り付けます。 このコードでストアド プロシージャ本文のすべての既存のコードを置き換える必要があることに注意してください。 このコードでは、デバイス ID ごとに 1 行を維持し、最も大きなシーケンス番号を識別することによってそのデバイス ID の最新の接続状態を記録します。

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. ストアド プロシージャを保存します。

    ![ストアド プロシージャを保存する](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>ロジック アプリを作成します

最初にロジック アプリを作成し、仮想マシンのリソース グループを監視する Event Grid トリガーを追加します。

### <a name="create-a-logic-app-resource"></a>ロジック アプリ リソースを作成する

1. [Azure portal](https://portal.azure.com)で、 **[+リソースの作成]** 、 **[統合]** 、 **[ロジック アプリ]** の順に選択します。

   ![ロジック アプリを作成する](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. ロジック アプリにサブスクリプション内で一意の名前を指定し、IoT Hub と同じサブスクリプション、リソース グループ、場所を選びます。

   ![新しいロジック アプリ](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. **[作成]** を選択して、ロジック アプリを作成します。

   これで、ロジック アプリの Azure リソースが作成されました。 Azure によってロジック アプリがデプロイされたら、すばやく開始できるように Logic Apps デザイナーによって一般的なパターンのテンプレートが表示されます。

   > [!NOTE]
   > ロジック アプリを見つけて再度開くには、 **[リソース グループ]** を選択し、このハウツー記事で使用しているリソース グループを選択します。 次に、新しいロジック アプリを選択します。 これにより、ロジック アプリ デザイナーが開きます。

4. ロジック アプリ デザイナーで、一般的なトリガーが表示されるまで右にスクロールします。 **[テンプレート]** の下で **[空のロジック アプリ]** を選択すると、ロジック アプリを最初から作成できます。

### <a name="select-a-trigger"></a>トリガーを選択する

トリガーは、ロジック アプリを開始する特定のイベントです。 このチュートリアルでは、ワークフローを開始するトリガーは、HTTP 経由での要求の受信です。

1. コネクタとトリガーの検索バーに、「**HTTP**」と入力して Enter キーを押します。

2. トリガーとして **[要求 - HTTP 要求の受信時]** を選びます。

   ![HTTP 要求トリガーを選ぶ](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. **[サンプルのペイロードを使用してスキーマを生成する]** を選びます。

   ![サンプルのペイロードを使用してスキーマを生成する](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. 次のサンプル JSON コードをテキスト ボックスに貼り付けて、 **[完了]** を選びます。

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![サンプルの JSON ペイロードを貼り付ける](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. "**application/json に設定されている Content-Type ヘッダーを要求に忘れずに含めてください**" というポップアップ通知を受け取る場合があります。 この指摘は無視しても安全なので、次のセクションに進みます。

### <a name="create-a-condition"></a>条件を作成する

ロジック アプリのワークフロー内では、条件は特定の条件を満たした後に特定のアクションを実行するのに役立ちます。 条件が満たされると、目的の動作を定義することができます。 このチュートリアルでは、eventType がデバイス接続とデバイス切断のどちらであるかを調べる条件を設定します。 アクションでは、データベース内のストアド プロシージャを実行します。

1. **[+ 新しいステップ]** 、 **[ビルトイン]** の順に選択し、 **[条件]** を見つけて選択します。 **[値の選択]** をクリックすると、動的コンテンツ (選択可能なフィールド) を表示するボックスがポップアップ表示されます。 次のようにフィールドに入力し、デバイス接続イベントとデバイス切断イベントのみを対象にこのアクションが実行されるようにします。

   * 値の選択: **eventType** -- このフィールドをクリックすると表示される動的コンテンツ内のフィールドから、この値を選択します。
   * [次の値に等しい] を **[次の文字で終了する]** に変更します。
   * 値の選択: **nected**。

     ![条件を入力する](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. **[true の場合]** ダイアログで、 **[アクションの追加]** をクリックします。
  
   ![true の場合のアクションを追加する](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Cosmos DB を検索し、 **[Azure Cosmos DB - ストアド プロシージャの実行]** を選択します

   ![CosmosDB を検索する](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. **[接続名]** に「**cosmosdb-connection**」と入力し、テーブルでエントリを選択して、 **[作成]** を選択します。 **[ストアド プロシージャの実行]** パネルが表示されます。 フィールドに次の値を入力します。

   **データベース ID**:ToDoList

   **コレクション ID**:項目

   **Sproc ID**:LatestDeviceConnectionState

5. **[新しいパラメーターの追加]** を選択します。 表示されたドロップダウンで、 **[パーティション キー]** と **[ストアド プロシージャのパラメーター]** の横のボックスをチェックし、画面の任意の場所をクリックっします。これにより、パーティション キー値のフィールドとストアド プロシージャーのパラメーターのフィールドが追加されます。

   ![ロジック アプリのアクションを設定する](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. 次に示すように、パーティション キー値とパラメーターを入力します。 以下で示されているように、かっこと二重引用符を入力してください。 ここで使用できる有効な値を取得するために、 **[動的なコンテンツの追加]** をクリックしなければならない場合があります。

   ![ロジック アプリのアクションを設定する](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. **[For Each]** と表示されているウィンドウの上部の **[以前の手順から出力を選択]** で、 **[本文]** が選択されていることを確認します。

   ![ロジック アプリ for-each の入力](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. ロジック アプリを保存し、

### <a name="copy-the-http-url"></a>HTTP の URL をコピーする

Logic Apps デザイナーを終了する前に、ロジック アプリがトリガーをリッスンする URL をコピーします。 この URL を使って、Event Grid を構成します。

1. **[HTTP 要求の受信時]** トリガー構成ボックスをクリックして展開します。

2. **[HTTP POST の URL]** の横にあるコピー ボタンを選んで値をコピーします。

   ![HTTP POST の URL をコピーする](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. 次のセクションで参照できるように、この URL を保存します。

## <a name="configure-subscription-for-iot-hub-events"></a>IoT Hub イベント用のサブスクリプションを構成する

このセクションでは、発生したらイベントを発行するように IoT Hub を構成します。

1. Azure Portal で、お使いの IoT ハブに移動します。

2. **イベント**を選択します。

   ![Event Grid の詳細を表示する](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. **[+ イベント サブスクリプション]** を選びます。

   ![新しいイベント サブスクリプションを作成する](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. **[イベント サブスクリプションの詳細]** を入力します。わかりやすい名前を指定し、 **[イベント グリッド スキーマ]** を選択します。

5. **[イベントの種類]** フィールドに入力します。 ドロップダウン リストで、 **[Device Connected]\(デバイスの接続\)** および **[Device Disconnected]\(デバイスの切断\)** のみをメニューから選択します。 画面の他の場所をクリックしてリストを閉じ、選択内容を保存します。

   ![検索するイベントの種類を設定する](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. **[エンドポイントの詳細]** で、 [エンドポイントのタイプ] として **[web hook]** を選択し、[エンドポイントの選択] をクリックして、ロジック アプリからコピーした URL を貼り付けて選択を確認します。

   ![エンドポイントの URL を選択する](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. これで、フォームは次の例のようになります。

   ![サンプルのイベント サブスクリプション フォーム](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   **[作成]** を選び、イベント サブスクリプションを保存します。

## <a name="observe-events"></a>イベントを確認する

イベント サブスクリプションを設定した後は、デバイスを接続してテストします。

### <a name="register-a-device-in-iot-hub"></a>IoT Hub にデバイスを登録する

1. IoT Hub から、 **[IoT Devices]\(IoT デバイス\)** を選びます。

2. ウィンドの上部にある **[+ 追加]** を選択します。

3. **[デバイス ID]** に「`Demo-Device-1`」と入力します。

4. **[保存]** を選択します。

5. 異なるデバイス ID を使用して複数のデバイスを追加できます。

   ![ハブに追加されるデバイス](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. デバイスを再度クリックすると、接続文字列とキーが入力されます。 後で使用するために **[接続文字列 --- 主キー]** をコピーします。

   ![デバイスの接続文字列](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Raspberry Pi シミュレーターを起動する

Raspberry Pi Web シミュレーターを使用してデバイス接続をシミュレートしましょう。

[Raspberry Pi シミュレーターの起動](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Raspberry Pi Web シミュレーターでサンプル アプリケーションを実行する

この操作を実行すると、デバイス接続イベントがトリガーされます。

1. コーディング領域で、行 15 のプレースホルダーを、前のセクションの最後に保存した Azure IoT Hub デバイスの接続文字列に置き換えます。

   ![デバイスの接続文字列を貼り付ける](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. **[Run]\(実行\)** を選択してアプリケーションを実行します。

IoT Hub に送信されるセンサー データとメッセージを示す次のような出力が表示されます。

   ![アプリケーションの実行](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   **[Stop]\(停止\)** をクリックしてシミュレーターを停止して、**デバイス切断**イベントをトリガーします。

サンプル アプリケーションを実行した結果、センサー データが収集され、IoT Hub に送信されました。

### <a name="observe-events-in-cosmos-db"></a>Cosmos DB でイベントを確認する

実行されたストアド プロシージャの結果は、Cosmos DB ドキュメント内で確認できます。 外観は次のようになります。 各行にデバイスごとの最新のデバイス接続状態が含まれます。

   ![操作の結果](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Azure CLI の使用

[Azure portal](https://portal.azure.com) を使う代わりに、Azure CLI を使って IoT Hub の手順を行うことができます。 詳細については、[イベント サブスクリプションの作成](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription)と[IoT デバイスの作成](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create)に関する Azure CLI のページを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルでは、Azure サブスクリプションで料金が発生するリソースを使いました。 チュートリアルを試してテストを完了したら、残しておきたくないリソースを無効にするか削除します。

アプリ ロジックでの作業を失いたくない場合は、削除ではなく無効にします。

1. ロジック アプリに移動します。

2. **[概要]** ブレードで、 **[削除]** または **[無効]** を選びます。

    各サブスクリプションで使うことができる無料 IoT Hub は 1 つです。 このチュートリアル用に無料のハブを作成した場合は、課金されないように削除する必要はありません。

3. IoT Hub に移動します。

4. **[概要]** ブレードで **[削除]** を選びます。

    IoT Hub を残しておく場合でも、作成したイベント サブスクリプションを削除できます。

5. IoT Hub で **[イベント グリッド]** を選びます。

6. 削除するイベント サブスクリプションを選びます。

7. **[削除]** を選択します。

Azure Cosmos DB アカウントを Azure portal から削除するには、アカウント名を右クリックし、 **[アカウントの削除]** をクリックします。 [Azure Cosmos DB アカウントを削除](https://docs.microsoft.com/azure/cosmos-db/manage-account)するための詳細な手順を参照してください。

## <a name="next-steps"></a>次の手順

* [Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする](../iot-hub/iot-hub-event-grid.md)方法を確認します

* [IoT Hub イベントのチュートリアルを試します](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Event Grid](../event-grid/overview.md) で他にできることについて確認します