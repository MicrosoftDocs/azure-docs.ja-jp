---
featureFlags:
- usabilla
title: "Azure IoT への Raspberry Pi (Node) の接続 - レッスン 4: クラウドからデバイス | Microsoft Docs"
description: "サンプル アプリケーションは、Pi 上で動作し、IoT ハブからの受信メッセージを監視します。 新しい gulp タスクを使って IoT ハブから Pi にメッセージを送信して、LED を点滅させます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "クラウドからデバイス, クラウドからのメッセージ"
ms.assetid: 6ae6539e-1163-4490-8d72-fdf7803e3054
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 92ee9d6faae9f539c663395e47714609a146f2df
ms.lasthandoff: 01/24/2017


---
# <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>クラウドからデバイスへのメッセージを受信するサンプル アプリケーションの実行
この記事では、Raspberry Pi 3 にサンプル アプリケーションをデプロイします。 このサンプル アプリケーションは、IoT Hub から受信するメッセージを監視するものです。 さらに、コンピューターで gulp タスクを実行し、IoT ハブから Pi にメッセージを送信します。 このサンプル アプリケーションは、メッセージを受信すると LED を点滅させます。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-raspberry-pi-kit-node-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-do"></a>学習内容
* サンプル アプリケーションを IoT Hub に接続する。
* サンプル アプリケーションをデプロイし、実行する。
* IoT ハブから Pi にメッセージを送信し、LED を点滅させる。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。
* IoT ハブから受信したメッセージを監視する方法
* IoT ハブから Pi にクラウドからデバイスへのメッセージを送信する方法。

## <a name="what-you-need"></a>必要なもの
* 設定済み Raspberry Pi 3。 Pi を設定する方法については、「[デバイスを構成する](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)」を参照してください。
* Azure サブスクリプションで作成した IoT Hub。 IoT ハブを作成する方法については、「[IoT ハブの作成と Raspberry Pi 3 の登録](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)」を参照してください。

## <a name="connect-the-sample-application-to-your-iot-hub"></a>IoT ハブへのサンプル アプリケーションの接続
1. 現在のリポジトリ フォルダーが `iot-hub-node-raspberrypi-getting-started` であることを確認します。 以下のコマンドを実行して、Visual Studio Code でサンプル アプリケーションを開きます。
   
   ```bash
   cd Lesson4
   code .
   ```
   
   `app` サブフォルダーに `app.js` ファイルがあります。 この `app.js` ファイルは、IoT ハブから受信したメッセージを監視するためのコードが入ったキー ソース ファイルです。 `blinkLED` 関数は、LED を点滅させるものです。
   
   ![サンプル アプリケーションのリポジトリの構造](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)
2. 次のコマンドを使用して、構成ファイルを初期化します。
   
   ```bash
   npm install
   gulp init
   ```
   
   [Azure Function App と Azure Storage アカウントの作成](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)の手順をこのコンピューターで完了している場合はすべての構成が継承されるため、サンプル アプリケーションをデプロイして実行するタスクをスキップできます。 [Azure Function App と Azure Storage アカウントの作成](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)の手順を他のコンピューターで完了している場合は、`config-raspberrypi.json` ファイル内のプレースホルダーを置き換える必要があります。 `config-raspberrypi.json` ファイルは、ホーム フォルダーのサブフォルダーにあります。
   
   ![config-raspberrypi.json ファイルの内容](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

* **[device hostname or IP address]** の部分を、Pi の IP アドレスまたはホスト名で置き換えます。IP アドレスまたはホスト名は、`devdisco list --eth` コマンドで取得できます。
* **[IoT device connection string]** の部分を、デバイスの接続文字列で置き換えます。デバイスの接続文字列は、`az iot device show-connection-string --hub-name {my hub name} --device-id {device id} -g iot-sample {resource group name}` コマンドで取得できます。
* **[IoT hub connection string]** の部分を、IoT ハブの接続文字列で置き換えます。IoT ハブの接続文字列は、`az iot hub show-connection-string --name {my hub name} -g iot-sample {resource group name}` コマンドで取得できます。

> [!NOTE]
> レッスン 1 で **gulp install-tools** を実行していない場合はこのコマンドも実行します。

## <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションを Pi にデプロイして実行します。

```bash
gulp deploy && gulp run
```

このコマンドは、Pi にサンプル アプリケーションをデプロイします。 次に、このコマンドは、Pi のアプリケーションを実行する一方、ホスト コンピューター上で別のタスクを実行して、Pi から IoT ハブに 20 件の blink メッセージを送信します。

サンプル アプリケーションは、実行されると、IoT ハブからのメッセージのリッスンを開始します。 このとき、gulp タスクにより IoT ハブから Pi に対して "blink" メッセージが複数回送信されます。 Pi が blink メッセージを受信するたびに、サンプル アプリケーションは LED を点滅させる `blinkLED` 関数を呼び出します。

gulp タスクにより IoT ハブから Pi に 20 件のメッセージが送信され、LED が 2 秒間隔で点滅します。 最後のメッセージは、アプリケーションの実行を停止するための "stop" メッセージです。

![サンプル アプリケーションの実行と gulp コマンドと blink メッセージ](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="summary"></a>概要
IoT ハブから Pi にメッセージを送信し、LED を点滅させることができました。 次の、LED のオンとオフの動作の変更タスクは省略可能です。

## <a name="next-steps"></a>次のステップ
[LED のオンとオフの動作の変更](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)


