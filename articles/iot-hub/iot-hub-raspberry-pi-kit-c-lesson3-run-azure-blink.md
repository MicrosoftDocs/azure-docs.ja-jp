---
title: "Azure IoT への Raspberry Pi (C) の接続 - レッスン 3: サンプルの実行 | Microsoft Docs"
description: "IoT ハブにメッセージを送信して LED を点滅させるサンプル アプリケーションを Raspberry Pi 3 にデプロイして実行します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "点滅 LED クラウド Pi、クラウドから LED の点滅"
ms.assetid: e38df29f-f77f-435f-9add-46814297564f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 985ba895bfcad1611bebe80a6c2fc8ffed780da5
ms.lasthandoff: 01/24/2017


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージを送信するサンプル アプリケーションの実行
## <a name="what-you-will-do"></a>学習内容
この記事では、IoT ハブにメッセージを送信するサンプル アプリケーションを Raspberry Pi 3 にデプロイして実行する方法について説明します。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-raspberry-pi-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
gulp ツールを使用し、サンプル Node.js アプリケーションを Pi にデプロイして実行する方法について説明します。

## <a name="what-you-need"></a>必要なもの
* このタスクを開始する前に、[IoT ハブのメッセージを処理して格納するための Azure Function App とストレージ アカウントの作成](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md)を、正常に完了している必要があります。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>IoT ハブとデバイスの接続文字列の取得
デバイス接続文字列は、IoT Hub に接続するために Pi で使用されます。 IoT Hub 接続文字列は、IoT Hub 内の ID レジストリに接続して、IoT Hub への接続を許可されるデバイスを管理するために使用されます。 

* 次の Azure CLI コマンドを実行して、リソース グループ内のすべての IoT Hub を一覧表示します。

```bash
az iot hub list -g iot-sample --query [].name
```

値を変更していない場合、`{resource group name}` の値として `iot-sample` を使用します。

* 次の Azure CLI コマンドを実行して、IoT ハブの接続文字列を取得します。

```bash
az iot hub show-connection-string --name {my hub name} -g iot-sample
```

`{my hub name}` は、IoT ハブを作成し、Pi を登録するときに指定した名前です。

* 次のコマンドを実行して、デバイスの接続文字列を取得します。

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myraspberrypi -g iot-sample
```

値を変更していない場合、`{device id}` の値として `myraspberrypi` を使用します。

## <a name="configure-the-device-connection"></a>デバイス接続の構成
1. 次のコマンドを実行して、構成ファイルを初期化します。
   
   ```bash
   npm install
   gulp init
   ```

> [!NOTE]
> レッスン 1 で **gulp install-tools** を実行していない場合はこのコマンドも実行します。

2. 次のコマンドを実行して、Visual Studio Code でデバイス構成ファイル `config-raspberrypi.json` を開きます。
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
   
   ![config.json](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)
3. `config-raspberrypi.json` ファイルの値を次のように置き換えます。
   
   * **[device hostname or IP address]** を、`device-discovery-cli` から取得したデバイス IP アドレスまたはホスト名で置き換えるか、またはデバイスを構成したときに継承された値で置き換えます。
   * **[IoT device connection string]** を、取得した `device connection string` で置き換えます。
   * **[IoT hub connection string]** を、取得した `iot hub connection string` で置き換えます。

> [!NOTE]
> この記事では、`azure_storage_connection_string` は必要ありません。 そのままにしておいてください。

`config-raspberrypi.json` ファイルを更新し、コンピューターからサンプル アプリケーションをデプロイできるようにします。

## <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションを Pi にデプロイして実行します。

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>サンプル アプリケーションの動作確認
Pi に接続された LED が&2; 秒間隔で点滅していることを確認します。 LED が点滅するたびに、サンプル アプリケーションは IoT ハブにメッセージを送信し、メッセージが IoT ハブに正常に送信されていることを検証します。 さらにコンソール ウィンドウには、IoT ハブが受信した各メッセージが出力されます。 サンプル アプリケーションは、メッセージを 20 回送信した後に自動的に終了します。

![サンプル アプリケーションの実行とメッセージの送受信の出力](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run_c.png)

## <a name="summary"></a>概要
点滅を行う新しいサンプル アプリケーションを Pi にデプロイして実行し、デバイスからクラウドへのメッセージを IoT ハブに送信しました。 これで、ストレージ アカウントに書き込まれたメッセージを監視する準備が整いました。

## <a name="next-steps"></a>次のステップ
[Azure Storage に保持されたメッセージの読み取り](iot-hub-raspberry-pi-kit-c-lesson3-read-table-storage.md)


