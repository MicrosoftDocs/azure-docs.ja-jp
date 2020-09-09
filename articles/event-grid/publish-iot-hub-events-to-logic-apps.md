---
title: チュートリアル - IoT Hub のイベントを使用して Azure Logic Apps をトリガーする
description: このチュートリアルでは、Azure Event Grid のイベント ルーティング サービスを使い、IoT Hub のイベントに基づいて Azure Logic Apps のアクションを実行する自動化されたプロセスの作成方法を紹介します。
services: iot-hub, event-grid
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 07/07/2020
ms.author: robinsh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 35359c63b79d9eea6f8f6ad688bd040428a39eb8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503448"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>チュートリアル:Event Grid および Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する

Azure Event Grid を使うと、ダウンストリームのビジネス アプリケーションのアクションをトリガーすることによって、IoT Hub のイベントに対応することができます。

この記事では、IoT Hub と Event Grid を使うサンプルを構成する手順について説明します。 最終的に、デバイスが IoT Hub に追加されるたびに通知メールを送信するように Azure Logic Apps が設定されます。 

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション サブスクリプションがない場合は、[無料の Azure アカウントを作成](https://azure.microsoft.com/pricing/free-trial/)できます。

* Azure Logic Apps がサポートするメール プロバイダー (Office 365 Outlook、Outlook.com、Gmail など) のメール アカウント。 このメール アカウントは、イベント通知の送信に使われます。 サポートされている Logic App コネクタの完全な一覧については、[コネクタの概要](/connectors/)に関するページを参照してください。

  > [!IMPORTANT]
  > Gmail を使用する前に、G-Suite ビジネス アカウント (カスタム ドメインを持つメール アドレス) または Gmail コンシューマー アカウント (@gmail.com または @googlemail.com のメール アドレス) があるかどうかを確認してください。 ロジック アプリで制限なしに Gmail コネクタを他のコネクタと共に使用できるのは、G Suite ビジネス アカウントだけです。 Gmail コンシューマー アカウントを持っている場合は、Google によって承認された特定のサービスのみで Gmail コネクタを使用できるほか、[認証に使用する Google クライアント アプリを作成する](/connectors/gmail/#authentication-and-bring-your-own-application)ことができます。 詳細については、「[Azure Logic Apps での Google コネクタのデータ セキュリティとプライバシー ポリシー](../connectors/connectors-google-data-security-privacy-policy.md)」を参照してください。

* Azure の IoT Hub。 まだ作成していない場合は、「[IoT Hub の概要](../iot-hub/quickstart-send-telemetry-dotnet.md)」のチュートリアルをご覧ください。

## <a name="create-a-logic-app"></a>ロジック アプリを作成します

最初にロジック アプリを作成し、仮想マシンのリソース グループを監視する Event Grid トリガーを追加します。 

### <a name="create-a-logic-app-resource"></a>ロジック アプリ リソースを作成する

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]** を選択し、検索ボックスに「logic app」と入力して、Enter キーを押します。 結果から **[Logic App]** を選択します。

   ![ロジック アプリを作成する](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. 次の画面で **[作成]** を選択します。 

1. ロジック アプリにサブスクリプション内で一意の名前を指定し、IoT Hub と同じサブスクリプション、リソース グループ、場所を選びます。 

   ![ロジック アプリを作成するためのフィールド](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. **［作成］** を選択します

1. リソースが作成されたら、ロジック アプリに移動します。 これを実行するには、 **[リソース グループ]** を選択し、このチュートリアル用に作成したリソース グループを選択します。 次に、リソースの一覧でロジック アプリを見つけて選択します。 

1. Logic Apps デザイナーで、ページを下へスクロールして **[テンプレート]** を表示します。 ロジック アプリを最初から作成できるように、 **[空のロジック アプリ]** を選択します。

### <a name="select-a-trigger"></a>トリガーを選択する

トリガーは、ロジック アプリを開始する特定のイベントです。 このチュートリアルでは、ワークフローを開始するトリガーは、HTTP 経由での要求の受信です。  

1. コネクタとトリガーの検索バーに、「**HTTP**」と入力します。

1. トリガーとして **[要求 - HTTP 要求の受信時]** を選びます。 

   ![HTTP 要求トリガーを選ぶ](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. **[サンプルのペイロードを使用してスキーマを生成する]** を選びます。 

   ![HTTP 要求トリガーを選ぶ](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. 次のサンプル JSON コードをテキスト ボックスに貼り付けて、 **[完了]** を選びます。

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. "**application/json に設定されている Content-Type ヘッダーを要求に忘れずに含めてください**" というポップアップ通知を受け取る場合があります。 この指摘は無視しても安全なので、次のセクションに進みます。 

### <a name="create-an-action"></a>アクションを作成する

アクションは、トリガーがロジック アプリのワークフローを開始した後に発生する手順です。 このチュートリアルのアクションは、メール プロバイダーからのメール通知の送信です。 

1. **[新しいステップ]** を選択します。 **[アクションの選択]** ウィンドウが開きます。

1. **[電子メール]** を検索します。

1. 電子メール プロバイダーに基づいて、一致するコネクタを検索して選択します。 このチュートリアルでは、**Office 365 Outlook** を使います。 他のメール プロバイダーの手順も同様です。 

   ![メール プロバイダーのコネクタを選ぶ](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. **[電子メールの送信]** アクションを選びます。 

1. メッセージに従ってメール アカウントにサインインします。 

1. メール テンプレートを作成します。 

   * **[宛先]** :通知メールを受信するメール アドレスを入力します。 このチュートリアルでは、テスト用にアクセスできるメール アカウントを使います。 

   * **Subject**:件名のテキストを入力します。 [件名] テキストボックスをクリックすると、含める動的コンテンツを選択できます。 たとえば、このチュートリアルでは `IoT Hub alert: {event Type}` を使用します。 動的コンテンツが表示されない場合、 **[動的なコンテンツの追加]** ハイパーリンクを選択すると、オンとオフが切り替わります。

   * **本文**:メールのテキストを記述します。 イベント データに基づく動的なコンテンツを含めるには、選択ツールから JSON プロパティを選びます。 動的コンテンツが表示されない場合、 **[本文]** テキスト ボックスの下にある **[動的なコンテンツの追加]** ハイパーリンクを選択します。 必要なフィールドが表示されない場合は、[動的コンテンツ] 画面の *[その他]* をクリックして、前のアクションのフィールドを含めます。

   メール テンプレートは次の例のようになります。

   ![メールの情報を入力する](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. ロジック アプリを保存します。 

### <a name="copy-the-http-url"></a>HTTP の URL をコピーする

Logic Apps デザイナーを終了する前に、ロジック アプリがトリガーをリッスンする URL をコピーします。 この URL を使って、Event Grid を構成します。 

1. **[HTTP 要求の受信時]** トリガー構成ボックスをクリックして展開します。 

1. **[HTTP POST の URL]** の横にあるコピー ボタンを選んで値をコピーします。 

   ![HTTP POST の URL をコピーする](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. 次のセクションで参照できるように、この URL を保存します。 

## <a name="configure-subscription-for-iot-hub-events"></a>IoT Hub イベント用のサブスクリプションを構成する

このセクションでは、発生したらイベントを発行するように IoT Hub を構成します。 

1. Azure Portal で、お使いの IoT ハブに移動します。 この操作は **[リソース グループ]** を選択し、このチュートリアル用のリソース グループを選択し、リソースのリストから IoT ハブを選択して実行できます。

2. **イベント**を選択します。

   ![Event Grid の詳細を表示する](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. **[イベント サブスクリプション]** を選びます。 

   ![新しいイベント サブスクリプションを作成する](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. 次の値でイベント サブスクリプションを作成します。 

    1. **[イベント サブスクリプションの詳細]** セクションで、次の作業を行います。
        1. イベント サブスクリプションの**名前**を指定します。 
        2. **[イベント スキーマ]** に **[イベント グリッド スキーマ]** を選択します。 
   2. **[トピックの詳細]** セクションで、次の作業を行います。
       1. **[トピックの種類]** が **[IoT Hub]** に設定されていることを確認します。 
       2. **[ソース リソース]** フィールドの値として IoT ハブの名前が設定されていることを確認します。 
       3. 自動的に作成される**システム トピック**の名前を入力します。 システム トピックについては、「[システム トピックの概要](system-topics.md)」を参照してください。
   3. **[イベントの種類]** セクションで、次の作業を行います。 
        1. **[イベントの種類のフィルター]** で、 **[デバイスの作成完了]** を除くすべての選択項目をオフにします。

           ![サブスクリプション イベントの種類](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   4. **[エンドポイントの詳細]** セクションで、次の作業を行います。 
       1. **[webhook]** として **[エンドポイントのタイプ]** を選択します。
       2. **[エンドポイントの選択]** をクリックし、ロジック アプリからコピーした URL を貼り付けて、選択内容を確認します。

         ![エンドポイントの URL を選択する](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         終了すると、ペインは次の例のようになります。 

        ![サンプルのイベント サブスクリプション フォーム](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. 以上でイベント サブスクリプションを保存すると、IoT hub でデバイスが作成されるたびに通知を受け取るようになります。 しかし、このチュートリアルでは、特定のデバイスでフィルター処理をするためにオプションのフィールドを追加します。 ペインの上部にある **[フィルター]** を選択します。

6. **[Add new filter]\(新しいフィルターの追加\)** を選択します。 フィールドに次の値を入力します。

   * **[キー]** :[`Subject`] を選択します。

   * **オペレーター**:[`String begins with`] を選択します。

   * **値**: ビル 1 でのデバイス イベントでフィルター処理するため、「`devices/Building1_`」と入力します。
  
   次の値を持つ別のフィルターを追加します。

   * **[キー]** :[`Subject`] を選択します。

   * **オペレーター**:[`String ends with`] を選択します。

   * **値**: 温度に関するデバイス イベントでフィルター処理するため、「`_Temperature`」と入力します。

   イベント サブスクリプションの **[フィルター]** タブは、次の図のようになります。

   ![イベント サブスクリプションへのフィルターの追加](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. **[作成]** を選び、イベント サブスクリプションを保存します。

## <a name="create-a-new-device"></a>新しいデバイスを作成する

新しいデバイスを作成してイベント通知メールをトリガーすることで、ロジック アプリをテストします。 

1. IoT Hub から、 **[IoT Devices]\(IoT デバイス\)** を選びます。 

2. **[新規]** を選択します。

3. **[デバイス ID]** に「`Building1_Floor1_Room1_Light`」と入力します。

4. **[保存]** を選択します。 

5. 異なるデバイス ID で複数のデバイスを追加し、イベント サブスクリプション フィルターをテストできます。 次の例を試してください。 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   4 つの例を追加した場合、IoT デバイスの一覧は次の図のようになります。

   ![IoT Hub のデバイス一覧](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. IoT Hub にデバイスをいくつか追加した後、ロジック アプリをトリガーしたものをメールで確認します。 

## <a name="use-the-azure-cli"></a>Azure CLI の使用

Azure Portal を使う代わりに、Azure CLI を使って IoT Hub の手順を行うことができます。 詳細については、[イベント サブスクリプションの作成](/cli/azure/eventgrid/event-subscription)と[IoT デバイスの作成](/cli/azure/ext/azure-iot/iot/hub/device-identity)に関する Azure CLI のページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルでは、Azure サブスクリプションで料金が発生するリソースを使いました。 チュートリアルを試してテストを完了したら、残しておきたくないリソースを無効にするか削除します。 

このチュートリアルで作成したリソースをすべて削除するには、リソース グループを削除します。 

1. **[リソース グループ]** を選択し、このチュートリアル用に作成したリソース グループを選択します。

2. [リソース グループ] ペインで、 **[リソース グループの削除]** を選択します。 リソース グループ名を入力するように求められたら、それを削除できます。 そこに含まれているすべてのリソースも削除されます。

すべてのリソースを削除することを望まない場合、リソースを 1 つずつ管理できます。 

アプリ ロジックでの作業を失いたくない場合は、削除ではなく無効にします。 

1. ロジック アプリに移動します。

2. **[概要]** ブレードで、 **[削除]** または **[無効]** を選びます。 

各サブスクリプションで使うことができる無料 IoT Hub は 1 つです。 このチュートリアル用に無料のハブを作成した場合は、課金されないように削除する必要はありません。

1. IoT Hub に移動します。 

2. **[概要]** ブレードで **[削除]** を選びます。 

IoT Hub を残しておく場合でも、作成したイベント サブスクリプションを削除できます。 

1. IoT Hub で **[イベント グリッド]** を選びます。

2. 削除するイベント サブスクリプションを選びます。 

3. **[削除]** を選択します。 

## <a name="next-steps"></a>次のステップ

* 「[Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする](../iot-hub/iot-hub-event-grid.md)」で詳細を確認します。
* [デバイス接続イベントおよびデバイス切断イベントの順序を設定する方法を確認します](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* [Event Grid](overview.md) で他にできることについて確認します。
