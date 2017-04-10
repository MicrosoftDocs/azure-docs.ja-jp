---
title: "Azure IoT への Intel Edison (Node) の接続 - レッスン 4: メッセージの受信 | Microsoft Docs"
description: "サンプル アプリケーションは、Edison 上で動作し、IoT ハブからの受信メッセージを監視します。 新しい gulp タスクを使って IoT ハブから Edison にメッセージを送信して、LED を点滅させます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Web からの Arduino による LED の制御, Web 経由の Arduino による LED の制御"
ms.assetid: bc738bf6-e38d-4024-82d7-39b6c2d4bacb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 4dfddf6a2664abbdfd9b5d782dafc9e5ff243e5a
ms.lasthandoff: 01/25/2017


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>クラウドからデバイスへのメッセージを受信するサンプル アプリケーションの実行
この記事では、Intel Edison にサンプル アプリケーションをデプロイします。 このサンプル アプリケーションは、IoT Hub から受信するメッセージを監視するものです。 さらに、コンピューターで gulp タスクを実行し、IoT ハブから Edison にメッセージを送信します。 このサンプル アプリケーションは、メッセージを受信すると LED を点滅させます。 問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策を探してください。

## <a name="what-you-will-do"></a>学習内容
* サンプル アプリケーションを IoT Hub に接続する。
* サンプル アプリケーションをデプロイし、実行する。
* IoT ハブから Edison にメッセージを送信し、LED を点滅させる。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。
* IoT Hub から受信したメッセージを監視する方法。
* IoT ハブから Edison にクラウドからデバイスへのメッセージを送信する方法。

## <a name="what-you-need"></a>必要なもの
* 使用できるように設定済みの Intel Edison。 Edison を設定する方法については、[デバイスの構成][configure-your-device]に関する記事を参照してください。
* Azure サブスクリプションで作成した IoT Hub。 IoT ハブの作成方法については、[Azure IoT Hub の作成][create-your-azure-iot-hub]に関する記事をご覧ください。

## <a name="connect-the-sample-application-to-your-iot-hub"></a>IoT ハブへのサンプル アプリケーションの接続
1. 現在のリポジトリ フォルダーが `iot-hub-node-edison-getting-started` であることを確認します。 以下のコマンドを実行して、Visual Studio Code でサンプル アプリケーションを開きます。

   ```bash
   cd Lesson4
   code .
   ```

   `app` サブフォルダーのこのファイルは、IoT ハブから受信したメッセージを監視するためのコードが入ったキー ソース ファイルです。 `blinkLED` 関数は、LED を点滅させるものです。

   ![サンプル アプリケーションのリポジトリの構造][repo-structure]
2. 次のコマンドを実行して、構成ファイルを初期化します。

   ```bash
   npm install
   gulp init
   ```

   [Azure Function App とストレージ アカウントの作成][create-an-azure-function-app-and-storage-account]の手順をこのコンピューターで完了している場合はすべての構成が継承されるため、この手順を省略して、サンプル アプリケーションをデプロイして実行するタスクに進むことができます。 [Azure Function App とストレージ アカウントの作成][create-an-azure-function-app-and-storage-account]の手順を他のコンピューターで完了している場合は、`config-edison.json` ファイル内のプレースホルダーを置き換える必要があります。 `config-edison.json` ファイルは、ホーム フォルダーのサブフォルダーにあります。

   ![config-edison.json ファイルの内容](media/iot-hub-intel-edison-lessons/lesson4/config-edison.png)

   * **[device hostname or IP address]** を、デバイスを構成したときにメモしておいたデバイスの IP アドレスに置き換えます。
   * **[IoT device connection string]** の部分を、デバイスの接続文字列で置き換えます。デバイスの接続文字列は、`az iot device show-connection-string --hub-name {my hub name} --device-id {device id}` コマンドで取得できます。
   * **[IoT hub connection string]** の部分を、IoT ハブの接続文字列で置き換えます。IoT ハブの接続文字列は、`az iot hub show-connection-string --name {my hub name}` コマンドで取得できます。

## <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションを Edison にデプロイして実行します。

```bash
gulp deploy && gulp run
```

この gulp コマンドは、Edison にサンプル アプリケーションをデプロイします。 次に、このコマンドは、Edison のアプリケーションを実行する一方、ホスト コンピューターで別のタスクを実行して、Edison から IoT ハブに 20 件の blink メッセージを送信します。

サンプル アプリケーションは、実行されると、IoT ハブからのメッセージのリッスンを開始します。 このとき、gulp タスクにより IoT ハブから Edison に対して "blink" メッセージが複数回送信されます。 Edison が blink メッセージを受信するたびに、サンプル アプリケーションは LED を点滅させる `blinkLED` 関数を呼び出します。

gulp タスクにより IoT ハブから Edison に 20 件のメッセージが送信され、LED が 2 秒間隔で点滅します。 最後のメッセージは、アプリケーションの実行を停止する "stop" メッセージです。

![サンプル アプリケーションの実行と gulp コマンドと blink メッセージ][gulp-command-and-blink-messages]

## <a name="summary"></a>概要
IoT ハブから Edison にメッセージを送信し、LED を点滅させることができました。 次の、LED のオンとオフの動作の変更タスクは省略可能です。

## <a name="next-steps"></a>次のステップ
[LED のオン/オフの動作を変更する][change-the-on-and-off-behavior-of-the-led]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson4/repo_structure.png
[create-an-azure-function-app-and-storage-account]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
[gulp-command-and-blink-messages]: media/iot-hub-intel-edison-lessons/lesson4/gulp_blink.png
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-node-lesson4-change-led-behavior.md
