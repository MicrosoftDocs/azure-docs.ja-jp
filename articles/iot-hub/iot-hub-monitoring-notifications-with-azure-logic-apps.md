---
title: Azure Logic Apps による IoT リモート監視と通知 | Microsoft Docs
description: IoT Hub の IoT 温度の監視と、検出された異常に関するメール通知の自動送信には、Azure Logic Apps を使用します。
author: robinsh
keywords: IoT の監視、IoT の通知、IoT の温度の監視
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: cd14ff0688f4230aeedac748ca4b32609bdd2938
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92490324"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Azure Logic Apps で IoT Hub とメールボックスに接続した状態での IoT リモート監視と通知

![エンド ツー エンド ダイアグラム](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml) は、ワークフローをオンプレミスとクラウド サービス、1 つ以上のエンタープライズ、およびさまざまなプロトコルにわたって調整するのに役立ちます。 ロジック アプリはトリガーで始まり、その後に、条件や反復子などの組み込みのコントロールを使用して順序付けることができる 1 つ以上のアクションが実行されます。 この柔軟性により、Logic Apps は IoT の監視シナリオのための理想的な IoT ソリューションになります。 たとえば、デバイスからのテレメトリ データが IoT Hub エンドポイントに到着したら、そのデータを Azure Storage BLOB に格納するためにロジック アプリ ワークフローを開始したり、データの異常を警告するために電子メール アラートを送信したり、デバイスから障害が報告された場合は技術者の訪問をスケジュールしたりすることができます。

## <a name="what-you-learn"></a>学習内容

温度の監視と通知のメールボックスと IoT Hub を接続するロジック アプリを作成する方法を学びます。

デバイス上で実行されているクライアント コードは、IoT ハブに送信するすべてのテレメトリ メッセージ上にアプリケーション プロパティ `temperatureAlert` を設定します。 30 C を超える温度を検出すると、クライアント コードはこのプロパティを `true` に、それ以外の場合は `false` に設定します。

IoT ハブに到着したメッセージは次のような内容です。本文にテレメトリ データが含まれ、アプリケーション プロパティに `temperatureAlert` プロパティが含まれています (システム プロパティは示されていません)。

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

IoT Hub メッセージ形式の詳細については、「[IoT Hub メッセージを作成し、読み取る](iot-hub-devguide-messages-construct.md)」を参照してください。

このトピックでは、`temperatureAlert` プロパティが `true` であるメッセージを Service Bus エンドポイントに送信するように IoT ハブ上のルーティングを設定します。 次に、Service Bus エンドポイントに到着したメッセージでトリガーされ、ユーザーに電子メール通知を送信するロジック アプリを設定します。

## <a name="what-you-do"></a>作業内容

* Service Bus 名前空間を作成し、それに Service Bus キューを追加します。
* 温度アラートを含むメッセージを Service Bus キューにルーティングするために、IoT ハブにカスタム エンドポイントとルーティング規則を追加します。
* Service Bus キューからのメッセージを消費し、目的の受信者に通知電子メールを送信するロジック アプリを作成、構成、およびテストします。

## <a name="what-you-need"></a>必要なもの

* [Raspberry Pi オンライン シミュレーター](iot-hub-raspberry-pi-web-simulator-get-started.md)のチュートリアルまたはいずれかのデバイス チュートリアル ([Node.js での Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md) に関するチュートリアルなど) が完了していること。 次の要件について取り上げられています。

  * 有効な Azure サブスクリプション
  * サブスクリプションの Azure IoT Hub。
  * Azure IoT Hub にテレメトリ メッセージを送信する、デバイス上で実行されているクライアント アプリケーション。

## <a name="create-service-bus-namespace-and-queue"></a>Service Bus の名前空間とキューを作成する

Service Bus の名前空間とキューを作成します。 このトピックの後の方では、温度アラートを含むメッセージを Service Bus キューに転送するための IoT ハブ内のルーティング規則を作成します。そこで、これらのメッセージはロジック アプリによって選択され、通知電子メールを送信するためにそのアプリをトリガーします。

### <a name="create-a-service-bus-namespace"></a>Service Bus 名前空間を作成する

1. [Azure Portal](https://portal.azure.com/) で、 **[+ リソースの作成]**  >  **[統合]**  >  **[Service Bus]** の順に選択します。

1. **[名前空間の作成]** ウィンドウで、次の情報を指定します。

   **Name**:Service Bus 名前空間の名前。 この名前空間は Azure 全体で一意である必要があります。

   **価格レベル**:ドロップダウン リストから **[Basic]** を選択します。 このチュートリアルでは、[基本] レベルで十分です。

   **[リソース グループ]** :IoT ハブと同じリソース グループを使用します。

   **[場所]** :IoT ハブで使用するものと同じ場所を使用します。

   ![Azure Portal に Service Bus 名前空間を作成する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. **［作成］** を選択します デプロイが完了するまで待ってから次の手順に移動してください。

### <a name="add-a-service-bus-queue-to-the-namespace"></a>名前空間に Service Bus キューを追加する

1. Service Bus 名前空間を開きます。 Service Bus 名前空間に移動するための最も簡単な方法として、リソース ウィンドウから **[リソース グループ]** を選択し、リソース グループを選択した後、リソースの一覧から Service Bus 名前空間を選択します。

1. **[Service Bus 名前空間]** ウィンドウで、 **[+ キュー]** を選択します。

1. キューの名前を入力して、 **[作成]** を選択します。 キューが正常に作成されると、 **[キューの作成]** ウィンドウが閉じます。

   ![Azure Portal で Service Bus キューを追加する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. **[Service Bus 名前空間]** ウィンドウに戻り、 **[エンティティ]** で **[キュー]** を選択します。 一覧から Service Bus キューを開き、 **[共有アクセス ポリシー]**  >  **[+ 追加]** の順に選択します。

1. ポリシーの名前を入力し、 **[管理]** をオンにし、 **[作成]** を選択します。

   ![Azure Portal で Service Bus キュー ポリシーを追加する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>IoT ハブにカスタム エンドポイントとルーティング規則を追加する

IoT ハブに Service Bus キューのカスタム エンドポイントを追加し、温度アラートを含むメッセージをそのエンドポイントに転送するためのメッセージ ルーティング規則を作成します。そこで、これらのメッセージはロジック アプリによって選択されます。 このルーティング規則では、ルーティング クエリ `temperatureAlert = "true"` を使用して、デバイス上で実行されているクライアント コードによって設定された `temperatureAlert` アプリケーション プロパティの値に基づいてメッセージを転送します。 詳細については、「[メッセージ プロパティに基づいたメッセージ ルーティング クエリ](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties)」を参照してください。

### <a name="add-a-custom-endpoint"></a>カスタム エンドポイントを追加する

1. IoT ハブを開きます。 IoT ハブに移動するための最も簡単な方法として、リソース ウィンドウから **[リソース グループ]** を選択し、リソース グループを選択した後、リソースの一覧から IoT ハブを選択します。

1. **[メッセージング]** で、 **[メッセージ ルーティング]** を選択します。 **[メッセージ ルーティング]** ウィンドウで、 **[カスタム エンドポイント]** タブを選択して **[+ 追加]** を選択します。 ドロップダウン リストから、 **[Service bus queue] (Service Bus キュー)** を選択します。

   ![[Service Bus キュー] オプションが強調表示されているスクリーンショット。](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. **[Add a service bus endpoint] (Service Bus エンドポイントを追加する)** ウィンドウで、次の情報を入力します。

   **[エンドポイント名]** : エンドポイントの名前。

   **[Service Bus 名前空間]** : 作成した名前空間を選択します。

   **[Service bus queue] (Service Bus キュー)** : 作成したキューを選択します。

   ![Azure Portal で IoT Hub にエンドポイントを追加する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. **［作成］** を選択します エンドポイントが正常に作成されたら、次の手順に進みます。

### <a name="add-a-routing-rule"></a>ルーティングの規則を追加する

1. **[メッセージ ルーティング]** ウィンドウに戻り、 **[ルート]** タブを選択して **[+ 追加]** を選択します。

1. **[ルートを追加する]** ウィンドウで、次の情報を入力します。

   **Name**:ルーティング規則の名前。

   **エンドポイント**: 作成したエンドポイントを選択します。

   **データ ソース**: **[Device Telemetry Messages] (デバイス テレメトリ メッセージ)** を選択します。

   **ルーティング クエリ**:「`temperatureAlert = "true"`」と入力します。

   ![Azure Portal でルーティングの規則を追加する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. **[保存]** を選択します。 **[メッセージ ルーティング]** ウィンドウを閉じることができます。

## <a name="create-and-configure-a-logic-app"></a>ロジック アプリを作成して構成する

前のセクションでは、温度アラートを含むメッセージを Service Bus キューにルーティングするように IoT ハブを設定しました。 ここでは、Service Bus キューを監視し、そのキューにメッセージが追加された場合は常に電子メール通知を送信するロジック アプリを設定します。

### <a name="create-a-logic-app"></a>ロジック アプリを作成します

1. **[リソースの作成]**  >  **[統合]**  >  **[ロジック アプリ]** の順に選択します。

1. 次の情報を入力します。

   **Name**:ロジック アプリの名前。

   **[リソース グループ]** :IoT ハブと同じリソース グループを使用します。

   **[場所]** :IoT ハブで使用するものと同じ場所を使用します。

   ![Azure Portal でロジック アプリを作成する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. **［作成］** を選択します

### <a name="configure-the-logic-app-trigger"></a>ロジック アプリのトリガーを構成する

1. ロジック アプリを開きます。 ロジック アプリに移動するための最も簡単な方法として、リソース ウィンドウから **[リソース グループ]** を選択し、リソース グループを選択した後、リソースの一覧からロジック アプリを選択します。 ロジック アプリを選択すると、Logic Apps デザイナーが開きます。

1. Logic Apps デザイナーで、 **[テンプレート]** まで下へスクロールし、 **[空のロジック アプリ]** を選択します。

   ![Azure Portal で空のロジック アプリを開始する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. **[すべて]** タブを選択して **[Service Bus]** を選択します。

   ![Service Bus を選択して、Azure Portal でロジック アプリの作成を開始する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. **[トリガー]** で、 **[キューで 1 つ以上のメッセージを受信したとき (オート コンプリート)]** を選択します。

   ![Azure Portal でロジック アプリのトリガーを選択する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Service Bus の接続を作成します。
   1. 接続名を入力し、一覧から Service Bus 名前空間を選択します。 次の画面が開きます。

      ![[キューで 1 つ以上のメッセージを受信したとき (オート コンプリート)] オプションが強調表示されているスクリーンショット。](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Service Bus ポリシー (RootManageSharedAccessKey) を選択します。 次に、 **[作成]** を選択します。

      ![Azure Portal でロジック アプリ用の Service Bus 接続を作成する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. 最後の画面の **[キュー名]** で、ドロップダウンから前に作成したキューを選択します。 **[最大メッセージ数]** に「`175`」と入力します。

      ![ロジック アプリで Service Bus 接続の最大メッセージ数を指定する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Logic Apps デザイナーの上部にあるメニューで **[保存]** を選択して、変更内容を保存します。

### <a name="configure-the-logic-app-action"></a>ロジック アプリのアクションを構成する

1. SMTP サービス接続を作成します。

   1. **[新しいステップ]** を選択します。 **[アクションを選択する]** で、 **[すべて]** タブを選択します。

   1. 検索ボックスに「`smtp`」と入力し、検索結果の **[SMTP]** サービスを選択して **[電子メールの送信]** を選択します。

      ![Azure Portal でロジック アプリの SMTP 接続を作成する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. メールボックスの SMTP 情報を入力し、 **[作成]** を選択します。

      ![Azure Portal でロジック アプリの SMTP 接続情報を入力する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970)、[Gmail](https://support.google.com/a/answer/176600?hl=en)、[Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html) の SMTP 情報を入手します。

      > [!NOTE]
      > 接続を確立するために TLS/SSL を無効にすることが必要になる場合があります。 このとき、接続が確立された後に TLS を再び有効にする場合は、このセクションの最後にある省略可能な手順を参照してください。

   1. **[電子メールの送信]** 手順の **[新しいパラメーターの追加]** ドロップダウンから、 **[From] (差出人)** 、 **[To] (宛先)** 、 **[件名]** 、および **[本文]** を選択します。 画面上の任意の場所をクリックまたはタップして選択ボックスを閉じます。

      ![SMTP 接続の電子メール フィールドを選択する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. **[差出人]** と **[宛先]** にメール アドレス、 **[件名]** と **[本文]** に `High temperature detected` を入力します。 **[Add dynamic content from the apps and connectors used in this flow] (このフローで使用されているアプリやコネクタから動的コンテンツを追加する)** ダイアログが開いた場合は、 **[非表示]** を選択してそれを閉じます。 このチュートリアルでは、動的コンテンツを使用しません。

      ![SMTP 接続の電子メール フィールドに入力する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. **[保存]** を選択して SMTP 接続を保存します。

1. (省略可能) 電子メール プロバイダーとの接続を確立するために TLS を無効にする必要があり、ここで再び有効にする場合は、次の手順に従います。

   1. **[ロジック アプリ]** ウィンドウの **[開発ツール]** で、 **[API 接続]** を選択します。

   1. API 接続の一覧から、SMTP 接続を選択します。

   1. **[smtp API Connection] (SMTP API 接続)** ウィンドウの **[全般]** で、 **[API 接続の編集]** を選択します。

   1. **[API 接続の編集]** ウィンドウで、 **[SSL を有効にしますか?]** を選択し、電子メール アカウントのパスワードを再入力して **[保存]** を選択します。

      ![Azure Portal のロジック アプリで SMTP API 接続を編集する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

これで、ロジック アプリは Service Bus キューからの温度アラートを処理し、電子メール アカウントに通知を送信する準備ができました。

## <a name="test-the-logic-app"></a>ロジック アプリをテストする

1. デバイス上でクライアント アプリケーションを起動します。

1. 物理デバイスを使用している場合は、温度が 30 度 C を超えるまで、熱源を慎重に熱センサーに近付けます。オンライン シミュレーターを使用している場合は、クライアント コードによって、30 C を超えるテレメトリ メッセージがランダムに出力されます。

1. これにより、ロジック アプリによって送信された電子メール通知を受信し始めます。

   > [!NOTE]
   > メール サービス プロバイダーは、メールの送信者を送信者 ID で確認する必要が生じる場合があります。

## <a name="next-steps"></a>次のステップ

温度の監視と通知のメールボックスと IoT Hub を接続するロジック アプリが正常に作成されました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]