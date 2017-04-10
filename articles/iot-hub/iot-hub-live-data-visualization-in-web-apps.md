---
title: "Azure IoT Hub から取得したリアルタイム センサー データの視覚化 – Web Apps | Microsoft Docs"
description: "Azure Web Apps を使用して、センサーから収集されて Azure IoT ハブに送信された気温と湿度のデータを視覚化します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "リアルタイム データの視覚化, ライブ データの視覚化, センサー データの視覚化"
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 788b3a6ce98c6668a8efc41f66c7e39b8aeda2db
ms.lasthandoff: 03/30/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-azure-web-apps"></a>Azure Web Apps を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する

![センサー、IoT デバイス、IoT Hub、Azure Web アプリの接続](media/iot-hub-live-data-visualization-in-web-apps/1_sensor-iot-device-azure-iot-hub-web-app-connection.png)

> [!NOTE]
> このチュートリアルを開始する前に、[Azure IoT Hub への ESP8266 の接続](/iot-hub-arduino-huzzah-esp8266-get-started.md)に関するページの手順を完了しておいてください。 [Azure IoT Hub への ESP8266 の接続](/iot-hub-arduino-huzzah-esp8266-get-started.md)に関するページでは、IoT デバイスと IoT ハブを設定し、デバイスで実行するサンプル アプリケーションをデプロイします。 そのアプリケーションは、収集されたセンサー データを IoT ハブに送信します。

## <a name="what-you-learn"></a>学習内容

このレッスンでは、Azure Web アプリでホストされた Web アプリケーションを実行して、Azure IoT ハブに届いたリアルタイム センサー データを視覚化する方法について説明します。 Power BI を使用して IoT ハブのデータを視覚化したい場合は、[Power BI を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する方法](iot-hub-live-data-visualization-in-power-bi.md)に関するページを参照してください。

## <a name="what-you-do"></a>作業内容

- Azure Portal で Azure Web アプリを作成する。
- コンシューマー グループを追加して、データにアクセスできるよう IoT ハブを準備する。
- IoT ハブからセンサー データを読み取る Web アプリを構成する。
- Web アプリでホストされる Web アプリケーションをアップロードする。
- Web アプリを開いて、IoT ハブから取得したリアルタイムの温度と湿度のデータを確認する。

## <a name="what-you-need"></a>必要なもの

- 次の要件を対象とする [ESP8266 を Azure IoT Hub に接続](iot-hub-arduino-huzzah-esp8266-get-started.md)するためのチュートリアルを完了します。
  - 有効な Azure サブスクリプション
  - サブスクリプションの Azure IoT ハブ。
  - Azure IoT ハブにメッセージを送信するクライアント アプリケーション。
- Git ([Git のダウンロード](https://www.git-scm.com/downloads))。

## <a name="create-an-azure-web-app"></a>Azure Web アプリの作成

1. [Azure Portal](https://ms.portal.azure.com/) で、**[新規]** > **[Web + モバイル]** > **[Web アプリ]** の順にクリックします。
1. 一意のジョブ名を入力してサブスクリプションを確認し、リソース グループと場所を指定します。次に、**[ダッシュボードにピン留めする]** をオンにし、**[作成]** をクリックします。

   リソース グループと同じ場所を選択することをお勧めします。 そうすることで、データ転送の処理を高速化してコストを抑えることができます。

   ![Azure Web アプリの作成](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT ハブへのコンシューマー グループの追加

コンシューマー グループは、Azure IoT Hub からデータをプルするアプリケーションによって使用されます。 このレッスンでは、IoT ハブからデータを読み取る Web アプリによって使用されるコンシューマー グループを作成します。

コンシューマー グループを IoT ハブに追加するには、次の手順に従います。

1. [Azure Portal](https://ms.portal.azure.com/) で、IoT ハブを開きます。
1. 左側のウィンドウで **[エンドポイント]** をクリックし、中央のウィンドウで **[イベント]** を選択します。次に、右側のウィンドウの **[コンシューマー グループ]** で名前を入力し、**[保存]** をクリックします。

   ![Azure IoT Hub でのコンシューマー グループの作成](media/iot-hub-live-data-visualization-in-web-apps/3_add-consumer-group-iot-hub-azure.png)

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>IoT ハブからデータを読み取る Web アプリの構成

1. プロビジョニングしたばかりの Web アプリを開きます。
1. **[アプリケーション設定]** をクリックし、**[アプリの設定]** で次のキー/値のペアを追加します。

   | キー                                   | 値                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | IoT Hub エクスプローラーから取得                               |
   | Azure.IoT.IoTHub.DeviceId             | IoT Hub エクスプローラーから取得                               |
   | Azure.IoT.IoTHub.ConsumerGroup        | IoT ハブに追加するコンシューマー グループの名前  |

   ![キー/値のペアを使用して Azure Web アプリに設定を追加する](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Web アプリでホストされる Web アプリケーションのアップロード

GitHub には、IoT ハブからのリアルタイム センサー データを表示するための Web アプリケーションが Microsoft によって用意されています。 必要な作業は、Git リポジトリを使用するよう Web アプリを構成し、GitHub から Web アプリケーションをダウンロードしてから、Web アプリでホストされるよう Azure にそれをアップロードすることだけです。

1. Web アプリで、**[デプロイ オプション]** > **[ソースの選択]** > **[ローカル Git リポジトリ]** の順にクリックします。

   ![ローカル Git リポジトリを使用するよう Azure Web アプリのデプロイを構成する](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

1. **[接続のセットアップ]** をクリックし、Git リポジトリに接続する際に Azure で使用されるユーザー名とパスワードを作成し、**[OK]** をクリックします。

   ![Web アプリに関して Azure で Git リポジトリのユーザー名とパスワードを設定する](media/iot-hub-live-data-visualization-in-web-apps/6_web-app-set-user-password-git-repo-azure.png)

1. **[OK]** をクリックして構成を終了します。
1. **[概要]** をクリックし、**[Git クローン URL]** の値を書き留めます。

   ![Azure Web アプリの Git クローン URL の取得](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

1. ローカル コンピューターでコマンド ウィンドウまたはターミナル ウィンドウを開きます。
1. GitHub から Web アプリケーションをダウンロードし、それが Web アプリでホストされるよう Azure にアップロードします。 これを行うには、次のコマンドを実行します。

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!Note]
   > \<Git clone URL\> は、Web アプリの **[概要]** ページにあった Git リポジトリの URL になります。

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Web アプリを開いて IoT ハブから取得したリアルタイムの温度と湿度のデータを確認する

Web アプリの **[概要]** ページで、URL をクリックして Web アプリを開きます。

![Azure Web アプリの URL の取得](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

IoT ハブから取得したリアルタイムの温度と湿度のデータが表示されます。

![リアルタイムの温度と湿度を示す Azure Web アプリのページ](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

## <a name="next-steps"></a>次のステップ
Azure Web アプリを使用して、Azure IoT ハブからのリアルタイム センサー データを視覚化することができました。

このほかにも、Azure IoT Hub からのデータを視覚化する方法があります。 [Power BI を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する方法](iot-hub-live-data-visualization-in-power-bi.md)に関するページを参照してください。

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

- [iothub-explorer を使用したクラウド デバイス メッセージングの管理](iot-hub-explorer-cloud-device-messaging.md)
- [Azure データ ストレージへの IoT Hub メッセージの保存](iot-hub-store-data-in-azure-table-storage.md)

