---
title: "Azure Logic Apps による IoT リモート監視と通知 | Microsoft Docs"
description: "IoT Hub の IoT 温度の監視と、検出された異常に関するメール通知の自動送信には、Azure Logic Apps を使用します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IoT の監視、IoT の通知、IoT の温度の監視"
ms.assetid: 43043067-2e1f-42c9-953d-e2dce8fd86df
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 7a611912ae55eb22103539dbba9f1a06aaa543b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Azure Logic Apps で IoT Hub とメールボックスに接続した状態での IoT リモート監視と通知

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/7.png)

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

1. [Azure Portal](https://portal.azure.com/) で、[**新規作成**]  >  [**エンタープライズ統合**]  >  [**Service Bus**をクリックします。
1. 次の情報を指定します。

   **名前**: Service Bus の名前。

   **価格レベル**: [**基本**]  >  [**選択**] をクリックします。 このチュートリアルでは、[基本] レベルで十分です。

   **[リソース グループ]**: IoT ハブと同じリソース グループを使用します。

   **場所**: IoT Hub で使用するのと同じ場所を使用します。
1. **Create** をクリックしてください。

   ![Azure Portal に Service Bus 名前空間を作成する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Service Bus キューを追加する

1. Service bus 名前空間を開き、**+ キュー**をクリックします。
1. キューの名前を入力して、**作成**をクリックします。
1. Service Bus キューを開き、**共有アクセス ポリシー**  >  **+ 追加**をクリックします。
1. ポリシーの名前を入力し、**管理** をオンにし、**作成**をクリックします。

   ![Azure Portal で Service Bus キューを追加する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>エンドポイントとルーティングの規則を IoT Hub に追加する

### <a name="add-an-endpoint"></a>エンドポイントの追加

1. IoT Hub を開き、[エンドポイント] > [+ 追加] をクリックします。
1. 次の情報を入力します。

   **名前**: エンドポイントの名前。

   **エンドポイントの種類**: [**Service Bus キュー**] を選択します。

   **Service Bus 名前空間**: 作成した名前空間を選択します。

   **Service Bus キュー**: 作成したキューを選択します。
1. **[OK]**をクリックします。

   ![Azure Portal で IoT Hub にエンドポイントを追加する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>ルーティングの規則を追加する

1. IoT Hub で [**ルート**]  >  [**+ 追加**] をクリックします。
1. 次の情報を入力します。

   **名前**: ルーティングの規則の名前。

   **データ ソース**: [**DeviceMessages**] を選択します。

   **エンドポイント**: 作成したエンドポイントを選択します。

   **クエリ文字列**: 「`temperatureAlert = "true"`」と入力します。
1. [ **Save**] をクリックします。

   ![Azure Portal でルーティングの規則を追加する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>ロジック アプリを作成して構成する

### <a name="create-a-logic-app"></a>ロジック アプリを作成します

1. [Azure Portal](https://portal.azure.com/) で、[**新規作成**]  >  [**エンタープライズ統合**]  >  [**ロジック アプリ**をクリックします。
1. 次の情報を入力します。

   **名前**: ロジック アプリの名前。

   **[リソース グループ]**: IoT ハブと同じリソース グループを使用します。

   **場所**: IoT Hub で使用するのと同じ場所を使用します。
1. **Create** をクリックしてください。

### <a name="configure-the-logic-app"></a>ロジック アプリを構成する

1. Logic Apps デザイナーに表示されるロジック アプリを開きます。
1. Logic Apps デザイナーで、[**Blank Logic App**] \(空のロジック アプリ) をクリックします。

   ![Azure Portal で空のロジック アプリを開始する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. [**Service Bus**] をクリックします。

   ![Service Bus を選択して、Azure Portal でロジック アプリの作成を開始する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. [**Service Bus - 1 つ以上のメッセージがキューに届いたとき (オートコンプリート)**] をクリックします。
1. Service Bus の接続を作成します。
   1. 接続名を入力します。
   1. [Service Bus 名前空間] > [Service Bus ポリシー] > [**作成**] をクリックします。

      ![Azure Portal でロジック アプリ用の Service Bus 接続を作成する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. サービス バス接続が作成されたら、[**続行**] をクリックします。
   1. 作成したキューを選択し、[**Maximum message count**] \(最大メッセージ数) に「`175`」と入力する

      ![ロジック アプリで Service Bus 接続の最大メッセージ数を指定する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. [保存] ボタンをクリックして、変更を保存します。

1. SMTP サービス接続を作成します。
   1. **新しいステップ**  >  **アクションの追加**をクリックします。
   1. 「`SMTP`」と入力し、検索結果の **SMTP サービス**をクリックして、**SMTP - 電子メールの送信**をクリックします。

      ![Azure Portal でロジック アプリの SMTP 接続を作成する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. メールボックスの SMTP 情報を入力し、**作成**をクリックします。

      ![Azure Portal でロジック アプリの SMTP 接続情報を入力する](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      [Hotmail/Outlook.com](https://support.office.com/en-us/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970)、[Gmail](https://support.google.com/a/answer/176600?hl=en)、[Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html) の SMTP 情報を入手します。
   1. [**差出人**] と [**宛先**] にメール アドレス、[**件名**] と [**本文**] に `High temperature detected` を入力します。
   1. [ **Save**] をクリックします。

ロジック アプリは、保存時も動作しています。

## <a name="test-the-logic-app"></a>ロジック アプリをテストする

1. [[Connect ESP8266 to Azure IoT Hub]](iot-hub-arduino-huzzah-esp8266-get-started.md) \(Azure IoT Hub に ESP8266 を接続する) で、デバイスにデプロイするクライアント アプリケーションを開始します。
1. SensorTag の周囲でろうそくに火を付けるなど、SensorTag の周囲の環境の温度を 30 °C を超えるまで上昇させます。
1. これにより、ロジック アプリからメール通知が送信されます。

   > [!NOTE]
   > メール サービス プロバイダーは、メールの送信者を送信者 ID で確認する必要が生じる場合があります。

## <a name="next-steps"></a>次のステップ

温度の監視と通知のメールボックスと IoT Hub を接続するロジック アプリが正常に作成されました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
