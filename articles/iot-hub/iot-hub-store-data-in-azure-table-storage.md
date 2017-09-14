---
title: "IoT Hub メッセージを Azure データ ストレージに保存する | Microsoft Docs"
description: "Azure 関数アプリを使用して IoT Hub メッセージを Azure Table Storageに保存します。 IoT Hub メッセージには、IoT デバイスから送信されたセンサー データなどの情報が含まれます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IoT データ ストレージ, IoT センサー データ ストレージ"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 06503f9564e00ef62587d02f2da4778974e246c5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-table-storage"></a>センサー データを含む IoT Hub メッセージを Azure Table Storageに保存する

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学習内容

Azure ストレージ アカウントと Azure 関数アプリを作成し、IoT Hub メッセージをテーブル ストレージに格納する方法について説明します。

## <a name="what-you-do"></a>作業内容

- Azure ストレージ アカウントを作成します。
- メッセージを読み取るための IoT Hub 接続を用意します。
- Azure 関数アプリを作成してデプロイします。

## <a name="what-you-need"></a>必要なもの

- 次の要件に対応するための[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md):
  - 有効な Azure サブスクリプション
  - サブスクリプションの IoT Hub 
  - Azure IoT ハブにメッセージを送信する実行中のアプリケーション

## <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

1. [Azure Portal](https://portal.azure.com/) で、**[新規]** > **[ストレージ]** > **[ストレージ アカウント]** > **[作成]** の順にクリックします。

2. ストレージ アカウントに必要な情報を入力します。

   ![Azure Portal でのストレージ アカウントの作成](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **[名前]**: ストレージ アカウントの名前。 名前はグローバルに一意である必要があります。

   * **[リソース グループ]**: IoT ハブと同じリソース グループを使用します。

   * **[ダッシュボードにピン留めする]**: ダッシュボードから IoT Hub に簡単にアクセスできるようにするには、このオプションをオンにします。

3. ページの下部にある **[Create]**」を参照してください。

## <a name="prepare-your-iot-hub-connection-to-read-messages"></a>メッセージを読み取るための IoT Hub 接続を用意する

IoT Hub は、アプリケーションによる IoT Hub メッセージの読み取りを可能にするために、イベント ハブと互換性のある組み込みのエンドポイントを公開します。 このとき、アプリケーションはコンシューマー グループを使用して、IoT Hub からデータを読み取ります。 IoT ハブからデータを読み取る Azure 関数アプリを作成する前に、次の手順を行う必要があります。

- IoT ハブ エンドポイントの接続文字列の取得。
- IoT ハブのコンシューマー グループの作成。

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>IoT ハブ エンドポイントの接続文字列の取得

1. IoT ハブを開きます。

2. **[IoT Hub]** ウィンドウの **[メッセージング]** の下にある **[エンドポイント]** をクリックします。

3. 右側のウィンドウで、**[組み込みのエンドポイント]** の下の **[イベント]** をクリックします。

4. **[プロパティ]** ウィンドウの次の値を書き留めておきます。
   - Event Hub と互換性があるエンドポイント
   - イベント ハブと互換性がある名前

   ![Azure Portal で IoT ハブ エンドポイントの接続文字列を取得する](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

5. **[IoT Hub]** ウィンドウで **[設定]** の **[共有アクセス ポリシー]** をクリックします。

6. **iothubowner** をクリックします。

7. **[プライマリ キー]** の値を書き留めます。

8. IoT ハブ エンドポイントの接続文字列を次のように作成します。

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!NOTE]
   > `<Event Hub-compatible endpoint>` と `<Primary key>` は、書き留めておいた値に置き換えてください。

### <a name="create-a-consumer-group-for-your-iot-hub"></a>IoT ハブのコンシューマー グループの作成

1. IoT ハブを開きます。

2. **[IoT Hub]** ウィンドウの **[メッセージング]** の下にある **[エンドポイント]** をクリックします。

3. 右側のウィンドウで、**[組み込みのエンドポイント]** の下の **[イベント]** をクリックします。

4. **[プロパティ]** ウィンドウで **[コンシューマー グループ]** に名前を入力し、その名前を書き留めておきます。

5. **[保存]** をクリックします。

## <a name="create-and-deploy-an-azure-function-app"></a>Azure 関数アプリを作成してデプロイする

1. [Azure Portal](https://portal.azure.com/) で、**[新規]** > **[Compute]** > **[Function App]** > **[作成]** の順にクリックします。

2. 関数アプリに必要な情報を入力します。

   ![Azure Portal での関数アプリの作成](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   * **アプリ名**: 関数アプリの名前。 名前はグローバルに一意である必要があります。

   * **[リソース グループ]**: IoT ハブと同じリソース グループを使用します。

   * **[ストレージ アカウント]**: 作成したストレージ アカウント。

   * **ダッシュボードにピン留めする**: ダッシュボードから関数アプリに簡単にアクセスできるようにするには、このオプションをオンにします。

3. **[作成]**をクリックします。

4. 関数アプリが作成されたら、そのアプリを開きます。

5. 関数アプリで、次の手順を実行して新しい関数を作成します。

   a. **[新しい関数]** をクリックします。

   b. **[言語]** として **[JavaScript]**、**[シナリオ]** として **[データ処理]** を選択します。

   c. **[この関数を作成する]** をクリックし、**[新しい関数]** をクリックします。

   d. [言語] として **[JavaScript]**、[シナリオ] として **[データ処理]** を選択します。

   e. **EventHubTrigger-JavaScript** テンプレートをクリックします。

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 テンプレートに必要な情報を入力します。

      * **関数名の指定**: 関数の名前。

      * **イベント ハブ名**: 書き留めておいたイベント ハブと互換性がある名前。

      * **イベント ハブ接続**: 作成した IoT Hub エンドポイントの接続文字列を追加するには、**[新規]** をクリックします。

   g. ページの下部にある **[Create]**」を参照してください。

6. 次を実行して、関数の出力を構成します。

   a. **[統合]** > **[新しい出力]** > **[Azure Table Storage]** > **[選択]** の順にクリックします。

      ![Azure Portal でテーブル ストレージを関数アプリに追加する](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)

   b. 必要な情報を入力します。

      * **テーブル パラメーター名**: `outputTable` を使用します。これが Azure 関数のコードで使用されます。
      
      * **テーブル名:** : `deviceData` を使用します。

      * **ストレージ アカウント接続**: **[新規]** をクリックし、ストレージ アカウントを選択するか入力します。 ストレージ アカウントが表示されない場合は、「[ストレージ アカウントの要件](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)」を参照してください。
      
   c. **[保存]**をクリックします。

7. **[トリガー]** で **[Azure Event Hub (eventHubMessages)]** をクリックします。

8. **[イベント ハブ コンシューマー グループ]** で、作成したコンシューマー グループの名前を入力し、**[保存]** をクリックします。

9. 左側で作成した関数をクリックし、右側で **[ファイルの表示]** をクリックします。

10. `index.js` のコードを次のコードに置き換えます。

   ```javascript
   'use strict';

   // This function is triggered each time a message is received in the IoT hub.
   // The message payload is persisted in an Azure storage table
 
   module.exports = function (context, iotHubMessage) {
    context.log('Message received: ' + JSON.stringify(iotHubMessage));
    var date = Date.now();
    var partitionKey = Math.floor(date / (24 * 60 * 60 * 1000)) + '';
    var rowKey = date + '';
    context.bindings.outputTable = {
     "partitionKey": partitionKey,
     "rowKey": rowKey,
     "message": JSON.stringify(iotHubMessage)
    };
    context.done();
   };
   ```

11. **[保存]**をクリックします。

これで、関数アプリの作成が完了しました。 このアプリは、IoT ハブが受信したメッセージをテーブル ストレージに格納します。

> [!NOTE]
> **[実行]** ボタンを使用して、この関数アプリをテストできます。 **[実行]** をクリックすると、テスト メッセージが IoT ハブに送信されます。 メッセージの到着によって関数アプリの起動がトリガーされ、そのメッセージがテーブル ストレージに保存されます。 **[ログ]** ウィンドウにプロセスの詳細が記録されます。

## <a name="verify-your-message-in-your-table-storage"></a>テーブル ストレージ内のメッセージの確認

1. デバイスでサンプル アプリケーションを実行し、メッセージを IoT ハブに送信します。

2. [Azure ストレージ エクスプローラーをダウンロードしてインストールします](http://storageexplorer.com/)。

3. ストレージ エクスプローラーを開いて **[Azure アカウントの追加]** > **[サインイン]** をクリックし、Azure アカウントにサインインします。

4. Azure サブスクリプション > **[ストレージ アカウント]** > 自分のストレージ アカウント > **[テーブル]** > **deviceData** の順にクリックします。

   デバイスから IoT ハブに送信されたメッセージが、`deviceData` テーブルに記録されたことがわかります。

## <a name="next-steps"></a>次のステップ

Azure ストレージ アカウントと Azure 関数アプリを作成し、IoT ハブが受信したメッセージをテーブル ストレージに格納することができました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

