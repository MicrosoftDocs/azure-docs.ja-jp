---
title: "Azure IoT への Intel Edison (Node) の接続 - レッスン 3: メッセージの送信 | Microsoft Docs"
description: "IoT ハブにメッセージを送信して LED を点滅させるサンプル アプリケーションを Intel Edison にデプロイして実行します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT クラウド サービス, クラウドにデータを送信する Arduino"
ms.assetid: 1b3b1074-f4d4-42ac-b32c-55f18b304b44
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: ddc40f2e3467cb406a7336924f9577d52061c42e
ms.lasthandoff: 01/25/2017


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージを送信するサンプル アプリケーションの実行
## <a name="what-you-will-do"></a>学習内容
この記事では、IoT ハブにメッセージを送信するサンプル アプリケーションを Intel Edison にデプロイして実行する方法について説明します。 問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
gulp ツールを使用し、サンプル C アプリケーションを Edison にデプロイして実行する方法を学習します。

## <a name="what-you-need"></a>必要なもの
* このタスクを開始する前に、[IoT ハブのメッセージを処理して格納するための Azure Function App とストレージ アカウントの作成][process-and-store-iot-hub-messages]を正常に完了している必要があります。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>IoT ハブとデバイスの接続文字列の取得
デバイスの接続文字列は、Edison を IoT ハブに接続するために使用されます。 IoT ハブの接続文字列は、IoT ハブの Edison を表すデバイス ID に IoT ハブを接続するために使用されます。

* 次の Azure CLI コマンドを実行して、リソース グループ内のすべての IoT ハブを一覧表示します。

```bash
az iot hub list -g iot-sample --query [].name
```

値を変更していない場合、`{resource group name}` の値として `iot-sample` を使用します。

* 次の Azure CLI コマンドを実行して、IoT ハブの接続文字列を取得します。

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` は、IoT ハブを作成し、Edison を登録するときに指定した名前です。

* 次のコマンドを実行して、デバイスの接続文字列を取得します。

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myinteledison
```

値を変更していない場合、`{device id}` の値として `myinteledison` を使用します。

## <a name="configure-the-device-connection"></a>デバイス接続の構成
1. 次のコマンドを実行して、構成ファイルを初期化します。

   ```bash
   npm install
   gulp init
   ```

2. 次のコマンドを実行して、Visual Studio Code でデバイス構成ファイル `config-edison.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

   ![config.json](media/iot-hub-intel-edison-lessons/lesson3/config.png)
3. `config-edison.json` ファイルの値を次のように置き換えます。

   * **[device hostname or IP address]** を、デバイスを構成したときにメモしておいたデバイスの IP アドレスに置き換えます。
   * **[IoT device connection string]** を、取得した `device connection string` で置き換えます。
   * **[IoT hub connection string]** を、取得した `iot hub connection string` で置き換えます。

   > [!NOTE]
   > この記事では、`azure_storage_connection_string` は必要ありません。 そのままにしておいてください。

## <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションを Edison にデプロイして実行します。

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>サンプル アプリケーションの動作確認
Edison に接続された LED が&2; 秒間隔で点滅していることを確認します。 LED が点滅するたびに、サンプル アプリケーションは IoT ハブにメッセージを送信し、メッセージが IoT ハブに正常に送信されていることを検証します。 さらにコンソール ウィンドウには、IoT ハブが受信した各メッセージが出力されます。 サンプル アプリケーションは、メッセージを 20 回送信した後に自動的に終了します。

![サンプル アプリケーションの実行とメッセージの送受信の出力][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>概要
点滅を行う新しいサンプル アプリケーションを Edison にデプロイして実行し、デバイスからクラウドへのメッセージを IoT ハブに送信しました。 これで、ストレージ アカウントに書き込まれたメッセージを監視する準備が整いました。

## <a name="next-steps"></a>次のステップ
[Azure Storage に保持されたメッセージを読み取る][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-intel-edison-lessons/lesson3/gulp_run.png
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-node-lesson3-read-table-storage.md
