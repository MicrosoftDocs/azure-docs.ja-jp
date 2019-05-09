---
title: Azure Logic Apps による IoT リモート監視と通知 | Microsoft Docs
description: IoT Hub の IoT 温度の監視と、検出された異常に関するメール通知の自動送信には、Azure Logic Apps を使用します。
author: robinsh
keywords: IoT の監視、IoT の通知、IoT の温度の監視
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 5d5b1d1579600767153fcf5ad751e1224631d611
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262518"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Azure Logic Apps で IoT Hub とメールボックスに接続した状態での IoT リモート監視と通知

![エンド ツー エンド ダイアグラム](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Azure Logic Apps は、一連の手順として、プロセスを自動化する方法が用意されています。 ロジック アプリは、さまざまなサービスやプロトコル経由で接続できます。 ”アカウントが追加されたとき” などのトリガーによって開始され、その後に、”プッシュ通知の送信” などのアクションが組み合わされます。 この機能により、Logic Apps は、異常を常に警戒するなど、他の使用シナリオでも、IoT 監視のための完全な IoT ソリューションになります。

## <a name="what-you-learn"></a>学習内容

温度の監視と通知のメールボックスと IoT Hub を接続するロジック アプリを作成する方法を学びます。 温度が 30℃を超えると、クライアント アプリケーションは、IoT Hub に送信するメッセージに `temperatureAlert = "true"` とマークします。 このメッセージにより、ロジック アプリがトリガーされ、メール通知が送信されます。

## <a name="what-you-do"></a>作業内容

* Service Bus 名前空間を作成して、キューを追加します。
* エンドポイントとルーティングの規則を IoT Hub に追加します。
* ロジック アプリを作成、構成、およびをテストします。

## <a name="what-you-need"></a>必要なもの

* 次の要件に対応するために、[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md)に関するチュートリアルを完了しておきます。

  * 有効な Azure サブスクリプション
  * サブスクリプションの Azure IoT Hub。
  * Azure IoT Hub にメッセージを送信するクライアント アプリケーション。

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Service Bus 名前空間を作成してキューを追加する

### <a name="create-a-service-bus-namespace"></a>Service Bus 名前空間の作成を作成する

1. [Azure portal](https://portal.azure.com/) で、**[リソースの作成]** > **[エンタープライズ統合]** > **[Service Bus]** の順に選択します。

2. 次の情報を指定します。

   **[名前]**:サービス バスの名前。

   **価格レベル**:**[Basic]** > **[選択]** の順に選択します。 このチュートリアルでは、[基本] レベルで十分です。

   **[リソース グループ]**:IoT ハブと同じリソース グループを使用します。

   **[場所]**:IoT ハブで使用するものと同じ場所を使用します。

3. **作成** を選択します。

   ![Azure Portal に Service Bus 名前空間を作成する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Service Bus キューを追加する

1. サービス バスの名前空間を開き、**[+ キュー]** を選択します。

1. キューの名前を入力して、**[作成]** を選択します。

1. サービス バス キューを開き、**[共有アクセス ポリシー]** > **[+ 追加]** の順に選択します。

1. ポリシーの名前を入力し、**[管理]** をオンにし、**[作成]** を選択します。

   ![Azure Portal で Service Bus キューを追加する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-query-to-your-iot-hub"></a>エンドポイントとルーティング クエリを IoT ハブに追加する

エンドポイントとルーティング クエリを IoT ハブに追加します。

### <a name="add-an-endpoint"></a>エンドポイントの追加

1. IoT ハブを開き、**[エンドポイント]** > **[+ 追加]** の順に選択します。

1. 次の情報を入力します。

   **[名前]**:エンドポイントの名前。

   **[エンドポイントの種類]**:**[Service Bus キュー]** を選択します。

   **[Service Bus 名前空間]**:作成した名前空間を選択します。

   **Service Bus キュー**:作成したキューを選択します。

3. **[OK]** を選択します。

   ![Azure Portal で IoT Hub にエンドポイントを追加する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>ルーティングの規則を追加する

1. IoT ハブで **[ルート]** > **[+ 追加]** の順に選択します。

2. 次の情報を入力します。

   **[名前]**:ルーティング規則の名前。

   **データ ソース**:**[DeviceMessages]** を選択します。

   **エンドポイント**: 作成したエンドポイントを選択します。

   **クエリ文字列**:「 `temperatureAlert = "true"` 」を入力します。

3. **[保存]** を選択します。

   ![Azure Portal でルーティングの規則を追加する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>ロジック アプリを作成して構成する

次に、ロジック アプリを作成して構成します。

### <a name="create-a-logic-app"></a>ロジック アプリを作成します

1. [Azure portal](https://portal.azure.com/) で、**[リソースの作成]** > **[エンタープライズ統合]** > **[ロジック アプリ]** の順に選択します。

2. 次の情報を入力します。

   **[名前]**:ロジック アプリの名前。

   **[リソース グループ]**:IoT ハブと同じリソース グループを使用します。

   **[場所]**:IoT ハブで使用するものと同じ場所を使用します。

3. **作成** を選択します。

### <a name="configure-the-logic-app"></a>ロジック アプリを構成する

1. Logic Apps デザイナーに表示されるロジック アプリを開きます。

2. Logic Apps デザイナーで、**[空のロジック アプリ]** を選択します。

   ![Azure Portal で空のロジック アプリを開始する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

3. **[Service Bus]** を選びます。

   ![Service Bus を選択して、Azure Portal でロジック アプリの作成を開始する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

4. **[Service Bus - 1 つ以上のメッセージがキューに届いたとき (オートコンプリート)]** を選択します。

5. Service Bus の接続を作成します。

   1. 接続名を入力します。

   2. [サービス バス名前空間] > [Service Bus ポリシー] > **[作成]** の順に選択します。

      ![Azure Portal でロジック アプリ用の Service Bus 接続を作成する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   3. サービス バス接続が作成されたら、**[続行]** を選択します。

   4. 作成したキューを選択し、**[最大メッセージ数]** に「`175`」と入力します。

      ![ロジック アプリで Service Bus 接続の最大メッセージ数を指定する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   5. [保存] ボタンを選択して変更を保存します。

6. SMTP サービス接続を作成します。

   1. **[新しいステップ]** > **[アクションの追加]** の順に選択します。

   2. 「`SMTP`」と入力し、検索結果の **[SMTP サービス]** を選択して **[SMTP - 電子メールの送信]** を選択します。

      ![Azure Portal でロジック アプリの SMTP 接続を作成する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   3. メールボックスの SMTP 情報を入力し、**[作成]** を選択します。

      ![Azure Portal でロジック アプリの SMTP 接続情報を入力する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970)、[Gmail](https://support.google.com/a/answer/176600?hl=en)、[Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html) の SMTP 情報を入手します。

   4. **[差出人]** と **[宛先]** にメール アドレス、**[件名]** と **[本文]** に `High temperature detected` を入力します。

   5. **[保存]** を選択します。

ロジック アプリは、保存時も動作しています。

## <a name="test-the-logic-app"></a>ロジック アプリをテストする

1. [[Connect ESP8266 to Azure IoT Hub]](iot-hub-arduino-huzzah-esp8266-get-started.md) \(Azure IoT Hub に ESP8266 を接続する) で、デバイスにデプロイするクライアント アプリケーションを開始します。

2. SensorTag の周囲でろうそくに火を付けるなど、SensorTag の周囲の環境の温度を 30 °C を超えるまで上昇させます。

3. これにより、ロジック アプリからメール通知が送信されます。

   > [!NOTE]
   > メール サービス プロバイダーは、メールの送信者を送信者 ID で確認する必要が生じる場合があります。

## <a name="next-steps"></a>次の手順

温度の監視と通知のメールボックスと IoT Hub を接続するロジック アプリが正常に作成されました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
