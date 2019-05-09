---
title: Azure IoT Hub から取得したリアルタイム センサー データの視覚化 – Web Apps | Microsoft Docs
description: Microsoft Azure App Service の Web Apps 機能を使用して、センサーから収集された気温と湿度のデータを視覚化し、IoT ハブに送信します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: b870c432ea8021d9eeb14eec85ff9912b96ddc96
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270882"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Azure App Service の Web Apps 機能を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する

![エンド ツー エンド ダイアグラム](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学習内容

このチュートリアルでは、Web アプリでホストされた Web アプリケーションを実行して、IoT ハブに届いたリアルタイム センサー データを視覚化する方法について説明します。 Power BI を使用して IoT ハブのデータを視覚化したい場合は、[Power BI を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する方法](iot-hub-live-data-visualization-in-power-bi.md)に関するページを参照してください。

## <a name="what-you-do"></a>作業内容

* Azure ポータルで Web アプリを作成する。
* コンシューマー グループを追加して、データにアクセスできるよう IoT ハブを準備する。
* IoT ハブからセンサー データを読み取る Web アプリを構成する。
* Web アプリでホストされる Web アプリケーションをアップロードする。
* Web アプリを開いて、IoT ハブから取得したリアルタイムの温度と湿度のデータを確認する。

## <a name="what-you-need"></a>必要なもの

* 次の要件に対応するための[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md)

  * 有効な Azure サブスクリプション
  * サブスクリプションの IoT Hub
  * IoT Hub にメッセージを送信するクライアント アプリケーション

* [Git のダウンロード](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Web アプリを作成する

1. [Azure Portal](https://portal.azure.com/) で、**[リソースの作成]** > **[Web + モバイル]** > **[Web アプリ]** をクリックします。

2. 一意のジョブ名を入力してサブスクリプションを確認し、リソース グループと場所を指定します。次に、**[ダッシュボードにピン留めする]** をオンにし、**[作成]** をクリックします。

   リソース グループと同じ場所を選択することをお勧めします。 
   
   ![Web アプリを作成する](./media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>IoT ハブからデータを読み取る Web アプリの構成

1. プロビジョニングしたばかりの Web アプリを開きます。

2. **[アプリケーション設定]** をクリックし、**[アプリの設定]** で次のキー/値のペアを追加します。

   | キー                                   | 値                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Azure CLI から取得                                      |
   | Azure.IoT.IoTHub.ConsumerGroup        | IoT ハブに追加するコンシューマー グループの名前  |
   | WEBSITE_NODE_DEFAULT_VERSION          | 8.9.4                                                        |

   ![キー/値のペアを使用して Web アプリに設定を追加する](./media/iot-hub-live-data-visualization-in-web-apps/3_web-app-settings-key-value-azure.png)

3. **[アプリケーション設定]** をクリックし、**[全般設定]** で **[Web Socket]** オプションを切り替えてから **[保存]** をクリックします。

   ![Web Socket オプションを切り替える](./media/iot-hub-live-data-visualization-in-web-apps/4_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Web アプリでホストされる Web アプリケーションのアップロード

GitHub には、IoT ハブからのリアルタイム センサー データを表示するための Web アプリケーションが用意されています。 必要な作業は、Git リポジトリを使用するよう Web アプリを構成し、GitHub から Web アプリケーションをダウンロードしてから、Web アプリでホストされるよう Azure にそれをアップロードすることだけです。

1. Web アプリで、**[デプロイ オプション]** > **[ソースの選択]** > **[ローカル Git リポジトリ]** の順にクリックし、**[OK]** をクリックします。

   ![ローカル Git リポジトリを使用するよう Web アプリのデプロイを構成する](./media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. **[デプロイ資格情報]** をクリックし、Git リポジトリに接続するときに Azure で使用されるユーザー名とパスワードを作成し、**[保存]** をクリックします。

3. **[概要]** をクリックし、**[Git クローン URL]** の値を書き留めます。

   ![Web アプリの Git クローン URL の取得](./media/iot-hub-live-data-visualization-in-web-apps/6_web-app-git-clone-url-azure.png)

4. ローカル コンピューターでコマンド ウィンドウまたはターミナル ウィンドウを開きます。

5. GitHub から Web アプリをダウンロードし、それが Web アプリでホストされるよう Azure にアップロードします。 これを行うには、次のコマンドを実行します。

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<Git clone URL\> は、Web アプリの **[概要]** ページにあった Git リポジトリの URL になります。

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Web アプリを開いて IoT ハブから取得したリアルタイムの温度と湿度のデータを確認する

Web アプリの **[概要]** ページで、URL をクリックして Web アプリを開きます。

![Web アプリの URL の取得](./media/iot-hub-live-data-visualization-in-web-apps/7_web-app-url-azure.png)

IoT ハブから取得したリアルタイムの温度と湿度のデータが表示されます。

![リアルタイムの温度と湿度を示す Web アプリのページ](./media/iot-hub-live-data-visualization-in-web-apps/8_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> デバイスでサンプル アプリケーションが実行されていることを確認します。 実行されていない場合は、空のグラフを取得し、チュートリアルの[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

Web アプリを使用して、IoT ハブからのリアルタイム センサー データを視覚化することができました。

Azure IoT Hub からのデータを視覚化する別の方法については、[Power BI を使用して IoT Hub からのリアルタイム センサー データを視覚化する](iot-hub-live-data-visualization-in-power-bi.md)に関するページを参照してください。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]