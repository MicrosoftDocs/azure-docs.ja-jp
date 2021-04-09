---
title: チュートリアル - IoT Hub のイベントを使用して Azure Logic Apps をトリガーする
description: このチュートリアルでは、Azure Event Grid のイベント ルーティング サービスを使い、IoT Hub のイベントに基づいて Azure Logic Apps のアクションを実行する自動化されたプロセスの作成方法を紹介します。
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3772988e9bf4f733323adae6c0527c8b811c4d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624458"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>チュートリアル:Event Grid および Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する

Azure Event Grid を使うと、ダウンストリームのビジネス アプリケーションのアクションをトリガーすることによって、IoT Hub のイベントに対応することができます。

この記事では、IoT Hub と Event Grid を使うサンプルを構成する手順について説明します。 最終的に、デバイスが IoT Hub に接続されるか接続が解除されるたびに通知メールを送信するように Azure Logic Apps が設定されます。 Event Grid を使用すると、重要なデバイスの接続解除について、適切なタイミングで通知を受け取ることができます。 メトリックと診断がログまたはアラートとして現れるまでには、数分 (厳密な数字を挙げることは難しいですが、20 分以上) かかる場合があります。 これは、重要なインフラストラクチャでは受け入れがたい遅れでしょう。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* Azure Logic Apps がサポートするメール プロバイダー (Office 365 Outlook、Outlook.com など) のメール アカウント。 このメール アカウントは、イベント通知の送信に使われます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>IoT ハブを作成する

新しい IoT ハブは、ポータルから Azure Cloud Shell ターミナルを使用してすぐに作成できます。

1. [Azure portal](https://portal.azure.com) にサインインします。 

1. ページの右上にある [Cloud Shell] ボタンを選択します。

   ![[Cloud Shell] ボタン](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. 次のコマンドを実行して、新しいリソース グループを作成します。

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. 次のコマンドを実行して、IoT ハブを作成します。

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Cloud Shell ターミナルを最小化します。 このチュートリアルで、後でまたシェルを使用します。

## <a name="create-a-logic-app"></a>ロジック アプリを作成します

次に、ロジック アプリを作成し、IoT ハブからの要求を処理する HTTP イベント グリッド トリガーを追加します。 

### <a name="create-a-logic-app-resource"></a>ロジック アプリ リソースを作成する

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]** を選択し、検索ボックスに「logic app」と入力して、Enter キーを押します。 結果から **[Logic App]** を選択します。

   ![ロジック アプリを作成する](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. 次の画面で **[作成]** を選択します。 

1. ロジック アプリにサブスクリプション内で一意の名前を指定し、IoT Hub と同じサブスクリプション、リソース グループ、場所を選びます。 

   ![ロジック アプリを作成するためのフィールド](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. 設定を確認し、 **[作成]** を選択します。

1. リソースが作成されたら、 **[リソースに移動]** を選択します。 

1. Logic Apps デザイナーで、ページを下へスクロールして **[テンプレート]** を表示します。 ロジック アプリを最初から作成できるように、 **[空のロジック アプリ]** を選択します。

### <a name="select-a-trigger"></a>トリガーを選択する

トリガーは、ロジック アプリを開始する特定のイベントです。 このチュートリアルでは、ワークフローを開始するトリガーは、HTTP 経由での要求の受信です。  

1. コネクタとトリガーの検索バーに、「**HTTP**」と入力します。

1. 結果をスクロールし、トリガーとして **[要求 - HTTP 要求の受信時]** を選びます。 

   ![HTTP 要求トリガーを選ぶ](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. **[サンプルのペイロードを使用してスキーマを生成する]** を選びます。 

   ![サンプル ペイロードを使用する](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. "*デバイス接続イベントのスキーマ*" の JSON をテキスト ボックスに貼り付け、 **[完了]** を選択します。

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   このイベントは、デバイスが IoT Hub に接続されると発行されます。

> [!NOTE]
> "**application/json に設定されている Content-Type ヘッダーを要求に忘れずに含めてください**" というポップアップ通知を受け取る場合があります。 この指摘は無視しても安全なので、次のセクションに進みます。 

### <a name="create-an-action"></a>アクションを作成する

アクションは、トリガーがロジック アプリのワークフローを開始した後に発生する手順です。 このチュートリアルのアクションは、メール プロバイダーからのメール通知の送信です。 

1. **[新しいステップ]** を選択します。 **[アクションの選択]** ウィンドウが開きます。

1. **[Outlook]** を探します。

1. 電子メール プロバイダーに基づいて、一致するコネクタを検索して選択します。 このチュートリアルでは、**Outlook.com** を使用します。 他のメール プロバイダーの手順も同様です。 

   ![メール プロバイダーのコネクタを選ぶ](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. **[メールの送信 (V2)]** アクションを選択します。 

1. **[サインイン]** を選択して、ご使用の電子メール アカウントにサインインします。 このアプリがあなたの情報にアクセスすることを許可する場合は、 **[はい]** を選択します。

1. 電子メール テンプレートを作成します。 

   * **送信先**: 通知メールを受信するメール アドレスを入力します。 このチュートリアルでは、テストの目的でアクセスできる電子メール アカウントを使用します。 

   * **Subject**:件名のテキストを入力します。 [件名] テキストボックスをクリックすると、含める動的コンテンツを選択できます。 たとえば、このチュートリアルでは `IoT Hub alert: {eventType}` を使用します。 動的コンテンツが表示されない場合、 **[動的なコンテンツの追加]** ハイパーリンクを選択すると、オンとオフが切り替わります。

   * **本文**:メールのテキストを記述します。 イベント データに基づく動的なコンテンツを含めるには、選択ツールから JSON プロパティを選びます。 動的コンテンツが表示されない場合、 **[本文]** テキスト ボックスの下にある **[動的なコンテンツの追加]** ハイパーリンクを選択します。 必要なフィールドが表示されない場合は、[動的コンテンツ] 画面の *[その他]* をクリックして、前のアクションのフィールドを含めます。

   メール テンプレートは次の例のようになります。

   ![メールの情報を入力する](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Logic Apps デザイナーで **[保存]** を選択します。  

### <a name="copy-the-http-url"></a>HTTP の URL をコピーする

Logic Apps デザイナーを終了する前に、ロジック アプリがトリガーをリッスンする URL をコピーします。 この URL を使って、Event Grid を構成します。 

1. **[HTTP 要求の受信時]** トリガー構成ボックスをクリックして展開します。 

1. **[HTTP POST の URL]** の横にあるコピー ボタンを選んで値をコピーします。 

   ![HTTP POST の URL をコピーする](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. 次のセクションで参照できるように、この URL を保存します。 

## <a name="configure-subscription-for-iot-hub-events"></a>IoT Hub イベント用のサブスクリプションを構成する

このセクションでは、発生したらイベントを発行するように IoT Hub を構成します。 

1. Azure Portal で、お使いの IoT ハブに移動します。 この操作は **[リソース グループ]** を選択し、このチュートリアル用のリソース グループを選択し、リソースのリストから IoT ハブを選択して実行できます。

1. **イベント** を選択します。

   ![Event Grid の詳細を表示する](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. **[イベント サブスクリプション]** を選びます。 

   ![新しいイベント サブスクリプションを作成する](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. 次の値でイベント サブスクリプションを作成します。 

   1. **[イベント サブスクリプションの詳細]** セクションで、次の手順に従います。
      1. イベント サブスクリプションの **名前** を指定します。 
      2. **[イベント スキーマ]** に **[イベント グリッド スキーマ]** を選択します。 
   2. **[トピックの詳細]** セクションで、次の手順に従います。
      1. **[トピックの種類]** が **[IoT Hub]** に設定されていることを確認します。 
      2. **[ソース リソース]** フィールドの値として IoT ハブの名前が設定されていることを確認します。 
      3. 自動的に作成される **システム トピック** の名前を入力します。 システム トピックについては、「[システム トピックの概要](system-topics.md)」を参照してください。
   3. **[イベントの種類]** セクションで、次の手順に従います。
      1. **[イベントの種類のフィルター]** ドロップダウンを選択します。
      1. **[Device Created]\(デバイスの作成完了\)** チェック ボックスと **[Device Deleted]\(デバイスの削除完了\)** チェック ボックスをオフにし、 **[Device Connected]\(デバイスの接続完了\)** チェック ボックスと **[Device Disconnected]\(デバイスの切断完了\)** チェック ボックスのみをオンのままにします。

         ![サブスクリプション イベントの種類を選択する](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. **[エンドポイントの詳細]** セクションで、次の手順に従います。 
       1. **[webhook]** として **[エンドポイントのタイプ]** を選択します。
       2. **[エンドポイントの選択]** をクリックし、ロジック アプリからコピーした URL を貼り付けて、選択内容を確認します。

         ![エンドポイントの URL を選択する](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         終了すると、ペインは次の例のようになります。 

         ![サンプルのイベント サブスクリプション フォーム](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  **［作成］** を選択します

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>新しいデバイスの接続とテレメトリ送信をシミュレートする

Azure CLI を使用して簡単にデバイスの接続をシミュレートし、ロジック アプリをテストします。 

1. [Cloud Shell] ボタンを選択して、再度ターミナルを開きます。

1. 次のコマンドを実行して、シミュレートされたデバイスの ID を作成します。
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. 次のコマンドを実行して、デバイスの IoT ハブへの接続とテレメトリの送信をシミュレートします。

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. シミュレートされたデバイスが IoT Hub に接続すると、"DeviceConnected" イベントを通知するメールが届きます。

1. シミュレーションが完了すると、"DeviceDisconnected" イベントを通知するメールが届きます。 

    ![アラート メールの例](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルでは、Azure サブスクリプションで料金が発生するリソースを使いました。 チュートリアルを試してテストを完了したら、残しておきたくないリソースを無効にするか削除します。 

このチュートリアルで作成したリソースをすべて削除するには、リソース グループを削除します。 

1. **[リソース グループ]** を選択し、このチュートリアル用に作成したリソース グループを選択します。

2. [リソース グループ] ペインで、 **[リソース グループの削除]** を選択します。 リソース グループ名を入力するように求められたら、それを削除できます。 そこに含まれているすべてのリソースも削除されます。

## <a name="next-steps"></a>次のステップ

* 「[Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする](../iot-hub/iot-hub-event-grid.md)」で詳細を確認します。
* [デバイス接続イベントおよびデバイス切断イベントの順序を設定する方法を確認します](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* [Event Grid](overview.md) で他にできることについて確認します。

サポートされている Logic App コネクタの完全な一覧については、 

> [!div class="nextstepaction"]
> [コネクタの概要](/connectors/)に関するページを参照してください。