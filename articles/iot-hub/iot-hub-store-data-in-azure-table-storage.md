---
title: "Azure データ ストレージへの IoT Hub メッセージの保存 | Microsoft Docs"
description: "Azure Function App を使用して IoT Hub メッセージを Azure テーブル ストレージに保存します。 IoT Hub メッセージには、IoT デバイスから送信されたセンサー データなどの情報が含まれます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT データ ストレージ, IoT センサー データ ストレージ"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: de47a15dd4009b9a33a53fd981bc178eaa50c035
ms.lasthandoff: 03/30/2017


---
# <a name="save-iot-hub-messages-that-contain-information-like-sensor-data-to-azure-table-storage"></a>センサー データなどの情報が含まれた IoT Hub メッセージを Azure テーブル ストレージに保存する

> [!Note]
> このチュートリアルを開始する前に、[Azure IoT Hub への ESP8266 の接続](/iot-hub-arduino-huzzah-esp8266-get-started.md)に関するページの手順を完了しておいてください。 [Azure IoT Hub への ESP8266 の接続](/iot-hub-arduino-huzzah-esp8266-get-started.md)に関するページでは、IoT デバイスと IoT ハブを設定し、デバイスで実行するサンプル アプリケーションをデプロイします。 そのアプリケーションは、収集されたセンサー データを IoT ハブに送信します。

## <a name="what-you-will-learn"></a>学習内容

Azure ストレージ アカウントと Azure Function App を作成し、IoT Hub メッセージを Azure テーブル ストレージに格納する方法について説明します。

## <a name="what-you-will-do"></a>学習内容

- Azure ストレージ アカウントを作成します。
- メッセージを読み取るための IoT Hub 接続を用意します。
- Azure Function App を作成してデプロイします。

## <a name="what-you-will-need"></a>前提条件

- 次の要件を対象とする [ESP8266 を Azure IoT Hub に接続](/iot-hub-arduino-huzzah-esp8266-get-started.md)するためのチュートリアルを完了します。
  - 有効な Azure サブスクリプション
  - サブスクリプションの Azure IoT ハブ。
  - Azure IoT ハブにメッセージを送信する実行中のアプリケーション。

## <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

1. Azure Portal で、**[新規]** > **[ストレージ]** > **[ストレージ アカウント]** の順にクリックします。
1. ストレージ アカウントに必要な情報を入力します。

   ![Azure Portal でのストレージ アカウントの作成](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   **[名前]**: ストレージ アカウントの名前。 名前はグローバルに一意である必要があります。

   **[リソース グループ]**: IoT ハブと同じリソース グループを使用します。

   **[ダッシュボードにピン留めする]**: ダッシュボードから IoT ハブに簡単にアクセスできるようにするには、このオプションをオンにします。
1. **[作成]**をクリックします。

## <a name="prepare-for-iot-hub-connection-to-read-messages"></a>メッセージを読み取るための IoT Hub 接続の用意

IoT Hub は、アプリケーションによる IoT Hub メッセージの読み取りを可能にするために、イベント ハブと互換性のある組み込みのエンドポイントを公開します。 このとき、アプリケーションはコンシューマー グループを使用して、IoT Hub からデータを読み取ります。 IoT ハブからデータを読み取る Azure Function App を作成する前に、次の手順を行う必要があります。

- IoT ハブ エンドポイントの接続文字列の取得。
- IoT ハブのコンシューマー グループの作成。

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>IoT ハブ エンドポイントの接続文字列の取得

1. IoT ハブを開きます。
1. **[IoT Hub]** ウィンドウの **[メッセージング]** の下にある **[エンドポイント]** をクリックします。
1. 右側のウィンドウで、**[Built-in endpoints (組み込みエンドポイント)]** の下の **[イベント]** をクリックします。
1. **[プロパティ]** ウィンドウで、次の値を書き留めておきます。
   - Event Hub と互換性があるエンドポイント
   - イベント ハブと互換性がある名前

   ![Azure Portal で IoT ハブ エンドポイントの接続文字列を取得する](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

1. **[IoT Hub]** ウィンドウで **[設定]** の **[共有アクセス ポリシー]** をクリックします。
1. **iothubowner** をクリックします。
1. **[主キー]** の値を書き留めておきます。
1. 次のように、IoT ハブ エンドポイントの接続文字列を構成します。

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!Note]
   > `<Event Hub-compatible endpoint>` と `<Primary key>` は、書き留めた値に置き換えてください。

### <a name="create-a-consumer-group-for-your-iot-hub"></a>IoT ハブのコンシューマー グループの作成

1. IoT ハブを開きます。
1. **[IoT Hub]** ウィンドウの **[メッセージング]** の下にある **[エンドポイント]** をクリックします。
1. 右側のウィンドウで、**[Built-in endpoints (組み込みエンドポイント)]** の下の **[イベント]** をクリックします。
1. **[プロパティ]** ウィンドウで **[コンシューマー グループ]** に名前を入力し、その名前を書き留めておきます。
1. **[Save]**をクリックします。

## <a name="create-and-deploy-an-azure-function-app"></a>Azure Function App の作成とデプロイ

1. [Azure Portal](https://portal.azure.com/) で、**[新規]** > **[Compute]** > **[Function App]** の順にクリックします。
1. Function App に必要な情報を入力します。

   ![Azure Portal での Function App の作成](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   **[アプリ名]**: Function App の名前。 名前はグローバルに一意である必要があります。

   **[リソース グループ]**: IoT ハブと同じリソース グループを使用します。

   **[ストレージ アカウント]**: 作成したストレージ アカウント。

   **[ダッシュボードにピン留めする]**: ダッシュボードから Function App に簡単にアクセスできるようにするには、このオプションをオンにします。
1. **[作成]**をクリックします。
1. 作成された Function App を開きます。
1. この Function App で新しい関数を作成します。
   1. **[新しい関数]** をクリックします。
   1. **[言語]** として **[JavaScript]**、**[シナリオ]** として **[データ処理]** を選択します。
   1. **EventHubTrigger-JavaScript** テンプレートをクリックします。
   1. テンプレートに必要な情報を入力します。

      **[関数名の指定]**: 関数の名前。

      **[イベント ハブ名]**: 書き留めておいた、イベント ハブと互換性がある名前。

      **[イベント ハブ接続]**: [新規] をクリックして、構成した IoT ハブ エンドポイントの接続文字列を追加します。
   1. **[作成]**をクリックします。
1. 関数の出力を構成します。
   1. **[統合]** > **[新しい出力]** > **[Azure Table Storage]** > **[選択]** の順にクリックします。

      ![Azure Portal でテーブル ストレージを Function App に追加する](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)
   1. 必要な情報を入力します。

      **[テーブル名]**: `deviceData` を名前として使用します。

      **[ストレージ アカウント接続]**: **[新規]** をクリックしてストレージ アカウントを選択します。
   1. [ **Save**] をクリックします。
1. **[トリガー]** で **[Azure Event Hub (myEventHubTrigger) (Azure イベント ハブ (myEventHubTrigger))]** をクリックします。
1. **[イベント ハブ コンシューマー グループ]** で、作成したコンシューマー グループの名前を入力し、**[保存]** をクリックします。
1. **[開発]** をクリックし、**[ファイルの表示]** をクリックします。
1. **[追加]** をクリックして `package.json` という名前の新しいファイルを追加し、次の情報を貼り付けてから **[保存]** をクリックします。

   ```json
   {
      "name": "iothub_save_message_to_table",
      "version": "0.0.1",
      "private": true,
      "main": "index.js",
      "author": "Microsoft Corp.",
      "dependencies": {
         "azure-iothub": "1.0.9",
         "azure-iot-common": "1.0.7",
         "moment": "2.14.1"
      }
   }
   ```
1. `index.js` のコードを次の内容に置き換えて、**[保存]** をクリックします。

   ```javascript
   'use strict';

   // This function is triggered each time a message is revieved in the IoTHub.
   // The message payload is persisted in an Azure Storage Table
   var moment = require('moment');

   module.exports = function (context, iotHubMessage) {
      context.log('Message received: ' + JSON.stringify(iotHubMessage));
      context.bindings.outputTable = {
      "partitionKey": moment.utc().format('YYYYMMDD'),
         "rowKey": moment.utc().format('hhmmss') + process.hrtime()[1] + '',
         "message": JSON.stringify(iotHubMessage)
      };
      context.done();
   };
   ```
1. **[Function App の設定]** > **[開発者コンソールを開く]** の順にクリックします。

   最初の場所は Function App の `wwwroot` フォルダーです。
1. 次のコマンドを実行して関数フォルダーに移動します。

   ```bash
   cd <your function name>
   ```
1. 次のコマンドを実行して、npm パッケージをインストールします。

   ```bash
   npm install
   ```

   > [!Note]
   > インストールが完了するまで時間がかかる場合があります。

ここまでで、Function App の作成が完了しました。 このアプリは、IoT ハブが受信したメッセージを Azure テーブル ストレージに格納します。

> [!Note]
> **[実行]** ボタンを使用すると、Function App のテストを実行できます。 **[実行]** をクリックすると、テスト メッセージが IoT ハブに送信されます。 メッセージが届くと Function App の開始がトリガーされ、そのメッセージは Azure テーブル ストレージに保存されます。 **[ログ]** ウィンドウにプロセスの詳細が記録されます。

## <a name="verify-your-message-in-your-table-storage"></a>テーブル ストレージ内のメッセージの確認

1. デバイスでサンプル アプリケーションを実行し、メッセージを IoT ハブに送信します。
1. [Microsoft Azure ストレージ エクスプローラーをダウンロードしてインストールします](http://storageexplorer.com/)。
1. Microsoft Azure ストレージ エクスプローラーを開いて **[Add an Azure Account (Azure アカウントの追加)]** > **[サインイン]** の順にクリックし、Azure アカウントにサインインします。
1. Azure サブスクリプション > **[ストレージ アカウント]** > 自分のストレージ アカウント > **[テーブル]** > **deviceData** の順にクリックします。

   デバイスから IoT ハブに送信されたメッセージが、`deviceData` テーブルに記録されたことがわかります。

## <a name="next-steps"></a>次のステップ

Azure ストレージ アカウントと Azure Function App を作成し、IoT ハブが受信したメッセージを Azure テーブル ストレージに格納することができました。

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

- [iothub-explorer を使用したクラウド デバイス メッセージングの管理](iot-hub-explorer-cloud-device-messaging.md)
